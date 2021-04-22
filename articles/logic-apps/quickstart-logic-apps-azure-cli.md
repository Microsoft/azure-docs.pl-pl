---
title: Tworzenie aplikacji logiki i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć aplikację logiki, a następnie zarządzać aplikacją logiki przy użyciu operacji takich jak lista, pokazywanie (uzyskiwanie), aktualizowanie i usuwanie.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: afc39673a30f5c99455696c7a075cb1a6a33ecd1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875507"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Szybki start: tworzenie aplikacji logiki i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start pokazano, jak tworzyć aplikacje logiki i zarządzać nimi przy użyciu rozszerzenia interfejsu [wiersza polecenia Logic Apps platformy Azure](/cli/azure/logic) ( `az logic` ). Z wiersza polecenia możesz utworzyć aplikację logiki przy użyciu pliku JSON dla definicji przepływu pracy aplikacji logiki. Następnie możesz zarządzać aplikacją logiki, uruchamiając operacje takie jak `list` , ( ), i z wiersza `show` `get` `update` `delete` polecenia.

> [!WARNING]
> Rozszerzenie interfejsu wiersza polecenia Logic Apps platformy Azure jest obecnie *eksperymentalne* i *nie jest objęte pomocą techniczną dla klientów.* Używaj tego rozszerzenia interfejsu wiersza polecenia z rozwagą, szczególnie jeśli zdecydujesz się używać tego rozszerzenia w środowiskach produkcyjnych.

