---
title: Co to jest usługa Personalizacja?
description: Personalizacja to oparta na chmurze usługa interfejsu API, która pozwala na wybranie najlepszego środowiska, które ma być widoczne dla użytkowników, a także uczenie się przed zachowaniem ich w czasie rzeczywistym.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: cf046ada21c4920ea9e3853668a5928b2ca9f33a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586222"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Personalizacja Azure to oparta na chmurze usługa interfejsu API, która pomaga aplikacji klienckiej wybrać najlepszy, pojedynczy element _zawartości_ , aby wyświetlić każdego użytkownika. Usługa wybiera najlepszy element z elementów zawartości w oparciu o zbiorowe informacje w czasie rzeczywistym, które zapewnia zawartość i kontekst.

Po zaprezentowaniu elementu zawartości użytkownikowi system monitoruje zachowanie użytkownika i raportuje wynik odnoszący się do personalizacji, aby zwiększyć jego zdolność do wybierania najlepszej zawartości na podstawie otrzymanych informacji kontekstowych.

**Zawartość** może być dowolną jednostką informacji, taką jak tekst, obrazy, adresy URL lub wiadomości e-mail, które chcesz wybrać, aby pokazać użytkownikowi.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Jak Personalizował wybór najlepszego elementu zawartości?

Personalizacja korzysta z **uczenia wzmacniania** , aby wybrać najlepszy element (_akcję_) w oparciu o wspólne zachowanie i nagrody dla wszystkich użytkowników. Akcje to elementy zawartości, takie jak artykuły z wiadomościami, określone filmy lub produkty do wyboru.

Wywołanie **rangi** przyjmuje element akcji, a także funkcje akcji i funkcji kontekstu, aby wybrać górny element akcji:

* **Akcje z funkcjami** — elementy zawartości z funkcjami specyficznymi dla każdego elementu
* **Funkcje kontekstu** — funkcje użytkowników, ich kontekst lub środowisko w przypadku korzystania z aplikacji

Wywołanie rangi zwraca identyfikator, dla którego element zawartości, __akcję__, która ma być wyświetlana użytkownikowi, w polu **Identyfikator akcji nagrody** .
__Akcja__ pokazywana użytkownikowi jest wybierana z modelami uczenia maszynowego, co pozwala zwiększyć łączną ilość korzyści w czasie.

Oto kilka przykładowych scenariuszy:

