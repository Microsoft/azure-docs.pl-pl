---
title: Często zadawane pytania dotyczące interfejs API tabel w programie Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejs API tabel w programie Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: 77e74a36d7b353d0713a7bf17f7a6341558f0739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485005"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące interfejs API tabel w programie Azure Cosmos DB

Interfejs API tabel Azure Cosmos DB jest dostępny w [Azure Portal](https://portal.azure.com) najpierw należy zarejestrować się w celu uzyskania subskrypcji platformy Azure. Po zarejestrowaniu możesz dodać konto Azure Cosmos DB interfejs API tabel do subskrypcji platformy Azure, a następnie dodać tabele do swojego konta. Obsługiwane języki i skojarzone szybkie uruchomienia można znaleźć w temacie [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>interfejs API tabel w Azure Cosmos DB programu vs Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Dlaczego interfejs API tabel nie zachowuje się tak samo jak usługa Azure Table Storage?

Istnieją pewne różnice w zachowaniu, które użytkownicy pochodzą z usługi Azure Table Storage, którzy chcą tworzyć tabele z Azure Cosmos DB interfejs API tabel powinny mieć świadomość:

* Azure Cosmos DB interfejs API tabel wykorzystuje zastrzeżony model pojemności, aby zapewnić zagwarantowaną wydajność, ale oznacza to, że jeden płaci dla pojemności zaraz po utworzeniu tabeli, nawet jeśli pojemność nie jest używana. Za pomocą usługi Azure Table Storage jest stosowana tylko do używanej pojemności. Pomaga to w wyjaśnieniu, dlaczego interfejs API tabel może oferować 10 ms umów SLA do odczytu i 15 MS w 99 percentylu, podczas gdy usługa Azure Table Storage oferuje 10-sekundową umowę SLA. Ale w związku z tym interfejs API tabel tabele, nawet puste tabele bez żadnych żądań, koszt finansowy w celu zapewnienia, że wydajność jest dostępna do obsługi dowolnych żądań do nich w ramach umowy SLA oferowanej przez Azure Cosmos DB.

* Wyniki zapytania zwrócone przez interfejs API tabel nie są posortowane w kolejności kluczy/wierszy partycji, ponieważ znajdują się w usłudze Azure Table Storage.

* Klucze wierszy mogą zawierać maksymalnie 255 bajtów.

* Partie mogą mieć maksymalnie 2 MB.

* Mechanizm CORS nie jest obecnie obsługiwany.

* W nazwach tabel w usłudze Azure Table Storage nie jest rozróżniana wielkość liter, ale znajdują się one w Azure Cosmos DB interfejs API tabel.

* Niektóre formaty wewnętrzne Azure Cosmos DB dla informacji o kodowaniu, takie jak pola binarne, nie są obecnie takie same jak takie, jak takie. W związku z tym może to spowodować nieoczekiwane ograniczenia rozmiaru danych. Na przykład obecnie nie można użyć pełnej MB jednostki tabeli do przechowywania danych binarnych, ponieważ kodowanie zwiększa rozmiar danych.

* Nazwa właściwości jednostki "ID" jest obecnie nieobsługiwana.

* TableQuery TakeCount nie jest ograniczona do 1000.

* W przypadku interfejsu API REST istnieje kilka punktów końcowych/opcji zapytania, które nie są obsługiwane przez Azure Cosmos DB interfejs API tabel:

  | Metody REST | Punkt końcowy REST/opcja zapytania | Adresy URL doc | Wyjaśnienie |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Ustawianie właściwości usługi Table Service](/rest/api/storageservices/set-table-service-properties) i [pobieranie właściwości usługi Table Service](/rest/api/storageservices/get-table-service-properties) | Ten punkt końcowy służy do ustawiania reguł CORS, konfiguracji analizy magazynu i ustawień rejestrowania. Mechanizm CORS nie jest obecnie obsługiwany, a analiza i rejestrowanie są obsługiwane inaczej w Azure Cosmos DB niż tabele usługi Azure Storage |
  | Opcje | `/<table-resource-name>` | [Żądanie tabeli CORS przed lotem](/rest/api/storageservices/preflight-table-request) | Jest to część mechanizmu CORS, który Azure Cosmos DB obecnie nie jest obsługiwany. |
  | GET | `/?restype=service@comp=stats` | [Pobierz statystyki usługi Table Service](/rest/api/storageservices/get-table-service-stats) | Zawiera informacje o tym, jak szybko dane są replikowane między podstawowymi i pomocniczymi elementami. Nie jest to wymaganie w Cosmos DB, ponieważ replikacja jest częścią operacji zapisu. |
  | GET, PUT | `/mytable?comp=acl` | [Pobieranie listy ACL tabel](/rest/api/storageservices/get-table-acl) i [Ustawianie listy ACL tabel](/rest/api/storageservices/set-table-acl) | Spowoduje to pobranie i ustawienie przechowywanych zasad dostępu używanych do zarządzania sygnaturami dostępu współdzielonego (SAS). Chociaż sygnatura dostępu współdzielonego jest obsługiwana, są one ustawiane i zarządzane w różny sposób. |

