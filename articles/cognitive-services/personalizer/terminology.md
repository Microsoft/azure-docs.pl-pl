---
title: Terminologia — Personalizacja
description: Personalizowanie używa terminologii z uczenia wzmacniania. Te warunki są używane w Azure Portal i interfejsy API.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77624276"
---
# <a name="terminology"></a>Terminologia

Personalizowanie używa terminologii z uczenia wzmacniania. Te warunki są używane w Azure Portal i interfejsy API.

## <a name="conceptual-terminology"></a>Terminologia dotycząca pojęć

* **Pętla szkoleniowa**: tworzysz zasób narzędzia personalizacji o nazwie _Pętla szkoleniowa_dla każdej części aplikacji, która może korzystać z personalizacji. Jeśli masz więcej niż jedno środowisko do personalizacji, Utwórz pętlę dla każdego z nich.

* **Model**: model personalizowania przechwytuje wszystkie dane, które są uzyskiwane na temat zachowania użytkowników, uzyskiwanie danych szkoleniowych z kombinacji argumentów wysyłanych do rangi i płatnych wywołań oraz z zachowaniem szkolenia określonym przez zasady uczenia się.

## <a name="personalizer-configuration"></a>Konfiguracja personalizacji

Personalizacja jest konfigurowana z poziomu [Azure Portal](https://portal.azure.com).

* **Nagrody**: Skonfiguruj wartości domyślne dla nagrody czas oczekiwania, domyślne wynagrodzenie i nagrody agregacji.

* **Eksploracja**: Konfigurowanie wartości procentowej wywołań rangi do użycia podczas eksploracji

* **Częstotliwość aktualizacji modelu**: częstotliwość ponownego uczenia modelu.

* **Przechowywanie danych**: ile dni danych ma być przechowywanych. Może to mieć wpływ na oceny w trybie offline, które są używane do ulepszania pętli szkoleniowej.

## <a name="use-rank-and-reward-apis"></a>Korzystanie z interfejsów API rangi i nagrody

* **Ranga**: w przypadku akcji z funkcjami i funkcjami kontekstu użyj narzędzia Eksploruj lub Exploit, aby zwrócić górną akcję (element zawartości).

    * **Akcje**: akcje to elementy zawartości, takie jak produkty lub promocje, do wyboru. Personalizacja wybiera górną akcję (zwrotny Identyfikator akcji) do wyświetlenia użytkownikom za pośrednictwem interfejsu API rangi.

    * **Kontekst**: aby zapewnić dokładniejszą rangę, podaj informacje o Twoim kontekście, na przykład:
        * Użytkownik.
        * Urządzenie, na którym się znajdują.
        * Bieżący czas.
        * Inne dane dotyczące bieżącej sytuacji.
        * Dane historyczne dotyczące użytkownika lub kontekstu.

        Dana aplikacja może mieć różne informacje o kontekście.

    * **[Funkcje](concepts-features.md)**: jednostka informacji o elemencie zawartości lub kontekście użytkownika. Pamiętaj, aby używać tylko zagregowanych funkcji. Nie używaj określonych godzin, identyfikatorów użytkowników ani innych danych niezagregowanych jako funkcji.

        * _Funkcja akcji_ to metadane dotyczące zawartości.
        * _Funkcja kontekstu_ to metadane dotyczące kontekstu, w którym jest prezentowana zawartość.

* **Eksploracja**: usługa personalizacji szuka, gdy, zamiast zwracać najlepszą akcję, wybiera inną akcję dla użytkownika. Usługa personalizacji pozwala uniknąć dryfowania, stagnation i można dostosowywać do bieżących zachowań użytkowników przez Eksplorowanie.

* **Wykorzystanie**: usługa personalizujer korzysta z bieżącego modelu, aby określić najlepszą akcję na podstawie przeszłych danych.

* **Czas trwania eksperymentu**: ilość czasu, przez jaką usługa personalizacji czeka na wynagrodzenie, rozpoczynając od momentu wywołania rangi dla tego zdarzenia.

* **Zdarzenia nieaktywne**: zdarzenie nieaktywne to wystąpienie o nazwie rangi, ale nie masz pewności, że użytkownik zobaczy wynik z powodu decyzji aplikacji klienta. Nieaktywne zdarzenia umożliwiają tworzenie i przechowywanie wyników personalizacji, a następnie podjęcie decyzji o odrzuceniu ich później bez wpływu na model uczenia maszynowego.


* **Wynagrodzenie**: miara sposobu, w jaki użytkownik ODPOWIEDZIAŁ na identyfikator akcji w interfejsie API rangi, jako wynik z przedziału od 0 do 1. Wartość od 0 do 1 jest ustawiana przez logikę biznesową, w zależności od tego, jak wybór pozwala osiągnąć cele biznesowe personalizacji. Pętla szkoleniowa nie przechowuje tego wynagrodzenia jako pojedynczej historii użytkownika.

## <a name="offline-evaluations"></a>Oceny w trybie offline

* **Ocena**: Ocena w trybie offline określa najlepsze zasady uczenia dla pętli na podstawie danych pętli.

* **Zasady uczenia**: jak program Personalizuj pociąga za siebie model dla każdego zdarzenia, będzie określać niektóre parametry, które wpływają na działanie algorytmu uczenia maszynowego. Nowa pętla szkoleniowa rozpoczyna się od domyślnych **zasad uczenia**, co może spowodować umiarkowaną wydajność. Podczas przeprowadzania [obliczeń](concepts-offline-evaluation.md)program Personalizuj tworzy nowe zasady uczenia, które zostały zoptymalizowane pod kątem użycia pętli. Personalizacja będzie działać znacznie lepiej dzięki zasadom zoptymalizowanym dla każdej konkretnej pętli, generowanej podczas obliczania. Zasady uczenia są nazywane _ustawieniami uczenia_ w **ustawieniach model i nauka** dla zasobu Personalizacja w Azure Portal.