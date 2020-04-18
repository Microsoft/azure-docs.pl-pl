---
title: Gdzie i jak używać - Personalizer
description: Personalizer może być stosowany w każdej sytuacji, w której aplikacja może wybrać odpowiedni element, działanie lub produkt do wyświetlenia - w celu poprawy doświadczenia, osiągnięcia lepszych wyników biznesowych lub poprawy wydajności.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: c562d7a1853736204a7a03262547e083bd85fb75
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617684"
---
# <a name="where-and-how-to-use-personalizer"></a>Gdzie i jak korzystać z Personalizer

Personalizer należy używać w każdej sytuacji, w której aplikacja musi wybrać prawidłowe działanie (zawartość) do wyświetlenia - w celu poprawy doświadczenia, osiągnięcia lepszych wyników biznesowych lub zwiększenia wydajności.

Personalizer używa uczenia wzmacniania, aby wybrać akcję (zawartość), aby pokazać użytkownikowi. Wybór może się znacznie różnić w zależności od ilości, jakości i dystrybucji danych wysyłanych do usługi.

## <a name="example-use-cases-for-personalizer"></a>Przykładowe przypadki użycia personalizatora

* **Wyjaśnienie intencji & niejednoznaczności:** pomóż użytkownikom mieć lepsze wrażenia, gdy ich intencja nie jest jasna, zapewniając opcję spersonalizowaną.
* **Domyślne sugestie** dotyczące menu & opcji: niech bot zaproponuje najbardziej prawdopodobny element w spersonalizowany sposób jako pierwszy krok, zamiast przedstawiać bezosobowe menu lub listę alternatyw.
* **Cechy botów & ton:** dla botów, które mogą zmieniać ton, szczegółowość i styl pisania, należy rozważyć zmianę tych cech.
* **Powiadomienie & treści alertów:** zdecyduj, jakiego tekstu użyć w przypadku alertów, aby przyciągnąć użytkowników.
* **Powiadomienie & czas alertu:** mieć spersonalizowane uczenie się, kiedy wysyłać powiadomienia do użytkowników, aby zaangażować ich bardziej.


## <a name="expectations-required-to-use-personalizer"></a>Oczekiwania wymagane do korzystania z Personalizer

Personalizer można zastosować w sytuacjach, w których spełniasz lub można wdrożyć następujące wskazówki.

|Wytyczna|Wyjaśnienie|
|--|--|
|Cel biznesowy|Masz cel biznesowy lub użyteczność dla aplikacji.|
|Zawartość|Masz miejsce w aplikacji, gdzie podejmowanie kontekstowej decyzji, co pokazać użytkownikom poprawi ten cel.|
|Ilość zawartości|Masz mniej niż 50 akcji do rangi na wywołanie.|
|Zagregowane dane|Najlepszy wybór można i należy wyciągnąć z zbiorowego zachowania użytkownika i całkowitego wyniku nagrody.|
|Etyczne stosowanie|Korzystanie z uczenia maszynowego do personalizacji jest zgodne z [wytycznymi odpowiedzialnego użytkowania](ethics-responsible-use.md) i wybranymi wyborami.
|Najlepsza pojedyncza opcja|Decyzja kontekstowa może być wyrażona jako ranking najlepszą opcją (działanie) z ograniczonego zestawu opcji.|
|Wynik strzelony|Jak dobrze wybór w rankingu pracował dla aplikacji można określić, mierząc jakiś aspekt zachowania użytkownika i wyrażając go w _[wyniku nagrody](concept-rewards.md)_.|
|Odpowiedni termin|Wynik nagrody nie przynosi zbyt wielu czynników zakłócających lub zewnętrznych. Czas trwania eksperymentu jest na tyle niski, że wynik nagrody można obliczyć, gdy jest on nadal aktualny.|
|Wystarczające funkcje kontekstowe|Możesz wyrazić kontekst rangi jako listę co najmniej 5 [funkcji,](concepts-features.md) które uważasz, że pomogą ci dokonać właściwego wyboru, a które nie zawierają informacji umożliwiających identyfikację użytkownika.|
|Wystarczające funkcje działania|Masz informacje o każdym wyborze treści, _akcji,_ jako listę co najmniej 5 [funkcji,](concepts-features.md) które uważasz, że pomoże Personalizer dokonać właściwego wyboru.|
|Dane dzienne|Jest wystarczająco dużo wydarzeń, aby być na szczycie optymalnej personalizacji, jeśli problem dryfuje w czasie (takich jak preferencje w wiadomościach lub modzie). Personalizer dostosuje się do ciągłych zmian w świecie rzeczywistym, ale wyniki nie będą optymalne, jeśli nie ma wystarczającej ilości zdarzeń i danych, z których można się nauczyć, aby odkryć i osiedlić się na nowych wzorcach. Należy wybrać przypadek użycia, który zdarza się wystarczająco często. Rozważ poszukanie przypadków użycia, które zdarzają się co najmniej 500 razy dziennie.|
|Dane historyczne|Aplikacja może przechowywać dane wystarczająco długo, aby zgromadzić historię co najmniej 100 000 interakcji. Dzięki temu Personalizer do zbierania wystarczającej ilości danych do wykonywania ocen w trybie offline i optymalizacji zasad.|

