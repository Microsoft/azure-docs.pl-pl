---
title: Programowe tworzenie pulpitów nawigacyjnych platformy Azure
description: Użyj pulpitu nawigacyjnego w Azure Portal jako szablonu do programistycznego tworzenia pulpitów nawigacyjnych platformy Azure. Zawiera odwołanie JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: c7fcced1833cb5f4ccb9c2c2fd44dd84a9abb3cb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077868"
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych platformy Azure

Ten artykuł przeprowadzi Cię przez proces programistycznego tworzenia i publikowania pulpitów nawigacyjnych platformy Azure. Na pulpicie nawigacyjnym przedstawionym poniżej znajduje się odwołanie do dokumentu.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Omówienie

Udostępnione pulpity nawigacyjne w [Azure Portal](https://portal.azure.com) są [zasobami](../azure-resource-manager/management/overview.md) , takimi jak maszyny wirtualne i konta magazynu. Można zarządzać zasobami programowo przy użyciu [Azure Resource Manager interfejsów API REST](/rest/api/), [interfejsu wiersza polecenia platformy Azure](/cli/azure)i [poleceń Azure PowerShell](/powershell/azure/get-started-azureps).

Wiele funkcji kompiluje się na tych interfejsów API, aby ułatwić zarządzanie zasobami. Każdy z tych interfejsów API i narzędzi oferuje sposoby tworzenia, wyświetlania, pobierania, modyfikowania i usuwania zasobów. Ponieważ pulpity nawigacyjne są zasobami, możesz wybrać ulubiony interfejs API lub narzędzie do użycia.

Niezależnie od tego, jakie narzędzia są używane, aby programistycznie utworzyć pulpit nawigacyjny, należy skonstruować reprezentację w formacie JSON obiektu pulpitu nawigacyjnego. Ten obiekt zawiera informacje o kafelkach na pulpicie nawigacyjnym. Obejmuje to rozmiary, pozycje, zasoby, z którymi są one powiązane, oraz dowolnych dostosowań użytkowników.

Najbardziej praktycznym sposobem tworzenia tego dokumentu JSON jest użycie Azure Portal. Możesz interaktywnie dodawać i pozycjonować kafelki. Następnie wyeksportuj kod JSON i Utwórz szablon na podstawie wyniku do późniejszego użycia w skryptach, programach i narzędziach wdrażania.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Aby utworzyć pulpit nawigacyjny, wybierz pozycję **pulpit nawigacyjny** z menu [Azure Portal](https://portal.azure.com) , a następnie wybierz pozycję **nowy pulpit nawigacyjny**.

![nowe polecenie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Użyj galerii kafelków, aby znaleźć i dodać kafelki. Kafelki są dodawane przez przeciąganie i upuszczanie. Niektóre kafelki obsługują zmianę rozmiarów przy użyciu uchwytu przeciągania.

![Przeciągnij uchwyt, aby zmienić rozmiar](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Inne osoby mają stałe rozmiary do wyboru w ich menu kontekstowym.

![rozmiar menu kontekstowego do zmiany rozmiaru](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnianie pulpitu nawigacyjnego

Po skonfigurowaniu pulpitu nawigacyjnego następnym krokiem jest opublikowanie pulpitu nawigacyjnego przy użyciu polecenia **Udostępnij** .

![Udostępnianie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Po wybraniu pozycji **Udostępnij** zostanie wyświetlony komunikat z prośbą o wybranie subskrypcji i grupy zasobów, w których ma zostać opublikowany. Musisz mieć dostęp do zapisu do wybranej subskrypcji i grupy zasobów. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](../role-based-access-control/role-assignments-portal.md).

![wprowadzanie zmian w celu udostępniania i dostępu](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobierz reprezentację w formacie JSON pulpitu nawigacyjnego

Publikowanie trwa tylko kilka sekund. Gdy wszystko będzie gotowe, następnym krokiem jest pobranie pliku JSON przy użyciu polecenia **Pobierz** .

![Pobierz reprezentację JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Tworzenie szablonu na podstawie kodu JSON

Następnym krokiem jest utworzenie szablonu na podstawie tego kodu JSON. Użyj tego szablonu programowo przy użyciu odpowiednich interfejsów API zarządzania zasobami, narzędzi wiersza polecenia lub w portalu.

Nie musisz w pełni zrozumieć struktury JSON pulpitu nawigacyjnego, aby utworzyć szablon. W większości przypadków chcesz zachować strukturę i konfigurację każdego kafelka. Następnie Sparametryzuj zbiór zasobów platformy Azure, do których odnoszą się kafelki. Zapoznaj się z wyeksportowanym pulpitem nawigacyjnym JSON i Znajdź wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Nasz przykładowy pulpit nawigacyjny zawiera wiele kafelków, które są wskazywane przez pojedynczą maszynę wirtualną platformy Azure. Dzieje się tak, ponieważ nasz pulpit nawigacyjny przegląda tylko ten pojedynczy zasób. Jeśli przeszukasz przykładowy kod JSON, który znajduje się na końcu dokumentu dla "/subscriptions", znajdziesz kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla dowolnej maszyny wirtualnej w przyszłości, Sparametryzuj każde wystąpienie tego ciągu w formacie JSON.

Istnieją dwa podejścia do interfejsów API, które tworzą zasoby na platformie Azure:

* Bezwzględne interfejsy API tworzą jeden zasób w danym momencie. Aby uzyskać więcej informacji, zobacz [zasoby](/rest/api/resources/resources).
* System wdrażania oparty na szablonach, który tworzy wiele zależnych zasobów z pojedynczym wywołaniem interfejsu API. Aby uzyskać więcej informacji, zobacz  [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Wdrożenie oparte na szablonach obsługuje parametryzacja i tworzenia szablonów. Używamy tej metody w tym artykule.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowe tworzenie pulpitu nawigacyjnego na podstawie szablonu przy użyciu wdrożenia szablonu

Platforma Azure oferuje możliwość organizowania wdrożenia wielu zasobów. Tworzysz szablon wdrożenia, który wyraża zestaw zasobów do wdrożenia i relacje między nimi.  Format JSON każdego zasobu jest taki sam, jak w przypadku ich tworzenia przez jeden. Różnica polega na tym, że język szablonu dodaje kilka koncepcji, takich jak zmienne, parametry, podstawowe funkcje i wiele innych. Ta rozszerzona składnia jest obsługiwana tylko w kontekście wdrożenia szablonu. Nie działa, jeśli jest używany z bezwzględnymi interfejsami API opisanymi wcześniej. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Parametryzacja należy wykonać przy użyciu składni parametru szablonu.  Zastąp wszystkie wystąpienia identyfikatora zasobu znalezione wcześniej, jak pokazano poniżej.

Przykładowa Właściwość JSON z zakodowanym IDENTYFIKATORem zasobu:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Przykładowa Właściwość JSON konwertowana do wersji sparametryzowanej na podstawie parametrów szablonu

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Zadeklaruj wymagane metadane szablonu i parametry w górnej części szablonu JSON w następujący sposób:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
Po skonfigurowaniu szablonu należy wdrożyć go przy użyciu dowolnej z następujących metod:

* [Interfejsy API REST](/rest/api/resources/deployments)
* [Program PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [Strona wdrożenia szablonu Azure Portal](https://portal.azure.com/#create/Microsoft.Template)

Następnie zobaczysz dwie wersje naszego przykładowego kodu JSON pulpitu nawigacyjnego. Pierwsza to wersja wyeksportowana z portalu, która została już powiązana z zasobem. Druga to wersja szablonu, którą można programowo powiązać z dowolną maszyną wirtualną i wdrożona przy użyciu Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentacja kodu JSON naszego przykładowego pulpitu nawigacyjnego przed tworzenia szablonów

Ten przykład pokazuje, czego można się spodziewać w tym artykule. Instrukcje wyeksportowano reprezentację w formacie JSON pulpitu nawigacyjnego, który został już wdrożony. Zakodowane identyfikatory zasobów pokazują, że ten pulpit nawigacyjny wskazuje na określoną maszynę wirtualną platformy Azure.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentacja szablonu naszego przykładowego pulpitu nawigacyjnego

Wersja szablonu pulpitu nawigacyjnego została zdefiniowana trzy parametry o nazwie `virtualMachineName` , `virtualMachineResourceGroup` , i `dashboardName` .  Parametry pozwalają wskazać ten pulpit nawigacyjny na innej maszynie wirtualnej platformy Azure przy każdym wdrożeniu. Ten pulpit nawigacyjny może być programowo skonfigurowany i wdrażany w taki sposób, aby wskazywał dowolną maszynę wirtualną platformy Azure. Aby przetestować tę funkcję, Skopiuj poniższy szablon i wklej go na [stronie wdrożenia szablonu Azure Portal](https://portal.azure.com/#create/Microsoft.Template).

W tym przykładzie jest wdrażany pulpit nawigacyjny, ale język szablonu umożliwia wdrożenie wielu zasobów i nawiązuje się do nich jeden lub więcej pulpitów nawigacyjnych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Teraz, gdy widzisz przykład użycia szablonu sparametryzowanego do wdrożenia pulpitu nawigacyjnego, możesz spróbować wdrożyć szablon przy użyciu [Azure Resource Manager interfejsów API REST](/rest/api/), [interfejsu wiersza polecenia platformy Azure](/cli/azure)lub [poleceń Azure PowerShell](/powershell/azure/get-started-azureps).