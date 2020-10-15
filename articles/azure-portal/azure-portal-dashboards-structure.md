---
title: Struktura pulpitów nawigacyjnych platformy Azure | Microsoft Docs
description: Zapoznaj się ze strukturą JSON pulpitu nawigacyjnego platformy Azure, korzystając z przykładowego pulpitu nawigacyjnego. Zawiera odwołanie do właściwości zasobów.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: 014463fb0a5af639ff0da5f8db2805f9796fd928
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072479"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura pulpitów nawigacyjnych platformy Azure
Ten dokument przedstawia strukturę pulpitu nawigacyjnego platformy Azure, korzystając z następującego pulpitu nawigacyjnego:

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Ponieważ udostępnione [pulpity nawigacyjne platformy Azure to zasoby](../azure-resource-manager/management/overview.md), ten pulpit nawigacyjny może być reprezentowany w formacie JSON.  Poniższy kod JSON przedstawia wizualizację pulpitu nawigacyjnego powyżej.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Wspólne właściwości zasobów

Podziel się odpowiednimi sekcjami kodu JSON.  Właściwości najwyższego poziomu, __identyfikatora__, __nazwy__, __typu__, __lokalizacji__i __tagów__ są udostępniane dla wszystkich typów zasobów platformy Azure. Oznacza to, że nie mają one wielu czynności z zawartością pulpitu nawigacyjnego.

### <a name="the-id-property"></a>Właściwość identyfikatora

Identyfikator zasobu platformy Azure, objęty [konwencjami nazewnictwa zasobów platformy Azure](/azure/architecture/best-practices/resource-naming). Gdy Portal tworzy pulpit nawigacyjny, zwykle wybiera identyfikator w postaci identyfikatora GUID, ale możesz użyć dowolnej prawidłowej nazwy podczas programowego tworzenia. 

### <a name="the-name-property"></a>Właściwość Name
Nazwa jest segmentem identyfikatora zasobu, który nie zawiera informacji o subskrypcji, typie zasobu lub grupie zasobów. Zasadniczo jest to ostatni segment identyfikatora zasobu.

### <a name="the-type-property"></a>Właściwość typu
Wszystkie pulpity nawigacyjne są typu __Microsoft. Portal/pulpity nawigacyjne__.

