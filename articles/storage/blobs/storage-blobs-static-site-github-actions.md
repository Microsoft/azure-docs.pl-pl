---
title: Korzystanie z funkcji GitHub Actions w celu wdrożenia witryny statycznej w usłudze Azure Storage
description: Hosting statycznej witryny sieci Web usługi Azure Storage za pomocą akcji GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 919fa0d7b6dff0361e4439b442bcfe9648ed8677
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776395"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Konfigurowanie przepływu pracy akcji usługi GitHub w celu wdrożenia statycznej witryny sieci Web w usłudze Azure Storage

Wprowadzenie do [akcji usługi GitHub](https://docs.github.com/en/actions) za pomocą przepływu pracy do wdrożenia lokacji statycznej w usłudze Azure Storage BLOB. Po skonfigurowaniu przepływu pracy akcji usługi GitHub będzie możliwe automatyczne wdrażanie witryny na platformie Azure z usługi GitHub po wprowadzeniu zmian w kodzie witryny. 

> [!NOTE]
> Jeśli używasz [usługi Azure Static Web Apps](https://docs.microsoft.com/azure/static-web-apps/), nie musisz ręcznie konfigurować przepływu pracy akcji usługi GitHub.
> Usługa Azure static Web Apps automatycznie tworzy przepływ pracy w usłudze GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure i konto usługi GitHub. 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub z kodem statycznej witryny sieci Web. Jeśli nie masz konta usługi GitHub, [zarejestruj się bezpłatnie](https://github.com/join).  
- Działająca statyczna witryna sieci Web hostowana w usłudze Azure Storage. Dowiedz się [, jak hostować statyczną witrynę sieci Web w usłudze Azure Storage](storage-blob-static-website-how-to.md). Statyczna Witryna internetowa powinna zawierać [Azure CDN](static-website-content-delivery-network.md).

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

Zastąp symbol zastępczy `myStaticSite` nazwą swojej witryny hostowanej w usłudze Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

W powyższym przykładzie Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji i grupą zasobów. Dane wyjściowe są obiektem JSON z poświadczeniami przypisywania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższego. Skopiuj ten obiekt JSON do nowszej wersji.

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
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium.

1. Wybierz pozycję **ustawienia > wpisy tajne > nowe hasło**.

1. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj tajną nazwę, taką jak `AZURE_CREDENTIALS` .

    Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Dodawanie przepływu pracy

1. Przejdź do **akcji** dla repozytorium GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Element menu akcji GitHub&quot;:::

1. Wybierz **samodzielnie Skonfiguruj swój przepływ pracy**. 

1. Usuń wszystko po `on:` sekcji pliku przepływu pracy. Na przykład pozostały przepływ pracy może wyglądać następująco. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Zmień nazwę przepływu pracy `Blob storage website CI` i Dodaj akcje wyewidencjonowywania i logowania. Te akcje spowodują wyewidencjonowanie kodu lokacji i uwierzytelnienie na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej wpisu tajnego usługi GitHub. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Użyj akcji interfejsu wiersza polecenia platformy Azure, aby przekazać kod do magazynu obiektów blob i przeczyścić punkt końcowy usługi CDN. W przypadku programu `az storage blob upload-batch` Zastąp symbol zastępczy nazwą konta magazynu. Skrypt zostanie przekazany do `$web` kontenera. W przypadku programu `az cdn endpoint purge` Zastąp symbole zastępcze nazwą profilu CDN, nazwą punktu końcowego usługi CDN i grupą zasobów.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Ukończ przepływ pracy, dodając akcję wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Przejrzyj wdrożenie

1. Przejdź do **akcji** dla repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby zobaczyć szczegółowe dzienniki przebiegu przepływu pracy. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Element menu akcji GitHub&quot;:::

1. Wybierz **samodzielnie Skonfiguruj swój przepływ pracy**. 

1. Usuń wszystko po `on:` sekcji pliku przepływu pracy. Na przykład pozostały przepływ pracy może wyglądać następująco. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Zmień nazwę przepływu pracy `Blob storage website CI` i Dodaj akcje wyewidencjonowywania i logowania. Te akcje spowodują wyewidencjonowanie kodu lokacji i uwierzytelnienie na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej wpisu tajnego usługi GitHub. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Użyj akcji interfejsu wiersza polecenia platformy Azure, aby przekazać kod do magazynu obiektów blob i przeczyścić punkt końcowy usługi CDN. W przypadku programu `az storage blob upload-batch` Zastąp symbol zastępczy nazwą konta magazynu. Skrypt zostanie przekazany do `$web` kontenera. W przypadku programu `az cdn endpoint purge` Zastąp symbole zastępcze nazwą profilu CDN, nazwą punktu końcowego usługi CDN i grupą zasobów.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Ukończ przepływ pracy, dodając akcję wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy witryna i repozytorium statyczne platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usłudze Azure static Web Apps](https://docs.microsoft.com/azure/static-web-apps/)