---
title: Wynik nagrody — Personalizacja
description: Wynik nagrody wskazuje, jak dobrze wybrana została opcja personalizacji, RewardActionID, czego dotyczy użytkownik. Wartość wyniku nagrody jest określana na podstawie logiki biznesowej w oparciu o obserwacje zachowania użytkownika. Personalizacja pociąga za siebie modele uczenia maszynowego, oceniając nagrody.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365571"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Wyniki nagrody wskazują na pomyślne personalizację

Wynik nagrody wskazuje, jak dobrze wybrana została opcja personalizacji, [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response), czego dotyczy użytkownik. Wartość wyniku nagrody jest określana na podstawie logiki biznesowej w oparciu o obserwacje zachowania użytkownika.

Personalizacja pociąga za siebie modele uczenia maszynowego, oceniając nagrody.

Dowiedz się, [jak](how-to-settings.md#configure-rewards-for-the-feedback-loop) skonfigurować domyślny wskaźnik nagrody w Azure Portal dla zasobu personalizacji.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Skorzystaj z funkcji API nagradzania, aby wysłać wynik nagrody do personalizacji

Nagrody są wysyłane do programu Personalizacja przez [interfejs API nagradzania](/rest/api/cognitiveservices/personalizer/events/reward). Zazwyczaj jest to liczba od 0 do 1. W niektórych scenariuszach jest możliwe uzyskanie ujemnego wynagrodzenia o wartości-1, które należy stosować tylko wtedy, gdy masz doświadczenie w nauce wzmacniania (RL). Personalizacja pociąga za model, aby osiągnąć najwyższą możliwą sumę korzyści w czasie.

Nagrody są wysyłane po zakończeniu działania użytkownika, co może potrwać kilka dni później. Maksymalny czas, przez który personalizowa, będzie oczekiwać do momentu, gdy zdarzenie zostanie uznane za niepłatne lub domyślne wynagrodzenie zostanie skonfigurowane z [wynagrodzeniem czas oczekiwania](#reward-wait-time) w Azure Portal.

Jeśli wynik nagrody dla zdarzenia nie został odebrany w **czasie oczekiwania na wynagrodzenie** , zostanie zastosowane **domyślne wynagrodzenie** . Zazwyczaj **[domyślne wynagrodzenie](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** jest skonfigurowane jako zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Zachowania i dane, które należy wziąć pod uwagę za nagrody

Należy wziąć pod uwagę te sygnały i zachowania dla kontekstu wyniku nagrody:

* Bezpośrednie dane wejściowe użytkownika dla sugestii, gdy są one związane z opcjami ("czy oznaczasz X?").
* Długość sesji.
* Czas między sesjami.
* Tonacji analizę interakcji użytkownika.
* Bezpośrednie pytania i praktyczne badania, w których bot prosi użytkownika o opinię na temat użyteczności, dokładności.
* Odpowiedź na alerty lub opóźnienie odpowiedzi na alerty.

## <a name="composing-reward-scores"></a>Tworzenie ocen nagrody

Wynik nagrody musi być obliczany w logice biznesowej. Wynik może być reprezentowany jako:

* Pojedyncza liczba wysłana raz
* Wynik jest wysyłany natychmiast (na przykład 0,8) i dodatkowym wynikiem wysłanym później (zazwyczaj 0,2).

## <a name="default-rewards"></a>Nagrody domyślne

Jeśli nie otrzymasz nagrody w [czasie oczekiwania na wynagrodzenie](#reward-wait-time), czas trwania od wywołania rangi, personalizujer niejawnie stosuje **wynagrodzenie domyślne** do tego zdarzenia rangi.

## <a name="building-up-rewards-with-multiple-factors"></a>Tworzenie nagrody z wieloma czynnikami

Aby uzyskać efektywne personalizacje, można utworzyć wynik nagrody na podstawie wielu czynników.

Można na przykład zastosować te reguły do personalizowania listy zawartości wideo:

|Zachowanie użytkownika|Wartość wyniku częściowego|
|--|--|
|Użytkownik kliknął górny element.|+ 0,5|
|Użytkownik otworzył rzeczywistą zawartość tego elementu.|+ 0,3|
|Użytkownik oglądał 5 minut zawartości lub 30%, w zależności od tego, co jest dłuższe.|+ 0,2|
|||

Następnie można wysłać łączny zarobk do interfejsu API.

## <a name="calling-the-reward-api-multiple-times"></a>Wielokrotnie wywoływanie interfejsu API nagradzania

Możesz również wywołać interfejs API nagradzania przy użyciu tego samego identyfikatora zdarzenia, wysyłając różne wyniki. Gdy Personalizowanie uzyska te korzyści, określa ostateczną opłatą dla tego zdarzenia poprzez agregowanie ich w sposób określony w konfiguracji personalizacji.

Wartości agregacji:

*  **Pierwszy** : Pobiera wynik pierwszej nagrody dla zdarzenia i odrzuca resztę.
* **Sum** : Pobiera wszystkie wyniki zarobkowe zebrane dla EventId i dodaje je razem.

Wszystkie nagrody dla zdarzenia, które są odbierane po upływie **czasu oczekiwania** , są odrzucane i nie mają wpływu na szkolenia modeli.

Po dodaniu uzyskanych wyników ostateczne wynagrodzenie może być poza oczekiwanym zakresem wyników. Nie spowoduje to błędu usługi.

## <a name="best-practices-for-calculating-reward-score"></a>Najlepsze rozwiązania dotyczące obliczania wyniku nagrody

* **Weź pod uwagę prawdziwe wskaźniki pomyślnej personalizacji** : łatwo jest myśleć o kliknięciach, ale dobre wynagrodzenie opiera się na tym, co użytkownicy chcą *osiągnąć* , a nie na tym, co chcesz *robić*.  Na przykład nagradzanie kliknięć może prowadzić do wyboru zawartości, która jest podatna na clickbait.

* **Skorzystaj z nagrody dotyczącej poprawnego działania personalizacji** : Personalizowanie sugestii filmu miejmy nadzieję wyniki oglądania filmu i nadając mu wysoką ocenę. Ponieważ klasyfikacja filmu prawdopodobnie zależy od wielu rzeczy (jakości działania, nastrój użytkownika), nie jest to dobry sygnał na to, jak dobrze działa *Personalizacja* . Użytkownik oglądał pierwsze kilka minut filmu, jednak może być lepszym sygnałem skuteczności personalizacji i wysłaniem nagrody 1 po 5 minutach będzie lepszym sygnałem.

* **Wynagrodzenie dotyczy tylko RewardActionID** : personalizujer stosuje nagrody, aby zrozumieć skuteczność działania określonego w RewardActionID. Jeśli zdecydujesz się wyświetlić inne akcje, a użytkownik kliknie je, wynagrodzenie powinno mieć wartość zero.

* **Rozważ niezamierzone konsekwencje** : Utwórz funkcje nagradzania, które prowadzą do odpowiedzialnych wyników z [etyką i właściwymi do użycia](ethics-responsible-use.md).

* **Korzystaj z korzyści przyrostowych** : dodanie częściowego nagrody dla mniejszych zachowań użytkownika ułatwia personalizację w celu osiągnięcia lepszych korzyści. Ten Przyrostowy wzrost umożliwia algorytmowi uzyskanie bliższego zaangażowania użytkownika w końcowe żądane zachowanie.
    * Jeśli zostanie wyświetlona lista filmów, jeśli użytkownik umieści wskaźnik myszy nad pierwszym przez jakiś czas, aby wyświetlić więcej informacji, można określić, że wystąpił pewne zaangażowanie użytkownika. Zachowanie może być liczone z wynikiem nagrody wynoszącym 0,1.
    * Jeśli użytkownik otworzył stronę, a następnie zakończył działanie, wynik nagrody może być 0,2.

## <a name="reward-wait-time"></a>Nagradzany czas oczekiwania

Personalizacja będzie skorelować informacje o randze wywołania z wynagrodzeniem wysyłanym w nagradzaniu, aby szkolić model. Mogą one występować w różnych godzinach. Personalizowanie czeka przez ograniczony czas, rozpoczynając od wywołania rangi, nawet jeśli wywołanie rangi zostało wykonane jako zdarzenie nieaktywne i aktywowane później.

Jeśli nastąpi **przekroczenie czasu oczekiwania** i nie otrzymasz żadnych informacji o tym zdarzeniu, w przypadku szkolenia zostanie zastosowane domyślne wynagrodzenie. Maksymalny czas oczekiwania wynosi 6 dni.

## <a name="best-practices-for-reward-wait-time"></a>Najlepsze rozwiązania związane z nagrodami czasu oczekiwania

Postępuj zgodnie z tymi zaleceniami, aby uzyskać lepsze wyniki.

* Zwiększ czas oczekiwania, tak jak to możliwe, przy jednoczesnym pozostawieniu wystarczająco dużo czasu, aby uzyskać opinię użytkownika.

* Nie wybieraj czasu trwania, który jest krótszy niż czas potrzebny do uzyskania opinii. Na przykład jeśli pewne nagrody są dostępne po wykonaniu przez użytkownika 1 minuty filmu wideo, Długość eksperymentu powinna wynosić co najmniej dwa razy.

## <a name="next-steps"></a>Następne kroki

* [Uczenie przez wzmacnianie](concepts-reinforcement-learning.md)
* [Wypróbuj interfejs API rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Wypróbuj interfejs API nagradzania](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)