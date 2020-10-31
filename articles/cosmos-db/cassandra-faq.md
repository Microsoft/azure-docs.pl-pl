---
title: Często zadawane pytania dotyczące interfejs API Cassandra dla Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejs API Cassandra Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: b467e191a8f009b85abb4866821dd64fc87ce657
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092385"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące interfejs API Cassandra w programie Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

W tym artykule opisano różnice między funkcjami programu Apache Cassandra i interfejs API Cassandra w programie Azure Cosmos DB. Zawiera także odpowiedzi na często zadawane pytania dotyczące interfejs API Cassandra w Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Kluczowe różnice między programem Apache Cassandra i interfejs API Cassandra

- W przypadku systemu Apache Cassandra zaleca się limit 100 MB klucza partycji. Interfejs API Cassandra dla Azure Cosmos DB umożliwia do 20 GB na partycję.
- Apache Cassandra umożliwia wyłączenie trwałych zatwierdzeń. Możesz pominąć zapisywanie w dzienniku zatwierdzeń i przejść bezpośrednio do memtables. Może to prowadzić do utraty danych, jeśli węzeł ulegnie awarii przed opróżnieniem memtables do SSTables na dysku. Azure Cosmos DB zawsze zapewnia trwałe zatwierdzenia, aby zapobiec utracie danych.
- W przypadku, gdy obciążenie obejmuje wiele elementów zastępczych lub usunięć, w programie Apache Cassandra może zostać wyświetlona mniejsza wydajność. Powodem jest to, że obciążenie odczytywane wymaga pominięcia, aby pobrać najnowsze dane. Interfejs API Cassandra nie będzie widział wydajności operacji odczytu, gdy obciążenie ma wiele elementów zastępczych lub usunięć.
- W przypadku obciążeń o dużej wymianie należy uruchomić kompaktowanie w celu scalenia SSTables na dysku. (Scalanie jest zbędne, ponieważ operacje zapisu Apache Cassandra są tylko do dołączenia. Wiele aktualizacji jest przechowywanych jako pojedyncze wpisy SSTable, które muszą być okresowo scalane). Ta sytuacja może również prowadzić do obniżenia wydajności odczytu podczas kompaktowania. Ten wpływ na wydajność nie występuje w interfejs API Cassandra, ponieważ interfejs API nie implementuje kompaktowania.
- Ustawienie współczynnika replikacji 1 jest możliwe z Apache Cassandra. Jednak prowadzi ona do niskiej dostępności, jeśli jedyny węzeł z danymi ulegnie awarii. Nie jest to problem z interfejs API Cassandra Azure Cosmos DB, ponieważ jest zawsze współczynnik replikacji wynoszący 4 (kworum 3).
- Dodawanie lub usuwanie węzłów w programie Apache Cassandra wymaga ręcznej interwencji oraz wysokiego użycia procesora CPU w nowym węźle, gdy istniejące węzły przenieśą niektóre z ich zakresów tokenów do nowego węzła. Ta sytuacja jest taka sama w przypadku likwidowania istniejącego węzła. Jednak interfejs API Cassandra skaluje się bez żadnych problemów obserwowanych w usłudze lub aplikacji.
- Nie ma potrzeby ustawiania **num_tokens** w każdym węźle klastra jako na platformie Apache Cassandra. Azure Cosmos DB w pełni zarządzać węzłami i zakresami tokenów.
- Interfejs API Cassandra jest w pełni zarządzana. Nie potrzebujesz poleceń **nodetool** , takich jak naprawa i likwidowanie, które są używane w Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Inne często zadawane pytania

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Jaka wersja protokołu obsługuje interfejs API Cassandra?

