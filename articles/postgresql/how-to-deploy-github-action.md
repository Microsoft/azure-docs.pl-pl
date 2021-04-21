---
title: 'Szybki start: nawiązywanie połączenia z usługą Azure PostgreSQL przy użyciu GitHub Actions'
description: Korzystanie z usługi Azure PostgreSQL z przepływu GitHub Actions przepływu pracy
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 7fc59c0d9036a2e83c742f51fc17750d40e057fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791431"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Szybki start: nawiązywanie połączenia GitHub Actions z usługą Azure PostgreSQL przy użyciu usługi Azure PostgreSQL

<Token>**DOTYCZY:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for PostgreSQL — pojedynczy serwer :::image type="icon" source="./media/applies-to/yes.png" border="false"::: — Azure Database for PostgreSQL serwer elastyczny</Token>

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions) przy użyciu przepływu pracy w celu wdrażania aktualizacji bazy danych [w Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Repozytorium GitHub z przykładami danych ( `data.sql` ). Jeśli nie masz konta usługi GitHub, zarejestruj [się bezpłatnie.](https://github.com/join)
- Serwer Azure Database for PostgreSQL serwera.
    - [Szybki start: tworzenie serwera usługi Azure Database for PostgreSQL w witrynie Azure Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ GitHub Actions jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Definiowanie jednostki usługi. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdrażanie bazy danych. |

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Jednostkę usługi można utworzyć [za](../active-directory/develop/app-objects-and-service-principals.md) pomocą polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) w interfejsie wiersza polecenia [platformy Azure.](/cli/azure/) Uruchom to polecenie, [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

Zastąp symbole zastępcze `server-name` nazwą serwera PostgreSQL hostowanej na platformie Azure. Zastąp i `subscription-id` `resource-group` identyfikatorem subskrypcji i grupą zasobów połączeni z serwerem PostgreSQL.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do bazy danych, podobnie jak poniżej. Skopiuj ten wyjściowy obiekt JSON na później.

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
> Zawsze dobrym rozwiązaniem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do określonego serwera, a nie do całej grupy zasobów.

## <a name="copy-the-postgresql-connection-string"></a>Kopiowanie parametrów połączenia postgreSQL

W Azure Portal przejdź do serwera Azure Database for PostgreSQL i otwórz **pozycję Parametry** połączenia  >  **ustawień**. Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych `your_database` dla i `your_password` . Ciąg połączenia będzie wyglądać podobnie do tego.

> [!IMPORTANT]
> - W przypadku pojedynczego serwera użyj . ```user=adminusername@servername``` Zwróć ```@servername``` uwagę, że jest to wymagane.
> - W przypadku serwera elastycznego użyj ```user= adminusername``` opcji bez  ```@servername``` .

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

Użyjemy parametrów połączenia jako tajnego repozytorium GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium.

1. Wybierz **pozycję Ustawienia > wpisy tajne > nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

    Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego hasła dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Wybierz **ponownie pozycję Nowy klucz tajny.**

1. Wklej wartość parametrów połączenia w polu wartości tajnego. Nadaj kluczowi tajnego nazwę `AZURE_POSTGRESQL_CONNECTION_STRING` .


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

1. Zmień nazwę `PostgreSQL for GitHub Actions` przepływu pracy i dodaj akcje wyewidencjonowania i logowania. Te akcje wyewidencjonowają kod witryny i uwierzytelnią się na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej tajnego kodu usługi GitHub.

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

2. Użyj akcji Wdrażanie usługi Azure PostgreSQL, aby nawiązać połączenie z wystąpieniem postgreSQL. Zastąp `POSTGRESQL_SERVER_NAME` nazwą serwera. Plik danych PostgreSQL powinien mieć nazwę `data.sql` na poziomie głównym repozytorium.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Ukończ przepływ pracy, dodając akcję w celu wylogowania się z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

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

## <a name="review-your-deployment"></a>Przeglądanie wdrożenia

1. Przejdź do **akcji** repozytorium GitHub.

1. Otwórz pierwszy wynik, aby wyświetlić szczegółowe dzienniki przebiegów przepływu pracy.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Uruchamianie dziennika akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy baza danych i repozytorium azure PostgreSQL nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji platformy Azure i usługi GitHub](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Dowiedz się, jak nawiązać połączenie z serwerem](how-to-connect-query-guide.md)
