---
title: Co to jest usługa Personalizacja?
description: Personalizacja to usługa oparta na chmurze, która umożliwia wybranie najlepszego środowiska, które ma być widoczne dla użytkowników, a także uczenie się od ich zachowania w czasie rzeczywistym.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: Personalizacja, Personalizacja platformy Azure, uczenie maszynowe
ms.openlocfilehash: b2577502907b69e134651c93ab7a98fc51e9aaa6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169253"
---
# <a name="what-is-personalizer"></a>Co to jest usługa Personalizacja?

Personalizacja Azure to usługa oparta na chmurze, która ułatwia aplikacjom wybranie najlepszego elementu zawartości do wyświetlania użytkowników. Możesz użyć usługi personalizacji, aby określić, który produkt ma zasugerować kupujący, lub ustalić optymalną pozycję anonsu. Gdy zawartość jest pokazywana użytkownikowi, aplikacja monitoruje jej reakcji i raportuje wynik z odliczenia do usługi personalizacji. Pozwala to na ciągłe ulepszanie modelu uczenia maszynowego i personalizowanie możliwości wybierania najlepszego elementu zawartości na podstawie informacji kontekstowych, które otrzymuje.

> [!TIP]
> Zawartość to dowolna jednostka informacji, taka jak tekst, obrazy, adresy URL, wiadomości e-mail lub inne elementy, które chcesz wybrać i pokazać użytkownikom.

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki [**Szybki Start**](quickstart-personalizer-sdk.md) to instrukcje umożliwiające wykonywanie żądań do usługi.  
* [**Przewodniki z**](how-to-settings.md) instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.  
* [**Koncepcje**](how-personalizer-works.md) zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.  
* [**Samouczki**](tutorial-use-personalizer-web-app.md) są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.  

