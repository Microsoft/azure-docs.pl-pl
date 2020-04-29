---
title: Planowanie aplikacji — LUIS
description: Zaplanuj odpowiednie intencje i jednostki dotyczące aplikacji, a następnie utwórz plany aplikacji w Language Understanding Intelligent Services (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382303"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planowanie schematu aplikacji LUIS przy użyciu domeny podmiotu i wyodrębniania danych

Schemat aplikacji LUIS zawiera [intencje](luis-glossary.md#intent) i [jednostki](luis-glossary.md#entity) powiązane z Twoją [domeną](luis-glossary.md#domain)podmiotu. Zamiary klasyfikują [wyrażenia długości](luis-glossary.md#utterance)użytkownika i jednostki wyodrębnią dane z wyrażenia długości użytkownika.

## <a name="identify-your-domain"></a>Zidentyfikuj domenę

Aplikacja LUIS jest wyśrodkowana wokół domeny podmiotu. Na przykład może istnieć aplikacja podróży, która obsługuje rezerwacje biletów, lotów, hoteli i samochodów. Inna aplikacja może zapewnić zawartość powiązaną z wykonywaniem, śledzeniem wysiłków związanych z sprawnością i ustawianiem celów. Zidentyfikowanie domeny ułatwia znalezienie wyrazów lub fraz, które są istotne dla Twojej domeny.

> [!TIP]
> LUIS oferuje [prebudowane domeny](luis-how-to-use-prebuilt-domains.md) dla wielu typowych scenariuszy. Sprawdź, czy możesz użyć wstępnie skompilowanej domeny jako punktu wyjścia dla aplikacji.

## <a name="identify-your-intents"></a>Zidentyfikuj swoje intencje

Pomyśl o [intencjach](luis-concept-intent.md) , które są ważne dla zadania aplikacji.

Przyjrzyjmy się przykładowi do aplikacji podróży z funkcjami do rezerwacji lotu i sprawdzania pogody w miejscu docelowym użytkownika. Można zdefiniować `BookFlight` i `GetWeather` intencje dla tych działań.

W bardziej złożonej aplikacji z większą liczbą funkcji masz więcej intencji i należy je dokładnie definiować, aby nie było to intencje. `BookFlight` Na przykład `BookHotel` może być konieczne osobne zamiary, ale `BookInternationalFlight` i `BookDomesticFlight` mogą być zbyt podobne.

> [!NOTE]
> Najlepszym rozwiązaniem jest użycie tylko tyle rzeczy, ile potrzebujesz do wykonywania funkcji aplikacji. Jeśli określisz zbyt wiele intencji, będzie trudniejsze, aby LUIS prawidłowo klasyfikować wyrażenia długości. Jeśli zdefiniujesz zbyt kilka, może to być ogólny, że nakładają się na siebie.

Jeśli nie musisz identyfikować ogólnych założeń użytkowników, Dodaj do `None` zamiaru wszystkie przykładowe wyrażenia długości użytkownika. Jeśli aplikacja zostanie powiększona o potrzebę bardziej zamiarów, można utworzyć je później.

## <a name="create-example-utterances-for-each-intent"></a>Utwórz przykład wyrażenia długości dla każdego zamiaru

Aby zacząć od, należy unikać tworzenia zbyt wielu wyrażenia długości dla każdego zamiaru. Po ustaleniu intencji należy utworzyć na przykład wyrażenia długości od 15 do 30. Każdy wypowiedź powinien różnić się od wcześniej podanej wyrażenia długości. Dobra odmiana wyrażenia długości zawierać ogólną liczbę słów, wybór wyrazów, intensywność i [znaki interpunkcyjne](luis-reference-application-settings.md#punctuation-normalization).

Aby uzyskać więcej informacji, zobacz temat [wyrażenia długości for Luis Apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identyfikowanie jednostek

W przykładzie wyrażenia długości Określ jednostki, które mają zostać wyodrębnione. Aby zaksięgować lot, potrzebne są informacje, takie jak lokalizacja docelowa, Data, linia lotnicza, Kategoria biletu i Klasa podróży. Utwórz jednostki dla tych typów danych, a następnie Oznacz [jednostki](luis-concept-entity-types.md) w przykładzie wyrażenia długości. Jednostki są ważne do wykonywania zamierzeń.

Podczas określania obiektów, które mają być używane w aplikacji, należy pamiętać, że istnieją różne typy jednostek do przechwytywania relacji między typami obiektów. [Jednostki w Luis](luis-concept-entity-types.md) zapewniają więcej szczegółowych informacji o różnych typach.

> [!TIP]
> LUIS oferuje [wstępnie utworzone jednostki](luis-prebuilt-entities.md) na potrzeby typowych scenariuszy użytkownika. Rozważ użycie wstępnie utworzonych jednostek jako punktu wyjścia do tworzenia aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uczenie się LUIS Development lifecylce](luis-concept-app-iteration.md)

