---
title: 'Szybki start: nawiązywanie połączenia z usługą Azure MySQL przy użyciu GitHub Actions'
description: Korzystanie z usługi Azure MySQL z przepływu GitHub Actions przepływu pracy
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 807fdbb1844eb6f89f71e639537a65baf8c76ad5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761767"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Szybki start: nawiązywanie połączenia GitHub Actions z usługą Azure MySQL przy użyciu usługi Azure GitHub Actions

**DOTYCZY:** :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL - Single Server :::image type="icon" source="./media/applies-to/yes.png" border="false"::: Azure Database for MySQL - Flexible Server

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions) przy użyciu przepływu pracy w celu wdrażania aktualizacji bazy danych [w Azure Database for MySQL](https://azure.microsoft.com/services/mysql/).


## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy: 
- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Repozytorium GitHub z przykładami danych ( `data.sql` ). Jeśli nie masz konta usługi GitHub, zarejestruj [się bezpłatnie.](https://github.com/join)  
- Serwer Azure Database for MySQL serwera.
    - [Szybki start: tworzenie Azure Database for MySQL serwera w Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ GitHub Actions jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik ma dwie sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Definiowanie jednostki usługi. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Wdrażanie** | 1. Wdrażanie bazy danych. |

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Jednostkę usługi można utworzyć [za](../active-directory/develop/app-objects-and-service-principals.md) pomocą polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) w interfejsie wiersza polecenia [platformy Azure.](/cli/azure/) Uruchom to [polecenie, Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

Zastąp symbole zastępcze `server-name` nazwą serwera MySQL hostowanej na platformie Azure. Zastąp i `subscription-id` `resource-group` identyfikatorem subskrypcji i grupą zasobów połączeni z serwerem MySQL.  

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

## <a name="copy-the-mysql-connection-string"></a>Kopiowanie parametrów połączenia mySQL 

W Azure Portal przejdź do serwera Azure Database for MySQL i otwórz **ustawienia**  >  **Parametry połączenia**. Skopiuj parametry połączenia **ADO.NET**. Zastąp wartości symboli zastępczych `your_database` dla i `your_password` . Ciąg połączenia będzie wyglądać podobnie do tego. 

> [!IMPORTANT]
> - W przypadku pojedynczego serwera użyj **Uid= adminusername@servername**. Zwróć **@servername** uwagę, że jest to wymagane.
> - W przypadku serwera elastycznego użyj **wartości Uid= adminusername bez** @servername parametru . Pamiętaj, że serwer elastyczny MySQL jest w wersji zapoznawczej. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
Użyjemy parametrów połączenia jako tajnego repozytorium GitHub. 

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium.

1. Wybierz **pozycję Ustawienia > wpisy tajne > nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

    Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego identyfikatora wejściowego dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

    ```yaml
    - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Wybierz **ponownie pozycję Nowy klucz tajny.** 

1. Wklej wartość parametrów połączenia w polu wartości tajnego. Nadaj kluczowi tajnego nazwę `AZURE_MYSQL_CONNECTION_STRING` .


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

1. Zmień nazwę `MySQL for GitHub Actions` przepływu pracy i dodaj akcje wyewidencjonowania i logowania. Te akcje wyewidencjonuje kod witryny i uwierzytelnią się na platformie Azure przy użyciu `AZURE_CREDENTIALS` utworzonego wcześniej tajnego kodu usługi GitHub. 

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

1. Użyj akcji Wdrażanie usługi Azure MySQL, aby nawiązać połączenie z wystąpieniem programu MySQL. Zastąp `MYSQL_SERVER_NAME` nazwą serwera. Plik danych MySQL powinien mieć nazwę `data.sql` na poziomie głównym repozytorium. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Ukończ przepływ pracy, dodając akcję w celu wylogowania się z platformy Azure. Oto ukończony przepływ pracy. Plik zostanie wyświetlony w `.github/workflows` folderze repozytorium.

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

## <a name="review-your-deployment"></a>Przeglądanie wdrożenia

1. Przejdź do **akcji** repozytorium GitHub. 

1. Otwórz pierwszy wynik, aby wyświetlić szczegółowe dzienniki przebiegów przepływu pracy. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Dziennik uruchamianych akcji usługi GitHub":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy baza danych i repozytorium Usługi Azure MySQL nie są już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji platformy Azure i usługi GitHub](/azure/developer/github/)
