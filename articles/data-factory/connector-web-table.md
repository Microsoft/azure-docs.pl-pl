---
title: Kopiowanie danych z tabeli sieci Web przy użyciu Azure Data Factory
description: Informacje o łączniku tabeli sieci Web Azure Data Factory, który umożliwia kopiowanie danych z tabeli sieci Web do magazynów danych obsługiwanych przez Data Factory jako ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0eb4d37342685c13027a69bb6cb85f618fa63f20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81410214"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli sieci Web przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-web-table-connector.md)
> * [Bieżąca wersja](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych tabeli sieci Web. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem tabeli sieci Web, [łącznikiem REST](connector-rest.md) i [łącznikiem http](connector-http.md) :

- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli z strony html.
- **Łącznik REST** obsługujący kopiowanie danych z interfejsów API RESTful.
- **Łącznik http** jest ogólny do pobierania danych z dowolnego punktu końcowego http, np. do pobrania pliku. 

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik tabeli sieci Web jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych tabeli sieci Web można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik tabeli sieci Web obsługuje **wyodrębnianie zawartości tabeli ze strony HTML**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli sieci Web, należy skonfigurować samoobsługowy Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika tabeli sieci Web.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi tabeli sieci Web:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Web** |Tak |
| url | Adres URL źródła sieci Web |Tak |
| authenticationType | Dozwolona wartość to: **anonimowe**. |Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli sieci Web.

Aby skopiować dane z tabeli sieci Web, ustaw właściwość Type zestawu danych na **webtable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na: **Webtable** | Tak |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, zostanie użyta tylko adres URL określony w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz sekcję [pobieranie indeksu tabeli w sekcji strony HTML,](#get-index-of-a-table-in-an-html-page) aby poznać procedurę pobierania indeksu tabeli na stronie HTML. |Tak |

**Przykład:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli sieci Web.

### <a name="web-table-as-source"></a>Tabela internetowa jako źródło

Aby skopiować dane z tabeli sieci Web, ustaw typ źródła w działaniu Kopiuj do **websource**, a żadne dodatkowe właściwości nie są obsługiwane.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobieranie indeksu tabeli na stronie HTML

Aby uzyskać indeks tabeli, która jest potrzebna do skonfigurowania we [właściwościach zestawu danych](#dataset-properties), można użyć np. Excel 2016 jako narzędzia w następujący sposób:

1. Uruchom **program Excel 2016** i przejdź do karty **dane** .
2. Na pasku narzędzi kliknij pozycję **nowe zapytanie** , wskaż polecenie **z innych źródeł** i kliknij opcję **z sieci Web**.

    ![Menu Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. W oknie dialogowym **z sieci Web** wprowadź **adres URL** , który będzie używany w formacie JSON połączonej usługi (na przykład: https://en.wikipedia.org/wiki/) wraz z ścieżką, którą określisz dla zestawu danych (na przykład: AFI% 27s_100_Years... 100_Movies), a następnie kliknij przycisk **OK**.

    ![Z okna dialogowego sieci Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli zobaczysz okno dialogowe **dostęp do zawartości sieci Web** , wybierz prawidłowy **adres URL**, **uwierzytelnianie**i kliknij przycisk **Połącz**.

   ![Okno dialogowe dostęp do zawartości sieci Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknij element **tabeli** w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** u dołu.  

   ![Okno dialogowe Nawigator](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. W oknie **Edytor zapytań** kliknij przycisk **Edytor zaawansowany** na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Edytor zaawansowany liczba obok pozycji "Źródło" jest indeksem.

    ![Edytor zaawansowany — indeks](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, użyj [Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) , aby pobrać indeks. Aby uzyskać szczegółowe informacje, zobacz artykuł [nawiązywanie połączenia ze stroną sieci Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Kroki są podobne, jeśli używasz [programu Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
