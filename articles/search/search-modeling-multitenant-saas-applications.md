---
title: Wielodostępność i izolacja zawartości
titleSuffix: Azure Cognitive Search
description: Poznaj typowe wzorce projektowe dla wielodostępnych aplikacji SaaS przy użyciu usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: cd21197d6d1559b681ae622b974f6eb7ba95ad3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397372"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Wzorce projektowe dla wielodostępnych aplikacji SaaS i platformy Azure Wyszukiwanie poznawcze

Aplikacja wielodostępna jest taka, która zapewnia te same usługi i możliwości dla dowolnej liczby dzierżawców, którzy nie mogą zobaczyć ani udostępnić danych innych dzierżawców. W tym dokumencie omówiono strategie izolacji dzierżawców dla aplikacji wielodostępnych utworzonych przy użyciu usługi Azure Wyszukiwanie poznawcze.

## <a name="azure-cognitive-search-concepts"></a>Pojęcia dotyczące usługi Azure Wyszukiwanie poznawcze
Jako rozwiązanie typu "wyszukiwanie jako usługa" [platforma Azure wyszukiwanie poznawcze](search-what-is-azure-search.md) pozwala deweloperom dodawać zaawansowane środowiska wyszukiwania do aplikacji bez konieczności zarządzania infrastrukturą ani do pobierania informacji. Dane są przekazywane do usługi, a następnie przechowywane w chmurze. Przy użyciu prostych żądań do interfejsu API usługi Azure Wyszukiwanie poznawcze można modyfikować i przeszukiwać dane. 

### <a name="search-services-indexes-fields-and-documents"></a>Usługi wyszukiwania, indeksy, pola i dokumenty

Przed przeprowadzeniem omawiania wzorców projektowych należy zrozumieć kilka podstawowych pojęć.

W przypadku korzystania z usługi Azure Wyszukiwanie poznawcze jeden subskrybuje *usługę wyszukiwania*. Dane przekazywane do usługi Azure Wyszukiwanie poznawcze są przechowywane w *indeksie* w ramach usługi wyszukiwania. W ramach jednej usługi może istnieć wiele indeksów. Aby korzystać ze znanych koncepcji baz danych, usługa wyszukiwania może być likened do bazy danych, podczas gdy indeksy w ramach usługi mogą być likened do tabel w bazie danych.

Każdy indeks w ramach usługi wyszukiwania ma własny schemat, który jest definiowany przez wiele dostosowywalnych *pól*. Dane są dodawane do indeksu Wyszukiwanie poznawcze platformy Azure w postaci poszczególnych *dokumentów*. Każdy dokument musi być przekazany do określonego indeksu i musi pasować do tego schematu indeksu. Podczas wyszukiwania danych przy użyciu usługi Azure Wyszukiwanie poznawcze zapytania wyszukiwania pełnotekstowego są wydawane względem określonego indeksu.  Aby porównać te koncepcje z bazą danych, pola mogą być likened do kolumn w tabeli, a dokumenty mogą być likened do wierszy.

