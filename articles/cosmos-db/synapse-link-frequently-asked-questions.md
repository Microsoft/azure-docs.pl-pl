---
title: Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Azure Cosmos DB linków Synapse w obszarach takich jak rozliczenia, magazyn analityczny, zabezpieczenia, czas wygaśnięcia w magazynie analitycznym.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 9eb1f9162f0546e08f59391af1042becad25cf3b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803997"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB

Link Synapse platformy Azure dla Azure Cosmos DB umożliwia ścisłą integrację między usługami Azure Cosmos DB i Azure Synapse Analytics. Umożliwia klientom Uruchamianie analizy niemal w czasie rzeczywistym na podstawie danych operacyjnych z pełną izolacją wydajności z obciążeń transakcyjnych i bez potoku ETL. W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Synapse Link dla usługi Azure Cosmos DB.

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Czy link usługi Azure Synapse jest obsługiwany przez wszystkie Azure Cosmos DB interfejsy API?

W publicznej wersji zapoznawczej usługa Azure Synapse link jest obsługiwana w przypadku interfejsu API SQL (rdzeń) Azure Cosmos DB i interfejsu API Azure Cosmos DB dla MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Czy link usługi Azure Synapse jest obsługiwany dla wieloregionowych kont Azure Cosmos DB?

Tak, w przypadku kont usługi Azure Cosmos na wiele regionów dane przechowywane w magazynie analitycznym są również dystrybuowane globalnie. Niezależnie czy chodzi o pojedynczy region zapisu, czy wiele regionów zapisu, analityczne zapytania wykonywane z usługi Azure Synapse Analytics mogą być obsługiwane z najbliższego lokalnego regionu.

Podczas planowania konfigurowania wieloregionowego konta Azure Cosmos DB z obsługą magazynu analitycznego zaleca się, aby wszystkie wymagane regiony zostały dodane podczas tworzenia konta.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Czy mogę włączyć link usługi Azure Synapse tylko dla pewnego regionu, a nie wszystkich regionów w konfiguracji konta wieloregionowego?

W wersji zapoznawczej, gdy łącze Azure Synapse jest włączone dla konta wieloregionowego, magazyn analityczny jest tworzony we wszystkich regionach. Dane podstawowe są zoptymalizowane pod kątem przepływności i spójności transakcyjnej w magazynie transakcyjnym.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Czy funkcja tworzenia kopii zapasowych i przywracania jest obsługiwana dla kont usługi Azure Synapse link?

W wersji zapoznawczej dla kont bazy danych z włączonym linkiem usługi Azure Synapse nie jest obsługiwane wykonywanie kopii zapasowych i przywracanie kontenerów. W przypadku obciążeń produkcyjnych, które wymagają funkcji tworzenia kopii zapasowych i przywracania, zaleca się, aby nie włączać linku Synapse na tych kontach baz danych. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Czy mogę wyłączyć funkcję linku usługi Azure Synapse dla mojego konta Azure Cosmos DB?

Obecnie po włączeniu funkcji usługi Synapse Link na poziomie konta nie można jej wyłączyć. Należy pamiętać, że włączenie funkcji usługi Synapse Link na poziomie konta nie pociąga żadnych implikacji rozliczeniowych w przypadku braku kontenerów z włączonym magazynem analitycznym. 

Jeśli musisz wyłączyć tę możliwość, masz dwie opcje. Pierwszym z nich jest usunięcie i ponowne utworzenie nowego konta Azure Cosmos DB, w razie potrzeby, Migrowanie danych. Druga opcja to otwarcie biletu pomocy technicznej, aby uzyskać pomoc na temat migracji danych na inne konto.

## <a name="azure-cosmos-db-analytical-store"></a>Magazyn analityczny Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Czy mogę włączyć magazyn analityczny w istniejących kontenerach?

Obecnie magazyn analityczny można włączyć tylko dla nowych kontenerów (zarówno w nowych, jak i istniejących kont).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Czy można wyłączyć magazyn analityczny w kontenerach Azure Cosmos DB po włączeniu go podczas tworzenia kontenera?

Obecnie nie można wyłączyć magazynu analitycznego w kontenerze usługi Azure Cosmos DB po jego włączeniu podczas tworzenia kontenera.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Czy magazyn analityczny jest obsługiwany w przypadku kontenerów Azure Cosmos DB o przepływności z obsługą automatycznego skalowania?

Tak. magazyn analityczny można włączyć w kontenerach z elastyczną przepustowością skalowania automatycznego.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Czy ma to wpływ na Azure Cosmos DB transakcyjnego magazynu jednostek ru?

