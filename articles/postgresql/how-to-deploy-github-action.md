---
title: 'Szybki Start: Nawiązywanie połączenia z usługą Azure PostgreSQL za pomocą akcji GitHub'
description: Korzystanie z usługi Azure PostgreSQL w przepływie pracy akcji GitHub
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97365119"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Szybki Start: korzystanie z akcji usługi GitHub w celu nawiązania połączenia z usługą Azure PostgreSQL

<Token>**dotyczy:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL — pojedynczy serwer :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL — elastyczny serwer</Token>

Rozpocznij pracę z [akcjami usługi GitHub](https://docs.github.com/en/actions) za pomocą przepływu pracy, aby wdrożyć aktualizacje bazy danych do [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Repozytorium GitHub z przykładowymi danymi ( `data.sql` ). Jeśli nie masz konta usługi GitHub, [zarejestruj się bezpłatnie](https://github.com/join).
- Serwer Azure Database for PostgreSQL.
    - [Szybki start: tworzenie serwera usługi Azure Database for PostgreSQL w witrynie Azure Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy akcji usługi GitHub jest definiowany przy użyciu pliku YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdróż bazę danych. |

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

Zastąp symbole zastępcze `server-name` nazwą serwera PostgreSQL hostowanego na platformie Azure. Zastąp wartość `subscription-id` i `resource-group` identyfikatorem subskrypcji i grupą zasobów połączoną z serwerem PostgreSQL.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Dane wyjściowe są obiektem JSON z poświadczeniami przypisania roli, które zapewniają dostęp do bazy danych podobnego do poniższego. Skopiuj ten wyjściowy obiekt JSON do późniejszej wersji.

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

## <a name="copy-the-postgresql-connection-string"></a>Kopiuj parametry połączenia PostgreSQL

W Azure Portal przejdź do serwera Azure Database for PostgreSQL **i Otwórz pozycję**  >  **Parametry połączenia**. Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych dla `your_database` i `your_password` . Parametry połączenia będą wyglądać podobnie do tego.

> [!IMPORTANT]
> - Do użycia na jednym serwerze ```user=adminusername@servername```  . Należy pamiętać, że ```@servername``` jest to wymagane.
> - W przypadku serwera elastycznego Użyj ```user= adminusername``` programu bez  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
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

1. Wklej wartość parametrów połączenia w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_POSTGRESQL_CONNECTION_STRING` .


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

1. Zmień nazwę przepływu pracy `PostgreSQL for GitHub Actions` i Dodaj akcje wyewidencjonowywania i logowania. Te akcje spowodują wyewidencjonowanie kodu lokacji i uwierzytelnienie na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej wpisu tajnego usługi GitHub.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Użyj akcji wdrażania usługi Azure PostgreSQL, aby nawiązać połączenie z wystąpieniem programu PostgreSQL. Zamień `POSTGRESQL_SERVER_NAME` na nazwę serwera. Plik danych PostgreSQL powinien mieć nazwę `data.sql` na poziomie głównym repozytorium.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Ukończ przepływ pracy, dodając akcję wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Przejrzyj wdrożenie

1. Przejdź do **akcji** dla repozytorium GitHub.

1. Otwórz pierwszy wynik, aby zobaczyć szczegółowe dzienniki przebiegu przepływu pracy.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Dziennik uruchamiania akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy baza danych i repozytorium usługi Azure PostgreSQL nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji z platformą Azure i usługą GitHub](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Dowiedz się, jak nawiązać połączenie z serwerem](how-to-connect-query-guide.md)
