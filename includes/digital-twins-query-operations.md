---
author: baanders
description: plik dołączany dla operacji zapytań usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486661"
---
## <a name="query-language-features"></a>Funkcje języka zapytań

Usługa Azure Digital bliźniaczych reprezentacji zapewnia szeroką gamę możliwości zapytań w odniesieniu do grafu bliźniaczych. Zapytania są opisane przy użyciu składni podobnej do języka SQL, w języku zapytania podobnym do [IoT Hub język zapytań](../articles/iot-hub/iot-hub-devguide-query-language.md) z wieloma porównywalnymi funkcjami.

> [!NOTE]
> Wszystkie operacje zapytań usługi Azure Digital bliźniaczych reprezentacji są zależne od wielkości liter.

Oto operacje dostępne w języku magazynu zapytań usługi Azure Digital bliźniaczych reprezentacji.

Pobierz cyfrowy bliźniaczych reprezentacji...
* Model ( `IS_OF_MODEL` operator using)
* Właściwości (z uwzględnieniem [Właściwości tagu](../articles/digital-twins/how-to-use-tags.md))
* interfejsy
* relationships
  - właściwości relacji

Można bardziej ulepszyć zapytania przy użyciu następujących operacji:
* Pobierz bliźniaczych reprezentacji przez wiele typów relacji ( `JOIN` zapytania). 
  - Istnieją ograniczenia liczby `JOIN` dozwolonych elementów (jeden poziom dla publicznej wersji zapoznawczej).
* Wybierz tylko najtrafniejsze wyniki zapytania ( `Select TOP` operator)
* Użyj funkcji skalarnych: `IS_BOOL` ,,,, `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` ,,, `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Użyj operatorów porównania zapytania: `IN` / `NIN` , `=` ,,, `!=` `<` `>` , `<=` , `>=` .
* Użyj dowolnej kombinacji ( `AND` , `OR` , `NOT` operatora) `IS_OF_MODEL` , funkcji skalarnych i operatorów porównania.
* Użyj kontynuacji: obiekt zapytania jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Możesz pobrać cyfrowy bliźniaczych reprezentacji jedną stronę jednocześnie, dostarczając token kontynuacji podczas kolejnych wywołań interfejsu API.