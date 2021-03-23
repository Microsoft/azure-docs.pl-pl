---
title: Poziomy spójności w usłudze Azure Cosmos DB
description: Azure Cosmos DB ma pięć poziomów spójności, co pomaga zrównoważyć spójność, dostępność i wady opóźnienia.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0a203531e026d00b274ac98784076d33b22666d8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800146"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Poziomy spójności w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Rozproszone bazy danych korzystające z replikacji w celu zapewnienia wysokiej dostępności, małych opóźnień lub obu muszą mieć zasadnicze kompromisy między spójnością odczytu, dostępnością, opóźnieniem i przepływności, zgodnie z definicją [PACLC theorem](https://en.wikipedia.org/wiki/PACELC_theorem). Linearizability modelu silnej spójności jest standardem programowalności danych. Jednak dodaje ogromną cenę od wyższych opóźnień zapisu ze względu na dane, które mają być replikowane i zatwierdzane na dużą odległość. Silna spójność może również mieć wpływ na ograniczoną dostępność (podczas niepowodzeń), ponieważ dane nie mogą być replikowane i zatwierdzane w każdym regionie. Ciągła spójność oferuje wyższą dostępność i lepszą wydajność, ale trudniejsze do programowania aplikacji, ponieważ dane mogą nie być całkowicie spójne we wszystkich regionach.

Najbardziej komercyjnie dostępne bazy danych NoSQL, dostępne na rynku, już dziś zapewniają jedynie silną i ostateczną spójność. Azure Cosmos DB oferuje pięć dokładnie zdefiniowanych poziomów. Od najsilniejszych do najsłabszych poziomów są:

- *Silna*
- *Powiązana nieaktualność*
- *Sesja*
- *Spójny prefiks*
- *Ostateczna*

Każdy poziom zapewnia kompromisy dostępności i wydajności. Na poniższej ilustracji przedstawiono różne poziomy spójności jako spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Spójność jako spektrum" border="false" :::

Poziomy spójności to region-niezależny od i są gwarantowane dla wszystkich operacji niezależnie od regionu, w którym są obsługiwane odczyty i zapisy, liczbę regionów skojarzonych z kontem usługi Azure Cosmos lub czy Twoje konto jest skonfigurowane z jednym lub wieloma regionami zapisu.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Poziomy spójności i interfejsy API usługi Azure Cosmos DB