**Nie używaj Personalizer,** gdzie spersonalizowane zachowanie nie jest czymś, co można odkryć we wszystkich użytkownikach. Na przykład, za pomocą Personalizer zaproponować pierwsze zamówienie pizzy z listy 20 możliwych elementów menu jest przydatna, ale kontakt, aby zadzwonić z listy kontaktów użytkowników, gdy wymaga pomocy w opiece nad dziećmi (takich jak "Babcia") nie jest coś, co jest personalizacji w całej bazy użytkowników.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak korzystać z Personalizer w aplikacji internetowej

Dodawanie pętli uczenia się do aplikacji sieci web obejmuje:

* Określ, które środowisko spersonalizować, jakie masz działania i funkcje, jakie funkcje kontekstu użyć i jaką nagrodę ustawisz.
* Dodaj odwołanie do sdk personalizacji w aplikacji.
* Wywołanie interfejsu API rangi, gdy jesteś gotowy do personalizacji.
* Zapisz identyfikator zdarzenia. Później wyślesz nagrodę za pomocą interfejsu API nagrody.
1. Zawołaj aktywuj wydarzenie, gdy masz pewność, że użytkownik zobaczył Twoją spersonalizowaną stronę.
1. Poczekaj na wybór zawartości rankingowej przez użytkownika.
1. Wywołaj interfejs API nagrody, aby określić, jak dobrze dane wyjściowe interfejsu API rangi nie.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak korzystać z Personalizer z chat bot

W tym przykładzie zobaczysz, jak za pomocą personalizacji, aby dokonać domyślnej sugestii zamiast wysyłania użytkownika w dół serii menu lub wyborów za każdym razem.

* Pobierz [kod](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) dla tego przykładu.
* Skonfiguruj rozwiązanie bota. Upewnij się, aby opublikować aplikację usługi LUIS.
* Zarządzanie wezwaniami interfejsu API rangi i nagrody dla bota.
    * Dodaj kod do zarządzania przetwarzaniem intencji usługi LUIS. Jeśli **None** jest zwracany jako najwyższej intencji lub wynik najwyższego intencji jest poniżej progu logiki biznesowej, wyślij listę intencji do Personalizer do rangi intencji.
    * Pokaż listę intencji użytkownikowi jako łącza do wyboru z pierwszą intencją, która jest intencją najwyższej rangi z odpowiedzi interfejsu API rangi.
    * Przechwyć wybór użytkownika i wyślij to w wywołaniu interfejsu API nagrody.

### <a name="recommended-bot-patterns"></a>Zalecane wzorce botów

* Make Personalizer Rank wywołane przez interfejs API za każdym razem, gdy wymagana jest niejednoznaczna, w przeciwieństwie do wyników buforowania dla każdego użytkownika. Wynik rozróżniania intencji może z czasem ulec zmianie dla jednej osoby, a zezwolenie interfejsowi API rangi na eksplorowanie wariancji przyspieszy ogólne uczenie się.
* Wybierz interakcję, która jest wspólna dla wielu użytkowników, aby mieć wystarczającą ilość danych do personalizacji. Na przykład pytania wprowadzające mogą być lepsze niż mniejsze wyjaśnienia głęboko na wykresie konwersacji, do których może dostać się tylko kilku użytkowników.
* Użyj wywołań interfejsu API rangi, aby włączyć konwersacje "pierwsza sugestia jest właściwa", w których użytkownik otrzymuje pytanie "Czy chcesz X?" lub "Czy miałeś na myśli X?" a użytkownik może po prostu potwierdzić; w przeciwieństwie do dawania opcji użytkownikowi, gdzie musi wybrać z menu. Na przykład Użytkownik:"Chciałbym zamówić kawę" Bot:"Czy chcesz podwójne espresso?". W ten sposób sygnał nagrody jest również silny, ponieważ odnosi się bezpośrednio do jednej sugestii.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak używać Personalizer z rozwiązaniem rekomendacji

