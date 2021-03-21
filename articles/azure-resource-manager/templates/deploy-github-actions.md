---
title: Wdrażanie szablonów Menedżer zasobów przy użyciu akcji GitHub
description: Opisuje sposób wdrażania szablonów Azure Resource Manager (szablony ARM) za pomocą akcji usługi GitHub.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 564a21d565fb80eba605eece95562a809a93246f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471916"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Wdrażanie szablonów usługi ARM przy użyciu akcji GitHub

[Akcje GitHub](https://docs.github.com/en/actions) to zestaw funkcji w usłudze GitHub umożliwiających automatyzację przepływów pracy tworzenia oprogramowania w tym samym miejscu, w którym można przechowywać kod i współpracować nad żądaniami ściągnięcia i problemami.

[Akcja Wdróż szablon Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) służy do automatyzowania wdrażania szablonu Azure Resource Manager (szablon ARM) na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).
    - Repozytorium GitHub do przechowywania szablonów Menedżer zasobów i plików przepływu pracy. Aby go utworzyć, zobacz [Tworzenie nowego repozytorium](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdróż szablon Menedżer zasobów. |

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia


Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) można utworzyć jednostkę [usługi](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

Utwórz grupę zasobów, jeśli jeszcze jej nie masz.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Zastąp symbol zastępczy `myApp` nazwą aplikacji.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

W powyższym przykładzie Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji i grupą zasobów. Dane wyjściowe są obiektem JSON z poświadczeniami przypisywania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższego. Skopiuj ten obiekt JSON do nowszej wersji. Wymagane są tylko sekcje zawierające `clientId` wartości,, `clientSecret` `subscriptionId` i `tenantId` .

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
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do grupy zasobów.



## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

Musisz utworzyć wpisy tajne dla poświadczeń platformy Azure, grupy zasobów i subskrypcji.

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium.

1. Wybierz pozycję **ustawienia > wpisy tajne > nowe hasło**.

1. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_CREDENTIALS` .

1. Utwórz inny klucz tajny o nazwie `AZURE_RG` . Dodaj nazwę grupy zasobów do pola wartości wpisu tajnego (przykład: `myResourceGroup` ).

1. Utwórz dodatkowy klucz tajny o nazwie `AZURE_SUBSCRIPTION` . Dodaj swój identyfikator subskrypcji do pola wartości wpisu tajnego (przykład: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Dodaj szablon Menedżer zasobów

Dodaj szablon Menedżer zasobów do repozytorium GitHub. Ten szablon służy do tworzenia konta magazynu.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Plik można umieścić w dowolnym miejscu w repozytorium. Przykład przepływu pracy w następnej sekcji zakłada, że plik szablonu ma nazwę **azuredeploy.jsna** i jest przechowywany w katalogu głównym repozytorium.

## <a name="create-workflow"></a>Tworzenie przepływu pracy

Plik przepływu pracy musi być przechowywany w folderze **. GitHub/** Workflows w katalogu głównym repozytorium. Rozszerzenie pliku przepływu pracy może mieć wartość **. yml** lub **. YAML**.

1. W repozytorium GitHub wybierz pozycję **Akcje** z górnego menu.
1. Wybierz pozycję **Nowy przepływ pracy**.
1. Wybierz opcję **Skonfiguruj przepływ pracy samodzielnie**.
1. Zmień nazwę pliku przepływu pracy, jeśli wolisz inną nazwę niż **Main. yml**. Na przykład: **deployStorageAccount. yml**.
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
    > Zamiast tego można określić plik parametrów formatu JSON w akcji wdrażania ARM (przykład: `.azuredeploy.parameters.json` ).

    Pierwsza sekcja pliku przepływu pracy zawiera:

    - **name**: Nazwa przepływu pracy.
    - **włączone**: Nazwa zdarzeń usługi GitHub, które wyzwalają przepływ pracy. Przepływ pracy jest wyzwalany w przypadku wystąpienia zdarzenia push w głównej gałęzi, która modyfikuje co najmniej jeden z dwóch określonych plików. Te dwa pliki to plik przepływu pracy i plik szablonu.

1. Wybierz pozycję **Start commit** (Rozpocznij zatwierdzanie).
1. Wybierz pozycję **Zatwierdź bezpośrednio w gałęzi głównej**.
1. Wybierz pozycję **Zatwierdź nowy plik** (lub **Zatwierdź zmiany**).

Ponieważ przepływ pracy jest skonfigurowany do wyzwalania przez plik przepływu pracy lub aktualizowany plik szablonu, przepływ pracy zaczyna się bezpośrednio po zatwierdzeniu zmian.

## <a name="check-workflow-status"></a>Sprawdź stan przepływu pracy

1. Wybierz kartę **Akcje** . Zostanie wyświetlony przepływ pracy **Utwórz deployStorageAccount. yml** . Uruchomienie przepływu pracy trwa 1-2 minut.
1. Wybierz przepływ pracy, aby go otworzyć.
1. Wybierz pozycję **Uruchom wdrożenie usługi ARM** z menu, aby zweryfikować wdrożenie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli grupa zasobów i repozytorium nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszego szablonu ARM](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Uczenie modułu: Automatyzowanie wdrażania szablonów ARM przy użyciu akcji GitHub](/learn/modules/deploy-templates-command-line-github-actions/)
