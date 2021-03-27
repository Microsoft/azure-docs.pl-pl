---
title: Power BI i bezserwerowa Pula SQL do analizowania danych Azure Cosmos DB za pomocą linku Synapse
description: Dowiedz się, jak utworzyć bezserwerową bazę danych puli i widoki za pośrednictwem linku Synapse dla Azure Cosmos DB, wysłać zapytanie do kontenerów Azure Cosmos DB, a następnie skompilować model przy użyciu Power BI w tych widokach.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: d84508a7629481a7138f1080c86f4a203d35894d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626252"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Użyj Power BI i bezserwerowej puli SQL Synapse, aby analizować dane Azure Cosmos DB za pomocą linku Synapse 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

W tym artykule dowiesz się, jak utworzyć bazę danych puli SQL bezserwerowej i widoki dla Azure Cosmos DB za pośrednictwem linku Synapse. Należy wykonać zapytanie dotyczące kontenerów Azure Cosmos DB, a następnie skompilować model z Power BI nad tymi widokami w celu odzwierciedlenia tego zapytania.

W tym scenariuszu będziesz używać fikcyjnych danych dotyczących sprzedaży produktu Surface w sklepie detalicznym partnera. Możesz analizować przychody dla sklepu w oparciu o bliskość do dużych gospodarstw domowych i wpływ anonsowania na określony tydzień. W tym artykule opisano tworzenie dwóch widoków o nazwach **RetailSales** i **StoreDemographics** oraz zapytania między nimi. Dane przykładowego produktu można pobrać z tego repozytorium [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały utworzone następujące zasoby:

* [Utwórz konto Azure Cosmos DB rodzaju SQL (rdzeń) lub MongoDB.](create-cosmosdb-resources-portal.md)

* Włącz link Synapse platformy Azure dla [konta usługi Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Utwórz bazę danych w ramach konta usługi Azure Cosmos i dwóch kontenerów z [włączonym magazynem analitycznym.](configure-synapse-link.md#create-analytical-ttl)

* Załaduj dane produktów do kontenerów usługi Azure Cosmos, zgodnie z opisem w tym notesie pozyskiwania [danych wsadowych](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Utwórz obszar roboczy Synapse](../synapse-analytics/quickstart-create-workspace.md) o nazwie **SynapseLinkBI**.

* [Połącz bazę danych usługi Azure Cosmos z obszarem roboczym Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Tworzenie bazy danych i widoków

W obszarze roboczym Synapse przejdź do karty **programowanie** , wybierz **+** ikonę, a następnie wybierz pozycję **skrypt SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Dodawanie skryptu SQL do obszaru roboczego analizy Synapse":::

Każdy obszar roboczy jest dostarczany z bezserwerowym punktem końcowym SQL. Po utworzeniu skryptu SQL, na pasku narzędzi na górze Połącz z **wbudowaną**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Umożliwienie skryptowi SQL używania punktu końcowego SQL bezserwerowego w obszarze roboczym":::

Tworzenie widoków w bazach danych **Master** lub **default** nie jest zalecane ani obsługiwane. Utwórz nową bazę danych o nazwie **RetailCosmosDB** i Widok SQL dla kontenerów z włączonym linkiem Synapse. Następujące polecenie pokazuje, jak utworzyć bazę danych:

```sql
-- Create database
Create database RetailCosmosDB
```

Następnie Utwórz wiele widoków w ramach różnych linków Synapse z obsługą kontenerów usługi Azure Cosmos. Widoki umożliwiają używanie języka T-SQL do przyłączania i wykonywania zapytań dotyczących danych Azure Cosmos DB znajdujących się w różnych kontenerach.  Upewnij się, że podczas tworzenia widoków została wybrana baza danych **RetailCosmosDB** .

Poniższe skrypty pokazują, jak tworzyć widoki w poszczególnych kontenerach. Dla uproszczenia Użyjmy funkcji [automatycznego wnioskowania schematu](analytical-store-introduction.md#analytical-schema) dla bezserwerowej puli SQL za pośrednictwem kontenerów z włączonym linkiem Synapse:


### <a name="retailsales-view"></a>Widok RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Upewnij się, że wstawisz region Azure Cosmos DB i klucz podstawowy w poprzednim skrypcie SQL. Wszystkie znaki w nazwie regionu powinny być małymi literami bez spacji. W przeciwieństwie do innych parametrów `OPENROWSET` polecenia nazwę kontenera należy określić bez cudzysłowu.

### <a name="storedemographics-view"></a>Widok StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Teraz uruchom skrypt SQL, wybierając polecenie **Uruchom** .

## <a name="query-the-views"></a>Wykonywanie zapytań w widokach

Teraz, gdy dwa widoki są tworzone, zdefiniujmy zapytanie w celu dołączenia do tych dwóch widoków w następujący sposób:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Wybierz pozycję **Uruchom** , która daje w wyniku następującą tabelę:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Wyniki zapytania po dołączeniu do widoków StoreDemographics i RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Widoki modelu za pośrednictwem kontenerów z Power BI

Następnie otwórz Power BI Desktop i Połącz się z punktem końcowym SQL bezserwerowym, wykonując następujące czynności:

1. Otwórz aplikację Power BI Desktop. Wybierz pozycję **Pobierz dane** i wybierz pozycję **więcej**.

1. Wybierz pozycję **Azure Synapse Analytics (SQL DW)** z listy opcje połączenia.

1. Wprowadź nazwę punktu końcowego SQL, w którym znajduje się baza danych. Wprowadź `SynapseLinkBI-ondemand.sql.azuresynapse.net` wartość w polu **serwer** . W tym przykładzie  **SynapseLinkBI** jest nazwą obszaru roboczego. Zastąp ją, jeśli masz inną nazwę w obszarze roboczym. Wybierz opcję **bezpośrednie zapytanie** dla trybu łączności danych, a następnie kliknij przycisk **OK**.

1. Wybierz preferowaną metodę uwierzytelniania, taką jak Azure AD.

1. Wybierz **RetailCosmosDB** bazę danych i widoki **RetailSales**, **StoreDemographics** .

1. Wybierz pozycję **Załaduj** , aby załadować dwa widoki do trybu zapytania bezpośredniego.

1. Wybierz pozycję **model** , aby utworzyć relację między dwoma widokami za pomocą kolumny **StoreID** .

1. Przeciągnij kolumnę **StoreId** z widoku **RetailSales** do kolumny **StoreId** w widoku **StoreDemographics** .

1. Wybierz relację wiele do jednego (*: 1), ponieważ w widoku **RetailSales** istnieje wiele wierszy z tym samym identyfikatorem magazynu. **StoreDemographics** ma tylko jeden wiersz identyfikatora magazynu (jest to tabela wymiarów).

Teraz przejdź do okna **raport** i Utwórz raport, aby porównać względną ważność rozmiaru gospodarstwa domowego z średnim przychodem na sklep w oparciu o rozproszenie reprezentację przychodu i indeksu LargeHH:

1. Wybierz **Wykres punktowy**.

1. Przeciągnij i upuść **LargeHH** z widoku **StoreDemographics** do osi X.

1. Przeciągnij i upuść **przychód** z widoku **RetailSales** do osi Y. Wybierz pozycję **średnia** , aby uzyskać średnią sprzedaż na produkt i na tydzień.

1. Przeciągnij i upuść **productCode** z widoku **RetailSales** w legendzie, aby wybrać konkretny wiersz produktu.
Po wybraniu tych opcji powinien zostać wyświetlony wykres podobny do poniższego zrzutu ekranu:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Raport, który porównuje względną ważność rozmiaru gospodarstwa domowego z średnim przychodem na sklep":::

## <a name="next-steps"></a>Następne kroki

[Używanie języka T-SQL do wykonywania zapytań dotyczących danych Azure Cosmos DB przy użyciu usługi Azure Synapse link](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Używanie bezserwerowej puli SQL do [analizowania otwartych zestawów danych platformy Azure i wizualizacji wyników w usłudze Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
