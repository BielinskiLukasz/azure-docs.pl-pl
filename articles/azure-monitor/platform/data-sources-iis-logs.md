---
title: Dzienniki usług IIS w Azure Monitor | Microsoft Docs
description: Internet Information Services (IIS) przechowuje aktywność użytkownika w plikach dziennika, które mogą być zbierane przez Azure Monitor.  W tym artykule opisano sposób konfigurowania kolekcji dzienników usług IIS i szczegółów dotyczących rekordów tworzonych w Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: a865f43585ccbb31569e2ca0987aae62a89a9281
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932484"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Zbieranie dzienników usług IIS w Azure Monitor
Internet Information Services (IIS) przechowuje aktywność użytkownika w plikach dziennika, które mogą być zbierane przez Azure Monitor i przechowywane jako [dane dziennika](data-platform.md).

![Dzienniki usług IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurowanie dzienników usług IIS
Azure Monitor zbiera wpisy z plików dziennika utworzonych przez usługi IIS, dlatego należy [skonfigurować usługi IIS do rejestrowania](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor obsługuje tylko pliki dzienników usług IIS przechowywane w formacie W3C i nie obsługuje pól niestandardowych ani zaawansowanego rejestrowania usług IIS. Nie zbiera dzienników w formacie NCSA lub IIS.

Skonfiguruj dzienniki usług IIS w Azure Monitor z poziomu [menu Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).  Nie jest wymagana żadna konfiguracja, a nie opcja **zbierania plików dziennika usług IIS w formacie W3C**.


## <a name="data-collection"></a>Zbieranie danych
Azure Monitor zbiera wpisy dziennika usług IIS z każdego agenta przy każdej zmianie sygnatury czasowej dziennika. Dziennik jest odczytywany co **5 minut**. Jeśli z jakiegokolwiek powodu usługi IIS nie zaktualizują sygnatury czasowej przed upływem czasu przerzucenia podczas tworzenia nowego pliku, zostaną zebrane wpisy po utworzeniu nowego pliku. Częstotliwość tworzenia nowych plików jest kontrolowana przez ustawienie **harmonogramu przerzucania pliku dziennika** dla witryny IIS, która jest domyślnie raz dziennie. Jeśli ustawienie jest **co godzinę**, Azure monitor zbiera dziennik co godzinę. Jeśli to ustawienie ma wartość **codziennie**, Azure monitor zbiera dziennik co 24 godziny.


## <a name="iis-log-record-properties"></a>Właściwości rekordu dziennika usług IIS
Rekordy dziennika usług IIS mają typ **W3CIISLog** i mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer |Nazwa komputera, z którego został zebrany zdarzenie. |
| Opcja |Adres IP klienta. |
| csMethod |Metoda żądania, taka jak GET lub POST. |
| csReferer |Lokacja, z której korzysta użytkownik z linkiem do bieżącej lokacji. |
| csUserAgent |Typ przeglądarki klienta. |
| csUserName |Nazwa uwierzytelnionego użytkownika, który uzyskał dostęp do serwera. Użytkownicy anonimowi są wskazywani przez łącznik. |
| csUriStem |Miejsce docelowe żądania, takie jak strona sieci Web. |
| csUriQuery |Zapytanie (jeśli istnieje), które klient próbował wykonać. |
| ManagementGroupName |Nazwa grupy zarządzania dla agentów Operations Manager.  W przypadku innych agentów jest to AOI\<identyfikator obszaru roboczego\> |
| RemoteIPCountry |Kraj/region adresu IP klienta. |
| RemoteIPLatitude |Szerokość geograficzna adresu IP klienta. |
| RemoteIPLongitude |Długość adresu IP klienta. |
| scStatus |Kod stanu HTTP. |
| scSubStatus |Kod błędu podstanu. |
| scWin32Status |Kod stanu systemu Windows. |
| kabl |Adres IP serwera sieci Web. |
| SourceSystem |Programu Operations Manager |
| sPort |Port na serwerze, z którym jest połączony klient. |
| sSiteName |Nazwa witryny usług IIS. |
| TimeGenerated |Data i godzina zarejestrowania wpisu. |
| timeTaken |Długość czasu przetwarzania żądania w milisekundach. |

## <a name="log-queries-with-iis-logs"></a>Rejestruj zapytania z dziennikami usług IIS
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy dziennika usług IIS.

| Zapytanie | Opis |
|:--- |:--- |
| W3CIISLog |Wszystkie rekordy dziennika usług IIS. |
| W3CIISLog &#124; , gdzie scStatus = = 500 |Wszystkie rekordy dziennika IIS ze stanem powrotu 500. |
| W3CIISLog &#124; podsumowania () według przelewu |Liczba wpisów dziennika IIS według adresu IP klienta. |
| W3CIISLog &#124; , gdzie csHost = = "www\.contoso.com &#124; ", Podsumuj liczbę () przez csUriStem |Liczba wpisów dziennika usług IIS według adresu URL dla hosta www\.contoso.com. |
| W3CIISLog &#124; podsumowanie sum (csBytes) przez komputer &#124; 500000 |Łączna liczba bajtów odebranych przez każdy komputer IIS. |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj Azure Monitor, aby zbierać inne [źródła danych](agent-data-sources.md) na potrzeby analizy.
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
