---
title: 'Szybki Start: łączenie się z Azure SQL Database przy użyciu akcji GitHub'
description: Korzystanie z usługi Azure SQL w ramach przepływu pracy akcji GitHub
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 335879af93834665985fe2c14ce3cbd827387920
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172143"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>Używanie akcji usługi GitHub do nawiązywania połączenia z usługą Azure SQL Database

Rozpocznij pracę z [akcjami usługi GitHub](https://docs.github.com/en/actions) za pomocą przepływu pracy, aby wdrożyć aktualizacje bazy danych do [Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy: 
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Repozytorium GitHub z pakietem dacpac ( `Database.dacpac` ). Jeśli nie masz konta usługi GitHub, [zarejestruj się bezpłatnie](https://github.com/join).  
- Azure SQL Database.
    - [Szybki Start: Tworzenie Azure SQL Database pojedynczej bazy danych](single-database-create-quickstart.md)
    - [Jak utworzyć pakiet DACPAC z istniejącej bazy danych SQL Server](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy akcji usługi GitHub jest definiowany przy użyciu pliku YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdróż bazę danych. |

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) można utworzyć jednostkę [usługi](../../active-directory/develop/app-objects-and-service-principals.md) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

Zastąp symbole zastępcze `server-name` nazwą programu SQL Server hostowanego na platformie Azure. Zastąp wartość `subscription-id` i `resource-group` identyfikatorem subskrypcji i grupą zasobów połączoną z serwerem SQL.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Dane wyjściowe są obiektem JSON z poświadczeniami przypisania roli, które zapewniają dostęp do bazy danych podobnego do tego przykładu. Skopiuj wyjściowy obiekt JSON do późniejszej wersji.

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
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do określonego serwera, a nie całej grupy zasobów.

## <a name="copy-the-sql-connection-string"></a>Kopiuj parametry połączenia SQL 

W Azure Portal przejdź do Azure SQL Database i Otwórz **Ustawienia**  >  **Parametry połączenia**. Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych dla `your_database` i `your_password` . Parametry połączenia będą wyglądać podobnie do tych danych wyjściowych. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Parametry połączenia będą używane jako wpis tajny usługi GitHub. 

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium.

1. Wybierz pozycję **ustawienia > wpisy tajne > nowe hasło**.

1. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_CREDENTIALS` .

    Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Ponownie wybierz pozycję **Nowy wpis tajny** . 

1. Wklej wartość parametrów połączenia w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_SQL_CONNECTION_STRING` .


## <a name="add-your-workflow"></a>Dodawanie przepływu pracy

1. Przejdź do **akcji** dla repozytorium GitHub. 

2. Wybierz **samodzielnie Skonfiguruj swój przepływ pracy**. 

2. Usuń wszystko po `on:` sekcji pliku przepływu pracy. Na przykład pozostały przepływ pracy może wyglądać następująco. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Zmień nazwę przepływu pracy `SQL for GitHub Actions` i Dodaj akcje wyewidencjonowywania i logowania. Te akcje spowodują wyewidencjonowanie kodu lokacji i uwierzytelnienie na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej wpisu tajnego usługi GitHub. 

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

1. Użyj akcji Wdróż SQL platformy Azure, aby nawiązać połączenie z wystąpieniem programu SQL. Zamień `SQL_SERVER_NAME` na nazwę serwera. Na poziomie głównym repozytorium powinien znajdować się pakiet dacpac ( `Database.dacpac` ). 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Ukończ przepływ pracy, dodając akcję wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

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

## <a name="review-your-deployment"></a>Przejrzyj wdrożenie

1. Przejdź do **akcji** dla repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby zobaczyć szczegółowe dzienniki przebiegu przepływu pracy. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Dziennik uruchamiania akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy usługa Azure SQL Database i repozytorium nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji z platformą Azure i usługą GitHub](/azure/developer/github/)