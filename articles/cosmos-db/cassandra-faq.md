---
title: Często zadawane pytania dotyczące interfejsu API bazy danych usługi Azure Cosmos dla usługi Cassandra.
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API bazy danych Usługi Azure Cosmos dla usługi Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727387"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Często zadawane pytania dotyczące interfejsu API bazy danych usługi Azure Cosmos dla usługi Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Jakie są kluczowe różnice między Apache Cassandra i Cassandra API?

- Apache Cassandra zaleca limit 100MB na rozmiar klucza partycji. Api Cassandra pozwala na maksymalnie 10 GB na partycję.
- Apache Cassandra pozwala wyłączyć trwałe zatwierdzenia - czyli pominąć zapisywanie do dziennika zatwierdzania i przejść bezpośrednio do Memtable(s). Może to prowadzić do utraty danych, jeśli węzeł ulegnie upadkowi przed opróżnieniem Memtables do tabel SStables na dysku. Usługa Cosmos DB zawsze wykonuje trwałe zatwierdzenia, dzięki czemu nigdy nie będzie utraty danych.
- Apache Cassandra może zobaczyć zmniejszoną wydajność, jeśli obciążenie wiąże się z dużą ilością zastępuje i/lub usuwa. Powodem tego jest relikmy, które obciążenia odczytu musi pominąć, aby pobrać najnowsze dane. Interfejs API cassandra nie będzie widoczny zmniejszonej wydajności odczytu, gdy obciążenie ma wiele zastępów i/lub usuwa.
- Podczas scenariuszy obciążenia o dużej wymianie zagęszczanie musi zostać uruchomione w celu scalenia tabel SSTable na dysku (scalanie jest potrzebne, ponieważ zapisy Apache Cassandra są dołączane tylko, w związku z czym wiele aktualizacji jest przechowywanych jako pojedyncze wpisy SSTable, które muszą być okresowo scalane). Może to również prowadzić do obniżenia wydajności odczytu podczas zagęszczania. Nie występuje to w api Cassandra, ponieważ nie implementuje zagęszczania.
- Ustawienie współczynnika replikacji 1 jest możliwe z Apache Cassandra. Jednak prowadzi to do niskiej dostępności, jeśli tylko węzeł z danymi ulegnie ulegnie upadkowi. Nie jest to problem z interfejsem API Cassandra usługi Azure Cosmos DB, ponieważ zawsze występuje współczynnik replikacji 4 (kworum 3).
- Dodawanie/usuwanie węzłów w Apache Cassandra wymaga dużo ręcznej interwencji, ale także wysoki procesor CPU w nowym węźle, podczas gdy istniejące węzły przenieść niektóre z ich zakresów tokenów do nowego węzła. Jest to samo podczas likwidacji istniejącego węzła. Jednak skalowanie w poziomie odbywa się bezproblemowo pod maską w usłudze Azure Cosmos DB Cassandra INTERFEJSU API, bez żadnych problemów obserwowanych w usłudze/aplikacji.
- Nie ma potrzeby ustawiania num_tokens w każdym węźle w klastrze, tak jak w Apache Cassandra. Zakresy węzłów i tokenów są w pełni zarządzane przez usługi Cosmos DB.
- Interfejs API cassandra usługi Azure Cosmos DB jest w pełni zarządzany, więc nie wymagają poleceń nodetool, takich jak naprawa, likwidacja itp., które są używane w Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Inne często zadawane pytania

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jaka jest wersja protokołu obsługiwana przez interfejs API Cassandra usługi Azure Cosmos DB Cassandra? Czy istnieje plan obsługi innych protokołów?

