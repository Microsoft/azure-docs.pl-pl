---
title: Gdzie i jak używać programu-Personalizacja
description: Personalizacja może być stosowana w każdej sytuacji, w której aplikacja może wybrać odpowiedni element, akcję lub produkt do wyświetlenia — w celu lepszego działania, osiągnięcia lepszych wyników firmy lub zwiększenia produktywności.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777228"
---
# <a name="where-and-how-to-use-personalizer"></a>Gdzie i jak używać personalizacji

Użyj personalizacji w każdej sytuacji, w której aplikacja musi wybrać poprawną akcję (zawartość) do wyświetlenia — aby zapewnić lepszą wydajność, osiągać lepsze wyniki biznesowe lub zwiększyć produktywność.

Personalizacja używa uczenia wzmacniania, aby wybrać akcję (zawartość), która ma być wyświetlana użytkownikowi. Wybór może się różnić w zależności od ilości, jakości i dystrybucji danych wysyłanych do usługi.

## <a name="example-use-cases-for-personalizer"></a>Przykładowe przypadki użycia dla personalizacji

* **Wyjaśnienie Zamiaru & uściślania**: Pomóż użytkownikom w lepszym działaniu, gdy ich intencje nie są jasne, zapewniając spersonalizowaną opcję.
* **Domyślne sugestie** dotyczące menu & opcje: bot Sugeruj najbardziej prawdopodobną pozycję w spersonalizowanym trybie jako pierwszy krok, zamiast przedstawiać menu lub listę alternatyw.
* **Cechy Bot & ton**: dla botów, które mogą różnić się od tonu, szczegółowości i stylu pisania, należy rozważyć zróżnicowanie tych cech.
* **Treść alertu & powiadomień**: określ tekst, który ma być używany na potrzeby alertów, aby zapewnić użytkownikom więcej.
* **Czas alertu & powiadomień**: masz spersonalizowaną naukę podczas wysyłania powiadomień do użytkowników w celu ich przeprowadzenia.


## <a name="expectations-required-to-use-personalizer"></a>Oczekiwania wymagane do korzystania z programu Personalizujer

Można zastosować personalizację w sytuacjach, w których są spełnione lub można zaimplementować następujące wytyczne.

|Wytyczna|Objaśnienie|
|--|--|
|Cel biznesowy|Użytkownik ma cel biznesowy lub użyteczny dla Twojej aplikacji.|
|Zawartość|Masz miejsce w aplikacji, w którym należy wprowadzić kontekstową decyzję, którą należy pokazać użytkownikom w celu usprawnienia tego celu.|
|Ilość zawartości|Masz mniej niż 50 akcji do rangi na wywołanie.|
|Agregowanie danych|Najlepszym wyborem może być i powinna być uczenie się od rozłącznych zachowań użytkowników i łącznego wyniku.|
|Użycie etyczne|Korzystanie z uczenia maszynowego na potrzeby personalizacji odbywa się zgodnie z [właściwymi wskazówkami dotyczącymi użycia](ethics-responsible-use.md) .
|Najlepsza opcja|Decyzja kontekstowa może być wyrażona jako Klasyfikacja najlepszej opcji (Akcja) z ograniczonego zestawu opcji.|
|Wynik wynikowy|Sposób, w jaki zamierzone wybór działa dla aplikacji, można określić przez zmierzenie pewnego aspektu zachowania użytkownika i wyrażanie go w _[wyniku nagrody](concept-rewards.md)_.|
|Odpowiedni czas|Wynik nagrody nie powoduje zbyt wielu lub zewnętrznych czynników. Czas trwania eksperymentu jest niski, ponieważ wynik nagrody może być obliczany, gdy nadal ma zastosowanie.|
|Wystarczające funkcje kontekstu|Można wyrazić kontekst dla rangi jako listę co najmniej 5 [funkcji](concepts-features.md) , które można wybrać, aby pomóc w wyborze odpowiedniego wyboru, które nie zawierają informacji specyficznych dla użytkownika.|
|Wystarczające funkcje akcji|Istnieją informacje o każdej wybranej zawartości, _akcji_, jako lista co najmniej 5 [funkcji](concepts-features.md) , które należy wziąć pod uwagę, aby ułatwić sobie wybranie.|
|Dzienne dane|Istnieje wystarczająco dużo zdarzeń, aby zachować dostęp do optymalnej personalizacji, jeśli problem zostanie odczytany z upływem czasu (na przykład preferencje w wiadomościach lub w sposób). Personalizowanie dostosuje się do ciągłej zmiany w świecie rzeczywistym, ale wyniki nie będą optymalne, jeśli nie ma wystarczającej liczby zdarzeń i danych, które należy poznać, aby odkryć i rozliczyć nowe wzorce. Należy wybrać przypadek użycia, który ma być często wystarczający. Rozważ wyszukiwanie przypadków użycia, które wystąpią co najmniej 500 razy dziennie.|
|Dane historyczne|Aplikacja może przechowywać dane przez wystarczająco dużo, aby gromadzić historię co najmniej 100 000 interakcji. Umożliwia to programowi Personalizujemu zbieranie wystarczającej ilości danych w celu przeprowadzenia oceny w trybie offline i optymalizacji zasad.|

