---
title: 'Szybki Start: Nawiązywanie połączenia z usługą Azure MySQL przy użyciu akcji GitHub'
description: Korzystanie z usługi Azure MySQL z przepływu pracy akcji GitHub
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 7b4620c739b2f94cb6b96743280cd1decbbb746e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326191"
---
# <a name="use-github-actions-to-connect-to-azure-mysql"></a>Łączenie się z usługą Azure MySQL za pomocą akcji usługi GitHub

Rozpocznij pracę z [akcjami usługi GitHub](https://docs.github.com/en/actions) za pomocą przepływu pracy, aby wdrożyć aktualizacje bazy danych do [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/). 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy: 
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Repozytorium GitHub z przykładowymi danymi ( `data.sql` ). Jeśli nie masz konta usługi GitHub, [zarejestruj się bezpłatnie](https://github.com/join).  
- Serwer Azure Database for MySQL.
    - [Szybki Start: Tworzenie serwera Azure Database for MySQL w Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy akcji usługi GitHub jest definiowany przy użyciu pliku YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdróż bazę danych. |

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

Zastąp symbole zastępcze `server-name` nazwą serwera MySQL hostowanego na platformie Azure. Zastąp wartość `subscription-id` i `resource-group` identyfikatorem subskrypcji i grupą zasobów połączoną z serwerem MySQL.  

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

## <a name="copy-the-mysql-connection-string"></a>Kopiuj parametry połączenia MySQL 

W Azure Portal przejdź do serwera Azure Database for MySQL **i Otwórz pozycję**  >  **Parametry połączenia**. Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych dla `your_database` i `your_password` . Parametry połączenia będą wyglądać podobnie do tego. 

```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
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

1. Wklej wartość parametrów połączenia w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_MYSQL_CONNECTION_STRING` .


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

1. Zmień nazwę przepływu pracy `MySQL for GitHub Actions` i Dodaj akcje wyewidencjonowywania i logowania. Te akcje spowodują wyewidencjonowanie kodu lokacji i uwierzytelnienie na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej wpisu tajnego usługi GitHub. 

    ```yaml
    name: MySQL for GitHub Actions

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

1. Użyj akcji Wdróż aplikację Azure MySQL, aby nawiązać połączenie z wystąpieniem programu MySQL. Zamień `MYSQL_SERVER_NAME` na nazwę serwera. Plik danych MySQL powinien mieć nazwę `data.sql` na poziomie głównym repozytorium. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Ukończ przepływ pracy, dodając akcję wylogowania z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

    ```yaml
   name: MySQL for GitHub Actions

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

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Przejrzyj wdrożenie

1. Przejdź do **akcji** dla repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby zobaczyć szczegółowe dzienniki przebiegu przepływu pracy. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Dziennik uruchamiania akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy baza danych i repozytorium usługi Azure MySQL nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji z platformą Azure i usługą GitHub](https://docs.microsoft.com/azure/developer/github/)