Azure Cosmos DB zapewnia natywną obsługę interfejsów API zgodnych z protokołem przewodowym dla popularnych baz danych. Obejmują one MongoDB, Apache Cassandra, Gremlin i Azure Table Storage. W przypadku korzystania z interfejsu API Gremlin i interfejs API tabel, używany jest domyślny poziom spójności skonfigurowany na koncie usługi Azure Cosmos. Aby uzyskać szczegółowe informacje na temat mapowania poziomu spójności między interfejs API Cassandra lub interfejsem API dla poziomów spójności MongoDB i Azure Cosmos DB, zobacz [interfejs API Cassandra mapowania spójności](cassandra-consistency.md) i [interfejsu API dla mapowania spójności MongoDB](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Zakres spójności odczytu

Spójność odczytu dotyczy pojedynczej operacji odczytu w zakresie partycji logicznej. Operacja odczytu może zostać wystawiona przez klienta zdalnego lub procedurę przechowywaną.

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

Domyślny poziom spójności można skonfigurować w dowolnym momencie w ramach konta usługi Azure Cosmos. Domyślny poziom spójności skonfigurowany na Twoim koncie ma zastosowanie do wszystkich baz danych i kontenerów usługi Azure Cosmos w ramach tego konta. Wszystkie operacje odczytu i zapytania wydawane w odniesieniu do kontenera lub bazy danych domyślnie używają określonego poziomu spójności. Aby dowiedzieć się więcej, zobacz jak [skonfigurować domyślny poziom spójności](how-to-manage-consistency.md#configure-the-default-consistency-level). Możesz również zastąpić domyślny poziom spójności dla konkretnego żądania, aby dowiedzieć się więcej, zobacz jak [zastąpić domyślny artykuł poziomu spójności](how-to-manage-consistency.md?#override-the-default-consistency-level) .

> [!IMPORTANT]
> Przed zmianą domyślnego poziomu spójności należy ponownie utworzyć dowolne wystąpienie zestawu SDK. Można to zrobić, uruchamiając ponownie aplikację. Dzięki temu zestaw SDK używa nowego domyślnego poziomu spójności.

## <a name="guarantees-associated-with-consistency-levels"></a>Gwarancje związane z poziomami spójności

Azure Cosmos DB gwarantuje, że 100 procent żądań odczytu spełnia gwarancję spójności dla wybranego poziomu spójności. Precyzyjne definicje pięciu poziomów spójności w Azure Cosmos DB przy użyciu języka specyfikacji TLA + są dostępne w repozytorium GitHub [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) .

Semantyka pięciu poziomów spójności jest opisana w poniższych sekcjach.

### <a name="strong-consistency"></a>Silna spójność

Silna spójność zapewnia gwarancję operacji atomowych. Linearizability odwołuje się do obsługi żądań współbieżnie. Odczyty są gwarantowane do zwrócenia najnowszej zatwierdzonej wersji elementu. Klient nigdy nie widzi niezatwierdzonego ani częściowego zapisu. Użytkownicy zawsze mają zagwarantowany odczyt najnowszej zatwierdzonego zapisu.

  Poniższa ilustracja ilustruje silną spójność z notatkami muzycznymi. Po zapisaniu danych w regionie "zachodnie stany USA 2" podczas odczytywania danych z innych regionów otrzymujesz najnowszą wartość:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Ilustracja przedstawiająca wysoki poziom spójności":::

### <a name="bounded-staleness-consistency"></a>Powiązana nieaktualność

Ze względu na nieaktualność, odczyty są gwarantowane do przestrzegania gwarancji o spójnym prefiksie. Odczyty mogą być opóźnione w stosunku do zapisu o większości *"K"* wersjach (czyli "aktualizacje") elementu lub interwału czasu *"T"* , w zależności od tego, który zostanie osiągnięty jako pierwszy. Innymi słowy, w przypadku wybrania ograniczonej nieodświeżoności, "nieaktualność" można skonfigurować na dwa sposoby:

- Liczba wersji (*K*) elementu
- Odczyty interwału czasu (*T*) mogą być opóźnieniami za zapisem

W przypadku konta z jednym regionem minimalna wartość *K* i *T* to 10 operacji zapisu lub 5 sekund. W przypadku kont wieloregionowych minimalna wartość *K* i *T* to 100 000 operacji zapisu lub 300 sekund.

Ograniczone nieaktualność oferuje całkowitą kolejność globalną poza oknem "przestarzałe". Gdy klient wykonuje operacje odczytu w obrębie regionu akceptującego zapisy, gwarancje wynikające z podanej nieodświeżonej spójności są identyczne z tymi gwarancjami przez silną spójność. Jak okno przestarzałe zbliża się do czasu lub aktualizacji, w zależności od tego, która z nich jest bliższa, usługa ogranicza nowe zapisy, aby umożliwić replikację i honorować gwarancję spójności.

W oknie nieodświeżone, powiązana nieaktualność zapewnia następujące gwarancje spójności:

- Spójność klientów w tym samym regionie dla konta z pojedynczym regionem zapisu = Strong
- Spójność klientów w różnych regionach dla konta z pojedynczym regionem zapisu = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma regionami zapisu = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w różnych regionach dla konta z wieloma regionami zapisu = ostatecznie

  Ograniczone nieodświeżoności są często wybierane przez aplikacje rozproszone globalnie, które oczekują niskich opóźnień zapisu, ale wymagają całkowitej gwarancji zamówienia globalnego. Powiązana nieaktualność jest doskonałe dla aplikacji, które udostępniają współpracę i udostępnianie grup, taktowanie, publikowanie/subskrybowanie/kolejkowanie itp. Poniższa ilustracja ilustruje ograniczone niezgodność ze spójnością z notatkami muzycznymi. Po zapisaniu danych w regionie "zachodnie stany USA 2" regiony "Wschodnie stany USA 2" i "Australia Wschodnia" odczytaj wartość zapisaną na podstawie skonfigurowanego maksymalnego czasu zwłoki lub maksymalnej liczby operacji:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Ilustracja przedstawiająca ograniczony poziom spójności niezgodności":::

### <a name="session-consistency"></a>Spójność sesji

W ramach spójności sesji w ramach jednej sesji klienta zagwarantowane jest przestrzeganie spójnego prefiksu, odczytów monotoniczny, zapisów monotoniczny, odczytów i zapisów oraz zapisywanych w wyniku zapisów. Przyjęto założenie pojedynczej sesji "składnika zapisywania" lub udostępnienie tokenu sesji dla wielu modułów zapisujących.

Klienci poza sesją wykonującą operacje zapisu będą widzieć następujące gwarancje:

- Spójność klientów w tym samym regionie dla konta z pojedynczym regionem zapisu = spójny prefiks
- Spójność klientów w różnych regionach dla konta z pojedynczym regionem zapisu = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma regionami zapisu = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w wielu regionach dla konta z wieloma regionami zapisu = ostatecznie

  Spójność sesji to najczęściej używany poziom spójności dla jednego regionu, a także aplikacje rozproszone globalnie. Zapewnia ona opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej samej spójności ostatecznej, ale również zapewnia gwarancje spójności, które odpowiadają potrzebom aplikacji napisanych w kontekście użytkownika. Poniższa ilustracja ilustruje spójność sesji z notatkami muzycznymi. Moduł zapisujący "zachodnie stany USA 2" i "zachodnie stany USA 2" używają tej samej sesji (sesji A), tak aby jednocześnie odczytywać te same dane w tym samym czasie. Natomiast region "Australia Wschodnia" używa "Session B", więc otrzymuje dane później, ale w tej samej kolejności jak zapisy.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Ilustracja poziomu spójności sesji":::

### <a name="consistent-prefix-consistency"></a>Spójny prefiks

W przypadku opcji spójnego prefiksu aktualizacje, które są zwracane, zawierają prefiks wszystkich aktualizacji bez przerw. Spójny poziom spójności prefiksu gwarantuje, że odczyty nigdy nie są wyświetlane.

Jeśli operacje zapisu zostały wykonane w podanej kolejności `A, B, C` , klient zobaczy `A` , `A,B` , lub `A,B,C` , ale nigdy nie z kolejności permutacji, takich jak `A,C` lub `B,A,C` . Spójny prefiks zapewnia opóźnienia zapisu, dostępność i przepływność odczytu porównywalne do tej spójności ostatecznej, ale również zapewnia gwarancję zamówienia, która odpowiada potrzebom scenariuszy, w których kolejność jest ważna.

Poniżej przedstawiono gwarancje spójności dla spójnego prefiksu:

- Spójność klientów w tym samym regionie dla konta z pojedynczym regionem zapisu = spójny prefiks
- Spójność klientów w różnych regionach dla konta z pojedynczym regionem zapisu = spójny prefiks
- Spójność zapisywania klientów w jednym regionie dla konta z wieloma regionami zapisu = spójny prefiks
- Spójność w przypadku klientów, którzy zapisują w wielu regionach dla konta z wieloma regionami zapisu = ostatecznie

Poniższa ilustracja ilustruje spójność prefiksu spójności z notatkami muzycznymi. We wszystkich regionach odczyty nigdy nie są wyświetlane w kolejności zapisu:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Ilustracja przedstawiająca spójny prefiks":::

### <a name="eventual-consistency"></a>Spójność ostateczna

W przypadku spójności ostatecznej nie ma gwarancji porządkowania dla operacji odczytu. W przypadku braku jakichkolwiek dalszych zapisów repliki staną się ostatecznie zbieżne.  
Spójność ostateczna to najsłaba forma spójności, ponieważ klient może odczytać wartości starsze niż te, które były wcześniej odczytywane. Spójność ostateczna jest idealnym miejscem, w którym aplikacja nie wymaga żadnych gwarancji związanych z porządkowaniem. Przykłady obejmują liczbę ponownych tweetów, polubień lub komentarzy niewielowątkowych. Na poniższej ilustracji przedstawiono spójność ostateczną z notatkami muzycznymi.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration spójności ostatecznej":::

## <a name="consistency-guarantees-in-practice"></a>Gwarancje spójności w programie

W tym przypadku często można uzyskać silniejsze gwarancje spójności. Gwarancje spójności operacji odczytu odnoszą się do aktualności i kolejności żądanego stanu bazy danych. Spójność odczytu jest związana z porządkowaniem i propagacją operacji zapisu/aktualizacji.  

Jeśli w bazie danych nie ma żadnych operacji **zapisu, operacja odczytu z poziomem** spójności, **sesja** lub **spójny prefiks** może dać te same wyniki co operacja odczytu o silnym poziomie spójności.

Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z poziomem spójności innym niż silna spójność, można sprawdzić prawdopodobieństwo, że klienci mogą uzyskać mocne i spójne odczyty dla obciążeń, patrząc na metrykę *probabilistically z Nieodświeżoną* (PBS). Ta Metryka jest dostępna w Azure Portal, aby dowiedzieć się więcej, zobacz [monitorowanie metryki probabilistically ograniczonej (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistyczne ograniczone nieaktualność pokazuje, jak to jest spójność ostateczna. Ta Metryka zawiera szczegółowe informacje o tym, jak często można uzyskać większą spójność niż poziom spójności skonfigurowany obecnie na koncie usługi Azure Cosmos. Innymi słowy, można zobaczyć prawdopodobieństwo (mierzone w milisekundach), aby uzyskać silnie spójne odczyty dla kombinacji regionów zapisu i odczytu.

## <a name="consistency-levels-and-latency"></a>Poziomy spójności i czas oczekiwania

Opóźnienie odczytu dla wszystkich poziomów spójności zawsze gwarantuje mniej niż 10 milisekund w 99 percentylu. Średnie opóźnienie odczytu w pięćdziesiąt percentylu jest zwykle 4 milisekund lub mniej.

Czas oczekiwania na zapis dla wszystkich poziomów spójności jest zawsze gwarantowany poniżej 10 milisekund w 99 percentylu. Średnie opóźnienie zapisu w pięćdziesiąt percentylu jest zwykle 5 milisekund lub mniej. W przypadku kont usługi Azure Cosmos, które obejmują kilka regionów i skonfigurowano silną spójność, są wyjątkiem od tej gwarancji.

### <a name="write-latency-and-strong-consistency"></a>Opóźnienie zapisu i silna spójność

W przypadku kont usługi Azure Cosmos skonfigurowanych pod kątem silnej spójności z więcej niż jednym regionem opóźnienie zapisu jest równe dwukrotnemu czasowi błądzenia (RTT) między dowolnymi z dwóch najwyższych regionów i 10 milisekund w 99 percentylu. Wysoka wartość czasu RTT w sieci między regionami zostanie przeprowadzona w celu uzyskania większej opóźnienia w przypadku żądań Cosmos DB, ponieważ silna spójność kończy działanie dopiero po upewnieniu się, że został on przekazany do wszystkich regionów w ramach konta.

Dokładne opóźnienie czasu RTT to funkcja szybkości i topologii sieci platformy Azure. Usługa Azure Network nie zapewnia umowy SLA opóźnienia dla czasu RTT między dowolnymi dwoma regionami świadczenia usługi Azure, jednak publikuje [statystyki opóźnienia w sieci platformy Azure](../networking/azure-network-latency.md). W Azure Portal są wyświetlane opóźnienia replikacji w ramach konta usługi Azure Cosmos. Możesz użyć Azure Portal (przejdź do bloku metryk, wybierz kartę spójności), aby monitorować opóźnienia replikacji między różnymi regionami, które są skojarzone z kontem usługi Azure Cosmos.

> [!IMPORTANT]
> Silna spójność kont z regionami obejmującymi ponad 5000 kilometrów (8000 kilometrów) jest domyślnie zablokowana z powodu dużego opóźnienia zapisu. Aby włączyć tę funkcję, skontaktuj się z pomocą techniczną.

## <a name="consistency-levels-and-throughput"></a>Poziomy spójności i przepływność

- Aby zapewnić silną i powiązana nieaktualność, odczyty są przeprowadzane w przypadku dwóch replik z czterema zestawem replik (kworum mniejszościowego) w celu zapewnienia spójności. Sesja, spójny prefiks i ostateczne odczyty pojedynczej repliki. W efekcie, w przypadku tej samej liczby jednostek żądania, przepływność odczytu dla silnych i ograniczonych nieodświeżonych jest połowami innych poziomów spójności.

- Dla danego typu operacji zapisu, takich jak INSERT, Replace, upsert i DELETE, przepływność zapisu dla jednostek żądania jest taka sama dla wszystkich poziomów spójności.

|**Poziom spójności**|**Odczyty kworum**|**Zapisy kworum**|
|--|--|--|
|**Silna**|Lokalna mniejszości|Większość globalna|
|**Powiązana nieaktualność**|Lokalna mniejszości|Większość lokalna|
|**Sesja**|Pojedyncza replika (przy użyciu tokenu sesji)|Większość lokalna|
|**Spójny prefiks**|Pojedyncza replika|Większość lokalna|
|**Ostateczna**|Pojedyncza replika|Większość lokalna|

> [!NOTE]
> Koszt operacji odczytu (RU/s) dla lokalnego odczytu mniejszościowego jest dwa razy większy niż słabsze poziomy spójności, ponieważ odczyty są wykonywane z dwóch replik w celu zapewnienia spójności mocnej i ograniczonej nieodświeżoności.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Poziomy spójności i trwałość danych

W globalnie rozproszonym środowisku bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w przypadku awarii całego regionu. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po zdarzeniu zakłócania. Czas wymagany do pełnego odzyskania aplikacji jest znany jako **cel czasu odzyskiwania** (**RTO**). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych, które aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia. Przedział czasu aktualizacji, które mogą zostać utracone, jest określany jako **cel punktu odzyskiwania** (**RPO**).

W poniższej tabeli zdefiniowano relacje między modelem spójności i trwałością danych w przypadku awarii całego regionu. Należy pamiętać, że w systemie rozproszonym, nawet ze silną spójnością, nie można mieć rozproszonej bazy danych z celem punktu odzyskiwania i RTO równym zero ze względu na [theorem](https://en.wikipedia.org/wiki/CAP_theorem).

|**Regiony**|**Tryb replikacji**|**Poziom spójności**|**Cel punktu odzyskiwania**|**Cel czasu odzyskiwania**|
|---------|---------|---------|---------|---------|
|1|Jeden lub wiele regionów zapisu|Dowolny poziom spójności|< 240 minut|<1 tydzień|
|>1|Pojedynczy region zapisu|Sesja, spójny prefiks, ostateczna|< 15 minut|< 15 minut|
|>1|Pojedynczy region zapisu|Powiązana nieaktualność|*K*  &  *T*|< 15 minut|
|>1|Pojedynczy region zapisu|Silna|0|< 15 minut|
|>1|Wiele regionów zapisu|Sesja, spójny prefiks, ostateczna|< 15 minut|0|
|>1|Wiele regionów zapisu|Powiązana nieaktualność|*K*  &  *T*|0|

*K* = liczba wersji *"K"* (tj. aktualizacji) elementu.

*T* = przedział czasu *"T"* od momentu ostatniej aktualizacji.

W przypadku konta z jednym regionem minimalna wartość *K* i *T* to 10 operacji zapisu lub 5 sekund. W przypadku kont wieloregionowych minimalna wartość *K* i *T* to 100 000 operacji zapisu lub 300 sekund. Definiuje minimalny cel punktu odzyskiwania dla danych w przypadku korzystania z ograniczenia nieodświeżoności.

## <a name="strong-consistency-and-multiple-write-regions"></a>Silna spójność i wiele regionów zapisu

Kont Cosmos skonfigurowanych z wieloma regionami zapisu nie można skonfigurować pod kątem silnej spójności, ponieważ nie jest to możliwe w systemie rozproszonym do zapewnienia punktu odzyskiwania równego zero i RTO równego zero. Ponadto nie ma korzyści z opóźnienia zapisu w przypadku używania silnej spójności z wieloma regionami zapisu, ponieważ zapis w dowolnym regionie musi być replikowany i zatwierdzony do wszystkich skonfigurowanych regionów w ramach konta. Powoduje to takie samo opóźnienie zapisu jak jedno konto regionu zapisu.

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

- [Konfigurowanie domyślnego poziomu spójności](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Zastępowanie domyślnego poziomu spójności](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