Interfejs API Cassandra dla Azure Cosmos DB obsługuje CQL w wersji 3. x. Jego zgodność CQL jest oparta na publicznym [repozytorium Apache Cassandra GitHub](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Jeśli masz opinię na temat obsługi innych protokołów, poinformuj nas za pośrednictwem [opinii o użytkowniku](https://feedback.azure.com/forums/263030-azure-cosmos-db) lub Wyślij wiadomość e-mail na adres [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Dlaczego należy wybrać przepływność dla tabeli a wymagania?

Azure Cosmos DB ustawia domyślną przepływność dla kontenera w zależności od tego, skąd tworzysz tabelę: Azure Portal lub CQL.

Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Te gwarancje są możliwe, gdy silnik może wymusić Zarządzanie operacjami dzierżawcy. Ustawienie przepływności zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne wykonanie operacji.
Możesz [elastycznie zmieniać przepływność](manage-scale-cassandra.md) , aby korzystać z sezonowości aplikacji i oszczędzać koszty.

Koncepcji przepływności wyjaśniono w [jednostkach żądań w Azure Cosmos DB](request-units.md) artykule. Przepływność tabeli jest równomiernie rozłożona między podstawowymi partycjami fizycznymi.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Jaka jest przepływność tabeli utworzonej za pomocą CQL?

Azure Cosmos DB używa jednostek żądań na sekundę (RU/s) jako waluty do zapewnienia przepływności. Tabele utworzone za pomocą CQL mają domyślnie 400 RU. Można zmienić RU z Azure Portal.

CQL

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

### <a name="what-happens-when-throughput-is-used-up"></a>Co się stanie, gdy przepływność jest używana?

Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Te gwarancje są możliwe, gdy silnik może wymusić Zarządzanie operacjami dzierżawcy. Ustawienie przepływności zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne wykonanie operacji.

Gdy przejdziesz do tej pojemności, zostanie wyświetlony następujący komunikat o błędzie z informacją o tym, że pojemność została użyta:

**0x1001 przeciążone: nie można przetworzyć żądania, ponieważ "częstotliwość żądania jest duża"** 

Istotne jest, aby zobaczyć, jakie operacje (i ich woluminy) powodują ten problem. Możesz uzyskać pomysł dotyczący zużytej pojemności przechodzącej przez zainicjowaną pojemność przy użyciu metryk na Azure Portal. Następnie należy upewnić się, że pojemność jest używana niemal równomiernie we wszystkich partycji podstawowych. Jeśli widzisz, że jedna partycja zużywa większość przepływności, nastąpiło pochylenie.

Dostępne są metryki pokazujące, jak przepływność jest używana w godzinach, w dniach i na siedem dni, między partycjami lub agregacją. Aby uzyskać więcej informacji, zobacz [monitorowanie i debugowanie za pomocą metryk w Azure Cosmos DB](use-metrics.md).

Dzienniki diagnostyczne zostały omówione w artykule [rejestrowanie diagnostyczne Azure Cosmos DB](./monitor-cosmos-db.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Czy klucz podstawowy jest mapowany na klucz partycji koncepcji Azure Cosmos DB?

Tak, klucz partycji jest używany do umieszczania jednostki w prawej lokalizacji. W Azure Cosmos DB służy do znajdowania odpowiedniej partycji logicznej przechowywanej na partycji fizycznej. Koncepcja partycjonowania jest dobrze omówiona w [partycji i skalowaniu w Azure Cosmos DB](partitioning-overview.md) artykule. Istotną wnioskiemą jest to, że partycja logiczna nie powinna przekroczyć limitu 20 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Co się stanie, gdy otrzymam powiadomienie o zapełnieniu partycji?

Azure Cosmos DB to system oparty na umowie dotyczącej poziomu usług (SLA). Zapewnia nieograniczoną skalę i gwarantuje opóźnienia, przepływność, dostępność i spójność. Ten nieograniczony magazyn jest oparty na skalowalnych w poziomie danych, przy użyciu partycjonowania w ramach kluczowych koncepcji. Koncepcja partycjonowania jest dobrze omówiona w [partycji i skalowaniu w Azure Cosmos DB](partitioning-overview.md) artykule.

Należy przestrzegać limitu 20 GB dla liczby jednostek lub elementów na partycję logiczną. Aby upewnić się, że aplikacja jest odpowiednio skalowana, zalecamy, aby *nie* tworzyć partycji gorąca, przechowując wszystkie informacje w jednej partycji i wykonując zapytania. Ten błąd może występować tylko wtedy, gdy dane są skośne: oznacza to, że masz dużo danych dla jednego klucza partycji (więcej niż 20 GB). Dystrybucję danych można znaleźć za pomocą portalu magazynu. Aby naprawić ten błąd, należy ponownie utworzyć tabelę i wybrać szczegółowy podstawowy (klucz partycji), co umożliwi lepsze dystrybuowanie danych.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Czy można użyć interfejs API Cassandra jako magazynu wartości klucza z milionami lub miliardami kluczy partycji?

Azure Cosmos DB mogą przechowywać nieograniczone dane przez skalowanie magazynu. Ten magazyn jest niezależny od przepływności. Tak. można zawsze używać interfejs API Cassandra, aby przechowywać i pobierać klucze i wartości, określając klucz z prawej strony. Te pojedyncze klucze uzyskują własną partycję logiczną i korzystającegoą partycję fizyczną bez problemów.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Czy mogę utworzyć więcej niż jedną tabelę z interfejs API Cassandra?

Tak, można utworzyć więcej niż jedną tabelę z interfejs API Cassandra. Każda z tych tabel jest traktowana jako jednostka do przepływności i magazynu.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Czy mogę utworzyć więcej niż jedną tabelę w kolejnych sukcesach?

Azure Cosmos DB jest systemem opartym na zasobach zarówno dla działania płaszczyzny danych, jak i kontroli. Kontenery, takie jak kolekcje i tabele, to jednostki środowiska uruchomieniowego, które są obsługiwane dla danej wydajności przepływności. Tworzenie tych kontenerów w szybkim pomyślniu nie jest oczekiwanym działaniem i może być ograniczone. Jeśli masz testy, które natychmiast porzucają lub tworzą tabele, spróbuj je przetworzyć.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Jaka jest maksymalna liczba tabel, które mogę utworzyć?

Nie ma fizycznego limitu liczby tabel. Jeśli masz dużą liczbę tabel (o ile łączny rozmiar przekracza 10 TB danych), które muszą zostać utworzone, a nie zwykłe dziesiątki lub setki, Wyślij wiadomość e-mail na adres [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Jaka jest maksymalna liczba miejsc, które mogę utworzyć?

Nie ma fizycznego limitu liczby miejsc, ponieważ są to kontenery metadanych. Jeśli masz dużą liczbę miejsc, Wyślij wiadomość e-mail na adres [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Czy mogę wprowadzić wiele danych po uruchomieniu z normalnej tabeli?

Tak. Przyjmując równomiernie rozproszone partycje, pojemność magazynu jest automatycznie zarządzana i zwiększa się w miarę wypychania większej ilości danych. Pozwala to na zaimportowanie dowolnej ilości danych bez konieczności zarządzania węzłami i ich aprowizacji. Ale Jeśli przewidujesz dużo bezpośredniego wzrostu danych, lepiej jest zapewnić bezpośrednią obsługę przewidzianych [przepływów](set-throughput.md) pracy zamiast obniżania ich od razu.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Czy można użyć ustawień pliku YAML, aby skonfigurować zachowanie interfejsu API?

Interfejs API Cassandra dla Azure Cosmos DB zapewnia zgodność na poziomie protokołu na potrzeby wykonywania operacji. Upraszcza to zarządzanie, monitorowanie i konfigurację. Jako deweloper/użytkownik nie musisz martwić się o dostępność, relikty, pamięć podręczną kluczy, pamięć podręczną wierszy, filtr z zalotem i wiele innych ustawień. Interfejs API Cassandra koncentruje się na zapewnianiu wydajności odczytu i zapisu bez konieczności konfigurowania i zarządzania.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Czy interfejs API Cassandra będzie obsługiwać Dodawanie węzła, stan klastra i polecenia stanu węzła?

Interfejs API Cassandra upraszcza planowanie pojemności i reagowanie na wymagania elastycznej przepływności i magazynu. W przypadku Azure Cosmos DB należy zapewnić wymaganą przepływność. Następnie można skalować w górę i w dół dowolną liczbę razy dziennie, bez obaw o dodawanie, usuwanie i zarządzanie węzłami. Nie musisz używać narzędzi do zarządzania węzłami i klastrami.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Co się dzieje z różnymi ustawieniami konfiguracji dla tworzenia miejsc kluczy, takich jak Simple/Network?

Azure Cosmos DB zapewnia globalną dystrybucję z usługi Box do dostępności i przyczyny o małym opóźnieniu. Nie musisz konfigurować replik ani innych elementów. Zapisy są zawsze trwale kworum zatwierdzone w dowolnym regionie, w którym piszesz, a jednocześnie zapewniają gwarancje wydajności.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Co się dzieje z różnymi ustawieniami metadanych tabeli?

Azure Cosmos DB zapewnia gwarancje wydajności operacji odczytu, zapisu i przepływności. Nie musisz martwić się o dotknięcie jakichkolwiek ustawień konfiguracji i przypadkowe manipulowanie nimi. Te ustawienia obejmują filtr wypełniania, buforowanie, szansę naprawy odczytu, gc_grace i kompresję memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Czy w tabelach Cassandra jest obsługiwana godzina wygaśnięcia?

Tak, wartość czasu wygaśnięcia jest obsługiwana.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Jak mogę monitorować infrastrukturę wraz z przepływem?

Azure Cosmos DB to usługa platformy, która pozwala zwiększyć produktywność i nie martwić się o zarządzanie i monitorowanie infrastruktury. Na przykład nie trzeba monitorować stanu węzła, stanu repliki, GC i parametrów systemu operacyjnego wcześniej przy użyciu różnych narzędzi. Wystarczy zadbać o przepływność, która jest dostępna w metrykach portalu, aby zobaczyć, czy masz ograniczenie przepustowości, a następnie zwiększyć lub zmniejszyć tę przepływność. Oto co możesz zrobić:

- Monitorowanie [umowy SLA](./monitor-cosmos-db.md)
- Korzystanie z [metryk](use-metrics.md)
- Korzystanie z [dzienników diagnostycznych](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Które zestawy SDK klienta mogą współpracowały z interfejs API Cassandra?

Sterowniki klienta zestawu Apache Cassandra SDK, które używają CQLv3, zostały użyte dla programów klienckich. Jeśli masz inne sterowniki, które są używane, lub jeśli masz problemy, Wyślij wiadomość e-mail do programu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Czy klucze partycji złożonej są obsługiwane?

Tak, możesz użyć zwykłej składni, aby utworzyć złożone klucze partycji.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Czy mogę używać sstableloader do ładowania danych?

Nie, sstableloader nie jest obsługiwany.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Czy można sparować lokalny Klaster Apache Cassandra z interfejs API Cassandra?

W tej chwili Azure Cosmos DB ma zoptymalizowane środowisko dla środowiska chmury bez konieczności wykonywania dodatkowych czynności. Jeśli wymagasz parowania, Wyślij wiadomość e-mail do [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) opisu Twojego scenariusza. Pracujemy nad ofertą, aby pomóc w sparowaniu klastra lokalnego lub Cassandra w chmurze z interfejs API Cassandra Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Czy interfejs API Cassandra zapewniać pełne kopie zapasowe?

Azure Cosmos DB oferuje dwa bezpłatne pełne kopie zapasowe wykonane w czterech godzinach dla wszystkich interfejsów API. Nie trzeba więc konfigurować harmonogramu tworzenia kopii zapasowych. 

Jeśli chcesz zmodyfikować przechowywanie i częstotliwość, Wyślij wiadomość e-mail na adres [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) pomocy technicznej lub Zgłoś ją. Informacje o możliwościach tworzenia kopii zapasowych znajdują się w artykule [Automatyczna kopia zapasowa online i przywracanie z Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak konto interfejs API Cassandra obsługuje tryb failover, jeśli region ulegnie awarii?

Interfejs API Cassandra jest pożyczana z platformy rozproszonej globalnie Azure Cosmos DB. Aby zapewnić, że aplikacja może tolerować przestoje centrum danych, należy włączyć co najmniej jeden region dla konta w Azure Portal. Aby uzyskać więcej informacji, zobacz [wysoka dostępność w Azure Cosmos DB](high-availability.md).

Możesz dodać dowolną liczbę regionów dla konta i kontrolować miejsce, w którym może być przełączane do trybu failover, podając priorytet pracy awaryjnej. Aby można było korzystać z bazy danych, należy podać również aplikację. W takim przypadku klienci nie będą napotykać przestojów.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Czy interfejs API Cassandra indeksować wszystkie atrybuty jednostki domyślnie?

Nie. Interfejs API Cassandra obsługuje [indeksy pomocnicze](cassandra-secondary-index.md), które działają w podobny sposób do Apache Cassandra. Interfejs API domyślnie nie indeksuje każdego atrybutu.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Czy mogę użyć nowego zestawu interfejs API Cassandra SDK lokalnie z emulatorem?

Tak, takie rozwiązanie jest obsługiwane. Aby uzyskać szczegółowe informacje na temat tego, jak to zrobić, [Skorzystaj z emulatora usługi Azure Cosmos na potrzeby lokalnego projektowania i testowania](local-emulator.md#cassandra-api) .


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Jak przeprowadzić migrację danych z klastrów Apache Cassandra do Azure Cosmos DB?

Informacje o opcjach migracji można znaleźć w samouczku [Migrowanie danych do konta interfejs API Cassandra w Azure Cosmos DB](cassandra-import-data.md) .


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Gdzie mogę przekazać opinię na temat funkcji interfejs API Cassandra?

Prześlij opinię za pośrednictwem [opinii użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [elastycznego skalowania Azure Cosmos DB interfejs API Cassandra konta](manage-scale-cassandra.md).