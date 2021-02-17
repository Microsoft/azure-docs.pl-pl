---
title: Historia wersji schematu konfiguracji rozszerzenia Diagnostyka Azure systemu Windows (funkcji wad)
description: Ważne do zbierania liczników wydajności na platformie Azure Virtual Machines, VM Scale Sets, Service Fabric i Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 3b6dc0e19c181ebf4df2778633f981258023e046
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617320"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Wersje i historia schematu konfiguracji rozszerzenia Diagnostyka Azure systemu Windows (funkcji wad)
Ten artykuł zawiera informacje o historii wersji [Diagnostyka Azure rozszerzeń schematu dla systemu Windows (funkcji wad)](diagnostics-extension-overview.md) , które zostały wysłane jako część zestawu Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Zestaw Azure SDK i wersje diagnostyki — wykres  

|Wersja zestawu Azure SDK | Wersja rozszerzenia diagnostyki | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Wtyczka|  
|2,0 – 2,4         |1.0                            |Wtyczka|  
|2.5               |1.2                            |rozszerzenie|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1.7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1.9                            |"|
|2,96              |1,11                           |"|


 Diagnostyka Azure w wersji 1,0 najpierw wysłanej w modelu wtyczki — to znaczy, że podczas instalowania zestawu Azure SDK uzyskano wersję diagnostyki platformy Azure.  

 Począwszy od zestawu SDK 2,5 (wersja diagnostyki 1,2), Diagnostyka platformy Azure została przemieszczona w modelu rozszerzeń. Narzędzia do korzystania z nowych funkcji były dostępne tylko w nowszych zestawach SDK platformy Azure, ale każda usługa korzystająca z usługi Azure Diagnostics może pobrać najnowszą wersję wysyłkową bezpośrednio z platformy Azure. Na przykład każdy nadal korzystający z zestawu SDK 2,5 będzie ładować najnowszą wersję przedstawioną w poprzedniej tabeli, niezależnie od tego, czy korzystają one z nowszych funkcji.  

## <a name="schemas-index"></a>Indeks schematów  
Różne wersje usługi Azure Diagnostics używają różnych schematów konfiguracji. Schemat 1,0 i 1,2 są przestarzałe. Aby uzyskać więcej informacji na temat wersji 1,3 i nowszych, zobacz [diagnostyka 1,3 i nowszy Schemat konfiguracji](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Historia wersji

### <a name="diagnostics-extension-111"></a>Rozszerzenie diagnostyki 1,11
Dodano obsługę ujścia Azure Monitor. Ten obiekt ujścia ma zastosowanie tylko do liczników wydajności. Umożliwia wysyłanie liczników wydajności zebranych w ramach maszyny wirtualnej, VMSS lub usługi w chmurze w celu Azure Monitor jako metryki niestandardowych. Azure Monitor sink obsługuje:
* Pobieranie wszystkich liczników wydajności wysyłanych do Azure Monitor za pośrednictwem [interfejsów API metryk Azure monitor.](/rest/api/monitor/metrics/list)
* Alerty dotyczące wszystkich liczników wydajności wysyłanych do Azure Monitor za pomocą nowych [ujednoliconych alertów](../platform/alerts-overview.md) w programie Azure monitor
* Sposób traktowania operatora wieloznacznego w licznikach wydajności jako wymiar "wystąpienie" w metryce. Na przykład jeśli zebrano licznik "dysk logiczny ( \* )/DiskWrites/SEC", można filtrować i dzielić na wymiarze "wystąpienie", aby wykreślić lub alertować na dyskach zapisy/s dla każdego dysku logicznego (C:, D:, itp.)

Zdefiniuj Azure Monitor jako nowy ujścia w konfiguracji rozszerzenia diagnostyki
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Skonfigurowanie ujścia Azure Monitor dla klasycznych maszyn wirtualnych i klasycznej usługi w chmurze wymaga zdefiniowania więcej parametrów w prywatnej konfiguracji rozszerzenia diagnostyki.
>
> Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją szczegółowych schematów rozszerzenia diagnostyki.](diagnostics-extension-schema-windows.md)

Następnie można skonfigurować liczniki wydajności, które mają być kierowane do ujścia Azure Monitor.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Rozszerzenie diagnostyki 1,9
Dodano obsługę platformy Docker.


