---
title: Wskazówki dotyczące projektowania ujawniania
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do ujawnienia wytycznych dotyczących projektowania i oceniania poziomu ujawniania.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 472d55f79033d60c4f40e60b55e0f7fc2ea4517e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716656"
---
# <a name="disclosure-design-guidelines"></a>Wytyczne dotyczące projektowania ujawniania
Dowiedz się, jak tworzyć i obsługiwać relacje zaufania z klientami, ponieważ są one przejrzyste na temat syntetycznego charakteru Twojego środowiska głosowego.

## <a name="what-is-disclosure"></a>Co to jest ujawnienie?

Ujawnienie jest sposobem, aby użytkownicy wiedzieli, że&#39;w trakcie korzystania z lub nasłuchiwanie głosu, który został wygenerowany syntetycznie.

## <a name="why-is-disclosure-necessary"></a>Dlaczego jest wymagane ujawnienie?

Konieczność ujawnienia syntetycznych źródeł głosu generowanego przez komputer jest stosunkowo nowa. W przeszłości głosy generowane przez komputery były oczywiście, ale nikt z nich nie mógłby ich w rzeczywistości wyznaczyć. Codziennie, jednak realne głosy syntetyczne zwiększają się i coraz bardziej odróżniają się od głosów ludzkich.

## <a name="goals"></a>Cele
Są to zasady, które należy wziąć pod uwagę podczas projektowania rozwiązań dotyczących głosu syntetycznego:

**Wzmocnienie zaufania**
<br>Zaprojektuj z zamiarem niepowodzenia testu Turing bez obniżenia wydajności. Poinformuj użytkowników, że są w stanie korzystać z syntetycznego głosu, jednocześnie pozwalając im bezproblemowo komunikować się z tym doświadczeniem.

**Dostosuj do kontekstu użytkowania**
<br>Dowiedz się, kiedy i w jaki sposób użytkownicy będą korzystać z głosu syntetycznego, aby zapewnić właściwy typ ujawniania w odpowiednim czasie.

**Ustaw jasne oczekiwania**
<br>Zezwól użytkownikom na łatwe odnajdowanie i zrozumienie możliwości agenta. Oferuj szanse na uzyskanie dodatkowych informacji na temat syntetycznej technologii głosowej na żądanie.

**Wystąpił błąd**
<br>Aby wzmocnić możliwości agenta, użyj momentu niepowodzenia.

## <a name="how-to-use-this-guide"></a>Jak korzystać z tego przewodnika

Ten przewodnik pomaga określić, które wzorce ujawniania najlepiej pasują do Twoich danych. Następnie oferujemy Przykłady sposobu i czasu korzystania z nich. Każdy z tych wzorców został zaprojektowany w celu zmaksymalizowania przejrzystości dla użytkowników na temat syntetycznej mowy, gdy jest to prawdziwe w przypadku projektowania przez człowieka.

Biorąc pod uwagę ogromną część wytycznych dotyczących projektowania w zakresie obsługi głosu, koncentrujemy się tutaj na:

1. [**Ocena ujawniania**](#disclosure-assessment): proces ustalania typu ujawniania zalecanego w przypadku korzystania z syntetycznego głosu

2. [**Jak ujawnić**](concepts-disclosure-patterns.md): przykłady wzorców ujawniania, które mogą być stosowane do Twoich syntetycznych funkcji głosowych

3. [**Kiedy ujawniać**](concepts-disclosure-patterns.md#when-to-disclose): optymalny czas na ujawnienie w trakcie podróży użytkownika

## <a name="disclosure-assessment"></a>Ocena ujawniania
Należy wziąć pod uwagę, że użytkownicy&#39;ą oczekiwania na interakcję i kontekst, w którym będą mogli napotkać głos. Jeśli kontekst wyraźnie oznacza, że głos syntetyczny jest &quot; wymawiany, &quot; ujawnienie może być minimalne, dwuosobowe lub nawet zbędne. Główne typy kontekstu, które mają wpływ na ujawnienie, obejmują typ osoby, typ scenariusza i poziom narażenia. Pomaga również rozważyć, kto może nasłuchiwać.

### <a name="understand-context"></a>Omówienie kontekstu

Ten arkusz służy do określania kontekstu Twojego środowiska głosu syntetycznego. Zostanie ona zastosowana w następnym kroku, w którym określisz poziom ujawniania.

|                                    | Kontekst użytkowania                                                                                                                                                                                                                                                                                                                                                       | Potencjalne zagrożenia & wyzwania                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. typ osoby**               | **Jeśli którykolwiek z poniższych warunków dotyczy, osoba ta pasuje do kategorii "osoby podobne do":**<br><br><ul><li> Osoba jest w rzeczywistości prawdziwa, niezależnie od tego, czy jest to fikcyjna reprezentacja, czy nie. (np. Fotografia lub wygenerowane przez komputer renderowanie rzeczywistej osoby)<br><br><li> Cyfrowy głos jest oparty na głosowaniu szeroko rozpoznawalnej osoby rzeczywistej (np. osobistości, na przykład polityczny) | Im bardziej podobne przedstawicielstwa są udzielane osobie, tym większe prawdopodobieństwo, że użytkownik będzie skojarzyć go z osobą rzeczywistą lub może uznać, że zawartość jest wymawiana przez osobę rzeczywistą, a nie wygenerowaną przez komputer. </ul>                                                                                                                                                                      |
| **2. typ scenariusza**            | **Jeśli którekolwiek z poniższych warunków, środowisko głosu pasuje do kategorii "poufne":**<br><br><ul><li> Uzyskuje lub wyświetla dane osobowe użytkownika <br><br> <li> Emituje poufne wiadomości/informacje (np. alerty awaryjne)<br><br><li> Ma na celu ułatwienie komunikacji ze sobą w rzeczywistych osobach (np. odczytywanie osobistych wiadomości e-mail/tekstów)<br><br> <li> Zapewnia pomoc medyczną/kondycję </ul>            | Korzystanie z syntetycznego głosu może nie być odpowiednie ani wiarygodne dla osób korzystających z niego, gdy tematy są powiązane z wrażliwymi, osobistymi lub pilnymi kwestiami. Mogą również oczekiwać tego samego poziomu empatię i świadomości kontekstowej jako prawdziwych ludzi. |
| **3. poziom ekspozycji** |**Twoje środowisko głosu najprawdopodobniej pasuje do kategorii "wysoka", jeśli:** <br><br><ul><li>Użytkownik będzie słyszeć lub korzystać z głosu syntetycznego często lub przez długi czas </ul>                                                                                                                                                                             | Znaczenie dla przejrzystości i kompilowanie relacji zaufania z użytkownikami jest jeszcze większe podczas ustanawiania długoterminowych relacji.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Określ poziom ujawniania

Użyj poniższego diagramu, aby określić, czy Twoje syntetyczne środowisko głosu wymaga wysokiego lub niskiego ujawnienia na podstawie kontekstu użytkowania.

  ![Diagram oceny ujawniania](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Zobacz też

* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)
* [Ujawnienie dla talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Wytyczne dotyczące odpowiedzialnego wdrożenia syntetycznej technologii głosowej](concepts-guidelines-responsible-deployment-synthetic.md)