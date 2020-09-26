---
title: Zrozumienie różnic między Azure Cosmos DB NoSQL i relacyjnymi bazami danych
description: W tym artykule opisano różnice między NoSQL i relacyjnymi bazami danych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 50d077c1d7c9e4e421a43a4e0379b57608d1192c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361770"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Zrozumienie różnic między NoSQL i relacyjnymi bazami danych

W tym artykule opisano niektóre z najważniejszych zalet baz danych NoSQL za pośrednictwem relacyjnych baz danych. Będziemy również omawiać niektóre wyzwania związane z pracą z usługą NoSQL. Szczegółowe omówienie różnych istniejących magazynów danych można znaleźć w artykule dotyczącym [wybierania odpowiedniego magazynu danych](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Wysoka przepływność

Jednym z najbardziej oczywistych wyzwań związanych z utrzymywaniem systemu relacyjnej bazy danych jest to, że większość aparatów relacyjnych stosuje blokady i zamki w celu wymuszenia ścisłej [semantyki kwasowej](https://en.wikipedia.org/wiki/ACID). Takie podejście ma korzyści wynikające z zapewnienia spójnego stanu danych w bazie danych. Istnieją jednak duże wady dotyczące współbieżności, opóźnień i dostępności. Ze względu na te podstawowe ograniczenia architektury, duże woluminy transakcyjne mogą spowodować konieczność ręcznego fragmentu danych. Implementowanie ręcznego fragmentowania może być czasochłonne i bolesnym.

W tych scenariuszach [rozproszone bazy danych](https://en.wikipedia.org/wiki/Distributed_database) mogą oferować bardziej skalowalne rozwiązanie. Jednak konserwacja nadal może być kosztowna i czasochłonna. Administratorzy mogą koniecznie wykonać dodatkowe czynności, aby zapewnić, że rozproszony charakter systemu jest przejrzysty. Mogą również mieć możliwość uwzględnienia "rozłącznego" charakteru bazy danych.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) upraszcza te wyzwania, wdrażając na całym świecie w całym regionie świadczenia usługi Azure. Zakresy partycji mogą być dynamicznie podzielona, aby bezproblemowo rozszerzać bazę danych w wierszu do aplikacji, jednocześnie utrzymując wysoką dostępność. Szczegółowe i ścisłe sterowanie z obsługą wielodostępności, oparte na chmurze, ułatwiają zarządzanie zasobami natywnymi [w ramach chmury i](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) przewidywalną wydajność. Partycjonowanie jest w pełni zarządzane, dlatego administratorzy nie muszą pisać kodu lub zarządzać partycjami.

Jeśli woluminy transakcyjne są osiągane na poziomach skrajnych, takich jak wiele tysięcy transakcji na sekundę, należy wziąć pod uwagę rozproszoną bazę danych NoSQL. Rozważ Azure Cosmos DB, aby uzyskać maksymalną wydajność, łatwość konserwacji i obniżyć całkowity koszt posiadania.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Danych" border="false":::

## <a name="hierarchical-data"></a>Dane hierarchiczne

Istnieje znaczna liczba przypadków użycia, w których transakcje w bazie danych mogą zawierać wiele relacji nadrzędny-podrzędny. Te relacje mogą znacząco rosnąć w czasie i trudno było nimi zarządzać. Formy [hierarchicznych baz danych](https://en.wikipedia.org/wiki/Hierarchical_database_model) zostały napotkane podczas 1980s, ale nie były popularne z powodu niewydajności w magazynie. Również utraciły trakcję, ponieważ [model relacyjny Ted Codd](https://en.wikipedia.org/wiki/Relational_model) stał się niefaktycznym standardem używanym przez praktycznie wszystkie systemy zarządzania bazami danych.

Jednak obecnie popularność baz danych w stylu dokumentu znacznie się zwiększyła. Te bazy danych mogą być uznawane za przechowanie hierarchicznego modelu baz danych, a teraz nie są one powstrzymane przez problemy związane z przechowywaniem danych na dysku. W efekcie utrzymywanie wielu złożonych relacji jednostek nadrzędny-podrzędny w relacyjnej bazie danych może teraz być uznawane za antywzorców w porównaniu z nowoczesnymi podejściami zorientowanymi na dokumenty.

Problem z modelem zorientowanym na obiekty oraz [niezgodność](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) z [założeniami](https://en.wikipedia.org/wiki/Object-oriented_design), które powstają podczas łączenia go z modeli relacyjnych, powoduje również wyróżnienie antywzorców w relacyjnych bazach danych dla niektórych przypadków użycia. Ukryte, ale często mające znaczący koszt konserwacji, mogą wynikać z tego powodu. Mimo że [podejścia ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) zostały rozwijające się częściowo w ten sposób, to zorientowane na dokumenty bazy danych są znacznie lepiej połączone z podejście zorientowane obiektowo. W tym podejściu deweloperzy nie są zmuszeni do zatwierdzania sterowników ORM ani Bespoke [aparatów bazy danych oo](https://en.wikipedia.org/wiki/Object_database). Jeśli dane zawierają wiele relacji nadrzędny-podrzędny i poziomów hierarchii, warto rozważyć użycie bazy danych dokumentów NoSQL, takiej jak [Azure Cosmos DB interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/introduction).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Złożone sieci i relacje

Ironically, uwzględniając swoją nazwę, relacyjne bazy danych obecne mniej niż optymalne rozwiązanie do modelowania głębokości i złożonych relacji. Przyczyną tego jest fakt, że relacje między jednostkami nie istnieją rzeczywiście w relacyjnej bazie danych. Muszą one być obliczane w czasie wykonywania, z relacjami złożonymi wymagającymi sprzężeń kartezjańskiego, aby zezwolić na mapowanie przy użyciu zapytań. W związku z tym operacje stają się wykładniczo droższe w zakresie obliczeń w miarę wzrostu relacji. W niektórych przypadkach relacyjna baza danych próbująca zarządzać takimi jednostkami stanie się bezużyteczny.

Różne formy baz danych "sieci" pojawiły się w czasie, gdy relacyjne bazy danych zakończyły się, ale podobnie jak w przypadku hierarchicznych baz danych, te systemy są niezależne do uzyskania popularności. Wolne przyjęcie zostało spowodowane brakiem przypadków użycia i nieefektywność magazynu. Obecnie aparaty bazy danych grafu mogą być uważane za ponowne wystąpienie modelu bazy danych sieci. Najważniejsze korzyści związane z tymi systemami polegają na tym, że relacje są przechowywane jako "" pierwszej klasy obywatele "w bazie danych. W związku z tym, przechodzenie między relacjami może odbywać się w stałym czasie, zamiast zwiększać złożoność każdego nowego sprzężenia lub iloczynu krzyżowego.

W przypadku zachowania złożonej sieci relacji w bazie danych warto rozważyć użycie bazy danych grafu, takiej jak [Azure Cosmos DB API Gremlin](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) do zarządzania tymi danymi.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Diagram bazy danych przedstawia kilku pracowników i działów ze sobą połączone.":::

Azure Cosmos DB to wielomodelowa usługa bazy danych, która oferuje projekcję interfejsu API dla wszystkich głównych typów modeli NoSQL; Rodzina kolumn, dokument, wykres i wartość klucza. Warstwy interfejsu API dokumentów [Gremlin (Graph)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) i SQL (rdzeń) są w pełni obsługiwane. Ma to zalety przełączenia między różnymi modelami na poziomie programowania. W sklepach grafów można wykonywać zapytania dotyczące zarówno złożonych przechodzenia do sieci, jak i transakcji, które są modelowane jako rekordy dokumentów w tym samym magazynie.

## <a name="fluid-schema"></a>Schemat cieczy

Inną szczególną cechą relacyjnych baz danych jest to, że schematy są wymagane do zdefiniowania w czasie projektowania. Ma to korzyści ze względu na integralność referencyjną i zgodność danych. Jednak może być również restrykcyjna, gdy aplikacja rośnie. Reagowanie na zmiany w schemacie między logicznie oddzielnymi modelami, które współużytkują tę samą tabelę lub definicję bazy danych, mogą stać się złożone w czasie. Takie przypadki użycia często korzystają ze schematu devolved do aplikacji, aby zarządzać dla poszczególnych rekordów. Wymaga to, aby baza danych była "Schema niezależny od", i zezwalać na "samoopisujący się" w odniesieniu do zawartych w nich danych.

Jeśli zarządzasz danymi, których struktury stale się zmieniają, szczególnie w przypadku, gdy transakcje mogą pochodzić ze źródeł zewnętrznych, w których trudno jest wymusić zgodność między bazami danych, warto rozważyć więcej podejścia niezależny od schemat przy użyciu zarządzanej usługi bazy danych NoSQL, takiej jak Azure Cosmos DB.

## <a name="microservices"></a>Mikrousługi

Wzorzec [mikrousług](https://en.wikipedia.org/wiki/Microservices) znacznie się zwiększył w ciągu ostatnich lat. Ten wzorzec ma swoje elementy główne w [architekturze zorientowanej na usługę](https://en.wikipedia.org/wiki/Service-oriented_architecture). Ogólnie obowiązującym standardem przesyłania danych w tych nowoczesnych architekturach mikrousług jest [kod JSON](https://en.wikipedia.org/wiki/JSON), który stanowi również nośnik magazynu dla większości baz danych NoSQL zorientowanych na dokumenty. Dzięki temu dokument NoSQL przechowuje znacznie bardziej bezproblemowe dopasowanie do trwałości i synchronizacji (przy użyciu wzorców określania [źródła zdarzeń](https://en.wikipedia.org/wiki/Event-driven_architecture)) w ramach złożonych implementacji mikrousług. Bardziej skomplikowane relacyjne bazy danych mogą być znacznie bardziej złożone, aby zachować je w tych architekturach. Jest to spowodowane większą ilością transformacji wymaganą zarówno w przypadku stanu, jak i synchronizacji w ramach interfejsów API. Azure Cosmos DB w szczególności zawiera szereg funkcji, które sprawiają, że jeszcze bardziej bezproblemowe w przypadku architektury mikrousług opartych na notacji JSON niż wiele baz danych NoSQL:

* wybór czystych typów danych JSON
* aparat JavaScript i [interfejs API zapytania](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) wbudowane w bazę danych.
* aktualny [kanał informacyjny zmiany](https://docs.microsoft.com/azure/cosmos-db/change-feed) , który klienci mogą subskrybować w celu uzyskania powiadomień o zmianach w kontenerze.

## <a name="some-challenges-with-nosql-databases"></a>Niektóre wyzwania z bazami danych NoSQL

Chociaż podczas implementowania baz danych NoSQL istnieją pewne jasne zalety, istnieją również pewne wyzwania, które warto wziąć pod uwagę. Mogą one nie być obecne w tym samym stopniu podczas pracy z modelem relacyjnym:

* transakcje z wieloma relacjami wskazujące tę samą jednostkę.
* transakcje wymagające silnej spójności w całym zestawie danych.

Analizując pierwsze wyzwanie, reguła-kciuk w bazach danych NoSQL jest ogólnie nieznormalizowana, którą podano wcześniej, w systemie rozproszonym bardziej wydajne operacje odczytu. Istnieje jednak kilka wyzwań związanych z projektowaniem, które są dostępne w ramach tego podejścia. Przyjrzyjmy się przykładowi do produktu, który jest powiązany z jedną kategorią i wieloma tagami:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Sprzężenia":::

Najlepszym rozwiązaniem w bazie danych dokumentów NoSQL jest denormalizacja nazwy kategorii i tagów bezpośrednio w "dokumencie produktu". Jednak w celu zachowania synchronizacji kategorii, tagów i produktów, opcje projektowania ułatwiające to dodanie złożoności konserwacji, ponieważ dane są duplikowane przez wiele rekordów w produkcie, a nie jako proste aktualizacje w relacji "jeden do wielu", a następnie przyłączanie do pobierania danych. 

Jest to, że odczyty są bardziej wydajne w nieznormalizowanym rekordzie i stają się coraz bardziej wydajne w miarę zwiększania się liczby jednostek przyłączonych koncepcyjnie. Jednak podobnie jak wydajność odczytu zwiększa się wraz ze wzrostem liczby przyłączonych jednostek w rekordzie denormalizacji, tak że jest to zbyt skomplikowana złożoność utrzymywania obiektów w synchronizacji. Jednym ze sposobów złagodzenia tego problemu jest utworzenie [hybrydowego modelu danych](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models).

Mimo że w bazach danych NoSQL jest dostępna większa elastyczność, dzięki czemu może ona również zwiększyć elastyczność w zakresie projektowania. Zapoznaj się z artykułem [jak modelować i dzielić dane na Azure Cosmos dB przy użyciu rzeczywistego przykładu](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), który obejmuje podejście do utrzymywania [nieznormalizowanych danych użytkownika w ramach synchronizacji](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) , w których użytkownicy nie tylko znajdują się w różnych partycjach, ale w różnych kontenerach.

W odniesieniu do silnej spójności bardzo rzadko jest to wymagane w całym zestawie danych. Jednak w przypadkach, gdy jest to konieczne, może to być wyzwanie w rozproszonych bazach danych. Aby zapewnić silną spójność, dane muszą zostać zsynchronizowane we wszystkich replikach i regionach przed zezwoleniem klientom na ich odczytywanie. Może to zwiększyć opóźnienie operacji odczytu.

Azure Cosmos DB zapewnia większą elastyczność niż relacyjne bazy danych dla różnych, które są istotne w tym miejscu, ale w przypadku implementacji małych skali takie podejście może zwiększyć zagadnienia dotyczące projektowania. Zapoznaj się z naszym artykułem dotyczącym [spójności, dostępności i wydajności,](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) Aby uzyskać więcej szczegółów na temat tego tematu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać kontem usługi Azure Cosmos i innymi pojęciami:

* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Praca z kontenerami i elementami platformy Azure Cosmos](databases-containers-items.md)
* [Punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [IP-Zapora dla konta usługi Azure Cosmos](firewall-support.md)
* [Jak dodawać i usuwać regiony platformy Azure na koncie usługi Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
