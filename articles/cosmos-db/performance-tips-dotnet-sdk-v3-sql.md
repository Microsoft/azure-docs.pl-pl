---
title: Porady dotyczące wydajności Azure Cosmos DB dla zestawu .NET SDK v3
description: Informacje o opcjach konfiguracji klienta pomagające ulepszyć wydajność zestawu SDK programu Azure Cosmos DB .NET v3.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: ab9fc4f08b96fc10a20125c30af2d6b8050c7606
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341743"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i platformy .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Zestaw .NET SDK w wersji 2](performance-tips.md)
> * [Java SDK 4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK 2](performance-tips-async-java.md)
> * [Sync Java SDK 2](performance-tips-java.md)

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i poziomów przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste jak w przypadku jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz temat [Obsługa przepływności kontenera](how-to-provision-container-throughput.md) lub [Obsługa przepływności bazy danych](how-to-provision-database-throughput.md). 

Ponieważ do Azure Cosmos DB jest uzyskiwany dostęp za pośrednictwem wywołań sieciowych, można przeprowadzić optymalizacje po stronie klienta, aby osiągnąć szczytową wydajność podczas korzystania z [zestawu SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Jeśli próbujesz poprawić wydajność bazy danych, zapoznaj się z opcjami przedstawionymi w poniższych sekcjach.

## <a name="hosting-recommendations"></a>Zalecenia dotyczące hostingu

**W przypadku obciążeń intensywnie korzystających z zapytań należy użyć systemu Windows 64-bitowego zamiast systemu Linux lub Windows 32-bitowego przetwarzania hosta**

Zalecamy przetwarzanie hosta systemu Windows 64-bitowego w celu zwiększenia wydajności. Zestaw SDK SQL zawiera natywną ServiceInterop.dll do analizy i optymalizowania zapytań lokalnie. ServiceInterop.dll jest obsługiwana tylko na platformie Windows x64. 

W przypadku systemu Linux i innych nieobsługiwanych platform, w których ServiceInterop.dll nie jest dostępna, do bramy jest nawiązywane dodatkowe połączenie sieciowe w celu uzyskania zoptymalizowanego zapytania. 

Cztery typy aplikacji wymienione tutaj używają domyślnie 32-bitowego przetwarzania hosta. Aby zmienić przetwarzanie hosta na 64-bitowe dla typu aplikacji, wykonaj następujące czynności:

- W **przypadku aplikacji wykonywalnych** : w oknie **właściwości projektu** w okienku **kompilacja** ustaw wartość [docelowy platformy](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) na **x64**.

- W **przypadku projektów testowych opartych na VSTest** : w menu **test** programu Visual Studio wybierz kolejno pozycje **Testuj**  >  **Ustawienia testu** , a następnie ustaw **domyślną architekturę procesora** na **x64**.

- **W przypadku lokalnie wdrożonych aplikacji sieci Web ASP.NET** : Wybierz opcje **Narzędzia**  >  **Options**  >  **projekty i rozwiązania**  >  **projekty sieci Web** , a następnie wybierz opcję **Użyj 64-bitowej wersji IIS Express dla witryn i projektów sieci Web**.

- W **przypadku aplikacji sieci web ASP.NET wdrożonych na platformie Azure** : w Azure Portal w obszarze **Ustawienia aplikacji** Wybierz platformę **64-bitową** .

> [!NOTE] 
> Domyślnie nowe projekty programu Visual Studio są ustawiane na **dowolny procesor**. Zalecamy ustawienie dla projektu wartości **x64** , aby nie przełączać się na **architekturę x86**. Projekt, który jest ustawiony na **dowolny procesor CPU** , można łatwo przełączyć na **procesor x86** , jeśli zostanie dodany zależność tylko dla architektury x86.<br/>
> Plik ServiceInterop.dll musi znajdować się w folderze, z którego jest wykonywana Biblioteka DLL SDK. Ta wartość powinna być istotna tylko w przypadku ręcznego kopiowania bibliotek DLL lub niestandardowych systemów kompilacji lub wdrożenia.
    
**Włącz odzyskiwanie pamięci po stronie serwera**

Zmniejszenie częstotliwości wyrzucania elementów bezużytecznych może pomóc w niektórych przypadkach. W programie .NET ustaw wartość [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) na `true` .

**Skalowanie obciążenia klienta**

W przypadku testowania o wysokiej przepływności lub stawek, które są większe niż 50 000 jednostek żądań na sekundę (RU/s), aplikacja kliencka może stać się wąskim gardłem obciążeń. Wynika to z faktu, że maszyna może ulec wykorzystaniu procesora lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

> [!NOTE] 
> Duże użycie procesora CPU może spowodować zwiększone opóźnienia i wyjątki limitu czasu żądania.

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

**Zasady połączenia: Użyj trybu połączenia bezpośredniego**

Domyślny tryb połączenia zestawu SDK platformy .NET V3 jest bezpośredni. Podczas tworzenia wystąpienia w programie należy skonfigurować tryb połączenia `CosmosClient` `CosmosClientOptions` .  Aby dowiedzieć się więcej o różnych opcjach łączności, zobacz artykuł dotyczący [trybów łączności](sql-sdk-connection-modes.md) .

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Wyczerpanie portów efemerycznych**

Jeśli w wystąpieniach jest wyświetlany wysoki poziom połączenia lub wysokie użycie portów, należy najpierw sprawdzić, czy wystąpienia klienta są pojedynczymi. Innymi słowy, wystąpienia klienta powinny być unikatowe w okresie istnienia aplikacji.

Gdy jest uruchomiona w protokole TCP, klient optymalizuje się pod kątem opóźnienia przy użyciu połączeń długotrwałych. Jest to w przeciwieństwie do protokołu HTTPS, który kończy połączenia po dwóch minutach braku aktywności.

W scenariuszach, w których masz dostęp rozrzedzony, a jeśli zauważysz wyższą liczbę połączeń w porównaniu z dostępem do trybu bramy, możesz:

* Skonfiguruj Właściwość [CosmosClientOptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) na wartość `PrivatePortPool` (obowiązującą w Framework w wersji 4.6.1 lub nowszej oraz .NET Core w wersji 2,0 lub nowszej). Ta właściwość umożliwia zestawowi SDK użycie małej puli tymczasowych portów dla różnych Azure Cosmos DB docelowych punktów końcowych.
* Skonfiguruj Właściwość [CosmosClientOptions. IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) o wartości większej lub równej 10 minut. Zalecane wartości to od 20 minut do 24 godzin.

<a id="same-region"></a>

**W celu uzyskania wydajności kolokacja klientów w tym samym regionie świadczenia usługi Azure**

Jeśli to możliwe, należy umieścić wszystkie aplikacje, które wywołują Azure Cosmos DB w tym samym regionie, co baza danych Azure Cosmos DB. Oto przybliżone porównanie: wywołania Azure Cosmos DB w tym samym regionie kończą się w ciągu 1 milisekund (MS) do 2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych przekracza 50 ms. To opóźnienie może się różnić od żądania do żądania, w zależności od trasy podjętej przez żądanie przesyłanej z klienta do granicy centrum danych platformy Azure. 

Najniższe możliwe opóźnienie można uzyskać, upewniając się, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Kolokacja klientów w tym samym regionie." border="false":::

   <a id="increase-threads"></a>

**Zwiększenie liczby wątków/zadań**

Ponieważ wywołania Azure Cosmos DB są realizowane za pośrednictwem sieci, może być konieczne zróżnicowanie stopnia współbieżności żądań, aby aplikacja kliencka spędza minimalny czas oczekiwania między żądaniami. Na przykład, jeśli używasz [biblioteki równoległej zadania](/dotnet/standard/parallel-programming/task-parallel-library-tpl).NET, Utwórz w kolejności setek zadań odczytywanych z lub zapisu do Azure Cosmos DB.

**Włącz przyspieszone sieci**
 
Aby zmniejszyć opóźnienia i wahania procesora, zalecamy włączenie przyspieszonej sieci na maszynach wirtualnych klienta. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)

