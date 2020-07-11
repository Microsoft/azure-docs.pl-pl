---
title: Konta magazynu należące do klienta na potrzeby pozyskiwania dziennika
description: Użyj własnego konta magazynu na potrzeby pozyskiwania danych dziennika w obszarze roboczym Log Analytics w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 05eb92e2fb887b5c64e2c73576fe85a4543ac1b7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184501"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Konta magazynu należące do klienta na potrzeby pozyskiwania dzienników w Azure Monitor

Azure Monitor używa kont magazynu w procesie pozyskiwania niektórych typów danych, takich jak [dzienniki niestandardowe](data-sources-custom-logs.md) i niektóre [dzienniki platformy Azure](azure-storage-iis-table.md). W procesie pozyskiwania dzienniki są najpierw wysyłane do konta magazynu i później wprowadzane do Log Analytics lub Application Insights. Jeśli chcesz kontrolować dane podczas pozyskiwania, możesz użyć własnych kont magazynu zamiast magazynu zarządzanego przez usługę. Korzystanie z własnego konta magazynu zapewnia kontrolę nad dostępem, zawartością, szyfrowaniem i przechowywaniem dzienników podczas pozyskiwania. Nazywamy to własnymi magazynami lub BYOS. 

Jeden scenariusz wymagający BYOS jest izolacją sieci za poorednictwem prywatnych linków. W przypadku używania sieci wirtualnej izolacja sieci jest często wymagane i dostęp do publicznej sieci Internet jest ograniczony. W takich przypadkach dostęp do usługi Azure Monitor Service Storage na potrzeby pozyskiwania dzienników jest całkowicie zablokowany lub traktowany jako niewłaściwy. Zamiast tego dzienniki powinny być pozyskane za pośrednictwem konta magazynu należącego do klienta w sieci wirtualnej lub łatwo dostępne.

Innym scenariuszem jest szyfrowanie dzienników za pomocą kluczy zarządzanych przez klienta (CMK). Klienci mogą szyfrować zarejestrowane dane przy użyciu CMK w klastrach przechowujących dzienniki. Ten sam klucz może być również używany do szyfrowania dzienników podczas procesu pozyskiwania.

## <a name="data-types-supported"></a>Obsługiwane typy danych

Typy danych, które są pozyskiwane z konta magazynu, obejmują następujące elementy: Aby uzyskać więcej informacji na temat pozyskiwania tych typów, zobacz [zbieranie danych z rozszerzenia diagnostyki platformy Azure do Azure monitor dzienników](azure-storage-iis-table.md) .

| Typ | Informacje o tabeli |
|:-----|:------------------|
| Dzienniki usług IIS | BLOB: funkcji wad-IIS-LogFiles|
|dzienniki zdarzeń systemu Windows | Tabela: WADWindowsEventLogsTable |
| Dziennik systemu | Tabela: LinuxsyslogVer2v0 |
| Dzienniki funkcji ETW systemu Windows | Tabela: WADETWEventTable|
| Service Fabric | Tabela: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Niestandardowe dzienniki | nie dotyczy |
| Pliki zrzutu programu Azure Security Center Watson | nie dotyczy|  

## <a name="storage-account-requirements"></a>Wymagania konta magazynu 
Konto magazynu musi spełniać następujące wymagania:

- Dostępne dla zasobów w sieci wirtualnej, które zapisują dzienniki w magazynie.
- Musi znajdować się w tym samym regionie, w którym znajduje się obszar roboczy, z którym jest połączony.
- Zezwalaj na dostęp Azure Monitor — w przypadku wybrania opcji ograniczania dostępu do konta magazynu do wybranych sieci upewnij się, że jest dozwolony ten wyjątek: *Zezwól zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu*.

## <a name="process-to-configure-customer-owned-storage"></a>Proces konfigurowania magazynu należącego do klienta
Podstawowy proces korzystania z Twojego konta magazynu na potrzeby pozyskiwania jest następujący:

1. Utwórz konto magazynu lub Wybierz istniejące konto.
2. Połącz konto magazynu z obszarem roboczym Log Analytics.
3. Zarządzaj magazynem, przeglądając jego obciążenie i przechowywanie, aby upewnić się, że działa zgodnie z oczekiwaniami.

Jedyną dostępną metodą tworzenia i usuwania łączy jest użycie interfejsu API REST. Szczegółowe informacje dotyczące konkretnego żądania interfejsu API wymagane dla każdego procesu znajdują się w poniższych sekcjach.

## <a name="command-line-and-rest-api"></a>Wiersz polecenia i interfejs API REST

### <a name="command-line"></a>Wiersz polecenia
Aby utworzyć połączone konta magazynu i zarządzać nimi, użyj [AZ Monitor Log-Analytics obszaru roboczego połączonego magazynu](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage). To polecenie umożliwia łączenie i odłączanie kont magazynu z obszaru roboczego i wyświetlanie listy połączonych kont magazynu.

