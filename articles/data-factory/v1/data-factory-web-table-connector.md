---
title: Przenoszenie danych z tabeli sieci Web przy użyciu Azure Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z tabeli na stronie sieci Web przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012854"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła tabeli sieci Web przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-web-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-web-table.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik tabeli sieci Web w wersji 2](../connector-web-table.md).

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do przenoszenia danych z tabeli na stronie sieci Web do obsługiwanego magazynu danych ujścia. Ten artykuł zawiera informacje o [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md) , które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania i listę magazynów danych obsługiwanych jako źródła/ujścia.

Fabryka danych obsługuje obecnie tylko przeniesienie danych z tabeli sieci Web do innych magazynów danych, ale nie przenosi danych z innych magazynów danych do lokalizacji docelowej tabeli sieci Web.

> [!IMPORTANT]
> Ten łącznik sieci Web obecnie obsługuje tylko wyodrębnianie zawartości tabeli ze strony HTML. Aby pobrać dane z punktu końcowego HTTP/s, należy zamiast tego użyć [łącznika http](data-factory-http-connector.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli sieci Web, należy skonfigurować samoobsługowy Integration Runtime (alias Zarządzanie danymi Gateway) i skonfigurować `gatewayName` Właściwość w połączonej usłudze ujścia. Aby na przykład skopiować z tabeli sieci Web do usługi Azure Blob Storage, należy skonfigurować połączoną usługę Azure Storage w następujący sposób:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych. 
- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z tabeli sieci Web, zobacz [przykład JSON: kopiowanie danych z tabeli sieci Web do usługi Azure BLOB w](#json-example-copy-data-from-web-table-to-azure-blob) tym artykule. 

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla tabeli sieci Web:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi sieci Web.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **Web** |Tak |
| Url |Adres URL źródła sieci Web |Tak |
| authenticationType |Anonimowe. |Tak |

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties zestawu danych typu **webtable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Typ zestawu danych. musi być ustawiona na **Webtable** |Tak |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, zostanie użyta tylko adres URL określony w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz sekcję [pobieranie indeksu tabeli w sekcji strony HTML,](#get-index-of-a-table-in-an-html-page) aby poznać procedurę pobierania indeksu tabeli na stronie HTML. |Tak |

**Przykład:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Obecnie, gdy źródło w działaniu kopiowania jest typu **websource**, nie są obsługiwane żadne dodatkowe właściwości.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Przykład JSON: kopiowanie danych z tabeli sieci Web do obiektu blob platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [Web](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [webtable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [websource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowo kopiuje dane z tabeli sieci Web do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Poniższy przykład pokazuje, jak skopiować dane z tabeli sieci Web do obiektu blob platformy Azure. Dane można jednak skopiować bezpośrednio do dowolnego ujścia określonego w artykule [działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md) za pomocą działania kopiowania w Azure Data Factory.

**Połączona usługa sieci Web** Ten przykład używa usługi połączonej z siecią Web z uwierzytelnianiem anonimowym. Zobacz sekcję [połączonej usługi sieci Web](#linked-service-properties) , aby poznać różne typy uwierzytelniania, których można użyć.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Połączona usługa Azure Storage**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Wejściowy zestaw danych Webtable** Ustawienie od **zewnątrz** do **true** informuje usługę Data Factory, że zestaw danych znajduje się poza fabryką danych i nie jest wytwarzany przez działanie w fabryce danych.

> [!NOTE]
> Zobacz sekcję [pobieranie indeksu tabeli w sekcji strony HTML,](#get-index-of-a-table-in-an-html-page) aby poznać procedurę pobierania indeksu tabeli na stronie HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma ustawioną wartość **websource** , a typ **ujścia** to **wartość blobsink**.

Zobacz właściwości typu websource dla listy właściwości obsługiwanych przez źródło websource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobieranie indeksu tabeli na stronie HTML
1. Uruchom **program Excel 2016** i przejdź do karty **dane** .  
2. Na pasku narzędzi kliknij pozycję **nowe zapytanie** , wskaż polecenie **z innych źródeł** i kliknij opcję **z sieci Web**.

    ![Menu Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. W oknie dialogowym **z sieci Web** wprowadź **adres URL** , który będzie używany w formacie JSON połączonej usługi (na przykład: https://en.wikipedia.org/wiki/) wraz z ścieżką, którą określisz dla zestawu danych (na przykład: AFI% 27s_100_Years... 100_Movies), a następnie kliknij przycisk **OK**.

    ![Z okna dialogowego sieci Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli zobaczysz okno dialogowe **dostęp do zawartości sieci Web** , wybierz prawidłowy **adres URL**, **uwierzytelnianie** i kliknij przycisk **Połącz**.

   ![Okno dialogowe dostęp do zawartości sieci Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknij element **tabeli** w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** u dołu.  

   ![Okno dialogowe Nawigator](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. W oknie **Edytor zapytań** kliknij przycisk **Edytor zaawansowany** na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Edytor zaawansowany liczba obok pozycji "Źródło" jest indeksem.

    ![Edytor zaawansowany — indeks](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, użyj [Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) , aby pobrać indeks. Aby uzyskać szczegółowe informacje, zobacz artykuł [nawiązywanie połączenia ze stroną sieci Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Kroki są podobne, jeśli używasz [programu Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
