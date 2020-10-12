---
title: Język zapytań
titleSuffix: Azure Digital Twins
description: Zapoznaj się z podstawą języka zapytań Digital bliźniaczych reprezentacji na platformie Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562479"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informacje o języku zapytań dla usługi Azure Digital bliźniaczych reprezentacji

Odwołaj się do tego, że centrum Digital bliźniaczych reprezentacji na platformie Azure jest [**grafem bliźniaczym**](concepts-twins-graph.md), zbudowanym na podstawie **cyfrowych bliźniaczych reprezentacji** i **relacji**. Ten Graf można zbadać, aby uzyskać informacje na temat bliźniaczych reprezentacji cyfrowego i relacji, które zawiera. Te zapytania są zapisywane w niestandardowym języku zapytań przypominającym język SQL, nazywanym **językiem zapytań usługi Azure Digital bliźniaczych reprezentacji**.

Aby przesłać zapytanie do usługi z aplikacji klienckiej, użyj [**interfejsu API zapytań**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)Digital bliźniaczych reprezentacji platformy Azure. Pozwala to deweloperom pisać zapytania i stosować filtry do znajdowania zestawów bliźniaczych reprezentacji cyfrowych w grafie bliźniaczym oraz inne informacje o scenariuszu Digital bliźniaczych reprezentacji platformy Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać zapytania i zapoznać się z przykładami kodu klienta w artykule [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).
