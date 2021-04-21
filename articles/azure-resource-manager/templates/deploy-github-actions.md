---
title: Wdrażanie Resource Manager szablonów przy użyciu GitHub Actions
description: Opisuje sposób wdrażania Azure Resource Manager usługi ARM przy użyciu GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781977"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Wdrażanie szablonów usługi ARM przy użyciu GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) to pakiet funkcji w usłudze GitHub, które automatyzują przepływy pracy tworzenia oprogramowania w tym samym miejscu, w którym przechowujesz kod oraz współpracujesz nad żądaniami ściągnięć i problemami.

Użyj akcji [Wd Azure Resource Manager szablonu,](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) aby zautomatyzować wdrażanie szablonu Azure Resource Manager (szablonu ARM) na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli go nie masz, zarejestruj się [bezpłatnie.](https://github.com/join)
    - Repozytorium GitHub do przechowywania szablonów Resource Manager i plików przepływu pracy. Aby go utworzyć, zobacz [Creating a new repository (Tworzenie nowego repozytorium).](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)


## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik zawiera dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Definiowanie jednostki usługi. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Wdrażanie** | 1. Wd wdrażaj Resource Manager szablonu. |

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia


Jednostkę usługi można utworzyć [za](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) Uruchom to [polecenie, Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

Utwórz grupę zasobów, jeśli jeszcze jej nie masz.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Zastąp symbol `myApp` zastępczy nazwą swojej aplikacji.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

W powyższym przykładzie zastąp symbole zastępcze identyfikatorem subskrypcji i nazwą grupy zasobów. Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższej. Skopiuj ten obiekt JSON na później. Potrzebne będą tylko sekcje z `clientId` wartościami , `clientSecret` , i `subscriptionId` `tenantId` .

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Zawsze dobrym rozwiązaniem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do grupy zasobów.



## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

Musisz utworzyć wpisy tajne dla poświadczeń platformy Azure, grupy zasobów i subskrypcji.

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium.

1. Wybierz **pozycję Ustawienia > wpisy tajne > nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości w kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

1. Utwórz inny klucz tajny o nazwie `AZURE_RG` . Dodaj nazwę grupy zasobów do pola wartości tajnego (przykład: `myResourceGroup` ).

1. Utwórz dodatkowy klucz tajny o nazwie `AZURE_SUBSCRIPTION` . Dodaj identyfikator subskrypcji do pola wartości tajnego (przykład: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Dodawanie Resource Manager szablonu

Dodaj szablon Resource Manager do repozytorium GitHub. Ten szablon tworzy konto magazynu.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Plik można umieścić w dowolnym miejscu w repozytorium. W przykładzie przepływu pracy w następnej sekcji przyjęto założenie, że plik szablonu ma nazwę **azuredeploy.js** w pliku i jest przechowywany w katalogu głównym repozytorium.

## <a name="create-workflow"></a>Tworzenie przepływu pracy

Plik przepływu pracy musi być przechowywany w **folderze .github/workflows** w katalogu głównym repozytorium. Rozszerzeniem pliku przepływu pracy może być **plik yml** lub **yaml.**

1. W repozytorium GitHub wybierz pozycję **Actions (Akcje)** z górnego menu.
1. Wybierz **pozycję Nowy przepływ pracy.**
1. Wybierz **opcję skonfiguruj przepływ pracy samodzielnie.**
1. Zmień nazwę pliku przepływu pracy, jeśli wolisz inną nazwę niż **main.yml.** Na przykład: **deployStorageAccount.yml**.
1. Zastąp zawartość tego pliku yml następującym kodem:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Zamiast tego można określić plik parametrów formatu JSON w akcji wdrażania usługi ARM (na przykład: `.azuredeploy.parameters.json` ).

    Pierwsza sekcja pliku przepływu pracy obejmuje:

    - **name**: nazwa przepływu pracy.
    - **on**: nazwa zdarzeń usługi GitHub, które wyzwalają przepływ pracy. Przepływ pracy jest wyzwalany, gdy w gałęzi main istnieje zdarzenie wypychania, które modyfikuje co najmniej jeden z dwóch określonych plików. Dwa pliki to plik przepływu pracy i plik szablonu.

1. Wybierz pozycję **Start commit** (Rozpocznij zatwierdzanie).
1. Wybierz **pozycję Zat zatwierdzanie bezpośrednio w głównej gałęzi**.
1. Wybierz **pozycję Zat zatwierdzanie nowego pliku** (lub **Zatwierdzanie zmian).**

Ponieważ przepływ pracy jest skonfigurowany do wyzwalania przez plik przepływu pracy lub aktualizowany plik szablonu, przepływ pracy jest uruchamiany bezpośrednio po zatwierdzeniu zmian.

## <a name="check-workflow-status"></a>Sprawdzanie stanu przepływu pracy

1. Wybierz **kartę** Akcje. Zostanie wyświetlony przepływ pracy **Create deployStorageAccount.yml.** Uruchomienie przepływu pracy trwa od 1 do 2 minut.
1. Wybierz przepływ pracy, aby go otworzyć.
1. Wybierz **pozycję Uruchom wdrożenie usługi ARM** z menu, aby zweryfikować wdrożenie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy grupa zasobów i repozytorium nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszego szablonu usługi ARM](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Moduł Learn: Automatyzowanie wdrażania szablonów usługi ARM przy użyciu GitHub Actions](/learn/modules/deploy-templates-command-line-github-actions/)
