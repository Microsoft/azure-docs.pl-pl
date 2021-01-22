---
title: Różnicowa prywatność w usłudze Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, co to jest różnicowa Ochrona prywatności i jak wdrożyć zróżnicowane systemy prywatne, które zachowują prywatność danych.
author: luisquintanilla
ms.author: luquinta
ms.date: 01/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: 39f4b1a7b9eb1ad7a87097240dd772e4f2dadf17
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683547"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Co to jest różnicowa prywatność w usłudze Machine Learning (wersja zapoznawcza)

Dowiedz się więcej o zasadach poufności informacji w usłudze Machine Learning i sposobie jej działania.

Ponieważ ilość danych, które organizacja zbiera i używa do analiz, zwiększa się, dlatego nie ma obaw o prywatność i bezpieczeństwo. Analizy wymagają danych. Zazwyczaj im więcej danych służy do uczenia modeli, tym dokładniejsze są. Gdy dane osobowe są używane na potrzeby tych analiz, szczególnie ważne jest, aby były one prywatne przez cały czas użytkowania.

## <a name="how-differential-privacy-works"></a>Jak działa różnicowa Ochrona prywatności

Różnicowa Ochrona prywatności to zestaw systemów i praktyk, które ułatwiają zapewnienie bezpieczeństwa i prywatnego danych osobom.

