---
title: Kopiowanie danych z tabeli sieci Web przy użyciu usługi Azure Data Factory
description: Dowiedz się więcej o łączniku tabel sieci Web usługi Azure Data Factory, który umożliwia kopiowanie danych z tabeli sieci web do magazynów danych obsługiwanych przez usługę Data Factory jako pochłaniacze.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410214"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli sieci Web przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-web-table-connector.md)
> * [Aktualna wersja](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych tabel sieci Web. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

Różnica między tym łącznikiem tabeli sieci Web, [łącznikiem REST](connector-rest.md) i [łącznikiem HTTP](connector-http.md) to:

- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli ze strony html.
- **Łącznik REST** obsługuje w szczególności kopiowanie danych z interfejsów API RESTful.
- **Łącznik HTTP** jest ogólny, aby pobrać dane z dowolnego punktu końcowego HTTP, na przykład do pobrania pliku. 

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik tabeli sieci Web jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane można kopiować z bazy danych tabel sieci Web do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności ten łącznik tabeli sieci Web obsługuje **wyodrębnianie zawartości tabeli ze strony HTML**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli sieci Web, należy skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika tabeli sieci Web.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej tabeli sieci Web:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Sieć Web** |Tak |
| url | Adres URL do źródła sieci Web |Tak |
| authenticationType | Dozwoloną wartością jest: **Anonimowy**. |Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli sieci Web.

Aby skopiować dane z tabeli sieci Web, ustaw właściwość typu zestawu danych na **WebTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **WebTable** | Tak |
| ścieżka |Względny adres URL do zasobu zawierającego tabelę. |Nie. Jeśli ścieżka nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Aby uzyskać indeks tabeli w sekcji strony HTML, zobacz [Uzyskiwanie indeksu tabeli](#get-index-of-a-table-in-an-html-page) w sekcji html. |Tak |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli sieci Web.

### <a name="web-table-as-source"></a>Tabela sieci Web jako źródło

Aby skopiować dane z tabeli sieci Web, należy ustawić typ źródła w działaniu kopiowania na **WebSource**, żadne dodatkowe właściwości nie są obsługiwane.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobierz indeks tabeli na stronie HTML

Aby uzyskać indeks tabeli, którą należy skonfigurować we [właściwościach zestawu danych,](#dataset-properties)można użyć np.

1. Uruchom **program Excel 2016** i przełącz się na kartę **Dane.**
2. Kliknij **pozycję Nowa kwerenda** na pasku narzędzi, wskaż **pozycję Z innych źródeł** i kliknij pozycję Z **sieci Web**.

    ![Menu dodatku Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. W oknie dialogowym **Z sieci Web** wprowadź adres **URL,** który https://en.wikipedia.org/wiki/) będzie używany w połączonym usłudze JSON (na przykład: wraz ze ścieżką określoną dla zestawu danych (na przykład: AFI%27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![Z okna dialogowego Sieci Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adres URL użyty w tym przykładzie:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz okno dialogowe **Zawartość sieci Web programu Access,** wybierz odpowiedni adres **URL**, **uwierzytelnianie**i kliknij pozycję **Połącz**.

   ![Okno dialogowe Zawartość sieci Web programu Access](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknij element **tabeli** w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** u dołu.  

   ![Okno dialogowe Nawigator](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. W oknie **Edytor zapytań** kliknij przycisk **Edytor zaawansowany** na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Edytor zaawansowany jest numerem obok pozycji "Źródło".

    ![Zaawansowany edytor - Indeks](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, użyj [programu Microsoft Power Query dla programu Excel,](https://www.microsoft.com/download/details.aspx?id=39379) aby uzyskać indeks. Szczegółowe informacje można znaleźć w artykule [Połącz ze stroną sieci Web.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Kroki są podobne w przypadku korzystania z usługi [Microsoft Power BI dla komputerów stacjonarnych](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
