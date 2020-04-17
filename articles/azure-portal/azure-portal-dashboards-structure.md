---
title: Struktura pulpitów nawigacyjnych platformy Azure | Dokumenty firmy Microsoft
description: Przejdź przez strukturę JSON pulpitu nawigacyjnego platformy Azure przy użyciu przykładowego pulpitu nawigacyjnego. Zawiera odwołanie do właściwości zasobu.
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
ms.openlocfilehash: ad0d3a1bf2c293039df3bba3aa18da7d6e7dd0a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459223"
---
# <a name="the-structure-of-azure-dashboards"></a>Struktura pulpitów nawigacyjnych platformy Azure
Ten dokument przechodzi przez strukturę pulpitu nawigacyjnego platformy Azure, przy użyciu następującego pulpitu nawigacyjnego jako przykład:

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Ponieważ udostępnione [pulpity nawigacyjne platformy Azure są zasobami,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)ten pulpit nawigacyjny może być reprezentowany jako JSON.  Następujący JSON reprezentuje pulpit nawigacyjny zwizualizowany powyżej.

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

## <a name="common-resource-properties"></a>Typowe właściwości zasobu

Podzielmy odpowiednie sekcje JSON.  Właściwości najwyższego poziomu, __identyfikator,__ __nazwa__, __typ__, __lokalizacja__i __tagi__ właściwości są współużytkowane przez wszystkie typy zasobów platformy Azure. Oznacza to, że nie mają wiele wspólnego z zawartością pulpitu nawigacyjnego.

### <a name="the-id-property"></a>Właściwość identyfikatora

Identyfikator zasobu platformy Azure, z zastrzeżeniem [konwencji nazewnictwa zasobów platformy Azure](/azure/architecture/best-practices/resource-naming). Gdy portal tworzy pulpit nawigacyjny, zazwyczaj wybiera identyfikator w postaci identyfikatora guid, ale możesz używać dowolnej prawidłowej nazwy podczas tworzenia ich programowo. 

### <a name="the-name-property"></a>Właściwość name
Nazwa jest segmentem identyfikatora zasobu, który nie zawiera informacji o subskrypcji, typie zasobu ani grupie zasobów. Zasadniczo jest to ostatni segment identyfikatora zasobu.

