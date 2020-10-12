---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Tworzenie aplikacji logiki
description: Przykładowy skrypt służący do tworzenia aplikacji logiki za pomocą rozszerzenia Logic Apps w interfejsie wiersza polecenia platformy Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/30/2020
ms.openlocfilehash: ceb4a3356ef78d2129f76bd11f555a9ca5206d51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87505837"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Tworzenie aplikacji logiki

Ten skrypt tworzy przykładową aplikację logiki za pomocą [rozszerzenia Logic Apps interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/logic/logic?view=azure-cli-latest)( `az logic` ). Aby uzyskać szczegółowy przewodnik tworzenia aplikacji logiki i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure, [Logic Apps zobacz Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> Rozszerzenie interfejsu wiersza polecenia platformy Azure Logic Apps jest obecnie *eksperymentalne* i *nie jest objęte pomocą techniczną klienta*. Użyj tego rozszerzenia interfejsu wiersza polecenia z zachowaniem ostrożności, zwłaszcza jeśli zdecydujesz się użyć rozszerzenia w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) zainstalowany na komputerze lokalnym.
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure Logic Apps](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest) zainstalowane na tym komputerze. Aby zainstalować to rozszerzenie, użyj tego polecenia: `az extension add --name logic`
* [Definicja przepływu pracy](quickstart-logic-apps-azure-cli.md#workflow-definition) dla aplikacji logiki. Ten plik JSON musi być zgodny ze [schematem języka definicji przepływu pracy](logic-apps-workflow-definition-language.md).
* Połączenie interfejsu API z kontem e-mail za pośrednictwem obsługiwanego [łącznika Logic Apps](../connectors/apis-list.md) w tej samej grupie zasobów, w której znajduje się aplikacja logiki. W tym przykładzie użyto łącznika [pakietu Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) , ale można również użyć innych łączników, takich jak [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź poprawność środowiska przed rozpoczęciem:

* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając `az login` .

* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zapoznaj się z [najnowszymi informacjami o wersji](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest).

  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, postępując zgodnie z [instrukcją instalacji systemu operacyjnego lub platformy](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sample-workflow-explanation"></a>Przykładowe wyjaśnienie przepływu pracy

Ten przykładowy plik definicji przepływu pracy tworzy tę samą podstawową aplikację logiki, co [Logic Apps przewodniku szybki start dla Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Ten przykładowy przepływ pracy: 

1. Określa schemat `$schema` dla aplikacji logiki.

1. Definiuje wyzwalacz dla aplikacji logiki na liście wyzwalaczy `triggers` . Wyzwalacz powtarza się ( `recurrence` ) co 3 godziny. Akcje są wyzwalane po opublikowaniu nowego elementu kanału informacyjnego ( `When_a_feed_item_is_published` ) dla określonego kanału informacyjnego RSS ( `feedUrl` ).

1. Definiuje akcję dla aplikacji logiki na liście akcji `actions` . Akcja wysyła wiadomość e-mail ( `Send_an_email_(V2)` ) za pośrednictwem Microsoft 365 ze szczegółowymi informacjami z elementów kanału informacyjnego RSS, jak określono w sekcji treści ( `body` ) danych wejściowych akcji () `inputs` .

## <a name="sample-workflow-definition"></a>Przykładowa definicja przepływu pracy

Przed uruchomieniem przykładowego skryptu należy najpierw utworzyć przykładową [definicję przepływu pracy](#prerequisites).

1. Utwórz plik JSON `testDefinition.json` na komputerze. 

1. Skopiuj poniższą zawartość do pliku JSON: 
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

1. Zaktualizuj wartości zastępcze przy użyciu własnych informacji:

    1. Zamień symbol zastępczy adresu e-mail ( `"To": "test@example.com"` ). Musisz użyć adresu e-mail zgodnego z łącznikami Logic Apps. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

    1. Zastąp dodatkowe szczegóły łącznika, jeśli używasz innego łącznika poczty e-mail niż łącznik Office 365 Outlook.

    1. Zastąp zastępcze wartości subskrypcji ( `00000000-0000-0000-0000-000000000000` ) dla identyfikatorów połączeń ( `connectionId` i `id` ) w ramach parametru Connections ( `$connections` ) przy użyciu własnych wartości subskrypcji.

1. Zapisz zmiany.

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Ten przykład jest zapisywana dla `bash` powłoki. Jeśli chcesz uruchomić ten przykład w innej powłoki, takiej jak program Windows PowerShell lub wiersz polecenia, może być konieczne wprowadzenie modyfikacji skryptu.

Przed uruchomieniem tego przykładowego skryptu Uruchom to polecenie, aby nawiązać połączenie z platformą Azure:

```azurecli-interactive

az login

```

Następnie przejdź do katalogu, w którym została utworzona definicja przepływu pracy. Jeśli na przykład plik JSON definicji przepływu pracy został utworzony na pulpicie:

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

Po zakończeniu korzystania z przykładowego skryptu Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie jej zagnieżdżone zasoby, w tym aplikację logiki.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten przykładowy skrypt używa następujących poleceń w celu utworzenia nowej grupy zasobów i aplikacji logiki.

| Polecenie | Uwagi |
| ------- | ----- |
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane zasoby aplikacji logiki. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) | Tworzy aplikację logiki na podstawie przepływu pracy zdefiniowanego w parametrze `--definition` . |
| [`az group delete`](/cli/azure/vm/extension?view=azure-cli-latest) | Usuwa grupę zasobów i wszystkie jej zagnieżdżone zasoby. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest).

Logic Apps dodatkowe przykłady skryptów interfejsu wiersza polecenia można znaleźć w [przeglądarce przykładów kodu firmy Microsoft](/samples/browse/?products=azure-logic-apps).