### <a name="diagnostics-extension-181"></a>1.8.1 rozszerzenia diagnostyki
W prywatnej konfiguracji można określić token SAS zamiast klucza konta magazynu. W przypadku podanego tokenu SAS klucz konta magazynu jest ignorowany.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Rozszerzenie diagnostyki 1,8
Dodano typ magazynu do PublicConfig. StorageType może być *Table*, *BLOB*, *TableAndBlob*. *Tabela* jest wartością domyślną.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Rozszerzenie diagnostyki 1,7
Dodano możliwość kierowania do centrum EventHub.

### <a name="diagnostics-extension-15"></a>Rozszerzenie diagnostyki 1,5
Dodano element ujścia oraz możliwość wysyłania danych diagnostycznych do [Application Insights](../app/cloudservices.md) , co ułatwia diagnozowanie problemów w aplikacji oraz na poziomie systemu i infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Zestaw Azure SDK 2,6 i rozszerzenie diagnostyki 1,3
W przypadku projektów usług w chmurze w programie Visual Studio wprowadzono następujące zmiany. (Te zmiany mają zastosowanie również do nowszych wersji zestawu Azure SDK).

* Emulator lokalny obsługuje teraz diagnostykę. Ta zmiana oznacza, że można zbierać dane diagnostyczne i upewnić się, że aplikacja tworzy odpowiednie ślady podczas opracowywania i testowania w programie Visual Studio. Parametry połączenia `UseDevelopmentStorage=true` umożliwiają zbieranie danych diagnostycznych podczas korzystania z projektu usługi w chmurze w programie Visual Studio przy użyciu emulatora usługi Azure Storage. Wszystkie dane diagnostyczne są zbierane na koncie magazynu (Tworzenie magazynu).
* Parametry połączenia konta magazynu diagnostyki (Microsoft. WindowsAzure. plugins. Diagnostics. ConnectionString) są zapisywane ponownie w pliku konfiguracji usługi (cscfg). W zestawie Azure SDK 2,5 konto magazynu diagnostyki zostało określone w pliku Diagnostics. wadcfgx.

Istnieją pewne istotne różnice między sposobem, w jaki parametry połączenia działały w zestawie Azure SDK 2,4 i starszych oraz jak działa on w zestawie Azure SDK 2,6 lub nowszym.

* W zestawie Azure SDK 2,4 i starszych parametry połączenia zostały użyte w czasie wykonywania przez wtyczkę diagnostyki w celu uzyskania informacji o koncie magazynu na potrzeby przesyłania dzienników diagnostycznych.
* W zestawie Azure SDK 2,6 i nowszych program Visual Studio używa parametrów połączenia diagnostyki do konfigurowania rozszerzenia diagnostyki z odpowiednimi informacjami o koncie magazynu podczas publikowania. Parametry połączenia umożliwiają definiowanie różnych kont magazynu dla różnych konfiguracji usługi, których program Visual Studio będzie używać podczas publikowania. Jednak ponieważ wtyczka diagnostyki nie jest już dostępna (po zestawie Azure SDK 2,5), sam plik. cscfg nie może włączyć rozszerzenia diagnostyki. Należy włączyć rozszerzenie oddzielnie za pomocą narzędzi, takich jak Visual Studio lub PowerShell.
* Aby uprościć proces konfigurowania rozszerzenia diagnostyki przy użyciu programu PowerShell, dane wyjściowe pakietu programu Visual Studio zawierają również plik XML konfiguracji publicznej dla rozszerzenia diagnostyki dla każdej roli. Program Visual Studio używa parametrów połączenia diagnostyki, aby wypełnić informacje o koncie magazynu obecne w konfiguracji publicznej. Pliki konfiguracji publicznej są tworzone w folderze rozszerzeń i zgodne ze wzorcem `PaaSDiagnostics.<RoleName>.PubConfig.xml` . Wszystkie wdrożenia oparte na programie PowerShell mogą używać tego wzorca do mapowania każdej konfiguracji do roli.
* Parametry połączenia w pliku cscfg są również używane przez Azure Portal do uzyskiwania dostępu do danych diagnostycznych, dzięki czemu mogą być wyświetlane na karcie **monitorowanie** . Parametry połączenia są konieczne, aby skonfigurować usługę do wyświetlania pełnych danych monitorowania w portalu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrowanie projektów do zestawu Azure SDK 2,6 i nowszego
W przypadku migrowania z zestawu Azure SDK 2,5 do zestawu Azure SDK 2,6 lub nowszego, jeśli masz konto magazynu diagnostyki określone w pliku. wadcfgx, zostanie ono tam zachowane. Aby skorzystać z elastyczności korzystania z różnych kont magazynu dla różnych konfiguracji magazynu, trzeba ręcznie dodać parametry połączenia do projektu. W przypadku migrowania projektu z zestawu Azure SDK 2,4 lub starszego do zestawu Azure SDK 2,6 parametry połączenia diagnostyki są zachowywane. Należy jednak pamiętać o zmianach sposobu traktowania parametrów połączenia w zestawie Azure SDK 2,6, jak określono w poprzedniej sekcji.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak program Visual Studio Określa konto magazynu diagnostyki
* Jeśli w pliku cscfg określono parametry połączenia diagnostyki, program Visual Studio używa go do skonfigurowania rozszerzenia diagnostyki podczas publikowania oraz podczas generowania plików XML konfiguracji publicznej podczas tworzenia pakietów.
* Jeśli w pliku cscfg nie określono parametrów połączenia diagnostyki, program Visual Studio powraca do korzystania z konta magazynu określonego w pliku. wadcfgx, aby skonfigurować rozszerzenie diagnostyki podczas publikowania i generować pliki XML konfiguracji publicznej podczas tworzenia pakietów.
* Parametry połączenia diagnostyki w pliku cscfg mają pierwszeństwo przed kontem magazynu w pliku. wadcfgx. Jeśli w pliku cscfg określono parametry połączenia diagnostyki, program Visual Studio używa tego programu i zignoruje konto magazynu w. wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co to jest "Aktualizacja parametrów połączenia magazynu programistycznego"... " pole wyboru?
Pole wyboru **aktualizacji parametrów połączenia magazynu deweloperskiego dla celów diagnostycznych i pamięci podręcznej przy użyciu poświadczeń konta magazynu Microsoft Azure podczas publikowania w usłudze Microsoft Azure** zapewnia wygodny sposób aktualizowania parametrów połączenia konta magazynu deweloperskiego za pomocą konta usługi Azure Storage określonego podczas publikowania.