### <a name="the-type-property"></a>Właściwość type
Wszystkie pulpity nawigacyjne są typu __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>Właściwość lokalizacjowa
W przeciwieństwie do innych zasobów pulpity nawigacyjne nie mają składnika środowiska wykonawczego.  W przypadku pulpitów nawigacyjnych lokalizacja wskazuje podstawową lokalizację geograficzną, w którym jest reprezentacja JSON pulpitu nawigacyjnego. Wartość powinna być jednym z kodów lokalizacji, które mogą być pobierane przy użyciu [interfejsu API lokalizacji w zasobie subskrypcji](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Właściwość tagów
Tagi są wspólną cechą zasobów platformy Azure, które umożliwiają organizowanie zasobu przez pary dowolnej nazwy wartości. W przypadku pulpitów nawigacyjnych istnieje jeden specjalny tag o nazwie __hidden-title__. Jeśli pulpit nawigacyjny ma tę właściwość wypełnioną, jest on używany jako nazwa wyświetlana pulpitu nawigacyjnego w portalu. Nie można zmienić nazwy identyfikatorów zasobów platformy Azure, ale tagi można zmienić. Ten tag umożliwia zmianę nazwy wyświetlaną pulpitu nawigacyjnego.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Obiekt właściwości
Obiekt właściwości zawiera dwie właściwości, __soczewki__ i __metadane__. Właściwość __obiektywów__ zawiera informacje o kafelkach na desce rozdzielczej.  Właściwość __metadanych__ jest tam dla potencjalnych przyszłych funkcji.

### <a name="the-lenses-property"></a>Właściwości soczewek
Właściwość __obiektywów__ zawiera pulpit nawigacyjny. Należy zauważyć, że obiektyw obiektu w tym przykładzie zawiera jedną właściwość o nazwie "0". Obiektywy to koncepcja grupowania, która nie jest obecnie zaimplementowana na pulpitach nawigacyjnych. Na razie wszystkie pulpity nawigacyjne mają tę pojedynczą właściwość na obiekcie obiektywu, ponownie o nazwie "0".

### <a name="the-lens-object"></a>Obiekt obiektywu
Obiekt pod "0" zawiera dwie właściwości, __kolejność__ i __części__.  W bieżącej wersji pulpitów nawigacyjnych __kolejność__ jest zawsze 0. Właściwość __parts__ zawiera obiekt definiujący poszczególne części (nazywane również kafelkami) na pulpicie nawigacyjnym.

Obiekt __parts__ zawiera właściwość dla każdej części, gdzie nazwa właściwości jest liczbą. Liczba ta nie jest znacząca. 

### <a name="the-part-object"></a>Obiekt części
Każdy obiekt poszczególnych części ma __pozycję__i __metadane__.

### <a name="the-position-object"></a>Obiekt położenia
Właściwość __position__ zawiera informacje o rozmiarze i lokalizacji dla części wyrażone jako __x__, __y__, __rowSpan__i __colSpan__. Wartości są w odniesieniu do jednostek siatki. Te jednostki siatki są widoczne, gdy pulpit nawigacyjny jest w trybie dostosowywania, jak pokazano poniżej. Jeśli kafelek ma mieć szerokość dwóch jednostek siatki, wysokość jednej jednostki siatki i lokalizację w lewym górnym rogu pulpitu nawigacyjnego, obiekt położenia wygląda następująco:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![jednostki sieciowe](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Obiekt metadanych
Każda część ma właściwość metadanych, obiekt ma tylko jedną wymaganą właściwość o nazwie __type__. Ten ciąg informuje portal, który kafelek ma być pokazywalny. Nasz przykładowy pulpit nawigacyjny używa następujących typów kafelków:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart`– Służy do pokazywalkowych wskaźników monitorowania
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`- Służy do pokazywanie z tekstem lub obrazami z podstawowym formatowaniem list, linków itp.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`– Służy do pokazywalki filmów z YouTube, Channel9 i innego typu wideo, który działa w tagu wideo HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`– Służy do pokazywanie nazwy i stanu maszyny wirtualnej platformy Azure.

Każdy typ części ma swoją własną konfigurację. Możliwe właściwości konfiguracji są nazywane __wejściami,__ __ustawieniami__i __zasobem.__ 

### <a name="the-inputs-object"></a>Obiekt inputs
Obiekt inputs zazwyczaj zawiera informacje, które wiąże kafelek z wystąpieniem zasobu.  Część maszyny wirtualnej w naszym przykładowym pulpicie nawigacyjnym zawiera pojedyncze dane wejściowe, które używa identyfikatora zasobu platformy Azure do wyrażenia powiązania.  Ten format identyfikatora zasobu jest spójny we wszystkich zasobach platformy Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Część wykresu metryk ma pojedyncze dane wejściowe, które wyraża zasób do powiązania, a także informacje o wyświetlanych metrykach. Oto dane wejściowe dla kafelka, który pokazuje metryki Wejście w sieci i Wyjście sieciowe.

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

### <a name="the-settings-object"></a>Obiekt ustawień
Obiekt ustawień zawiera konfigurowalne elementy części.  W naszym przykładowym pulpicie nawigacyjnym część Markdown używa ustawień do przechowywania niestandardowej zawartości znaczników, a także konfigurowalnego tytułu i podtytułu.

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

Podobnie kafelek wideo ma własne ustawienia, które zawierają wskaźnik do odtwarzania wideo, ustawienie autoodtwarzania i opcjonalne informacje o tytule.

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

### <a name="the-asset-object"></a>Obiekt środka trwałego
Kafelki, które są powiązane z pierwszej klasy zarządzanych obiektów portalu (nazywane zasobami) mają tę relację wyrażoną za pośrednictwem obiektu zasobu.  W naszym przykładowym pulpicie nawigacyjnym kafelka maszyny wirtualnej zawiera ten opis zasobu.  Właściwość __idInputName__ informuje portal, że dane wejściowe identyfikatora zawierają unikatowy identyfikator zasobu, w tym przypadku identyfikator zasobu. Większość typów zasobów platformy Azure mają zasoby zdefiniowane w portalu.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
