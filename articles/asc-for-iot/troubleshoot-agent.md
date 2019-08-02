---
title: Azure Security Center Przewodnik rozwiązywania problemów z agentem zabezpieczeń IoT Linux | Microsoft Docs
description: Rozwiązywanie problemów z pracą z Azure Security Centerami dla agentów zabezpieczeń IoT dla systemu Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600568"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Przewodnik rozwiązywania problemów z agentem zabezpieczeń (Linux)

W tym artykule wyjaśniono, jak rozwiązać potencjalne problemy w procesie uruchamiania agenta zabezpieczeń.

Azure Security Center dla agenta IoT jest samoczynnie uruchamiany natychmiast po zakończeniu instalacji. Proces uruchamiania agenta obejmuje odczytywanie konfiguracji lokalnej, nawiązywanie połączenia z usługą Azure IoT Hub i pobieranie konfiguracji zdalnej przędzy. Niepowodzenie w jednym z tych kroków może spowodować niepowodzenie działania agenta zabezpieczeń.

W tym przewodniku rozwiązywania problemów dowiesz się, jak:
> [!div class="checklist"]
> * Sprawdź, czy Agent zabezpieczeń jest uruchomiony
> * Pobieranie błędów agenta zabezpieczeń
> * Zrozumienie i korygowanie błędów agentów zabezpieczeń 

## <a name="validate-if-the-security-agent-is-running"></a>Sprawdź, czy Agent zabezpieczeń jest uruchomiony

1. Aby sprawdzić, czy Agent zabezpieczeń jest uruchomiony, zaczekaj kilka minut po zainstalowaniu agenta i uruchom następujące polecenie. 
     <br>

    **Agent języka C**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C#Odczynnik**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Jeśli polecenie zwróci pusty wiersz, Agent zabezpieczeń nie został uruchomiony pomyślnie.    

## <a name="force-stop-the-security-agent"></a>Wymuś zatrzymanie agenta zabezpieczeń 
W przypadkach, gdy nie można uruchomić agenta zabezpieczeń, Zatrzymaj agenta za pomocą poniższego polecenia, a następnie przejdź do tabeli błędów poniżej:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Pobieranie błędów agenta zabezpieczeń
1. Pobierz błędy agenta zabezpieczeń, uruchamiając następujące polecenie:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. Polecenie Pobierz błąd agenta zabezpieczeń pobiera wszystkie dzienniki utworzone przez Azure Security Center dla agenta IoT. Skorzystaj z poniższej tabeli, aby poznać błędy i wykonać odpowiednie czynności naprawcze. 

> [!Note]
> Dzienniki błędów są wyświetlane w kolejności chronologicznej. Pamiętaj, aby zanotować sygnaturę czasową każdego błędu, aby ułatwić korygowanie. 

## <a name="restart-the-agent"></a>Ponowne uruchamianie agenta

1. Po znalezieniu i naprawieniu błędu agenta zabezpieczeń spróbuj ponownie uruchomić agenta, uruchamiając następujące polecenie. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Powtórz poprzedni proces, aby pobrać zatrzymanie i pobrać błędy, jeśli Agent nadal kończy pracę. 

## <a name="understand-security-agent-errors"></a>Omówienie błędów agenta zabezpieczeń