**Zainstaluj najnowszy zestaw SDK**

Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Aby określić najnowszy zestaw SDK i przejrzeć ulepszenia, zobacz [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Korzystanie z interfejsów API przesyłania strumieniowego**

[Zestaw .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) zawiera interfejsy API przesyłania strumieniowego, które mogą odbierać i zwracać dane bez serializacji. 

Aplikacje warstwy środkowej, które nie zużywają odpowiedzi bezpośrednio z zestawu SDK, ale przekazują je do innych warstw aplikacji, mogą korzystać z interfejsów API usługi Stream. Aby zapoznać się z przykładami obsługi strumienia, zobacz przykłady [zarządzania elementami](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

Każde `CosmosClient` wystąpienie jest bezpieczne dla wątków i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów, gdy działa w trybie bezpośrednim. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność klienta zestawu SDK, zalecamy użycie jednego wystąpienia na `AppDomain` okres istnienia aplikacji.

Podczas pracy nad Azure Functions wystąpienia powinny również postępować zgodnie z istniejącymi [wskazówkami](../azure-functions/manage-connections.md#static-clients) i obsługiwać pojedyncze wystąpienie.

<a id="max-connection"></a>

**Wyłącz odpowiedź zawartości przy operacjach zapisu**

W przypadku obciążeń, które mają duże ilości tworzenia ładunków, należy ustawić `EnableContentResponseOnWrite` opcję żądania na `false` . Usługa nie zwróci już utworzonego lub zaktualizowanego zasobu do zestawu SDK. Zwykle ponieważ aplikacja ma obiekt, który jest tworzony, nie potrzebuje usługi do zwrócenia. Wartości nagłówka są nadal dostępne, na przykład opłata za żądanie. Wyłączenie odpowiedzi zawartości może pomóc w zwiększeniu wydajności, ponieważ zestaw SDK nie musi już przydzielać pamięci lub serializować treści odpowiedzi. Zmniejsza to również wykorzystanie przepustowości sieci, aby zwiększyć wydajność.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Włącz zbiorcze, aby zoptymalizować przepływność zamiast opóźnień**

Włącz *zbiorczo* dla scenariuszy, w których obciążenie wymaga dużej ilości przepływności, a opóźnienie nie jest tak ważne. Aby uzyskać więcej informacji o sposobie włączania funkcji Bulk i dowiedzieć się, które scenariusze powinny być używane w programie, zobacz [wprowadzenie do pomocy technicznej zbiorczej](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Zwiększ System.Net MaxConnections na hosta, gdy używasz trybu bramy**

Żądania Azure Cosmos DB są wykonywane za pośrednictwem protokołu HTTPS/REST w przypadku korzystania z trybu bramy. Są one przedmiotem domyślnego limitu połączeń dla nazwy hosta lub adresu IP. Może być konieczne ustawienie `MaxConnections` wyższej wartości (od 100 do 1 000), aby Biblioteka klienta mogła używać wielu jednoczesnych połączeń do Azure Cosmos DB. W zestawie .NET SDK 1.8.0 i nowszych wartość domyślna dla [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) to 50. Aby zmienić wartość, można ustawić [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) wyższą wartość.

**Dostrajaj równoległe zapytania dla kolekcji partycjonowanych**

Zestaw SDK programu SQL .NET obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań względem kontenera partycjonowanego. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) związane z pracą z zestawami SDK. Zapytania równoległe zostały zaprojektowane w celu zapewnienia lepszej opóźnienia zapytań i przepływności niż ich odpowiedniki seryjne. 

Zapytania równoległe zawierają dwa parametry, które można dostosować w celu dopasowania do własnych wymagań: 

- **MaxConcurrency** : określa maksymalną liczbę partycji, które mogą być wykonywane równolegle.

   Równoległe zapytanie działa przez wykonywanie zapytań na wielu partycjach równolegle. Ale dane z pojedynczej partycji są pobierane sekwencyjnie w odniesieniu do zapytania. Ustawienie `MaxConcurrency` w [zestawie SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) na liczbę partycji ma najlepszą szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz ustawić stopień równoległości na wysoki. System wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako stopień równoległości.

    Zapytania równoległe dają najwięcej korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest podzielona na partycje, tak aby wszystkie lub większość danych zwróconych przez zapytanie było skoncentrowane na kilku partycjach (jedna partycja jest najgorszą wielkością), te partycje spowodują wąskie gardła wydajności zapytania.
   
- **MaxBufferedItemCount** : określa liczbę wstępnie pobranych wyników.

   Zapytanie równoległe zostało zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. To wstępne pobranie ułatwia zwiększenie ogólnego opóźnienia zapytania. `MaxBufferedItemCount`Parametr ogranicza liczbę wstępnie pobranych wyników. Ustaw `MaxBufferedItemCount` na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę), aby zezwolić na zapytanie, aby otrzymać maksymalną korzyść z wstępnego pobierania.

   Przed pobraniem działa w taki sam sposób, niezależnie od stopnia równoległości, i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  

**Implementowanie wycofywania w interwałach RetryAfter**

Podczas testowania wydajności należy zwiększyć obciążenie do momentu ograniczenia niewielkiej liczby żądań. Jeśli żądania są ograniczone, aplikacja kliencka powinna cofnąć ograniczenie przepustowości dla interwału ponawiania prób określonego serwera. Poszanowanie wycofywania gwarantuje, że poświęcisz minimalny czas oczekiwania między ponownymi próbami. 

Aby uzyskać więcej informacji, zobacz [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Istnieje mechanizm rejestrowania dodatkowych informacji diagnostycznych i rozwiązywania problemów z opóźnieniami, jak pokazano w poniższym przykładzie. Można rejestrować ciąg diagnostyczny dla żądań o wyższym opóźnieniu odczytu. Przechwycony ciąg diagnostyczny pomoże zrozumieć, ile razy otrzymano błąd *429* dla danego żądania.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Zwiększenie liczby wątków/zadań**

Zobacz [zwiększenie liczby wątków/zadań](#increase-threads) w sekcji Sieć w tym artykule.

## <a name="indexing-policy"></a>Zasady indeksowania
 
**Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

Zasady indeksowania Azure Cosmos DB umożliwiają również określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (IndexingPolicy. IncludedPaths i IndexingPolicy. ExcludedPaths). 

Indeksowanie tylko potrzebnych ścieżek może poprawić wydajność zapisu, zmniejszyć opłaty za usługę RU przy operacjach zapisu i zmniejszyć ilość miejsca w indeksie dla scenariuszy, w których wzorce zapytań są znane wcześniej. Dzieje się tak, ponieważ koszty indeksowania są skorelowane bezpośrednio z liczbą unikatowych ścieżek indeksowanych. Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](index-policy.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

**Mierzenie i dostrajanie dla mniejszego użycia RU/s**

Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych. Te operacje obejmują zapytania relacyjne i hierarchiczne z plikami w formacie uniwersalnego dysku (UDF), procedurami składowanymi i wyzwalaczami, które działają na dokumentach w ramach kolekcji baz danych. 

Koszty związane z każdą z tych operacji różnią się w zależności od procesora CPU, operacji we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostek żądania jest oceniane jako stawka za sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania.

Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba plików UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytań.

Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-obciążeni](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną `RequestCharge` Właściwość w `ResourceResponse\<T>` lub `FeedResponse\<T>` w zestawie .NET SDK), aby zmierzyć liczbę jednostek żądania używanych przez operacje:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Opłata za żądanie, która jest zwracana w tym nagłówku, jest częścią alokowanej przepływności (czyli 2 000 RU/s). Na przykład jeśli poprzednia kwerenda zwraca dokumenty 1 000 1-KB, koszt operacji wynosi 1 000. Tak więc w ramach jednej sekundy serwer honoruje tylko dwa takie żądania, zanim zostanie on przekroczony. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Zbyt duży współczynnik obsługi — limit liczby żądań**

Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i żadne użycie wydajności przepływności wykracza poza poziom zarezerwowany. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429). Zwraca nagłówek [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób, która aktualnie jest ustawiona na 9 wewnętrznie przez klienta, może być niewystarczająca. W takim przypadku klient zgłasza CosmosException z kodem stanu 429 do aplikacji. 

Domyślną liczbę ponownych prób można zmienić, ustawiając wartość `RetryOptions` na `CosmosClientOptions` instancji. Domyślnie CosmosException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Ten błąd jest zwracany nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, niezależnie od tego, czy bieżąca wartość jest wartością domyślną 9, czy wartość zdefiniowaną przez użytkownika.

Automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji. Może jednak nie być najlepszym zachowaniem, gdy wykonujesz testy wydajnościowe, szczególnie gdy mierzy się opóźnienia. Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w zakresie wydajności, Zmierz opłaty, które są zwracane przez poszczególne operacje, i upewnij się, że żądania są działające poniżej zarezerwowanej stawki żądania. 

Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).

**Aby uzyskać większą przepływność, Zaprojektuj w poszukiwaniu mniejszych dokumentów**

Opłata za żądanie (czyli koszt przetwarzania żądań) określonej operacji jest skorelowana bezpośrednio z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w małych dokumentach.

## <a name="next-steps"></a>Następne kroki
Aby skorzystać z przykładowej aplikacji służącej do oceny Azure Cosmos DB w scenariuszach o wysokiej wydajności na kilku komputerach klienckich, zobacz [testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](performance-testing.md).

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partitioning-overview.md).