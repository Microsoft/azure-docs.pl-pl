---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji logiki
description: Przykładowy skrypt do tworzenia aplikacji logiki za pośrednictwem Logic Apps w interfejsie wiersza polecenia platformy Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: b81d9b4a637965dd103d8fa89305424686a0c72c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789919"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji logiki

Ten skrypt tworzy przykładową aplikację logiki za pośrednictwem interfejsu [wiersza polecenia platformy Azure Logic Apps rozszerzeniem](/cli/azure/ext/logic/logic), ( `az logic` ). Aby uzyskać szczegółowy przewodnik dotyczący tworzenia aplikacji logiki i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure, zobacz Logic Apps szybki start dotyczący interfejsu [wiersza polecenia platformy Azure.](quickstart-logic-apps-azure-cli.md)

> [!WARNING]
> Rozszerzenie interfejsu wiersza polecenia Logic Apps platformy Azure jest obecnie *eksperymentalne* i *nie jest objęte pomocą techniczną dla klientów.* Używaj tego rozszerzenia interfejsu wiersza polecenia z rozwagą, szczególnie jeśli zdecydujesz się używać tego rozszerzenia w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowany na komputerze lokalnym.
* Rozszerzenie [Logic Apps interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-list) zainstalowane na komputerze. Aby zainstalować to rozszerzenie, użyj tego polecenia: `az extension add --name logic`
* Definicja [przepływu](quickstart-logic-apps-azure-cli.md#workflow-definition) pracy dla aplikacji logiki. Ten plik JSON musi być zgodne ze [schematem języka definicji przepływu pracy](logic-apps-workflow-definition-language.md).
* Połączenie interfejsu API z kontem e-mail za pośrednictwem obsługiwanego [łącznika Logic Apps w](../connectors/apis-list.md) tej samej grupie zasobów co aplikacja logiki. W tym przykładzie użyto [łącznika usługi Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) ale można również użyć innych łączników, takich [jak Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Przed rozpoczęciem zweryfikuj środowisko:

* Zaloguj się do Azure Portal i sprawdź, czy twoja subskrypcja jest aktywna, uruchamiając . `az login`

* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zobacz [najnowsze informacje o wersji.](/cli/azure/release-notes-azure-cli)

  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, korzystając z przewodnika instalacji dla systemu operacyjnego [lub platformy](/cli/azure/install-azure-cli).

### <a name="sample-workflow-explanation"></a>Wyjaśnienie przykładowego przepływu pracy

Ten przykładowy plik definicji przepływu pracy tworzy tę samą podstawową aplikację [logiki co Logic Apps szybki](quickstart-create-first-logic-app-workflow.md)start dla Azure Portal . 

Ten przykładowy przepływ pracy: 

1. Określa schemat aplikacji `$schema` logiki.

1. Definiuje wyzwalacz dla aplikacji logiki na liście wyzwalaczy `triggers` . Wyzwalacz jest powtarzany `recurrence` () co 3 godziny. Akcje są wyzwalane po opublikowaniu nowego elementu kanału informacyjnego ( `When_a_feed_item_is_published` ) dla określonego kanału informacyjnego RSS ( `feedUrl` ).

1. Definiuje akcję dla aplikacji logiki na liście akcji `actions` . Akcja wysyła wiadomość e-mail ( ) za pośrednictwem Microsoft 365 ze szczegółami z elementów kanału informacyjnego RSS określonymi w sekcji treści ( ) danych wejściowych `Send_an_email_(V2)` `body` akcji ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definicja przykładowego przepływu pracy

Przed uruchomieniem przykładowego skryptu należy najpierw utworzyć przykładową definicję [przepływu pracy](#prerequisites).

1. Utwórz plik JSON `testDefinition.json` na komputerze. 

1. Skopiuj następującą zawartość do pliku JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Zaktualizuj wartości symboli zastępczych własnymi informacjami:

    1. Zastąp zastępczy adres e-mail ( `"To": "test@example.com"` ). Musisz użyć adresu e-mail zgodnego z Logic Apps łącznikami. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

    1. Zastąp dodatkowe szczegóły łącznika, jeśli używasz innego łącznika poczty e-mail niż łącznik usługi Office 365 Outlook.

    1. Zastąp symbole zastępcze wartości subskrypcji ( ) dla `00000000-0000-0000-0000-000000000000` identyfikatorów połączeń ( `connectionId` i ) `id` w parametrze połączeń ( ) `$connections` własnymi wartościami subskrypcji.

1. Zapisz zmiany.

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Ten przykład jest napisany dla `bash` powłoki . Jeśli chcesz uruchomić ten przykład w innej powłoki, takiej jak Windows PowerShell lub wierszu polecenia, może być konieczne wprowadzenie modyfikacji skryptu.

Przed uruchomieniem tego przykładowego skryptu uruchom to polecenie, aby nawiązać połączenie z platformą Azure:

```azurecli-interactive

az login

```

Następnie przejdź do katalogu, w którym utworzono definicję przepływu pracy. Jeśli na przykład utworzono plik JSON definicji przepływu pracy na pulpicie:

```azurecli

cd ~/Desktop

```

Następnie uruchom ten skrypt, aby utworzyć aplikację logiki. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po zakończeniu korzystania z przykładowego skryptu uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie jej zagnieżdżone zasoby, w tym aplikację logiki.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten przykładowy skrypt używa poniższych poleceń do utworzenia nowej grupy zasobów i aplikacji logiki.

| Polecenie | Uwagi |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane zasoby aplikacji logiki. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Tworzy aplikację logiki na podstawie przepływu pracy zdefiniowanego w parametrze `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Usuwa grupę zasobów i wszystkie jej zagnieżdżone zasoby. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure.](/cli/azure/)

Dodatkowe przykłady skryptów Logic Apps interfejsu wiersza polecenia można znaleźć w [przeglądarce przykładów kodu firmy Microsoft.](/samples/browse/?products=azure-logic-apps)
