---
title: Tryb praktykanta — Personalizacja
description: Dowiedz się, jak używać trybu programu w celu uzyskania zaufania do modelu bez zmiany kodu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100385034"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Używanie trybu programu w celu uczenia personalizacji bez wpływu na istniejącą aplikację

Ze względu na charakter **rzeczywistej** nauki wzmacniania model personalizowania może być szkolony tylko w środowisku produkcyjnym. W przypadku wdrażania nowego przypadku użycia model personalizowania nie działa efektywnie, ponieważ trwa wystarczająco szczegółowe uczenie modelu.  **Tryb** współdziałania to zachowanie uczenia, które upraszcza tę sytuację i umożliwia uzyskanie pewności w modelu — bez konieczności zmiany kodu przez dewelopera.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Co to jest tryb praktykant?

Podobnie jak w przypadku, gdy praktykant uczy się od wzorca i środowisko może być lepsze. Tryb współdziałania to _zachowanie_ , które umożliwia Personalizowanie informacji przez zaobserwowanie wyników uzyskanych z istniejącej logiki aplikacji.

Personalizacja pociąga przez naśladując te same dane wyjściowe co aplikacja. Podobnie jak przepływ więcej zdarzeń, Personalizowanie może _przechwycić_ do istniejącej aplikacji bez wpływu na istniejącą logikę i wyniki. Metryki dostępne w Azure Portal i interfejsie API ułatwiają zrozumienie wydajności w miarę zdobywania modeli.

Po uzyskaniu informacji przez program Personalizuj i osiągnięciu określonego poziomu wiedzy deweloper może zmienić zachowanie trybu usługi w trybie online. W tym czasie program Personalizujer zacznie wpływać na działania w interfejsie API rangi.

## <a name="purpose-of-apprentice-mode"></a>Cel trybu działania

Tryb współdziałania daje zaufanie do usługi personalizacji oraz jej możliwości uczenia maszynowego i zapewnia gwarancję, że usługa jest wysyłana do informacji, które mogą być uzyskane z programu — bez ryzyka związanego z ruchem online.

Oto dwa główne przyczyny używania trybu praktykant:

* Eliminowanie **zimnego startu**: tryb podmiotu pomaga zarządzać kosztami "nowego" czasu uczenia modelu i oceniać je, gdy nie zwróci najlepszej akcji i nie osiągnął zadowalającego poziomu skuteczności około 60-80%.
* **Sprawdzanie poprawności funkcji akcji i kontekstu**: funkcje wysyłane w akcjach i kontekście mogą być nieodpowiednie lub niedokładne — zbyt małe, zbyt duże, nieprawidłowe lub zbyt specyficzne dla narzędzia do uczenia się, aby osiągnąć idealny wskaźnik wydajności. Za pomocą funkcji [oceny funkcji](concept-feature-evaluation.md) można znajdować i rozwiązywać problemy z funkcjami.

## <a name="when-should-you-use-apprentice-mode"></a>Kiedy należy używać trybu praktykant?

Użyj trybu podmiotu w celu pouczenia się narzędzia personalizacji w celu poprawy jego wydajności w następujących scenariuszach, pozostawiając użytkownikom niezależnym przez program Personalizuj:

* Wdrażasz personalizację w nowym przypadku użycia.
* Znacznie zmieniono funkcje wysyłane w kontekście lub akcjach.
* Znacznie zmieniono czas i sposób obliczania nagrody.

