---
title: Wdrażanie szablonów Menedżer zasobów przy użyciu akcji GitHub
description: Opisuje sposób wdrażania szablonów Azure Resource Manager przy użyciu akcji usługi GitHub.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85854744"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Wdrażanie szablonów Azure Resource Manager przy użyciu akcji GitHub

[Akcje GitHub](https://help.github.com/en/actions) umożliwiają tworzenie niestandardowych przepływów pracy tworzenia oprogramowania w repozytorium GitHub, w których są przechowywane szablony Azure Resource Manager (ARM). [Przepływ pracy](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) jest definiowany przez plik YAML. Przepływy pracy zawierają jedno lub więcej zadań z zadaniami zawierającymi zestaw kroków wykonujących poszczególne zadania. Kroki mogą uruchamiać polecenia lub korzystać z akcji. Możesz tworzyć własne działania lub wykonywać akcje udostępnione przez [społeczność usługi GitHub](https://github.com/marketplace?type=actions) i dostosowywać je zgodnie z potrzebami. W tym artykule przedstawiono sposób wdrażania szablonów Menedżer zasobów przy użyciu [akcji interfejsu wiersza polecenia platformy Azure](https://github.com/marketplace/actions/azure-cli-action) .

Akcja interfejsu wiersza polecenia platformy Azure ma dwie akcje zależne:

- **[Wyewidencjonowywanie](https://github.com/marketplace/actions/checkout)**: Wyewidencjonuj repozytorium, aby przepływ pracy mógł uzyskać dostęp do określonego szablonu Menedżer zasobów.
- **[Logowanie do platformy Azure](https://github.com/marketplace/actions/azure-login)**: Logowanie przy użyciu poświadczeń platformy Azure

Podstawowy przepływ pracy wdrażania szablonu Menedżer zasobów może obejmować trzy kroki:

1. Wyewidencjonuj plik szablonu.
2. Zaloguj się do platformy Azure.
3. Wdrażanie szablonu Menedżer zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz repozytorium GitHub do przechowywania szablonów Menedżer zasobów i plików przepływu pracy. Aby go utworzyć, zobacz [Tworzenie nowego repozytorium](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Konfigurowanie poświadczeń wdrożenia

Akcja logowania platformy Azure używa jednostki usługi do uwierzytelniania na platformie Azure. Główna przepływ pracy ciągłej integracji/ciągłego wdrażania wymaga od razu wbudowanego współautora w celu wdrożenia zasobów platformy Azure.

Poniższy skrypt interfejsu wiersza polecenia platformy Azure pokazuje, jak wygenerować jednostkę usługi platformy Azure z uprawnieniami współautora w grupie zasobów platformy Azure. Ta grupa zasobów to miejsce, w którym przepływ pracy wdraża zasoby zdefiniowane w szablonie Menedżer zasobów.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Dostosuj wartość **$projectName** i **$Location** w skrypcie. Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** . Należy określić nazwę grupy zasobów w przepływie pracy.

Skrypt wyprowadza obiekt JSON podobny do tego:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Skopiuj dane wyjściowe JSON i Zapisz je jako wpis tajny usługi GitHub w repozytorium GitHub. Jeśli nie masz jeszcze repozytorium, zapoznaj się z [wymaganiami wstępnymi](#prerequisites) .

1. W repozytorium GitHub wybierz kartę **Ustawienia** .
1. W menu po lewej stronie wybierz pozycję wpisy **tajne** .
1. Podaj następujące wartości:

    - **Nazwa**: AZURE_CREDENTIALS
    - **Wartość**: (Wklej dane wyjściowe JSON)
1. Wybierz pozycję **Dodaj klucz tajny**.

Należy określić nazwę wpisu tajnego w przepływie pracy.

## <a name="add-resource-manager-template"></a>Dodaj szablon Menedżer zasobów

Dodaj szablon Menedżer zasobów do repozytorium GitHub. Jeśli go nie masz, możesz użyć poniższego szablonu. Szablon tworzy konto magazynu.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Plik można umieścić w dowolnym miejscu w repozytorium. Przykład przepływu pracy w następnej sekcji zakłada, że plik szablonu ma nazwę **azuredeploy.json**i jest przechowywany w folderze o nazwie **templates** w katalogu głównym repozytorium.

## <a name="create-workflow"></a>Tworzenie przepływu pracy

Plik przepływu pracy musi być przechowywany w folderze **. GitHub/** Workflows w katalogu głównym repozytorium. Rozszerzenie pliku przepływu pracy może mieć wartość **. yml** lub **. YAML**.

Można utworzyć plik przepływu pracy, a następnie wypchnąć/przekazać plik do repozytorium lub użyć następującej procedury:

1. W repozytorium GitHub wybierz pozycję **Akcje** z górnego menu.
1. Wybierz pozycję **Nowy przepływ pracy**.
1. Wybierz opcję **Skonfiguruj przepływ pracy samodzielnie**.
1. Zmień nazwę pliku przepływu pracy, jeśli wolisz inną nazwę niż **Main. yml**. Na przykład: **deployStorageAccount. yml**.
1. Zastąp zawartość pliku YML następującym:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Plik przepływu pracy ma trzy sekcje:

    - **name**: Nazwa przepływu pracy.
    - **włączone**: Nazwa zdarzeń usługi GitHub, które wyzwalają przepływ pracy. Przepływ pracy jest wyzwalany w przypadku wystąpienia zdarzenia push w gałęzi głównej, który modyfikuje co najmniej jeden z dwóch określonych plików. Te dwa pliki to plik przepływu pracy i plik szablonu.

        > [!IMPORTANT]
        > Sprawdź, czy te dwa pliki i ich ścieżki pasują do siebie.
    - **zadania**: przebieg przepływu pracy składa się z co najmniej jednego zadania. Istnieje tylko jedno zadanie o nazwie **Deploy-Storage-account-Template**.  To zadanie ma trzy kroki:

        - **Wyewidencjonowywanie kodu źródłowego**.
        - **Zaloguj się do platformy Azure**.

            > [!IMPORTANT]
            > Sprawdź, czy nazwa wpisu tajnego pasuje do zawartości zapisanej w repozytorium. Zobacz [Konfigurowanie poświadczeń wdrożenia](#configure-deployment-credentials).
        - **Wdróż szablon ARM**. Zastąp wartość **resourceGroupName**.  Jeśli użyto skryptu interfejsu wiersza polecenia platformy Azure w obszarze [Konfigurowanie poświadczeń wdrażania](#configure-deployment-credentials), wygenerowana nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** . Sprawdź wartość **templateLocation**.

1. Wybierz pozycję **Rozpocznij zatwierdzenie**.
1. Wybierz pozycję **Zatwierdź bezpośrednio w gałęzi głównej**.
1. Wybierz pozycję **Zatwierdź nowy plik** (lub **Zatwierdź zmiany**).

Ponieważ przepływ pracy jest skonfigurowany do wyzwalania przez plik przepływu pracy lub aktualizowany plik szablonu, przepływ pracy zaczyna się bezpośrednio po zatwierdzeniu zmian.

## <a name="check-workflow-status"></a>Sprawdź stan przepływu pracy

1. Wybierz kartę **Akcje** . Zobaczysz przepływ pracy **Create deployStorageAccount. yml** na liście. Wykonanie przepływu pracy trwa 1-2 minut.
1. Wybierz przepływ pracy, aby go otworzyć.
1. Wybierz pozycję **Wdróż-Storage-account-Template** (nazwa zadania) w menu po lewej stronie. Nazwa zadania jest definiowana w przepływie pracy.
1. Wybierz pozycję **Wdróż szablon ARM** (Nazwa kroku), aby ją rozwinąć. Możesz zobaczyć odpowiedź interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu usługi ARM](template-tutorial-create-first-template.md).
