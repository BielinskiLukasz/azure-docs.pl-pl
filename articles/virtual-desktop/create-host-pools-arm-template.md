---
title: Tworzenie puli hostów pulpitu wirtualnego systemu Windows przy użyciu szablonu Azure Resource Manager — Azure
description: Jak utworzyć pulę hostów w programie Virtual Desktop systemu Windows przy użyciu szablonu Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: b96ecad93c481a762b81a7888940850ed3a73e5f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679610"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w pracę, tak jak na pulpicie fizycznym.

Postępuj zgodnie z instrukcjami w tej sekcji, aby utworzyć pulę hostów dla dzierżawy pulpitu wirtualnego systemu Windows przy użyciu szablonu Azure Resource Manager dostarczonego przez firmę Microsoft. W tym artykule przedstawiono sposób tworzenia puli hostów w programie Virtual Desktop systemu Windows, tworzenia grupy zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure, przyłączania tych maszyn wirtualnych do domeny usługi AD i rejestrowania maszyn wirtualnych za pomocą pulpitu wirtualnego systemu Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co jest potrzebne do uruchomienia szablonu Azure Resource Manager

Przed uruchomieniem szablonu Azure Resource Manager upewnij się, że znasz następujące kwestie:

- Miejsce źródłowe obrazu, którego chcesz użyć, to. Czy pochodzi ona z galerii platformy Azure lub jest niestandardowa?
- Poświadczenia dołączania do domeny.
- Poświadczenia pulpitu wirtualnego systemu Windows.

Podczas tworzenia puli hostów pulpitu wirtualnego systemu Windows przy użyciu szablonu Azure Resource Manager można utworzyć maszynę wirtualną z poziomu galerii platformy Azure, obrazu zarządzanego lub niezarządzanego obrazu. Aby dowiedzieć się więcej na temat tworzenia obrazów maszyn wirtualnych, zobacz [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) i [Tworzenie zarządzanego obrazu maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Uruchom szablon Azure Resource Manager, aby zainicjować obsługę nowej puli hostów

Aby rozpocząć, przejdź do [adresu URL usługi GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

Jeśli wdrażasz program w ramach subskrypcji przedsiębiorstwa, przewiń w dół i wybierz pozycję **Wdróż na platformie Azure**, a następnie Pomiń parametry w oparciu o źródło obrazu.

Jeśli wdrażasz w ramach subskrypcji dostawcy rozwiązań w chmurze, wykonaj następujące kroki, aby wdrożyć platformę Azure:

1. Przewiń w dół i kliknij prawym przyciskiem myszy pozycję **Wdróż na platformie Azure**, a następnie wybierz pozycję **Kopiuj lokalizację linku**.
2. Otwórz Edytor tekstu, taki jak Notatnik, i Wklej tam link.
3. Bezpośrednio po "https://portal.azure.com/" i przed wystąpieniem (#) wprowadź znak (@), a po nim nazwę domeny dzierżawy. Oto przykład formatu, którego należy użyć: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Zaloguj się do Azure Portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
5. Wklej skopiowany link do edytora tekstu na pasku adresu.

Aby uzyskać wskazówki dotyczące parametrów, które należy wprowadzić dla danego scenariusza, zobacz [plik Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)pulpitu wirtualnego systemu Windows. Plik Readme jest zawsze aktualizowany przy użyciu najnowszych zmian.

## <a name="assign-users-to-the-desktop-application-group"></a>Przypisywanie użytkowników do grupy aplikacji klasycznych

Po zakończeniu szablonu Azure Resource Manager GitHub należy przypisać dostęp użytkownika przed rozpoczęciem testowania pełnych pulpitów sesji na maszynach wirtualnych.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

Aby przypisać użytkowników do grupy aplikacji klasycznych, Otwórz okno programu PowerShell i Uruchom to polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie Dodaj użytkowników do grupy aplikacji klasycznych za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Nazwa UPN użytkownika powinna być zgodna z tożsamością użytkownika w Azure Active Directory (na przykład user1@contoso.com). Jeśli chcesz dodać wielu użytkowników, musisz uruchomić to polecenie cmdlet dla każdego użytkownika.

Po wykonaniu tych kroków użytkownicy dodani do grupy aplikacji klasycznych mogą zalogować się do pulpitu wirtualnego systemu Windows z obsługiwanymi Pulpit zdalny klientami i zobaczyć zasób pulpitu sesji.

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).