---
title: Często zadawane pytania dotyczące różnych interfejsów API w Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Azure Cosmos DB, globalnie dystrybuowanej, wielomodelowej usługi bazy danych. Dowiedz się więcej o pojemności, poziomach wydajności i skalowaniu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 79ef46eef4da1beeea1c177ce39a09eea133f545
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089665"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jakie są typowe przypadki użycia dla Azure Cosmos DB?

Azure Cosmos DB to dobry wybór w przypadku nowych aplikacji sieci Web, mobilnych, gier i IoT, w których automatyczne skalowanie, przewidywalna wydajność, szybka kolejność milisekund czas odpowiedzi i możliwość wykonywania zapytań dotyczących danych bez schematu jest ważna. Azure Cosmos DB nadają sobie możliwość szybkiego tworzenia i obsługi ciągłej iteracji modeli danych aplikacji. Aplikacje zarządzające zawartością i danymi generowanymi przez użytkownika są [typowymi przypadkami użycia dla Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Jak Azure Cosmos DB oferować przewidywalną wydajność?

[Jednostka żądania](request-units.md) (ru) to miara przepływności w Azure Cosmos DB. Przepływność 1RU odpowiada przepływności pobrania dokumentu o wartości 1 KB. Każda operacja w Azure Cosmos DB, w tym operacje odczytu, zapisu, zapytania SQL i wykonania procedury składowanej, ma wartość deterministyczną RU, która jest oparta na przepływności wymaganej do ukończenia tej operacji. Zamiast zastanawiać się nad procesorami CPU, we/wy i pamięcią oraz jak każdy ma wpływ na przepływność aplikacji, można myśleć o pojedynczej mierze RU.

Każdy kontener usługi Azure Cosmos można skonfigurować przy użyciu alokowanej przepływności w zakresie jednostek ru przepływności na sekundę. W przypadku aplikacji dowolnej skali można testować pojedyncze żądania, aby zmierzyć ich wartości RU i udostępnić kontener do obsługi łącznej liczby jednostek żądań we wszystkich żądaniach. Możesz również skalować w górę lub w dół przepływność kontenera w miarę rozwoju aplikacji. Aby uzyskać więcej informacji na temat jednostek żądania i pomocy przy określaniu potrzeb kontenera, wypróbuj [Kalkulator przepływności](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Jak Azure Cosmos DB obsługuje różne modele danych, takie jak key/value, kolumnowy, Document i Graph?

Modele danych typu klucz/wartość (tabela), kolumnowy, dokument i Graph są obsługiwane natywnie z powodu projektu ARS (Atoms, Records i sequences), który jest zbudowany Azure Cosmos DB. Atomy, rekordy i sekwencje mogą być łatwo mapowane i rzutowane na różne modele danych. Interfejsy API dla podzestawu modeli są dostępne teraz (SQL, MongoDB, Table i Gremlin), a inne specyficzne dla dodatkowych modeli danych będą dostępne w przyszłości.

Azure Cosmos DB ma aparat indeksowania niezależny od schematu, który umożliwia automatyczne indeksowanie wszystkich danych, które pozyskają, bez konieczności stosowania żadnych indeksów schematu lub pomocniczych od dewelopera. Aparat opiera się na zestawie logicznych układów indeksów (odwróconych, kolumnowych, drzewa), które oddzielają układ magazynu od podsystemów indeksu i przetwarzania zapytań. Cosmos DB ma także możliwość obsługi zestawu protokołów sieci i interfejsów API w sposób rozszerzalny i przetłumaczy je wydajnie na podstawowy model danych (1) i logiczne układy indeksów (2), dzięki czemu można jednoznacznie obsługiwać więcej niż jeden model danych w sposób natywny.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Czy można używać wielu interfejsów API do uzyskiwania dostępu do danych?

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. W przypadku, gdy wiele modeli oznacza, Azure Cosmos DB obsługuje wiele interfejsów API i wielu modeli danych, różne interfejsy API używają różnych formatów danych dla magazynu i protokołu przewodowego. Na przykład SQL używa JSON, MongoDB używa BSON, tabela używa modelu EDM, Cassandra używa CQL, Gremlin używa formatu JSON. W związku z tym zalecamy używanie tego samego interfejsu API do uzyskiwania dostępu do danych w danym koncie.

Każdy interfejs API działa niezależnie, z wyjątkiem interfejsu API Gremlin i SQL, które są współobsługiwane.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Czy Azure Cosmos DB jest zgodny z HIPAA?

Tak, Azure Cosmos DB jest zgodny z HIPAA. Ustawa HIPAA określa wymogi dotyczące wykorzystywania, ujawniania i ochrony informacji o zdrowiu umożliwiających indywidualną identyfikację osoby. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jakie są limity magazynu Azure Cosmos DB?

Nie ma limitu całkowitej ilości danych, które kontener może przechowywać w Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jakie są limity przepływności Azure Cosmos DB?

Nie ma limitu całkowitej ilości przepływności obsługiwanej przez kontener w Azure Cosmos DB. Najważniejszym pomysłem jest równomierne rozłożenie obciążenia na wystarczająco dużą liczbę kluczy partycji.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Czy tryby łączności bezpośredniej i bramy są szyfrowane?

Tak Oba tryby są zawsze w pełni szyfrowane.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Ile kosztuje Azure Cosmos DB?

Szczegółowe informacje można znaleźć na stronie [szczegóły cennika Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Opłaty za użycie Azure Cosmos DB są określane przez liczbę kontenerów z zainicjowaną obsługą, liczbę godzin, w których kontenery były w trybie online, oraz zainicjowaną przepływność dla każdego kontenera.

### <a name="is-a-free-account-available"></a>Czy jest dostępne bezpłatne konto?

Tak, możesz utworzyć konto w ograniczonym czasie bez opłat bez żadnych zobowiązań. Aby się zarejestrować, odwiedź stronę [Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) lub Przeczytaj więcej na stronie [Wypróbuj Azure Cosmos DB często zadawane pytania](#try-cosmos-db).

Jeśli dopiero zaczynasz korzystać z platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), które zapewnia 30 dni i środki do wypróbowania wszystkich usług platformy Azure. Jeśli masz subskrypcję programu Visual Studio, możesz również uzyskać bezpłatne środki na korzystanie z [platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) do użycia w dowolnej usłudze platformy Azure.

Możesz również użyć [emulatora Azure Cosmos DB](local-emulator.md) , aby bezpłatnie opracowywać i testować swoją aplikację lokalnie, bez tworzenia subskrypcji platformy Azure. Jeśli sposób działania aplikacji w emulatorze usługi Azure Cosmos DB jest zadowalający, możesz zacząć korzystać z konta usługi Azure Cosmos DB w chmurze.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak uzyskać dodatkową pomoc dotyczącą Azure Cosmos DB?

Aby zadać pytanie techniczne, możesz ogłosić na jednym z tych pytań i odpowiedzi na forach:

* [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow jest Najlepsza w przypadku pytań związanych z programowaniem. Upewnij się, że pytanie jest [w temacie](https://stackoverflow.com/help/on-topic) i [Podaj jak najwięcej szczegółów, dzięki czemu pytanie jest jasne i możliwe do odpowiedzi](https://stackoverflow.com/help/how-to-ask).

Aby zażądać nowych funkcji, Utwórz nowe żądanie na [głos użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Wypróbuj Azure Cosmos DB subskrypcje

Możesz teraz korzystać z ograniczonych do czasu Azure Cosmos DB środowiska bez subskrypcji i zobowiązań. Aby zarejestrować się w celu uzyskania bezpłatnej subskrypcji usługi try Azure Cosmos DB, przejdź do pozycji [wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) i użyj dowolnego osobistego konto Microsoft (MSA). Ta subskrypcja jest oddzielona od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/)i może być używana razem z bezpłatną wersją próbną platformy Azure lub subskrypcją płatną platformy Azure.

Wypróbuj Azure Cosmos DB subskrypcje pojawiają się w obszarze Azure Portal dalej inne subskrypcje skojarzone z IDENTYFIKATORem użytkownika.

Aby wypróbować subskrypcje Azure Cosmos DB, mają zastosowanie następujące warunki:

* Dostęp do konta można udzielić do osobistych kont Microsoft (MSA). Unikaj używania kont usługi Azure Active Directory (Azure AD) lub kont należących do firmowych dzierżawców usługi Azure AD, które mogą mieć ograniczenia na miejscu, które mogą blokować udzielanie dostępu.
* Jeden [kontener aprowizacji o przepływności](./set-throughput.md#set-throughput-on-a-container) na subskrypcję usług SQL, Gremlin API i Table accounts.
* Maksymalnie trzy [kolekcje inicjowane przez przepływność](./set-throughput.md#set-throughput-on-a-container) na subskrypcję dla kont usługi MongoDB.
* Jedna [baza danych z obsługą przepływności](./set-throughput.md#set-throughput-on-a-database) na subskrypcję. Bazy danych z obsługą przepływności mogą zawierać dowolną liczbę kontenerów wewnątrz.
* Pojemność 10 GB pamięci masowej.
* Replikacja globalna jest dostępna w następujących [regionach świadczenia usługi Azure](https://azure.microsoft.com/regions/): środkowe stany USA, Europa Północna i Azja Południowo-Wschodnia
* Maksymalna przepływność 5 K/s w przypadku aprowizacji na poziomie kontenera.
* Maksymalna przepływność 20 K/s w przypadku aprowizacji na poziomie bazy danych.
* Subskrypcje wygasną po upływie 30 dni i można je przedłużyć do maksymalnie 31 dni. Po wygaśnięciu zawarte informacje zostaną usunięte.
* Nie można utworzyć biletów pomocy technicznej platformy Azure dla usług try Azure Cosmos DB. Jednak pomoc techniczna jest świadczona dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="set-up-azure-cosmos-db"></a>Skonfiguruj Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak mogę zarejestrować się w usłudze Azure Cosmos DB?

Azure Cosmos DB jest dostępna w Azure Portal. Najpierw Zarejestruj się, aby uzyskać subskrypcję platformy Azure. Po zarejestrowaniu możesz dodać konto Azure Cosmos DB do subskrypcji platformy Azure.

### <a name="what-is-a-primary-key"></a>Co to jest klucz podstawowy?

Klucz podstawowy to token zabezpieczający służący do uzyskiwania dostępu do wszystkich zasobów dla konta. Osoby z kluczem mają dostęp do odczytu i zapisu do wszystkich zasobów na koncie bazy danych. Należy zachować ostrożność podczas dystrybucji kluczy podstawowych. Podstawowy klucz podstawowy i pomocniczy klucz podstawowy są dostępne w bloku **klucze** [Azure Portal][azure-portal]. Aby uzyskać więcej informacji na temat kluczy, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Do jakich regionów można ustawić PreferredLocations?

Wartość PreferredLocations można ustawić na dowolne regiony platformy Azure, w których Cosmos DB jest dostępna. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Czy jest coś, co należy wiedzieć, gdy rozpowszechniasz dane na całym świecie za pośrednictwem centrów danych platformy Azure?

Azure Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure, jak określono na stronie [regionów platformy Azure](https://azure.microsoft.com/regions/) . Ponieważ jest to podstawowa usługa, każde nowe centrum danych ma Azure Cosmos DB obecność.

Po ustawieniu regionu należy pamiętać, że Azure Cosmos DB respektuje ona chmury suwerenne i rządowe. Oznacza to, że w przypadku utworzenia konta w [suwerennym regionie](https://azure.microsoft.com/global-infrastructure/)nie można przeprowadzić replikacji poza ten [suwerenny region](https://azure.microsoft.com/global-infrastructure/). Podobnie nie można włączyć replikacji do innych suwerennych lokalizacji na koncie zewnętrznym.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Czy jest możliwe przełączenie z aprowizacji na poziomie kontenera do aprowizacji na poziomie bazy danych? Lub odwrotnie

Alokacja przepływności na poziomie kontenera i bazy danych to osobne oferty i przełączanie między tymi, które wymagają migrowania danych ze źródła do miejsca docelowego. Co oznacza, że trzeba utworzyć nową bazę danych lub nowy kontener, a następnie przeprowadzić migrację danych przy użyciu [biblioteki wykonawczy zbiorczej](bulk-executor-overview.md) lub [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Czy usługa Azure CosmosDB obsługuje analizę szeregów czasowych?

Tak, aby usługa Azure CosmosDB obsługiwała analizę szeregów czasowych, Oto przykład dla [wzorca szeregów czasowych](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Ten przykład pokazuje, jak używać kanału informacyjnego zmiany do kompilowania zagregowanych widoków w danych szeregów czasowych. Tę metodę można rozłożyć przy użyciu strumienia Spark lub innego procesora danych strumienia.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jakie są limity przydziału usługi Azure Cosmos DB i przepływności

Aby uzyskać więcej informacji, zobacz [przydziały usługi](concepts-limits.md) Azure Cosmos DB i [limity dotyczące poszczególnych kontenerów i baz danych](set-throughput.md#comparison-of-models) .

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Często zadawane pytania dotyczące interfejsu API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak mogę rozpocząć programowanie względem interfejsu API SQL?

Najpierw musisz zarejestrować się, aby uzyskać subskrypcję platformy Azure. Po zarejestrowaniu się w celu uzyskania subskrypcji platformy Azure Możesz dodać do niej kontener interfejsu API SQL. Aby uzyskać instrukcje dotyczące dodawania konta Azure Cosmos DB, zobacz [Tworzenie konta bazy danych usługi Azure Cosmos](create-sql-api-dotnet.md#create-account).

[Zestawy SDK](sql-api-sdk-dotnet.md) są dostępne dla języków .NET, Python, Node.js, JavaScript i Java. Deweloperzy mogą również używać [interfejsów API RESTful http](/rest/api/cosmos-db/) do współpracy z zasobami Azure Cosmos DB z różnych platform i języków.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Czy mogę uzyskać dostęp do gotowych przykładów, aby zacząć od początku?

Przykłady [dla interfejsów API](sql-api-dotnet-samples.md)SQL, [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)i [Python](sql-api-python-samples.md) SDK są dostępne w serwisie GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Czy baza danych SQL API obsługuje dane bez schematu?

Tak. interfejs API SQL umożliwia aplikacjom przechowywanie dowolnych dokumentów JSON bez definicji schematu ani wskazówek. Dane są natychmiast dostępne dla zapytań za pomocą interfejsu zapytania SQL Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>Czy interfejs API SQL obsługuje transakcje dotyczące kwasu?

Tak, interfejs API SQL obsługuje transakcje między dokumentami wyrażone jako procedury i wyzwalacze przechowywane w języku JavaScript. Transakcje są objęte zakresem pojedynczej partycji w ramach każdego kontenera i wykonywane z użyciem semantyki KWASowej jako "All lub Nothing", odizolowane od innych współbieżnie wykonywanych żądań kodu i użytkowników. Jeśli wyjątki są zgłaszane przez wykonanie kodu aplikacji JavaScript po stronie serwera, cała transakcja zostanie wycofana. 

### <a name="what-is-a-container"></a>Co to jest kontener?

Kontener jest grupą dokumentów i skojarzonych z nimi logiki aplikacji JavaScript. Kontener jest jednostką rozliczaną, w której [koszt](performance-levels.md) jest określany przez przepływność i użycie magazynu. Kontenery mogą obejmować co najmniej jedną partycję lub serwery i mogą być skalowane w celu obsługi praktycznie nieograniczonego wolumenu magazynu lub przepływności.

* W przypadku interfejsu API SQL zasób nosi nazwę kontenera.
* Dla interfejsu API Cosmos DB dla kont MongoDB, kontener jest mapowany do kolekcji.
* W przypadku kont Cassandra i interfejs API tabel kontener jest mapowany na tabelę.
* W przypadku kont interfejsu API Gremlin kontener jest mapowany na wykres.

Kontenery są również jednostkami rozliczeń dla Azure Cosmos DB. Każdy kontener jest rozliczany godzinowo, w oparciu o zainicjowaną przepływność i ilość miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Jak utworzyć bazę danych?

Bazy danych można tworzyć przy użyciu [Azure Portal](https://portal.azure.com), zgodnie z opisem w [sekcji Dodawanie kontenera](create-sql-api-java.md#add-a-container), jednego z [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak skonfigurować użytkowników i uprawnienia?

Możesz tworzyć użytkowników i uprawnienia za pomocą jednego z [Cosmos DB zestawów SDK interfejsu API](sql-api-sdk-dotnet.md) lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Czy interfejs API SQL obsługuje kod SQL?

Język zapytań SQL obsługiwany przez konta interfejsu API SQL to ulepszony podzbiór funkcji zapytania, które są obsługiwane przez SQL Server. Język zapytań SQL Azure Cosmos DB zapewnia zaawansowane operatory hierarchiczne i relacyjne oraz rozszerzalność za pośrednictwem opartych na języku JavaScript funkcji zdefiniowanych przez użytkownika (UDF). Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzew z węzłami oznaczonymi etykietami, które są używane przez zarówno Azure Cosmos DB technik automatycznego indeksowania i dialekt zapytań SQL Azure Cosmos DB. Aby uzyskać informacje o używaniu gramatyki SQL, zobacz artykuł dotyczący [zapytań SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Czy interfejs API SQL obsługuje funkcje agregacji SQL?

Interfejs API SQL obsługuje agregację o małym opóźnieniu w dowolnej skali za pośrednictwem funkcji agregujących `COUNT` , `MIN` ,, `MAX` `AVG` i `SUM` za pomocą gramatyki SQL. Aby uzyskać więcej informacji, zobacz [funkcje agregujące](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Jak interfejs API SQL udostępnia współbieżność?

Interfejs API SQL obsługuje optymistyczną kontrolę współbieżności (OCC) za pośrednictwem tagów jednostek HTTP lub elementów ETag. Każdy zasób interfejsu API SQL ma element ETag, a element ETag jest ustawiany na serwerze za każdym razem, gdy dokument zostanie zaktualizowany. Nagłówek ETag i bieżąca wartość są uwzględniane we wszystkich komunikatach odpowiedzi. Elementy ETag mogą być używane z nagłówkiem If-Match, aby umożliwić serwerowi podjęcie decyzji o tym, czy zasób powinien zostać zaktualizowany. Wartość If-Match jest wartością ETag, która ma zostać sprawdzona. Jeśli wartość ETag jest zgodna z wartością ETag serwera, zasób zostanie zaktualizowany. Jeśli element ETag nie jest już aktualny, serwer odrzuca operację z kodem odpowiedzi "HTTP 412 niepowodzenie warunku wstępnego". Klient ponownie pobierze zasób, aby uzyskać bieżącą wartość ETag dla zasobu. Ponadto elementy ETag mogą być używane z nagłówkiem If-None-Match, aby określić, czy konieczne jest ponowne pobranie zasobu.

Aby użyć optymistycznej współbieżności w programie .NET, należy użyć klasy [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) . Aby zapoznać się z przykładem platformy .NET, zobacz [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) w przykładzie DocumentManagement w witrynie GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak mogę wykonywać transakcji w interfejsie API SQL?

Interfejs API SQL obsługuje transakcje zintegrowane z językiem za pomocą procedur i wyzwalaczy przechowywanych w języku JavaScript. Wszystkie operacje bazy danych wewnątrz skryptów są wykonywane w ramach izolacji migawki. Jeśli jest to kontener o pojedynczej partycji, wykonanie jest ograniczone do kontenera. W przypadku partycjonowania kontenera wykonywanie jest ograniczone do dokumentów o tej samej wartości klucza partycji w kontenerze. Migawka wersji dokumentów (elementy ETag) jest wykonywana na początku transakcji i zatwierdzana tylko wtedy, gdy działanie skryptu zakończy się pomyślnie. Jeśli kod JavaScript zwraca błąd, transakcja zostaje wycofana. Aby uzyskać więcej informacji, zobacz [programowanie JavaScript po stronie serwera dla Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak można zbiorczo wstawiać dokumenty do Cosmos DB?

Można zbiorczo wstawiać dokumenty do Azure Cosmos DB w jeden z następujących sposobów:

* Narzędzie do wykonywania zbiorczego, zgodnie z opisem w temacie Korzystanie z biblioteki wykonawczej [programu .NET](bulk-executor-dot-net.md) i [Używanie zbiorczej procedury wykonawcze środowiska Java](bulk-executor-java.md)
* Narzędzie do migracji danych, zgodnie z opisem w [narzędziu do migracji bazy danych dla Azure Cosmos DB](import-data.md).
* Procedury składowane, zgodnie z opisem w [programowaniu JavaScript po stronie serwera dla Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Czy interfejs API SQL obsługuje buforowanie linków zasobów?

Tak, ponieważ Azure Cosmos DB jest usługą RESTful, linki zasobów są niezmienne i mogą być buforowane. Klienci interfejsu API SQL mogą określić nagłówek "If-None-Match", aby odczytywać wszystkie dokumenty lub kontenery podobne do zasobów, a następnie zaktualizować ich kopie lokalne po zmianie wersji serwera.

### <a name="is-a-local-instance-of-sql-api-available"></a>Czy jest dostępne lokalne wystąpienie interfejsu API SQL?

Tak. [Emulator Azure Cosmos DB](local-emulator.md) zapewnia emulację o wysokiej dokładności usługi Cosmos DB. Obsługuje ona funkcje identyczne z Azure Cosmos DB, w tym obsługę tworzenia i wykonywania zapytań dotyczących dokumentów JSON, aprowizacji i skalowania kolekcji oraz wykonywania procedur składowanych i wyzwalaczy. Możesz tworzyć i testować aplikacje za pomocą emulatora Azure Cosmos DB i wdrażać je na platformie Azure na skalę globalną, wprowadzając jedną zmianę konfiguracji do punktu końcowego połączenia dla Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Dlaczego są długotrwałe wartości zmiennoprzecinkowe w dokumencie zaokrąglonym podczas wyświetlania z Eksploratora danych w portalu.

Jest to ograniczenie języka JavaScript. Kod JavaScript używa liczb zmiennoprzecinkowych o podwójnej precyzji, jak określono w standardzie IEEE 754 i może bezpiecznie przechowywać cyfry od-(2<sup>53</sup> -1) i 2<sup>53</sup>-1 (tj. 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Gdzie są dozwolone uprawnienia w hierarchii obiektów?

Tworzenie uprawnień przy użyciu ResourceTokens jest dozwolone na poziomie kontenera i jego elementach podrzędnych (takich jak dokumenty, załączniki). Oznacza to, że próba utworzenia uprawnienia do bazy danych lub poziomu konta nie jest obecnie dozwolona.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat często zadawanych pytań w innych interfejsach API, zobacz:

* Często zadawane pytania dotyczące [interfejsu API Azure Cosmos DB MongoDB](mongodb-api-faq.md)
* Często zadawane pytania dotyczące [interfejsu API Gremlin w Azure Cosmos DB](gremlin-api-faq.md)
* Często zadawane pytania dotyczące [interfejs API Cassandra w programie Azure Cosmos DB](cassandra-faq.md)
* Często zadawane pytania dotyczące [interfejs API tabel w programie Azure Cosmos DB](table-api-faq.md)