---
title: Omówienie łącznika Azure Data Factory
description: Poznaj obsługiwane łączniki w Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: jingwang
ms.openlocfilehash: cfd3376174ec0f7789389988245f7377b9896a00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015928"
---
# <a name="azure-data-factory-connector-overview"></a>Omówienie łącznika Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory obsługuje następujące magazyny danych i formaty za pośrednictwem funkcji kopiowania, przepływu danych, wyszukiwania, pobierania metadanych i usuwania działań. Kliknij każdy magazyn danych, aby poznać obsługiwane możliwości i odpowiednie konfiguracje w szczegółach.

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integracja z większą liczbą magazynów danych

Azure Data Factory może dotrzeć do szerszego zestawu magazynów danych niż lista wymieniona powyżej. Jeśli musisz przenieść dane do/z magazynu danych, który nie znajduje się na liście wbudowanych łączników Azure Data Factory, poniżej przedstawiono kilka rozszerzalnych opcji:
- W przypadku bazy danych i magazynu danych zazwyczaj można znaleźć odpowiedni sterownik ODBC, za pomocą którego można użyć [generycznego łącznika ODBC](connector-odbc.md).
- Dla aplikacji SaaS:
    - Jeśli udostępnia interfejsy API RESTful, można użyć [ogólnego łącznika REST](connector-rest.md).
    - Jeśli jest to źródło danych OData, można użyć [ogólnego łącznika OData](connector-odata.md).
    - Jeśli udostępnia interfejsy API protokołu SOAP, można użyć [ogólnego łącznika http](connector-http.md).
    - Jeśli ma sterownik ODBC, można użyć [uniwersalnego łącznika ODBC](connector-odbc.md).
- W przypadku innych funkcji Sprawdź, czy możesz ładować dane lub udostępniać je w taki sam sposób, jak w przypadku magazynów danych obsługiwanych przez usługę ADF, np. Azure Blob/File/FTP/SFTP/itp. Mechanizm niestandardowego ładowania danych można wywołać za pomocą [funkcji platformy Azure](control-flow-azure-function-activity.md), [działania niestandardowego](transform-data-using-dotnet-custom-activity.md) [, usługi](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md)datahook, [działania sieci Web](control-flow-web-activity.md)itd.

## <a name="supported-file-formats"></a>Obsługiwane formaty plików

Azure Data Factory obsługuje następujące formaty plików. Zapoznaj się z każdym artykułem dotyczącym ustawień opartych na formacie.

- [Format Avro](format-avro.md)
- [Format binarny](format-binary.md)
- [Format Common Data Model](format-common-data-model.md)
- [Format tekstu rozdzielanego](format-delimited-text.md)
- [Format Delta](format-delta.md)
- [Format programu Excel](format-excel.md)
- [Format JSON](format-json.md)
- [Format ORC](format-orc.md)
- [Format Parquet](format-parquet.md)
- [Format XML](format-xml.md)

## <a name="next-steps"></a>Następne kroki

- [Działanie kopiowania](copy-activity-overview.md)
- [Przepływ mapowania danych](concepts-data-flow-overview.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)