### <a name="request-and-cli-values"></a>Wartości żądań i interfejsu wiersza polecenia

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson — Użyj tej wartości dla plików zrzutów Azure Security Center programu Azure Watson.
- CustomLogs — Użyj tej wartości dla następujących typów danych:
  - Niestandardowe dzienniki
  - Dzienniki usług IIS
  - Zdarzenia (Windows)
  - Dziennik systemu (Linux)
  - Dzienniki ETW
  - Zdarzenia Service Fabric
  - Dane oceny  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Ta wartość używa następującej struktury:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Pobierz połączone konta magazynu dla wszystkich typów źródeł danych

#### <a name="api-request"></a>Żądanie interfejsu API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Odpowiedź

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Pobierz połączone konta magazynu dla określonego typu źródła danych

#### <a name="api-request"></a>Żądanie interfejsu API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Odpowiedź 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Tworzenie lub modyfikowanie linku

Po połączeniu konta magazynu z obszarem roboczym Log Analytics rozpocznie korzystanie z niego zamiast konta magazynu należącego do usługi. Można rejestrować listę kont magazynu jednocześnie i korzystać z tego samego konta magazynu dla wielu obszarów roboczych.

Jeśli obszar roboczy obsługuje zasoby sieci wirtualnej i zasoby spoza sieci wirtualnej, upewnij się, że nie odrzucisz ruchu pochodzącego z Internetu. Magazyn powinien mieć te same ustawienia co obszar roboczy i być udostępniany dla zasobów poza siecią wirtualną. 

### <a name="api-request"></a>Żądanie interfejsu API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Ładunku

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Odpowiedź

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Odłączanie konta magazynu
Jeśli zdecydujesz się zatrzymać korzystanie z konta magazynu na potrzeby pozyskiwania lub zamienić używany obszar roboczy, odłącz magazyn od obszaru roboczego.

Odłączanie wszystkich kont magazynu z obszaru roboczego oznacza, że pozyskiwanie będzie próbować polegać na kontach magazynu zarządzanych przez usługę. Jeśli agenci działają w sieci wirtualnej z ograniczonym dostępem do Internetu, przechodzenie nie powiedzie się. Obszar roboczy musi mieć połączone konto magazynu, które jest dostępne z monitorowanych zasobów.

Przed usunięciem konta magazynu należy upewnić się, że wszystkie zawarte w nim dane zostały odebrane do obszaru roboczego. Jako środek ostrożności należy zachować dostęp do konta magazynu po połączeniu z alternatywnym magazynem. Należy je usunąć tylko wtedy, gdy cała jego zawartość została pozyskana i będzie można zobaczyć, że nowe dane są zapisywane na nowo podłączonym koncie magazynu.


### <a name="api-request"></a>Żądanie interfejsu API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Zastępowanie konta magazynu

Aby zastąpić konto magazynu używane na potrzeby pozyskiwania, najpierw utwórz link do nowego konta magazynu. Agenci rejestrowania otrzymają zaktualizowaną konfigurację i rozpoczną wysyłanie danych do nowego magazynu.

Dalej Odłącz stare konto magazynu, aby agenci zatrzymali zapisywanie na usuniętym koncie. Proces pozyskiwania przechowuje dane z tego konta do momentu jego pozyskania. Nie usuwaj konta magazynu, dopóki nie zostaną wyświetlone wszystkie dzienniki.

Konfiguracja agenta zostanie odświeżona po kilku minutach i przejdzie do nowego magazynu.

## <a name="manage-storage-account"></a>Zarządzanie kontem magazynu

### <a name="load"></a>Ładowanie

Konta magazynu mogą obsługiwać pewne obciążenie żądań odczytu i zapisu przed rozpoczęciem ograniczania żądań. Ograniczanie przepływności wpływa na czas pobierania dzienników i może spowodować utratę danych. Jeśli magazyn jest przeciążony, należy zarejestrować dodatkowe konta magazynu i rozłożyć obciążenie między nimi. 

### <a name="related-charges"></a>Powiązane opłaty

Konta magazynu są rozliczone według ilości przechowywanych danych, typów magazynu i typu nadmiarowości. Aby uzyskać szczegółowe informacje, zobacz [Cennik usługi Block BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) i [Cennik Table Storage](https://azure.microsoft.com/pricing/details/storage/tables/).

Jeśli zarejestrowane konto magazynu obszaru roboczego znajduje się w innym regionie, zostanie naliczona opłata za ruch wychodzący zgodnie z powyższymi [szczegółami dotyczącymi cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat konfigurowania linku prywatnego, zobacz [bezpieczne łączenie sieci do Azure monitor przy użyciu prywatnego linku platformy Azure](private-link-security.md)
