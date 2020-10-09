---
title: Wyślij dane z rozszerzenia Diagnostyka systemu Windows Azure do usługi Azure Event Hubs
description: Skonfiguruj rozszerzenie diagnostyki w Azure Monitor, aby wysyłać dane do usługi Azure Event Hub, aby można je było przesłać dalej do lokalizacji poza platformą Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82233469"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Wyślij dane z rozszerzenia Diagnostyka systemu Windows Azure do usługi Azure Event Hubs
Rozszerzenie diagnostyki Azure to Agent w Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. W tym artykule opisano, jak wysyłać dane z rozszerzenia diagnostyki systemu Windows Azure (funkcji wad) do [usługi azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) , aby umożliwić przesyłanie dalej do lokalizacji poza platformą Azure.

## <a name="supported-data"></a>Obsługiwane dane

Dane zbierane z systemu operacyjnego gościa, które mogą być wysyłane do Event Hubs, obejmują następujące elementy: Inne źródła danych zbierane przez funkcji wad, w tym dzienniki usług IIS i Zrzuty awaryjne, nie mogą być wysyłane do Event Hubs.

* Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
* Liczniki wydajności
* Dzienniki zdarzeń systemu Windows, w tym Dzienniki aplikacji w dzienniku zdarzeń systemu Windows
* Dzienniki infrastruktury diagnostyki Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Rozszerzenie diagnostyki systemu Windows w wersji 1,6 lub nowszej. Zapoznaj się z tematem [Konfiguracja rozszerzenia Diagnostyka Azure wersje schematu oraz historia](diagnostics-extension-versions.md) wersji i [Omówienie rozszerzenia Diagnostyka Azure](diagnostics-extension-overview.md) dla obsługiwanych zasobów.
* Przestrzeń nazw Event Hubs musi zawsze być obsługiwana. Aby uzyskać szczegółowe informacje, zobacz Wprowadzenie do [Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .


## <a name="configuration-schema"></a>Schemat konfiguracji
Zapoznaj się z tematem [Instalowanie i Konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (funkcji wad)](diagnostics-extension-windows-install.md) dla różnych opcji włączania i konfigurowania rozszerzenia diagnostyki oraz [Diagnostyka Azure schematu konfiguracji](diagnostics-extension-schema-windows.md) , aby uzyskać odwołanie do schematu konfiguracji. W pozostałej części tego artykułu opisano sposób korzystania z tej konfiguracji w celu wysyłania danych do centrum zdarzeń. 

Diagnostyka Azure zawsze wysyła dzienniki i metryki do konta usługi Azure Storage. Można skonfigurować co najmniej jeden *ujścia danych* , który wysyła dane do dodatkowych lokalizacji. Każdy ujścia jest zdefiniowany w [elemencie SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) konfiguracji publicznej z informacjami poufnymi w konfiguracji prywatnej. Ta konfiguracja centrów zdarzeń używa wartości z poniższej tabeli.

| Właściwość | Opis |
|:---|:---|
| Nazwa | Nazwa opisowa ujścia. Używane w konfiguracji, aby określić źródła danych do wysłania do ujścia. |
| Url  | Adres URL centrum zdarzeń w postaci \<event-hubs-namespace\> . ServiceBus.Windows.NET/ \<event-hub-name\> .          |
| SharedAccessKeyName | Nazwa zasad dostępu współdzielonego centrum zdarzeń, które ma co najmniej urząd **wysyłania** . |
| SharedAccessKey     | Klucz podstawowy lub pomocniczy z zasad dostępu współdzielonego dla centrum zdarzeń. |

Poniżej przedstawiono przykładowe konfiguracje publiczne i prywatne. Jest to minimalna konfiguracja z pojedynczym licznikiem wydajności i dziennikiem zdarzeń, aby zilustrować sposób konfigurowania i używania ujścia danych centrum zdarzeń. Zobacz [Schemat konfiguracji Diagnostyka Azure](diagnostics-extension-schema-windows.md) , aby uzyskać bardziej skomplikowany przykład.

### <a name="public-configuration"></a>Konfiguracja publiczna

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Konfiguracja prywatna

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opcje konfiguracji
Aby wysłać dane do ujścia danych, należy określić atrybut **ujścia** w węźle źródła danych. Gdy umieszczasz atrybut **ujścias** określa zakres przypisania. W poniższym przykładzie atrybut **ujścia** jest zdefiniowany jako węzeł **liczniki wydajności** , co spowoduje wysłanie wszystkich podrzędnych liczników wydajności do centrum zdarzeń.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


W poniższym przykładzie atrybut **ujścia** jest stosowany bezpośrednio do trzech liczników, co spowoduje wysłanie tylko tych liczników wydajności do centrum zdarzeń. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Weryfikowanie konfiguracji
Możesz użyć różnych metod, aby sprawdzić, czy dane są wysyłane do centrum zdarzeń. ne prosta metoda polega na użyciu funkcji przechwytywania Event Hubs, zgodnie z opisem w temacie [przechwytywanie zdarzeń za pomocą usługi azure Event Hubs na platformie azure BLOB Storage lub Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Rozwiązywanie problemów z Event Hubs ujściami

- Zapoznaj się z tabelą usługi Azure Storage **WADDiagnosticInfrastructureLogsTable** , która zawiera dzienniki i błędy Diagnostyka Azure samego siebie. Jedną z opcji jest użycie narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://www.storageexplorer.com) , aby nawiązać połączenie z tym kontem magazynu, wyświetlić tę tabelę i dodać zapytanie dla sygnatury czasowej w ciągu ostatnich 24 godzin. Możesz użyć narzędzia, aby wyeksportować plik CSV i otworzyć go w aplikacji, takiej jak program Microsoft Excel. Program Excel ułatwia wyszukiwanie ciągów kart wywołujących, takich jak **EventHubs**, aby zobaczyć, jaki błąd jest zgłaszany.  

- Sprawdź, czy centrum zdarzeń zostało pomyślnie zainicjowane. Wszystkie informacje o połączeniu w sekcji **PrivateConfig** konfiguracji muszą być zgodne z wartościami Twojego zasobu, które są widoczne w portalu. Upewnij się, że masz zdefiniowane zasady sygnatury dostępu współdzielonego (*SendRule* w tym przykładzie) w portalu oraz że udzielono uprawnienia do *wysyłania* .  

## <a name="next-steps"></a>Następne kroki

* [Przegląd usługi Event Hubs](../../event-hubs/event-hubs-about.md)
* [Tworzenie centrum zdarzeń](../../event-hubs/event-hubs-create.md)
* [Event Hubs — często zadawane pytania](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



