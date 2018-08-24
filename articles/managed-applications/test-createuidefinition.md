---
title: Testowanie definicji interfejsu użytkownika usługi Azure Managed Applications | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób testowania środowiska użytkownika do tworzenia aplikacji zarządzanych platformy Azure za pośrednictwem portalu.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: c88bdce64e88f8639da2c4ebb01f4594fccff8a0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747092"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testowanie interfejsu portalu platformy Azure dla aplikacji zarządzanej
Po [tworzenia pliku createUiDefinition.json](create-uidefinition-overview.md) dla aplikacji zarządzanych platformy Azure, musisz przetestowanie środowiska użytkownika. Aby uprościć testowanie, należy użyć skryptu, który ładuje plik w portalu. Nie potrzebujesz rzeczywiście wdrożyć Twoją zarządzaną aplikacją.

## <a name="prerequisites"></a>Wymagania wstępne

* A **createUiDefinition.json** pliku. Jeśli nie masz tego pliku, skopiuj [przykładowy plik](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json) i zapisać go lokalnie.

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="download-test-script"></a>Pobierz skrypt testu

Aby przetestować interfejs użytkownika w portalu, skopiuj jeden z poniższych skryptów na komputer lokalny:

* [Skrypt programu PowerShell ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt interfejsu wiersza polecenia platformy Azure w ładowane bezpośrednio](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Uruchom skrypt

Aby wyświetlić plik interfejsu w portalu, uruchom pobranego skryptu. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure, a następnie przekazuje plik createUiDefinition.json do konta magazynu. Konto magazynu jest tworzone podczas pierwszego uruchomienia skryptu, lub jeśli konto magazynu zostało usunięte. Jeśli konto magazynu już istnieje w subskrypcji platformy Azure, skrypt ponownie go używa. Skrypt spowoduje otwarcie portalu i ładuje plik z konta magazynu.

Podaj lokalizację dla konta magazynu, a następnie określ folder, który zawiera plik createUiDefinition.json.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Jeśli plik createUiDefinition.json znajduje się w tym samym folderze co skrypt, a masz już utworzone konto magazynu, nie trzeba podać te parametry.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>Testowanie interfejsu użytkownika

Skrypt zostanie otwarty w nowej karcie w przeglądarce. Wyświetla portalu przy użyciu interfejsu do tworzenia aplikacji zarządzanej.

![Wyświetl portal](./media/test-createuidefinition/view-portal.png)

Przed rozpoczęciem wypełniania pól, należy otworzyć narzędzia Web Developer Tools w przeglądarce. **Konsoli** ważne komunikaty dotyczące interfejsu.

![Wybierz pozycję Konsola](./media/test-createuidefinition/select-console.png)

Jeśli Twoja definicja interfejsu zawiera błąd, zostanie wyświetlony opis w konsoli.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

Podaj wartości dla pól. Po zakończeniu przekonasz się wartości, które są przekazywane do szablonu.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

Te wartości można użyć jako plik parametrów do testowania wdrożenia szablonu.

## <a name="troubleshooting-the-interface"></a>Rozwiązywanie problemów z interfejsu

Niektóre typowe błędy, które można napotkać, to:

* W portalu nie załadować interfejsu. Zamiast tego zawiera ikonę chmury z listy zakończenia. Zazwyczaj zobaczysz tę ikonę, gdy występuje błąd składni w pliku. Otwórz plik w programie VS Code (lub innego edytora JSON, zawierającą sprawdzanie poprawności schematu) i Znajdź błędy składniowe.

* Portal zawiesza się na ekranie Podsumowanie. Zazwyczaj ten przeszkód występuje, gdy znajduje się błąd w sekcji danych wyjściowych. Na przykład użytkownik może mieć odwołuje się do formant, który nie istnieje.

* Parametr w danych wyjściowych jest pusty. Parametr może odwoływać się do właściwości, która nie istnieje. Na przykład odwołanie do formantu jest prawidłowy, ale odwołania do właściwości jest nieprawidłowa.

## <a name="test-your-solution-files"></a>Testowanie pliki rozwiązania

Teraz, gdy masz pewność, że portal interfejsu działa zgodnie z oczekiwaniami, nadszedł czas na zweryfikować, że plik createUiDefinition prawidłowo jest zintegrowany z pliku mainTemplate.json. Można uruchomić skrypt do weryfikowania testami zawartości pliki rozwiązania, dołączając plik createUiDefinition. Skrypt sprawdza poprawność ze składnią pliku JSON, sprawdza, czy wyrażenie regularne wyrażenia pól tekstowych i upewnia się, że wartości danych wyjściowych w interfejsie portalu odpowiadać parametrom szablonu. Aby uzyskać informacje na temat uruchamiania tego skryptu, zobacz [Uruchom sprawdzanie poprawności statycznego dla szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Kolejne kroki

Po upewnieniu się, portalu interfejsu, więcej informacji na temat tworzenia swojej [Azure zarządzanych aplikacji, które są dostępne w portalu Marketplace](publish-marketplace-app.md).
