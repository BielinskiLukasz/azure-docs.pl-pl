---
title: Utwórz pulę hosta za pomocą portalu Azure Marketplace (wersja zapoznawcza) — platformy Azure
description: Jak utworzyć pulę hosta Windows pulpitu wirtualnego przy użyciu portalu Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: a1d172f219580b390ba4855928d6f6a68c8269b9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318113"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace-preview"></a>Samouczek: Utwórz pulę hosta za pomocą portalu Azure Marketplace (wersja zapoznawcza)

Pule hosta to zbiór przynajmniej jednej identycznych maszyn wirtualnych w środowiskach dzierżawy (wersja zapoznawcza) Windows pulpitu wirtualnego. Każda pula hosta może zawierać grupy aplikacji, które użytkownicy mogą korzystać z tak, jak w fizycznych pulpitu.

W tym artykule opisano sposób tworzenia puli hosta w ramach dzierżawy Windows pulpitu wirtualnego przy użyciu oferty Microsoft Azure Marketplace. Obejmuje to tworzenie puli hosta w Windows pulpitu wirtualnego, tworząc grupę zasobów z maszynami wirtualnymi w subskrypcji platformy Azure, dołączenie do tych maszyn wirtualnych do domeny usługi Active Directory i rejestrowanie maszyn wirtualnych przy użyciu wirtualnego pulpitu Windows.

Przed przystąpieniem do wykonywania [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Uruchom w portalu Azure Marketplace, oferty zainicjować obsługę nowej puli hosta

Aby uruchomić w portalu Azure Marketplace, oferty zainicjować obsługę nowej puli hosta:

1. Wybierz **+** lub **+ Utwórz zasób**.
2. Wprowadź **pulpitu wirtualnego Windows** w oknie wyszukiwania portalu Marketplace.
3. Wybierz **Windows pulpitu wirtualnego — Aprowizowanie puli hosta**, a następnie wybierz **Utwórz**.

Postępuj zgodnie ze wskazówkami, aby wprowadzić informacje dotyczące odpowiednich bloków.

### <a name="basics"></a>Podstawy

Oto, co możesz zrobić bloku podstawy:

1. Wprowadź nazwę puli hosta, która jest unikatowa w ramach dzierżawy Windows pulpitu wirtualnego.
2. Wybierz opcję odpowiednią dla osobisty pulpit. Jeśli wybierzesz **tak**, każdy użytkownik, który nawiązuje połączenie z tej puli hosta zostaną trwale przypisane do maszyny wirtualnej.
3. Wprowadź rozdzielaną przecinkami listę użytkowników, którzy mogą zalogować się do klientów pulpitu wirtualnego Windows i dostęp do pulpitu, po zakończeniu oferty w portalu Azure Marketplace. Na przykład, jeśli chcesz przypisać user1@contoso.com i user2@contoso.com dostępu, wprowadź "user1@contoso.com,user2@contoso.com."
4. Wybierz **Utwórz nową** i podaj nazwę dla nowej grupy zasobów.
5. Aby uzyskać **lokalizacji**, wybrać tej samej lokalizacji co sieć wirtualna, która ma połączenie z serwerem usługi Active Directory.
6. Kliknij przycisk **OK**.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

Konfiguruj bloku maszyn wirtualnych:

1. Zaakceptuj wartości domyślne lub dostosować liczbę i rozmiar maszyn wirtualnych.
2. Wprowadź prefiks dla nazwy maszyn wirtualnych. Na przykład jeśli wprowadzasz nazwę "prefiks", maszyn wirtualnych zostanie wywołana "prefiks-0," "prefiks-1" i tak dalej.
3. Kliknij przycisk **OK**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

Do bloku ustawienia maszyny wirtualnej:

1. Wybierz **źródło obrazu** i wprowadź odpowiednie informacje dotyczące sposobu znaleźć go i zapisz go. Jeśli zrezygnujesz z używania dysków zarządzanych, wybierz konto magazynu zawierające plik VHD.
2. Wprowadź główną nazwę użytkownika i hasło dla konta domeny, do której zostaną dołączone maszyny wirtualne do domeny usługi Active Directory. Tej samej nazwy użytkownika i hasło zostaną utworzone na maszynach wirtualnych, jako konto lokalne. Później możesz zresetować te konta lokalnego.
3. Wybierz sieć wirtualną, który ma połączenie z serwerem usługi Active Directory, a następnie wybierz podsieć do hostowania maszyn wirtualnych.
4. Kliknij przycisk **OK**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informacje o dzierżawie Windows pulpitu wirtualnego

Bloku informacji o dzierżawie pulpitu wirtualnego Windows:

1. Wprowadź **Nazwa grupy dzierżawy pulpitu wirtualnego Windows** grupy dzierżawy, który zawiera Twojej dzierżawy. Jeśli nie masz nazwy grupy określonej dzierżawy planowane, pozostaw domyślne.
2. Wprowadź **nazwa dzierżawy pulpitu wirtualnego Windows** dla dzierżawy zostanie utworzona ta pula hosta w.
3. Określ typ poświadczeń, których chcesz użyć do uwierzytelnienia się jako dzierżawy pulpitu wirtualnego Windows właściciela pulpitu zdalnego. Jeśli wybierzesz **nazwy głównej usługi**, należy również podać **Identyfikatora dzierżawy usługi Azure AD** skojarzony z jednostką usługi.
4. Wprowadź albo poświadczenia dla konta administratora dzierżawy. Obsługiwane są tylko jednostki usługi przy użyciu poświadczeń hasła.
5. Kliknij przycisk **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Kończenie konfiguracji, a następnie utwórz maszynę wirtualną

Dla ostatnich dwóch bloków:

1. W **Podsumowanie** bloku, przejrzyj informacje o instalacji. Jeśli potrzebujesz wprowadzić zmiany, wróć do bloku odpowiedniego i wprowadzić zmiany przed kontynuowaniem. Jeśli wydaje się odpowiednie informacje, wybierz **OK**.
2. W **Kup** bloku, przejrzyj dodatkowe informacje na temat zakupu w portalu Azure Marketplace.
3. Wybierz **Utwórz** do wdrożenia hostów puli.

W zależności od liczby maszyn wirtualnych, tworzysz ten proces może potrwać 30 minut lub dłużej.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcjonalnie) Przypisać użytkowników do grupy aplikacji klasycznej

