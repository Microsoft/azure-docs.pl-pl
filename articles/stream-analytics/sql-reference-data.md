---
title: Używanie SQL Database danych referencyjnych w zadaniu Azure Stream Analytics
description: W tym artykule opisano sposób użycia SQL Database jako referencyjne dane wejściowe dla zadania Azure Stream Analytics w Azure Portal i w programie Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 7e2826221bd9d15472467c4dd8676d3d0538e0d6
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326577"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Korzystanie z danych referencyjnych z SQL Database dla zadania Azure Stream Analytics

Azure Stream Analytics obsługuje Azure SQL Database jako źródło danych wejściowych w celu uzyskania informacji referencyjnych. SQL Database jako dane referencyjne dla zadania Stream Analytics można użyć w Azure Portal i w programie Visual Studio przy użyciu narzędzi Stream Analytics. W tym artykule przedstawiono sposób wykonywania obu tych metod.

## <a name="azure-portal"></a>Azure Portal

Wykonaj następujące kroki, aby dodać Azure SQL Database jako źródłowe źródło danych wejściowych przy użyciu Azure Portal:

### <a name="portal-prerequisites"></a>Wymagania wstępne portalu

1. Tworzenie zadania usługi Stream Analytics

2. Utwórz konto magazynu, które będzie używane przez zadanie Stream Analytics.

3. Utwórz Azure SQL Database z zestawem danych, który ma być używany jako dane referencyjne w ramach zadania Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Zdefiniuj dane referencyjne SQL Database

