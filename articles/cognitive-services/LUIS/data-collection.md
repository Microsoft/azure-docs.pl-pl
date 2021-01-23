---
title: Zbieranie danych
description: Dowiedz się, jakie przykładowe dane mają być zbierane podczas opracowywania aplikacji
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738088"
---
# <a name="data-collection-for-your-app"></a>Zbieranie danych dla aplikacji

Aplikacja Language Understanding (LUIS) potrzebuje danych w ramach opracowywania aplikacji.

## <a name="data-used-in-luis"></a>Dane używane w LUIS

LUIS używa tekstu jako danych do uczenia i testowania aplikacji LUIS w celu klasyfikacji dla [intencji](luis-concept-intent.md) i wyodrębnienia [jednostek](luis-concept-entity-types.md). Potrzebujesz wystarczającej ilości danych, aby uzyskać wystarczające dane, aby utworzyć oddzielne zestawy danych do szkolenia i testowania, które mają różnorodność i dystrybucję, w odniesieniu do określonego poniżej.  Dane w każdym z tych zestawów nie powinny nakładać się na siebie.

## <a name="training-data-selection-for-example-utterances"></a>Wybór danych szkoleniowych na przykład wyrażenia długości

Wybierz pozycję wyrażenia długości dla swojego zestawu szkoleniowego na podstawie następujących kryteriów:

* **Prawdziwe dane są najlepsze**:
    * **Prawdziwe dane z aplikacji klienckiej**: Wybierz wyrażenia długości, które są rzeczywistymi danymi z aplikacji klienckiej.  Jeśli klient wyśle formularz sieci Web za pomocą swojego zapytania dzisiaj i tworzysz bot, możesz rozpocząć od użycia danych formularza sieci Web.
    * **Dane z wielu źródeł**: Jeśli nie masz żadnych istniejących danych, rozważ wyrażenia długości pozyskiwania.  Staraj się, aby uzyskać najlepsze przybliżenie rzeczywistych danych, które będą widoczne dla aplikacji. Wyrażenia długości ludzki z natłokami są lepsze niż wygenerowane przez komputer wyrażenia długości.  Podczas kompilowania zestawu danych syntetycznego wyrażenia długości wygenerowanego dla określonych wzorców nie będzie on miał wielu naturalnych odmian, które zobaczysz dla osób tworzących wyrażenia długości i nie zakończy się w środowisku produkcyjnym.
* **Różnorodność danych**:
    * **Różnorodność regionów**: Upewnij się, że dane dla każdego zamiaru są tak zróżnicowane jak to możliwe, w tym _sformułowanie_ (wybór słowa) i _Gramatyka_.  W przypadku uczenia się zamiaru dotyczącego zasad dotyczących usługi HR w dniach urlopów upewnij się, że masz wyrażenia długości, które reprezentują warunki, które są używane dla wszystkich obsługiwanych regionów.  Na przykład w przypadku osób pracujących nad innymi osobami może zadawać pytania dotyczące `taking a holiday` osób, które mogą się pojawić `taking vacation days` .
    * **Zróżnicowanie języka**: Jeśli masz użytkowników z różnymi językami macierzystymi, które komunikują się w drugim języku, upewnij się, że wyrażenia długości, który reprezentuje NIENATYWNE głośniki.
    * **Zróżnicowanie danych wejściowych**: Uwzględnij ścieżkę wejściową danych. Jeśli zbierasz dane z jednej osoby, działu lub urządzenia wejściowego (mikrofon), prawdopodobnie brakuje różnorodności, która będzie ważna dla aplikacji, aby poznać wszystkie ścieżki wejściowe.
    * **Różnorodność interpunkcji**: należy wziąć pod uwagę, że ludzie korzystają z różnych poziomów interpunkcji w aplikacjach tekstowych i upewnij się, że masz zróżnicowanie sposobu używania interpunkcji. Jeśli używasz danych pochodzących z mowy, nie będą one miały żadnych znaków interpunkcyjnych, więc dane nie powinny być.