Po zakończeniu oferty w portalu Azure Marketplace, można przypisać dodatkowych użytkowników do grupy aplikacji pulpitu, przed rozpoczęciem testowania pulpity sesji pełną na maszynach wirtualnych. Jeśli dodano już domyślnych użytkowników w ramach oferty w portalu Azure Marketplace i nie chcesz dodać więcej, możesz pominąć tę sekcję.

Aby przypisać użytkowników do grupy aplikacji klasycznej, możesz otworzyć okno programu PowerShell. Po tym należy wprowadzić następujące dwa polecenia cmdlet.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska wirtualnego pulpitu Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ustaw kontekst do grupy dzierżawy pulpitu wirtualnego Windows, które określiłeś w portalu Azure Marketplace oferuje przy użyciu następującego polecenia cmdlet. Jeśli dzierżawa pulpitu wirtualnego Windows wartość grupy jako wartość domyślna w witrynie Azure Marketplace oferty, możesz pominąć ten krok.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Po wykonaniu tych dwóch elementów, można dodać użytkowników do grupy aplikacji klasycznej przy użyciu tego polecenia cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Nazwa UPN użytkownika powinna być zgodna tożsamość użytkownika w usłudze Azure Active Directory (na przykład user1@contoso.com). Jeśli chcesz dodać wielu użytkowników, należy uruchomić to polecenie cmdlet dla każdego użytkownika.

Po wykonaniu tych czynności, dodano użytkowników do grupy aplikacji klasycznej można zalogować się do Windows pulpitu wirtualnego przy użyciu obsługiwanych klientów usług pulpitu zdalnego i zobacz zasób sesji komputerów stacjonarnych.

Poniżej przedstawiono bieżąca liczba obsługiwanych klientów:

- [Klient pulpitu zdalnego dla Windows 7 i Windows 10](connect-windows-7-and-10.md)
- [Klient sieci web Windows pulpitu wirtualnego](connect-web.md)

## <a name="next-steps"></a>Kolejne kroki

Teraz, po nawiązaniu hosta, puli i przypisanych użytkowników do jego pulpitu, możesz również wypełnić puli hosta za pomocą RemoteApps. Aby dowiedzieć się więcej na temat sposobu zarządzania aplikacjami w Windows pulpitu wirtualnego, zobacz samouczek grup aplikacji Zarządzanie.

> [!div class="nextstepaction"]
> [Zarządzać samouczek grup aplikacji](./manage-app-groups.md)