Azure Cosmos DB gwarantuje izolację wydajności między obciążeniami transakcyjnymi i analitycznymi. Włączenie magazynu analitycznego w kontenerze nie wpłynie na zainicjowanie obsługi jednostek RU w Azure Cosmos DB magazynie transakcyjnym. Transakcje (odczyt & zapis) i koszty magazynu dla magazynu analitycznego zostaną rozliczone osobno. Aby uzyskać więcej informacji, zobacz [Cennik dla magazynu analitycznego Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Czy operacje usuwania i aktualizacji w magazynie transakcyjnym są odzwierciedlone w magazynie analitycznym?

Tak, usunięcie i aktualizacja danych w magazynie transakcyjnym zostaną odzwierciedlone w magazynie analitycznym. Można skonfigurować czas wygaśnięcia (TTL) w kontenerze w celu uwzględnienia danych historycznych, aby Magazyn analityczny przechowywał wszystkie wersje elementów, które spełniają kryteria analityczne czasu wygaśnięcia. Aby uzyskać więcej informacji, zobacz [Omówienie czasu wygaśnięcia (TTL)](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Czy mogę połączyć się z magazynem analitycznym z innych aparatów analizy niż usługa Azure Synapse Analytics?

Można uzyskać dostęp magazynu analitycznego i uruchamiać zapytania względem magazynu analitycznego, jedynie korzystając z różnych środowisk uruchomieniowych udostępnianych przez usługę Azure Synapse Analytics. Magazyn analityczny można odpytywać i analizować przy użyciu następujących narzędzi:

* Synapse Spark z pełną obsługą Scala, Python, SparkSQL i C#. Usługa Synapse Spark stanowi podstawę w przypadku scenariuszy z zakresu inżynierii danych i nauki
* Bezserwerowa usługa SQL z językiem T-SQL i obsługą znanych narzędzi analizy biznesowej (na przykład Power BI Premium itd.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Czy można nawiązać połączenie z magazynem analitycznym z Synapse SQL?

W tej chwili nie można uzyskać dostępu do magazynu analitycznego z Synapse SQL.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Czy mogę zapisać z powrotem wyniki agregacji zapytania z Synapse z powrotem do magazynu analitycznego?

Magazyn analityczny jest magazynem tylko do odczytu w kontenerze Azure Cosmos DB. Dlatego nie można bezpośrednio zapisywać wyników agregacji w magazynie analitycznym, ale mogą zapisywać je w magazynie transakcyjnym Azure Cosmos DB innego kontenera, który można później wykorzystać jako obsługującą warstwę.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Czy replikacja AutoSync z magazynu transakcyjnego do magazynu analitycznego odbywa się asynchronicznie czy synchronicznie i jakie są opóźnienia?

Opóźnienie autosynchronizacji jest zwykle w ciągu 2 minut. W przypadku udostępnionej bazy danych przepływności o dużej liczbie kontenerów czas oczekiwania na synchronizację poszczególnych kontenerów może być większy i potrwać do 5 minut. Chcielibyśmy dowiedzieć się więcej o tym, jak to opóźnienie pasuje do Twoich scenariuszy. W tym celu skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Czy istnieją jakieś scenariusze, w których elementy z magazynu transakcyjnego nie są automatycznie propagowane do magazynu analitycznego?

Jeśli określone elementy w kontenerze naruszają [dobrze zdefiniowany schemat dla analiz](analytical-store-introduction.md#analytical-schema), nie zostaną uwzględnione w magazynie analitycznym. Jeśli masz scenariusze blokowane przez dobrze zdefiniowany schemat analityczny, Wyślij wiadomość e-mail do [zespołu Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) , aby uzyskać pomoc.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Czy można podzielić dane w magazynie analitycznym inaczej niż w sklepie transakcyjnym?

Dane w magazynie analitycznym są partycjonowane w oparciu o partycjonowanie poziome fragmentów w magazynie transakcyjnym. Obecnie nie można wybrać innej strategii partycjonowania dla magazynu analitycznego.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Czy mogę dostosować lub zastąpić sposób przekształcania danych transakcyjnych na format kolumnowy w magazynie analitycznym?

Obecnie nie można przekształcić elementów danych, gdy są one automatycznie propagowane ze sklepu transakcyjnego do magazynu analitycznego. Jeśli masz scenariusze blokowane przez to ograniczenie, Wyślij wiadomość e-mail do [zespołu Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Analityczny czas wygaśnięcia (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Czy wartość czasu wygaśnięcia dla danych analitycznych jest obsługiwana na poziomie kontenera i elementu?

W tej chwili czas TTl dla danych analitycznych można skonfigurować tylko na poziomie kontenera i nie ma obsługi ustawiania analitycznego czasu wygaśnięcia na poziomie elementu.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Czy po ustawieniu wartości czasu wygaśnięcia analitycznego na poziomie kontenera Azure Cosmos DB można zmienić inną wartość później?

Tak, analityczny czas TTL można zaktualizować do dowolnej prawidłowej wartości. Zobacz artykuł [analityczny czas wygaśnięcia (TTL)](analytical-store-introduction.md#analytical-ttl) , aby uzyskać więcej informacji na temat analizy czasu wygaśnięcia.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Czy mogę zaktualizować lub usunąć element z magazynu analitycznego po jego upływie czasu wygaśnięcia od magazynu transakcyjnego?

Wszystkie aktualizacje i usunięcia transakcyjne są kopiowane do magazynu analitycznego, ale jeśli element został przeczyszczony ze sklepu transakcyjnego, nie można go zaktualizować w magazynie analitycznym. Aby dowiedzieć się więcej, zobacz artykuł [analityczny czas wygaśnięcia (TTL)](analytical-store-introduction.md#analytical-ttl) .

## <a name="billing"></a>Rozliczenia

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Jaki jest model rozliczeń usługi Azure Synapse dla Azure Cosmos DB?

[Magazyn analityczny Azure Cosmos DB](analytical-store-introduction.md) jest dostępny w publicznej wersji zapoznawczej bez opłat za magazyn analityczny do 30 sierpnia 2020. Synapse Spark i Synapse SQL są rozliczane przez [użycie usługi Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Zabezpieczenia

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Jakie są sposoby uwierzytelniania w magazynie analitycznym?

Uwierzytelnianie za pomocą magazynu analitycznego jest takie samo jak w przypadku magazynu transakcyjnego. Dla danej bazy danych można uwierzytelniać się za pomocą klucza podstawowego lub tylko do odczytu. Możesz użyć połączonej usługi w usłudze Azure Synapse Studio, aby zapobiec wklejaniu kluczy Azure Cosmos DB w notesach platformy Spark. Dostęp do tej połączonej usługi jest dostępny dla wszystkich użytkowników, którzy mają dostęp do obszaru roboczego.

## <a name="synapse-run-times"></a>Synapse czasu wykonywania

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Jakie są obecnie obsługiwane Synapse w czasie wykonywania w celu uzyskania dostępu do magazynu analitycznego Azure Cosmos DB?

|Środowisko uruchomieniowe usługi Azure Synapse |Bieżąca obsługa |
|---------|---------|
|Pule Azure Synapse Spark | Odczyt, zapis (poprzez magazyn transakcyjny), tabela, widok tymczasowy |
|Azure Synapse — pule bezserwerowe SQL    | Odczytaj, Wyświetl |
|Usługa Azure Synapse w wersji zainicjowanej   |  Niedostępne |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Czy moje tabele usługi Azure Synapse Spark są zsynchronizowane z moją tabelą bezserwerową Azure Synapse SQL w taki sam sposób, jak w przypadku Azure Data Lake?

Obecnie ta funkcja jest niedostępna.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Czy można wykonać transmisję strukturalną platformy Spark z magazynu analitycznego?

Obecnie obsługa przesyłania strumieniowego platformy Spark dla Azure Cosmos DB jest implementowana przy użyciu funkcji źródła zmian w magazynie transakcyjnym i nie jest jeszcze obsługiwana w przypadku magazynu analitycznego.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Jak rozpoznać, czy w usłudze Azure Synapse Studio mam połączenie z kontenerem Azure Cosmos DB z włączonym magazynem analizy?

Kontener Azure Cosmos DB włączony z magazynem analitycznym ma następującą ikonę:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Kontener Azure Cosmos DB włączony z magazynem analitycznym — ikona":::

Kontener magazynu transakcyjnego będzie reprezentowany z następującą ikoną:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Kontener Azure Cosmos DB włączony z magazynem analitycznym — ikona":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Jak przekazać poświadczenia Azure Cosmos DB z usługi Azure Synapse Studio?

Obecnie Azure Cosmos DB poświadczenia są przesyłane podczas tworzenia połączonej usługi przez użytkownika, który ma dostęp do Azure Cosmos DB baz danych. Dostęp do tego magazynu jest dostępny dla innych użytkowników, którzy mają dostęp do obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [korzyści z używania usługi Azure Synapse link](synapse-link.md#synapse-link-benefits)

* Poznaj [integrację między usługą Azure Synapse i Azure Cosmos DB](synapse-link.md#synapse-link-integration).