Załóżmy na przykład, że zaznaczysz to pole wyboru, a parametry połączenia diagnostyki określają `UseDevelopmentStorage=true` . Po opublikowaniu projektu na platformie Azure program Visual Studio automatycznie zaktualizuje parametry połączenia diagnostyki przy użyciu konta magazynu określonego w Kreatorze publikacji. Jeśli jednak prawdziwe konto magazynu zostało określone jako parametry połączenia diagnostyki, to konto jest używane zamiast tego.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Różnice między funkcjami diagnostyki w zestawie Azure SDK 2,4 i starszymi oraz zestawem Azure SDK 2,5 lub nowszym
Jeśli uaktualniasz projekt z zestawu Azure SDK 2,4 do zestawu Azure SDK 2,5 lub nowszego, należy wziąć pod uwagę następujące różnice dotyczące funkcjonalności diagnostyki.

* **Interfejsy API konfiguracji są przestarzałe** — konfiguracja programowa diagnostyki jest dostępna w zestawie azure SDK 2,4 lub wcześniejszych wersjach, ale jest przestarzała w zestawie azure SDK 2,5 lub nowszym. Jeśli konfiguracja diagnostyki jest obecnie zdefiniowana w kodzie, należy ponownie skonfigurować te ustawienia od podstaw w migrowanym projekcie, aby Diagnostyka mogła kontynuować pracę. Plik konfiguracji diagnostyki dla zestawu Azure SDK 2,4 to Diagnostics. wadcfg i Diagnostics. wadcfgx dla zestawu Azure SDK 2,5 lub nowszego.
* **Diagnostykę aplikacji usługi w chmurze można skonfigurować tylko na poziomie roli, a nie na poziomie wystąpienia.**
* Za **każdym razem, gdy wdrażana jest aplikacja, konfiguracja diagnostyki zostaje zaktualizowana** — może to spowodować problemy z parzystością w przypadku zmiany konfiguracji diagnostyki z Eksplorator serwera, a następnie ponownego wdrożenia aplikacji.
* **W zestawie Azure sdk 2,5 i nowszych Zrzuty awaryjne są konfigurowane w pliku konfiguracji diagnostyki, a nie w kodzie** — w przypadku braku zrzutów awaryjnych skonfigurowanych w kodzie trzeba ręcznie przetransferować konfigurację z kodu do pliku konfiguracji, ponieważ Zrzuty awaryjne nie są transferowane podczas migracji do zestawu Azure SDK 2,6.

