---
title: Język zapytań
titleSuffix: Azure Digital Twins
description: Zapoznaj się z podstawą języka zapytań Digital bliźniaczych reprezentacji na platformie Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0faa0af3bb793cbd75139ab42edd0aa7e20de78a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543847"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informacje o języku zapytań dla usługi Azure Digital bliźniaczych reprezentacji

Odwołaj się do tego, że centrum Digital bliźniaczych reprezentacji na platformie Azure jest [**grafem bliźniaczym**](concepts-twins-graph.md), zbudowanym na podstawie **cyfrowych bliźniaczych reprezentacji** i **relacji**. Ten Graf można zbadać, aby uzyskać informacje na temat bliźniaczych reprezentacji cyfrowego i relacji, które zawiera. Te zapytania są zapisywane w niestandardowym języku zapytań przypominającym język SQL o nazwie **język zapytań usługi Azure Digital bliźniaczych reprezentacji**.

Aby przesłać zapytanie do usługi z aplikacji klienckiej, użyj [**interfejsu API zapytań**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)Digital bliźniaczych reprezentacji platformy Azure. Pozwala to deweloperom pisać zapytania i stosować filtry do znajdowania zestawów bliźniaczych reprezentacji cyfrowych w grafie bliźniaczym oraz inne informacje o scenariuszu Digital bliźniaczych reprezentacji platformy Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać zapytania i zapoznać się z przykładami kodu klienta w artykule [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).
