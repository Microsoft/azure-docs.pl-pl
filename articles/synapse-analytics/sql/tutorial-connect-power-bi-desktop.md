---
title: 'Samouczek: łączenie programu SQL na żądanie (wersja zapoznawcza) w celu Power BI Desktop & tworzenia raportu'
description: W tym samouczku dowiesz się, jak połączyć usługę SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics, aby Power BI Desktop i utworzyć raport demonstracyjny na podstawie widoku.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769478"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Samouczek: łączenie programu SQL na żądanie (wersja zapoznawcza) w celu Power BI Desktop & tworzenia raportu

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Utwórz demonstracyjną bazę danych
> - Utwórz widok używany na potrzeby raportu
> - Nawiązywanie połączenia z programem Power BI Desktop
> - Utwórz raport w oparciu o widok

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne jest następujące oprogramowanie:

- Narzędzie zapytania SQL, takie jak [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)lub [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Wartości dla następujących parametrów:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Używane jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Służy do określania magazynu używanego w przykładach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Używane do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych, która będzie używana do tworzenia widoków     | Baza danych używana jako punkt wyjścia w przykładach       |

## <a name="1---create-database"></a>1 — Tworzenie bazy danych

Dla środowiska demonstracyjnego Utwórz własną demonstracyjną bazę danych. Ta baza danych służy do wyświetlania metadanych, a nie do przechowywania rzeczywistych danych.

Utwórz demonstracyjną bazę danych (i w razie potrzeby upuść istniejącą bazę danych), uruchamiając następujący skrypt języka Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 — Tworzenie poświadczeń

Do uzyskiwania dostępu do plików w magazynie jest wymagane poświadczenie usługi SQL na żądanie. Utwórz poświadczenie dla konta magazynu znajdującego się w tym samym regionie, w którym znajduje się punkt końcowy. Mimo że usługa SQL na żądanie może uzyskać dostęp do kont magazynu z różnych regionów, posiadanie magazynu i punktu końcowego w tym samym regionie zapewnia lepszą wydajność.

Utwórz poświadczenie, uruchamiając następujący skrypt języka Transact-SQL (T-SQL):

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

## <a name="3---prepare-view"></a>3 — Przygotuj widok

Utwórz widok na podstawie zewnętrznych danych demonstracyjnych, które Power BI wykorzystać, uruchamiając następujący skrypt języka Transact-SQL (T-SQL):

Utwórz widok `usPopulationView` wewnątrz bazy danych `Demo` przy użyciu następującej kwerendy:

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

Populacja USA według płci i rasę dla każdego z powiatów USA pochodzących z 2000 i 2010 Decennial spisu w formacie Parquet.

| Ścieżka folderu                                                  | Opis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /Release                                                    | Folder nadrzędny dla danych na koncie magazynu demonstracyjnego               |
| /Release/us_population_county/                               | Pliki danych populacji US w formacie Parquet, podzielone na partycje według roku przy użyciu schematu partycjonowania Hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 — Tworzenie raportu Power BI

Utwórz raport dla Power BI Desktop, wykonując następujące czynności:

1. Otwórz aplikację Power BI Desktop i wybierz pozycję **Pobierz dane**.

   ![Otwórz aplikację klasyczną Power BI i wybierz pozycję Pobierz dane.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Wybierz pozycję **Azure** > **Azure SQL Database**. 

   ![Wybierz pozycję źródło danych.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Wpisz nazwę serwera, na którym znajduje się baza danych w polu **serwer** , a następnie wpisz `Demo` nazwę bazy danych. Wybierz opcję **Importuj** , a następnie wybierz przycisk **OK**. 

   ![Wybierz bazę danych w punkcie końcowym.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Wybierz preferowaną metodę uwierzytelniania:

    - Przykład dla usługi AAD 
  
    ![Kliknij pozycję Zaloguj się.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Przykład logowania SQL — wpisz nazwę użytkownika i hasło.

    ![Użyj logowania SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Wybierz widok `usPopulationView`, a następnie wybierz pozycję **Załaduj**. 

   ![Wybierz widok w wybranej bazie danych.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Poczekaj na zakończenie operacji, a następnie zostanie wyświetlone okno podręczne `There are pending changes in your queries that haven't been applied`. Wybierz pozycję **Zastosuj zmiany**. 

   ![Kliknij przycisk Zastosuj zmiany.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Zaczekaj na zniknięcie okna dialogowego **Zastosuj zmiany zapytania** , co może potrwać kilka minut. 

   ![Poczekaj na zakończenie zapytania.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Po zakończeniu ładowania wybierz następujące kolumny w tej kolejności, aby utworzyć raport:
   - Województwo
   - ludzkie
   - stateName

   ![Wybierz kolumny zainteresowania, aby wygenerować raport mapy.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z tego raportu Usuń zasoby z następującymi krokami:

1. Usuń poświadczenie dla konta magazynu

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Usuń widok

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Porzuć bazę danych

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Następne kroki

Przejdź do [plików magazynu zapytań](develop-storage-files-overview.md) , aby dowiedzieć się, jak wykonywać zapytania dotyczące plików magazynu przy użyciu języka SQL Synapse.
