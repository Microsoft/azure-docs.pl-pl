---
title: Programowe tworzenie pulpitów nawigacyjnych platformy Azure
description: Użyj pulpitu nawigacyjnego w witrynie Azure portal jako szablon do programowego tworzenia pulpitów nawigacyjnych platformy Azure. Zawiera odwołanie JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 9ec9a4daad139a4930174ba9e3445e1cda1f8c54
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461314"
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych platformy Azure

W tym artykule otrzymasz od procesu programowego tworzenia i publikowania pulpitów nawigacyjnych platformy Azure. Pulpit nawigacyjny pokazany poniżej odwołuje się do całego dokumentu.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Omówienie

Udostępnione pulpity nawigacyjne w [witrynie Azure portal](https://portal.azure.com) to [zasoby,](../azure-resource-manager/management/overview.md) takie jak maszyny wirtualne i konta magazynu. Zasoby można zarządzać programowo za pomocą [interfejsów API rest usługi Azure Resource Manager](/rest/api/), interfejsu [wiersza polecenia platformy Azure](/cli/azure)i programu Azure [PowerShell.](/powershell/azure/get-started-azureps)

Wiele funkcji opiera się na tych interfejsach API, aby ułatwić zarządzanie zasobami. Każdy z tych interfejsów API i narzędzi oferuje sposoby tworzenia, listy, pobierania, modyfikowania i usuwania zasobów. Ponieważ pulpity nawigacyjne są zasobami, możesz wybrać ulubiony interfejs API lub narzędzie do użycia.

Niezależnie od tego, jakich narzędzi użyjesz, aby programowo utworzyć pulpit nawigacyjny, należy utworzyć reprezentację JSON obiektu pulpitu nawigacyjnego. Ten obiekt zawiera informacje o kafelkach na pulpicie nawigacyjnym. Zawiera rozmiary, pozycje, zasoby, z których są powiązane, oraz wszelkie dostosowania użytkowników.

Najbardziej praktycznym sposobem tworzenia tego dokumentu JSON jest użycie witryny Azure portal. Możesz interaktywnie dodawać i umieszczać kafelki. Następnie wyeksportuj JSON i utwórz szablon z wyniku do późniejszego użycia w skryptach, programach i narzędziach wdrożeniowych.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Aby utworzyć pulpit nawigacyjny, wybierz **pozycję Pulpit nawigacyjny** z menu portalu [Azure,](https://portal.azure.com) a następnie wybierz pozycję **Nowy pulpit nawigacyjny**.

![nowe polecenie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Użyj galerii kafelków, aby znaleźć i dodać kafelki. Kafelki są dodawane przez przeciąganie i upuszczanie ich. Niektóre kafelki obsługują zmiany rozmiaru przy użyciu uchwytu przeciągania.

![przeciąganie uchwytu, aby zmienić rozmiar](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Inne mają stałe rozmiary do wyboru w menu kontekstowym.

![menu kontekstowe rozmiary, aby zmienić rozmiar](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnianie pulpitu nawigacyjnego

Po skonfigurowaniu pulpitu nawigacyjnego następnym krokiem jest opublikowanie pulpitu nawigacyjnego za pomocą polecenia **Udostępnij.**

![udostępnianie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Wybranie **opcji Udostępnij** powoduje wyświetlenie monitu o wybranie subskrypcji i grupy zasobów do opublikowania. Musisz mieć dostęp do zapisu do subskrypcji i grupy zasobów, które wybierzesz. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

![wprowadzanie zmian w udostępnianiu i uzyskiwaniu dostępu](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobieranie reprezentacji JSON pulpitu nawigacyjnego

Publikowanie zajmuje tylko kilka sekund. Po zakończeniu następnym krokiem jest pobranie JSON za pomocą polecenia **Pobierz.**

![pobieranie reprezentacji JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Tworzenie szablonu na podstawie json

Następnym krokiem jest utworzenie szablonu z tego JSON. Użyj tego szablonu programowo z odpowiednimi interfejsami API zarządzania zasobami, narzędziami wiersza polecenia lub w portalu.

Nie musisz w pełni zrozumieć struktury JSON pulpitu nawigacyjnego, aby utworzyć szablon. W większości przypadków chcesz zachować strukturę i konfigurację każdego kafelka. Następnie parametryzuć zestaw zasobów platformy Azure, które wskazują na kafelki. Spójrz na eksportowany pulpit nawigacyjny JSON i znajdź wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Nasz przykładowy pulpit nawigacyjny ma wiele kafelków, które wszystkie wskazują na jednej maszynie wirtualnej platformy Azure. To dlatego, że nasz pulpit nawigacyjny patrzy tylko na ten pojedynczy zasób. Jeśli przeszukujesz przykładowy JSON, zawarte na końcu dokumentu, dla "/subscriptions", można znaleźć kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla dowolnej maszyny wirtualnej w przyszłości, parametryzuj każde wystąpienie tego ciągu w ujmowaniu.

Istnieją dwa podejścia dla interfejsów API, które tworzą zasoby na platformie Azure:

* Imperatywne interfejsy API tworzą jeden zasób naraz. Aby uzyskać więcej informacji, zobacz [Zasoby](/rest/api/resources/resources).
* System wdrażania oparty na szablonach, który tworzy wiele zasobów zależnych za pomocą jednego wywołania interfejsu API. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

Wdrożenie oparte na szablonach obsługuje parametryzację i tworzenie szablonów. Używamy tego podejścia w tym artykule.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowo tworzenie pulpitu nawigacyjnego na podstawie szablonu przy użyciu wdrożenia szablonu

Platforma Azure oferuje możliwość organizowania wdrażania wielu zasobów. Tworzenie szablonu wdrożenia, który wyraża zestaw zasobów do wdrożenia i relacje między nimi.  Format JSON każdego zasobu jest taki sam, jak w przypadku tworzenia ich jeden po drugim. Różnica polega na tym, że język szablonu dodaje kilka pojęć, takich jak zmienne, parametry, podstawowe funkcje i inne. Ta rozszerzona składnia jest obsługiwana tylko w kontekście wdrożenia szablonu. To nie działa, jeśli jest używany z imperatywnych interfejsów API omówione wcześniej. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Parametryzację należy wykonać przy użyciu składni parametrów szablonu.  Możesz zastąpić wszystkie wystąpienia identyfikatora zasobu, który znaleźliśmy wcześniej, jak pokazano tutaj.

Przykładowa właściwość JSON o identyfikatorze zasobu zakodowanym:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Przykładowa właściwość JSON przekonwertowana na wersję sparametryzowaną opartą na parametrach szablonu

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Zadeklarowanie wymaganych metadanych szablonu i parametrów w górnej części szablonu JSON w ten sposób:

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
Po skonfigurowaniu szablonu należy go wdrożyć przy użyciu dowolnej z następujących metod:

* [Interfejsy API REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [Strona wdrażania szablonu portalu Azure](https://portal.azure.com/#create/Microsoft.Template)

Następnie zobaczysz dwie wersje naszego przykładowego pulpitu nawigacyjnego JSON. Pierwsza to wersja, którą wyeksportowaliśmy z portalu, która została już powiązana z zasobem. Druga to wersja szablonu, która może być programowo powiązana z dowolną maszyną wirtualną i wdrożona przy użyciu usługi Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON reprezentacja naszego przykładowego pulpitu nawigacyjnego przed templatingiem

W tym przykładzie pokazano, czego można oczekiwać, aby zobaczyć, jeśli następnie wraz z tym artykułem. Instrukcje wyeksportowane reprezentacji JSON pulpitu nawigacyjnego, który jest już wdrożony. Identyfikatory zasobów zakodowanych na czas pokażą, że ten pulpit nawigacyjny wskazuje na określoną maszynę wirtualną platformy Azure.

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

Wersja szablonu pulpitu nawigacyjnego zdefiniowała trzy parametry o nazwie `virtualMachineName`, `virtualMachineResourceGroup`i `dashboardName`.  Parametry umożliwiają punkt ten pulpit nawigacyjny na innej maszynie wirtualnej platformy Azure za każdym razem, gdy wdrażasz. Ten pulpit nawigacyjny można skonfigurować programowo i wdrożyć w taki sposób, aby wskazywał dowolną maszynę wirtualną platformy Azure. Aby przetestować tę funkcję, skopiuj następujący szablon i wklej go na [stronie wdrażania szablonu portalu Azure](https://portal.azure.com/#create/Microsoft.Template).

W tym przykładzie wdraża pulpit nawigacyjny sam, ale język szablonu umożliwia wdrażanie wielu zasobów i pakiet jeden lub więcej pulpitów nawigacyjnych wraz z nimi.

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

Teraz, gdy widzisz przykład użycia szablonu sparametryzowanego do wdrożenia pulpitu nawigacyjnego, możesz spróbować wdrożyć szablon przy użyciu [poleceń RESTIKTOR USŁUGI Azure Resource Manager](/rest/api/), interfejsu [wiersza polecenia platformy Azure cli](/cli/azure)lub azure [powershell.](/powershell/azure/get-started-azureps)
