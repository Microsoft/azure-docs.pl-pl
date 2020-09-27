---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Azure Cosmos DB ma pięć poziomów spójności, co pomaga zrównoważyć spójność, dostępność i wady opóźnienia.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8f482c4fe6817c75079ceb98e981c846c395ad13
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396029"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Jakie są poziomy spójności w Azure Cosmos DB?

Rozproszone bazy danych, które korzystają z replikacji w celu zapewnienia wysokiej dostępności, małych opóźnień lub obu, sprawiają zasadnicze kompromisy między spójnością odczytu a dostępnością, opóźnieniem i przepływności. Najbardziej komercyjnie dostępne bazy danych umożliwiają deweloperom wybór między dwoma ekstremalnymi modelami spójności: *silną* spójnością i spójnością *ostateczną* . Linearizability modelu silnej spójności jest standardem programowalności danych. Jednak dodaje cenę wyższego opóźnienia zapisu (w stanie stałym) i ograniczoną dostępność (podczas niepowodzeń). Z drugiej strony spójność ostateczna zapewnia wyższą dostępność i lepszą wydajność, ale utrudnia Programowanie aplikacji.

Azure Cosmos DB podejścia do spójności danych jako spektrum wyborów, a nie dwóch skrajnych. Deweloperzy mogą korzystać z tych opcji, aby wybierać precyzyjne i szczegółowe kompromisy w odniesieniu do wysokiej dostępności i wydajności.

Dzięki Azure Cosmos DB deweloperzy mogą wybierać spośród pięciu dobrze zdefiniowanych poziomów spójności w spektrum spójności. Te poziomy obejmują *mocną*, *powiązana nieaktualność*, *sesję*, *spójny prefiks*oraz spójność *ostateczną* . Poziomy są dobrze zdefiniowane i intuicyjne i mogą być używane w określonych rzeczywistych scenariuszach. Każdy poziom zapewnia [kompromisy dostępności i wydajności](consistency-levels-tradeoffs.md) i jest obsługiwane przez umowy SLA. Na poniższej ilustracji przedstawiono różne poziomy spójności jako spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Spójność jako spektrum" border="false" :::

Poziomy spójności to region-niezależny od i są gwarantowane dla wszystkich operacji niezależnie od regionu, w którym są obsługiwane odczyty i zapisy, liczbę regionów skojarzonych z kontem usługi Azure Cosmos lub czy Twoje konto jest skonfigurowane z jednym lub wieloma regionami zapisu.

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Spójność odczytu dotyczy pojedynczej operacji odczytu w zakresie partycji logicznej. Operacja odczytu może zostać wystawiona przez klienta zdalnego lub procedurę przechowywaną.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

