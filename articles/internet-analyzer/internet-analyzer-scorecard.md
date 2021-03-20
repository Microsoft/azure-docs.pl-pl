---
title: Interpretowanie karty wyników | Microsoft Docs
description: Dowiedz się, jak interpretować kartę wyników. Karta Karta wyników zawiera zagregowane i przeanalizowane wyniki testów.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91330217"
---
# <a name="interpreting-your-scorecard"></a>Interpretowanie karty wyników

Karta Karta wyników zawiera zagregowane i przeanalizowane wyniki testów. Każdy test ma własne karty wyników. Karty wyników zapewniają szybkie i zrozumiałe podsumowania wyników pomiarów w celu zapewnienia wyników opartych na danych dla wymagań sieciowych. Analizator Internetu zajmuje się analizą, co pozwala skupić się na decyzji.

Kartę karty wyników można znaleźć w menu zasobów analizatora internetowego. 


## <a name="filters"></a>Filtry

* ***Test:*** Wybierz test, dla którego chcesz wyświetlić wyniki — każdy test ma własną kartę wyników. Dane testowe pojawią się po wystarczającej ilości danych, aby zakończyć analizę — w większości przypadków powinno to być w ciągu 24 godzin. 
* ***Okres & daty zakończenia:*** Trzy karty wyników są generowane codziennie — każda karta wyników odzwierciedla inny okres agregacji — 24 godziny poprzedzające (dzień), siedem dni przed (tydzień) i 30 dni przed (miesiąc). Użyj filtru "Data końcowa", aby wybrać ostatni dzień okresu, który ma zostać wyświetlony. 
* ***Kraj:*** Dla każdego kraju, w którym znajdują się użytkownicy końcowi, jest generowana Karta wyników. Filtr globalny zawiera wszystkich użytkowników końcowych.

## <a name="measurement-count"></a>Liczba pomiarów

Liczba pomiarów wpływa na wiarygodność analizy. Im większa liczba, tym dokładniejszy wynik. Testy powinny mieć na celu co najmniej 100 pomiarów na punkt końcowy dziennie. Jeśli liczba pomiarów jest zbyt niska, Skonfiguruj klienta JavaScript do uruchamiania częściej aplikacji. Liczba pomiarów dla punktów końcowych A i B powinna być bardzo podobna, chociaż małe różnice są oczekiwane i odpowiednie. W przypadku dużych różnic wyniki nie powinny być zaufane.

## <a name="percentiles"></a>Percentyle

Opóźnienie (w milisekundach) to popularna Metryka do mierzenia szybkości między źródłem a miejscem docelowym w Internecie. Dane opóźnienia nie są zwykle dystrybuowane (tj. nie są zgodne ze znakiem "dzwonka"), ponieważ występuje "Long-Tail" wartości dużych opóźnień, które pochylać wyniki podczas korzystania z statystyk, takich jak średnia arytmetyczna. Alternatywnym rozwiązaniem jest to, że percentyly umożliwiają analizowanie danych. Na przykład mediana lub 50. percentyl podsumowuje średnią część dystrybucji — połowa wartości powyżej, a połowa jest poniżej. Wartość percentylu 75. oznacza, że jest ona większa niż 75% wszystkich wartości w dystrybucji. Analizator Internetu odwołuje się do percentylów w postaci skróconej, jak P50, P75 i p95.

Percentyle analizatora Internetu są _przykładowymi metrykami_. Jest to w przeciwieństwie do _metryki_ prawdziwej populacji. Na przykład codzienne rzeczywiste prawdziwe wypełnienie w czasie trwania dla studentów na uczelni Południowo-środkowe, a firma Microsoft to wartość mediana opóźnienia wszystkich żądań w danym dniu. W praktyce pomiar wartości wszystkich żądań jest niepraktyczny, więc Załóżmy, że uzasadniona duża próbka jest reprezentatywna dla prawdziwej populacji.

Na potrzeby analizy P50 (mediana) jest przydatne jako oczekiwana wartość rozkładu opóźnień. Wyższe percentyly, takie jak P95, są przydatne do identyfikowania, jak duże opóźnienie jest w najgorszym przypadku. Jeśli interesują Cię ogólne informacje o opóźnieniu klienta, P50 jest poprawną metryką, na którą należy się skoncentrować. Jeśli chodzi o zrozumienie wydajności dla najgorszych klientów, P95 powinna być fokusem. P75 to równowaga między tymi dwoma.


## <a name="deltas"></a>Różnic

Różnica jest różnicą wartości metryk dla punktów końcowych a i B. różnice są obliczane, aby pokazać korzyść B za pośrednictwem. wartości dodatnie oznaczają B wykonane lepiej niż, natomiast wartości ujemne wskazują na wydajność B. Przyrosty mogą być bezwzględne (np. 10 milisekund) lub względne (5%).

## <a name="confidence-interval"></a>Interwał ufności 

Interwały zaufania są zakresem wartości, które są prawdopodobieństwem zawierającym metrykę populacji, taką jak Media, P75 lub Average. Przestrzegamy wspólnej Konwencji statystycznej dotyczącej korzystania z 95% CI.

W przypadku programu Internet Analyzer jest dobry Interwał ufności, ponieważ pokazuje przykładową metrykę najprawdopodobniej blisko rzeczywistej metryki populacji. Szeroki Interwał ufności oznacza mniej pewności, że nasza Przykładowa Metryka odzwierciedla metrykę prawdziwej populacji. Najlepszym sposobem poprawienia CI jest zwiększenie liczby pomiarów.

## <a name="time-series"></a>szereg czasowy 

Seria czasowa pokazuje, jak zmienia się Metryka w czasie. W Internecie istnieje wiele czynników, które wpływają na wydajność, takich jak szczytowe okresy ruchu, różnice w weekendy i populacje dni wolnych.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz [Omówienie analizatora Internetu](internet-analyzer-overview.md).
