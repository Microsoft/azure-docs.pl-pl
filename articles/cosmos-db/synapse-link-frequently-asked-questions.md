---
title: Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Synapse Link dla Azure Cosmos DB w takich obszarach, jak rozliczenia, magazyn analityczny, zabezpieczenia, czas życia w magazynie analitycznym.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483857"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Często zadawane pytania dotyczące usługi Azure Synapse Link dla usługi Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link for Azure Cosmos DB tworzy ścisłą integrację między Azure Cosmos DB i Azure Synapse Analytics. Umożliwia klientom uruchamianie analiz niemal w czasie rzeczywistym na danych operacyjnych z pełną izolacją wydajności od obciążeń transakcyjnych i bez potoku ETL. W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Synapse Link dla usługi Azure Cosmos DB.

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Czy Azure Synapse Link jest obsługiwany dla wszystkich Azure Cosmos DB API?

Azure Synapse Link jest obsługiwany w przypadku podstawowego interfejsu API sql Azure Cosmos DB oraz interfejsu API Azure Cosmos DB dla bazy danych MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Czy Azure Synapse link jest obsługiwany w przypadku kont Azure Cosmos DB wielu regionach?

Tak, w przypadku kont usługi Azure Cosmos w wielu regionach dane przechowywane w magazynie analitycznym są również dystrybuowane globalnie. Niezależnie czy chodzi o pojedynczy region zapisu, czy wiele regionów zapisu, analityczne zapytania wykonywane z usługi Azure Synapse Analytics mogą być obsługiwane z najbliższego lokalnego regionu.

Podczas planowania konfigurowania konta magazynu Azure Cosmos DB w wielu regionach z obsługą magazynu analitycznego zaleca się dodanie wszystkich niezbędnych regionów w czasie tworzenia konta.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Czy mogę włączyć usługę Azure Synapse Link tylko dla określonego regionu, a nie dla wszystkich regionów w przypadku skonfigurowania konta w wielu regionach?

Gdy Azure Synapse link jest włączony dla konta w wielu regionach, magazyn analityczny jest tworzony we wszystkich regionach. Dane bazowe są zoptymalizowane pod kątem przepływności i spójności transakcyjnej w magazynie transakcyjnym.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Czy magazyn analityczny jest obsługiwany we wszystkich Azure Cosmos DB regionach?

Tak.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Czy kopie zapasowe i przywracanie są obsługiwane Azure Synapse kont z włączoną obsługą usługi Link?

W przypadku kontenerów z włączonym magazynem analitycznym automatyczne tworzenie kopii zapasowych i przywracanie danych w magazynie analitycznym nie jest obecnie obsługiwane. 

Gdy Synapse Link na koncie bazy danych, program Azure Cosmos DB będzie [](./online-backup-and-restore.md) nadal automatycznie tworzyć kopie zapasowe danych w magazynie transakcyjnym (tylko) kontenerów w zaplanowanych interwałach tworzenia kopii zapasowych, jak zawsze. Należy pamiętać, że gdy kontener z włączonym magazynem analitycznym zostanie przywrócony do nowego konta, kontener zostanie przywrócony tylko z magazynem transakcyjnym i bez włączonego magazynu analitycznego. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Czy mogę wyłączyć funkcję Azure Synapse Link dla mojego Azure Cosmos DB konta?

Obecnie po włączeniu funkcji usługi Synapse Link na poziomie konta nie można jej wyłączyć. Należy pamiętać, że włączenie funkcji usługi Synapse Link na poziomie konta nie pociąga żadnych implikacji rozliczeniowych w przypadku braku kontenerów z włączonym magazynem analitycznym.

Jeśli musisz wyłączyć tę możliwość, masz 2 opcje. Pierwszą jest usunięcie i ponowne utworzenie nowego konta usługi Azure Cosmos DB oraz, jeśli jest to konieczne, przeprowadzenie migracji danych. Drugą opcją to otwarcie biletu pomocy technicznej, aby uzyskać pomoc na temat migracji danych na inne konto.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Czy magazyn analityczny ma jakikolwiek wpływ na Cosmos DB transakcyjnych sla?

Nie, nie ma to żadnego wpływu.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB magazynu analitycznego

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Czy mogę włączyć magazyn analityczny w istniejących kontenerach?

