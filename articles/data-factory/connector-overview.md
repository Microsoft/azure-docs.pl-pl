---
title: Omówienie łącznika Azure Data Factory
description: Poznaj obsługiwane łączniki w Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007451"
---
# <a name="azure-data-factory-connector-overview"></a>Omówienie łącznika Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory obsługuje następujące magazyny i formaty danych za pomocą funkcji kopiowania, przepływu danych, wyszukiwania, pobierania metadanych i usuwania działań. Kliknij każdy magazyn danych, aby poznać obsługiwane możliwości i odpowiednie konfiguracje w szczegółach.

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

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