Wiele firm korzysta z aparatów rekomendacji, narzędzi marketingowych i kampanijnych, segmentacji odbiorców i klastrowania, filtrowania opartego na współpracy i innych środków, aby polecać klientom produkty z dużego katalogu.

[Repozytorium GitHub firmy Microsoft rekomenduje](https://github.com/Microsoft/Recommenders) przykłady i najlepsze rozwiązania dotyczące tworzenia systemów rekomendacji, dostarczane jako notesy Jupyter. Zawiera przykłady pracy do przygotowywania danych, tworzenia modeli, oceny, strojenia i operacjonalizacji aparatów rekomendacji, dla wielu typowych podejść, w tym xDeepFM, SAR, ALS, RBM, DKN.

Personalizer może pracować z aparatem rekomendacji, gdy jest obecny.

* Aparaty rekomendacji przyjmują duże ilości elementów (na przykład 500 000) i polecają podzbiór (na przykład 20 najlepszych) z setek lub tysięcy opcji.
* Personalizer wykonuje niewielką liczbę akcji z dużą ilością informacji o nich i plasuje je w czasie rzeczywistym dla danego bogatego kontekstu, podczas gdy większość aparatów rekomendacji używa tylko kilku atrybutów dotyczących użytkowników, produktów i ich interakcji.
* Personalizer jest przeznaczony do autonomicznego eksplorowania preferencji użytkownika przez cały czas, co przyniesie lepsze wyniki tam, gdzie zawartość szybko się zmienia, takie jak wiadomości, wydarzenia na żywo, treści społeczności na żywo, treści z codziennymi aktualizacjami lub treści sezonowe.

Typowym zastosowaniem jest podjęcie danych wyjściowych aparatu rekomendacji (na przykład 20 najlepszych produktów dla określonego klienta) i użycie go jako akcji wejściowych dla Personalizer.

## <a name="adding-content-safeguards-to-your-application"></a>Dodawanie zabezpieczeń zawartości do aplikacji

Jeśli aplikacja pozwala na duże odchylenia w treści wyświetlane dla użytkowników, a niektóre z tych treści mogą być niebezpieczne lub nieodpowiednie dla niektórych użytkowników, należy zaplanować z wyprzedzeniem, aby upewnić się, że odpowiednie zabezpieczenia są w miejscu, aby uniemożliwić użytkownikom widzenie niedopuszczalnej zawartości. Najlepszym wzorcem do wdrożenia zabezpieczeń jest:
    * Uzyskaj listę akcji do rangi.
    * Odfiltruj te, które nie są opłacalne dla odbiorców.
    * Tylko rangi tych realnych działań.
    * Wyświetl użytkownikowi najwyższą akcję rankingowa.

W niektórych architekturach powyższej sekwencji może być trudne do zaimplementowania. W takim przypadku istnieje alternatywne podejście do wdrażania zabezpieczeń po rankingu, ale należy wprowadzić przepis, aby działania, które nie mieszczą się w zabezpieczeniach, nie były wykorzystywane do szkolenia modelu Personalizer.

* Uzyskaj listę akcji do rangi, z nauką dezaktywowane.
* Akcje rangi.
* Sprawdź, czy najważniejsze działanie jest opłacalne.
    * Jeśli najlepsze działanie jest opłacalne, aktywuj naukę dla tej rangi, a następnie pokaż ją użytkownikowi.
    * Jeśli najlepsze działanie nie jest opłacalne, nie należy aktywować uczenia się dla tego rankingu i zdecydować za pomocą własnej logiki lub alternatywnych podejść, co pokazać użytkownikowi. Nawet jeśli korzystasz z drugiej najlepszej opcji rankingowej, nie aktywuj nauki w tym rankingu.


## <a name="next-steps"></a>Następne kroki

[Etyka & odpowiedzialne użytkowanie.](ethics-responsible-use.md)