Tryb współdziałania nie jest skutecznym sposobem mierzenia poziomu personalizacji wpływu na wyniki. Aby zmierzyć, jak skuteczny program personalizuje wybiera najlepszą możliwą akcję dla każdego wywołania rangi, użyj [ocen w trybie offline](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Kto powinien korzystać z trybu praktykant?

Tryb działalności jest przydatny dla deweloperów, naukowców danych i osób podejmujących decyzje biznesowe:

* **Deweloperzy** mogą korzystać z trybu służbowego, aby upewnić się, że interfejsy API rangi i nagrody są poprawnie używane w aplikacji, a funkcje wysyłane do narzędzia personalizacji z aplikacji nie zawierają błędów lub nieodpowiednich funkcji, takich jak sygnatura czasowa lub element identyfikatora użytkownika.

* Analitycy **danych** mogą używać trybu programu w celu sprawdzenia, czy funkcje są skuteczne do uczenia modeli personalizacji, że nagrody czasu oczekiwania nie są zbyt długie lub krótkie.

* Osoby **podejmujące decyzje biznesowe** mogą używać trybu podmiotu do oceny potencjału personalizacji w celu poprawy wyników (tj. korzyści) w porównaniu z istniejącą logiką biznesową. Dzięki temu mogą oni podejmować świadome decyzje mające wpływ na środowisko użytkownika, w którym realne dochody i zadowolenie użytkowników są w danej chwili.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Porównanie zachowań — tryb działania i tryb online

Uczenie się w trybie pracy różni się od trybu online w następujący sposób.

|Warstwowy|Tryb ucznia|Tryb online|
|--|--|--|
|Wpływ na środowisko użytkownika|Możesz użyć istniejącego zachowania użytkownika w celu pouczenia personalizacji, zezwalając na przestrzeganie (nie ma wpływu na to, jakie **działania domyślne** byłyby, a uzyskaną przez niego opłatą). Oznacza to, że nie będzie to miało wpływu na wrażenia użytkowników i wyniki biznesowe.|Wyświetl najwyższą akcję zwróconą z wywołania rangi w celu wpływu na zachowanie użytkownika.|
|Szybkość uczenia|Personalizowanie będzie bardziej wolniejsze w trybie zawodnym niż w przypadku uczenia się w trybie online. Tryb działania może uczyć się tylko przez zaobserwowanie korzyści uzyskanych przez **akcję domyślną**, co ogranicza szybkość uczenia się, ponieważ nie można przeprowadzić eksploracji.|Szybsze uczenie się, ponieważ może wykorzystać bieżący model i poznać nowe trendy.|
|"Pułap" skuteczności uczenia się|Personalizowanie może być przybliżone, bardzo rzadko dopasowywane, a nigdy nie przekracza wydajności podstawowej logiki biznesowej (suma uzyskana przez **domyślną akcję** każdego wywołania rangi). Ten górny limit jest redukowany przez eksplorację. Na przykład w przypadku eksploracji o 20% jest bardzo mało prawdopodobne, że wydajność trybu działania będzie przekroczyć 80%, a 60% to rozsądny element docelowy, w którym można przeprowadzić stopniowe skalowanie do trybu online.|Program Personalizuj powinien przekroczyć linię bazową aplikacji, a w czasie, w którym zajdzie taka potrzeba, należy przeprowadzić ocenę w trybie offline i ocenę funkcji, aby nadal uzyskać ulepszenia modelu. |
|Wartość interfejsu API rangi dla rewardActionId|Nie ma to wpływu na środowisko użytkownika, ponieważ _rewardActionId_ jest zawsze pierwszą akcją wysłaną w żądaniu rangi. Innymi słowy, interfejs API rangi nie jest widoczny dla aplikacji w trybie wykonywania. Nagradzanie interfejsów API w aplikacji nie powinno zmieniać sposobu korzystania z interfejsu API nagradzania między jednym trybem a drugim.|Środowisko użytkownika zostanie zmienione przez _rewardActionId_ , który wybierze dla aplikacji. |
|Ocen|Personalizacja zachowuje porównanie sum całkowitych, które są uzyskiwane przez domyślną logikę biznesową, a w tym momencie Personalizacja sum uzyskuje się w trybie online. W Azure Portal dla tego zasobu jest dostępne porównanie|Oceń efektywność personalizacji, uruchamiając [oceny w trybie offline](concepts-offline-evaluation.md), co pozwoli na porównanie całkowitego nagrody, który został osiągnięty w porównaniu z potencjalnymi korzyściami z linii bazowej aplikacji.|

Uwaga dotycząca skuteczności trybu działania:

* Efektywność personalizacji w trybie zawodnym będzie rzadko osiągać 100% linii bazowej aplikacji; i nigdy nie należy go przekroczyć.
* Najlepsze rozwiązania nie będą próbować uzyskać do 100% realizacji; ale zakres 60% – 80% powinien być przeznaczony w zależności od przypadku użycia.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Używanie trybu praktykant do uczenia się z danymi historycznymi

Jeśli masz znaczną ilość danych historycznych, chcesz użyć programu do pouczenia narzędzia personalizacji, można użyć trybu programu w celu odtworzenia danych przez personalizację.

Skonfiguruj personalizację w trybie praktykant i Utwórz skrypt, który wywołuje rangę z działaniami i funkcjami kontekstu z danych historycznych. Wywołaj interfejs API nagradzania na podstawie obliczeń rekordów w tych danych. Aby zobaczyć niektóre wyniki, potrzebne są około 50 000 zdarzeń historycznych, ale 500 000 jest zalecane w celu uzyskania bardziej wiarygodnych wyników.

W przypadku uczenia się z danych historycznych zaleca się, aby dane wysyłane w ramach (funkcje kontekstu i akcji, ich układu w formacie JSON używanym przez żądania rangi i obliczenia odnoszące się do tego zestawu danych szkoleniowych) były zgodne z danymi (funkcjami i obliczaniem nagrody) dostępnymi w istniejącej aplikacji.

Dane w trybie offline i infacto mogą być bardziej niekompletne i noisier i różnią się w formacie. Podczas gdy jest możliwe uczenie się danych historycznych, wyniki z tego działania mogą być niejednoznaczne i nie jest dobrym predykcyjnym o tym, w jakim stopniu będzie się uczyć, szczególnie jeśli funkcje te różnią się w zależności od danych i istniejącej aplikacji.

Zwykle w przypadku personalizowania, w porównaniu do szkoleń z danymi historycznymi, zmiana zachowania do trybu praktykant i uczenie się z istniejącej aplikacji jest bardziej efektywną ścieżką do wydajnego modelu, z mniejszą robocizną, inżynierią danych i oczyszczaniem.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Korzystanie z trybu usługi dla testów A/B

Jest to przydatne tylko w przypadku testów A/B przeprowadzenia działania personalizacji, gdy została sprawdzona, i uczenie się w trybie online. W trybie zawodnym używana jest tylko **Akcja domyślna** , co oznacza, że wszyscy użytkownicy będą efektywnie widzieć środowisko kontroli.

Nawet jeśli Personalizacja jest tylko _traktowaniem_, to to samo wyzwanie jest obecne podczas walidacji danych jest dobre dla personalizacji szkoleniowej. W zamian można używać trybu podmiotu, z 100% ruchu i ze wszystkimi użytkownikami, którzy uzyskują kontrolę (bez zmian).

Gdy masz przypadek użycia przy użyciu narzędzia Personalizacja i uczenie online, eksperymenty A/B umożliwiają kontrolowane kohorty i porównanie wyników, które mogą być bardziej skomplikowane niż sygnały wykorzystywane do nagrody. Przykładem pytania można udzielić odpowiedzi na test A/B: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Następne kroki

* Informacje o [aktywnych i nieaktywnych zdarzeniach](concept-active-inactive-events.md)