1. W zadaniu Stream Analytics wybierz pozycję **dane wejściowe** w obszarze **topologia zadania**. Kliknij pozycję **Dodaj dane wejściowe odwołania** i wybierz **SQL Database**.

   ![Dane wejściowe są wybrane w lewym okienku nawigacji. W przypadku danych wejściowych + Dodaj dane wejściowe odwołania jest zaznaczone, aby odsłonić listę rozwijaną, która zawiera wartości BLOB Storage i SQL Database.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Wypełnij Stream Analytics konfiguracjami wejściowymi. Wybierz nazwę bazy danych, nazwę serwera, nazwa użytkownika i hasło. Jeśli chcesz, aby dane wejściowe danych referencyjnych były odświeżane okresowo, wybierz pozycję "włączone", aby określić częstotliwość odświeżania w DD: HH: MM. Jeśli masz duże zestawy danych z krótką częstotliwością odświeżania, możesz użyć [zapytania Delta](sql-reference-data.md#delta-query).

   ![Po wybraniu SQL Database zostanie wyświetlona strona SQL Database nowe dane wejściowe. W lewym okienku istnieje formularz konfiguracji i zapytanie migawki w okienku po prawej stronie.](./media/sql-reference-data/sql-input-config.png)

3. Przetestuj zapytanie migawki w edytorze zapytań SQL. Aby uzyskać więcej informacji, zobacz [Używanie edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Określ konto magazynu w konfiguracji zadania

Przejdź do pozycji **Ustawienia konta magazynu** w obszarze **Konfiguruj** i wybierz pozycję **Dodaj konto magazynu**.

   ![Ustawienia konta magazynu są wybrane w okienku po lewej stronie. W okienku po prawej stronie znajduje się przycisk Dodaj konto magazynu.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Uruchamianie zadania

Po skonfigurowaniu innych wejść, danych wyjściowych i zapytań można uruchomić zadanie Stream Analytics.

## <a name="tools-for-visual-studio"></a>Narzędzia dla programu Visual Studio

Wykonaj następujące kroki, aby dodać Azure SQL Database jako źródłowe źródło danych wejściowych przy użyciu programu Visual Studio:

### <a name="visual-studio-prerequisites"></a>Wymagania wstępne programu Visual Studio

1. [Zainstaluj narzędzia Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Obsługiwane są następujące wersje programu Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2019

2. Zapoznaj się z [narzędziami Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md) — Szybki Start.

3. Tworzenie konta magazynu

### <a name="create-a-sql-database-table"></a>Tworzenie tabeli SQL Database

Użyj SQL Server Management Studio, aby utworzyć tabelę do przechowywania danych referencyjnych. Aby uzyskać szczegółowe informacje, zobacz artykuł [projektowanie pierwszego Azure SQL Database za pomocą programu SSMS](../azure-sql/database/design-first-database-tutorial.md) .

Przykładowa tabela użyta w poniższym przykładzie została utworzona z następującej instrukcji:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Wybierz subskrypcję

1. W programie Visual Studio w menu **Widok** wybierz opcję **Eksplorator serwera**.

2. Kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure** i zaloguj się przy użyciu konta platformy Azure.

### <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. Wybierz pozycję **Plik > Nowy projekt**. 

2. Na liście szablonów po lewej stronie wybierz pozycję **Stream Analytics**, a następnie wybierz pozycję **Azure Stream Analytics Application**. 

3. Wprowadź **nazwę** projektu, **lokalizację** i **nazwę rozwiązania**, a następnie wybierz **przycisk OK**.

   ![Wybrano szablon Stream Analytics, Azure Stream Analytics aplikacja jest zaznaczona, pola Nazwa, lokalizacja i nazwy rozwiązań są wyróżnione.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Zdefiniuj dane referencyjne SQL Database

1. Utwórz nowe dane wejściowe.

   ![W obszarze Dodaj nowy element jest zaznaczone pole wyboru.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kliknij dwukrotnie **Input.js** w **Eksplorator rozwiązań**.

3. Wypełnij **Stream Analytics konfigurację wejściową**. Wybierz nazwę bazy danych, nazwę serwera, typ odświeżania i częstotliwość odświeżania. Określ częstotliwość odświeżania w formacie `DD:HH:MM` .

   ![W konfiguracji danych wejściowych Stream Analytics wartości są wprowadzane lub wybierane z list rozwijanych.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Jeśli wybierzesz opcję "wykonaj tylko raz" lub "wykonaj okresowo", jeden plik SQL CodeBehind o nazwie **[Input alias]. snapshot. SQL** jest generowany w projekcie pod **Input.jsw** węźle plik.

   ![Plik chemikaliów SQL CodeBehind. snapshot. SQL został wyróżniony.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   W przypadku wybrania opcji "Odświeżaj okresowo z różnicą" zostaną wygenerowane dwa pliki CodeBehind języka SQL: **[alias wejściowy]. snapshot. SQL** i **[Input alias]. Delta. SQL**.

   ![Pliki SQL CodeBehinds. Delta. różnicowa. SQL i chemikali. snapshot. SQL są wyróżnione.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Otwórz plik SQL w edytorze i napisz zapytanie SQL.

5. Jeśli używasz programu Visual Studio 2019 i masz zainstalowane SQL Server narzędzia danych, możesz przetestować zapytanie, klikając pozycję **Wykonaj**. Zostanie wyświetlone okno kreatora, które pomoże Ci połączyć się z SQL Database, a wynik zapytania pojawi się w oknie u dołu.

### <a name="specify-storage-account"></a>Określ konto magazynu

Otwórz **JobConfig.jsna** , aby określić konto magazynu do przechowywania migawek odwołań SQL.

   ![Konfiguracja konfiguracji zadania Stream Analytics jest wyświetlana z wartościami domyślnymi. Globalne ustawienia magazynu są wyróżnione.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testowanie lokalnego i wdrażanie na platformie Azure

Przed wdrożeniem zadania na platformie Azure można testować logikę zapytania lokalnie w odniesieniu do danych wejściowych na żywo. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [test danych na żywo lokalnie przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio (wersja zapoznawcza)](stream-analytics-live-data-local-testing.md). Po zakończeniu testowania kliknij przycisk **Prześlij do platformy Azure**. Zapoznaj się z tematem [tworzenie Stream Analytics przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md) — Szybki Start, aby dowiedzieć się, jak uruchomić zadanie.

## <a name="delta-query"></a>Zapytanie różnicowe

W przypadku korzystania z zapytania różnicowego tabele danych czasowych [w Azure SQL Database](../azure-sql/temporal-tables.md) są zalecane.

1. Utwórz tabelę danych czasowych w Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Utwórz zapytanie migawki. 

   Użyj parametru **\@ snapshotTime** , aby nakazać środowisko uruchomieniowe Stream Analytics w celu uzyskania zestawu danych referencyjnych z SQL Database tabeli czasowej prawidłowej w czasie systemowym. Jeśli ten parametr nie jest określony, istnieje ryzyko uzyskania niedokładnego zestawu danych referencyjnych odniesienia z powodu przesunięcia zegara. Poniżej przedstawiono przykładowe zapytanie o pełną migawkę:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Utwórz zapytanie różnicowe. 
   
   To zapytanie pobiera wszystkie wiersze w SQL Database, które zostały wstawione lub usunięte w czasie rozpoczęcia, **\@ deltaStartTime** i **\@ deltaEndTime** czasu zakończenia. Zapytanie Delta musi zwracać te same kolumny co zapytanie migawki, a także **_operację_** kolumny. Ta kolumna określa, czy wiersz został wstawiony, czy usunięty między **\@ deltaStartTime** i **\@ deltaEndTime**. Utworzone wiersze są oflagowane jako **1** , jeśli rekordy zostały wstawione lub **2** , jeśli zostały usunięte. Zapytanie musi również dodać **znak wodny** ze strony SQL Server, aby upewnić się, że wszystkie aktualizacje w okresie Delta zostaną odpowiednio przechwycone. Użycie zapytania Delta bez **znaku wodnego** może spowodować powstanie nieprawidłowego zestawu danych referencyjnych.  

   W przypadku rekordów, które zostały zaktualizowane, tabela danych czasowych wykonuje operacje we/wykorzystaniu operacji wstawiania i usuwania. Środowisko uruchomieniowe Stream Analytics następnie zastosuje wyniki zapytania różnicowego do poprzedniej migawki, aby zapewnić aktualność danych referencyjnych. Poniżej przedstawiono przykładowe zapytanie różnicowe:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Należy pamiętać, że środowisko uruchomieniowe Stream Analytics może okresowo uruchamiać zapytanie migawek oprócz zapytania Delta do przechowywania punktów kontrolnych.

## <a name="test-your-query"></a>Testowanie zapytania
   Ważne jest, aby sprawdzić, czy zapytanie zwraca oczekiwany zestaw danych, który będzie używany przez zadanie Stream Analytics jako dane referencyjne. Aby przetestować zapytanie, przejdź do pozycji dane wejściowe w obszarze Topologia zadania w portalu. Następnie możesz wybrać przykładowe dane dla danych wejściowych odwołania SQL Database. Po udostępnieniu próbki można pobrać plik i sprawdzić, czy zwracane dane są zgodnie z oczekiwaniami. Jeśli chcesz zoptymalizować iteracje do tworzenia i testowania, zaleca się korzystanie z [Stream Analytics narzędzi dla programu Visual Studio](./stream-analytics-tools-for-visual-studio-install.md). Możesz również użyć dowolnego innego narzędzia preferencji, aby najpierw upewnić się, że zapytanie zwróci odpowiednie wyniki z Azure SQL Database, a następnie użyje go w zadaniu Stream Analytics. 

### <a name="test-your-query-with-visual-studio-code"></a>Przetestuj zapytanie przy użyciu Visual Studio Code

   Zainstaluj [Azure Stream Analytics narzędzia](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) i [SQL Server (mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) na Visual Studio Code i skonfiguruj projekt ASA. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie zadania Azure Stream Analytics w Visual Studio Code](./quick-create-visual-studio-code.md) i [samouczku rozszerzenia SQL Server (MSSQL)](/sql/tools/visual-studio-code/sql-server-develop-use-vscode).

1. Skonfiguruj dane wejściowe danych referencyjnych języka SQL.
   
   ![Edytor Visual Studio Code (karta) pokazuje ReferenceSQLDatabase.json.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Wybierz ikonę SQL Server a następnie kliknij pozycję **Dodaj połączenie**.
   
   ![+ Dodaj połączenie pojawia się w lewym okienku i jest wyróżnione.](./media/sql-reference-data/add-sql-connection.png)

3. Wprowadź informacje o połączeniu.
   
   ![Zostaną wyróżnione dwa pola informacji o bazie danych i serwerze.](./media/sql-reference-data/fill-connection-information.png)

4. Kliknij prawym przyciskiem myszy w polu referencyjne SQL, a następnie wybierz polecenie **wykonaj zapytanie**.
   
   ![Polecenie Execute Query zostało wyróżnione w menu kontekstowym.](./media/sql-reference-data/execute-query.png)

5. Wybierz połączenie.
   
   ![W oknie dialogowym zostanie wyświetlony komunikat "Tworzenie profilu połączenia z poniższej listy", a lista zawiera jeden wpis, który jest aktualnie.](./media/sql-reference-data/choose-connection.png)

6. Przejrzyj i sprawdź wynik zapytania.
   
   ![Wyniki wyszukiwania zapytań znajdują się na karcie edytora VS Code.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Często zadawane pytania

**Czy ponosimy dodatkowe koszty przy użyciu danych referencyjnych SQL w Azure Stream Analytics?**

W zadaniu Stream Analytics nie ma dodatkowych [kosztów na jednostkę przesyłania strumieniowego](https://azure.microsoft.com/pricing/details/stream-analytics/) . Jednak zadanie Stream Analytics musi mieć skojarzone konto usługi Azure Storage. Zadanie Stream Analytics wysyła zapytanie do bazy danych SQL (podczas rozpoczęcia i interwału odświeżania zadania) w celu pobrania zestawu dane referencyjne i przechowuje tę migawkę na koncie magazynu. Przechowywanie tych migawek spowoduje naliczenie dodatkowych opłat szczegółowych na [stronie cennika](https://azure.microsoft.com/pricing/details/storage/) dla konta usługi Azure Storage.

**Jak mogę znane są zapytania o dane referencyjne z bazy danych SQL i używane w zadaniu Azure Stream Analytics?**

Istnieją dwie metryki odfiltrowane według nazwy logicznej (w obszarze metryki Azure Portal), których można użyć do monitorowania kondycji SQL Database danych referencyjnych.

   * InputEvents: Ta Metryka mierzy liczbę rekordów załadowanych z SQL Database zestawie danych referencyjnych.
   * InputEventBytes: Ta Metryka mierzy rozmiar migawki danych referencyjnych załadowanej w pamięci zadania Stream Analytics. 

Połączenie obu tych metryk może służyć do wnioskowania, czy zadanie wykonuje zapytanie SQL Database, aby pobrać zestaw danych referencyjnych, a następnie załadować go do pamięci.

**Czy jest wymagany specjalny typ Azure SQL Database?**

Azure Stream Analytics będzie współpracować z dowolnym typem Azure SQL Database. Należy jednak pamiętać, że częstotliwość odświeżania ustawiona dla danych wejściowych referencyjnych może mieć wpływ na obciążenie zapytań. Aby można było użyć opcji zapytania Delta, zaleca się korzystanie z tabel danych czasowych w Azure SQL Database.

**Dlaczego Azure Stream Analytics są przechowywane migawki na koncie usługi Azure Storage?**

Poza tym gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń. W przypadkach, gdy przejściowe problemy mają wpływ na zadanie, niezbędna jest niewielka ilość powtórzeń w celu przywrócenia stanu. Aby włączyć powtarzanie, musisz mieć te migawki przechowywane na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat powtarzania punktów kontrolnych, zobacz temat Tworzenie [punktów kontrolnych i powtarzanie w zadaniach Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Następne kroki

* [Używanie danych referencyjnych do wyszukiwania w Stream Analytics](stream-analytics-use-reference-data.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu narzędzi usługi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio (wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)