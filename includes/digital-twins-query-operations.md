---
author: baanders
description: plik dołączany dla operacji zapytań usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 450ea0b8024192f6f351b4893fe6e8e07db236f7
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931986"
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
* Użyj funkcji skalarnych: `IS_BOOL` ,,,, `IS_DEFINED` `IS_NULL` `IS_NUMBER` `IS_OBJECT` ,,, `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` , `ENDSWITH` .
* Użyj operatorów porównania zapytania: `IN` / `NIN` , `=` ,,, `!=` `<` `>` , `<=` , `>=` .
* Użyj dowolnej kombinacji ( `AND` , `OR` , `NOT` operatora) `IS_OF_MODEL` , funkcji skalarnych i operatorów porównania.
* Użyj kontynuacji: obiekt zapytania jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Możesz pobrać cyfrowy bliźniaczych reprezentacji jedną stronę jednocześnie, dostarczając token kontynuacji podczas kolejnych wywołań interfejsu API.