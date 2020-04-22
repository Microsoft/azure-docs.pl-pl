---
title: Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Cosmos DB, globalnie rozproszonej, wielomodelowej usługi bazy danych. Dowiedz się więcej o pojemności, poziomach wydajności i skalowaniu.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 25b669493e1ee32df4a8c6fc33bdb1b21b08c70f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683328"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące różnych interfejsów API w usłudze Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Jakie są typowe przypadki użycia usługi Azure Cosmos DB?

Usługa Azure Cosmos DB to dobry wybór dla nowych aplikacji sieci web, urządzeń mobilnych, gier i IoT, w których ważna jest automatyczna skala, przewidywalna wydajność, szybka kolejność milisekundowych czasów odpowiedzi i możliwość wykonywania zapytań o dane wolne od schematu. Usługa Azure Cosmos DB nadaje się do szybkiego rozwoju i obsługi ciągłej iteracji modeli danych aplikacji. Aplikacje zarządzające zawartością i danymi generowanymi przez użytkowników są [typowymi przypadkami użycia usługi Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>W jaki sposób usługa Azure Cosmos DB oferuje przewidywalną wydajność?

[Jednostka żądania](request-units.md) (RU) jest miarą przepływności w usłudze Azure Cosmos DB. Przepływność 1RU odpowiada przepływności get dokumentu 1 KB. Każda operacja w usłudze Azure Cosmos DB, w tym odczyty, zapisy, zapytania SQL i wykonania procedur składowanych, ma deterministyczną wartość RU, która jest oparta na przepływności wymaganej do ukończenia operacji. Zamiast myśleć o procesorze CPU, we/wy i pamięci oraz o tym, jak każdy z nich wpływa na przepływność aplikacji, można myśleć w kategoriach pojedynczej miary RU.

Można skonfigurować każdy kontener usługi Azure Cosmos z aprowizowaną przepływnością pod względem wydajności procesorów RU na sekundę. W przypadku aplikacji dowolnej skali można porównać indywidualne żądania do pomiaru ich wartości RU i aprowizować kontener do obsługi całkowitej liczby jednostek żądań we wszystkich żądaniach. Można również skalować w górę lub skalować w dół przepływności kontenera w miarę rozwoju potrzeb aplikacji. Aby uzyskać więcej informacji na temat jednostek żądań i pomocy w określaniu potrzeb kontenera, wypróbuj [kalkulator przepływności](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>W jaki sposób usługa Azure Cosmos DB obsługuje różne modele danych, takie jak klucz/wartość, kolumna, dokument i wykres?

Klucz/wartość (tabela), kolumnowe, dokument i wykres modeli danych są obsługiwane natywnie ze względu na ARS (atomy, rekordy i sekwencje) projekt, który jest zbudowany na usługi Azure Cosmos DB. Atomy, rekordy i sekwencje można łatwo mapować i rzutować na różne modele danych. Interfejsy API dla podzbioru modeli są już dostępne (SQL, MongoDB, Table i Gremlin), a inne specyficzne dla dodatkowych modeli danych będą dostępne w przyszłości.

Usługa Azure Cosmos DB ma aparat indeksowania niezależny od schematu, który umożliwia automatyczne indeksowanie wszystkich danych, które publikuje, bez konieczności tworzenia jakichkolwiek schematów lub indeksów pomocniczych od dewelopera. Aparat opiera się na zestawie układów indeksu logicznego (odwrócony, kolumnowy, drzewa), które oddzielają układ magazynu od podsystemów przetwarzania indeksu i kwerend. Usługa Cosmos DB ma również możliwość obsługi zestawu protokołów przewodowych i interfejsów API w sposób rozszerzalny i tłumaczyć je skutecznie na podstawowy model danych (1) i układy indeksu logicznego (2), dzięki czemu jest on wyjątkowo zdolny do obsługi więcej niż jednego modelu danych natywnie.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Czy mogę używać wielu interfejsów API, aby uzyskać dostęp do moich danych?

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Gdzie wiele modeli oznacza, że usługa Azure Cosmos DB obsługuje wiele interfejsów API i wiele modeli danych, różne interfejsy API używają różnych formatów danych do przechowywania i protokołu przewodowego. Na przykład SQL używa JSON, MongoDB używa BSON, Tabela używa EDM, Cassandra używa CQL, Gremlin używa formatu JSON. W związku z tym zaleca się używanie tego samego interfejsu API dla wszystkich dostępu do danych na danym koncie.

Każdy interfejs API działa niezależnie, z wyjątkiem interfejsu API Gremlin i SQL, które są interoperacyjne.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Czy usługa Azure Cosmos DB jest zgodna z normą HIPAA?

Tak, usługa Azure Cosmos DB jest zgodna z normą HIPAA. Ustawa HIPAA określa wymogi dotyczące wykorzystywania, ujawniania i ochrony informacji o zdrowiu umożliwiających indywidualną identyfikację osoby. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Jakie są limity magazynowania usługi Azure Cosmos DB?

Nie ma limitu całkowitej ilości danych, które kontener może przechowywać w usłudze Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Jakie są limity przepływności usługi Azure Cosmos DB?

Nie ma limitu całkowitej przepływności, która kontener może obsługiwać w usłudze Azure Cosmos DB. Kluczową ideą jest dystrybucja obciążenia mniej więcej równomiernie między wystarczająco dużą liczbę kluczy partycji.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Czy tryby łączności direct i gateway są szyfrowane?

Tak, oba tryby są zawsze w pełni zaszyfrowane.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Ile kosztuje usługa Azure Cosmos DB?

Aby uzyskać szczegółowe informacje, zobacz stronę [szczegółów cennika usługi Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Opłaty za użycie usługi Azure Cosmos DB są określane przez liczbę aprowizowanych kontenerów, liczbę godzin kontenerów w trybie online i aprowizowaną przepływność dla każdego kontenera.

### <a name="is-a-free-account-available"></a>Czy dostępne jest darmowe konto?

Tak, możesz założyć konto ograniczone w czasie bez żadnych opłat, bez zobowiązań. Aby się zarejestrować, odwiedź [witrynę Wypróbuj usługę Azure Cosmos DB za darmo](https://azure.microsoft.com/try/cosmosdb/) lub przeczytaj więcej w [często zadawanych pytaniach dotyczących usługi Try Azure Cosmos DB.](#try-cosmos-db)

Jeśli jesteś nowym użytkownikiem platformy Azure, możesz zarejestrować się w celu utworzenia [bezpłatnego konta platformy Azure,](https://azure.microsoft.com/free/)które daje 30 dni i środki na wypróbowanie wszystkich usług platformy Azure. Jeśli masz subskrypcję programu Visual Studio, masz również prawo do [bezpłatnych kredytów platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) do użycia w dowolnej usłudze platformy Azure.

Można również użyć [emulatora usługi Azure Cosmos DB](local-emulator.md) do tworzenia i testowania aplikacji lokalnie za darmo, bez tworzenia subskrypcji platformy Azure. Jeśli sposób działania aplikacji w emulatorze usługi Azure Cosmos DB jest zadowalający, możesz zacząć korzystać z konta usługi Azure Cosmos DB w chmurze.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Jak uzyskać dodatkową pomoc dotyczącą usługi Azure Cosmos DB?

Aby zadać pytanie techniczne, możesz opublikować na jednym z tych dwóch pytań i odpowiedzi na forach:

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najlepsze dla pytań programowania. Upewnij się, że twoje pytanie jest [na temat](https://stackoverflow.com/help/on-topic) i podać jak [najwięcej szczegółów, jak to możliwe, dzięki czemu pytanie jasne i odpowiadać](https://stackoverflow.com/help/how-to-ask).

Aby zażądać nowych funkcji, utwórz nowe żądanie w [głosie użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Wypróbuj subskrypcje usługi Azure Cosmos DB

Teraz możesz cieszyć się ograniczonym w czasie doświadczeniem usługi Azure Cosmos DB bez subskrypcji, bezpłatnie i zobowiązań. Aby zarejestrować się w ramach subskrypcji usługi Try Cosmos DB, przejdź do [usługi Wypróbuj usługę Azure Cosmos DB za darmo](https://azure.microsoft.com/try/cosmosdb/) i użyj dowolnego osobistego konta Microsoft (MSA). Ta subskrypcja jest niezależna od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/)i może być używana wraz z bezpłatną wersję próbną platformy Azure lub płatną subskrypcją platformy Azure.

Wypróbuj subskrypcje usługi Azure Cosmos DB są wyświetlane w witrynie Azure portal obok innych subskrypcji skojarzonych z identyfikatorem użytkownika.

Następujące warunki dotyczą subskrypcji usługi Azure Cosmos DB:

* Dostęp do konta można przyznać osobistym kontom Microsoft (MSA). Unikaj używania kont lub kont usługi Active Directory (AAD) należących do firmowych dzierżaw y usługi AAD, mogą one mieć ograniczenia, które mogą blokować udzielanie dostępu.
* Jeden [kontener aprowizowana przepływności](./set-throughput.md#set-throughput-on-a-container) na subskrypcję kont SQL, Gremlin API i Table.
* Maksymalnie trzy [aprowizowanej kolekcji przepływności](./set-throughput.md#set-throughput-on-a-container) na subskrypcję dla kont MongoDB.
* Jedna [baza danych aprowizowana przepływności](./set-throughput.md#set-throughput-on-a-database) na subskrypcję. Przepływność aprowizacji baz danych może zawierać dowolną liczbę kontenerów wewnątrz.
* 10 GB pojemności.
* Replikacja globalna jest dostępna w następujących [regionach platformy Azure: Środkowe](https://azure.microsoft.com/regions/)stany USA, Europa Północna i Azja Południowo-Wschodnia
* Maksymalna przepustowość 5 K RU/s po zainicjowaniu obsługi administracyjnej na poziomie kontenera.
* Maksymalna przepustowość 20 K RU/s po zainicjowaniu obsługi administracyjnej na poziomie bazy danych.
* Subskrypcje wygasają po 30 dniach i mogą zostać przedłużone do maksymalnie 31 dni.
* Nie można utworzyć biletów pomocy technicznej platformy Azure dla kont usługi Try Usługi Azure Cosmos DB; jednak pomoc techniczna jest dostępna dla subskrybentów z istniejącymi planami pomocy technicznej.

## <a name="set-up-azure-cosmos-db"></a>Konfigurowanie usługi Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Jak zarejestrować się w usłudze Azure Cosmos DB?

Usługa Azure Cosmos DB jest dostępna w witrynie Azure portal. Najpierw zarejestruj się w celu subskrypcji platformy Azure. Po zarejestrowaniu się możesz dodać konto usługi Azure Cosmos DB do subskrypcji platformy Azure.

### <a name="what-is-a-master-key"></a>Co to jest klucz główny?

Klucz główny jest tokenem zabezpieczającym dostęp do wszystkich zasobów dla konta. Osoby z kluczem mają dostęp do odczytu i zapisu do wszystkich zasobów na koncie bazy danych. Należy zachować ostrożność podczas dystrybucji kluczy głównych. Podstawowy klucz główny i pomocniczy klucz główny są dostępne w bloku **Klucze** [witryny Azure Portal][azure-portal]. Aby uzyskać więcej informacji na temat kluczy, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>W jakich regionach można ustawić preferredlocations?

Wartość PreferredLocations można ustawić na dowolny z regionów platformy Azure, w których usługa Cosmos DB jest dostępna. Aby uzyskać listę dostępnych regionów, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Czy jest coś, o czym powinienem wiedzieć podczas dystrybucji danych na całym świecie za pośrednictwem centrów danych platformy Azure?

Usługa Azure Cosmos DB jest obecna we wszystkich regionach platformy Azure, zgodnie z określoną na stronie [regiony platformy Azure.](https://azure.microsoft.com/regions/) Ponieważ jest to usługa podstawowa, każde nowe centrum danych ma obecność usługi Azure Cosmos DB.

Po ustawieniu regionu należy pamiętać, że usługa Azure Cosmos DB szanuje chmury suwerenne i rządowe. Oznacza to, że jeśli utworzysz konto w [suwerennym regionie,](https://azure.microsoft.com/global-infrastructure/)nie możesz replikować z tego [suwerennego regionu](https://azure.microsoft.com/global-infrastructure/). Podobnie nie można włączyć replikacji do innych lokalizacji suwerennych z konta zewnętrznego.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Czy można przełączyć się z obsługi administracyjnej na poziomie kontenera do inicjowania obsługi administracyjnej na poziomie bazy danych? Lub odwrotnie

Inicjowanie obsługi administracyjnej na poziomie kontenera i bazy danych są oddzielnymi ofertami, a przełączanie między jednym z nich wymaga migracji danych ze źródła do miejsca docelowego. Oznacza to, że musisz utworzyć nową bazę danych lub nowy kontener, a następnie przeprowadzić migrację danych przy użyciu [biblioteki zbiorczego executora](bulk-executor-overview.md) lub [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Czy usługa Azure CosmosDB obsługuje analizę szeregów czasowych?

Tak Usługa Azure CosmosDB obsługuje analizę szeregów czasowych, oto przykład [wzorca szeregów czasowych.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns) W tym przykładzie pokazano, jak używać źródła danych do tworzenia zagregowanych widoków za pomocą danych szeregów czasowych. Tę te podejście można rozszerzyć przy użyciu przesyłania strumieniowego iskrowego lub innego procesora danych strumienia.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Jakie są przydziały usług usługi Usługi Azure Cosmos DB i limity przepływności

Aby uzyskać więcej informacji, zobacz [przydziały usługi usługi](concepts-limits.md) Usługi Azure Cosmos DB i limity na artykuły [kontenera i bazy danych.](set-throughput.md#comparison-of-models)

## <a name="sql-api"></a>Interfejs API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Jak rozpocząć tworzenie interfejsu API SQL?

Najpierw musisz zarejestrować się w celu subskrypcji platformy Azure. Po zarejestrowaniu się w ramach subskrypcji platformy Azure można dodać kontener interfejsu API SQL do subskrypcji platformy Azure. Aby uzyskać instrukcje dotyczące dodawania konta usługi Azure Cosmos DB, zobacz [Tworzenie konta bazy danych usługi Azure Cosmos.](create-sql-api-dotnet.md#create-account)

[Zestawy SDK](sql-api-sdk-dotnet.md) są dostępne dla języków .NET, Python, Node.js, JavaScript i Java. Deweloperzy mogą również używać [interfejsów API HTTP restful](/rest/api/cosmos-db/) do interakcji z zasobami usługi Azure Cosmos DB z różnych platform i języków.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Czy mogę uzyskać dostęp do gotowych próbek, aby uzyskać przewagę?

Przykłady zestawów SDK INTERFEJSU API SQL [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)i [Python](sql-api-python-samples.md) są dostępne w usłudze GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Czy baza danych interfejsu API SQL obsługuje dane wolne od schematu?

Tak, interfejs API SQL umożliwia aplikacjom przechowywanie dowolnych dokumentów JSON bez definicji schematu lub wskazówek. Dane są natychmiast dostępne dla kwerendy za pośrednictwem interfejsu zapytań SQL usługi Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>Czy interfejs API SQL obsługuje transakcje ACID?

Tak, interfejs API SQL obsługuje transakcje między dokumentami wyrażone jako procedury i wyzwalacze przechowywane w języku JavaScript. Transakcje są ograniczone do jednej partycji w każdym kontenerze i wykonywane z semantyki ACID jako "wszystko lub nic", odizolowane od innych jednocześnie wykonywania kodu i żądań użytkownika. Jeśli wyjątki są generowane za pośrednictwem wykonywania po stronie serwera kodu aplikacji JavaScript, cała transakcja jest przywracana. 

### <a name="what-is-a-container"></a>Co to jest kontener?

Kontener to grupa dokumentów i skojarzona z nimi logika aplikacji JavaScript. Kontener jest jednostką podlegającej rozliczaniu, gdzie [koszt](performance-levels.md) jest określany przez przepływność i używany magazyn. Kontenery mogą obejmować jedną lub więcej partycji lub serwerów i można skalować do obsługi praktycznie nieograniczoną ilość pamięci masowej lub przepływności.

* W przypadku interfejsu API SQL kontener jest mapowy do kontenera.
* W przypadku interfejsu API usługi Cosmos DB dla kont MongoDB kontener jest mapowy do kolekcji.
* W przypadku kont interfejsu API cassandra i tabel kontener jest mapowy do tabeli.
* W przypadku kont interfejsu API gremlin kontener jest mapowy do wykresu.

Kontenery są również jednostkami rozliczeniowymi dla usługi Azure Cosmos DB. Każdy kontener jest rozliczany co godzinę, na podstawie aprowizowanej przepływności i używanego miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz [Cennik bazy danych Usługi Azure Cosmos.](https://azure.microsoft.com/pricing/details/cosmos-db/)

### <a name="how-do-i-create-a-database"></a>Jak utworzyć bazę danych?

Bazy danych można tworzyć za pomocą [portalu Azure](https://portal.azure.com), zgodnie z [opisem](create-sql-api-java.md#add-a-container)w add a container , jeden z [zestawów SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md)lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Jak skonfigurować użytkowników i uprawnienia?

Użytkownicy i uprawnienia można tworzyć przy użyciu jednego z [zestawy SDK interfejsu API usługi Cosmos DB](sql-api-sdk-dotnet.md) lub [interfejsów API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Czy interfejs API SQL obsługuje sql?

Język zapytań SQL obsługiwany przez konta interfejsu API SQL jest rozszerzonym podzbiorem funkcji kwerendy obsługiwanej przez program SQL Server. Język zapytań SQL usługi Azure Cosmos DB zapewnia zaawansowane hierarchiczne i relacyjne operatory oraz rozszerzalność za pośrednictwem funkcji zdefiniowanych przez użytkownika (UDF) opartych na języku JavaScript. Gramatyka JSON umożliwia modelowanie dokumentów JSON jako drzew z oznaczonymi węzłami, które są używane zarówno przez techniki automatycznego indeksowania usługi Azure Cosmos DB, jak i dialekt zapytań SQL usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące używania gramatyki SQL, zobacz artykuł [dotyczący kwerendy SQL.][query]

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Czy interfejs API SQL obsługuje funkcje agregacji SQL?

Interfejs API SQL obsługuje agregację o małym `COUNT`opóźnieniu w dowolnej skali za pomocą funkcji agregujących `MIN`, , `MAX` `AVG`, i `SUM` za pośrednictwem gramatyki SQL. Aby uzyskać więcej informacji, zobacz [Agregowanie funkcji](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>W jaki sposób interfejs API SQL zapewnia współbieżność?

Interfejs API SQL obsługuje optymistyczną kontrolę współbieżności (OCC) za pośrednictwem tagów jednostek HTTP lub tagów ETags. Każdy zasób interfejsu API SQL ma etag, a etag jest ustawiany na serwerze za każdym razem, gdy dokument jest aktualizowany. Nagłówek ETag i bieżąca wartość są uwzględniane we wszystkich komunikatach odpowiedzi. Tagi ETags mogą być używane z nagłówkiem If-Match, aby umożliwić serwerowi podjęcie decyzji, czy zasób powinien zostać zaktualizowany. Wartość If-Match jest wartością ETag, która ma być zaznaczona. Jeśli wartość ETag jest zgodna z wartością ETag serwera, zasób jest aktualizowany. Jeśli etag nie jest już aktualny, serwer odrzuca operację z kodem odpowiedzi "Błąd warunku wstępnego HTTP 412". Następnie klient ponownie pobiera zasób w celu uzyskania bieżącej wartości ETag dla zasobu. Ponadto ETags może służyć z if-None-Match nagłówka, aby ustalić, czy refetch zasobu jest potrzebne.

Aby użyć optymistycznej współbieżności w .NET, należy użyć [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klasy. W przypadku przykładu .NET zobacz [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) w przykładzie DocumentManagement w usłudze GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Jak wykonać transakcje w interfejsie API SQL?

Interfejs API SQL obsługuje transakcje zintegrowane z językiem za pośrednictwem procedur i wyzwalaczy przechowywanych w języku JavaScript. Wszystkie operacje bazy danych wewnątrz skryptów są wykonywane w izolacji migawki. Jeśli jest to kontener z jedną partycją, wykonanie jest ograniczone do kontenera. Jeśli kontener jest podzielony na partycje, wykonanie jest ograniczone do dokumentów o tej samej wartości klucza partycji w kontenerze. Migawka wersji dokumentów (elementy ETag) jest wykonywana na początku transakcji i zatwierdzana tylko wtedy, gdy działanie skryptu zakończy się pomyślnie. Jeśli kod JavaScript zwraca błąd, transakcja zostaje wycofana. Aby uzyskać więcej informacji, zobacz [Programowanie javascript po stronie serwera dla usługi Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Jak mogę wstawić dokumenty zbiorczo do usługi Cosmos DB?

Dokumenty można zbiorczo wstawiać do usługi Azure Cosmos DB w jeden z następujących sposobów:

* Narzędzie do wykonywania zbiorczego, zgodnie [z opisem w using bulk executor .NET library](bulk-executor-dot-net.md) and [Using bulk executor Java library](bulk-executor-java.md)
* Narzędzie do migracji danych, zgodnie z opisem w [narzędziu migracji bazy danych dla usługi Azure Cosmos DB](import-data.md).
* Procedury przechowywane, zgodnie z opisem w [programie JavaScript po stronie serwera dla usługi Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Czy interfejs API SQL obsługuje buforowanie łączy zasobów?

Tak, ponieważ usługa Azure Cosmos DB jest usługą RESTful, łącza zasobów są niezmienne i mogą być buforowane. Klienci interfejsu API SQL można określić nagłówek "If-None-Match" dla odczytów względem dowolnego dokumentu lub kontenera podobnego do zasobu, a następnie zaktualizować swoje kopie lokalne po zmianie wersji serwera.

### <a name="is-a-local-instance-of-sql-api-available"></a>Czy lokalne wystąpienie interfejsu API SQL jest dostępne?

Tak. [Emulator usługi Azure Cosmos DB](local-emulator.md) zapewnia emulację wysokiej wierności usługi Usługi Usługi DB usługi. Obsługuje funkcje, które są identyczne z usługi Azure Cosmos DB, w tym obsługę tworzenia i wykonywania zapytań dokumentów JSON, inicjowania obsługi administracyjnej i skalowania kolekcji oraz wykonywania procedur przechowywanych i wyzwalaczy. Można tworzyć i testować aplikacje przy użyciu emulatora usługi Azure Cosmos DB i wdrażać je na platformie Azure w skali globalnej, dokonując jednej zmiany konfiguracji w punkcie końcowym połączenia dla usługi Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Dlaczego są długie wartości zmiennoprzecinkowe w dokumencie zaokrąglone podczas wyświetlania z eksploratora danych w portalu.

Jest to ograniczenie JavaScript. JavaScript używa podwójnej precyzji zmiennoprzecinkowych liczb formatowych określonych w IEEE 754 i może bezpiecznie pomieścić numery między -(2<sup>53</sup> - 1) i 2<sup>53</sup>-1 (tj. 9007199254740991) tylko.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Gdzie są dozwolone uprawnienia w hierarchii obiektów?

Tworzenie uprawnień przy użyciu ResourceTokens jest dozwolone na poziomie kontenera i jego elementów podrzędnych (takich jak dokumenty, załączniki). Oznacza to, że próba utworzenia uprawnienia w bazie danych lub na poziomie konta nie jest obecnie dozwolona.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Co to jest interfejs API usługi Azure Cosmos DB dla mongodb?

Interfejs API usługi Azure Cosmos DB dla mongodb to warstwa zgodności protokołu przewodowego, która umożliwia aplikacjom łatwą i przejrzystą komunikację z natywnym aparatem bazy danych usługi Azure Cosmos przy użyciu istniejących zestawów SDK obsługiwanych przez społeczność i sterowników dla usługi MongoDB. Deweloperzy mogą teraz używać istniejących narzędzi MongoDB i umiejętności do tworzenia aplikacji, które korzystają z usługi Azure Cosmos DB. Deweloperzy korzystają z unikatowych możliwości usługi Azure Cosmos DB, które obejmują dystrybucję globalną z replikacją wielopanową, automatyczne indeksowanie, konserwację kopii zapasowych, umowy dotyczące poziomu usług (SLA) zabezpieczone finansowo itp.

### <a name="how-do-i-connect-to-my-database"></a>Jak połączyć się z bazą danych?

Najszybszym sposobem na połączenie się z bazą danych usługi Cosmos za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB jest przekazanie witryny [Azure portal.](https://portal.azure.com) Przejdź do swojego konta, a następnie w lewym menu nawigacyjnym kliknij pozycję **Szybki start**. Szybki start to najlepszy sposób na uzyskanie fragmentów kodu w celu połączenia się z bazą danych.

Usługa Azure Cosmos DB wymusza ścisłe wymagania i standardy zabezpieczeń. Konta usługi Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu TLS, dlatego należy używać usługi TLSv1.2.

Aby uzyskać więcej informacji, zobacz [Łączenie się z bazą danych usługi Cosmos za pomocą interfejsu API usługi Azure Cosmos DB dla usługi MongoDB.](connect-mongodb-account.md)

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Czy istnieją dodatkowe kody błędów, z którymi muszę sobie poradzić podczas korzystania z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB?

Wraz ze wspólnymi kodami błędów MongoDB interfejs API usługi Azure Cosmos DB dla mongodb ma własne specyficzne kody błędów:

| Błąd               | Code  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba jednostek żądań zużytych jest więcej niż aprowizowana stawka jednostkowa żądania dla kontenera i została ograniczona. | Należy rozważyć skalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z witryny Azure portal lub ponowne próby. |
| ExceededMemoryLimit | 16501 | Jako usługa wielodostępna operacja przeszła przez przydział pamięci klienta. | Zmniejsz zakres operacji za pomocą bardziej restrykcyjnych kryteriów kwerendy lub skontaktuj się z pomocą techniczną z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br><em> &nbsp; &nbsp;Przykład: &nbsp; &nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {nazwa: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {wiek: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Czy sterownik Simba dla mongodb jest obsługiwany do użytku z interfejsem API usługi Azure Cosmos DB dla mongodb?

Tak, możesz użyć sterownika Mongo ODBC Simby z interfejsem API usługi Azure Cosmos DB dla MongoDB

## <a name="table-api"></a><a id="table"></a>Interfejs API tabel

### <a name="how-can-i-use-the-table-api-offering"></a>Jak korzystać z oferty interfejsu API tabeli?

Interfejs API tabeli usługi Azure Cosmos DB jest dostępny w [witrynie Azure portal][azure-portal]. Najpierw musisz zarejestrować się w celu subskrypcji platformy Azure. Po zarejestrowaniu się możesz dodać konto interfejsu API tabeli usługi Azure Cosmos DB do subskrypcji platformy Azure, a następnie dodać tabele do konta.

Obsługiwane języki i skojarzone szybkie uruchamianie można znaleźć w [interfejsie API tabeli usługi Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Czy do korzystania z interfejsu API tabeli potrzebny jest nowy sdk?

Nie, istniejące skuszki SDK magazynu powinny nadal działać. Jednak zaleca się, że zawsze otrzymuje najnowsze SDK dla najlepszej obsługi i w wielu przypadkach najwyższej wydajności. Zobacz listę dostępnych języków w [interfejsie API tabeli usługi Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Dlaczego interfejs API tabel nie zachowuje się tak samo jak usługa Azure Table Storage?

Istnieją pewne różnice w zachowaniu, które użytkownicy pochodzący z usługi Azure Table Storage, którzy chcą tworzyć tabele za pomocą interfejsu API tabeli usługi Azure Cosmos DB, powinni być świadomi:

* Interfejs API tabeli usługi Azure Cosmos DB używa modelu pojemności zarezerwowanej w celu zapewnienia gwarantowanej wydajności, ale oznacza to, że płaci się za pojemność, gdy tylko tabela zostanie utworzona, nawet jeśli pojemność nie jest używana. W przypadku usługi Azure Table Storage jeden płaci tylko za używaną pojemność. Pomaga to wyjaśnić, dlaczego interfejs API tabel może oferować 10 ms odczytu i 15 ms zapisu SLA na 99 percentyl, podczas gdy usługa Azure Table Storage oferuje 10-sekundową umowy SLA. Jednak w związku z tym z tabel interfejsu API tabel, nawet puste tabele bez żadnych żądań, koszt, aby upewnić się, że pojemność jest dostępna do obsługi wszelkich żądań do nich w ramach umowy SLA oferowanych przez usługę Azure Cosmos DB.
* Wyniki kwerendy zwracane przez interfejs API tabeli nie są sortowane w kolejności klucza partycji/wiersza, ponieważ znajdują się w magazynie tabel platformy Azure.
* Klawisze wierszy mogą mieć maksymalnie 255 bajtów
* Partie mogą mieć tylko 2 MB
* CORS nie jest obecnie obsługiwany
* W nazwach tabel w magazynie tabel platformy Azure nie jest rozróżniana wielkość liter, ale są one w interfejsie API tabeli usługi Azure Cosmos DB
* Niektóre wewnętrzne formaty usługi Azure Cosmos DB dla informacji o kodowaniu, takie jak pola binarne, nie są obecnie tak wydajne, jak mogłoby się wydawać. W związku z tym może to spowodować nieoczekiwane ograniczenia rozmiaru danych. Na przykład obecnie nie można użyć pełnego Meg jednostki tabeli do przechowywania danych binarnych, ponieważ kodowanie zwiększa rozmiar danych.
* Nazwa właściwości encji "Identyfikator" obecnie nie jest obsługiwana
* Liczba pochówek dokonujyć jest ograniczona do 1000

Pod względem interfejsu API REST istnieje wiele punktów końcowych/opcji kwerendy, które nie są obsługiwane przez interfejs API tabeli usługi Azure Cosmos DB:

| Metoda(-y) odpoczynku | Opcja punktu końcowego/kwerendy | Adresy URL doc | Wyjaśnienie |
| ------------| ------------- | ---------- | ----------- |
| POBIERZ, UMIEŚĆ | /?restype=service@comp=właściwości| [Ustawianie właściwości usługi tabel](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) i [pobierz właściwości usługi tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ten punkt końcowy służy do ustawiania reguł CORS, konfiguracji analizy magazynu i ustawień rejestrowania. Usługa CORS nie jest obecnie obsługiwana, a analizy i rejestrowanie są obsługiwane w usłudze Azure Cosmos DB inaczej niż w witrynie Azure Storage Tables |
| Opcje | /\<> nazwa zasobu tabeli | [Przed lotem żądanie tabeli CORS](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Jest to część usługi CORS, której usługa Azure Cosmos DB obecnie nie obsługuje. |
| GET | /?restype=service@comp=statystyki | [Pobierz statystyki usługi tabel](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Zawiera informacje, jak szybko dane są replikowane między podstawowymi i pomocniczymi. Nie jest to potrzebne w usłudze Cosmos DB, ponieważ replikacja jest częścią zapisów. |
| POBIERZ, UMIEŚĆ | /mytable?comp=acl | [Pobierz acl tabeli](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) i [ustaw tabelę ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Spowoduje to oś dostępu do przechowywanego dostępu używane do zarządzania sygnaturami dostępu współdzielonego(SAS). Mimo że sygnatury dostępu Współdzielonego jest obsługiwany, są one ustawione i zarządzane inaczej. |

Ponadto interfejs API tabeli usługi Azure Cosmos DB obsługuje tylko format JSON, a nie ATOM.

Usługa Azure Cosmos DB obsługuje podpisy dostępu współdzielonego (SAS) istnieją pewne zasady, których nie obsługuje, w szczególności te związane z operacjami zarządzania, takimi jak prawo do tworzenia nowych tabel.

W szczególności dla pliku .NET SDK istnieją pewne klasy i metody, których usługa Azure Cosmos DB obecnie nie obsługuje.

| Klasa | Metoda nieobsługiwała |
|-------|-------- |
| CloudTableClient (CloudTableClient) | \*Właściwości usługi* |
|                  | \*ServiceStats* |
| CloudTable (Tabela chmur) | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext (TableServiceContext) | * (ta klasa jest przestarzała) |
| Usługa usługi stołowej | " " |
| TableServiceExtensions (Usługi stołowe) | " " |
| Zapytanie usługi tableservicequery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak przekazać opinię na temat SDK lub błędów?

Możesz podzielić się swoją opinią w dowolny z następujących sposobów:

* [Głos użytkownika](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Przepełnienie stosu jest najlepsze dla pytań programowania. Upewnij się, że twoje pytanie jest [na temat](https://stackoverflow.com/help/on-topic) i podać jak [najwięcej szczegółów, jak to możliwe, dzięki czemu pytanie jasne i odpowiadać](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jaki jest ciąg połączenia, którego muszę użyć, aby połączyć się z interfejsem API tabeli?

Parametry połączenia to:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Ciąg połączenia można uzyskać ze strony Parametry połączenia w witrynie Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak zastąpić ustawienia konfiguracji dla opcji żądania w pliku .NET SDK dla interfejsu API tabeli?

Niektóre ustawienia są obsługiwane w CreateCloudTableClient metody i inne za pośrednictwem app.config w appSettings sekcji w aplikacji klienckiej. Aby uzyskać informacje o ustawieniach konfiguracji, zobacz [Możliwości usługi Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Czy są jakieś zmiany dla klientów, którzy korzystają z istniejących zestawów SDK magazynu tabel platformy Azure?

Brak. Nie ma żadnych zmian dla istniejących lub nowych klientów, którzy korzystają z istniejących zestawów SDK magazynu tabel platformy Azure.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak wyświetlić dane tabeli przechowywane w usłudze Azure Cosmos DB do użytku z interfejsem API tabeli?

Za pomocą witryny Azure portal można przeglądać dane. Można również użyć kodu interfejsu API tabeli lub narzędzi wymienionych w następnej odpowiedzi.

### <a name="which-tools-work-with-the-table-api"></a>Które narzędzia współpracują z interfejsem API tabeli?

Można użyć [Eksploratora usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Narzędzia z elastycznością do podjęcia ciągu połączenia w formacie określonym wcześniej może obsługiwać nowy interfejs API tabeli. Lista narzędzi tabel znajduje się na stronie [Narzędzia klienta usługi Azure Storage.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>Czy współbieżność operacji jest kontrolowana?

Tak, optymistyczna współbieżność jest dostarczana za pomocą mechanizmu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Czy model kwerendy OData jest obsługiwany dla jednostek?

Tak, interfejs API tabeli obsługuje kwerendę OData i kwerendę LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Czy mogę połączyć się z usługi Azure Table Storage i interfejsu API tabeli usługi Azure Cosmos DB obok siebie w tej samej aplikacji?

Tak, można połączyć, tworząc dwa oddzielne wystąpienia CloudTableClient, z których każdy wskazuje na własny identyfikator URI za pośrednictwem ciągu połączenia.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak przeprowadzić migrację istniejącej aplikacji usługi Azure Table Storage do tej oferty?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) i [Narzędzia migracji danych usługi Azure Cosmos DB](import-data.md) są obsługiwane.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak jest rozszerzenie rozmiaru magazynu dla tej usługi, jeśli, na przykład, zacznę od *n* GB danych i moje dane będą rosnąć do 1 TB w czasie?

Usługa Azure Cosmos DB została zaprojektowana w celu zapewnienia nieograniczonej przestrzeni dyskowej za pomocą skalowania poziomego. Usługa może monitorować i skutecznie zwiększać ilość pamięci masowej.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak monitorować ofertę interfejsu API tabeli?

Okienko **Metryki interfejsu** API tabeli służy do monitorowania żądań i użycia magazynu.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Jak obliczyć wymagany przepływność?

Estymator pojemności służy do obliczania TableThroughput, który jest wymagany dla operacji. Aby uzyskać więcej informacji, zobacz [Szacowanie jednostek żądań i magazyn danych](https://www.documentdb.com/capacityplanner). Ogólnie rzecz biorąc można wyświetlić jednostki jako JSON i podać numery dla operacji.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Czy można używać SDK interfejsu API tabeli lokalnie z emulatorem?

Nie w tej chwili.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Czy moja istniejąca aplikacja może pracować z interfejsem API tabeli?

Tak, ten sam interfejs API jest obsługiwany.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Czy muszę przeprowadzić migrację istniejących aplikacji magazynu tabel platformy Azure do zestawu SDK, jeśli nie chcę używać funkcji interfejsu API tabel?

Nie, można tworzyć i używać istniejących zasobów magazynu tabel platformy Azure bez żadnych przerw. Jeśli jednak nie używasz interfejsu API tabeli, nie możesz korzystać z indeksu automatycznego, opcji dodatkowej spójności ani dystrybucji globalnej.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak dodać replikację danych w interfejsie API tabeli w więcej niż jednym regionie platformy Azure?

Globalne [ustawienia replikacji](tutorial-global-distribution-sql-api.md#portal) portalu usługi Azure Cosmos DB umożliwiają dodawanie regionów odpowiednich dla danej aplikacji. Aby opracować globalnie rozproszoną aplikację, należy również dodać aplikację z informacjami PreferredLocation ustawionymi w regionie lokalnym w celu zapewnienia małych opóźnień odczytu.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak zmienić podstawowy region zapisu dla konta w interfejsie API tabeli?

Za pomocą okienka portalu replikacji globalnej usługi Azure Cosmos DB można dodać region, a następnie przejść w stan fail over do wymaganego regionu. Aby uzyskać instrukcje, zobacz [Tworzenie za pomocą wieloregionowych kont usługi Azure Cosmos DB.](high-availability.md)

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak skonfigurować preferowane regiony odczytu pod kątem małych opóźnień podczas dystrybucji danych?

Aby ułatwić czytanie z lokalizacji lokalnej, użyj klucza PreferredLocation w pliku app.config. W przypadku istniejących aplikacji interfejs API tabeli zgłasza błąd, jeśli ustawiono tryb LocationMode. Usuń ten kod, ponieważ interfejs API tabeli pobiera te informacje z pliku app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak należy myśleć o poziomach spójności w interfejsie API tabeli?

Usługa Azure Cosmos DB zapewnia dobrze uzasadnione kompromisy między spójnością, dostępnością i opóźnieniami. Usługa Azure Cosmos DB oferuje pięć poziomów spójności do deweloperów interfejsu API tabeli, dzięki czemu można wybrać odpowiedni model spójności na poziomie tabeli i wysyłać indywidualne żądania podczas wykonywania zapytań o dane. Gdy klient łączy, można określić poziom spójności. Można zmienić poziom za pomocą konsystencjiPoziomowy argument CreateCloudTableClient.

Interfejs API tabeli zapewnia odczyty o małym opóźnieniu z "Odczytuj własne zapisy", z spójność ograniczone-nieaktualność jako domyślna. Aby uzyskać więcej informacji, zobacz [Poziomy spójności](consistency-levels.md).

Domyślnie usługa Azure Table Storage zapewnia silną spójność w regionie i spójność ostateczną w lokalizacjach pomocniczych.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Czy interfejs API tabel usługi Azure Cosmos DB oferuje więcej poziomów spójności niż magazyn tabel platformy Azure?

Tak, aby uzyskać informacje dotyczące sposobu korzystania z rozproszonego charakteru usługi Azure Cosmos DB, zobacz [poziomy spójności](consistency-levels.md). Ponieważ gwarancje są przewidziane dla poziomów spójności, można ich używać z ufnością.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Kiedy dystrybucja globalna jest włączona, jak długo trwa replikacja danych?

Usługa Azure Cosmos DB zatwierdza dane trwale w regionie lokalnym i wypycha dane do innych regionów natychmiast w ciągu milisekund. Ta replikacja zależy tylko od czasu podróży w obie strony (RTT) centrum danych. Aby dowiedzieć się więcej o możliwościie dystrybucji globalnej usługi Azure Cosmos DB, zobacz [Usługa Azure Cosmos DB: Globalnie rozproszona usługa baz danych na platformie Azure.](distribute-data-globally.md)

### <a name="can-the-read-request-consistency-level-be-changed"></a>Czy można zmienić poziom spójności żądania odczytu?

Za pomocą usługi Azure Cosmos DB można ustawić poziom spójności na poziomie kontenera (w tabeli). Za pomocą .NET SDK, można zmienić poziom, podając wartość tableconsistencyLevel klucz w pliku app.config. Możliwe wartości to: Silna, Ograniczona nieaktualność, Sesja, Spójny prefiks i Ostateczny. Aby uzyskać więcej informacji, zobacz [Poziomy spójności danych przestrajalne w usłudze Azure Cosmos DB](consistency-levels.md). Kluczową ideą jest to, że nie można ustawić poziom spójności żądania na więcej niż ustawienie dla tabeli. Na przykład nie można ustawić poziom spójności dla tabeli w Ostateczna i poziom spójności żądania w Strong.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak interfejs API tabeli obsługuje przemija po awarii, jeśli region ustępuje?

Interfejs API tabel wykorzystuje globalnie rozproszoną platformę usługi Azure Cosmos DB. Aby upewnić się, że aplikacja może tolerować przestoje centrum danych, włącz co najmniej jeden region dla konta w portalu usługi Azure Cosmos DB [Developing z wieloregionowymi kontami usługi Azure Cosmos DB.](high-availability.md) Priorytet regionu można ustawić za pomocą portalu [Programowanie z wieloregionowymi kontami usługi Azure Cosmos DB](high-availability.md).

Można dodać dowolną liczbę regionów dla konta i kontrolować, gdzie można awaryjnie, podając priorytet pracy awaryjnej. Aby korzystać z bazy danych, należy podać aplikację tam też. Gdy to zrobisz, twoi klienci nie doświadczą przestojów. [Najnowszy sdk klienta platformy .NET](table-sdk-dotnet.md) jest automatycznym tworzeniem macierzy, ale inne skomuny SDK nie są. Oznacza to, że może wykryć region, który jest w dół i automatycznie awaryjnie do nowego regionu.

### <a name="is-the-table-api-enabled-for-backups"></a>Czy interfejs API tabeli jest włączony dla kopii zapasowych?

Tak, interfejs API tabel wykorzystuje platformę usługi Azure Cosmos DB do tworzenia kopii zapasowych. Kopie zapasowe są automatycznie. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie w trybie online za pomocą usługi Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Czy interfejs API tabeli domyślnie indeksuje wszystkie atrybuty encji?

Tak, wszystkie atrybuty encji są indeksowane domyślnie. Aby uzyskać więcej informacji, zobacz [Usługi Azure Cosmos DB: Zasady indeksowania](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Czy to oznacza, że nie muszę tworzyć więcej niż jeden indeks, aby spełnić zapytania?

Tak, interfejs API tabeli usługi Azure Cosmos DB zapewnia automatyczne indeksowanie wszystkich atrybutów bez definicji schematu. Ta automatyzacja zwalnia deweloperów skupić się na aplikacji, a nie na tworzenie indeksu i zarządzania. Aby uzyskać więcej informacji, zobacz [Usługi Azure Cosmos DB: Zasady indeksowania](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Czy mogę zmienić zasady indeksowania?

Tak, można zmienić zasady indeksowania, podając definicję indeksu. Musisz prawidłowo zakodować i uciec z ustawień.

W przypadku non-.NET zestawów SDK zasady indeksowania można ustawić tylko w portalu w **Eksploratorze danych**, przejdź do określonej tabeli, którą chcesz zmienić, a następnie przejdź do **zasad skalowania &**->indeksowania, dokonaj żądanej zmiany, a następnie **zapisz**.

Z pliku .NET SDK można go przesłać w pliku app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Usługa Azure Cosmos DB jako platforma wydaje się mieć wiele możliwości, takich jak sortowanie, agregacje, hierarchia i inne funkcje. Czy dodasz te możliwości do interfejsu API tabeli?

Interfejs API tabeli udostępnia te same funkcje kwerendy co magazyn tabel platformy Azure. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Aby uzyskać więcej informacji, zobacz [kwerendy SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kiedy należy zmienić TableThroughput dla interfejsu API tabeli?

Należy zmienić TableThroughput, gdy ma zastosowanie jeden z następujących warunków:

* Wykonujesz wyodrębnianie, przekształcanie i ładowanie (ETL) danych lub chcesz przekazać dużo danych w krótkim czasie.
* Potrzebujesz więcej przepływności z kontenera lub z zestawu kontenerów na zapleczu. Na przykład widać, że używana przepływność jest więcej niż aprowizowana przepływność i otrzymujesz ograniczanie. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności kontenerów usługi Azure Cosmos.](set-throughput.md)

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Czy można skalować w górę lub w dół przepływność mojej tabeli interfejsu API tabeli tabeli tabeli?

Tak, do skalowania przepływności można użyć okienka skalowania portalu usługi Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Czy domyślny zestaw TableThroughput dla nowo aprowizowanych tabel?

Tak, jeśli nie zastąpisz TableThroughput za pośrednictwem app.config i nie używasz wstępnie utworzonego kontenera w usłudze Azure Cosmos DB, usługa tworzy tabelę z przepływnością 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Czy istnieje jakakolwiek zmiana cen dla istniejących klientów usługi magazynu tabel platformy Azure?

Brak. Nie ma żadnych zmian w cenie dla istniejących klientów usługi Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak jest obliczana cena dla interfejsu API tabeli?

Cena zależy od przydzielonego TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak obsługiwać wszelkie ograniczenia szybkości w tabelach w ofercie interfejsu API tabeli?

Jeśli szybkość żądania jest większa niż pojemność aprowizowanej przepływności dla kontenera źródłowego lub zestaw kontenerów, pojawia się błąd, a zestaw SDK ponawia próbę wywołania, stosując zasady ponawiania.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Dlaczego muszę wybrać przepływność oprócz PartitionKey i RowKey, aby skorzystać z oferty interfejsu API tabel usługi Azure Cosmos DB?

Usługa Azure Cosmos DB ustawia domyślną przepływność kontenera, jeśli nie podasz jej w pliku app.config lub za pośrednictwem portalu.

Usługa Azure Cosmos DB zapewnia gwarancje wydajności i opóźnienia, z górnymi granicami w operacji. Ta gwarancja jest możliwe, gdy aparat może wymusić zarządzanie w operacjach dzierżawy. Ustawienie TableThroughput zapewnia uzyskanie gwarantowanej przepływności i opóźnienia, ponieważ platforma rezerwuje tę pojemność i gwarantuje sukces operacyjny.

Korzystając ze specyfikacji przepływności, można elastycznie zmienić go, aby korzystać z sezonowości aplikacji, spełniają potrzeby przepływności i zaoszczędzić koszty.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Usługa Azure Table Storage jest dla mnie niedroga, ponieważ płacę tylko za przechowywanie danych i rzadko wysyłam zapytania. Usługa Azure Cosmos DB Table API oferuje wydaje się być ładowania mnie, mimo że nie wykonałem pojedynczej transakcji lub przechowywane niczego. Czy możesz wyjaśnić?

Usługa Azure Cosmos DB została zaprojektowana jako globalnie rozproszony system oparty na ramach umowy SLA z gwarancjami dostępności, opóźnienia i przepływności. Po zarezerwowania przepływności w usłudze Azure Cosmos DB, jest gwarantowana, w przeciwieństwie do przepływności innych systemów. Usługa Azure Cosmos DB zapewnia dodatkowe funkcje, o które zażądali klienci, takie jak indeksy pomocnicze i dystrybucja globalna.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nigdy nie otrzymuję pełnego przydziału" powiadomienia (wskazującego, że partycja jest pełna) podczas pozyskiwania danych do magazynu tabel platformy Azure. Za pomocą interfejsu API tabeli otrzymuję ten komunikat. Czy ta oferta ogranicza mnie i zmusza mnie do zmiany istniejącej aplikacji?

Usługa Azure Cosmos DB to system oparty na umowy SLA, który zapewnia nieograniczoną skalę, z gwarancjami opóźnienia, przepływności, dostępności i spójności. Aby zapewnić gwarantowaną wydajność w wersji premium, upewnij się, że rozmiar danych i indeks są łatwe w zarządzaniu i skalowalne. Limit 10 GB liczby jednostek lub elementów na klucz partycji ma na celu zapewnienie dużej wydajności wyszukiwania i zapytań. Aby upewnić się, że aplikacja jest dobrze skalowana, nawet dla usługi Azure Storage, zaleca się, aby *nie* tworzyć partycji gorąca, przechowując wszystkie informacje w jednej partycji i kwerendy.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Więc PartitionKey i RowKey są nadal wymagane z interfejsu API tabeli?

Tak. Ponieważ powierzchnia interfejsu API tabeli jest podobna do obszaru SDK magazynu tabel platformy Azure, klucz partycji zapewnia skuteczny sposób dystrybucji danych. Klucz wiersza jest unikatowy w tej partycji. Klucz wiersza musi być obecny i nie może być null, jak w standardowym SDK. Długość RowKey jest 255 bajtów i długość PartitionKey jest 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jakie są komunikaty o błędach dla interfejsu API tabeli?

Usługa Azure Table storage i interfejs API tabel usługi Azure Cosmos DB używają tych samych zestawów SDK, więc większość błędów będzie taka sama.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Dlaczego podczas próby utworzenia wielu tabel jeden po drugim w interfejsie API tabely jest ograniczona?

Usługa Azure Cosmos DB to system oparty na umowy SLA, który zapewnia gwarancje opóźnienia, przepływności, dostępności i spójności. Ponieważ jest to system aprowizacji, rezerwuje zasoby, aby zagwarantować te wymagania. Szybki wskaźnik tworzenia tabel jest wykrywany i ograniczany. Zaleca się przyjrzenie się szybkości tworzenia tabel i obniżenie jej do mniej niż 5 na minutę. Należy pamiętać, że interfejs API tabeli jest systemem aprowizacji. W momencie, gdy go udostępnisz, zaczniesz za to płacić.

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>W przypadku tworzenia języka C#/.NET należy użyć pakietu Microsoft.Azure.Graphs lub Gremlin.NET?

Interfejs API gremlin usługi Azure Cosmos DB wykorzystuje sterowniki typu open source jako główne łączniki usługi. Tak więc zalecaną opcją jest użycie [sterowników obsługiwanych przez Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak są naliczane opłaty ru/s podczas uruchamiania zapytań w bazie danych wykresów?

Wszystkie obiekty wykresu, wierzchołki i krawędzie są wyświetlane jako dokumenty JSON w wewnętrznej wejściu. Ponieważ jedno zapytanie Gremlin może modyfikować jeden lub wiele obiektów wykresu naraz, koszt skojarzony z nim jest bezpośrednio związane z obiektów, krawędzie, które są przetwarzane przez kwerendę. Jest to ten sam proces, który usługa Azure Cosmos DB używa dla wszystkich innych interfejsów API. Aby uzyskać więcej informacji, zobacz [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md).

Opłata RU jest oparta na zestawie danych roboczych przechodzenia, a nie na zestawie wyników. Na przykład jeśli kwerenda ma na celu uzyskanie pojedynczego wierzchołka w wyniku, ale musi przechodzić przez więcej niż jeden inny obiekt na drodze, a następnie koszt będzie oparty na wszystkich obiektów wykresu, które zajmie obliczenie jednego wierzchołka wynik.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaka jest maksymalna skala, jaką może mieć baza danych wykresu w interfejsie API Gremlin usługi Azure Cosmos DB?

Usługa Azure Cosmos DB korzysta z [partycjonowania poziomego,](partition-data.md) aby automatycznie rozwiązać wzrost wymagań dotyczących magazynu i przepływności. Maksymalna przepustowość i pojemność magazynu obciążenia zależy od liczby partycji, które są skojarzone z danym kontenerem. Jednak kontener interfejsu API Gremlin ma określony zestaw wskazówek, aby zapewnić odpowiednie środowisko wydajności na dużą skalę. Aby uzyskać więcej informacji na temat partycjonowania i najlepszych rozwiązań, zobacz [partycjonowanie w artykule usługi Azure Cosmos DB.](partition-data.md)

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak chronić przed atakami iniekcji za pomocą sterowników Gremlin?

Większość natywnych sterowników Apache Tinkerpop Gremlin umożliwia podanie słownika parametrów do wykonywania kwerend. Jest to przykład, jak to zrobić w [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) i [w Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Dlaczego pojawia się błąd "Błąd kompilacji zapytań Gremlin: nie można znaleźć żadnej metody"?

Interfejs API gremlin usługi Azure Cosmos DB implementuje podzbiór funkcji zdefiniowanych w obszarze powierzchni Gremlin. Aby uzyskać obsługiwane kroki i więcej informacji, zobacz artykuł [pomocy technicznej gremlin.](gremlin-support.md)

Najlepszym rozwiązaniem jest przepisanie wymaganych kroków Gremlin z obsługą funkcji, ponieważ wszystkie istotne kroki Gremlin są obsługiwane przez usługę Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Dlaczego pojawia się błąd "WebSocketException: Serwer zwrócił kod stanu "200", gdy oczekiwano kodu stanu "101"?200: The server returned status code '200'when status code '101' was expected" error?

Ten błąd jest prawdopodobnie generowany, gdy używany jest niewłaściwy punkt końcowy. Punkt końcowy, który generuje ten błąd ma następujący wzorzec:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Jest to punkt końcowy dokumentów dla bazy danych wykresu.  Poprawny punkt końcowy do użycia jest punkt końcowy Gremlin, który ma następujący format:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Dlaczego pojawia się błąd "RequestRateIsTooLarge"?

Ten błąd oznacza, że przydzielone jednostki żądań na sekundę nie są wystarczające do obsługi kwerendy. Ten błąd jest zwykle widoczny po uruchomieniu kwerendy, która uzyskuje wszystkie wierzchołki:

```
// Query example:
g.V()
```

Ta kwerenda podejmie próbę pobrania wszystkich wierzchołków z wykresu. Tak więc koszt tej kwerendy będzie równa co najmniej liczbie wierzchołków pod względem procesorów RU. Ustawienie RU/s należy dostosować w celu rozwiązania tej kwerendy.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Dlaczego moje połączenia sterowników Gremlin zostają ostatecznie usunięte?

Połączenie Gremlin odbywa się za pośrednictwem połączenia WebSocket. Mimo że połączenia WebSocket nie mają określonego czasu na żywo, interfejs API gremlin usługi Azure Cosmos DB zakończy bezczynne połączenia po 30 minutach braku aktywności.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Dlaczego nie mogę używać płynnych wywołań interfejsu API w natywnych sterownikach Gremlin?

Płynne wywołania interfejsu API nie są jeszcze obsługiwane przez interfejs API gremlin usługi Azure Cosmos DB. Fluent wywołania interfejsu API wymagają wewnętrznej funkcji formatowania znany jako obsługi kodu bajtowego, który obecnie nie jest obsługiwany przez interfejs API Gremlin usługi Azure Cosmos DB. Z tego samego powodu najnowszy sterownik Gremlin-JavaScript również nie jest obecnie obsługiwany.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Jak mogę ocenić skuteczność moich zapytań Gremlin?

Krok w wersji zapoznawczej **executionProfile()** może służyć do analizy planu wykonania kwerendy. Ten krok należy dodać na końcu dowolnej kwerendy Gremlin, jak pokazano w poniższym przykładzie:

**Przykład kwerendy**

```
g.V('mary').out('knows').executionProfile()
```

**Przykładowe dane wyjściowe**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Dane wyjściowe powyższego profilu pokazują, ile czasu poświęca się na uzyskanie obiektów wierzchołków, obiektów krawędzi i rozmiaru zestawu danych roboczych. Jest to związane z pomiarami kosztów standardowych dla zapytań usługi Azure Cosmos DB.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