### <a name="scalability"></a>Skalowalność
Każda usługa Wyszukiwanie poznawcze platformy Azure w [warstwie cenowej](https://azure.microsoft.com/pricing/details/search/) standardowa może być skalowana w dwóch wymiarach: przechowywanie i dostępność.

* *Partycje* można dodać, aby zwiększyć magazyn usługi wyszukiwania.
* *Repliki* można dodawać do usługi w celu zwiększenia przepływności żądań, które usługa wyszukiwania może obsłużyć.

Dodawanie i usuwanie partycji oraz replik umożliwia zwiększenie pojemności usługi wyszukiwania z ilością danych i ruchem wymaganym przez aplikację. Aby usługa wyszukiwania mogła uzyskać umowę [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)do odczytu, wymaga dwóch replik. Aby usługa mogła osiągnąć umowę [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)do odczytu i zapisu, wymaga trzech replik.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Limity usługi i indeksu na platformie Azure Wyszukiwanie poznawcze
Na platformie Azure Wyszukiwanie poznawcze istnieje kilka różnych [warstw cenowych](https://azure.microsoft.com/pricing/details/search/) , a każda z nich ma różne [limity i przydziały](search-limits-quotas-capacity.md). Niektóre z tych limitów znajdują się na poziomie usługi, a niektóre z nich znajdują się na poziomie indeksu, a niektóre z nich znajdują się na poziomie partycji.

|  | Podstawowy | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Maksymalna liczba replik na usługę** |3 |12 |12 |12 |12 |
| **Maksymalna liczba partycji na usługę** |1 |12 |12 |12 |3 |
| **Maksymalna liczba jednostek wyszukiwania (repliki * partycje) na usługę** |3 |36 |36 |36 |36 (maksymalnie 3 partycje) |
| **Maksymalny rozmiar magazynu na usługę** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Maksymalny rozmiar magazynu na partycję** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Maksymalna liczba indeksów na usługę** |5 |50 |200 |200 |3000 (maksymalnie 1000 indeksów/partycji) |

#### <a name="s3-high-density"></a>Wysoka gęstość S3
W warstwie cenowej S3 usługi Azure Wyszukiwanie poznawcze istnieje opcja trybu wysokiej gęstości (HD) przeznaczonego specjalnie dla scenariuszy wielodostępnych. W wielu przypadkach konieczne jest obsługę dużej liczby mniejszych dzierżawców w ramach jednej usługi, aby osiągnąć zalety prostoty i opłacalności.

Funkcja S3 HD pozwala na zapakowanie wielu małych indeksów w ramach zarządzania pojedynczą usługą wyszukiwania przez handel możliwością skalowania indeksów w poziomie przy użyciu partycji, aby hostować więcej indeksów w jednej usłudze.

Usługa S3 została zaprojektowana tak, aby obsługiwała stałą liczbę indeksów (maksymalnie 200) i zezwalać na skalowanie poszczególnych indeksów w poziomie w miarę dodawania nowych partycji do usługi. Dodawanie partycji do usług S3 HD zwiększa maksymalną liczbę indeksów, które może obsługiwać usługa. Idealny maksymalny rozmiar pojedynczego indeksu S3HD to około 50 – 80 GB, chociaż nie ma żadnego limitu rozmiaru dla każdego indeksu narzuconego przez system.

## <a name="considerations-for-multitenant-applications"></a>Zagadnienia dotyczące aplikacji wielodostępnych
Aplikacje wielodostępne muszą efektywnie dystrybuować zasoby między dzierżawcami, zachowując jednocześnie pewien poziom prywatności między różnymi dzierżawcami. Podczas projektowania architektury dla takiej aplikacji należy wziąć pod uwagę kilka kwestii:

* *Izolacja dzierżawy:* Deweloperzy aplikacji muszą podjąć odpowiednie środki, aby upewnić się, że żaden dzierżawca nie ma nieautoryzowanego lub niepożądanego dostępu do danych innych dzierżawców. Z perspektywy prywatności danych, strategie izolacji dzierżawców wymagają efektywnego zarządzania zasobami udostępnionymi i ochrony przed przechodzącymi przez nie sąsiadów.
* *Koszt zasobów w chmurze:* Podobnie jak w przypadku każdej innej aplikacji, rozwiązania programowe muszą pozostawać konkurencyjne jako składnik aplikacji wielodostępnej.
* *Łatwość operacji:* Podczas tworzenia architektury wielodostępnego, wpływ na operacje i złożoność aplikacji jest ważnym zagadnieniem. Usługa Azure Wyszukiwanie poznawcze ma umowę [SLA na 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globalne rozmiary:* Aplikacje wielodostępne mogą być efektywnie obsługiwać dzierżawców rozmieszczonych na całym świecie.
* *Skalowalność:* Deweloperzy aplikacji muszą rozważyć, jak uzgadniają między utrzymaniem wystarczająco niskich poziomów złożoności aplikacji i projektowaniem aplikacji do skalowania przy użyciu liczby dzierżawców i rozmiaru danych i obciążeń dzierżawców.

Usługa Azure Wyszukiwanie poznawcze oferuje kilka granic, których można użyć do izolowania danych i obciążeń dzierżawców.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Wielodostępność modelowania przy użyciu usługi Azure Wyszukiwanie poznawcze
W przypadku scenariusza wielodostępnego Deweloper aplikacji korzysta z jednej lub kilku usług wyszukiwania i dzieli dzierżawców między usługi, indeksy lub oba te usługi. Usługa Azure Wyszukiwanie poznawcze ma kilka typowych wzorców podczas modelowania scenariusza wielodostępnego:

1. *Indeks na dzierżawcę:* Każda dzierżawa ma swój własny indeks w ramach usługi wyszukiwania, który jest udostępniany innym dzierżawcom.
2. *Usługa na dzierżawcę:* Każda dzierżawa ma własną dedykowaną usługę Wyszukiwanie poznawcze platformy Azure, która oferuje najwyższy poziom danych i rozdzielanie obciążeń.
3. *Kombinacja obu:* Większe, bardziej aktywne dzierżawy są przypisane do dedykowanych usług, podczas gdy mniejsze dzierżawy są przypisane do poszczególnych indeksów w ramach usług udostępnionych.

## <a name="1-index-per-tenant"></a>1. indeks na dzierżawcę

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Wskaźnik modelu indeksu na dzierżawcę" border="false":::

W modelu z indeksem na dzierżawcę wiele dzierżawców zajmuje jedną usługę Wyszukiwanie poznawcze platformy Azure, w której każdy dzierżawca ma swój własny indeks.

Dzierżawcy uzyskują izolację danych, ponieważ wszystkie żądania wyszukiwania i operacje dokumentu są wydawane na poziomie indeksu na platformie Azure Wyszukiwanie poznawcze. W warstwie aplikacji istnieje konieczna świadomość umożliwiająca kierowanie ruchu różnych dzierżawców do właściwych indeksów, a także zarządzanie zasobami na poziomie usługi dla wszystkich dzierżawców.

Kluczowym atrybutem modelu indeksu na dzierżawcę jest możliwość, aby Deweloper aplikacji anulował subskrypcję pojemności usługi wyszukiwania wśród dzierżawców aplikacji. Jeśli dzierżawcy mają nierównomierny rozkład obciążenia, optymalna kombinacja dzierżawców może być dystrybuowana w ramach indeksów usługi wyszukiwania, aby pomieścić wiele wysoce aktywnych, intensywnie korzystających z zasobów dzierżawców jednocześnie obsługujących długi okres mniej aktywnych dzierżawców. Handel jest niezdolnością do obsługi sytuacji, w których każda dzierżawa jest wysoce aktywna.

Model "indeks na dzierżawcę" stanowi podstawę dla modelu kosztu zmiennego, w którym cała usługa Wyszukiwanie poznawcze platformy Azure jest zakupiona, a następnie uzupełniona dzierżawcami. Pozwala to na wyznaczenie nieużywanej pojemności dla prób i kont bezpłatnych.

W przypadku aplikacji z globalnym wpływem model indeksu na dzierżawcę może nie być najbardziej wydajny. Jeśli dzierżawy aplikacji są dystrybuowane na całym świecie, może być konieczne oddzielna usługa dla każdego regionu, co może pociągnąć za sobą dodatkowe koszty.

Usługa Azure Wyszukiwanie poznawcze umożliwia skalowanie zarówno pojedynczych indeksów, jak i całkowitej liczby indeksów, które mają zostać powiększone. W przypadku wybrania odpowiedniej warstwy cenowej partycje i repliki można dodać do całej usługi wyszukiwania, gdy pojedynczy indeks w ramach usługi powiększa się zbyt duży pod względem magazynu lub ruchu.

Jeśli łączna liczba indeksów rośnie zbyt duże dla pojedynczej usługi, należy zastanowić się, aby zapewnić obsługę nowych dzierżawców. Jeśli indeksy muszą zostać przeniesione między usługami wyszukiwania po dodaniu nowych usług, dane z indeksu muszą zostać ręcznie skopiowane z jednego indeksu do drugiego, ponieważ usługa Azure Wyszukiwanie poznawcze nie zezwala na przenoszenie indeksu.

## <a name="2-service-per-tenant"></a>2. usługa na dzierżawcę

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="Wskaźnik modelu indeksu na dzierżawcę" border="false":::

W architekturze usługi dla dzierżawy każdy dzierżawca ma własną usługę wyszukiwania.

W tym modelu aplikacja osiąga maksymalny poziom izolacji dla dzierżawców. Każda usługa ma dedykowany magazyn i przepływność na potrzeby obsługi żądania wyszukiwania oraz oddzielnych kluczy interfejsu API.

W przypadku aplikacji, w których każda dzierżawa ma duże rozmiary lub że obciążenie ma niewielkie zróżnicowanie z dzierżawy do dzierżawy, model usługi dla dzierżawy jest skutecznym wyborem, ponieważ zasoby nie są współużytkowane przez różne obciążenia dzierżawców.

Usługa na model dzierżawy oferuje również możliwość korzystania z przewidywalnego, stałego modelu kosztów. Usługa Search nie oferuje żadnych inwestycji z góry, dopóki nie zostanie wypełniona dzierżawca, ale koszt dla dzierżawy jest wyższy niż model dla dzierżawy.

Model usługi dla dzierżawy jest wydajnym wyborem dla aplikacji z globalnym wpływem. W przypadku dzierżawców rozproszonych geograficznie można łatwo korzystać z usługi każdej dzierżawy w odpowiednim regionie.

Wyzwania w zakresie skalowania tego wzorca powstają, gdy poszczególne dzierżawy skalowalnośćą swoją usługę. Usługa Azure Wyszukiwanie poznawcze nie obsługuje obecnie uaktualniania warstwy cenowej usługi wyszukiwania, dlatego wszystkie dane będą musiały zostać ręcznie skopiowane do nowej usługi.

## <a name="3-mixing-both-models"></a>3. mieszanie obu modeli
Innym wzorcem modelowania wielodostępności jest mieszanie strategii dla dzierżawców i usług dla dzierżawców.

Przez mieszanie dwóch wzorców, największa liczba dzierżawców aplikacji może zajmować się dedykowanymi usługami, a długi ogon mniej aktywnych, krótszych dzierżawców może zajmować indeksy w usłudze udostępnionej. Ten model zapewnia, że największe dzierżawy mają spójną wysoką wydajność usługi, jednocześnie pomagając w ochronie mniejszych dzierżawców z dowolnego sąsiada z zakłóceniami.

Jednak wdrożenie tej strategii polega na prognozie przewidywania, które dzierżawcy będą wymagały dedykowanej usługi, a indeks w usłudze udostępnionej. Złożoność aplikacji zwiększa się wraz z koniecznością zarządzania obydwoma modelami wielodostępnymi.

## <a name="achieving-even-finer-granularity"></a>Osiąganie jeszcze większej szczegółowości
Powyższe wzorce projektowe do modelowania scenariuszy wielodostępnych na platformie Azure Wyszukiwanie poznawcze zakładają jednolity zakres, w którym każda dzierżawa jest całym wystąpieniem aplikacji. Jednak aplikacje mogą czasami obsługiwać wiele mniejszych zakresów.

Jeśli modele usługi dla dzierżawców i indeksu dla dzierżawców nie mają wystarczająco małych zakresów, można modelować indeks, aby osiągnąć jeszcze bardziej szczegółowy stopień szczegółowości.

Aby jeden indeks zachowywać się inaczej dla różnych punktów końcowych klienta, można dodać pole do indeksu, który wyznacza określoną wartość dla każdego możliwego klienta. Za każdym razem, gdy klient wywołuje Wyszukiwanie poznawcze platformy Azure, aby wykonać zapytanie lub zmodyfikować indeks, kod z aplikacji klienckiej określa odpowiednią wartość dla tego pola przy użyciu funkcji [filtrowania](./query-odata-filter-orderby-syntax.md) wyszukiwanie poznawcze platformy Azure w czasie wykonywania zapytania.

Ta metoda może służyć do osiągnięcia funkcjonalności oddzielnych kont użytkowników, oddzielenia poziomów uprawnień, a nawet całkowicie oddzielnych aplikacji.

> [!NOTE]
> Użycie opisanego powyżej podejścia do skonfigurowania jednego indeksu do obsłużynia wielu dzierżawców ma wpływ na znaczenie wyników wyszukiwania. Wyniki wyszukiwania istotnie są obliczane w zakresie poziomu indeksu, a nie w zakresie poziomu dzierżawy, więc dane wszystkich dzierżawców są zawarte w statystyce źródłowej ocen przydatności, takich jak częstotliwość okresu.
> 
> 

## <a name="next-steps"></a>Następne kroki
Usługa Azure Wyszukiwanie poznawcze to atrakcyjny wybór dla wielu aplikacji. Oceniając różne wzorce projektowe dla aplikacji wielodostępnych, należy wziąć pod uwagę [różne warstwy cenowe](https://azure.microsoft.com/pricing/details/search/) i odpowiednie [limity usług](search-limits-quotas-capacity.md) , aby najlepiej dostosować platformę Azure wyszukiwanie poznawcze tak, aby pasowała do obciążeń aplikacji i architektury wszystkich rozmiarów.

Wszystkie pytania dotyczące usługi Azure Wyszukiwanie poznawcze i wielodostępne scenariusze można kierować do programu azuresearch_contact@microsoft.com .