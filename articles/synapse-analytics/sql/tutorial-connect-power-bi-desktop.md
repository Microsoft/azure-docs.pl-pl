---
title: 'Samouczek: Łączenie sql na żądanie (wersja zapoznawcza) z programem Power BI Desktop & tworzenia raportu'
description: W tym samouczku dowiesz się, jak połączyć sql na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics z pulpitem usługi Power BI i utworzyć raport demonstracyjny na podstawie widoku.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0df8ac495b6aca81e46dffc248019483b1c82202
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422452"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Samouczek: Łączenie sql na żądanie (wersja zapoznawcza) z programem Power BI Desktop & tworzenia raportu

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie bazy danych demonstracyjnych
> - Tworzenie widoku używanego dla raportu
> - Łączenie się z programem Power BI Desktop
> - Tworzenie raportu na podstawie widoku

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne jest następujące oprogramowanie:

- Narzędzie do kwerend SQL, takie jak [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)lub SQL Server Management Studio [(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Program Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Wartości dla następujących parametrów:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Używany jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Służy do określania przechowywania używanego w próbkach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Służy do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych, której użyjesz do tworzenia widoków     | Baza danych używana jako punkt wyjścia w przykładach       |

## <a name="1---create-database"></a>1 - Tworzenie bazy danych

Dla środowiska demo, tworzenie własnej bazy danych demo. Ta baza danych służy do wyświetlania metadanych, a nie do przechowywania rzeczywistych danych.

Utwórz bazę danych demonstracyjnych (i w razie potrzeby upuść istniejącą bazę danych), uruchamiając następujący skrypt Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Tworzenie poświadczeń

Poświadczenia jest niezbędne dla usługi SQL na żądanie, aby uzyskać dostęp do plików w magazynie. Utwórz poświadczenia dla konta magazynu, który znajduje się w tym samym regionie co punkt końcowy. Chociaż SQL na żądanie można uzyskać dostęp do kont magazynu z różnych regionów, o magazynu i punktu końcowego w tym samym regionie zapewnia lepszą wydajność.

Utwórz poświadczenie, uruchamiając następujący skrypt Transact-SQL (T-SQL):

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - Przygotuj widok

Utwórz widok na podstawie zewnętrznych danych demonstracyjnych, zobycie usługi Power BI, uruchamiając następujący skrypt Transact-SQL (T-SQL):

Utwórz `usPopulationView` widok wewnątrz `Demo` bazy danych za pomocą następującej kwerendy:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

Dane demonstracyjne zawierają następujące zestawy danych:

Populacja USA według płci i rasy dla każdego hrabstwa USA pochodzą z 2000 i 2010 Decennial Census w formacie parkietu.

| Ścieżka folderu                                                  | Opis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Folder nadrzędny dla danych na koncie magazynu demonstracyjnego               |
| /release/us_population_county/                               | Us population data files in Parquet format, podzielony na partycje według roku przy użyciu schematu partycjonowania Hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 - Tworzenie raportu usługi Power BI

Tworzenie raportu dla programu Power BI Desktop przy użyciu następujących czynności:

1. Otwórz aplikację Power BI Desktop i wybierz pozycję **Pobierz dane**.

   ![Otwórz aplikację klasyczną Power BI i wybierz pozycję Pobierz dane.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Wybierz **usługę Azure** > **Azure SQL Database**. 

   ![Wybierz źródło danych.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Wpisz nazwę serwera, na którym znajduje się baza danych `Demo` w polu **Serwer,** a następnie wpisz nazwę bazy danych. Wybierz opcję **Importuj,** a następnie wybierz przycisk **OK**. 

   ![Wybierz bazę danych w punkcie końcowym.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Wybierz preferowaną metodę uwierzytelniania:

    - Przykład dla AAD 
  
    ![Kliknij pozycję Zaloguj się.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Przykład logowania SQL — wpisz nazwę użytkownika i hasło.

    ![Użyj logowania SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Wybierz widok `usPopulationView`, a następnie wybierz pozycję **Załaduj**. 

   ![Wybierz widok w wybranej bazie danych.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Poczekaj na zakończenie operacji, a następnie pojawi się `There are pending changes in your queries that haven't been applied`wyskakujące okienko z informacją . Wybierz **pozycję Zastosuj zmiany**. 

   ![Kliknij zastosuj zmiany.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Poczekaj, aż okno dialogowe **Zastosuj zmiany kwerendy** zniknie, co może potrwać kilka minut. 

   ![Poczekaj na zakończenie kwerendy.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Po zakończeniu ładowania wybierz następujące kolumny w tej kolejności, aby utworzyć raport:
   - nazwa powiatu
   - Populacji
   - nazwa stanu

   ![Wybierz kolumny interesujące, aby wygenerować raport mapy.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po wykonaniu tego raportu usuń zasoby, wykonując następujące czynności:

1. Usuwanie poświadczeń dla konta magazynu

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Usuwanie widoku

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Upuść bazę danych

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Następne kroki

Przejdź do [plików magazynu kwerendy,](develop-storage-files-overview.md) aby dowiedzieć się, jak wysyłać zapytania do plików magazynu przy użyciu synapse SQL.