* Azure Cosmos DB interfejs API tabel obsługuje tylko format JSON, a nie ATOM.

* Chociaż Azure Cosmos DB obsługuje sygnatury dostępu współdzielonego (SAS), istnieją pewne zasady, które nie są obsługiwane, a w odniesieniu do operacji zarządzania, takich jak prawo do tworzenia nowych tabel.

* W przypadku zestawu .NET SDK w szczególności istnieją klasy i metody, które Azure Cosmos DB nie są obecnie obsługiwane.

  | Klasa | Nieobsługiwana Metoda |
  |-------|-------- |
  | CloudTableClient | \*Właściwości serviceproperties * |
  |                  | \*Servicestatystyka * |
  | CloudTable | SetPermissions * |
  |            | Getpermissionss * |
  | TableServiceContext | * (Ta klasa jest przestarzała) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Inne często zadawane pytania

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Czy potrzebuję nowego zestawu SDK, aby użyć interfejs API tabel?

Nie, istniejące zestawy SDK magazynu powinny nadal funkcjonować. Zaleca się jednak, aby zawsze pobierać najnowsze zestawy SDK dla najlepszej obsługi i w wielu przypadkach o najwyższej wydajności. Zapoznaj się z listą dostępnych języków w artykule [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jakie są parametry połączenia potrzebne do nawiązania połączenia z interfejs API tabel?

Parametry połączenia:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Parametry połączenia można uzyskać ze strony parametrów połączenia w Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Jak mogę zastąpić ustawienia konfiguracji dla opcji żądania w zestawie .NET SDK dla interfejs API tabel?

Niektóre ustawienia są obsługiwane przez metodę CreateCloudTableClient i inne za pośrednictwem app.config w sekcji appSettings w aplikacji klienckiej. Informacje o ustawieniach konfiguracji można znaleźć w temacie [Azure Cosmos DB Capabilities](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Czy istnieją jakieś zmiany klientów korzystających z istniejących zestawów SDK usługi Azure Table Storage?

Brak. Nie wprowadzono żadnych zmian dla istniejących lub nowych klientów korzystających z istniejących zestawów SDK usługi Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Jak mogę przeglądać dane tabeli przechowywane w Azure Cosmos DB do użycia z interfejs API tabelem?

Możesz użyć Azure Portal, aby przeglądać dane. Możesz również użyć kodu interfejs API tabel lub narzędzi wymienionych w następnej odpowiedzi.

### <a name="which-tools-work-with-the-table-api"></a>Które narzędzia współpracują z interfejs API tabel?

Możesz użyć [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Narzędzia z elastycznością do łączenia parametrów połączenia w określonym wcześniej formacie mogą obsługiwać nowe interfejs API tabel. Lista narzędzi tabel jest dostępna na stronie [narzędzia klienta usługi Azure Storage](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Czy współbieżność w działaniu kontrolowanym przez operacje?

Tak, współbieżność optymistyczna jest zapewniana przy użyciu mechanizmu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Czy model zapytania OData jest obsługiwany dla jednostek?

Tak, interfejs API tabel obsługuje zapytania OData i zapytanie LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Czy można połączyć się z usługą Azure Table Storage i Azure Cosmos DB interfejs API tabel obok siebie w tej samej aplikacji?

Tak, możesz nawiązać połączenie, tworząc dwa oddzielne wystąpienia CloudTableClient, z których każdy wskazuje własny identyfikator URI za pośrednictwem parametrów połączenia.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Jak mogę przeprowadzić migrację istniejącej aplikacji usługi Azure Table Storage do tej oferty?

[AzCopy](../storage/common/storage-use-azcopy-v10.md) i [Narzędzie do migracji danych Azure Cosmos DB](import-data.md) są obsługiwane.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak można zwiększyć rozmiar magazynu dla tej usługi, jeśli na przykład zaczynam się od *n* GB danych, a moje dane zostaną rozbudowane na 1 TB w czasie?

Azure Cosmos DB zaprojektowano tak, aby zapewnić nieograniczony magazyn przy użyciu skalowania w poziomie. Usługa umożliwia monitorowanie i efektywne zwiększenie ilości miejsca do magazynowania.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Jak mogę monitorować interfejs API tabel oferty?

Za pomocą okienka **metryk** interfejs API tabel można monitorować żądania i użycie magazynu.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Jak mogę obliczyć wymaganą przepływność?

Możesz użyć szacowania pojemności, aby obliczyć TableThroughput, który jest wymagany dla operacji. Aby uzyskać więcej informacji, zobacz [szacunkowo jednostki żądań i magazyn danych](https://www.documentdb.com/capacityplanner). Ogólnie rzecz biorąc, można wyświetlić jednostkę jako kod JSON i wprowadzić numery dla operacji.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Czy można używać zestawu SDK interfejs API tabel lokalnie z emulatorem?

Nie w tej chwili.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Czy moja istniejąca aplikacja może współdziałać z interfejs API tabel?

Tak, obsługiwany jest ten sam interfejs API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Czy muszę migrować istniejące aplikacje usługi Azure Table Storage do zestawu SDK, jeśli nie chcę używać funkcji interfejs API tabel?

Nie. możesz tworzyć i korzystać z istniejących zasobów usługi Azure Table Storage bez zakłócania żadnego rodzaju. Niemniej jednak, jeśli nie używasz interfejs API tabel, nie możesz korzystać z indeksu automatycznego, opcji dodatkowej spójności ani dystrybucji globalnej.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Jak mogę dodać replikację danych w interfejs API tabel w więcej niż jednym regionie platformy Azure?

Aby dodać regiony odpowiednie dla aplikacji, można użyć [ustawień replikacji globalnej](tutorial-global-distribution-sql-api.md#portal) portalu Azure Cosmos DB. Aby opracować globalnie rozproszoną aplikację, należy również dodać aplikację z zestawem informacji PreferredLocation do lokalnego regionu w celu zapewnienia niskiego opóźnienia odczytu.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Jak mogę zmienić podstawowego regionu zapisu dla konta w interfejs API tabel?

Aby dodać region, a następnie przełączyć się do trybu failover w wymaganym regionie, można użyć okienka globalnego Azure Cosmos DB Portal replikacji. Aby uzyskać instrukcje, zobacz [programowanie z wieloregionowymi kontami Azure Cosmos DB](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Jak mogę skonfigurować moje preferowane regiony odczytu na potrzeby małych opóźnień podczas dystrybucji danych?

Aby ułatwić odczytywanie z lokalizacji lokalnej, użyj klucza PreferredLocation w pliku app.config. W przypadku istniejących aplikacji interfejs API tabel zgłasza błąd, jeśli ustawiono element Locationmode. Usuń ten kod, ponieważ interfejs API tabel pobiera te informacje z pliku app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak należy myśleć o poziomach spójności w interfejs API tabel?

Azure Cosmos DB zapewnia dobre kompromisy między spójnością, dostępnością i opóźnieniem. Azure Cosmos DB oferuje pięć poziomów spójności dla interfejs API tabel deweloperów, dzięki czemu można wybrać odpowiedni model spójności na poziomie tabeli i wprowadzić indywidualne żądania podczas wykonywania zapytania dotyczącego danych. Gdy klient łączy się z programem, może określić poziom spójności. Możesz zmienić poziom za pośrednictwem argumentu consistencyLevel CreateCloudTableClient.

Interfejs API tabel zapewnia odczyty o małym opóźnieniu przy użyciu "odczytywanie własnych zapisów", ze spójnością nieodświeżoną jako domyślną. Aby uzyskać więcej informacji, zobacz [poziomy spójności](consistency-levels.md).

Domyślnie usługa Azure Table Storage zapewnia silną spójność w obrębie regionu i ostatecznej spójności w lokalizacjach pomocniczych.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Czy Azure Cosmos DB interfejs API tabel oferować więcej poziomów spójności niż usługa Azure Table Storage?

Tak, aby uzyskać informacje o sposobach korzystania z rozproszonego charakteru Azure Cosmos DB, zobacz [poziomy spójności](consistency-levels.md). Ponieważ gwarancje są udostępniane dla poziomów spójności, można z nich korzystać z pewnością.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Kiedy jest włączona dystrybucja globalna, jak długo trwa replikowanie danych?

Azure Cosmos DB zatwierdza trwale danych w regionie lokalnym i wypychanie danych do innych regionów bezpośrednio w zakresie milisekund. Ta replikacja jest zależna tylko od czasu błądzenia centrum danych. Aby dowiedzieć się więcej o możliwościach dystrybucji globalnej Azure Cosmos DB, zobacz [Azure Cosmos DB: globalnie dystrybuowana usługa bazy danych na platformie Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Czy można zmienić poziom spójności żądania odczytu?

Za pomocą Azure Cosmos DB można ustawić poziom spójności na poziomie kontenera (w tabeli). Przy użyciu zestawu SDK platformy .NET można zmienić poziom, podając wartość klucza TableConsistencyLevel w pliku app.config. Możliwe wartości to: silna, powiązana nieaktualność, sesja, spójny prefiks i ostateczna. Aby uzyskać więcej informacji, zobacz [możliwość dostosowania poziomów spójności danych w Azure Cosmos DB](consistency-levels.md). Najważniejszym pomysłem jest to, że nie można ustawić poziomu spójności żądania na więcej niż ustawienie tabeli. Na przykład nie można ustawić poziomu spójności dla tabeli na początku i na poziomie spójności żądania.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak interfejs API tabel obsługiwać tryb failover, jeśli region ulegnie awarii?

Interfejs API tabel wykorzystuje globalnie dystrybuowaną platformę Azure Cosmos DB. Aby mieć pewność, że aplikacja może tolerować przestoje centrum danych, należy włączyć co najmniej jeden region dla konta w portalu Azure Cosmos DB [przy tworzeniu kont Azure Cosmos DB z wieloma regionami](high-availability.md). Priorytet regionu można ustawić przy użyciu portalu [opracowywania z wieloregionowymi kontami Azure Cosmos DB](high-availability.md).

Możesz dodać dowolną liczbę regionów dla konta i kontrolować miejsce, w którym może być przełączane do trybu failover, podając priorytet pracy awaryjnej. Aby można było korzystać z bazy danych, należy podać również aplikację. W takim przypadku klienci nie będą napotykać przestojów. [Najnowsza wersja zestawu SDK klienta platformy .NET](table-sdk-dotnet.md) to multihostingu, ale inne zestawy SDK nie są. Oznacza to, że może wykryć region, który jest wyłączony i automatycznie przechodzić do trybu failover w nowym regionie.

### <a name="is-the-table-api-enabled-for-backups"></a>Czy interfejs API tabel jest włączona dla kopii zapasowych?

Tak, interfejs API tabel wykorzystuje platformę Azure Cosmos DB do tworzenia kopii zapasowych. Kopie zapasowe są wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Czy interfejs API tabel indeksować wszystkie atrybuty jednostki domyślnie?

Tak, wszystkie atrybuty jednostki są indeksowane domyślnie. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB: zasady indeksowania](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Czy oznacza to, że nie trzeba tworzyć więcej niż jednego indeksu w celu spełnienia zapytań?

Tak, Azure Cosmos DB interfejs API tabel zapewnia automatyczne indeksowanie wszystkich atrybutów bez żadnej definicji schematu. Ta Automatyzacja pozwala deweloperom skupić się na aplikacji, a nie na tworzeniu indeksu i zarządzaniu nimi. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB: zasady indeksowania](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Czy mogę zmienić zasady indeksowania?

Tak, zasady indeksowania można zmienić, podając definicję indeksu. Należy prawidłowo zakodować i wyjść z ustawień.

W przypadku zestawów SDK non-.NET zasady indeksowania można ustawić tylko w portalu w **Eksplorator danych**, przejdź do konkretnej tabeli, którą chcesz zmienić, a następnie przejdź do pozycji **skalowanie & ustawienia**->zasad indeksowania, wprowadź żądaną zmianę, a następnie **Zapisz**.

Z zestawu SDK platformy .NET można przesłać plik app.config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB jako platforma prawdopodobnie ma wiele możliwości, takich jak sortowanie, agregacje, hierarchia i inne funkcje. Czy te funkcje zostaną dodane do interfejs API tabel?

Interfejs API tabel zapewnia te same funkcje zapytań co usługa Azure Table Storage. Usługa Azure Cosmos DB obsługuje także sortowanie, agregaty, zapytania geoprzestrzenne, hierarchię i szeroki zakres funkcji wbudowanych. Aby uzyskać więcej informacji, zobacz [zapytania SQL](./sql-query-getting-started.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kiedy należy zmienić TableThroughput dla interfejs API tabel?

Należy zmienić TableThroughput w przypadku zastosowania jednego z następujących warunków:

* Wykonujesz wyodrębnianie, przekształcanie i ładowanie danych (ETL) lub chcesz przekazać znaczną ilość danych w krótkim czasie.
* Potrzebna jest większa przepływność z kontenera lub z zestawu kontenerów na zapleczu. Na przykład widzisz, że używana przepływność jest większa niż przepustowość i jest ograniczona. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności dla kontenerów usługi Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Czy można skalować w górę i w dół przepływność tabeli interfejs API tabel?

Tak, możesz użyć okienka skalowanie w portalu Azure Cosmos DB, aby skalować przepływność. Aby uzyskać więcej informacji, zobacz [Ustawianie przepływności](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Czy jest to domyślny zestaw TableThroughput dla nowo zainicjowanych tabel?

Tak, jeśli nie zastąpisz TableThroughput za pośrednictwem app.config i nie używasz wstępnie utworzonego kontenera w Azure Cosmos DB, usługa utworzy tabelę o przepływności 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Czy istnieje jakakolwiek zmiana cen dla istniejących klientów usługi Azure Table Storage?

Brak. Nie wprowadzono zmian w cenie dla istniejących klientów usługi Azure Table Storage.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak obliczana jest cena dla interfejs API tabel?

Cena zależy od przydzieloną TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Jak mogę obsługiwać dowolnych limitów szybkości dla tabel w ofercie interfejs API tabel?

Jeśli częstotliwość żądań jest większa niż pojemność zainicjowanej przepływności dla kontenera bazowego lub zestawu kontenerów, występuje błąd, a zestaw SDK ponawia próbę wywołania, stosując zasady ponawiania.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Dlaczego należy wybrać przepływność z PartitionKey i RowKey, aby korzystać z oferty interfejs API tabel Azure Cosmos DB?

Azure Cosmos DB ustawia domyślną przepływność dla kontenera, jeśli nie podano go w pliku app.config lub za pośrednictwem portalu.

Azure Cosmos DB zapewnia gwarancje wydajności i opóźnień z górnymi granicami operacji. Ta gwarancja jest możliwa, gdy silnik może wymusić Zarządzanie operacjami dzierżawy. Ustawienie TableThroughput zapewnia gwarantowaną przepływność i opóźnienie, ponieważ platforma rezerwuje tę pojemność i gwarantuje pomyślne sukcesy działania.

Korzystając ze specyfikacji przepływności, można elastycznie zmienić ją na korzyść z sezonowości aplikacji, spełnić wymagania dotyczące przepływności i zaoszczędzić koszty.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Usługa Azure Table Storage została niedrogie dla mnie, ponieważ płacisz tylko za przechowywanie danych i rzadko wykonuję zapytania. Oferta interfejs API tabel Azure Cosmos DB wydaje się otrzymywać, mimo że nie wykonano żadnej pojedynczej transakcji ani nie zostały one zapisane. Czy można wyjaśnić?

Azure Cosmos DB jest zaprojektowana jako globalnie dystrybuowany system oparty na umowie SLA z gwarancjami dostępności, opóźnień i przepływności. Zarezerwowanie przepływności w Azure Cosmos DB jest gwarantowane, w przeciwieństwie do przepływności innych systemów. Azure Cosmos DB zapewnia dodatkowe możliwości, które klienci zażądały, takie jak indeksy pomocnicze i globalna dystrybucja.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nigdy nie otrzymuję pełnego limitu przydziału (oznacza to, że partycja jest pełna) podczas pozyskiwania danych do usługi Azure Table Storage. Korzystając z interfejs API tabel, uzyskuję tę wiadomość. Czy ta oferta ogranicza mnie i wymusza zmianę istniejącej aplikacji?

Azure Cosmos DB to system oparty na umowie SLA, który zapewnia nieograniczoną skalę, z gwarancją opóźnienia, przepływności, dostępności i spójności. Aby zapewnić gwarantowaną wydajność warstwy Premium, upewnij się, że rozmiar i indeks danych są zarządzane i skalowalne. Limit 20 GB dla liczby jednostek lub elementów na klucz partycji polega na tym, że zapewniamy doskonałe wyszukiwanie i wydajność zapytań. Aby zapewnić, że aplikacja będzie skalowana prawidłowo, nawet w przypadku usługi Azure Storage, zalecamy *utworzenie partycji* gorącej przez zapisanie wszystkich informacji w jednej partycji i wykonanie zapytania.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>PartitionKey i RowKey nadal są wymagane w przypadku interfejs API tabel?

Tak. Ponieważ obszar powierzchniowy interfejs API tabel jest podobny do tego w zestawie SDK usługi Azure Table Storage, klucz partycji stanowi wydajny sposób dystrybucji danych. Klucz wiersza jest unikatowy w obrębie tej partycji. Klucz wiersza musi być obecny i nie może mieć wartości null, podobnie jak w przypadku standardowego zestawu SDK. Długość RowKey to 255 bajtów, a długość PartitionKey wynosi 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jakie są komunikaty o błędach dla interfejs API tabel?

Usługa Azure Table Storage i Azure Cosmos DB interfejs API tabel używają tych samych zestawów SDK, dzięki czemu większość błędów będzie taka sama.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Dlaczego otrzymuję ograniczenie przy próbie utworzenia dużej ilości tabel po drugiej stronie w interfejs API tabel?

Azure Cosmos DB to system oparty na umowie SLA, który zapewnia opóźnienia, przepływność, dostępność i gwarancje spójności. Ponieważ jest to system aprowizacji, rezerwuje zasoby w celu zagwarantowania tych wymagań. Zostanie wykryta i ograniczona szybka stawka tworzenia tabel. Zalecamy przyjrzeć się szybkości tworzenia tabel i obniżyć ją do mniej niż 5 na minutę. Należy pamiętać, że interfejs API tabel jest systemem aprowizacji. Chwilę ją zainicjujesz.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Jak mogę przekazać opinię na temat zestawu SDK lub usterek?

Swoją opinię można podzielić na następujące sposoby:

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow jest Najlepsza w przypadku pytań związanych z programowaniem. Upewnij się, że pytanie jest [w temacie](https://stackoverflow.com/help/on-topic) i [Podaj jak najwięcej szczegółów, dzięki czemu pytanie jest jasne i możliwe do odpowiedzi](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji interfejs API tabel przy użyciu zestawu .NET SDK i Azure Cosmos DB](create-table-dotnet.md)
* [Tworzenie aplikacji Java do zarządzania danymi interfejs API tabel Azure Cosmos DB](create-table-java.md)