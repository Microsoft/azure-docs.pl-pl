---
title: Porady dotyczące wydajności Azure Cosmos DB dla zestawu .NET SDK V2
description: Dowiedz się więcej na temat opcji konfiguracji klienta, aby zwiększyć wydajność Azure Cosmos DB .NET V2 SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: efedfb9701d12548b80eccda9cd2aa29bc644ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802144"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Porady dotyczące wydajności usługi Azure Cosmos DB i zestawu .NET SDK w wersji 2

> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Zestaw .NET SDK w wersji 2](performance-tips.md)
> * [Java SDK 4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK 2](performance-tips-async-java.md)
> * [Sync Java SDK 2](performance-tips-java.md)

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste jak w przypadku jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność kontenera](how-to-provision-container-throughput.md) lub [jak zapewnić przepływność bazy danych](how-to-provision-database-throughput.md). Ponieważ do Azure Cosmos DB jest uzyskiwany dostęp za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z [zestawu SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Tak więc, jeśli próbujesz poprawić wydajność bazy danych, weź pod uwagę następujące opcje:

## <a name="upgrade-to-the-net-v3-sdk"></a>Uaktualnianie do zestawu SDK platformy .NET v3

[Zestaw SDK platformy .NET v3](https://github.com/Azure/azure-cosmos-dotnet-v3) został opublikowany. Jeśli używasz zestawu .NET v3 SDK, zobacz [Przewodnik dotyczący wydajności programu .NET v3](performance-tips-dotnet-sdk-v3-sql.md) , aby uzyskać następujące informacje:

- Domyślny tryb bezpośredniego protokołu TCP
- Obsługa interfejsu API usługi Stream
- Obsługa niestandardowego serializatora zezwalającego na System.Text.JSprzy użyciu
- Zintegrowana partia i wsparcie zbiorcze

## <a name="hosting-recommendations"></a>Zalecenia dotyczące hostingu

**W przypadku obciążeń intensywnie korzystających z zapytań należy użyć systemu Windows 64-bitowego zamiast systemu Linux lub Windows 32-bitowego przetwarzania hosta**

Zalecamy przetwarzanie hosta systemu Windows 64-bitowego w celu zwiększenia wydajności. Zestaw SDK SQL zawiera natywną ServiceInterop.dll do analizy i optymalizowania zapytań lokalnie. ServiceInterop.dll jest obsługiwana tylko na platformie Windows x64. W przypadku systemu Linux i innych nieobsługiwanych platform, w których ServiceInterop.dll nie jest dostępna, do bramy jest nawiązywane dodatkowe połączenie sieciowe w celu uzyskania zoptymalizowanego zapytania. Następujące typy aplikacji domyślnie korzystają z 32-bitowego przetwarzania hosta. Aby zmienić przetwarzanie hosta na 64-bitowe, wykonaj następujące kroki na podstawie typu aplikacji:

- W przypadku aplikacji wykonywalnych można zmienić przetwarzanie hosta, ustawiając wartość [docelowy platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) na **x64**  w oknie **właściwości projektu** na karcie **kompilacja** .

- W przypadku projektów testowych opartych na VSTest można zmienić przetwarzanie hosta **, wybierając opcję Testuj**  >  **Ustawienia testu**  >  **domyślne architektura procesora jako x64** w menu **test** programu Visual Studio.

- W przypadku lokalnie wdrożonych aplikacji sieci Web ASP.NET można zmienić przetwarzanie hosta, wybierając opcję **Użyj 64-bitowej wersji IIS Express dla witryn i projektów sieci Web** w obszarze **Narzędzia**  >  **Opcje**  >  **projekty i rozwiązania**  >  **projekty sieci Web**.

- W przypadku aplikacji sieci Web ASP.NET wdrożonych na platformie Azure można zmienić przetwarzanie hosta, wybierając platformę **64-bitową** w **ustawieniach aplikacji** w Azure Portal.

> [!NOTE] 
> Domyślnie nowe projekty programu Visual Studio są ustawiane na **dowolny procesor**. Zalecamy ustawienie dla projektu wartości **x64** , aby nie przełączać się na **architekturę x86**. Projekt ustawiony na **dowolny procesor CPU** może z łatwością przechodzić do **architektury x86** , jeśli zostanie dodany zależność tylko dla architektury x86.<br/>
> ServiceInterop.dll musi znajdować się w folderze, z którego jest wykonywana Biblioteka DLL SDK. Ta wartość powinna być istotna tylko w przypadku ręcznego kopiowania bibliotek DLL lub niestandardowych systemów kompilacji/wdrażania.
    
**Włącz odzyskiwanie pamięci po stronie serwera (GC)**

Zmniejszenie częstotliwości wyrzucania elementów bezużytecznych może pomóc w niektórych przypadkach. W programie .NET ustaw wartość [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na `true` .

**Skalowanie obciążenia klienta**

Jeśli testujesz się o wysokim poziomie przepływności (ponad 50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na obciążenie procesora CPU lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

> [!NOTE] 
> Duże użycie procesora CPU może spowodować zwiększone opóźnienia i wyjątki limitu czasu żądania.

## <a name="networking"></a><a id="networking"></a> Sieci

**Zasady połączenia: Użyj trybu połączenia bezpośredniego**

Sposób, w jaki klient nawiązuje połączenie z Azure Cosmos DB, ma ważne konsekwencje dotyczące wydajności, szczególnie w przypadku zaobserwowanego opóźnienia po stronie klienta. Dostępne są dwa ustawienia konfiguracji klucza do konfigurowania zasad połączenia klienta: *tryb* połączenia i *Protokół*połączenia.  Dostępne są dwa tryby:

  * Tryb bramy (wartość domyślna)
      
    Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest skonfigurowany domyślnie dla [Microsoft.Azure.DocUMENTDB SDK](sql-api-sdk-dotnet.md). Jeśli aplikacja działa w sieci firmowej z rygorystycznymi ograniczeniami zapory, najlepszym wyborem jest tryb bramy, ponieważ używa on standardowego portu HTTPS i pojedynczego punktu końcowego DNS. Jednak jest to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w Azure Cosmos DB. Tryb bezpośredni zapewnia lepszą wydajność z powodu mniejszej liczby przeskoków sieci. Zalecamy również tryb połączenia bramy w przypadku uruchamiania aplikacji w środowiskach, które mają ograniczoną liczbę połączeń gniazd.

    Korzystając z zestawu SDK w Azure Functions, szczególnie w [planie zużycia](../azure-functions/functions-scale.md#consumption-plan), należy pamiętać o bieżących [limitach połączeń](../azure-functions/manage-connections.md). W takim przypadku tryb bramy może być lepszy, jeśli pracujesz również z innymi klientami opartymi na protokole HTTP w aplikacji Azure Functions.

  * Tryb bezpośredni

    Tryb bezpośredni obsługuje łączność za pośrednictwem protokołu TCP.
     
W przypadku korzystania z protokołu TCP w trybie bezpośrednim oprócz portów bramy należy upewnić się, że zakres portów od 10000 do 20000 jest otwarty, ponieważ Azure Cosmos DB używa dynamicznych portów TCP. W przypadku korzystania z trybu bezpośredniego dla [prywatnych punktów końcowych](./how-to-configure-private-endpoints.md)powinien być otwarty cały zakres portów TCP — od 0 do 65535. Jeśli te porty nie są otwarte i podjęto próbę użycia protokołu TCP, zostanie wyświetlony błąd niedostępności usługi 503. W poniższej tabeli przedstawiono tryby łączności dostępne dla różnych interfejsów API i portów usług używanych dla każdego interfejsu API:

|Tryb połączenia  |Obsługiwany protokół  |Obsługiwane zestawy SDK  |Port API/usługi  |
|---------|---------|---------|---------|
|Brama  |   HTTPS    |  Wszystkie zestawy SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tabela (443), Cassandra (10350), Graph (443) <br> Port 10250 mapuje do domyślnego interfejsu API Azure Cosmos DB dla wystąpienia MongoDB bez replikacji geograficznej. Porty 10255 i 10256 są mapowane na wystąpienie mające replikację geograficzną.   |
|Direct    |     TCP    |  Zestaw SDK .NET    | Gdy są używane publiczne/punkty końcowe usługi: porty z zakresu od 10000 do 20000<br>W przypadku korzystania z prywatnych punktów końcowych: porty w zakresie od 0 do 65535 |

Azure Cosmos DB oferuje prosty, otwarty model programowania RESTful za pośrednictwem protokołu HTTPS. Ponadto oferuje wydajny protokół TCP, który jest również RESTful w swoim modelu komunikacji i jest dostępny za pośrednictwem zestawu SDK klienta platformy .NET. Protokół TCP używa protokołu TLS do uwierzytelniania początkowego i szyfrowania ruchu sieciowego. Aby uzyskać najlepszą wydajność, Użyj protokołu TCP, gdy jest to możliwe.

W przypadku zestawu SDK Microsoft.Azure.DocumentDB można skonfigurować tryb połączenia podczas konstruowania `DocumentClient` wystąpienia przy użyciu `ConnectionPolicy` parametru. W przypadku korzystania z trybu bezpośredniego można również ustawić `Protocol` za pomocą `ConnectionPolicy` parametru.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Ponieważ protokół TCP jest obsługiwany tylko w trybie bezpośrednim, w przypadku korzystania z trybu bramy protokół HTTPS jest zawsze używany do komunikowania się z bramą, a `Protocol` wartość w `ConnectionPolicy` jest ignorowana.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Zasady połączenia Azure Cosmos DB" border="false":::

**Wyczerpanie portów efemerycznych**

Jeśli w wystąpieniach jest wyświetlany wysoki poziom połączenia lub wysokie użycie portów, należy najpierw sprawdzić, czy wystąpienia klienta są pojedynczymi. Innymi słowy, wystąpienia klienta powinny być unikatowe w okresie istnienia aplikacji.

W przypadku uruchamiania w protokole TCP klient optymalizuje się pod kątem opóźnienia przy użyciu połączeń długotrwałych zamiast protokołu HTTPS, który kończy połączenia po 2 minutach braku aktywności.

W scenariuszach, w których masz dostęp rozrzedzony i jeśli zauważysz wyższą liczbę połączeń w porównaniu z dostępem do trybu bramy, możesz:

* Skonfiguruj Właściwość [ConnectionPolicy. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) na wartość `PrivatePortPool` (efektywna w przypadku wersji Framework>= 4.6.1 i .net core w wersji >= 2,0): Ta właściwość umożliwia zestawowi SDK użycie niewielkiej puli tymczasowych portów dla różnych Azure Cosmos DB docelowych punktów końcowych.
* Należy skonfigurować właściwość [ConnectionPolicy. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) , która nie może być większa niż 10 minut. Zalecane wartości są z zakresu od 20 minut do 24 godzin.

**Wywołaj OpenAsync, aby uniknąć opóźnień uruchamiania przy pierwszym żądaniu**

Domyślnie pierwsze żądanie ma większe opóźnienia, ponieważ musi pobrać tabelę routingu adresów. W przypadku korzystania z [zestawu SDK V2](sql-api-sdk-dotnet.md)należy wywołać `OpenAsync()` raz podczas inicjowania, aby uniknąć opóźnienia uruchamiania pierwszego żądania. Wywołanie wygląda następująco: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` Program będzie generował żądania w celu uzyskania tabeli routingu adresów dla wszystkich kontenerów na koncie. W przypadku kont, które mają wiele kontenerów, ale których aplikacja uzyskuje dostęp do podzbioru z nich, `OpenAsync` wygeneruje niepotrzebny ruch, co spowodowałoby powolne działanie inicjalizacji. Korzystanie `OpenAsync` z programu może nie być przydatne w tym scenariuszu, ponieważ spowalnia uruchamianie aplikacji.

**W celu uzyskania wydajności kolokacja klientów w tym samym regionie świadczenia usługi Azure**

Jeśli to możliwe, należy umieścić wszystkie aplikacje, które wywołują Azure Cosmos DB w tym samym regionie, co baza danych Azure Cosmos DB. Oto przybliżone porównanie: wywołania Azure Cosmos DB w tym samym regionie zakończyły się w ciągu 1 ms do 2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem USA jest większe niż 50 ms. To opóźnienie może się różnić od żądania do żądania, w zależności od trasy podjętej przez żądanie przesyłanej z klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie można uzyskać, upewniając się, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Zasady połączenia Azure Cosmos DB" border="false":::

**Zwiększenie liczby wątków/zadań**
<a id="increase-threads"></a>

Ponieważ wywołania Azure Cosmos DB są realizowane za pośrednictwem sieci, może być konieczne zróżnicowanie stopnia równoległości żądań, aby aplikacja kliencka poświęca minimalny czas oczekiwania na żądania. Na przykład, jeśli używasz [biblioteki równoległej zadania](https://msdn.microsoft.com//library/dd460717.aspx).NET, Utwórz w kolejności setek zadań odczytywanych z lub zapisu do Azure Cosmos DB.

**Włącz przyspieszone sieci**
 
Aby zmniejszyć opóźnienia i wahania procesora, zalecamy włączenie przyspieszonej sieci na maszynach wirtualnych klienta. Zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md) lub [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)

**Zainstaluj najnowszy zestaw SDK**

Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zobacz strony [zestawu sdk Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) , aby określić najnowszy zestaw SDK i zapoznać się z ulepszeniami.

**Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

Każde `DocumentClient` wystąpienie jest bezpieczne dla wątków i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów w przypadku działania w trybie bezpośrednim. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność klienta zestawu SDK, zalecamy użycie jednego wystąpienia na `AppDomain` okres istnienia aplikacji.

**Zwiększ System.Net MaxConnections na hosta podczas korzystania z trybu bramy**

Żądania Azure Cosmos DB są wykonywane za pośrednictwem protokołu HTTPS/REST w przypadku korzystania z trybu bramy. Są one uzależnione od domyślnego limitu połączeń na nazwę hosta lub adres IP. Może być konieczne ustawienie `MaxConnections` wyższej wartości (od 100 do 1 000), aby Biblioteka klienta mogła używać wielu jednoczesnych połączeń do Azure Cosmos DB. W zestawie .NET SDK 1.8.0 i nowszych wartość domyślna dla [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) to 50. Aby zmienić wartość, można ustawić opcję [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na wyższą wartość.

**Dostrajaj równoległe zapytania dla kolekcji partycjonowanych**

Zestaw SDK programu SQL 1.9.0 i nowsze obsługują zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) związane z pracą z zestawami SDK. Zapytania równoległe zostały zaprojektowane w celu zapewnienia lepszej opóźnienia zapytań i przepływności niż ich odpowiedniki seryjne. Zapytania równoległe zawierają dwa parametry, które można dostosować w celu dopasowania do własnych wymagań: 
- `MaxDegreeOfParallelism` Określa maksymalną liczbę partycji, które mogą być wykonywane równolegle. 
- `MaxBufferedItemCount` Określa liczbę wstępnie pobranych wyników.

***Dostrajanie stopnia równoległości***

Równoległe zapytanie działa przez wykonywanie zapytań na wielu partycjach równolegle. Ale dane z pojedynczej partycji są pobierane sekwencyjnie w odniesieniu do zapytania. Ustawienie `MaxDegreeOfParallelism` w [zestawie SDK V2](sql-api-sdk-dotnet.md) na liczbę partycji ma najlepszą szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz ustawić stopień równoległości na wysoki. System wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako stopień równoległości.

Zapytania równoległe dają najwięcej korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest podzielona na partycje, tak aby wszystkie lub większość danych zwróconych przez zapytanie było skoncentrowane na kilku partycjach (jedna partycja jest najgorszą wielkością), te partycje spowodują wąskie gardła wydajności zapytania.

***Dostrajanie MaxBufferedItemCount***
    
Zapytanie równoległe zostało zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. To wstępne pobranie ułatwia zwiększenie ogólnego opóźnienia zapytania. `MaxBufferedItemCount`Parametr ogranicza liczbę wstępnie pobranych wyników. Ustaw `MaxBufferedItemCount` na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę), aby zezwolić na zapytanie, aby otrzymać maksymalną korzyść z wstępnego pobierania.

Przed pobraniem działa w taki sam sposób, niezależnie od stopnia równoległości, i istnieje pojedynczy bufor dla danych ze wszystkich partycji.  

**Implementowanie wycofywania w interwałach RetryAfter**

Podczas testowania wydajności należy zwiększyć obciążenie do momentu ograniczenia niewielkiej liczby żądań. Jeśli żądania są ograniczone, aplikacja kliencka powinna wykonać wycofywanie z ograniczeniami dla interwału ponawiania prób określonego serwera. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami. 

W tych zestawach SDK uwzględniono obsługę zasad ponawiania prób:
- Wersja 1.8.0 i nowszego [zestawu .NET SDK dla SQL](sql-api-sdk-dotnet.md) i [zestawu Java SDK dla SQL](sql-api-sdk-java.md)
- Wersja 1.9.0 i nowsza [ zestawuNode.js SDK dla SQL](sql-api-sdk-node.md) i [zestawu Python SDK dla SQL](sql-api-sdk-python.md)
- Wszystkie obsługiwane wersje zestawów SDK [platformy .NET Core](sql-api-sdk-dotnet-core.md) 

Aby uzyskać więcej informacji, zobacz [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
W wersji 1,19 i nowszych zestawu .NET SDK istnieje mechanizm rejestrowania dodatkowych informacji diagnostycznych i rozwiązywania problemów z opóźnieniami, jak pokazano w poniższym przykładzie. Można rejestrować ciąg diagnostyczny dla żądań, które mają wyższy czas oczekiwania na odczyt. Przechwycony ciąg diagnostyczny pomoże zrozumieć, ile razy otrzymujesz 429 błędów dla danego żądania.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Identyfikatory URI dokumentów pamięci podręcznej dla niskiego opóźnienia odczytu**

W miarę możliwości można buforować identyfikatory URI dokumentów w celu uzyskania najlepszej wydajności odczytu. Należy zdefiniować logikę buforowania zasobu podczas tworzenia zasobu. Wyszukiwania oparte na identyfikatorach zasobów są szybsze niż wyszukiwania oparte na nazwach, więc buforowanie tych wartości zwiększa wydajność.

**Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

Gdy wykonujesz zbiorczy odczyt dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład `ReadDocumentFeedAsync` ) lub podczas wysyłania zapytania SQL, wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

Aby zmniejszyć liczbę podróży sieci wymaganych do pobrania wszystkich stosownych wyników, można zwiększyć rozmiar strony, używając wartości [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do żądania tak wielu nagłówków jak 1 000. Jeśli chcesz wyświetlić tylko kilka wyników, na przykład jeśli interfejs użytkownika lub API aplikacji zwraca tylko 10 wyników naraz, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

> [!NOTE] 
> `maxItemCount`Właściwość nie powinna być używana tylko do dzielenia na strony. Jego głównym zastosowaniem jest poprawa wydajności zapytań przez zmniejszenie maksymalnej liczby elementów zwracanych na jednej stronie.  

Możesz również ustawić rozmiar strony przy użyciu dostępnych zestawów SDK Azure Cosmos DB. Właściwość [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) w programie `FeedOptions` umożliwia ustawienie maksymalnej liczby elementów, które mają być zwracane w operacji wyliczania. Gdy `maxItemCount` ustawiono wartość-1, zestaw SDK automatycznie odnajdzie optymalną, w zależności od rozmiaru dokumentu. Na przykład:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Po wykonaniu zapytania dane uzyskane są wysyłane w ramach pakietu TCP. Jeśli określisz zbyt niską wartość dla `maxItemCount` , liczba podróży wymagana do wysłania danych w ramach pakietu TCP jest wysoka, co ma wpływ na wydajność. Dlatego jeśli nie masz pewności, jaka wartość ma być ustawiona dla `maxItemCount` właściwości, najlepiej ją ustawić na wartość-1 i pozwól, aby zestaw SDK wybierze domyślną.

**Zwiększenie liczby wątków/zadań**

Zobacz [zwiększenie liczby wątków/zadań](#increase-threads) w sekcji Sieć w tym artykule.

## <a name="indexing-policy"></a>Zasady indeksowania
 
**Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

Zasady indeksowania Azure Cosmos DB umożliwiają również określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (IndexingPolicy. IncludedPaths i IndexingPolicy. ExcludedPaths). Ścieżki indeksowania mogą zwiększyć wydajność zapisu i ograniczyć magazyn indeksów w scenariuszach, w których wzorce zapytań są znane wcześniej. Dzieje się tak, ponieważ koszty indeksowania są skorelowane bezpośrednio z liczbą unikatowych ścieżek indeksowanych. Na przykład ten kod pokazuje, jak wykluczyć całą sekcję dokumentów (poddrzewo) z indeksowania przy użyciu symbolu wieloznacznego "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Kazany

**Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych. Te operacje obejmują zapytania relacyjne i hierarchiczne z UDF, procedurami składowanymi i wyzwalaczami, które działają na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji różni się w zależności od procesora CPU, operacji we/wy i pamięci wymaganej do ukończenia tej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania.

Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar źródłowego zestawu danych wpływają na koszt operacji zapytania.

Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-obciążeni](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną `RequestCharge` Właściwość w `ResourceResponse\<T>` lub `FeedResponse\<T>` w zestawie .NET SDK), aby zmierzyć liczbę jednostek żądania używanych przez operacje:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności (czyli 2 000 jednostek ru/sekundę). Na przykład jeśli poprzednia kwerenda zwraca dokumenty 1 000 1-KB, koszt operacji wynosi 1 000. Tak więc w ramach jednej sekundy serwer honoruje tylko dwa takie żądania przed przystąpieniem do ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Zbyt duży współczynnik obsługi — limit liczby żądań**

Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i żadne użycie wydajności przepływności wykracza poza poziom zarezerwowany. Serwer będzie zapobiegawczo żądanie z RequestRateTooLarge (kod stanu HTTP 429). Spowoduje to zwrócenie nagłówka [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , który wskazuje ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może być niewystarczająca. W takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. 

Domyślną liczbę ponownych prób można zmienić, ustawiając wartość `RetryOptions` na `ConnectionPolicy` instancji. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Ten błąd zwraca nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, niezależnie od tego, czy bieżąca wartość jest wartością domyślną 9, czy wartość zdefiniowaną przez użytkownika.

Automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji. Może jednak nie być najlepszym zachowaniem, gdy wykonujesz testy wydajnościowe, szczególnie gdy mierzy się opóźnienia. Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).

**Aby uzyskać większą przepływność, Zaprojektuj w poszukiwaniu mniejszych dokumentów**

Opłata za żądanie (czyli koszt przetwarzania żądań) danej operacji jest skorelowana bezpośrednio z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w małych dokumentach.

## <a name="next-steps"></a>Następne kroki

Aby skorzystać z przykładowej aplikacji służącej do oceny Azure Cosmos DB w scenariuszach o wysokiej wydajności na kilku komputerach klienckich, zobacz [testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](performance-testing.md).

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partition-data.md).