* **Dystrybucja danych**: Upewnij się, że rozmieszczenie danych między intencjami reprezentuje takie samo rozmieszczenie danych, jakie odbiera aplikacja kliencka. Jeśli aplikacja LUIS będzie klasyfikować wyrażenia długości, które są żądaniami o zaplanowanie urlopu (50%), ale zobaczysz także wyrażenia długości dotyczące zapytania o dni urlopu pozostawione (20%), zatwierdzenie urlopów (20%) i niektóre z nich poza zakresem i czat Chit (10%) następnie zestaw danych powinien zawierać procent próbek dla każdego typu wypowiedź.
* **Użyj wszystkich formularzy danych**: Jeśli aplikacja Luis będzie podejmować dane w wielu formularzach, upewnij się, że zostały one uwzględnione w wyrażenia długościu szkoleniowym. Na przykład, jeśli aplikacja kliencka pobiera zarówno mowę, jak i tekstową, musisz mieć wygenerowane zamiany mowy na tekst wyrażenia długości oraz wyrażenia długości.  Zobaczysz różne różnice w sposobie, w jaki ludzie mówią, jak ich typ, a także różne błędy rozpoznawania mowy i literówki.  Wszystkie te zmiany powinny być reprezentowane w danych szkoleniowych.
* **Przykłady pozytywne i negatywne**: aby nauczyć aplikację Luis, należy zapoznać się z tym, co to jest (pozytywna) i czego nie ma (negatywna). W LUIS, wyrażenia długości może być dodatni tylko dla jednego celu. Po dodaniu wypowiedź do intencji, LUIS automatycznie czyni ten sam przykład wypowiedź dla innych intencji.
* **Dane poza zakresem aplikacji**: Jeśli aplikacja będzie widziała wyrażenia długości, które wykraczają poza zdefiniowane intencje, upewnij się, że zostały one podane. Przykłady, które nie są przypisane do określonego zdefiniowanego zamiaru, będą oznaczone jako zamiar **Brak** .  Ważne jest, aby mieć realistyczne przykłady dla opcji **Brak** , aby prawidłowo przewidzieć wyrażenia długości, które są poza zakresem zdefiniowanych intencji.

    Na przykład jeśli tworzysz bot o godz. w czasie urlopu i masz trzy intencje:
    * Zaplanuj lub Edytuj opuszczenie
    * zapytanie o dostępne dni urlopu
    * Zatwierdź/odtwierdź urlop

    Chcesz upewnić się, że masz wyrażenia długości, które obejmują obydwie te intencje, ale również obejmują potencjalne wyrażenia długości poza tym zakresem, które aplikacja powinna obsłużć takie:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Rzadkie przykłady**: aplikacja musi mieć rzadkie przykłady oraz typowe przykłady.  Jeśli aplikacja nigdy nie widziała rzadkich przykładów, nie będzie mogła ich identyfikować w środowisku produkcyjnym. Jeśli używasz rzeczywistych danych, będziesz mieć możliwość dokładniejszego przewidywania, w jaki sposób aplikacja LUIS będzie działać w środowisku produkcyjnym.

### <a name="quality-instead-of-quantity"></a>Jakość zamiast ilości

Przed dodaniem większej ilości danych należy wziąć pod uwagę jakość istniejących danych.  W przypadku usługi LUIS używasz funkcji uczenia maszynowego.  Kombinacje Twoich etykiet i funkcji uczenia maszynowego są używane przez aplikację LUIS.  Nie polega to po prostu na liczbie etykiet w celu uzyskania najlepszego przewidywania.  Istotną częścią jest różnorodność przykładów i ich reprezentacja aplikacji LUIS w środowisku produkcyjnym.

### <a name="preprocessing-data"></a>Przetwarzanie wstępne danych

Poniższe kroki przetwarzania wstępnego ułatwią utworzenie lepszej aplikacji LUIS:

