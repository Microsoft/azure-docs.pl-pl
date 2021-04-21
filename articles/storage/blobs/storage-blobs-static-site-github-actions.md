---
title: Korzystanie z funkcji GitHub Actions w celu wdrożenia witryny statycznej w usłudze Azure Storage
description: Hostowanie statycznej witryny internetowej usługi Azure Storage za pomocą GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ae0904eda2608026ad09ba8b8993008380725f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788533"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Konfigurowanie przepływu pracy GitHub Actions w celu wdrożenia statycznej witryny internetowej w usłudze Azure Storage

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions) przy użyciu przepływu pracy w celu wdrożenia statycznej witryny na koncie usługi Azure Storage. Po skonfigurowaniu przepływu pracy usługi GitHub Actions będzie można automatycznie wdrożyć witrynę na platformie Azure z repozytorium GitHub po wymusiniu zmian w kodzie witryny.

> [!NOTE]
> Jeśli używasz usługi [Azure Static Web Apps](../../static-web-apps/index.yml), nie musisz ręcznie samodzielnie skonfigurować przepływu GitHub Actions przepływu pracy.
> Azure Static Web Apps automatycznie tworzy przepływ GitHub Actions automatycznie. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure i konto usługi GitHub. 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Repozytorium GitHub ze statycznym kodem witryny internetowej. Jeśli nie masz konta usługi GitHub, zarejestruj [się bezpłatnie.](https://github.com/join)  
- Robocza statyczna witryna internetowa hostowana w usłudze Azure Storage. Dowiedz się, jak [hostować statyczną witrynę internetową w usłudze Azure Storage.](storage-blob-static-website-how-to.md) Aby postępować zgodnie z tym przykładem, należy również [wdrożyć](static-website-content-delivery-network.md)Azure CDN .

> [!NOTE]
> Często używa się sieci dostarczania zawartości (CDN), aby zmniejszyć opóźnienie dla użytkowników na całym świecie i zmniejszyć liczbę transakcji na koncie magazynu. Wdrażanie zawartości statycznej w usłudze magazynu w chmurze może zmniejszyć potrzebę potencjalnie kosztownego wystąpienia obliczeniowego. Aby uzyskać więcej informacji, zobacz [Wzorzec hostowania zawartości statycznej.](/azure/architecture/patterns/static-content-hosting)

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Jednostkę usługi można utworzyć [za](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) Uruchom to [polecenie, Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

Zastąp symbol zastępczy `myStaticSite` nazwą witryny hostowanej w usłudze Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

W powyższym przykładzie zastąp symbole zastępcze identyfikatorem subskrypcji i nazwą grupy zasobów. Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do konta magazynu, podobnie jak poniżej. Skopiuj ten obiekt JSON na później.

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
> Zawsze dobrym rozwiązaniem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do określonej App Service, a nie całej grupy zasobów.

## <a name="configure-the-github-secret"></a>Konfigurowanie tajnego repozytorium GitHub

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium.

1. Wybierz **pozycję Ustawienia > wpisy tajne > nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości w kluczu tajnym. Nadaj kluczowi tajnego nazwę, na przykład `AZURE_CREDENTIALS` .

    Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego identyfikatora wejściowego dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Dodawanie przepływu pracy

1. Przejdź do **akcji** repozytorium GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Element menu GitHub Actions":::

1. Wybierz **pozycję Skonfiguruj przepływ pracy samodzielnie.** 

1. Usuń wszystko po `on:` sekcji pliku przepływu pracy. Na przykład pozostały przepływ pracy może wyglądać tak. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Zmień nazwę `Blob storage website CI` przepływu pracy i dodaj akcje wyewidencjonowania i logowania. Te akcje wyewidencjonowają kod witryny i uwierzytelnią się na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej tajnego kodu usługi GitHub. 

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

1. Użyj akcji interfejsu wiersza polecenia platformy Azure, aby przekazać kod do magazynu obiektów blob i przeczyścić punkt końcowy usługi CDN. W `az storage blob upload-batch` przypadku elementu zastąp symbol zastępczy nazwą konta magazynu. Skrypt zostanie załadowany do `$web` kontenera. W `az cdn endpoint purge` przypadku elementu zastąp symbole zastępcze nazwą profilu usługi CDN, nazwą punktu końcowego usługi CDN i grupą zasobów.

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
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Ukończ przepływ pracy, dodając akcję w celu wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

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
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Przeglądanie wdrożenia

1. Przejdź do **akcji** repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby wyświetlić szczegółowe dzienniki przebiegów przepływu pracy. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Dziennik uruchamianych akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy statyczna witryna internetowa i repozytorium GitHub nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Static Web Apps](../../static-web-apps/index.yml)
