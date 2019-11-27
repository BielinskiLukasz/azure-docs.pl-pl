---
title: Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników analityka magazynu
description: Identyfikowanie i rozwiązywanie problemów z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i optymalizowanie aplikacji klienckiej.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196521"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników analityka magazynu

Diagnozowanie i rozwiązywanie problemów to kluczowa umiejętność tworzenia i obsługi aplikacji klienckich za pomocą usługi Azure Storage.

Ze względu na rozproszony charakter aplikacji platformy Azure, diagnozowanie i rozwiązywanie problemów z błędami i wydajnością może być bardziej skomplikowane niż w tradycyjnych środowiskach.

W poniższych krokach pokazano, jak identyfikować i rozwiązywać problemy z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i zoptymalizować aplikację kliencką.

## <a name="recommended-steps"></a>Zalecane czynności

1. Pobierz [dzienniki analityka magazynu](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Użyj poniższego skryptu programu PowerShell, aby przekonwertować nieprzetworzone dzienniki w formacie tabelarycznym:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skrypt uruchomi okno graficznego interfejsu użytkownika, w którym można filtrować informacje według kolumn, jak pokazano poniżej.

   ![Okno analizatora dzienników analitycznych magazynu](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Zawęź wpisy dziennika na podstawie "operacji-Type" i poszukaj wpisu dziennika utworzonego w przedziale czasowym problemu.

   ![Wpisy dziennika typu operacji](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. W czasie, gdy wystąpi problem, ważne są następujące wartości:

   * Operation-type = GetBlob
   * żądanie-status = SASNetworkError
   * Kompleksowe-to-end-in-MS = 8453
   * Serwer-opóźnienie-in-MS = 391

   Opóźnienie end-to-end jest obliczane przy użyciu następującego równania:

   * Opóźnienie od końca do końca = opóźnienie serwera i opóźnienie klienta

   Oblicz opóźnienie klienta przy użyciu wpisu dziennika:

   * Opóźnienie klienta = zakończenie do końca — serwer — opóźnienie

          * Example: 8453 – 391 = 8062ms

   Poniższa tabela zawiera informacje o dużym czasie opóźnienia i stanem żądania wyniki:

   |   |Stanem żądania =<br>Powodzenie|Stanem żądania =<br>SYGNATUR NetworkError|Zalecenie|
   |---|---|---|---|
   |GetBlob|Yes|Nie|[**Operacja GetBlob:** Stanem żądania = sukces](#getblob-operation-requeststatus--success)|
   |GetBlob|Nie|Yes|[**Operacja GetBlob:** Stanem żądania = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Yes|Nie|[**Operacja Put:** Stanem żądania = sukces](#put-operation-requeststatus--success)|
   |PutBlob|Nie|Yes|[**Operacja Put:** Stanem żądania = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Wyniki stanu

### <a name="getblob-operation-requeststatus--success"></a>Operacja GetBlob: stanem żądania = sukces

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Serwer — opóźnienie
* Klient — opóźnienie

W **operacji GetBlob** z **stanem żądania = Success**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, oznacza to, że usługa Azure Storage zajmuje dużo czasu na zapisanie danych do klienta. To opóźnienie wskazuje na problem po stronie klienta.

**Zaleca**

* Zbadaj kod w kliencie.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operacja GetBlob: stanem żądania = (SAS) NetworkError

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Serwer — opóźnienie
* Klient — opóźnienie

W **operacji GetBlob** z **stanem żądania = (SAS) NetworkError**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, najczęściej spotykanym problemem jest odłączenie klienta przed upływem limitu czasu w usłudze Storage.

**Zaleca**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="put-operation-requeststatus--success"></a>Operacja Put: stanem żądania = sukces

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Serwer — opóźnienie
* Klient — opóźnienie

W **operacji Put** z **stanem żądania = Success**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, oznacza to, że klient poświęca więcej czasu na wysyłanie danych do usługi Azure Storage. To opóźnienie wskazuje na problem po stronie klienta.

**Zaleca**

* Zbadaj kod w kliencie.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operacja Put: stanem żądania = (SAS) NetworkError

Sprawdź następujące wartości, jak wspomniano w kroku 5 sekcji "zalecane kroki":

* Opóźnienie końca do końca
* Serwer — opóźnienie
* Klient — opóźnienie

W **operacji PutBlob** z **stanem żądania = (SAS) NetworkError**, jeśli **Maksymalny czas** jest poświęcany na **opóźnienia klienta**, najczęściej spotykanym problemem jest odłączenie klienta przed upływem limitu czasu w usłudze Storage.

**Zaleca**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Aby zbadać problemy z łącznością sieciową z poziomu klienta, należy użyć programu Wireshark, Microsoft Message Analyzer lub Tcping.

