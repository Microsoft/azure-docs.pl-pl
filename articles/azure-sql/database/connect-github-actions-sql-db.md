---
title: 'Szybki start: nawiązywanie połączenia z Azure SQL Database za pomocą GitHub Actions'
description: Używanie Azure SQL z przepływu pracy GitHub Actions przepływu pracy
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ceb9f0f9ef2a88532d5af16a03fcfd0282da84f8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787291"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Nawiązywanie połączenia GitHub Actions z Azure SQL Database

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions) przy użyciu przepływu pracy w celu wdrożenia aktualizacji bazy danych [w Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy: 
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Repozytorium GitHub z pakietem dacpac ( `Database.dacpac` ). Jeśli nie masz konta usługi GitHub, zarejestruj [się bezpłatnie.](https://github.com/join)  
- Element Azure SQL Database.
    - [Szybki start: tworzenie pojedynczej Azure SQL Database bazy danych](single-database-create-quickstart.md)
    - [Jak utworzyć pakiet dacpac na podstawie istniejącej bazy SQL Server Database](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ GitHub Actions jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik zawiera dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Definiowanie jednostki usługi. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdrażanie bazy danych. |

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Jednostkę usługi można utworzyć [za](../../active-directory/develop/app-objects-and-service-principals.md) pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) Uruchom to polecenie, [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

Zastąp symbole zastępcze `server-name` nazwą serwera SQL hostowanej na platformie Azure. Zastąp i `subscription-id` `resource-group` identyfikatorem subskrypcji i grupą zasobów połączeni z serwerem SQL.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do bazy danych, podobnie jak w tym przykładzie. Skopiuj wyjściowy obiekt JSON na później.

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
> Zawsze dobrym rozwiązaniem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do określonego serwera, a nie całej grupy zasobów.

## <a name="copy-the-sql-connection-string"></a>Kopiowanie parametrów połączenia SQL 

W Azure Portal przejdź do swojego Azure SQL Database i otwórz **pozycję Parametry** połączenia  >  **ustawień.** Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych dla `your_database` i `your_password` . Ciąg połączenia będzie wyglądać podobnie do tych danych wyjściowych. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Użyjesz parametrów połączenia jako tajnego repozytorium GitHub. 

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium.

1. Wybierz **pozycję Ustawienia > wpisy tajne > nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości w kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

    Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego identyfikatora wejściowego dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Wybierz **ponownie pozycję Nowy klucz tajny.** 

1. Wklej wartość parametrów połączenia w polu wartości tajnego. Nadaj kluczowi tajnego nazwę `AZURE_SQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Dodawanie przepływu pracy

1. Przejdź do **akcji** repozytorium GitHub. 

2. Wybierz **pozycję Skonfiguruj przepływ pracy samodzielnie.** 

2. Usuń wszystko po `on:` sekcji pliku przepływu pracy. Na przykład pozostały przepływ pracy może wyglądać tak. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Zmień nazwę `SQL for GitHub Actions` przepływu pracy i dodaj akcje wyewidencjonowania i logowania. Te akcje wyewidencjonowają kod witryny i uwierzytelnią się na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej tajnego kodu usługi GitHub. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Użyj akcji Azure SQL Deploy, aby nawiązać połączenie z wystąpieniem SQL. Zastąp `SQL_SERVER_NAME` nazwą serwera. Pakiet dacpac () powinien być na poziomie głównym `Database.dacpac` repozytorium. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Ukończ przepływ pracy, dodając akcję w celu wylogowania się z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Przeglądanie wdrożenia

1. Przejdź do **akcji** repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby wyświetlić szczegółowe dzienniki przebiegów przepływu pracy. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Uruchamianie dziennika akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy baza Azure SQL i repozytorium nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji platformy Azure i usługi GitHub](/azure/developer/github/)