---
title: Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym przy użyciu Azure Cosmos DB, Azure Analysis Services i Power BI
description: Dowiedz się, jak utworzyć pulpit nawigacyjny pogody na żywo w Power BI przy użyciu Azure Cosmos DB i Azure Analysis Services.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 07a3deaf67c4f269b01d62ea25ddb212c1e01f6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657540"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym przy użyciu Azure Cosmos DB i Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano kroki wymagane do utworzenia pulpitu nawigacyjnego pogody na żywo w Power BI przy użyciu Azure Cosmos DB i Azure Analysis Services. Na pulpicie nawigacyjnym Power BI zostaną wyświetlone wykresy pokazujące informacje w czasie rzeczywistym dotyczące temperatury i opadów deszczu w regionie.

## <a name="reporting-scenarios"></a>Scenariusze raportowania

Istnieje wiele sposobów konfigurowania pulpitów nawigacyjnych raportowania dla danych przechowywanych w Azure Cosmos DB. W zależności od wymagań dotyczących nieodświeżoności i wielkości danych w poniższej tabeli opisano konfigurację raportowania dla każdego scenariusza:


|Scenariusz |Konfigurowanie |
|---------|---------|
|1. generowanie raportów ad hoc (bez odświeżania)    |  [Power BI łącznik Azure Cosmos DB z trybem importu](powerbi-visualize.md)       |
|2. generowanie raportów ad hoc przy użyciu okresowego odświeżania   |  [Power BI łącznik Azure Cosmos DB z trybem importu (zaplanowane okresowo odświeżanie)](powerbi-visualize.md)       |
|3. Raportowanie dotyczące dużych zestawów danych (< 10 GB)     |  Power BI łącznik Azure Cosmos DB z odświeżaniem przyrostowym       |
|4. raportowanie czasu rzeczywistego w dużych zestawach danych    |  Power BI łącznik Azure Analysis Services z bezpośrednią kwerendą i Azure Analysis Services (łącznik Azure Cosmos DB)       |
|5. raportowanie danych na żywo za pomocą agregacji     |  [Power BI łącznik Spark z bezpośrednim zapytaniem + Azure Databricks + Cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. raportowanie danych na żywo z agregacjami w dużych zestawach danych   |  Power BI łącznik Azure Analysis Services z bezpośrednim zapytaniem + Azure Analysis Services + Azure Databricks + Cosmos DB Spark Connector.       |

Scenariusze 1 i 2 można łatwo skonfigurować przy użyciu łącznika Power BI Azure Cosmos DB. W tym artykule opisano konfiguracje scenariuszy 3 i 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI z odświeżaniem przyrostowym

Power BI ma tryb, w którym można skonfigurować odświeżanie przyrostowe. Ten tryb eliminuje konieczność tworzenia partycji Azure Analysis Services i zarządzania nimi. Odświeżanie przyrostowe można skonfigurować w celu filtrowania tylko najnowszych aktualizacji w dużych zestawach danych. Jednak ten tryb działa tylko z usługą Power BI Premium, która ma ograniczenie zestawu danych 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI usługi Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services udostępnia w pełni zarządzaną platformę jako usługę, która obsługuje modele danych klasy korporacyjnej w chmurze. Duże zestawy danych mogą być ładowane z Azure Cosmos DB do Azure Analysis Services. Aby uniknąć wykonywania zapytania o cały zestaw danych przez cały czas, zestawy danych mogą być podzielone na partycje Azure Analysis Services, które mogą być odświeżane niezależnie od różnych częstotliwości.

## <a name="power-bi-incremental-refresh"></a>Power BI odświeżanie przyrostowe

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Pozyskiwanie danych pogodowych do Azure Cosmos DB

Skonfiguruj potok pozyskiwania w celu załadowania [danych pogody](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) do Azure Cosmos DB. Można skonfigurować zadanie [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) , aby okresowo ładować najnowsze dane pogodowe do Azure Cosmos dB przy użyciu źródła http i ujścia Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Połącz Power BI z Azure Cosmos DB

1. **Połącz konto usługi Azure Cosmos, aby Power BI** otworzyć Power BI Desktop i użyć łącznika Azure Cosmos DB do wybrania odpowiedniej bazy danych i kontenera.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Łącznik usługi PowerBI w usłudze Cosmos DB":::

1. **Skonfiguruj odświeżanie przyrostowe** — wykonaj kroki opisane w sekcji [odświeżanie przyrostowe w artykule Power BI](/power-bi/service-premium-incremental-refresh) , aby skonfigurować odświeżanie przyrostowe dla zestawu danych. Dodaj parametry **RangeStart** i **RangeEnd** , jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Konfigurowanie parametrów zakresu":::

   Ponieważ zestaw danych ma kolumnę daty, która znajduje się w postaci tekstowej, parametry **RangeStart** i **RangeEnd** powinny być przekształcone w celu użycia następującego filtra. W okienku **Edytor zaawansowany** zmodyfikuj zapytanie Dodaj następujący tekst, aby odfiltrować wiersze na podstawie parametrów RangeStart i RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   W zależności od tego, która kolumna i typ danych znajdują się w źródłowym zestawie danych, można odpowiednio zmienić pola RangeStart i RangeEnd

   
   |Właściwość  |Typ danych  |Filtr  |
   |---------|---------|---------|
   |_ts     |   Numeryczny      |  [_ts] > Duration. TotalSeconds (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) i [_ts] < Duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Data (na przykład:-2019-08-19)     |   Ciąg      | [Document. Date] > DateTime. ToText (RangeStart, "RRRR-MM-DD") i [Document. Date] < DateTime. ToText (RangeEnd, "RRRR-MM-DD")        |
   |Data (na przykład:-2019-08-11 12:00:00)   |  Ciąg       |  [Document. Date] > DateTime. ToText (RangeStart, "RRRR-MM-DD GG: mm: SS") i [Document. Date] < DateTime. ToText (RangeEnd, "RRRR-MM-DD GG: mm: SS")       |


1. **Zdefiniuj zasady odświeżania** — Zdefiniuj zasady odświeżania, przechodząc do karty **odświeżanie przyrostowe** w menu **kontekstowym** dla tabeli. Ustaw zasady odświeżania do odświeżania **codziennie** i przechowuj dane z ostatniego miesiąca.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Zdefiniuj zasady odświeżania":::

   Zignoruj ostrzeżenie informujące o *tym, że zapytanie M nie może zostać potwierdzone jako składane*. Łącznik Azure Cosmos DB składa kwerendy filtru.

1. **Załaduj dane i Wygeneruj raporty** — przy użyciu załadowanych wcześniej danych Utwórz wykresy w celu raportowania temperatury i opadów.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Ładowanie danych i generowanie raportu":::

1. **Publikowanie raportu w celu Power BI wersji Premium** — ponieważ odświeżanie przyrostowe jest funkcją tylko Premium, okno dialogowe publikowania umożliwia tylko wybór obszaru roboczego w pojemności Premium. Pierwsze odświeżanie może trwać dłużej ze względu na konieczność zaimportowania danych historycznych. Kolejne odświeżenia danych są znacznie szybsze, ponieważ korzystają z odświeżania przyrostowego.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI usługi Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Pozyskiwanie danych pogodowych do Azure Cosmos DB 

Skonfiguruj potok pozyskiwania w celu załadowania [danych pogody](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) do Azure Cosmos DB. Można skonfigurować zadanie Azure Data Factory (ADF), aby okresowo ładować najnowsze dane pogodowe do Azure Cosmos DB przy użyciu źródła HTTP i ujścia Cosmos DB.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Połącz Azure Analysis Services z kontem usługi Azure Cosmos

1. **Tworzenie nowego klastra Azure Analysis Services**  -  [Utwórz wystąpienie usług Azure Analysis Services](../analysis-services/analysis-services-create-server.md) w tym samym regionie, w którym znajduje się konto usługi Azure Cosmos i klaster datakostki.

1. **Tworzenie nowego projektu Analysis Services tabelarycznego w programie Visual Studio**  -   [Zainstaluj narzędzia SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true) i Utwórz Analysis Services Projekt tabelaryczny w programie Visual Studio.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Utwórz projekt Azure Analysis Services":::

   Wybierz **zintegrowane wystąpienie obszaru roboczego** i ustaw poziom zgodności na **SQL Server 2017/Azure Analysis Services (1400)**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services projektanta modelu tabelarycznego":::

1. **Dodaj źródło danych Azure Cosmos DB** — umożliwia przejście do **modeli** >  **źródła danych**  >  **nowe źródło danych** i dodanie Azure Cosmos DB źródła danych, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Dodaj źródło danych Cosmos DB":::

   Połącz się z Azure Cosmos DB, podając **Identyfikator URI konta**, **nazwę bazy danych** i **nazwę kontenera**. Teraz można zobaczyć dane z kontenera usługi Azure Cosmos do Power BI.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Podgląd danych Azure Cosmos DB":::

1. **Konstruowanie modelu Analysis Services** — Otwórz Edytor zapytań, wykonaj wymagane operacje, aby zoptymalizować załadowany zestaw danych:

   * Wyodrębnij tylko kolumny dotyczące pogody (temperatury i opadów)

   * Wyodrębnij informacje o miesiącu z tabeli. Te dane są przydatne podczas tworzenia partycji zgodnie z opisem w następnej sekcji.

   * Konwertuj kolumny temperatury na liczbę

   Wyniki wyrażenia M są następujące:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ponadto Zmień typ danych kolumn temperatury na Decimal, aby upewnić się, że te wartości można wykreślić w Power BI.

1. **Tworzenie partycji analizy platformy Azure** — tworzenie partycji w Azure Analysis Services, aby podzielić zestaw danych na partycje logiczne, które mogą być odświeżane niezależnie i przy użyciu różnych częstotliwości. W tym przykładzie utworzysz dwie partycje, które będą podzielić zestaw danych na dane z ostatniego miesiąca i wszystkie inne.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Tworzenie partycji usług Analysis Services":::

   Utwórz następujące dwie partycje w Azure Analysis Services:

   * **Ostatni miesiąc** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Uzyskane** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Wdróż model na platformie Azure Analysis Server** kliknij prawym przyciskiem myszy projekt Azure Analysis Services i wybierz polecenie **Wdróż**. Dodaj nazwę serwera w okienku **Właściwości serwera wdrażania** .

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Wdróż model Azure Analysis Services":::

1. **Skonfiguruj odświeżanie i scalanie partycji** — Azure Analysis Services umożliwia niezależne przetwarzanie partycji. Ponieważ chcemy, aby Najnowsza partycja **miesięczna** była stale aktualizowana o najnowsze dane, ustaw interwał odświeżania na 5 minut. Można odświeżyć dane przy użyciu [interfejsu API REST](../analysis-services/analysis-services-async-refresh.md), usługi [Azure Automation](../analysis-services/analysis-services-refresh-azure-automation.md)lub [aplikacji logiki](../analysis-services/analysis-services-refresh-logic-app.md). Odświeżanie danych w partycji historycznej nie jest wymagane. Ponadto należy napisać kod, aby skonsolidować najnowszą partycję miesiąca do partycji historycznej i utworzyć nową partycję miesiąca.

## <a name="connect-power-bi-to-analysis-services"></a>Połącz Power BI z Analysis Services

1. **Połącz się z usługą Azure Analysis Server przy użyciu łącznika Azure Analysis Services Database** — wybierz **tryb Live** i Połącz się z wystąpieniem Azure Analysis Services, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Pobieranie danych z usługi Azure Analysis Services":::

1. **Załaduj dane i Generuj raporty** — przy użyciu załadowanych wcześniej danych Utwórz wykresy w celu raportowania temperatury i opadów. Ponieważ tworzysz połączenie na żywo, należy wykonać zapytania dotyczące danych w modelu Azure Analysis Services, który został wdrożony w poprzednim kroku. Wykresy temperatury zostaną zaktualizowane w ciągu pięciu minut od momentu załadowania nowych danych do Azure Cosmos DB.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Ładowanie danych i generowanie raportów":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat Power BI, zobacz Rozpoczynanie [pracy z Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Połącz Qlik sens, aby Azure Cosmos DB i wizualizować dane](visualize-qlik-sense.md)
