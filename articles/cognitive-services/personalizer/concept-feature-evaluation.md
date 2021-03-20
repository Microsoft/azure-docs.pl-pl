---
title: Obliczanie funkcji — Personalizowanie
titleSuffix: Azure Cognitive Services
description: Po uruchomieniu oceny w zasobie narzędzia Personalizacja z Azure Portal, Personalizacja zawiera informacje o tym, które funkcje kontekstu i akcje wpływają na model.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87127727"
---
# <a name="feature-evaluation"></a>Ocena funkcji

Po uruchomieniu oceny w zasobie narzędzia Personalizacja z [Azure Portal](https://portal.azure.com), Personalizacja zawiera informacje o tym, które funkcje kontekstu i akcje wpływają na model. 

Jest to przydatne w celu:

* Wyobraź sobie dodatkowe funkcje, których możesz użyć, aby uzyskać inspirację od tego, jakie funkcje są ważniejsze w modelu.
* Zobacz, jakie funkcje nie są istotne, i potencjalnie usuń je lub Przeanalizuj, co może mieć wpływ na użycie.
* Podaj wskazówki dla zespołów redakcyjnych lub nadzorujących o nowej zawartości lub produktach, które warto umieścić w wykazie.
* Rozwiązywanie typowych problemów i błędów, które pojawiają się podczas wysyłania funkcji do narzędzia Personalizacja.

Ważniejsze funkcje mają silniejsze wagi w modelu. Ponieważ te funkcje mają silniejsze wagi, mają one być obecne, gdy Personalizowanie uzyskuje wyższe nagrody.

## <a name="getting-feature-importance-evaluation"></a>Pobieranie oceny ważności funkcji

Aby wyświetlić wyniki ważności funkcji, należy uruchomić ocenę. Ocena powoduje utworzenie czytelnych etykiet funkcji na podstawie nazw funkcji zaobserwowanych w okresie ewaluacyjnym.

Uzyskane informacje o ważności funkcji reprezentują bieżący model usługi Personalizowanie online. Ocena ocenia ważność funkcji modelu zapisaną w dniu zakończenia okresu próbnego, po przejściu na wszystkie szkolenia wykonywane podczas oceny, z bieżącymi zasadami uczenia online. 

Wyniki ważności funkcji nie reprezentują innych zasad i modeli przetestowanych lub utworzonych podczas oceny.  Ocena nie będzie zawierać funkcji wysyłanych do narzędzia Personalizacja po zakończeniu okresu próbnego.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Jak interpretować ocenę ważności funkcji

Personalizacja szacuje funkcje przez tworzenie "grup" funkcji mających podobne znaczenie. Jedną grupę można uzyskać, aby uzyskać ogólne silniejsze znaczenie niż inne, ale w ramach grupy, porządkowanie funkcji jest alfabetyczne.

Informacje o każdej z tych funkcji obejmują:

* Czy funkcja pochodzi z kontekstu lub akcji.
* Klucz funkcji i wartość.

Na przykład aplikacja do zamawiania lodów w sklepie lody może zobaczyć "Context. Pogoda: gorąca" jako bardzo ważną funkcję.

Personalizacja wyświetla korelacje funkcji, które w przypadku uwzględnienia razem tworzą wyższe nagrody.

Na przykład można zobaczyć "Context. Pogoda: Hot *with* Action. MenuItem: Icecream", a także "Context. Pogoda: zimn *with* Action. MenuItem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Akcje, które można wykonać na podstawie oceny funkcji

### <a name="imagine-additional-features-you-could-use"></a>Wyobraź sobie dodatkowe funkcje, których można użyć

Uzyskaj inspirację od ważniejszych funkcji w modelu. Jeśli na przykład w aplikacji mobilnej wideo widzisz polecenie "Context. MobileBattery: Low", możesz zastanowić się, że typ połączenia może również umożliwić klientom wybór jednego klipu wideo na inny, a następnie dodać do aplikacji funkcje dotyczące typu połączenia i przepustowości.

### <a name="see-what-features-are-not-important"></a>Zobacz, jakie funkcje nie są ważne

Potencjalnie Usuń nieważne funkcje lub Przeanalizuj, co może mieć wpływ na użycie. Funkcje mogą mieć rangę z wielu powodów. Może to być oryginalna funkcja nie wpływa na zachowanie użytkownika. Jednak może to oznaczać, że funkcja nie jest widoczna dla użytkownika. 

Na przykład witryna wideo może zobaczyć, że "Action. VideoResolution = 4K" jest funkcją o niskiej ważności, która jest sprzeczna z badaniem użytkownika. Przyczyną może być to, że aplikacja nie będzie nawet zawierać ani nie pokazywać rozdzielczości wideo, dlatego użytkownicy nie będą mogli zmienić ich zachowania na podstawie tego problemu.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Zapewnianie wskazówek dla zespołów redakcyjnych lub nadzorowanych

Zapoznaj się ze wskazówkami dotyczącymi nowej zawartości lub produktów. Narzędzie personalizacji zostało zaprojektowane z myślą o narzędziu, które rozszerzają wgląd i zespoły. Jednym ze sposobów jest podawanie informacji dla redakcyjnych grup na temat tego, co zawiera produkty, artykuły lub zawartość, które są związane z zachowaniem. Na przykład scenariusz aplikacji wideo może wskazywać, że istnieje ważna funkcja o nazwie "Action. VideoEntities. Cat: true", monitując zespół redakcyjny o przełączenie większej liczby filmów wideo.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Rozwiązywanie typowych problemów i pomyłek

Typowe problemy i błędy można naprawić, zmieniając kod aplikacji, aby nie wysyłał nieodpowiednich lub niepoprawnie sformatowanych funkcji do personalizacji. 

Typowe błędy podczas wysyłania funkcji obejmują następujące elementy:

* Wysyłanie informacji umożliwiających identyfikację użytkownika. Dane OSOBowe specyficzne dla jednej osoby (takie jak imię i nazwisko, numer telefonu, numery kart kredytowych, adresy IP) nie powinny być używane z personalizacją. Jeśli aplikacja wymaga śledzenia użytkowników, użyj nieidentyfikującego identyfikatora UUID lub innego numeru identyfikatora użytkownika. W większości scenariuszy jest to również problematyczne.
* W przypadku dużej liczby użytkowników jest mało prawdopodobne, że interakcja poszczególnych użytkowników będzie miała więcej niż cała interakcja populacji, więc wysłanie identyfikatorów użytkowników (nawet jeśli nie jest to dane OSOBowe) prawdopodobnie doda więcej szumów niż wartość do modelu.
* Wysyłanie pól daty i godziny jako precyzyjne sygnatury czasowe zamiast featurized wartości czasu. Posiadanie takich funkcji, jak Context. TimeStamp. Day = poniedziałek lub "Context. TimeStamp. Hour" = "13", jest bardziej przydatne. Każdy z nich będzie zawierać maksymalnie 7 lub 24 wartości funkcji. Ale "Context. TimeStamp": "1985-04-12T23:20:50.52 Z" jest tak dokładne, że nie będzie można z niej uczyć się, ponieważ nie zostanie ono nigdy wykonane.

## <a name="next-steps"></a>Następne kroki

Informacje o [skalowalności i wydajności](concepts-scalability-performance.md) za pomocą programu personalizujer.