|Typ zawartości|**Akcje (z funkcjami)**|**Funkcje kontekstu**|Identyfikator akcji zwrotu<br>(Wyświetl tę zawartość)|
|--|--|--|--|
|Lista wiadomości|a. `The president...`(National, polityka, [text])<br>b. `Premier League ...`(globalne, sportowe, [tekst, obraz, wideo])<br> c. `Hurricane in the ...`(regionalne, Pogoda, [tekst, obraz]|Wiadomości z urządzenia są odczytywane z<br>Miesiąc lub sezon<br>|z`The president...`|
|Lista filmów|1. `Star Wars` (1977, [Action, Adventure, fantastykę], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentacja, Sport], Steve Kuba<br>3. `Casablanca` (1942, [Romański, dramat, War], Michael Curtiz)|Film urządzenia jest oglądany z<br>rozmiar ekranu<br>Typ użytkownika<br>|r.3.`Casablanca`|
|Lista produktów|i. `Product A`(3 kg, $ $ $, dostarczenie w ciągu 24 godzin)<br>ii. `Product B`(20 kg, $ $, 2 tygodnie wysyłki z cłem)<br>iii. `Product C`(3 kg, $ $ $, dostarczenie w ciągu 48 godzin)|Odczytane są zakupy urządzeń<br>Warstwa wydatków użytkownika<br>Miesiąc lub sezon|ii. `Product B`|

Personalizacja wykorzystuje uczenie wzmacniające, aby wybrać jedną najlepszą akcję, znaną jako _premiowy Identyfikator akcji_, na podstawie kombinacji:
* Przeszkolony model — przeszła informacja, że odebrano usługę personalizacji
* Bieżące akcje specyficzne dla danych z funkcjami i funkcjami kontekstu

## <a name="when-to-call-personalizer"></a>Kiedy można wywołać personalizację

[Interfejs API](https://go.microsoft.com/fwlink/?linkid=2092082) **rangi** personalizacji jest wywoływany za _każdym razem, gdy_ zawartość jest wyświetlana w czasie rzeczywistym. Jest to znane jako **zdarzenie**z _identyfikatorem zdarzenia_.

[Interfejs API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **nagradzania** personalizacji może być wywoływany w czasie rzeczywistym lub w celu lepszego dopasowania do infrastruktury. Możesz określić wynik nagrody w zależności od potrzeb firmy. Wynik nagrody jest z zakresu od 0 do 1. Może to być pojedyncza wartość, taka jak 1 dla dobra, i 0 dla nieprawidłowych lub liczba generowana przez algorytm, który ma wpływ na cele biznesowe i metryki.

## <a name="personalizer-content-requirements"></a>Wymagania dotyczące zawartości dla personalizacji

Użyj personalizacji, gdy Twoja zawartość:

* Ma ograniczony zestaw elementów (maks. ~ 50) do wyboru. Jeśli masz większą listę, [Użyj aparatu rekomendacji](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) , aby zmniejszyć listę do 50 elementów.
* Zawiera informacje opisujące zawartość, którą chcesz klasyfikować: _akcje z funkcjami_ i _funkcjami kontekstu_.
* Ma co najmniej ~ KB/dzień zdarzeń związanych z zawartością dla personalizacji, aby można było efektywnie. Jeśli Personalizacja nie odbiera minimalnych wymagań dotyczących ruchu, usługa zajmuje więcej czasu na ustalenie pojedynczego elementu zawartości.

Ponieważ program Personalizujer używa informacji zbiorczych niemal w czasie rzeczywistym w celu zwrócenia jednego najlepszego elementu zawartości, usługa nie:
* Utrwalanie informacji o profilu użytkownika i zarządzanie nimi
* Rejestruj preferencje lub historię poszczególnych użytkowników
* Wymagaj oczyszczonej i oznaczonej etykietą zawartości

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Jak projektować i implementować Personalizowanie dla aplikacji klienckiej

1. [Projektuj](concepts-features.md) i planuj zawartość, **_Akcje_** i **_kontekst_**. Ustal algorytm nagrody dla wyniku **_nagrody_** .
1. Każdy utworzony [zasób personalizacji](how-to-settings.md) jest traktowany jako jedna pętla szkoleniowa. Pętla otrzyma zarówno wywołania rangi, jak i nagrody dla tej zawartości lub środowiska użytkownika.

    |Typ zasobu| Przeznaczenie|
    |--|--|
    |[Tryb praktykanta](concept-apprentice-mode.md)`E0`|Uczenie modelu personalizowania bez wpływu na istniejącą aplikację, a następnie wdrożenie go w celu uczenia się w trybie online w środowisku produkcyjnym|
    |Standardowa`S0`|Zachowanie uczenia online w środowisku produkcyjnym|
    |Zwolniony`F0`| Wypróbuj zachowanie uczenia w trybie online w środowisku nieprodukcyjnym|

1. Dodaj personalizację do swojej aplikacji, witryny sieci Web lub systemu:
    1. Dodaj wywołanie **rangi** do personalizacji w aplikacji, w witrynie sieci Web lub w systemie, aby określić najlepsze, pojedyncze elementy _zawartości_ przed wyświetleniem zawartości dla użytkownika.
    1. Wyświetlaj najlepsze, pojedyncze elementy _zawartości_ , które są ZWRÓCONYm _identyfikatorem akcji z wynagrodzeniem_do użytkownika.
    1. Zastosuj _logikę biznesową_ do zebranych informacji o sposobie zachowania użytkownika, aby określić wynik **nagrody** , taki jak:

    |Zachowanie|Obliczony wynik nagrody|
    |--|--|
    |Wybrany użytkownik: najlepszy, pojedynczy element _zawartości_ (Identyfikator akcji nagradzania)|**1**|
    |Użytkownik zaznaczył inną zawartość|**0**|
    |Użytkownik wstrzymał pracę, przewijając się w sposób niedecydujący, przed wybraniem najlepszego, pojedynczego elementu _zawartości_ (Identyfikator akcji nagradzania)|**0,5**|

    1. Dodaj wywołanie **zarobkowe** wysyłające wynik nagrody z zakresu od 0 do 1
        * Natychmiast po pokazywania zawartości
        * Lub w późniejszym czasie w systemie offline
    1. [Oceń pętlę](concepts-offline-evaluation.md) z oceną w trybie offline po upływie okresu użytkowania. Ocena w trybie offline umożliwia testowanie i ocenianie skuteczności usługi personalizacji bez zmiany kodu lub wpływu na środowisko użytkownika.

## <a name="next-steps"></a>Następne kroki


* [Jak działa usługa Personalizacja](how-personalizer-works.md)
* [Co to jest uczenie wzmacniania?](concepts-reinforcement-learning.md)
* [Informacje o funkcjach i akcjach dotyczących żądania rangi](concepts-features.md)
* [Dowiedz się więcej na temat określania wyniku żądania nagrody](concept-rewards.md)
* [Przewodniki Szybki start](sdk-learning-loop.md)
* [Samouczek](tutorial-use-azure-notebook-generate-loop-data.md)
* [Korzystanie z demonstracji interaktywnej](https://personalizationdemo.azurewebsites.net/)