### <a name="the-location-property"></a>Właściwość Location
W przeciwieństwie do innych zasobów, pulpity nawigacyjne nie mają składnika środowiska uruchomieniowego.  W przypadku pulpitów nawigacyjnych lokalizacja wskazuje podstawową lokalizację geograficzną, w której jest przechowywana reprezentacja JSON pulpitu nawigacyjnego. Wartość powinna być jednym z kodów lokalizacji, które można pobrać za pomocą [interfejsu API lokalizacji zasobu subskrypcje](/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Właściwość Tags
Tagi to typowa funkcja zasobów platformy Azure, która umożliwia organizowanie zasobów według arbitralnych par wartości nazw. W przypadku pulpitów nawigacyjnych istnieje jeden specjalny tag o nazwie __Hidden-title__. Jeśli pulpit nawigacyjny ma zapełnienie tej właściwości, zostanie użyta jako nazwa wyświetlana pulpitu nawigacyjnego w portalu. Nie można zmienić nazwy identyfikatorów zasobów platformy Azure, ale mogą to być Tagi. Ten tag pozwala na renamable wyświetlana nazwa pulpitu nawigacyjnego.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Obiekt właściwości
Obiekt Properties zawiera dwie właściwości, __soczewki__ i __metadane__. Właściwość __soczewki__ zawiera informacje o kafelkach na pulpicie nawigacyjnym.  Właściwość __Metadata__ jest dostępna w przypadku potencjalnych przyszłych funkcji.

### <a name="the-lenses-property"></a>Właściwość soczewki
Właściwość __soczewki__ zawiera pulpit nawigacyjny. Należy zauważyć, że obiekt soczewki w tym przykładzie zawiera pojedynczą właściwość o nazwie "0". Soczewki to koncepcja grupowania, która nie jest obecnie zaimplementowana w pulpitach nawigacyjnych. Na razie wszystkie pulpity nawigacyjne mają tę samą właściwość w obiekcie obiektywu, ponownie o nazwie "0".

### <a name="the-lens-object"></a>Obiekt obiektywu
Obiekt poniżej "0" zawiera dwie właściwości, __kolejność__ i __części__.  W bieżącej wersji pulpitów nawigacyjnych __kolejność__ jest zawsze równa 0. Właściwość __części__ zawiera obiekt definiujący poszczególne części (nazywane również kafelkami) na pulpicie nawigacyjnym.

Obiekt __części__ zawiera właściwość dla każdej części, gdzie nazwa właściwości jest liczbą. Ta liczba nie jest istotna. 

### <a name="the-part-object"></a>Obiekt części
Każdy obiekt poszczególnych części ma __położenie__i __metadane__.

### <a name="the-position-object"></a>Obiekt Position
Właściwość __Position__ zawiera informacje o rozmiarze i lokalizacji części wyrażone jako __x__, __y__, __rowSpan__i __colSpan__. Wartości są w zakresie jednostek siatki. Te jednostki siatki są widoczne, gdy pulpit nawigacyjny znajduje się w trybie dostosowywania, jak pokazano poniżej. Jeśli chcesz, aby kafelek miał szerokość dwóch jednostek siatki, Wysokość jednej jednostki siatki i lokalizację w lewym górnym rogu pulpitu nawigacyjnego, obiekt Position będzie wyglądać następująco:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Zrzut ekranu pokazuje zamknięcie siatki z wyróżnioną jedną jednostką siatki kwadratową.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Obiekt metadanych
Każda część ma właściwość metadanych, obiekt ma tylko jedną wymaganą właściwość o nazwie __Type__. Ten ciąg zawiera informacje o portalu, który ma być pokazywany przez kafelek. Nasz przykładowy pulpit nawigacyjny używa następujących typów kafelków:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` — Służy do wyświetlania metryk monitorowania
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` — Służy do wyświetlania tekstu lub obrazów z formatowaniem podstawowym dla list, linków itp.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` — Służy do wyświetlania wideo z witryny YouTube, channel9 i dowolnego innego typu wideo, który działa w tagu wideo HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` — Służy do wyświetlania nazwy i stanu maszyny wirtualnej platformy Azure.

Każdy typ części ma własną konfigurację. Możliwe właściwości konfiguracji są nazywane __danymi wejściowymi__, __ustawieniami__i __zasobami__. 

### <a name="the-inputs-object"></a>Obiekt Inputs
Obiekt Inputs zwykle zawiera informacje, które wiążą kafelek z wystąpieniem zasobu.  Część maszyny wirtualnej na naszym przykładowym pulpicie nawigacyjnym zawiera pojedyncze dane wejściowe, które używają identyfikatora zasobu platformy Azure do wyrażenia powiązania.  Ten format identyfikatora zasobu jest spójny dla wszystkich zasobów platformy Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Część wykresu metryk ma pojedyncze dane wejściowe, które wyrażają zasób do powiązania, a także informacje o wyświetlanych metrykach. Poniżej znajduje się dane wejściowe dla kafelka, w którym są wyświetlane metryki sieciowe i sieciowe.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>Obiekt Settings
Obiekt Settings zawiera elementy konfigurowalne części.  Na naszym przykładowym pulpicie nawigacyjnym część promocji używa ustawień do przechowywania niestandardowej zawartości promocji oraz konfigurowalnego tytułu i nazwy pomocniczej.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Podobnie kafelek wideo ma własne ustawienia, które zawierają wskaźnik do wideo do odtwarzania, ustawienia autoodtwarzania i opcjonalne informacje o tytule.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Obiekt zasobu
Kafelki, które są powiązane z obiektami portalu z możliwością zarządzania (nazywanymi elementami zawartości), mają tę relację wyrażoną za pośrednictwem obiektu zasobu.  Na naszym przykładowym pulpicie nawigacyjnym kafelek maszyna wirtualna zawiera opis tego elementu zawartości.  Właściwość __idInputName__ instruuje Portal, że dane wejściowe identyfikatora zawierają unikatowy identyfikator dla elementu zawartości, w tym przypadku identyfikator zasobu. Większość typów zasobów platformy Azure ma zasoby zdefiniowane w portalu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`