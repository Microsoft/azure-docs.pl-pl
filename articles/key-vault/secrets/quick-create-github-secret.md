---
title: Szybki Start — używanie Azure Key Vault wpisów tajnych w przepływach pracy akcji usługi GitHub
description: Użyj Key Vault wpisów tajnych w akcjach usługi GitHub w celu zautomatyzowania przepływów pracy tworzenia oprogramowania
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920284"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Używanie Key Vault wpisów tajnych w przepływach pracy akcji usługi GitHub

Używaj Key Vault wpisów tajnych w [działaniach usługi GitHub](https://help.github.com/en/articles/about-github-actions) i bezpiecznie przechowuj hasła oraz inne wpisy tajne w magazynie kluczy platformy Azure. Dowiedz się więcej o [Key Vault](../general/overview.md). 

Dzięki akcjom Key Vault i GitHub masz zalety scentralizowanego narzędzia do zarządzania kluczami tajnymi oraz wszystkie zalety akcji usługi GitHub. Akcje GitHub to zestaw funkcji w usłudze GitHub umożliwiających automatyzację przepływów pracy tworzenia oprogramowania. Przepływy pracy można wdrażać w tym samym miejscu, w którym można przechowywać kod i współpracować nad żądaniami ściągnięcia. 


## <a name="prerequisites"></a>Wymagania wstępne 
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aplikacja platformy Azure połączona z repozytorium GitHub. W tym przykładzie zastosowano [kontenery wdrażania do Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Magazyn kluczy platformy Azure.  Azure Key Vault można utworzyć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma na celu uwierzytelnianie przy użyciu dwóch sekcji akcji usługi GitHub:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. <br /> 3. Dodaj przypisanie roli. |
|**Usługa Key Vault** | 1. Dodaj akcję magazynu kluczy. <br /> 2. odwołuje się do wpisu tajnego magazynu kluczy. |

## <a name="authentication"></a>Authentication

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

W powyższym przykładzie Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji i grupą zasobów. Zastąp symbol zastępczy `myApp` nazwą aplikacji. Dane wyjściowe są obiektem JSON z poświadczeniami przypisywania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższego. Skopiuj ten obiekt JSON do nowszej wersji. Wymagane są tylko sekcje zawierające `clientId` wartości,, `clientSecret` `subscriptionId` i `tenantId` . 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

Utwórz wpisy tajne dla poświadczeń platformy Azure, grupy zasobów i subskrypcji. 

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium.

1. Wybierz pozycję **ustawienia > wpisy tajne > nowe hasło**.

1. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_CREDENTIALS` .

1. Skopiuj wartość `clientId` do użycia później. 

### <a name="add-a-role-assignment"></a>Dodaj przypisanie roli 
 
Musisz udzielić dostępu do jednostki usługi platformy Azure, aby można było uzyskać dostęp do magazynu kluczy dla `get` i `list` operacji. Jeśli tego nie zrobisz, nie będzie można używać nazwy głównej usługi. 

Zamień `keyVaultName` na nazwę magazynu kluczy i `clientIdGUID` wartość parametru `clientId` . 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Korzystanie z akcji magazynu kluczy platformy Azure

Za pomocą [akcji magazynu kluczy platformy Azure](https://github.com/marketplace/actions/azure-key-vault-get-secrets)można pobrać co najmniej jedno sekretowe z wystąpienia magazynu kluczy platformy Azure i korzystać z niego w przepływach pracy akcji usługi GitHub.

Pobrane wpisy tajne są ustawiane jako dane wyjściowe, a także jako zmienne środowiskowe. Zmienne są automatycznie maskowane podczas drukowania do konsoli lub do dzienników.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Aby można było użyć magazynu kluczy w przepływie pracy, należy wykonać zarówno akcję magazynu kluczy, jak i odwołać się do tej akcji. 

W tym przykładzie Magazyn kluczy ma nazwę `containervault` . Dwa wpisy tajne magazynu kluczy są dodawane do środowiska z akcją Magazyn kluczy — `containerPassword` i `containerUsername` . 

Wartości magazynu kluczy są później przywoływane w zadaniu Docker login z prefiksem `steps.myGetSecretAction.outputs` . 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy aplikacja platformy Azure, repozytorium GitHub i Magazyn kluczy nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów dla aplikacji, repozytorium GitHub i magazynu kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Key Vault](../general/overview.md)