> [!div class="mx-imgBorder"]
> ![Różnicowa proces uczenia maszynowego dotyczącego prywatności](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

W tradycyjnych scenariuszach dane pierwotne są przechowywane w plikach i bazach danych. Gdy użytkownicy analizują dane, zazwyczaj korzystają z danych pierwotnych. Jest to problem, ponieważ może naruszać prywatność poszczególnych użytkowników. Różnicowa Ochrona prywatności próbuje rozwiązać ten problem, dodając "szum" lub losowość do danych, tak aby użytkownicy nie mogli identyfikować poszczególnych punktów danych. Co najmniej taki system zapewnia możliwość odmowy dostępu. W związku z tym Ochrona prywatności osób jest zachowywana z ograniczonym wpływem na dokładność danych.

W różnych systemach prywatnych dane są udostępniane za poorednictwem żądań o nazwie **zapytania**. Gdy użytkownik przesyła zapytanie o dane, operacje znane jako **mechanizmy ochrony prywatności** dodają szum do żądanych danych. Mechanizmy ochrony prywatności zwracają *przybliżone dane* zamiast danych pierwotnych. Ten wynik zachowywania prywatności pojawia się w **raporcie**. Raporty składają się z dwóch części, obliczane są rzeczywiste dane oraz opis sposobu tworzenia danych.

## <a name="differential-privacy-metrics"></a>Różnicowe metryki ochrony prywatności

Różnicowa Ochrona prywatności próbuje chronić przed umożliwieniem, aby użytkownik mógł utworzyć nieokreśloną liczbę raportów, aby ostatecznie ujawnić poufne dane. Wartość znana jako **Epsilon** mierzy, jak działa szum lub prywatny raport. Opcja Epsilon ma odwrotną relację do szumu lub prywatności. Im niższa wartość, tym więcej szumów (i prywatnych) danych jest.

Wartości Epsilon nie są ujemne. Wartości poniżej 1 zapewniają pełną odmowę. Wszystkie elementy powyżej 1 są bardzo ryzykowne i zwiększają ryzyko ujawnienia rzeczywistych danych. W miarę implementowania różnych systemów prywatnych można generować raporty z Epsilon wartości z zakresu od 0 do 1.

Inna wartość, która jest bezpośrednio skorelowane z Epsilon, jest **różnicą**. Delta to miara prawdopodobieństwa, że raport nie jest w pełni prywatny. Im wyższa różnica, tym wyższa wartość Epsilon. Ponieważ te wartości są skorelowane, wartość Epsilon jest używana częściej.

## <a name="limit-queries-with-a-privacy-budget"></a>Ogranicz zapytania z budżetem prywatności

Aby zapewnić prywatność w systemach, w których dozwolone jest wiele zapytań, różnicowa Ochrona prywatności definiuje Limit szybkości. Ten limit jest określany jako **budżet ochrony prywatności**. Budżety prywatności uniemożliwiają odtworzenie danych przez wiele zapytań. Budżety dla prywatności są przydzieloną proporcjonalnie, zwykle od 1 do 3, aby ograniczyć ryzyko reidentyfikacji. W miarę generowania raportów, budżety prywatności śledzą wartość Epsilon poszczególnych raportów, a także agregację dla wszystkich raportów. Po uzyskaniu lub wyczerpaniu budżetu ochrony prywatności użytkownicy nie będą mieli już dostępu do danych. 

## <a name="reliability-of-data"></a>Niezawodność danych

Mimo że zachowanie prywatności powinno być celem, istnieje kompromis, gdy jest on przydatny do użyteczności i niezawodności danych. W analizie danych dokładność może być uważana za miarę niepewności wprowadzoną przez próbkowanie błędów. Ta niepewność jest zależeć do określonych granic. **Dokładność** różnic między zasadami zachowania poufności a zamiast nich mierzy niezawodność danych, na które ma wpływ niepewność wprowadzona przez mechanizmy ochrony prywatności. W skrócie wyższy poziom szumu lub prywatności tłumaczy dane o mniejszej wartości Epsilon, dokładności i niezawodności. 

## <a name="open-source-differential-privacy-libraries"></a>Różnicowe biblioteki ochrony prywatności Open Source

SmartNoise to projekt open-source, który zawiera różne składniki służące do tworzenia globalnych, różnicowych systemów prywatnych. SmartNoise składa się z następujących składników najwyższego poziomu:

- Biblioteka podstawowa SmartNoise
- Biblioteka zestawu SDK SmartNoise

### <a name="smartnoise-core"></a>SmartNoise rdzeń

Biblioteka podstawowa obejmuje następujące mechanizmy ochrony prywatności dotyczące wdrażania systemu w trybie różnicowym:

|Składnik  |Opis  |
|---------|---------|
|Analiza     | Opis wykresu dla dowolnych obliczeń. |
|Walidacj     | Biblioteka Rust, która zawiera zestaw narzędzi do sprawdzania i wyprowadzania niezbędnych warunków analizy do różnicowego prywatnego.          |
|Środowisko uruchomieniowe     | Nośnik do wykonania analizy. Dokumentacja środowiska uruchomieniowego jest zapisywana w Rust, ale środowiska uruchomieniowe można napisać przy użyciu dowolnego środowiska obliczeniowego, takiego jak SQL i Spark, w zależności od potrzeb dotyczących danych.        |
|Powiązania     | Powiązania języka i biblioteki pomocników do kompilowania analiz. Obecnie SmartNoise zapewnia powiązania języka Python. |

### <a name="smartnoise-sdk"></a>SmartNoise SDK

Biblioteka systemowa udostępnia następujące narzędzia i usługi do pracy z danymi tabelarycznymi i relacyjnymi:

|Składnik  |Opis  |
|---------|---------|
|Dostęp do danych     | Biblioteka, która przechwytuje i przetwarza zapytania SQL oraz tworzy raporty. Ta biblioteka jest zaimplementowana w języku Python i obsługuje następujące źródła danych ODBC i DBAPI:<ul><li>PostgreSQL</li><li>Oprogramowanie SQL Server</li><li>platforma Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Usługa     | Usługa wykonywania, która zapewnia punkt końcowy REST do obsługi żądań lub zapytań względem udostępnionych źródeł danych. Usługa została zaprojektowana tak, aby zezwalać na składanie różnicowych modułów prywatności, które działają na żądaniach zawierających różne wartości różnicowe i Epsilon, znane także jako żądania heterogeniczne. Ta implementacja referencyjna stanowi dodatkowy wpływ na zapytania dotyczące danych skorelowanych. |
|Obiekt     | Stochastycznego ewaluatora, który sprawdza poprawność prywatności, dokładność i BiAS. Ewaluatora obsługuje następujące testy: <ul><li>Test prywatności — określa, czy raport jest zgodny z warunkami różnicowej ochrony prywatności.</li><li>Test dokładności — określa, czy niezawodność raportów mieści się w górnym i niższym zakresie, który ma poziom ufności równy 95%.</li><li>Test narzędziowy — określa, czy granice zaufania raportu są wystarczająco blisko danych przy jednoczesnym maksymalizowaniu prywatności.</li><li>Test bias — mierzy rozkład raportów dla powtarzanych zapytań, aby upewnić się, że nie są one niezrównoważone</li></ul> |

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć zróżnicowany system prywatny](how-to-differential-privacy.md) w Azure Machine Learning.

Aby dowiedzieć się więcej o składnikach SmartNoise, zapoznaj się z repozytoriami usługi GitHub dla [SmartNoise Core](https://github.com/opendifferentialprivacy/smartnoise-core), [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)i [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples).