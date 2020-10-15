---
author: baanders
description: plik dołączany dla operacji zapytań usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078463"
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
  - W trakcie okresu zapoznawczego można używać maksymalnie pięciu poziomów `JOIN` .
* Wybierz tylko najtrafniejsze wyniki zapytania ( `Select TOP` operator)
* Zlicz liczbę elementów w zestawie wyników przy użyciu `Select COUNT`
* Użyj projekcji, aby wybrać kolumny, które będą zwracane przez zapytanie
* Użyj funkcji skalarnych: `IS_BOOL` ,,,, `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` ,,, `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Użyj operatorów porównania zapytania: `IN` / `NIN` , `=` ,,, `!=` `<` `>` , `<=` , `>=` .
* Użyj dowolnej kombinacji ( `AND` , `OR` , `NOT` operatora) `IS_OF_MODEL` , funkcji skalarnych i operatorów porównania.
* Użyj kontynuacji: obiekt zapytania jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Możesz pobrać cyfrowy bliźniaczych reprezentacji jedną stronę jednocześnie, dostarczając token kontynuacji podczas kolejnych wywołań interfejsu API.