Większość błędów agentów zabezpieczeń jest wyświetlanych w następującym formacie: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Kod błędu | Kod podrzędny błędu | Szczegóły błędu | Koryguj C | KorygujC# |
|:-----------|:---------------|:--------|:------------|:------------|
| Konfiguracja lokalna | Brakująca konfiguracja | Brak konfiguracji w lokalnym pliku konfiguracyjnym. Komunikat o błędzie powinien określać, który klucz nie istnieje. | Dodaj brakujący klucz do pliku/var/LocalConfiguration.json, aby uzyskać szczegółowe informacje, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) .| Dodaj brakujący klucz do pliku General. config, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) , aby uzyskać szczegółowe informacje. |
| Konfiguracja lokalna | Niemożliwa analiza konfiguracji | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, który klucz nie może zostać przeanalizowany. Nie można przeanalizować wartości konfiguracji, ponieważ wartość nie znajduje się w oczekiwanym typie lub wartość znajduje się poza zakresem. | Popraw wartość klucza w pliku/var/LocalConfiguration.json, tak aby był zgodny ze schematem LocalConfiguration, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) , aby uzyskać szczegółowe informacje. |  Popraw wartość klucza w pliku General. config, tak aby był zgodny ze schematem, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) , aby uzyskać szczegółowe informacje.|
| Konfiguracja lokalna | Format pliku | Nie można przeanalizować pliku konfiguracji. | Plik konfiguracji jest uszkodzony, pobierz agenta i zainstaluj go ponownie. | |
| Konfiguracja zdalna | limit czasu | Agent nie może pobrać sznurka modułu azureiotsecurity w określonym limicie czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna, i spróbuj ponownie. | Agent nie może pobrać sznurka modułu azureiotsecurity w ramach limitu czasu. | Upewnij się, że konfiguracja uwierzytelniania jest poprawna, i spróbuj ponownie. |
| Authentication | Plik nie istnieje | Plik w danej ścieżce nie istnieje. | Upewnij się, że plik istnieje w podanej ścieżce lub przejdź do pliku **LocalConfiguration. JSON** , a następnie Zmień konfigurację **ścieżki** . | Upewnij się, że plik istnieje w danej ścieżce lub przejdź do pliku **Authentication. config** , a następnie Zmień konfigurację **ścieżki** .|
| Authentication | Uprawnienie do pliku | Agent nie ma wystarczających uprawnień, aby otworzyć plik. | Nadaj użytkownikowi **asciotagent** uprawnienia do odczytu pliku w danej ścieżce. | Upewnij się, że plik jest dostępny. |
| Authentication | Format pliku | Dany plik ma nieprawidłowy format. | Upewnij się, że plik jest w poprawnym formacie. Obsługiwane typy plików to PFX i PEM. | Upewnij się, że plik jest prawidłowym plikiem certyfikatu. |
| Authentication | Brak autoryzacji | Agent nie może uwierzytelnić się w odniesieniu do IoT Hub z podanym poświadczeniami. | Sprawdź poprawność konfiguracji uwierzytelniania w pliku LocalConfiguration, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, i sprawdź, czy wpis tajny w pliku jest zgodny z uwierzytelnioną tożsamością. | Sprawdź poprawność konfiguracji uwierzytelniania w pliku Authentication. config, przejdź przez konfigurację uwierzytelniania i upewnij się, że wszystkie szczegóły są poprawne, a następnie sprawdź, czy wpis tajny w pliku jest zgodny z uwierzytelnioną tożsamością.
| Authentication | Nie znaleziono | Znaleziono urządzenie/moduł. | Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w IoT Hub i ma moduł azureiotsecurity bliźniaczy. |  Sprawdź poprawność konfiguracji uwierzytelniania — upewnij się, że nazwa hosta jest poprawna, urządzenie istnieje w IoT Hub i ma moduł azureiotsecurity bliźniaczy. |
| Authentication | Brakująca konfiguracja | Brak konfiguracji w pliku *Authentication. config* . Komunikat o błędzie powinien określać, który klucz nie istnieje. | Dodaj brakujący klucz do pliku *LocalConfiguration. JSON* .| Dodaj brakujący klucz do pliku *Authentication. config* , aby uzyskać szczegółowe informacje, zobacz [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Authentication | Niemożliwa analiza konfiguracji | Nie można przeanalizować wartości konfiguracji. Komunikat o błędzie powinien określać, który klucz nie może zostać przeanalizowany. Nie można przeanalizować wartości konfiguracji, ponieważ wartość nie jest oczekiwanego typu lub wartość znajduje się poza zakresem. |Popraw wartość klucza w pliku **LocalConfiguration. JSON** . |Popraw wartość klucza w pliku **Authentication. config** , aby dopasować schemat, zobacz [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) , aby uzyskać szczegółowe informacje.|
|

## <a name="restart-the-agent"></a>Ponowne uruchamianie agenta
1. Po znalezieniu i naprawieniu błędu agenta zabezpieczeń należy ponownie uruchomić agenta, uruchamiając następujące polecenie:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. W razie potrzeby powtórz poprzednie procesy, aby wymusić zatrzymanie agenta i pobrać błędy, jeśli Agent nadal kończy pracę. 

## <a name="next-steps"></a>Kolejne kroki
- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o [architekturze](architecture.md) Azure Security Center dla usługi IoT
- Włączanie Azure Security Center dla [usługi](quickstart-onboard-iot-hub.md) IoT
- Przeczytaj Azure Security Center dla usługi IoT [— często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- Omówienie [zaleceń](concept-recommendations.md)
- Informacje o [alertach](concept-security-alerts.md) zabezpieczeń