Jeśli jesteś nowym użytkownikom usługi Logic Apps, możesz również dowiedzieć się, jak tworzyć pierwsze aplikacje logiki za pomocą programu [Azure Portal](quickstart-create-first-logic-app-workflow.md), w programie [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)i Visual Studio Code [.](quickstart-create-logic-apps-visual-studio-code.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowany na komputerze lokalnym.
* Rozszerzenie [Logic Apps interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-list) zainstalowane na komputerze. Aby zainstalować to rozszerzenie, użyj tego polecenia: `az extension add --name logic`
* Grupa [zasobów platformy Azure,](#example---create-resource-group) w której ma być tworzyć aplikację logiki.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Przed rozpoczęciem zweryfikuj środowisko:

* Zaloguj się do Azure Portal i sprawdź, czy twoja subskrypcja jest aktywna, uruchamiając . `az login`
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zobacz [najnowsze informacje o wersji.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, korzystając z przewodnika instalacji dla systemu [operacyjnego lub platformy](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Przykład — tworzenie grupy zasobów

Jeśli nie masz jeszcze grupy zasobów dla aplikacji logiki, utwórz grupę za pomocą polecenia `az group create` . Na przykład następujące polecenie tworzy grupę zasobów o nazwie `testResourceGroup` w lokalizacji `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Dane wyjściowe `provisioningState` pokazują, że po `Succeeded` pomyślnym utworzeniu grupy zasobów:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definicja przepływu pracy

Przed [](#update-logic-apps-from-cli) [utworzeniem nowej aplikacji logiki](#create-logic-apps-from-cli) lub aktualizacją istniejącej aplikacji logiki przy użyciu interfejsu wiersza polecenia platformy Azure potrzebujesz definicji przepływu pracy dla aplikacji logiki. W Azure Portal można wyświetlić podstawową definicję przepływu pracy aplikacji logiki w  formacie JSON, przełączając się z widoku Projektant do **widoku Kod**.

Po uruchomieniu poleceń w celu utworzenia lub zaktualizowania aplikacji logiki definicja przepływu pracy zostanie przesłana jako wymagany parametr ( `--definition` ). Definicję przepływu pracy należy utworzyć jako plik JSON zgodny ze schematem [języka definicji przepływu pracy](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Tworzenie aplikacji logiki z interfejsu wiersza polecenia

Przepływ pracy aplikacji logiki można utworzyć z interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) z plikiem JSON dla definicji.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Polecenie musi zawierać następujące [wymagane parametry:](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Definicja przepływu pracy | `--definition` | Plik JSON z definicją przepływu pracy aplikacji [logiki](#workflow-definition). |
| Lokalizacja | `--location -l` | Region świadczenia usługi Azure, w którym znajduje się aplikacja logiki. |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. Nazwa może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( ) i `()` kropki ( `.` ). Nazwa musi być również unikatowa w różnych regionach. |
| Nazwa grupy zasobów | `--resource-group -g` | Grupa [zasobów platformy Azure,](../azure-resource-manager/management/overview.md) w której chcesz utworzyć aplikację logiki. [Utwórz grupę zasobów](#example---create-resource-group) przed rozpoczęciem, jeśli nie masz jej jeszcze dla aplikacji logiki. |

Można również uwzględnić dodatkowe parametry [opcjonalne do](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) konfigurowania kontroli dostępu, punktów końcowych, konta integracji, środowiska usługi integracji, stanu i tagów zasobów aplikacji logiki.

### <a name="example---create-logic-app"></a>Przykład — tworzenie aplikacji logiki

W tym przykładzie przepływ pracy o nazwie `testLogicApp` jest tworzony w grupie zasobów w lokalizacji `testResourceGroup` `westus` . Plik JSON `testDefinition.json` zawiera definicję przepływu pracy.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Po pomyślnym utworzeniu przepływu pracy interfejs wiersza polecenia wyświetla kod JSON nowej definicji przepływu pracy. Jeśli tworzenie przepływu pracy zakończy się niepowodzeniem, zobacz [listę możliwych błędów.](#errors)

## <a name="update-logic-apps-from-cli"></a>Aktualizowanie aplikacji logiki z interfejsu wiersza polecenia

Możesz również zaktualizować przepływ pracy aplikacji logiki z interfejsu wiersza polecenia platformy Azure za pomocą polecenia [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) .

Polecenie musi zawierać te same [wymagane parametry co](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) podczas tworzenia aplikacji [logiki](#create-logic-apps-from-cli). Możesz również dodać te same parametry [opcjonalne co](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) podczas tworzenia aplikacji logiki.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Przykład — aktualizowanie aplikacji logiki

W tym przykładzie przykładowy przepływ pracy utworzony w poprzedniej sekcji został zaktualizowany w celu użycia innego pliku definicji JSON , i dodania dwóch tagów zasobów oraz wartości [](#example---create-logic-app) `newTestDefinition.json` `testTag1` `testTag2` opisu.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Po pomyślnym zaktualizowaniu przepływu pracy interfejs wiersza polecenia wyświetla zaktualizowaną definicję przepływu pracy aplikacji logiki. Jeśli aktualizacja nie powiedzie się, zobacz [listę możliwych błędów.](#errors)

## <a name="delete-logic-apps-from-cli"></a>Usuwanie aplikacji logiki z interfejsu wiersza polecenia

Przepływ pracy aplikacji logiki można usunąć z interfejsu wiersza polecenia platformy Azure za pomocą polecenia [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete) .

Polecenie musi zawierać następujące [wymagane parametry:](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters)

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. |
| Nazwa grupy zasobów | `-resource-group -g` | Grupa zasobów, w której znajduje się aplikacja logiki. |

Możesz również dołączyć opcjonalny [parametr , aby](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) pominąć monity o potwierdzenie, `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

Następnie interfejs wiersza polecenia wyświetli monit o potwierdzenie usunięcia aplikacji logiki. Monit o potwierdzenie można pominąć, używając opcjonalnego `--yes -y` parametru z poleceniem .

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Usunięcie aplikacji logiki można potwierdzić, wyświetlając listę aplikacji logiki w interfejsie wiersza polecenia lub wyświetlając aplikacje logiki w Azure Portal. [](#list-logic-apps-in-cli)

### <a name="example---delete-logic-app"></a>Przykład — usuwanie aplikacji logiki

W tym przykładzie [przykładowy przepływ pracy utworzony w poprzedniej sekcji](#example---create-logic-app) zostanie usunięty.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Po wyświetleniu monitu o potwierdzenie za pomocą `y` polecenia aplikacja logiki zostanie usunięta.

## <a name="show-logic-apps-in-cli"></a>Pokazywanie aplikacji logiki w interfejsie wiersza polecenia

Określony przepływ pracy aplikacji logiki można uzyskać za pomocą polecenia [`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Polecenie musi zawierać następujące [wymagane parametry](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters)

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. |
| Nazwa grupy zasobów | `--resource-group -g` | Nazwa grupy zasobów, w której znajduje się aplikacja logiki. |

### <a name="example---get-logic-app"></a>Przykład — uzyskiwanie aplikacji logiki

W tym przykładzie aplikacja logiki w grupie zasobów jest zwracana z `testLogicApp` `testResourceGroup` pełnymi dziennikami na potrzeby debugowania.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Lista aplikacji logiki w interfejsie wiersza polecenia

Listę aplikacji logiki można wyświetlić według subskrypcji za pomocą polecenia [`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list) . To polecenie zwraca kod JSON dla przepływów pracy aplikacji logiki.

Wyniki można filtrować według następujących [parametrów opcjonalnych:](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters)

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa grupy zasobów | `--resource-group -g` | Nazwa grupy zasobów, według której chcesz filtrować wyniki. |
| Liczba elementów | `--top` | Liczba elementów uwzględnionych w wynikach. |
| Filtr | `--filter` | Typ filtru, który jest na liście. Możesz filtrować według stanu ( `State` ), wyzwalacza () i `Trigger` identyfikatora zasobu, do których się odwołujesz ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Przykład — lista aplikacji logiki

W tym przykładzie wszystkie włączone przepływy pracy w grupie zasobów są `testResourceGroup` zwracane w formacie tabeli ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>błędy

Poniższy błąd wskazuje, że Azure Logic Apps interfejsu wiersza polecenia nie jest zainstalowane. Wykonaj kroki opisane w wymaganiach wstępnych, aby [zainstalować rozszerzenie Logic Apps na](#prerequisites) komputerze.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Poniższy błąd może wskazywać, że ścieżka pliku do przekazywania definicji przepływu pracy jest nieprawidłowa.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Parametry globalne

Z poleceniami możesz użyć następujących opcjonalnych parametrów globalnego interfejsu wiersza `az logic` polecenia platformy Azure:

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Format danych wyjściowych | `--output -o` | Zmień format [danych wyjściowych](/cli/azure/format-output-azure-cli) z domyślnego formatu JSON. |
| Pokaż tylko błędy | `--only-show-errors` | Pomijaj ostrzeżenia i wyświetlaj tylko błędy. |
| Pełny | `--verbose` | Pokaż pełne dzienniki. |
| Debugowanie | `--debug` | Przedstawia wszystkie dzienniki debugowania. |
| Komunikat pomocy | `--help -h` | Okno dialogowe Pokaż pomoc. |
| Zapytanie | `--query` | Ustaw ciąg zapytania JMESPath dla danych wyjściowych JSON. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację](/cli/azure/)interfejsu wiersza polecenia platformy Azure .

Dodatkowe przykłady skryptów Logic Apps interfejsu wiersza polecenia można znaleźć w [przeglądarce przykładów kodu firmy Microsoft.](/samples/browse/?products=azure-logic-apps)

Następnie możesz utworzyć przykładową logikę aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure przy użyciu przykładowego skryptu i definicji przepływu pracy.

> [!div class="nextstepaction"]
> [Utwórz aplikację logiki przy użyciu przykładowego skryptu](sample-logic-apps-cli-script.md).
