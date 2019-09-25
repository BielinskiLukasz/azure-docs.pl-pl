---
title: Przygotuj do zmiany formatu Azure Monitor dzienników diagnostycznych
description: Dzienniki diagnostyczne platformy Azure zostaną przeniesione do użycia Dołącz obiekty blob 1 listopada 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: a5589828570455c61f857dbeadc896e8fef27178
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258381"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Przygotuj do zmiany formatu Azure Monitor dzienników diagnostycznych archiwizowanych na koncie magazynu

> [!WARNING]
> W przypadku wysyłania [dzienników diagnostycznych zasobów platformy Azure lub metryk do konta magazynu przy użyciu ustawień diagnostycznych zasobów](./../../azure-monitor/platform/archive-diagnostic-logs.md) lub [dzienników aktywności do konta magazynu przy użyciu profilów dzienników](./../../azure-monitor/platform/archive-activity-log.md), format danych na koncie magazynu zmieni się na linie JSON na Lis. 1, 2018. Poniższe instrukcje opisują wpływ i sposobu aktualizowania narzędzi do obsługi nowego formatu. 
>
> 

## <a name="what-is-changing"></a>Co się zmienia

Azure Monitor oferuje możliwość przesyłania danych diagnostycznych zasobów i danych dziennika aktywności do konta usługi Azure Storage, Event Hubs przestrzeni nazw lub obszaru roboczego Log Analytics w Azure Monitor. Aby rozwiązać problem z wydajnością systemu, **1 listopada 2018 o godzinie 12:00 północy** , format przesyłania danych dziennika do magazynu obiektów BLOB zostanie zmieniony. Jeśli masz narzędzia odczytujące dane z magazynu obiektów blob, musisz zaktualizować swoje narzędzia, aby zrozumieć nowy format danych.

* W czwartek, 1 listopada 2018 o 12:00 północy czasu UTC, format obiektu BLOB zmieni się na [wiersz JSON](http://jsonlines.org/). Oznacza to, że każdy rekord zostanie rozdzielony znakiem nowego wiersza, bez tablicy rekordów zewnętrznych i bez przecinków między rekordami JSON.
* Format obiektu BLOB zmienia się dla wszystkich ustawień diagnostycznych jednocześnie w ramach wszystkich subskrypcji. Pierwszy plik PT1H. JSON emitowany przez 1 listopada będzie używać tego nowego formatu. Nazwy obiektów blob i kontenerów pozostają takie same.
* Ustawienie ustawień diagnostycznych między teraz a listopadem nadal będzie emitować dane w bieżącym formacie do 1 listopada.
* Ta zmiana będzie wykonywana jednocześnie we wszystkich regionach chmury publicznej. Ta zmiana nie będzie jeszcze wykonywana w Microsoft Azure obsługiwane przez firmę 21Vianet, platformę Azure (Niemcy) ani chmurę Azure Government.
* Ta zmiana ma wpływ na następujące typy danych:
  * [Dzienniki diagnostyczne zasobów platformy Azure](archive-diagnostic-logs.md) ([Zobacz tutaj listę zasobów](diagnostic-logs-schema.md))
  * [Metryki zasobów platformy Azure eksportowane przez ustawienia diagnostyczne](diagnostic-settings.md)
  * [Dane dziennika aktywności platformy Azure eksportowane przez profile dziennika](archive-activity-log.md)
* Ta zmiana nie ma wpływu na:
  * Dzienniki przepływu sieci
  * Dzienniki usługi platformy Azure nie zostały jeszcze udostępnione za pomocą Azure Monitor (na przykład Azure App Service dzienników diagnostycznych, dzienników analizy magazynu)
  * Routing dzienników diagnostycznych platformy Azure i dzienników aktywności do innych miejsc docelowych (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak sprawdzić, czy ma to wpływ

Ta zmiana ma wpływ tylko na to, że:
1. Wysyła dane dziennika do konta usługi Azure Storage przy użyciu ustawienia diagnostyki zasobów i
2. Narzędzia, które są zależne od struktury JSON tych dzienników w magazynie.
 
Aby określić, czy masz ustawienia diagnostyczne zasobów wysyłające dane do konta usługi Azure Storage, możesz przejść do sekcji **monitorowanie** portalu, kliknąć pozycję **Ustawienia diagnostyczne**i zidentyfikować wszystkie zasoby, które mają **diagnostykę Stan** ustawiony na **włączone**:

![Blok ustawień diagnostycznych Azure Monitor](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Jeśli stan diagnostyki jest ustawiony na włączone, masz aktywne ustawienie diagnostyczne dla tego zasobu. Kliknij zasób, aby sprawdzić, czy żadne z ustawień diagnostycznych wysyła dane do konta magazynu:

![Konto magazynu jest włączone](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Jeśli masz zasoby wysyłające dane do konta magazynu przy użyciu tych ustawień diagnostycznych zasobów, ta zmiana będzie miała wpływ na format danych na tym koncie magazynu. Jeśli nie masz niestandardowych narzędzi, które działają poza tymi kontami magazynu, zmiana formatu nie wpłynie.

### <a name="details-of-the-format-change"></a>Szczegóły zmiany formatu

Bieżący format pliku PT1H. JSON w usłudze Azure Blob Storage używa tablicy rekordów JSON. Oto przykład pliku dziennika magazynu kluczy:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Nowy format używa [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Oto, co powyższy przykład będzie wyglądać w pliku PT1H. JSON po zmianie:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ten nowy format umożliwia Azure Monitor wypychania plików dziennika przy użyciu [dołączanych obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), co jest bardziej wydajne w przypadku ciągłego dołączania nowych danych zdarzeń.

## <a name="how-to-update"></a>Jak zaktualizować

Tylko wtedy, gdy istnieje narzędzie niestandardowe, które pozyskuje te pliki dziennika do dalszej obróbki. Jeśli korzystasz z zewnętrznego narzędzia do analizy dzienników lub SIEM, zalecamy [Używanie centrów zdarzeń do pozyskiwania tych danych](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integracja centrów zdarzeń jest łatwiejsza pod względem przetwarzania dzienników z wielu usług i lokalizacji zakładek w konkretnym dzienniku.

Narzędzia niestandardowe należy zaktualizować w taki sposób, aby obsługiwały zarówno bieżący format, jak i format linii JSON opisany powyżej. Zapewni to, że gdy dane zaczynają pojawiać się w nowym formacie, narzędzia nie są przerywane.

## <a name="next-steps"></a>Następne kroki

* Informacje o [archiwizowaniu dzienników diagnostycznych zasobów na koncie magazynu](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Informacje [na temat archiwizowania danych dziennika aktywności na koncie magazynu](./../../azure-monitor/platform/archive-activity-log.md)

