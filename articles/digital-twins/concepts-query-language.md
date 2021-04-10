---
title: Język zapytań
titleSuffix: Azure Digital Twins
description: Zapoznaj się z podstawą języka zapytań Digital bliźniaczych reprezentacji na platformie Azure.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490980"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informacje o języku zapytań dla usługi Azure Digital bliźniaczych reprezentacji

Odwołaj się do tego, że centrum Digital bliźniaczych reprezentacji na platformie Azure jest [grafem bliźniaczym](concepts-twins-graph.md), zbudowanym na podstawie cyfrowych bliźniaczych reprezentacji i relacji. 

Ten Graf można zbadać, aby uzyskać informacje na temat bliźniaczych reprezentacji cyfrowego i relacji, które zawiera. Te zapytania są zapisywane w niestandardowym języku zapytań przypominającym język SQL, nazywanym **językiem zapytań usługi Azure Digital Twins**. Jest to podobne do [języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) z wieloma porównywalnymi funkcjami.

W tym artykule opisano podstawowe informacje dotyczące języka zapytań i jego możliwości. Aby zapoznać się z bardziej szczegółowymi przykładami składni zapytań i sposobu uruchamiania żądań zapytań, zobacz [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Informacje o zapytaniach

Możesz użyć języka zapytań Digital bliźniaczych reprezentacji na platformie Azure, aby pobrać bliźniaczych reprezentacji cyfrowy zgodnie z ich...
* Właściwości (z uwzględnieniem [Właściwości tagu](how-to-use-tags.md))
* modele
* relationships
  - właściwości relacji

Aby przesłać zapytanie do usługi z aplikacji klienckiej, użyj [**interfejsu API zapytań**](/rest/api/digital-twins/dataplane/query)Digital bliźniaczych reprezentacji platformy Azure. Jednym ze sposobów korzystania z interfejsu API jest użycie jednego z [zestawów SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) dla usługi Azure Digital bliźniaczych reprezentacji.

### <a name="considerations-for-querying"></a>Zagadnienia dotyczące zapytań

Podczas pisania zapytań dotyczących usługi Azure Digital bliźniaczych reprezentacji należy pamiętać o następujących kwestiach:
* **Pamiętaj czułość uwzględniania wielkości liter**: wszystkie operacje zapytań usługi Azure Digital bliźniaczych reprezentacji są rozróżniane wielkości liter, więc pamiętaj, aby użyć dokładnych nazw zdefiniowanych w modelach. Jeśli nazwy właściwości są błędnie napisane lub nieprawidłowo, zestaw wyników jest pusty i nie są zwracane żadne błędy.
* **Pojedyncze cudzysłowy ucieczki**: Jeśli tekst zapytania zawiera pojedynczy znak cudzysłowu w danych, do cudzysłowu należy użyć znaku ucieczki `\` . Oto przykład, który zajmuje się wartością właściwości *D'Souza*:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Odwołanie: wyrażenia i warunki

W tej sekcji opisano operatory i funkcje, które są dostępne do pisania zapytań usługi Azure Digital bliźniaczych reprezentacji. Przykładowe zapytania, które ilustrują korzystanie z tych funkcji, można znaleźć w temacie [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

### <a name="operators"></a>Operatory

Obsługiwane są następujące operatory:

| Family | Operatory |
| --- | --- |
| Wartości logiczne |`AND`, `OR`, `NOT` |
| Porównanie | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>Funkcje

Obsługiwane są następujące funkcje sprawdzania typu i rzutowania:

| Funkcja | Opis |
| -------- | ----------- |
| `IS_DEFINED` | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. Jest to obsługiwane tylko wtedy, gdy wartość jest typem pierwotnym. Typy pierwotne obejmują ciąg, wartość logiczną, numeryczną lub `null` . `DateTime`, typy obiektów i tablice nie są obsługiwane. |
| `IS_OF_MODEL` | Zwraca wartość logiczną wskazującą, czy określony sznurek pasuje do określonego typu modelu |
| `IS_BOOL` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest wartością logiczną. |
| `IS_NUMBER` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest liczbą. |
| `IS_STRING` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest ciągiem. |
| `IS_NULL` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ma wartość null. |
| `IS_PRIMITIVE` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest typem pierwotnym (String, Boolean, numeric lub `null` ). |
| `IS_OBJECT` | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON. |

Obsługiwane są następujące funkcje ciągów:

| Funkcja | Opis |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zaczyna się od drugiego. |
| `ENDSWITH(x, y)` | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu ma kończyć się drugim. |

## <a name="query-limitations"></a>Ograniczenia zapytania

W tej sekcji opisano ograniczenia języka zapytań.

* Chronometraż: może istnieć opóźnienie do 10 sekund, po upływie którego zmiany w wystąpieniu zostaną odzwierciedlone w zapytaniach. Na przykład jeśli zostanie wykonana operacja, taka jak tworzenie lub usuwanie bliźniaczych reprezentacji za pomocą interfejsu API DigitalTwins, wynik może nie być natychmiast widoczny w żądaniach interfejsu API zapytań. Oczekiwanie na krótki okres powinno być wystarczające do rozwiązania.
* W instrukcji nie są obsługiwane żadne podzapytania `FROM` .
* `OUTER JOIN` Semantyka nie jest obsługiwana, znaczenie, jeśli relacja ma rangę zero, cały "wiersz" jest eliminowany z wyjściowego zestawu wyników.
* Głębokość przechodzenia wykresu jest ograniczona do pięciu `JOIN` poziomów na zapytanie.
* Nie można odpytać relacji w usłudze Azure Digital bliźniaczych reprezentacji jako jednostek niezależnych; należy również podać informacje o źródle źródła, z którego pochodzi relacja. Oznacza to, że istnieją pewne ograniczenia dotyczące `JOIN` operacji, która jest używana do wykonywania zapytań o relacje, aby upewnić się, że zapytanie deklaruje sznurki, w których rozpoczyna się zapytanie. Aby zapoznać się z przykładami, zobacz [*zapytanie według relacji*](how-to-query-graph.md#query-by-relationship) w artykule wykonywanie *zapytania dotyczącego grafu* .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak pisać zapytania i zapoznać się z przykładami kodu klienta w artykule [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).