Przed rozpoczęciem Wypróbuj program [Personalizuj z tym interaktywną prezentacją](https://personalizationdemo.azurewebsites.net/).

## <a name="how-does-personalizer-select-the-best-content-item"></a>Jak Personalizował wybór najlepszego elementu zawartości?

Personalizacja korzysta z **uczenia wzmacniania** , aby wybrać najlepszy element (_akcję_) w oparciu o wspólne zachowanie i nagrody dla wszystkich użytkowników. Akcje to elementy zawartości, takie jak artykuły z wiadomościami, określone filmy lub produkty.

Wywołanie **rangi** przyjmuje element akcji, a także funkcje akcji i funkcji kontekstu, aby wybrać górny element akcji:

* **Akcje z funkcjami** — elementy zawartości z funkcjami specyficznymi dla każdego elementu
* **Funkcje kontekstu** — funkcje użytkowników, ich kontekst lub środowisko w przypadku korzystania z aplikacji

Wywołanie rangi zwraca identyfikator, dla którego element zawartości, __akcję__, która ma być wyświetlana użytkownikowi, w polu **Identyfikator akcji nagrody** .

__Akcja__ pokazywana użytkownikowi jest wybierana z modelami uczenia maszynowego, które próbują zmaksymalizować łączną ilość korzyści w czasie.

### <a name="sample-scenarios"></a>Przykładowe scenariusze

Przyjrzyjmy się kilku scenariuszom, w których można użyć personalizacji, aby wybrać najlepszą zawartość do renderowania dla użytkownika.

|Typ zawartości|Akcje (z funkcjami)|Funkcje kontekstu|Identyfikator akcji zwrotu<br>(Wyświetl tę zawartość)|
|--|--|--|--|
|Lista wiadomości|a. `The president...` (National, polityka, [text])<br>b. `Premier League ...` (globalne, sportowe, [tekst, obraz, wideo])<br> c. `Hurricane in the ...` (regionalne, Pogoda, [tekst, obraz]|Wiadomości z urządzenia są odczytywane z<br>Miesiąc lub sezon<br>|z `The president...`|
|Lista filmów|1. `Star Wars` (1977, [Action, Adventure, fantastykę], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentacja, Sport], Steve Kuba<br>3. `Casablanca` (1942, [Romański, dramat, War], Michael Curtiz)|Film urządzenia jest oglądany z<br>rozmiar ekranu<br>Typ użytkownika<br>|r.3. `Casablanca`|
|Lista produktów|i. `Product A` (3 kg, $ $ $, dostarczenie w ciągu 24 godzin)<br>ii. `Product B` (20 kg, $ $, 2 tygodnie wysyłki z cłem)<br>iii. `Product C` (3 kg, $ $ $, dostarczenie w ciągu 48 godzin)|Odczytane są zakupy urządzeń<br>Warstwa wydatków użytkownika<br>Miesiąc lub sezon|ii. `Product B`|

Personalizacja wykorzystuje uczenie wzmacniające, aby wybrać jedną najlepszą akcję, znaną jako _premiowy Identyfikator akcji_. Model uczenia maszynowego używa: 

* Przeszkolony model — informacje otrzymane wcześniej od usługi personalizacji używane do ulepszania modelu uczenia maszynowego
* Bieżące akcje specyficzne dla danych z funkcjami i funkcjami kontekstu

## <a name="when-to-use-personalizer"></a>Kiedy używać personalizacji

[Interfejs API](https://go.microsoft.com/fwlink/?linkid=2092082) **rangi** personalizacji jest wywoływany za każdym razem, gdy aplikacja wyświetla zawartość. Jest to znane jako **zdarzenie** z _identyfikatorem zdarzenia_.

[Interfejs API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **nagradzania** personalizacji może być wywoływany w czasie rzeczywistym lub w celu lepszego dopasowania do infrastruktury. Możesz określić wynik nagrody w zależności od potrzeb firmy. Wynik nagrody jest z zakresu od 0 do 1. Może to być pojedyncza wartość, taka jak 1 dla dobra, i 0 dla nieprawidłowych lub liczba generowana przez algorytm, który ma wpływ na cele biznesowe i metryki.

## <a name="content-requirements"></a>Wymagania dotyczące zawartości

Użyj personalizacji, gdy Twoja zawartość:

* Zawiera ograniczony zestaw akcji lub elementów (maks. ~ 50) do wyboru spośród poszczególnych zdarzeń personalizacji. Jeśli masz większą listę, [Użyj aparatu rekomendacji](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) , aby zmniejszyć listę elementów do 50 dla każdego wywołania rangi w usłudze personalizacji.
* Zawiera informacje opisujące zawartość, którą chcesz klasyfikować: _akcje z funkcjami_ i _funkcjami kontekstu_.
* Ma co najmniej ~ KB/dzień zdarzeń związanych z zawartością dla personalizacji, aby można było efektywnie. Jeśli Personalizacja nie odbiera minimalnych wymagań dotyczących ruchu, usługa zajmuje więcej czasu na ustalenie pojedynczego elementu zawartości.

Ponieważ program Personalizujer używa informacji zbiorczych niemal w czasie rzeczywistym w celu zwrócenia jednego najlepszego elementu zawartości, usługa nie:
* Utrwalanie informacji o profilu użytkownika i zarządzanie nimi
* Rejestruj preferencje lub historię poszczególnych użytkowników
* Wymagaj oczyszczonej i oznaczonej etykietą zawartości

## <a name="how-to-design-for-and-implement-personalizer"></a>Jak projektować i implementować personalizację

1. [Projektuj](concepts-features.md) i planuj zawartość, **_Akcje_** i **_kontekst_**. Ustal algorytm nagrody dla wyniku **_nagrody_** .
1. Każdy utworzony [zasób personalizacji](how-to-settings.md) jest traktowany jako jedna pętla szkoleniowa. Pętla otrzyma zarówno wywołania rangi, jak i nagrody dla tej zawartości lub środowiska użytkownika.

    |Typ zasobu| Przeznaczenie|
    |--|--|
    |[Tryb ucznia](concept-apprentice-mode.md) `E0`|Uczenie modelu personalizowania bez wpływu na istniejącą aplikację, a następnie wdrożenie go w celu uczenia się w trybie online w środowisku produkcyjnym|
    |Standardowa `S0`|Zachowanie uczenia online w środowisku produkcyjnym|
    |Zwolniony `F0`| Wypróbuj zachowanie uczenia w trybie online w środowisku nieprodukcyjnym|

1. Dodaj personalizację do swojej aplikacji, witryny sieci Web lub systemu:
    1. Dodaj wywołanie **rangi** do personalizacji w aplikacji, w witrynie sieci Web lub w systemie, aby określić najlepsze, pojedyncze elementy _zawartości_ przed wyświetleniem zawartości dla użytkownika.
    1. Wyświetlaj najlepsze, pojedyncze elementy _zawartości_ , które są ZWRÓCONYm _identyfikatorem akcji z wynagrodzeniem_ do użytkownika.
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

## <a name="reference"></a>Odwołanie 

* [Personalizacja C#/.NET SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Zestaw SDK go personalizacji](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Personalizacja JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Zestaw SDK języka Python dla personalizacji](/python/api/overview/azure/cognitiveservices/personalizer)
* [Interfejsy API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak działa](how-personalizer-works.md) 
>  Personalizacja [Co to jest uczenie wzmacniania?](concepts-reinforcement-learning.md)
