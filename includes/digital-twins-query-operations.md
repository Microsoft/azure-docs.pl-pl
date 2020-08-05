---
author: baanders
description: plik dołączany dla operacji zapytań usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 82639a19ef728c22a74381d24754992e0f647976
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562478"
---
## <a name="query-language-features"></a>Funkcje języka zapytań

Usługa Azure Digital bliźniaczych reprezentacji zapewnia szeroką gamę możliwości zapytań w odniesieniu do grafu bliźniaczych. Zapytania są opisane przy użyciu składni podobnej do języka SQL, w języku zapytania podobnym do [IoT Hub język zapytań](../articles/iot-hub/iot-hub-devguide-query-language.md) z wieloma porównywalnymi funkcjami.

> [!NOTE]
> Wszystkie operacje zapytań usługi Azure Digital bliźniaczych reprezentacji są zależne od wielkości liter.

Oto operacje dostępne w języku zapytań usługi Azure Digital bliźniaczych reprezentacji.

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