**Nie używaj personalizacji** , gdy spersonalizowane zachowanie nie jest elementem, który można odnaleźć dla wszystkich użytkowników. Na przykład za pomocą narzędzia Personalizacja można zasugerować pierwsze zamówienie Pizza z listy 20 możliwych elementów menu jest przydatne, ale które kontaktuje się z listą kontaktów użytkowników, jeśli wymagają pomocy w zakresie pielęgnacji dzieci (na przykład "Grandma"), ale nie jest to coś, co jest Personalizable w bazie użytkownika.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak używać personalizacji w aplikacji sieci Web

Dodawanie pętli szkoleniowej do aplikacji sieci Web obejmuje:

* Określ, które środowisko ma być personalizowane, jakie akcje i funkcje, jakie funkcje kontekstu mają być używane, i jakie znaczenie ma.
* Dodaj odwołanie do zestawu SDK personalizacji w swojej aplikacji.
* Wywołaj interfejs API rangi, gdy wszystko będzie gotowe do personalizacji.
* Zapisz eventId. Za pomocą interfejsu API nagradzania wysyłasz premię później.
1. Wywołaj aktywację dla zdarzenia po upewnieniu się, że użytkownik zobaczy spersonalizowaną stronę.
1. Zaczekaj, aż użytkownik wybierze sklasyfikowaną zawartość.
1. Wywołaj interfejs API nagradzania, aby określić, jak dobrze dane wyjściowe interfejsu API rangi zostały wykonane.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak używać funkcji personalizowania z usługą Chat bot

W tym przykładzie zobaczysz, jak za pomocą personalizacji wprowadzić domyślną sugestię zamiast wysyłania użytkownika w dół szereg menu lub opcji za każdym razem.

* Pobierz [kod](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) dla tego przykładu.
* Skonfiguruj rozwiązanie bot. Upewnij się, że aplikacja LUIS została opublikowana.
* Zarządzaj wywołaniami interfejsu API rangi i nagradzania dla bot.
    * Dodaj kod, aby zarządzać LUIS zamiarem. Jeśli wartość **none** nie jest zwracana jako zamierzenie górne lub wartość oceny najwyższego poziomu jest niższa od progu logiki biznesowej, Wyślij listę intencji do personalizacji, aby zaklasyfikować intencje.
    * Pokaż listę intencji użytkownikom jako wybrane linki z pierwszym zamiarem w celu uzyskania najwyższej rangi zamiaru z odpowiedzi interfejsu API rangi.
    * Przechwyć wybór użytkownika i wyślij go w wywołaniu interfejsu API nagradzania.

### <a name="recommended-bot-patterns"></a>Zalecane wzorce bot

* Nadaje się wywołania interfejsu API rangi personalizacji za każdym razem, gdy konieczne jest odróżnienie, w przeciwieństwie do buforowania wyników dla każdego użytkownika. Wynik niedozwolonego zamiaru może ulec zmianie w czasie dla jednej osoby i umożliwienie interfejsowi API rangi Eksplorowanie wariancji spowoduje przyspieszenie ogólnej nauki.
* Wybierz interakcję, która jest wspólna dla wielu użytkowników, aby mieć wystarczającą ilość danych do spersonalizowania. Na przykład monity wprowadzające mogą być lepiej dopasowane od mniejszych wyjaśnień na grafie konwersacji, do których może przejść tylko kilku użytkowników.
* Użyj wywołań interfejsu API rangi, aby włączyć funkcję "Pierwsza sugestia jest właściwa", gdzie użytkownik otrzymuje monit "czy chcesz X?". lub "Czy chodziło o X?" Użytkownik może po prostu potwierdzić; w przeciwieństwie do udzielania użytkownikom opcji, gdzie muszą wybierać z menu. Na przykład użytkownik: ' chcę zamówić kawę "bot:" czy chcesz mieć podwójny Espresso? ". W ten sposób sygnał zarobkowy jest również silny, ponieważ odnosi się bezpośrednio do jednej sugestii.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak używać narzędzia Personalizacja z rozwiązaniem rekomendacji