Domyślny poziom spójności można skonfigurować w dowolnym momencie w ramach konta usługi Azure Cosmos. Domyślny poziom spójności skonfigurowany na Twoim koncie ma zastosowanie do wszystkich baz danych i kontenerów usługi Azure Cosmos w ramach tego konta. Wszystkie operacje odczytu i zapytania wydawane w odniesieniu do kontenera lub bazy danych domyślnie używają określonego poziomu spójności. Aby dowiedzieć się więcej, zobacz jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level). Możesz również zastąpić domyślny poziom spójności dla konkretnego żądania, aby dowiedzieć się więcej, zobacz jak [zastąpić domyślny artykuł poziomu spójności](how-to-manage-consistency.md?#override-the-default-consistency-level) .

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje związane z poziomami spójności

Kompleksowe umowy SLA zapewniane przez Azure Cosmos DB gwarantuje, że 100 procent żądań odczytu spełnia gwarancję spójności dla wybranego poziomu spójności. Żądanie odczytu spełnia warunki umowy SLA spójności, jeśli są spełnione wszystkie gwarancje spójności skojarzone z poziomem spójności. Precyzyjne definicje pięciu poziomów spójności w Azure Cosmos DB przy użyciu języka specyfikacji TLA + są dostępne w repozytorium GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Semantyka pięciu poziomów spójności została opisana tutaj:

- **Silne**: silna spójność oferuje gwarancję linearizability. Linearizability odwołuje się do obsługi żądań współbieżnie. Odczyty są gwarantowane do zwrócenia najnowszej zatwierdzonej wersji elementu. Klient nigdy nie widzi niezatwierdzonego ani częściowego zapisu. Użytkownicy zawsze mają zagwarantowany odczyt najnowszej zatwierdzonego zapisu.

  Poniższa ilustracja ilustruje silną spójność z notatkami muzycznymi. Po zapisaniu danych w regionie "zachodnie stany USA 2" podczas odczytywania danych z innych regionów otrzymujesz najnowszą wartość:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="wideo":::

- **Powiązana nieaktualność**: odczyty są gwarantowane do przestrzegania gwarancji o spójnym prefiksie. Odczyty mogą być opóźnione w stosunku do zapisu o większości *"K"* wersjach (czyli "aktualizacje") elementu lub interwału czasu *"T"* , w zależności od tego, który zostanie osiągnięty jako pierwszy. Innymi słowy, w przypadku wybrania ograniczonej nieodświeżoności, "nieaktualność" można skonfigurować na dwa sposoby:

- Liczba wersji (*K*) elementu
- Przedział czasu (*T*), przez który odczyty mogą być opóźnione za zapisem

Ograniczone nieaktualność oferuje całkowitą kolejność globalną poza oknem "przestarzałe". Gdy klient wykonuje operacje odczytu w obrębie regionu akceptującego zapisy, gwarancje wynikające z podanej nieodświeżonej spójności są identyczne z tymi gwarancjami przez silną spójność.

W oknie nieodświeżone, powiązana nieaktualność zapewnia następujące gwarancje spójności:

- Spójność klientów w tym samym regionie dla pojedynczego konta głównego = Strong
- Spójność klientów w różnych regionach dla pojedynczego konta głównego = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma wzorcami = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w różnych regionach dla konta z wieloma głównymi:

  Ograniczone nieodświeżoności są często wybierane przez aplikacje rozproszone globalnie, które oczekują niskich opóźnień zapisu, ale wymagają całkowitej gwarancji zamówienia globalnego. Powiązana nieaktualność jest doskonałe dla aplikacji, które udostępniają współpracę i udostępnianie grup, taktowanie, publikowanie/subskrybowanie/kolejkowanie itp. Poniższa ilustracja ilustruje ograniczone niezgodność ze spójnością z notatkami muzycznymi. Po zapisaniu danych w regionie "zachodnie stany USA 2" regiony "Wschodnie stany USA 2" i "Australia Wschodnia" odczytaj wartość zapisaną na podstawie skonfigurowanego maksymalnego czasu zwłoki lub maksymalnej liczby operacji:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="wideo":::

- **Sesja**: w ramach jednej sesji klienta zagwarantowane jest przestrzeganie spójnego prefiksu, odczytów monotoniczny, zapisów monotoniczny, odczytów i zapisów, a ponadto gwarantuje zapis w ramach funkcji odczytu. Przyjęto założenie pojedynczej sesji "składnika zapisywania" lub udostępnienie tokenu sesji dla wielu modułów zapisujących.

Klienci poza sesją wykonującą operacje zapisu będą widzieć następujące gwarancje:

- Spójność klientów w tym samym regionie dla pojedynczego konta głównego = spójny prefiks
- Spójność klientów w różnych regionach dla pojedynczego konta głównego = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma wzorcami = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w wielu regionach dla konta z wieloma głównymi:

  Spójność sesji to najczęściej używany poziom spójności dla jednego regionu, a także aplikacje rozproszone globalnie. Zapewnia ona opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej samej spójności ostatecznej, ale również zapewnia gwarancje spójności, które odpowiadają potrzebom aplikacji napisanych w kontekście użytkownika. Poniższa ilustracja ilustruje spójność sesji z notatkami muzycznymi. Moduł zapisujący "zachodnie stany USA 2" i "zachodnie stany USA 2" używają tej samej sesji (sesji A), tak aby jednocześnie odczytywać te same dane w tym samym czasie. Natomiast region "Australia Wschodnia" używa "Session B", więc otrzymuje dane później, ale w tej samej kolejności jak zapisy.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="wideo":::

- **Spójny prefiks**: zwrócone aktualizacje zawierają prefiks wszystkich aktualizacji bez przerw. Spójny poziom spójności prefiksu gwarantuje, że odczyty nigdy nie są wyświetlane.

Jeśli operacje zapisu zostały wykonane w podanej kolejności `A, B, C` , klient zobaczy `A` , `A,B` , lub `A,B,C` , ale nigdy nie z kolejności permutacji, takich jak `A,C` lub `B,A,C` . Spójny prefiks zapewnia opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej spójności ostatecznej, ale również zapewnia gwarancję zamówienia, która odpowiada potrzebom scenariuszy, w których kolejność jest ważna. 

Poniżej przedstawiono gwarancje spójności dla spójnego prefiksu:

- Spójność klientów w tym samym regionie dla pojedynczego konta głównego = spójny prefiks
- Spójność klientów w różnych regionach dla pojedynczego konta głównego = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma wzorcami = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w wielu regionach dla konta z wieloma głównymi:

Poniższa ilustracja ilustruje spójność prefiksu spójności z notatkami muzycznymi. We wszystkich regionach odczyty nigdy nie są wyświetlane w kolejności zapisu:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="wideo":::

- **Ostateczne**: nie ma gwarancji porządkowania dla operacji odczytu. W przypadku braku jakichkolwiek dalszych zapisów repliki staną się ostatecznie zbieżne.  
Spójność ostateczna to najsłaba forma spójności, ponieważ klient może odczytać wartości starsze niż te, które były wcześniej odczytywane. Spójność ostateczna jest idealnym miejscem, w którym aplikacja nie wymaga żadnych gwarancji związanych z porządkowaniem. Przykłady obejmują liczbę ponownych tweetów, polubień lub komentarzy niewielowątkowych. Na poniższej ilustracji przedstawiono spójność ostateczną z notatkami muzycznymi.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="wideo":::

## <a name="additional-reading"></a>Materiały uzupełniające

Aby dowiedzieć się więcej na temat koncepcji spójności, przeczytaj następujące artykuły:

- [TLA i specyfikacje wysokiego poziomu dla pięciu poziomów spójności oferowanych przez Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Spójność zreplikowanego danych omówiona przez siatkówki (wideo) przez Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Spójność zreplikowanego danych omówiona przez siatkówki (oficjalny dokument) przez Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Gwarancje sesji dla słabo spójnych replikowanych danych](https://dl.acm.org/citation.cfm?id=383631)
- [Wady spójności w projekcie nowoczesnej rozproszonej bazy danych: CAP jest tylko częścią artykułu](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Nieprobabilistycznea (PBS) w przypadku praktycznych częściowych kworum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Ostatecznie spójne — oglądany](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat poziomów spójności w Azure Cosmos DB, przeczytaj następujące artykuły:

- [Wybierz odpowiedni poziom spójności dla aplikacji](consistency-levels-choosing.md)
- [Poziomy spójności w interfejsach API Azure Cosmos DB](consistency-levels-across-apis.md)
- [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
- [Konfigurowanie domyślnego poziomu spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Zastępowanie domyślnego poziomu spójności](how-to-manage-consistency.md#override-the-default-consistency-level)