* **Usuń duplikaty**: zduplikowane wyrażenia długości nie będzie miało wpływu, ale ich usunięcie spowoduje zapisanie czasu etykietowania.
* **Zastosuj ten sam proces wstępnej aplikacji klient**: Jeśli aplikacja kliencka, która wywołuje punkt końcowy przewidywania Luis, stosuje przetwarzanie danych w czasie wykonywania przed wysłaniem tekstu do Luis, należy przeprowadzić uczenie aplikacji Luis na danych przetwarzanych w taki sam sposób. Na przykład, jeśli aplikacja kliencka używa [Sprawdzanie pisowni Bing](../bing-spell-check/overview.md) do poprawiania pisowni danych wejściowych w systemie Luis, Popraw szkolenia i test wyrażenia długości przed dodaniem do Luis.
* **Nie stosuj nowych procesów oczyszczania, które nie są używane przez aplikację kliencką**: Jeśli aplikacja kliencka akceptuje tekst wygenerowany przez mowę bezpośrednio bez czyszczenia, takiego jak Gramatyka lub interpunkcja, wyrażenia długości musi odzwierciedlać to samo, w tym wszelkie brakujące znaki interpunkcyjne i inne nierozpoznane, które należy uwzględnić.
* **Nie czyść danych**: nie pozbyć się nieprawidłowo sformułowanych danych wejściowych, które mogą się pojawić przed uszkodzeniem rozpoznawania mowy, przypadkowym naciśnięciem klawiszy lub błędnie wpisanym tekstem. Jeśli aplikacja będzie widzieć dane wejściowe takie jak te, ważne jest, aby były przeszkolone i przetestowane na nich. Dodaj _nieprawidłowo sformułowany cel wejścia_ , jeśli aplikacja nie będzie mogła go zrozumieć. Oznacz te dane, aby ułatwić aplikacji LUIS przewidywalność prawidłowej odpowiedzi w czasie wykonywania. Aplikacja kliencka może wybrać odpowiednią odpowiedź do niezrozumiałej wyrażenia długości, takiej jak `Please try again` .

### <a name="labeling-data"></a>Etykietowanie danych

* **Etykieta tekstu tak, jakby była poprawna**: przykład wyrażenia długości powinna mieć wszystkie formy jednostki oznaczonej etykietą. Obejmuje to tekst, który jest błędnie wpisany, nieprawidłowo wpisany i nieprawidłowo przetłumaczy.

### <a name="data-review-after-luis-app-is-in-production"></a>Przegląd danych po LUIS aplikacji jest w środowisku produkcyjnym

Zapoznaj się z [punktem końcowym wyrażenia długości](luis-concept-review-endpoint-utterances.md) , aby monitorować rzeczywisty ruch wypowiedź po wdrożeniu aplikacji w środowisku produkcyjnym.  Dzięki temu można aktualizować wyrażenia długości szkoleniowe za pomocą rzeczywistych danych, co poprawi swoją aplikację. Wszystkie aplikacje skompilowane przy użyciu danych scenariusza o pożądanym czasie lub w nieprawdziwym scenariuszu będą musiały zostać ulepszone w oparciu o rzeczywiste użycie.

## <a name="test-data-selection-for-batch-testing"></a>Wybór danych testowych dla testów wsadowych

Wszystkie zasady wymienione powyżej dla szkolenia wyrażenia długości mają zastosowanie do wyrażenia długości, którego należy użyć dla [zestawu testowego](./luis-how-to-batch-test.md). Upewnij się, że dystrybucja między intencjami i jednostkami dublowania prawdziwej dystrybucji tak blisko jak to możliwe.

Nie używaj ponownie wyrażenia długości z zestawu szkoleniowego w zestawie testów. To niepoprawnie podzieli Twoje wyniki i nie przekaże mu odpowiedniego wskazania, w jaki sposób aplikacja LUIS będzie działać w środowisku produkcyjnym.

Po opublikowaniu pierwszej wersji aplikacji należy zaktualizować zestaw testów za pomocą wyrażenia długości z rzeczywistego ruchu sieciowego, aby upewnić się, że zestaw testowy odzwierciedla dystrybucję produkcyjną i można monitorować realistyczną wydajność w miarę upływu czasu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak LUIS zmienia dane przed przewidywaniam](luis-concept-data-alteration.md)