Wiele firm używa aparatów rekomendacji, narzędzi marketingowych i kampanii, segmentacji odbiorców i klastrowania, filtrowania do współpracy i innych metod, aby zalecać produkty z dużego wykazu klientom.

[Repozytorium GitHub firmy Microsoft](https://github.com/Microsoft/Recommenders) zawiera przykłady i najlepsze rozwiązania dotyczące kompilowania systemów zaleceń, które są udostępniane jako notesy Jupyter. Zawiera ona przykładowe działania dotyczące przygotowywania danych, tworzenia modeli, oceniania, dostrajania i operacjonalizowaniai aparatów rekomendacji dla wielu wspólnych metod, takich jak xDeepFM, SAR, ALS, KMS, DKN.

Personalizowanie może współdziałać z aparatem rekomendacji, gdy jest obecny.

* Aparaty rekomendacji pobierają duże ilości elementów (na przykład 500 000) i zalecają podzbiór (np. 20 najważniejszych) z setek lub tysięcy opcji.
* Personalizacja wykonuje niewielką liczbę akcji z wieloma informacjami o nich i ustala ich rangę w czasie rzeczywistym dla danego rozbudowanego kontekstu, a większość aparatów rekomendacji używa tylko kilku atrybutów dotyczących użytkowników, produktów i ich interakcji.
* Personalizacja została zaprojektowana, aby samodzielnie eksplorować preferencje użytkownika przez cały czas, co zapewni lepsze wyniki, dzięki czemu zawartość będzie szybko zmieniana, taka jak Aktualności, wydarzenia na żywo, zawartość społeczności na żywo, zawartość z codziennymi aktualizacjami lub zawartość sezonowa.

Typowym zastosowaniem jest przejęcie danych wyjściowych aparatu rekomendacji (na przykład 20 najważniejszych produktów dla określonego klienta) i użycie go jako akcji wejściowych dla personalizacji.

## <a name="adding-content-safeguards-to-your-application"></a>Dodawanie zabezpieczeń zawartości do aplikacji

Jeśli aplikacja zezwala na duże Wariancje w zawartości widocznej dla użytkowników, a niektóre z nich mogą być niebezpieczne lub nieodpowiednie dla niektórych użytkowników, należy zaplanować z wyprzedzeniem, aby upewnić się, że odpowiednie zabezpieczenia są stosowane w celu uniemożliwienia użytkownikom wyświetlania nieakceptowalnej zawartości. Najlepszym wzorcem do implementowania zabezpieczeń są:
    * Uzyskaj listę akcji do rangi.
    * Odfiltruj te, które nie są przeznaczone dla odbiorców.
    * Należy tylko klasyfikować te działania.
    * Wyświetl dla użytkownika górną rangę.

W niektórych architekturach powyższa sekwencja może być trudna do zaimplementowania. W takim przypadku istnieje alternatywne podejście do implementowania zabezpieczeń po klasyfikacji, ale należy wprowadzić, aby działania, które wykraczają poza zabezpieczenie, nie są używane do uczenia modelu personalizowania.

* Uzyskaj listę akcji do rangi, z zdezaktywowaną nauką.
* Akcje rangi.
* Sprawdź, czy górna akcja jest opłacalna.
    * Jeśli Górna akcja jest wykonalna, Aktywuj uczenie dla tej rangi, a następnie Pokaż ją użytkownikowi.
    * Jeśli najwyższe działanie nie jest możliwe, nie należy aktywować uczenia w ramach tej klasyfikacji i zdecydować się na podstawie własnej logiki lub alternatywnej metody, które mają być widoczne dla użytkownika. Nawet jeśli używasz opcji Second-Najlepsza w rankingu, nie Uaktywniaj uczenia dla tej klasyfikacji.


## <a name="next-steps"></a>Następne kroki

[Etyka & odpowiedzialna za użycie](ethics-responsible-use.md).
