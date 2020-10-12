---
title: Przygotuj do zmiany formatu Azure Monitor dzienników zasobów
description: Dzienniki zasobów platformy Azure przeniesione do użycia Dołącz obiekty blob 1 listopada 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 45b18352d88877a5d611f203d87da83fd0d58c6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077128"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Przygotuj się do zmiany formatu Azure Monitor dzienników platformy zarchiwizowanych na koncie magazynu

> [!WARNING]
> W przypadku wysyłania [dzienników zasobów platformy Azure lub metryk do konta magazynu przy użyciu ustawień diagnostycznych](./resource-logs.md#send-to-azure-storage) lub [dzienników aktywności do konta magazynu przy użyciu profilów dzienników](./resource-logs.md#send-to-azure-storage), format danych w ramach konta magazynu zmieni się na wiersz JSON na lis. 1, 2018. Poniższe instrukcje opisują wpływ i sposobu aktualizowania narzędzi do obsługi nowego formatu.
>

## <a name="what-changed"></a>Co zostało zmienione

Azure Monitor oferuje możliwość wysyłania dzienników zasobów i dzienników aktywności do konta usługi Azure Storage, Event Hubs przestrzeni nazw lub obszaru roboczego Log Analytics w Azure Monitor. Aby rozwiązać problem z wydajnością systemu, **1 listopada 2018 o godzinie 12:00 północy** , format przesyłania danych dziennika do magazynu obiektów BLOB został zmieniony. Jeśli masz narzędzia odczytujące dane z magazynu obiektów blob, musisz zaktualizować swoje narzędzia, aby zrozumieć nowy format danych.

* W czwartek, 1 listopada 2018 o 12:00 północy czasu UTC, format obiektu BLOB został zmieniony na [linie JSON](http://jsonlines.org/). Oznacza to, że każdy rekord zostanie rozdzielony znakiem nowego wiersza, bez tablicy rekordów zewnętrznych i bez przecinków między rekordami JSON.
* Format obiektu BLOB został zmieniony dla wszystkich ustawień diagnostycznych we wszystkich subskrypcjach jednocześnie. Pierwszy PT1H.jsw pliku emitowanym przez 1 listopada był używany w tym nowym formacie. Nazwy obiektów blob i kontenerów pozostają takie same.
* Ustawienie ustawienia diagnostycznego między wcześniejszą i 1 listopada, które będzie w dalszym ciągu emitować dane w bieżącym formacie do 1 listopada.
* Ta zmiana nastąpiła jednocześnie we wszystkich regionach chmury publicznej. Ta zmiana nie będzie jeszcze wykonywana w Microsoft Azure obsługiwane przez firmę 21Vianet, platformę Azure (Niemcy) ani chmurę Azure Government.
* Ta zmiana ma wpływ na następujące typy danych:
  * [Dzienniki zasobów platformy Azure](./resource-logs.md#send-to-azure-storage) ([zobacz Lista zasobów tutaj](./resource-logs-schema.md))
  * [Metryki zasobów platformy Azure eksportowane przez ustawienia diagnostyczne](diagnostic-settings.md)
  * [Dane dziennika aktywności platformy Azure eksportowane przez profile dziennika](./activity-log.md)
* Ta zmiana nie ma wpływu na:
  * Dzienniki przepływu sieci
  * Dzienniki usługi platformy Azure nie zostały jeszcze udostępnione za pomocą Azure Monitor (na przykład dzienniki zasobów Azure App Service, dzienniki analizy magazynu)
  * Routing dzienników zasobów platformy Azure i dzienników aktywności do innych miejsc docelowych (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Jak sprawdzić, czy ma to wpływ

Ta zmiana ma wpływ tylko na to, że:
1. Wysyła dane dziennika do konta usługi Azure Storage przy użyciu ustawień diagnostycznych i
2. Narzędzia, które są zależne od struktury JSON tych dzienników w magazynie.
 
Aby określić, czy masz ustawienia diagnostyczne, które wysyłają dane do konta usługi Azure Storage, możesz przejść do sekcji **monitorowanie** portalu, kliknąć pozycję **Ustawienia diagnostyczne**i zidentyfikować wszystkie zasoby, które mają **stan** " **włączone**":

![Blok ustawień diagnostycznych Azure Monitor](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Jeśli stan diagnostyki jest ustawiony na włączone, masz aktywne ustawienie diagnostyczne dla tego zasobu. Kliknij zasób, aby sprawdzić, czy żadne z ustawień diagnostycznych wysyła dane do konta magazynu:

![Konto magazynu jest włączone](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Jeśli masz zasoby wysyłające dane do konta magazynu przy użyciu tych ustawień diagnostycznych zasobów, ta zmiana będzie miała wpływ na format danych na tym koncie magazynu. Jeśli nie masz niestandardowych narzędzi, które działają poza tymi kontami magazynu, zmiana formatu nie wpłynie.

### <a name="details-of-the-format-change"></a>Szczegóły zmiany formatu

Bieżący format PT1H.jsw pliku w usłudze Azure Blob Storage używa tablicy rekordów JSON. Oto przykład pliku dziennika magazynu kluczy:

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

Nowy format używa [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Oto, co powyższy przykład będzie wyglądać w PT1H.jspliku po zmianie:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ten nowy format umożliwia Azure Monitor wypychania plików dziennika przy użyciu [dołączanych obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), co jest bardziej wydajne w przypadku ciągłego dołączania nowych danych zdarzeń.

## <a name="how-to-update"></a>Jak zaktualizować

Tylko wtedy, gdy istnieje narzędzie niestandardowe, które pozyskuje te pliki dziennika do dalszej obróbki. Jeśli korzystasz z zewnętrznego narzędzia do analizy dzienników lub SIEM, zalecamy [Używanie centrów zdarzeń do pozyskiwania tych danych](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integracja centrów zdarzeń jest łatwiejsza pod względem przetwarzania dzienników z wielu usług i lokalizacji zakładek w konkretnym dzienniku.

Narzędzia niestandardowe należy zaktualizować w taki sposób, aby obsługiwały zarówno bieżący format, jak i format linii JSON opisany powyżej. Zapewni to, że gdy dane zaczynają pojawiać się w nowym formacie, narzędzia nie są przerywane.

## <a name="next-steps"></a>Następne kroki

* Informacje o [archiwizowaniu dzienników zasobów zasobów na koncie magazynu](./resource-logs.md#send-to-azure-storage)
* Informacje [na temat archiwizowania danych dziennika aktywności na koncie magazynu](./activity-log.md#legacy-collection-methods)