Interfejs API Cassandra usługi Azure Cosmos DB obsługuje interfejs CQL w wersji 3.x. Jest to kompatybilność CQL opiera się na publicznym [repozytorium Apache Cassandra GitHub](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Jeśli masz opinię na temat obsługi innych protokołów, daj nam znać [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)za pośrednictwem [opinii głosowej użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db) lub wyślij wiadomość e-mail na adres .

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Dlaczego wybieranie przepływności dla tabeli jest wymaganiem?

Usługa Azure Cosmos DB ustawia domyślną przepływność kontenera na podstawie miejsca tworzenia tabeli z — portalu lub CQL.
Usługa Azure Cosmos DB zapewnia gwarancje wydajności i opóźnienia, z górnymi granicami w operacji. Ta gwarancja jest możliwe, gdy aparat może wymusić zarządzanie w operacjach dzierżawy. Ustawienie przepływności zapewnia uzyskanie gwarantowanej przepływności i opóźnienia, ponieważ platforma rezerwuje tę pojemność i gwarantuje powodzenie operacji.
Można [elastycznie zmienić przepływność,](manage-scale-cassandra.md) aby korzystać z sezonowości aplikacji i zaoszczędzić koszty.

Koncepcja przepływności jest wyjaśniona w [jednostek żądań w usłudze Azure Cosmos DB](request-units.md) artykułu. Przepływność dla tabeli jest równomiernie rozłożona na podstawowe partycje fizyczne.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co to jest domyślna tabela RU/s podczas tworzenia za pośrednictwem CQL? Co zrobić, jeśli muszę to zmienić?

Usługa Azure Cosmos DB używa jednostek żądań na sekundę (RU/s) jako waluty do dostarczania przepływności. Tabele utworzone za pośrednictwem CQL mają 400 RU. Można zmienić RU z portalu.

z o.o.

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Co się stanie, gdy przepustowość zostanie wykorzystana?

Usługa Azure Cosmos DB zapewnia gwarancje wydajności i opóźnienia, z górnymi granicami w operacji. Ta gwarancja jest możliwe, gdy aparat może wymusić zarządzanie w operacjach dzierżawy. Jest to możliwe w oparciu o ustawienie przepływności, co zapewnia uzyskanie gwarantowanej przepływności i opóźnienia, ponieważ platforma rezerwuje tę pojemność i gwarantuje powodzenie operacji.
Po przełączeniu tej pojemności pojawi się przeciążony komunikat o błędzie wskazujący, że pojemność została wykorzystana.
0x1001 Przeciążone: żądanie nie może być przetworzone, ponieważ "Request Rate is large". W tym momencie ważne jest, aby zobaczyć, jakie operacje i ich objętość powoduje ten problem. Można zorientować się o zużytej pojemności będzie ponad aprowizowanych pojemności z metryki w portalu. Następnie należy upewnić się, że pojemność jest zużywana prawie równo we wszystkich partycjach źródłowych. Jeśli widzisz większość przepływności jest zużywana przez jedną partycję, masz pochylenie obciążenia.

Dostępne są metryki, które pokazują, jak przepływność jest używana przez godziny, dni i na siedem dni, między partycjami lub w ramach łącznie. Aby uzyskać więcej informacji, zobacz [Monitorowanie i debugowanie z metrykami w usłudze Azure Cosmos DB](use-metrics.md).

Dzienniki diagnostyczne są wyjaśnione w artykule [rejestrowania diagnostycznego usługi Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Czy klucz podstawowy jest mapą do koncepcji klucza partycji usługi Azure Cosmos DB?

Tak, klucz partycji jest używany do umieszczenia jednostki w odpowiedniej lokalizacji. W usłudze Azure Cosmos DB służy do znajdowania prawej partycji logicznej, która jest przechowywana na partycji fizycznej. Koncepcja partycjonowania jest dobrze wyjaśnione w [partycji i skali w usłudze Azure Cosmos DB](partition-data.md) artykułu. Najważniejsze zabrać tutaj jest to, że partycja logiczna nie powinna przejść przez limit 10 GB dzisiaj.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co się stanie, gdy otrzymuję pełne powiadomienie o przydziałach wskazujące, że partycja jest pełna?

Usługa Azure Cosmos DB to system oparty na umowy SLA, który zapewnia nieograniczoną skalę, z gwarancjami opóźnienia, przepływności, dostępności i spójności. Ta nieograniczona pamięć masowa jest oparta na skali poziomej danych przy użyciu partycjonowania jako koncepcji klucza. Koncepcja partycjonowania jest dobrze wyjaśnione w [partycji i skali w usłudze Azure Cosmos DB](partition-data.md) artykułu.

Limit 10 GB liczby jednostek lub elementów na partycję logiczną, której należy przestrzegać. Aby upewnić się, że aplikacja skaluje się dobrze, zaleca *się,* aby nie tworzyć partycji gorąca, przechowując wszystkie informacje w jednej partycji i kwerendy. Ten błąd może pojawić się tylko wtedy, gdy dane są wypaczone: oznacza to,&nbsp;że masz dużo danych dla jednego klucza partycji (więcej niż 10 GB). Dystrybucję danych można znaleźć za pomocą portalu magazynu. Sposobem naprawienia tego błędu jest ponownetworzenie tabeli i wybranie szczegółowego klucza podstawowego (klucz partycji), który umożliwia lepszą dystrybucję danych.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Czy można używać cassandra API jako magazynu wartości klucza z milionami lub miliardami pojedynczych kluczy partycji?

Usługa Azure Cosmos DB może przechowywać nieograniczoną ilość danych, skalując w poziomie magazynu. Jest to niezależne od przepływności. Tak, zawsze możesz po prostu użyć interfejsu API Cassandra do przechowywania i pobierania klucza/wartości, określając prawy klucz podstawowy/partycja. Te poszczególne klucze otrzymują własną partycję logiczną i siedzą na partycji fizycznej bez problemów.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Czy można utworzyć więcej niż jedną tabelę za pomocą interfejsu API Apache Cassandra usługi Azure Cosmos DB?

Tak, możliwe jest utworzenie więcej niż jednej tabeli za pomocą interfejsu API Apache Cassandra. Każda z tych tabel jest traktowana jako jednostka przepływności i magazynu.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Czy możliwe jest utworzenie więcej niż jednej tabeli kolejno?

Usługa Azure Cosmos DB to system zarządzany przez zasoby dla działań płaszczyzny danych i kontroli. Kontenery, takie jak kolekcje, tabele są jednostkami środowiska wykonawczego, które są aprowizowane dla danej przepustowości. Tworzenie tych kontenerów w krótkim odstępie czasu nie jest oczekiwaną aktywnością i ograniczane. Jeśli masz testy, które natychmiast upuszczają/tworzą tabele, spróbuj je rozmieszczenia.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Jaka jest maksymalna liczba tabel, które można utworzyć?

Nie ma fizycznego limitu liczby tabel, wyślij [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) wiadomość e-mail, jeśli masz dużą liczbę tabel (gdzie całkowity stały rozmiar przekracza 10 TB danych), które muszą zostać utworzone ze zwykłych 10s lub 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaki jest maksymalny numer przestrzeni kluczy, którą możemy utworzyć?

Nie ma fizycznego limitu liczby obszarów kluczowych, ponieważ są one kontenerami metadanych, wyślij wiadomość e-mail, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) jeśli z jakiegoś powodu masz dużą liczbę obszarów kluczowych.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Czy możliwe jest wprowadzenie dużej ilości danych po rozpoczęciu pracy z normalnej tabeli?

Tak, przy założeniu jednolicie rozproszonych partycji, pojemność magazynu jest automatycznie zarządzana i zwiększa się w miarę wypychania większej ilości danych. Dzięki czemu można śmiało importować tyle danych, ile potrzebujesz bez zarządzania i inicjowania obsługi administracyjnej węzłów i więcej. Jednak jeśli przewidujesz wiele natychmiastowego wzrostu danych, bardziej sensowne jest bezpośrednie [zapewnienie przewidywanej przepustowości,](set-throughput.md) a nie rozpoczynanie niższe i zwiększanie jej natychmiast.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Czy można podać ustawienia pliku yaml, aby skonfigurować apache Casssandra API zachowania usługi Azure Cosmos DB?

Apache Cassandra API usługi Azure Cosmos DB to usługa platformy. Zapewnia zgodność poziomu protokołu do wykonywania operacji. Ukrywa złożoność zarządzania, monitorowania i konfiguracji. Jako deweloper/użytkownik nie musisz martwić się o dostępność, nagrobki, pamięć podręczną kluczy, pamięć podręczną wierszy, filtr bloom i wiele innych ustawień. Interfejs API Apache Cassandra usługi Azure Cosmos DB koncentruje się na zapewnianiu wydajności odczytu i zapisu, której potrzebujesz, bez konieczności konfigurowania i zarządzania.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Czy apache Cassandra API dla usługi Azure Cosmos DB obsługuje polecenia dodawania węzłów/klaster stanu/węzła?

Apache Cassandra API to usługa platformy, która sprawia, że planowanie pojemności, odpowiadając na wymagania dotyczące elastyczności przepustowości & magazynu, jest bardzo proste. Z usługi Azure Cosmos DB można aprowizować przepływność, czego potrzebujesz. Następnie można skalować go w górę iw dół dowolną liczbę razy w ciągu dnia, nie martwiąc się o dodawanie / usuwanie węzłów lub zarządzanie nimi. Oznacza to, że nie trzeba używać węzła, narzędzie do zarządzania klastrem też.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co się dzieje w odniesieniu do różnych ustawień konfiguracji do tworzenia przestrzeni kluczy, takich jak proste / sieci?

Usługa Azure Cosmos DB zapewnia dystrybucję globalną po wyjęciu z pudełka ze względu na dostępność i małe opóźnienia. Nie trzeba konfigurować replik ani innych rzeczy. Wszystkie zapisy są zawsze trwale kworum popełnione w dowolnym regionie, w którym piszesz, zapewniając gwarancje wydajności.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co się dzieje w odniesieniu do różnych ustawień metadanych tabeli, takich jak filtr kwitnienia, buforowanie, zmiana naprawy odczytu, gc_grace, memtable_flush_period kompresji i inne?

Usługa Azure Cosmos DB zapewnia wydajność odczytów/zapisów i przepływności bez konieczności dotykania żadnych ustawień konfiguracji i przypadkowego manipulowania nimi.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Czy czas wygaśnięcia (TTL) jest obsługiwany dla tabel Cassandra?

Tak, TTL jest obsługiwany.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Czy możliwe jest wcześniejsze monitorowanie stanu węzła, stanu repliki, gc i parametrów systemu operacyjnego za pomocą różnych narzędzi? Co należy teraz monitorować?

Usługa Azure Cosmos DB to usługa platformy, która pomaga zwiększyć produktywność i nie martwić się o zarządzanie i monitorowanie infrastruktury. Wystarczy zadbać o przepływność, która jest dostępna w metrykach portalu, aby dowiedzieć się, czy otrzymujesz ograniczanie i zwiększać lub zmniejszać tę przepływność.
[Monitoruj ujedne pos.](monitor-accounts.md)
Użyj [metryki](use-metrics.md) Użyj [dzienników diagnostycznych](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Które zestawy SDK klienta mogą współpracować z interfejsem API Apache Cassandra usługi Azure Cosmos DB?

Sterowniki klienta apache Cassandra SDK, które używają CQLv3 były używane do programów klienckich. Jeśli masz inne sterowniki, których używasz lub masz problemy, wyślij pocztę do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Czy klucz partycji złożonej jest obsługiwany?

Tak, do utworzenia klucza partycji złożonej można użyć zwykłej składni.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Czy mogę używać sstableloader do ładowania danych?

Nie, sstableloader nie jest obsługiwany.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Czy lokalny klaster Apache Cassandra można sparować z interfejsem API Cassandra usługi Azure Cosmos DB?

Obecnie usługa Azure Cosmos DB ma zoptymalizowane środowisko dla środowiska w chmurze bez nakładu pracy. Jeśli potrzebujesz parowania, wyślij [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) pocztę z opisem scenariusza. Pracujemy nad oferowaniem, aby pomóc w parowaniu lokalnego/innego klastra Cassandra w chmurze z api Cassandra firmy Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Czy api Cassandra zapewnia pełne kopie zapasowe?

Usługa Azure Cosmos DB udostępnia dwie bezpłatne pełne kopie zapasowe wykonane w odstępie czterech godzin w dzisiejszych interfejsach API. Dzięki temu nie trzeba konfigurowania harmonogramu tworzenia kopii zapasowych i innych rzeczy.
Jeśli chcesz zmodyfikować przechowywanie i częstotliwość, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) wyślij wiadomość e-mail do lub zgłosić sprawę pomocy technicznej. Informacje o możliwości tworzenia kopii zapasowych znajdują się w [artykule Automatyczna kopia zapasowa i przywracanie online za pomocą usługi Azure Cosmos DB.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak konto interfejsu API Cassandra obsługuje przebłaganie w przypadku awarii regionu?

Interfejs API Cassandra usługi Azure Cosmos DB zapożycza się z globalnie rozproszonej platformy usługi Azure Cosmos DB. Aby upewnić się, że aplikacja może tolerować przestoje centrum danych, włącz co najmniej jeden region dla konta w portalu usługi Azure Cosmos DB [Developing z wieloregionowymi kontami usługi Azure Cosmos DB.](high-availability.md) Priorytet regionu można ustawić za pomocą portalu [Programowanie z wieloregionowymi kontami usługi Azure Cosmos DB](high-availability.md).

Można dodać dowolną liczbę regionów dla konta i kontrolować, gdzie można awaryjnie, podając priorytet pracy awaryjnej. Aby korzystać z bazy danych, należy podać aplikację tam też. Gdy to zrobisz, twoi klienci nie doświadczą przestojów.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Czy apache Cassandra API indeksuje wszystkie atrybuty jednostki domyślnie?

Nie. Api Cassandra obsługuje [indeksy pomocnicze,](cassandra-secondary-index.md)które zachowują się w bardzo podobny sposób do Apache Cassandra. Domyślnie nie indeksuje każdego atrybutu.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Czy mogę używać nowego SDK interfejsu API Cassandra lokalnie z emulatorem?

Tak, jest to obsługiwane. Szczegółowe informacje na temat włączania tej opcji można znaleźć [tutaj](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Jak mogę migrować dane z klastrów Apache Cassandra do usługi Cosmos DB?

O opcjach migracji można przeczytać [tutaj](cassandra-import-data.md).


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkcja x regularnego api Cassandra nie działa tak jak dzisiaj, gdzie można przekazać opinie?

Przekazywanie opinii za pośrednictwem [opinii głosowej użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [elastycznego skalowania konta interfejsu API usługi Azure Cosmos DB Cassandra](manage-scale-cassandra.md).
