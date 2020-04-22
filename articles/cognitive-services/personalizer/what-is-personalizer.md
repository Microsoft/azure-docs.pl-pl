---
title: Co to jest usługa Personalizacja?
description: Personalizer to usługa interfejsu API oparta na chmurze, która pozwala wybrać najlepsze środowisko do wyświetlenia użytkownikom, ucząc się na podstawie ich zachowania w czasie rzeczywistym.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 3ae425479d764c0a6bf6c63bdd54a964c48af8b6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687260"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer to usługa interfejsu API oparta na chmurze, która pomaga aplikacji klienckiej wybrać najlepszy, pojedynczy element _zawartości,_ aby pokazać każdemu użytkownikowi. Usługa wybiera najlepszy element z elementów zawartości, na podstawie zbiorczych informacji w czasie rzeczywistym, które podajesz o zawartości i kontekście.

Po przedstawieniu elementu zawartości użytkownikowi system monitoruje zachowanie użytkownika i zgłasza wynik nagrody z powrotem do Personalizer, aby poprawić jego możliwość wyboru najlepszej zawartości na podstawie informacji kontekstowych, które otrzymuje.

**Zawartość** może być dowolną jednostką informacji, takich jak tekst, obrazy, adresy URL lub wiadomości e-mail, które chcesz wybrać, aby wyświetlić użytkownikowi.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>W jaki sposób Personalizer wybiera najlepszy element zawartości?

Personalizer wykorzystuje **wzmocnienie uczenia się,** aby wybrać najlepszy element _(działanie_) na podstawie zbiorowego zachowania i nagrody wyniki we wszystkich użytkowników. Akcje to elementy zawartości, takie jak artykuły z wiadomościami, określone filmy lub produkty do wyboru.

Wezwanie **rangi** przyjmuje element akcji wraz z funkcjami akcji i funkcjami kontekstu, aby wybrać górny element akcji:

* **Akcje z funkcjami** - elementy zawartości z funkcjami specyficznymi dla każdego elementu
* **Funkcje kontekstowe** — funkcje użytkowników, ich kontekst lub środowisko podczas korzystania z aplikacji

Wywołanie rangi zwraca identyfikator elementu zawartości, __akcję,__ aby pokazać użytkownikowi, w polu **Identyfikator akcji nagrody.**
__Akcja__ wyświetlana użytkownikowi jest wybierana za pomocą modeli uczenia maszynowego, starając się zmaksymalizować całkowitą liczbę nagród w czasie.

Kilka przykładowych scenariuszy to:

|Typ zawartości|**Akcje (z funkcjami)**|**Funkcje kontekstowe**|Identyfikator akcji zwracana nagroda<br>(wyświetl tę zawartość)|
|--|--|--|--|
|Lista wiadomości|a. `The president...`(krajowy, polityczny, [tekst])<br>b. `Premier League ...`(globalny, sportowy, [tekst, obraz, wideo])<br> d. `Hurricane in the ...`(regionalny, pogoda, [tekst,obraz]|Wiadomości o urządzeniu są odczytywane z<br>Miesiąc lub pora roku<br>|A`The president...`|
|Lista filmów|1. `Star Wars` (1977, [akcja, przygoda, fantazja], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentalny, sportowy], Steve James<br>3. `Casablanca` (1942, [romans, dramat, wojna], Michael Curtiz)|Film na urządzeniu jest oglądany z<br>rozmiar ekranu<br>Typ użytkownika<br>|3.`Casablanca`|
|Lista produktów|i. `Product A`(3 kg, $$$$, dostawa w ciągu 24 godzin)<br>ii. `Product B`(20 kg, $$, 2 tygodniowa wysyłka z cłami)<br>iii. `Product C`(3 kg, $$$, dostawa w 48 godzin)|Zakupy na urządzeniu odczytuje się z<br>Warstwa wydatków użytkownika<br>Miesiąc lub pora roku|ii. `Product B`|

Personalizer wykorzystał uczenie wzmacniające, aby wybrać jedną najlepszą akcję, znaną jako _identyfikator akcji nagrody,_ w oparciu o kombinację:
* Wyszkolony model - wcześniejsze informacje otrzymane przez usługę Personalizer
* Bieżące dane — określone akcje z funkcjami i funkcjami kontekstu

## <a name="when-to-call-personalizer"></a>Kiedy zadzwonić do Personalizer

Personalizer's **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) jest wywoływana za _każdym razem, gdy_ prezentujesz zawartość w czasie rzeczywistym. Jest to znane jako **zdarzenie,** zauważyć o _identyfikatorze zdarzenia_.

**Personalizer's Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) można wywołać w czasie rzeczywistym lub opóźnione, aby lepiej dopasować swoją infrastrukturę. Określasz wynik nagrody na podstawie potrzeb biznesowych. Wynik nagrody wynosi od 0 do 1. Może to być pojedyncza wartość, taka jak 1 na dobre i 0 na złe lub liczba wyprodukowana przez algorytm utworzony z uwzględnieniem celów biznesowych i metryk.

## <a name="personalizer-content-requirements"></a>Wymagania dotyczące zawartości personalizatora

Używaj Personalizera, gdy zawartość:

* Ma ograniczony zestaw elementów (maks. ~50) do wyboru. Jeśli masz większą listę, [użyj aparatu rekomendacji,](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) aby zmniejszyć listę do 50 elementów.
* Zawiera informacje opisujące zawartość, którą chcesz uszeregować: _akcje z funkcjami_ i _funkcjami kontekstu_.
* Ma co najmniej ~ 1k / dzień zdarzenia związane z zawartością personalizatora być skuteczne. Jeśli Personalizer nie otrzyma minimalnego ruchu wymagane, usługa trwa dłużej, aby określić pojedynczy najlepszy element zawartości.

Ponieważ Personalizer używa informacji zbiorczych w czasie zbliżonym do rzeczywistego, aby zwrócić pojedynczy najlepszy element zawartości, usługa nie:
* Utrwalanie informacji o profilu użytkownika i zarządzanie nimi
* Rejestrowanie preferencji lub historii poszczególnych użytkowników
* Wymagaj wyczyszczonej i oznaczonej zawartości

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Jak zaprojektować i wdrożyć Personalizer dla aplikacji klienckiej

1. [Projektowanie](concepts-features.md) i planowanie zawartości, **_akcji_** i **_kontekstu_**. Określ algorytm nagrody dla wyniku **_nagrody._**
1. Każdy [utworzony zasób personalizatora](how-to-settings.md) jest uważany za 1 pętlę szkoleniową. Pętla otrzyma zarówno rangi i nagrody wzywa do tej zawartości lub doświadczenia użytkownika.
1. Dodaj Personalizer do swojej witryny lub systemu treści:
    1. Dodaj wywołania **Ranga** do Personalizer w aplikacji, witrynie sieci Web lub systemie, aby określić najlepszy, pojedynczy element _zawartości_ przed zawartości jest wyświetlany użytkownikowi.
    1. Wyświetl najlepszy, pojedynczy element _zawartości,_ który jest zwróconym _identyfikatorem akcji nagrody_dla użytkownika.
    1. Zastosuj _algorytm_ do zebranych informacji o zachowaniu użytkownika, aby określić wynik **nagrody,** takich jak:

        |Zachowanie|Obliczony wynik nagrody|
        |--|--|
        |Wybrany przez użytkownika najlepszy, pojedynczy element _zawartości_ (identyfikator akcji nagrody)|**1**|
        |Użytkownik wybrał inną zawartość|**0**|
        |Użytkownik wstrzymany, przewijając się niezdecydowanie, przed wybraniem najlepszego, pojedynczego elementu _zawartości_ (identyfikator akcji nagrody)|**0,5**|

    1. Dodaj połączenie **zaagrosowe,** wysyłając wynik nagrody z 0 do 1
        * Natychmiast po wyświetlenie zawartości
        * Lub jakiś czas później w systemie offline
    1. [Oceń swoją pętlę](concepts-offline-evaluation.md) za pomocą oceny w trybie offline po okresie użytkowania. Ocena w trybie offline pozwala przetestować i ocenić skuteczność Usługi Personalizer bez zmiany kodu lub wpływu na środowisko użytkownika.

## <a name="next-steps"></a>Następne kroki


* [Jak działa usługa Personalizacja](how-personalizer-works.md)
* [Co to jest uczenie się wzmacniania?](concepts-reinforcement-learning.md)
* [Dowiedz się więcej o funkcjach i akcjach żądania rangi](concepts-features.md)
* [Dowiedz się więcej o określaniu wyniku dla prośby o nagrodę](concept-rewards.md)
* [Przewodniki Szybki Start](sdk-learning-loop.md)
* [Samouczek](tutorial-use-azure-notebook-generate-loop-data.md)
* [Korzystanie z interaktywnego dema](https://personalizationdemo.azurewebsites.net/)
