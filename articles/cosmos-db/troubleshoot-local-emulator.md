---
title: Rozwiązywanie problemów podczas korzystania z emulatora usługi Azure Cosmos
description: Dowiedz się, jak Troubleshot usługę niedostępną, certyfikat, szyfrowanie i przechowywanie problemów podczas korzystania z emulatora usługi Azure Cosmos.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: cf174d45f33c50ce93b45b19c6030cf42cb20983
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081454"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Rozwiązywanie problemów podczas korzystania z emulatora usługi Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulator usługi Azure Cosmos udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB na potrzeby programowania. Porady w tym artykule ułatwiają rozwiązywanie problemów występujących podczas instalowania lub korzystania z emulatora usługi Azure Cosmos. 

Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Możesz zresetować dane, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na pasku zadań, a następnie klikając polecenie Zresetuj dane.... Jeśli to nie rozwiąże błędów, można odinstalować emulator i wszystkie starsze wersje emulatora, jeśli zostały znalezione, Usuń *C:\Program files\Azure Cosmos DB Directory emulator* i ponownie zainstaluj emulator. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](local-emulator.md#uninstall). Alternatywnie, jeśli Resetowanie danych nie działa, przejdź do `%LOCALAPPDATA%\CosmosDBEmulator` lokalizacji i Usuń folder.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Rozwiązywanie problemów z uszkodzonymi licznikami wydajności systemu Windows

* W przypadku awarii emulatora usługi Azure Cosmos należy zebrać pliki zrzutu z `%LOCALAPPDATA%\CrashDumps` folderu, skompresować je i otworzyć bilet pomocy technicznej z [Azure Portal](https://portal.azure.com).

* Jeśli wystąpi awaria w programie `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` , może to być objawem, w którym liczniki wydajności są w stanie uszkodzenia. Zwykle uruchomienie następującego polecenia z poziomu wiersza polecenia administratora rozwiązuje problem:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

* W przypadku wystąpienia problemu z łącznością należy [zebrać pliki śledzenia](#trace-files), skompresować je i otworzyć bilet pomocy technicznej w [Azure Portal](https://portal.azure.com).

* Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem. Alternatywnie można uruchomić emulator przy użyciu/DisableRIO, co spowoduje przełączenie komunikacji między sieciami emulatora do zwykłego interfejsu Winsock. 

* Jeśli wystąpi **"zabronione", "komunikat": "żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrie. Sprawdź ustawienia Minimalny dozwolony protokół SSL/TLS konta... "** problemy z łącznością mogą być spowodowane przez globalne zmiany w systemie operacyjnym (na przykład kompilacja Insider Preview 20170) lub ustawienia przeglądarki, które domyślnie włączają protokół TLS 1,3. Podobny błąd może wystąpić podczas używania zestawu SDK do wykonywania żądania względem emulatora Cosmos, takiego jak **Microsoft.Azure.Documents.DocumentClientException: żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrem. Sprawdź ustawienie Minimalny dozwolony protokół SSL/TLS konta** . Obecnie jest to oczekiwane zachowanie, ponieważ emulator usługi Cosmos akceptuje tylko protokół TLS 1.2 i z nim współpracuje. Zalecane obejście to zmiana ustawień i domyślnego protokołu TLS 1,2; na przykład w Menedżerze usług IIS przejdź do lokalizacji "Lokacje" — > "domyślne witryny sieci Web" i Znajdź "powiązania witryny" dla portu 8081 i edytuj je, aby wyłączyć protokół TLS 1,3. Podobną operację można wykonać dla przeglądarki internetowej za pomocą opcji „Ustawienia”.

* Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Zresetuj dane emulatora, klikając prawym przyciskiem myszy ikonę widoczną na pasku powiadomień systemu Windows i wybierając pozycję **Zresetuj dane** .

## <a name="collect-trace-files"></a><a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. Przejdź do ścieżki, w której zainstalowano emulator:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Zamknij emulator i obejrzyj zasobnik systemowy, aby upewnić się, że program został zamknięty. Ukończenie tego procesu może potrwać minutę. Możesz również wybrać pozycję **Wyjdź** w interfejsie użytkownika emulatora usługi Azure Cosmos.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Rozpocznij rejestrowanie przy użyciu następującego polecenia:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Uruchamianie emulatora

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Odtwórz problem. Jeśli Eksplorator danych nie działa, musisz poczekać, aż przeglądarka zostanie otwarta przez kilka sekund, aby przechwycić błąd.

1. Zatrzymaj rejestrowanie przy użyciu następującego polecenia:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Przejdź do `%ProgramFiles%\Azure Cosmos DB Emulator` ścieżki i Znajdź plik *docdbemulator_000001. etl* .

1. Otwórz bilet pomocy technicznej w [Azure Portal](https://portal.azure.com) i Uwzględnij plik ETL wraz z krokami Odtwórz.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób debugowania problemów z emulatorem lokalnym. Teraz możesz przechodzić do następnych artykułów:

* [Eksportowanie certyfikatów emulatora usługi Azure Cosmos do użytku z aplikacjami Java, Python i Node.js](local-emulator-export-ssl-certificates.md)
* [Używanie parametrów wiersza polecenia i poleceń programu PowerShell do sterowania emulatorem](emulator-command-line-parameters.md)