Obecnie magazyn analityczny można włączyć tylko dla nowych kontenerów (zarówno na nowych, jak i istniejących kontach).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Czy mogę wyłączyć magazyn analityczny w kontenerach Azure Cosmos DB po włączeniu go podczas tworzenia kontenera?

Obecnie nie można wyłączyć magazynu analitycznego w kontenerze usługi Azure Cosmos DB po jego włączeniu podczas tworzenia kontenera.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Czy magazyn analityczny jest obsługiwany dla kontenerów Azure Cosmos DB z aprowizowana przepływnością autoskalowania?

Tak, magazyn analityczny można włączyć w kontenerach z aprowizowana automatycznie przepływnością.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Czy istnieje jakikolwiek wpływ na Azure Cosmos DB magazynu transakcyjnego aprowizowanych procesorów?

Azure Cosmos DB gwarantuje izolację wydajności między obciążeniami transakcyjnym i analitycznym. Włączenie magazynu analitycznego w kontenerze nie będzie miało wpływu na wartość RU/s aprowizowana w Azure Cosmos DB magazynu transakcyjnego. Opłaty za transakcje (& odczytu i zapisu) i magazyn dla magazynu analitycznego będą naliczane osobno. Aby uzyskać [więcej informacji, zobacz cennik](analytical-store-introduction.md#analytical-store-pricing) Azure Cosmos DB magazynu analitycznego.

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Czy można ograniczyć dostęp sieciowy do Azure Cosmos DB magazynu analitycznego?

Tak, można skonfigurować zarządzany [prywatny punkt końcowy i](analytical-store-private-endpoints.md) ograniczyć dostęp sieciowy magazynu analitycznego do Azure Synapse zarządzanej sieci wirtualnej. Zarządzane prywatne punkty końcowe ustanawiają prywatne łącze do magazynu analitycznego. 

Możesz dodać prywatne punkty końcowe magazynu transakcyjnego i magazynu analitycznego do tego samego Azure Cosmos DB w Azure Synapse Analytics roboczym. Jeśli chcesz tylko uruchamiać zapytania analityczne, możesz włączyć analityczny prywatny punkt końcowy tylko w Synapse Analytics roboczym.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Czy mogę używać kluczy zarządzanych przez klienta z magazynem Azure Cosmos DB analitycznym?

Dane w magazynach transakcyjnych i analitycznych można bezproblemowo szyfrować przy użyciu tych samych kluczy zarządzanych przez klienta w sposób automatyczny i przezroczysty. Aby używać kluczy zarządzanych przez klienta z magazynem analitycznym, należy użyć przypisanej przez system tożsamości zarządzanej konta usługi Azure Cosmos DB w zasadach Azure Key Vault dostępu. Opisano to [tutaj.](how-to-setup-cmk.md#using-managed-identity) Następnie powinno być możliwe włączenie magazynu analitycznego na koncie.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Czy operacje usuwania i aktualizowania w magazynie transakcyjnym są odzwierciedlane w magazynie analitycznym?

Tak, usuwa i aktualizuje dane w magazynie transakcyjnym zostaną odzwierciedlone w magazynie analitycznym. Czas wygaśnięcia (TTL) w kontenerze można skonfigurować tak, aby zawierał dane historyczne, tak aby magazyn analityczny zachowywał wszystkie wersje elementów spełniających analityczne kryteria czasu wygaśnięcia. Aby uzyskać więcej informacji, zobacz omówienie [analitycznego TTL.](analytical-store-introduction.md#analytical-ttl)

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Czy można połączyć się z magazynem analitycznym z aparatów analitycznych innych niż Azure Synapse Analytics?

Można uzyskać dostęp magazynu analitycznego i uruchamiać zapytania względem magazynu analitycznego, jedynie korzystając z różnych środowisk uruchomieniowych udostępnianych przez usługę Azure Synapse Analytics. Magazyn analityczny można odpytywać i analizować przy użyciu następujących narzędzi:

* Synapse Spark z pełną obsługą języka Scala, Python, SparkSQL i C#. Usługa Synapse Spark stanowi podstawę w przypadku scenariuszy z zakresu inżynierii danych i nauki
* Bez serverless SQL pool with T-SQL language and support for familiar BI tools (For example, Power BI Premium, etc.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Czy można połączyć się z magazynem analitycznym z Synapse SQL aprowizowanych?

Obecnie nie można uzyskać dostępu do magazynu analitycznego z Synapse SQL aprowizowanych.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Czy mogę zapisać z powrotem wyniki agregacji zapytania z usługi Synapse do magazynu analitycznego?

Magazyn analityczny jest magazynem tylko do odczytu w kontenerze Azure Cosmos DB danych. W związku z tym nie można bezpośrednio zapisywać wyników agregacji w magazynie analitycznym, ale można je zapisywać w magazynie transakcyjnym Azure Cosmos DB innego kontenera, który można później wykorzystać jako warstwę obsługi.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Czy replikacja automatycznej synchronizacji z magazynu transakcyjnego do magazynu analitycznego jest asynchroniczna, czy synchroniczna i jakie są opóźnienia?

Opóźnienie automatycznej synchronizacji zwykle wynosi w ciągu 2 minut. W przypadku bazy danych z udostępnioną przepływnością z dużą liczbą kontenerów opóźnienie automatycznej synchronizacji poszczególnych kontenerów może być wyższe i może potrwać do 5 minut. Chcemy dowiedzieć się więcej o tym, jak to opóźnienie pasuje do Twoich scenariuszy. W tym celu skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Czy istnieją scenariusze, w których elementy z magazynu transakcyjnego nie są automatycznie propagowane do magazynu analitycznego?

Jeśli określone elementy w kontenerze naruszają [dobrze zdefiniowany schemat](analytical-store-introduction.md#analytical-schema)analizy, nie zostaną uwzględnione w magazynie analitycznym. Jeśli masz scenariusze zablokowane przez dobrze zdefiniowany schemat analizy, wyślij wiadomość e-mail do zespołu [Azure Cosmos DB,](mailto:cosmosdbsynapselink@microsoft.com) aby uzyskać pomoc.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Czy mogę partycjonować dane w magazynie analitycznym inaczej niż w magazynie transakcyjnym?

Dane w magazynie analitycznym są partycjonowane w oparciu o partycjonowanie poziome fragmentów w magazynie transakcyjnym. Obecnie nie można wybrać innej strategii partycjonowania dla magazynu analitycznego.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Czy mogę dostosować lub zastąpić sposób przekształcania danych transakcyjnych w format kolumnowy w magazynie analitycznym?

Obecnie nie można przekształcać elementów danych, gdy są one automatycznie propagowane z magazynu transakcyjnego do magazynu analitycznego. Jeśli masz scenariusze zablokowane przez to ograniczenie, wyślij wiadomość e-mail [Azure Cosmos DB zespołu.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="is-analytical-store-supported-by-terraform"></a>Czy magazyn analityczny jest obsługiwany przez program Terraform?

Obecnie program Terraform nie obsługuje kontenerów magazynu analitycznego. Aby uzyskać więcej informacji, zobacz [problemy dotyczące programu Terraform w serwisie GitHub](https://github.com/hashicorp/terraform/issues).

## <a name="analytical-time-to-live-ttl"></a>Analityczny czas wygaśnięcia (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Czy czas wygaśnięcia dla danych analitycznych jest obsługiwany zarówno na poziomie kontenera, jak i elementu?

Obecnie wartość TTL dla danych analitycznych można skonfigurować tylko na poziomie kontenera i ustawianie analitycznego czasu wygaśnięcia na poziomie elementu nie jest obsługiwane.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Czy po ustawieniu analitycznego czasu wygaśnięcia na poziomie kontenera Azure Cosmos DB kontenerze można później zmienić wartość na inną?

Tak, analityczny czas wygaśnięcia można zaktualizować do dowolnej prawidłowej wartości. Aby uzyskać więcej [informacji na temat analitycznego TTL,](analytical-store-introduction.md#analytical-ttl) zobacz artykuł Analityczny czas wygaśnięcia.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Czy mogę zaktualizować lub usunąć element z magazynu analitycznego po upływie czasu wygaśnięcia z magazynu transakcyjnego?

Wszystkie aktualizacje i usunięcia transakcyjne są kopiowane do magazynu analitycznego, ale jeśli element został przeczyszany z magazynu transakcyjnego, nie można go zaktualizować w magazynie analitycznym. Aby dowiedzieć się więcej, zobacz [artykuł Analityczny czas wygaśnięcia.](analytical-store-introduction.md#analytical-ttl)

## <a name="billing"></a>Rozliczenia

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Jaki jest model rozliczeń Azure Synapse Link dla Azure Cosmos DB?

Model rozliczeń usługi Azure Synapse Link obejmuje koszty związane z używaniem magazynu analitycznego usługi Azure Cosmos DB i środowiska uruchomieniowego usługi Synapse. Aby dowiedzieć się więcej, zobacz [cennik Azure Cosmos DB magazynu analitycznego](analytical-store-introduction.md#analytical-store-pricing) i [Azure Synapse Analytics cennika.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Jaki wpływ na rozliczenia ma włączenie Synapse Link na moim Azure Cosmos DB bazy danych?

Brak. Opłaty będą naliczane tylko podczas tworzenia kontenera z obsługą magazynu analitycznego i rozpoczynania ładowania danych.


## <a name="security"></a>Zabezpieczenia

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Jakie są sposoby uwierzytelniania w magazynie analitycznym?

Uwierzytelnianie za pomocą magazynu analitycznego jest takie samo jak magazyn transakcyjny. Dla danej bazy danych można uwierzytelnić się przy użyciu klucza podstawowego lub tylko do odczytu. Możesz użyć połączonej usługi w programie Azure Synapse Studio, aby zapobiec wklejania kluczy Azure Cosmos DB w notesach platformy Spark. Dostęp do tej połączonej usługi jest dostępny dla wszystkich użytkowników, którzy mają dostęp do obszaru roboczego.

W przypadku korzystania z bez serwera pul SQL usługi Synapse można odpytować magazyn analityczny usługi Azure Cosmos DB, wstępnie tworząc poświadczenia SQL przechowujące klucze kont i odwołując się do nich w funkcji OPENROWSET. Aby dowiedzieć się więcej, zobacz [artykuł Query with a serverless SQL pool in Azure Synapse Link (Wykonywanie](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) zapytań przy użyciu bez serwera puli SQL w Azure Synapse Link).

## <a name="synapse-run-times"></a>Czasy uruchamiania synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Jakie są obecnie obsługiwane czasy uruchamiania usługi Synapse w celu uzyskania dostępu Azure Cosmos DB magazynu analitycznego?

|Azure Synapse uruchomieniowe |Bieżąca obsługa |
|---------|---------|
|Azure Synapse spark | Odczyt, zapis (za pośrednictwem magazynu transakcyjnego), tabela, widok tymczasowy |
|Azure Synapse bez serwera SQL    | Odczyt, wyświetlanie |
|Azure Synapse SQL Provisioned   |  Niedostępne |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Czy moje tabele Azure Synapse Spark są synchronizowane z tabelami Azure Synapse bez serwera SQL tak samo jak z usługą Azure Data Lake?

Obecnie ta funkcja nie jest dostępna.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Czy mogę przesyłać strumieniowo ze strukturą platformy Spark z magazynu analitycznego?

Obecnie obsługa przesyłania strumieniowego ze strukturą platformy Spark Azure Cosmos DB jest implementowane przy użyciu funkcji zestawienia zmian magazynu transakcyjnego i nie jest jeszcze obsługiwana z magazynu analitycznego.

### <a name="is-streaming-supported"></a>Czy przesyłanie strumieniowe jest obsługiwane?

Nie obsługujemy przesyłania strumieniowego danych z magazynu analitycznego.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Jak w Azure Synapse Studio sprawdzić, czy mam połączenie z kontenerem usługi Azure Cosmos DB z włączonym magazynem analitycznym?

Kontener Azure Cosmos DB z włączonym magazynem analitycznym ma następującą ikonę:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB z włączonym magazynem analitycznym — ikona":::

Kontener magazynu transakcyjnego będzie reprezentowany za pomocą następującej ikony:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB z włączonym magazynem transakcyjnym — ikona":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Jak przekazać poświadczenia Azure Cosmos DB z programu Azure Synapse Studio?

Obecnie Azure Cosmos DB są przekazywane podczas tworzenia połączonej usługi przez użytkownika, który ma dostęp do Azure Cosmos DB danych. Dostęp do tego sklepu jest dostępny dla innych użytkowników, którzy mają dostęp do obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o zaletach Azure Synapse Link](synapse-link.md#synapse-link-benefits)

* Dowiedz się więcej [o integracji między Azure Synapse Link i Azure Cosmos DB](synapse-link.md#synapse-link-integration).
