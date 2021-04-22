---
title: Programowe tworzenie pulpitów nawigacyjnych platformy Azure
description: Użyj pulpitu nawigacyjnego w Azure Portal jako szablonu, aby programowo tworzyć pulpity nawigacyjne platformy Azure. Zawiera odwołanie JSON.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: 92848ac238ff11a90afc82713639b8abebf076ec
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878798"
---
# <a name="programmatically-create-azure-dashboards"></a>Programowe tworzenie pulpitów nawigacyjnych platformy Azure

W tym artykule otworzymy programowe tworzenie i publikowanie pulpitów nawigacyjnych platformy Azure. Pulpit nawigacyjny przedstawiony poniżej jest przywołyny w całym dokumencie.

![przykładowy pulpit nawigacyjny](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Omówienie

Udostępnione pulpity nawigacyjne w [Azure Portal](https://portal.azure.com) są [zasobami,](../azure-resource-manager/management/overview.md) podobnie jak maszyny wirtualne i konta magazynu. Zasobami można zarządzać programowo przy użyciu interfejsów [API REST](/rest/api/)Azure Resource Manager , interfejsu wiersza polecenia platformy [Azure](/cli/azure) [Azure PowerShell polecenia](/powershell/azure/get-started-azureps).

Wiele funkcji opiera się na tych interfejsach API, aby ułatwić zarządzanie zasobami. Każdy z tych interfejsów API i narzędzi oferuje sposoby tworzenia, pobierania, modyfikowania i usuwania zasobów. Ponieważ pulpity nawigacyjne są zasobami, możesz wybrać ulubiony interfejs API lub narzędzie do użycia.

Niezależnie od narzędzi, których używasz do programowego tworzenia pulpitu nawigacyjnego, tworzysz reprezentację JSON obiektu pulpitu nawigacyjnego. Ten obiekt zawiera informacje o kafelkach na pulpicie nawigacyjnym. Obejmuje ona rozmiary, pozycje, zasoby, z które są powiązane, oraz wszelkie dostosowania użytkowników.

Najbardziej praktycznym sposobem tworzenia tego dokumentu JSON jest użycie Azure Portal. Kafelki można dodawać i pozycjonować interaktywnie. Następnie wyeksportuj dane JSON i utwórz szablon na podstawie wyniku do późniejszego użycia w skryptach, programach i narzędziach wdrażania.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Aby utworzyć pulpit nawigacyjny, wybierz pozycję **Pulpit nawigacyjny** z menu [Azure Portal, a](https://portal.azure.com) następnie wybierz pozycję Nowy pulpit **nawigacyjny.**

![polecenie nowego pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Użyj galerii kafelków, aby znaleźć i dodać kafelki. Kafelki są dodawane przez przeciąganie i upuszczanie. Niektóre kafelki obsługują zmienianie rozmiaru przy użyciu uchwytu przeciągania.

![przeciąganie uchwytu w celu zmiany rozmiaru](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Inne mają stałe rozmiary do wyboru w menu kontekstowym.

![menu kontekstowe sizes w celu zmiany rozmiaru](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Udostępnianie pulpitu nawigacyjnego

Po skonfigurowaniu pulpitu nawigacyjnego następnym krokiem jest opublikowanie pulpitu nawigacyjnego przy użyciu **polecenia** Udostępnij.

![udostępnianie pulpitu nawigacyjnego](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Wybranie **opcji** Udostępnij monituje o wybranie subskrypcji i grupy zasobów do opublikowania. Musisz mieć dostęp do zapisu dla subskrypcji i grupy zasobów, które wybierzesz. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).

![wprowadzać zmiany w udostępnianiu i dostępie](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pobieranie reprezentacji JSON pulpitu nawigacyjnego

Publikowanie trwa tylko kilka sekund. Po zakończeniu następnym krokiem jest pobranie danych JSON za pomocą **polecenia** Pobierz.

![pobieranie reprezentacji JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Tworzenie szablonu na podstawie danych JSON

Następnym krokiem jest utworzenie szablonu na podstawie tego pliku JSON. Tego szablonu należy używać programowo z odpowiednimi interfejsami API zarządzania zasobami, narzędziami wiersza polecenia lub w portalu.

Nie musisz w pełni rozumieć struktury JSON pulpitu nawigacyjnego, aby utworzyć szablon. W większości przypadków chcesz zachować strukturę i konfigurację każdego kafelka. Następnie sparametryzuj zestaw zasobów platformy Azure, na które wskazują kafelki. Spójrz na wyeksportowany pulpit nawigacyjny JSON i znajdź wszystkie wystąpienia identyfikatorów zasobów platformy Azure. Nasz przykładowy pulpit nawigacyjny zawiera wiele kafelków, które wskazują jedną maszynę wirtualną platformy Azure. Wynika to z tego, że nasz pulpit nawigacyjny patrzy tylko na ten pojedynczy zasób. Jeśli wyszukasz przykładowy kod JSON dołączony na końcu dokumentu dla ciągu "/subscriptions", znajdziesz kilka wystąpień tego identyfikatora.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Aby opublikować ten pulpit nawigacyjny dla dowolnej maszyny wirtualnej w przyszłości, sparametryzuj każde wystąpienie tego ciągu w formacie JSON.

Istnieją dwa podejścia do interfejsów API, które tworzą zasoby na platformie Azure:

* Interfejsy API imperatywnych tworzą po jednym zasobie na raz. Aby uzyskać więcej informacji, zobacz [Zasoby](/rest/api/resources/resources).
* System wdrażania oparty na szablonach, który tworzy wiele zasobów zależnych za pomocą jednego wywołania interfejsu API. Aby uzyskać więcej informacji, zobacz  [Deploy resources with Resource Manager templates and Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)(Wdrażanie zasobów za pomocą szablonów Azure PowerShell ).

Wdrożenie oparte na szablonach obsługuje parametryzację i szablony. W tym artykule używamy tego podejścia.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Programowe tworzenie pulpitu nawigacyjnego na podstawie szablonu przy użyciu wdrożenia szablonu

Platforma Azure oferuje możliwość organizowania wdrażania wielu zasobów. Tworzysz szablon wdrożenia, który wyraża zestaw zasobów do wdrożenia oraz relacje między nimi.  Format JSON każdego zasobu jest taki sam, jak w przypadku tworzenia ich jeden po drugiej. Różnica polega na tym, że język szablonów dodaje kilka pojęć, takich jak zmienne, parametry, podstawowe funkcje i nie tylko. Ta rozszerzona składnia jest obsługiwana tylko w kontekście wdrożenia szablonu. Nie działa, jeśli jest używana z interfejsami API imperatywnymi omówiony wcześniej. Aby uzyskać więcej informacji, [zobacz Understand the structure and syntax of Azure Resource Manager templates (Opis](../azure-resource-manager/templates/template-syntax.md)struktury i składni szablonów Azure Resource Manager szablonów).

Parametryzacja powinna odbywać się przy użyciu składni parametrów szablonu.  Zastąp wszystkie wystąpienia identyfikatora zasobu, które znaleźliśmy wcześniej, jak pokazano poniżej.

Przykładowa właściwość JSON z zakodowanym identyfikatorem zasobu:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Przykładowa właściwość JSON przekonwertowana na wersję sparametryzowane na podstawie parametrów szablonu

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Zadeklaruj wymagane metadane szablonu i parametry w górnej części szablonu JSON w taki sposób:

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
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/group/deployment#az_group_deployment_create)
* [Strona Azure Portal wdrożenia szablonu aplikacji](https://portal.azure.com/#create/Microsoft.Template)

Następnie zobaczysz dwie wersje przykładowego danych JSON pulpitu nawigacyjnego. Pierwsza to wersja wyeksportowana z portalu, która została już powiązana z zasobem. Druga to wersja szablonu, która może być programowo powiązana z dowolną maszyną wirtualną i wdrożona przy użyciu Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Reprezentacja przykładowego pulpitu nawigacyjnego w danych JSON przed rozpoczęciem szablonów

W tym przykładzie pokazano, czego można oczekiwać, jeśli dzieje się to wraz z tym artykułem. Instrukcje wyeksportowały reprezentację JSON pulpitu nawigacyjnego, który został już wdrożony. Zakodowane identyfikatory zasobów pokazują, że ten pulpit nawigacyjny pokazuje określoną maszynę wirtualną platformy Azure.

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

### <a name="template-representation-of-our-example-dashboard"></a>Reprezentacja szablonu przykładowego pulpitu nawigacyjnego

Wersja szablonu pulpitu nawigacyjnego ma zdefiniowane trzy parametry o `virtualMachineName` nazwach `virtualMachineResourceGroup` , i `dashboardName` .  Parametry pozwalają wskazać ten pulpit nawigacyjny na innej maszynie wirtualnej platformy Azure przy każdym wdrożeniu. Ten pulpit nawigacyjny można programowo skonfigurować i wdrożyć, aby wskazać dowolną maszynę wirtualną platformy Azure. Aby przetestować tę funkcję, skopiuj następujący szablon i wklej go na Azure Portal [wdrażania szablonu.](https://portal.azure.com/#create/Microsoft.Template)

W tym przykładzie pulpit nawigacyjny jest wdrażany samodzielnie, ale język szablonu umożliwia wdrażanie wielu zasobów i tworzenie pakietów obok nich co najmniej jednego pulpitu nawigacyjnego.

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

Teraz, gdy już wiesz, jak używać sparametryzowanego szablonu do wdrażania pulpitu nawigacyjnego, możesz spróbować wdrożyć szablon przy użyciu interfejsów API REST usługi [Azure Resource Manager](/rest/api/), interfejsu wiersza polecenia platformy [Azure](/cli/azure) [Azure PowerShell polecenia](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Programowe tworzenie pulpitu nawigacyjnego przy użyciu interfejsu wiersza polecenia platformy Azure

Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Te przykłady używają następującego pulpitu [ nawigacyjnego:portal-dashboard-template-testvm.jsna stronie](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Zastąp zawartość w nawiasach kątowych swoimi wartościami.

Uruchom polecenie [az portal dashboard create,](/cli/azure/portal/dashboard#az_portal_dashboard_create) aby utworzyć pulpit nawigacyjny:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Pulpit nawigacyjny można zaktualizować za pomocą [polecenia az portal dashboard update:](/cli/azure/portal/dashboard#az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Aby wyświetlić szczegóły pulpitu nawigacyjnego, uruchamiając [polecenie az portal dashboard show:](/cli/azure/portal/dashboard#az_portal_dashboard_show)

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Aby wyświetlić wszystkie pulpity nawigacyjne dla bieżącej subskrypcji, użyj [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Możesz również wyświetlić wszystkie pulpity nawigacyjne dla grupy zasobów:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat komputerów stacjonarnych, zobacz [Zarządzanie Azure Portal ustawieniami i preferencjami.](set-preferences.md)

Aby uzyskać więcej informacji na temat obsługi pulpitów nawigacyjnych za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [az portal dashboard](/cli/azure/portal/dashboard).
