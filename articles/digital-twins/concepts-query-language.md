---
title: Język zapytań
titleSuffix: Azure Digital Twins
description: Zapoznaj się z podstawą języka magazynu zapytań Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3ed5d6605097b31dfaa58a2d37e71d3a6702bee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537503"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informacje o języku zapytań dla usługi Azure Digital bliźniaczych reprezentacji

Odwołaj się do tego, że centrum Digital bliźniaczych reprezentacji na platformie Azure jest [**grafem bliźniaczym**](concepts-twins-graph.md), zbudowanym na podstawie **cyfrowych bliźniaczych reprezentacji** i **relacji**. Ten Graf można zbadać, aby uzyskać informacje na temat bliźniaczych reprezentacji cyfrowego i relacji, które zawiera. Te zapytania są zapisywane w niestandardowym języku zapytań przypominającym język SQL o nazwie **Język magazynu zapytań usługi Azure Digital bliźniaczych reprezentacji**.

Aby przesłać zapytanie do usługi z aplikacji klienckiej, użyj [**interfejsu API zapytań**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)Digital bliźniaczych reprezentacji platformy Azure. Pozwala to deweloperom pisać zapytania i stosować filtry do znajdowania zestawów bliźniaczych reprezentacji cyfrowych w grafie bliźniaczym oraz inne informacje o scenariuszu Digital bliźniaczych reprezentacji platformy Azure.

## <a name="query-language-features"></a>Funkcje języka zapytań

Usługa Azure Digital bliźniaczych reprezentacji zapewnia szeroką gamę możliwości zapytań w odniesieniu do grafu bliźniaczych. Zapytania są opisane przy użyciu składni podobnej do języka SQL, w języku zapytania podobnym do [IoT Hub język zapytań](../iot-hub/iot-hub-devguide-query-language.md) z wieloma porównywalnymi funkcjami.

> [!NOTE]
> Wszystkie operacje zapytań usługi Azure Digital bliźniaczych reprezentacji są zależne od wielkości liter.

Oto operacje dostępne w języku magazynu zapytań usługi Azure Digital bliźniaczych reprezentacji:
* Pobierz bliźniaczych reprezentacji za pomocą właściwości Digital bliźniaczych reprezentacji '.
* Pobierz bliźniaczych reprezentacji przez interfejsy Digital bliźniaczych reprezentacji '.
* Pobierz bliźniaczych reprezentacji według właściwości relacji.
* Pobierz bliźniaczych reprezentacji przez wiele typów relacji ( `JOIN` zapytania). Istnieją ograniczenia liczby `JOIN` dozwolonych elementów (jeden poziom dla publicznej wersji zapoznawczej).
* Użyj funkcji niestandardowej `IS_OF_MODEL(twinCollection, twinTypeName)` , która umożliwia filtrowanie na podstawie [modelu](concepts-models.md)przędzy. Obsługuje dziedziczenie.
* Użyj funkcji skalarnych: `IS_BOOL` ,,,, `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` ,,, `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` , `ENDS_WITH` .
* Użyj operatorów porównania zapytania: `IN` / `NIN` , `=` ,,, `!=` `<` `>` , `<=` , `>=` .
* Użyj dowolnej kombinacji ( `AND` , `OR` , `NOT` operator) powyższego.
* Użyj kontynuacji: obiekt zapytania jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Możesz pobrać cyfrowy bliźniaczych reprezentacji jedną stronę jednocześnie, dostarczając token kontynuacji podczas kolejnych wywołań interfejsu API.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać zapytania i zapoznać się z przykładami kodu klienta w artykule [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).
