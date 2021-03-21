---
title: Tworzenie aplikacji logiki i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć aplikację logiki, a następnie zarządzać aplikacją logiki przy użyciu operacji takich jak list, show (Get), Update i DELETE.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033378"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Szybki Start: Tworzenie aplikacji logiki i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start pokazano, jak tworzyć aplikacje logiki i zarządzać nimi za pomocą [rozszerzenia Logic Apps interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/logic/logic) ( `az logic` ). W wierszu polecenia można utworzyć aplikację logiki przy użyciu pliku JSON dla definicji przepływu pracy aplikacji logiki. Następnie możesz zarządzać aplikacją logiki, uruchamiając operacje, takie jak `list` , `show` ( `get` ), `update` i `delete` z wiersza polecenia.

> [!WARNING]
> Rozszerzenie interfejsu wiersza polecenia platformy Azure Logic Apps jest obecnie *eksperymentalne* i *nie jest objęte pomocą techniczną klienta*. Użyj tego rozszerzenia interfejsu wiersza polecenia z zachowaniem ostrożności, zwłaszcza jeśli zdecydujesz się użyć rozszerzenia w środowiskach produkcyjnych.

Jeśli dopiero zaczynasz Logic Apps, możesz również dowiedzieć się, jak utworzyć swoje pierwsze Aplikacje logiki [za pomocą Azure Portal](quickstart-create-first-logic-app-workflow.md), [w programie Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)i [w Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowany na komputerze lokalnym.
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure Logic Apps](/cli/azure/azure-cli-extensions-list) zainstalowane na tym komputerze. Aby zainstalować to rozszerzenie, użyj tego polecenia: `az extension add --name logic`
* [Grupa zasobów platformy Azure](#example---create-resource-group) , w której ma zostać utworzona aplikacja logiki.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Sprawdź poprawność środowiska przed rozpoczęciem:

* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając `az login` .
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zapoznaj się z [najnowszymi informacjami o wersji](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, postępując zgodnie z [instrukcją instalacji systemu operacyjnego lub platformy](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Przykład — tworzenie grupy zasobów

Jeśli nie masz jeszcze grupy zasobów dla aplikacji logiki, Utwórz grupę przy użyciu polecenia `az group create` . Na przykład następujące polecenie tworzy grupę zasobów o nazwie `testResourceGroup` w lokalizacji `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Dane wyjściowe pokazują, `provisioningState` jak `Succeeded` po pomyślnym utworzeniu grupy zasobów:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definicja przepływu pracy

Przed [utworzeniem nowej aplikacji logiki](#create-logic-apps-from-cli) lub [zaktualizowaniem istniejącej aplikacji logiki](#update-logic-apps-from-cli) przy użyciu interfejsu wiersza polecenia platformy Azure potrzebna jest definicja przepływu pracy dla aplikacji logiki. W Azure Portal można wyświetlić podstawową definicję przepływu pracy aplikacji logiki w formacie JSON, przełączając się z widoku **projektanta** do **widoku kodu**.

Po uruchomieniu poleceń w celu utworzenia lub zaktualizowania aplikacji logiki, definicja przepływu pracy zostanie przekazana jako wymagany parametr ( `--definition` ). Należy utworzyć definicję przepływu pracy jako plik JSON, który następuje po [schemacie języka definicji przepływu pracy](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Tworzenie aplikacji logiki przy użyciu interfejsu wiersza polecenia

Można utworzyć przepływ pracy aplikacji logiki z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu podanej w [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) pliku JSON dla definicji.

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

Polecenie musi zawierać następujące [wymagane parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters):

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Definicja przepływu pracy | `--definition` | Plik JSON z [definicją przepływu pracy](#workflow-definition)aplikacji logiki. |
| Lokalizacja | `--location -l` | Region świadczenia usługi Azure, w którym znajduje się aplikacja logiki. |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. Nazwa może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `()` ) i kropki ( `.` ). Nazwa musi również być unikatowa w różnych regionach. |
| Nazwa grupy zasobów | `--resource-group -g` | [Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) , w której chcesz utworzyć aplikację logiki. [Utwórz grupę zasobów](#example---create-resource-group) przed rozpoczęciem, jeśli jeszcze jej nie masz dla aplikacji logiki. |

Można również dołączyć dodatkowe [Parametry opcjonalne](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) , aby skonfigurować kontrolki dostępu aplikacji logiki, punkty końcowe, konto integracji, środowisko usługi integracji, stan i Tagi zasobów.

### <a name="example---create-logic-app"></a>Przykład — tworzenie aplikacji logiki

W tym przykładzie zostanie utworzony przepływ pracy o nazwie `testLogicApp` w grupie zasobów `testResourceGroup` w lokalizacji `westus` . Plik JSON `testDefinition.json` zawiera definicję przepływu pracy.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Po pomyślnym utworzeniu przepływu pracy w interfejsie wiersza polecenia zostanie wyświetlony kod JSON nowej definicji przepływu pracy. Jeśli tworzenie przepływu pracy zakończy się niepowodzeniem, zobacz [listę możliwych błędów](#errors).

## <a name="update-logic-apps-from-cli"></a>Aktualizowanie aplikacji logiki przy użyciu interfejsu wiersza polecenia

Możesz również zaktualizować przepływ pracy aplikacji logiki z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) .

Polecenie musi zawierać te same [wymagane parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) , jak podczas [tworzenia aplikacji logiki](#create-logic-apps-from-cli). Możesz również dodać te same [Parametry opcjonalne](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) jak podczas tworzenia aplikacji logiki.

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

W tym przykładzie [przykładowy przepływ pracy utworzony w poprzedniej sekcji](#example---create-logic-app) został zaktualizowany tak, aby korzystał z innego pliku definicji JSON, `newTestDefinition.json` i dodać dwa Tagi zasobów `testTag1` i `testTag2` z wartościami opisu.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Po pomyślnym zaktualizowaniu przepływu pracy w interfejsie wiersza polecenia zostanie wyświetlona zaktualizowana definicja przepływu pracy aplikacji logiki. Jeśli aktualizacja nie powiedzie się, zapoznaj się z [listą możliwych błędów](#errors).

## <a name="delete-logic-apps-from-cli"></a>Usuwanie aplikacji logiki z interfejsu wiersza polecenia

Przepływ pracy aplikacji logiki można usunąć z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) .

Polecenie musi zawierać następujące [wymagane parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters):

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. |
| Nazwa grupy zasobów | `-resource-group -g` | Grupa zasobów, w której znajduje się aplikacja logiki. |

Można również dołączyć [opcjonalny parametr](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) , aby pominąć monity o potwierdzenie `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

W interfejsie wiersza polecenia zostanie wyświetlony komunikat z prośbą o potwierdzenie usunięcia aplikacji logiki. Możesz pominąć monit o potwierdzenie, używając opcjonalnego parametru `--yes -y` za pomocą polecenia.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Można potwierdzić usunięcie aplikacji logiki, [wymieniając Aplikacje logiki w interfejsie wiersza polecenia](#list-logic-apps-in-cli)lub wyświetlając Aplikacje logiki w Azure Portal.

### <a name="example---delete-logic-app"></a>Przykład — usuwanie aplikacji logiki

W tym przykładzie zostanie usunięty [przykładowy przepływ pracy utworzony w poprzedniej sekcji](#example---create-logic-app) .

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Po udzieleniu odpowiedzi na monit o potwierdzenie z programu `y` aplikacja logiki zostanie usunięta.

## <a name="show-logic-apps-in-cli"></a>Pokaż Aplikacje logiki w interfejsie wiersza polecenia

Możesz uzyskać konkretny przepływ pracy aplikacji logiki przy użyciu polecenia [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Polecenie musi zawierać następujące [wymagane parametry](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa | `--name -n` | Nazwa aplikacji logiki. |
| Nazwa grupy zasobów | `--resource-group -g` | Nazwa grupy zasobów, w której znajduje się aplikacja logiki. |

### <a name="example---get-logic-app"></a>Przykład — pobieranie aplikacji logiki

W tym przykładzie aplikacja logiki `testLogicApp` w grupie zasobów `testResourceGroup` jest zwracana z pełnymi dziennikami na potrzeby debugowania.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Wyświetlanie listy aplikacji logiki w interfejsie wiersza polecenia

Możesz wyświetlić listę aplikacji logiki za pomocą polecenia [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) . To polecenie zwraca kod JSON dla przepływów pracy aplikacji logiki.

Wyniki można filtrować według następujących [parametrów opcjonalnych](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters):

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Nazwa grupy zasobów | `--resource-group -g` | Nazwa grupy zasobów, za pomocą której mają być filtrowane wyniki. |
| Liczba elementów | `--top` | Liczba elementów uwzględnionych w wynikach. |
| Filtr | `--filter` | Typ filtru, który jest używany na liście. Można filtrować według stanu ( `State` ), wyzwalacza ( `Trigger` ) i identyfikatora przywoływanego zasobu ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Przykład — lista aplikacji logiki

W tym przykładzie wszystkie włączone przepływy pracy w grupie zasobów `testResourceGroup` są zwracane w formacie tabeli ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>błędy

Następujący błąd wskazuje, że rozszerzenie interfejsu wiersza polecenia Azure Logic Apps nie jest zainstalowane. Wykonaj kroki opisane w sekcji wymagania wstępne, aby [zainstalować rozszerzenie Logic Apps](#prerequisites) na komputerze.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Następujący błąd może wskazywać, że ścieżka pliku do przekazania definicji przepływu pracy jest niepoprawna.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Parametry globalne

Za pomocą poleceń można użyć następujących opcjonalnych globalnych parametrów interfejsu wiersza polecenia platformy Azure `az logic` :

| Parametr | Wartość | Opis |
| --------- | ----- | ----------- |
| Format danych wyjściowych | `--output -o` | Zmień [Format danych wyjściowych](/cli/azure/format-output-azure-cli) z domyślnego formatu JSON. |
| Pokaż tylko błędy | `--only-show-errors` | Pomijaj ostrzeżenia i Wyświetlaj tylko błędy. |
| Pełny | `--verbose` | Pokaż pełne dzienniki. |
| Debugowanie | `--debug` | Pokazuje wszystkie dzienniki debugowania. |
| Komunikat pomocy | `--help -h` | Pokaż okno dialogowe pomocy. |
| Zapytanie | `--query` | Ustaw ciąg zapytania JMESPath dla danych wyjściowych JSON. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/).

Logic Apps dodatkowe przykłady skryptów interfejsu wiersza polecenia można znaleźć w [przeglądarce przykładów kodu firmy Microsoft](/samples/browse/?products=azure-logic-apps).

Następnie można utworzyć przykładową logikę aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure przy użyciu przykładowego skryptu i definicji przepływu pracy.

> [!div class="nextstepaction"]
> [Utwórz aplikację logiki przy użyciu przykładowego skryptu](sample-logic-apps-cli-script.md).
