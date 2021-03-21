---
title: Partycjonowanie Service Fabric usług
description: Dowiedz się, jak podzielić się Service Fabric bezstanowe i stanowe usługi
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97915899"
---
# <a name="partition-service-fabric-reliable-services"></a>Partition Service Fabric reliable services (Partycjonowanie usług Reliable Services w ramach usługi Service Fabric)
Ten artykuł zawiera wprowadzenie do podstawowych pojęć dotyczących partycjonowania Service Fabric niezawodnych usług Azure. Partycjonowanie umożliwia przechowywanie danych na maszynach lokalnych, dzięki czemu można skalować dane i obliczenia jednocześnie.

> [!TIP]
> [Pełny przykład](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) kodu w tym artykule jest dostępny w witrynie GitHub.

## <a name="partitioning"></a>Partycjonowanie
Partycjonowanie nie jest unikatowe dla Service Fabric. W rzeczywistości jest to podstawowy wzorzec tworzenia skalowalnych usług. W szerszym sensie można myśleć o partycjonowaniu jako koncepcji dzielenia stanu (danych) i obliczeń na mniejsze jednostki dostępne w celu poprawy skalowalności i wydajności. Dobrze znana postać partycjonowania to [Partycjonowanie danych][wikipartition], znane także jako fragmentowania.

### <a name="partition-service-fabric-stateless-services"></a>Podziel na partycje Service Fabric usługi bezstanowe
W przypadku usług bezstanowych możesz zastanowić się, że partycja jest jednostką logiczną, która zawiera co najmniej jedno wystąpienie usługi. Rysunek 1 przedstawia usługę bezstanową z pięcioma wystąpieniami rozproszonymi w klastrze przy użyciu jednej partycji.

![Usługa bezstanowa](./media/service-fabric-concepts-partitioning/statelessinstances.png)

W rzeczywistości istnieją dwa typy rozwiązań usługi bezstanowej. Pierwszym z nich jest usługa, która zachowuje swój stan zewnętrznie, na przykład w bazie danych w Azure SQL Database (np. w witrynie sieci Web, w której przechowywane są informacje o sesji i dane). Druga z nich to usługi wyłącznie obliczeniowe (takie jak Kalkulator lub miniatury obrazów), które nie zarządzają stanem trwałym.

W obu przypadkach partycjonowanie usługi bezstanowej jest bardzo rzadkim scenariuszem — skalowalność i dostępność są zwykle osiągane przez dodanie większej liczby wystąpień. Jedyną sytuacją, w której należy rozważyć użycie wielu partycji dla wystąpień usługi bezstanowej, jest to, że należy spełnić specjalne żądania routingu.

Na przykład rozważmy przypadek, w którym użytkownicy z identyfikatorami w określonym zakresie powinni być obsługiwani tylko przez określone wystąpienie usługi. Innym przykładem sytuacji, w której można podzielić na partycje usługę bezstanową, jest to, że istnieje naprawdę podzielona na partycje zaplecze (np. baza danych podzielonej na fragmenty w SQL Database) i chcesz określić, które wystąpienie usługi ma zapisywać w bazie danych fragmentu, lub wykonać inne czynności przygotowawcze w ramach usługi bezstanowej, które wymagają tych samych informacji partycjonowania, co jest używane w zapleczu. Te typy scenariuszy można również rozwiązać na różne sposoby i niekoniecznie wymagać partycjonowania usługi.

Pozostała część tego instruktażu koncentruje się na usługach stanowych.

### <a name="partition-service-fabric-stateful-services"></a>Podziel na partycje Service Fabric usługi stanowe
Service Fabric ułatwia opracowywanie skalowalnych usług stanowych poprzez oferowanie pierwszej klasy w celu partycjonowania stanu (danych). Koncepcyjnie można myśleć o partycji usługi stanowej jako jednostki skalowania, która jest wysoce niezawodna za pośrednictwem [replik](service-fabric-availability-services.md) dystrybuowanych i zrównoważonych w węzłach w klastrze.

Partycjonowanie w kontekście Service Fabric usługi stanowe odnosi się do procesu określania, że określona partycja usługi jest odpowiedzialna za część pełnego stanu usługi. (Jak wspomniano wcześniej, partycja jest zestawem [replik](service-fabric-availability-services.md)). Service Fabric polega na tym, że umieszcza partycje w różnych węzłach. Dzięki temu można zwiększyć limit zasobów węzła. Gdy dane wymagają powiększania, zwiększają się partycje i Service Fabric ponownie zrównoważą partycje między węzłami. Zapewnia to ciągłe wydajne wykorzystanie zasobów sprzętowych.

Aby podać przykład, należy zacząć od klastra z 5 węzłami i usługi, która ma 10 partycji i element docelowy trzech replik. W takim przypadku Service Fabric zrównoważą i dystrybuują repliki w klastrze — a następnie można wykonać dwie [repliki](service-fabric-availability-services.md) podstawowe na węzeł.
Jeśli teraz trzeba skalować klaster do 10 węzłów, Service Fabric będzie zrównoważyć [repliki](service-fabric-availability-services.md) podstawowe na wszystkich 10 węzłach. Podobnie, jeśli przeskaluje się z powrotem do 5 węzłów, Service Fabric będzie zrównoważyć wszystkie repliki w 5 węzłach.  

Rysunek 2 przedstawia rozkład 10 partycji przed i po skalowaniu klastra.

![Usługa stanowa](./media/service-fabric-concepts-partitioning/partitions.png)

W efekcie skalowanie jest osiągane, ponieważ żądania od klientów są dystrybuowane między komputerami, ogólna wydajność aplikacji jest ulepszona i rywalizacja o dostęp do fragmentów danych jest ograniczona.

## <a name="plan-for-partitioning"></a>Planowanie partycjonowania
Przed zaimplementowaniem usługi należy zawsze rozważyć strategię partycjonowania, która jest wymagana do skalowania w poziomie. Istnieją różne sposoby, ale wszystkie z nich koncentrują się na tym, co aplikacja musi osiągnąć. W kontekście tego artykułu Rozważmy niektóre z ważniejszych aspektów.

Dobrym rozwiązaniem jest pomyśleć o strukturze stanu, który należy podzielić na partycje, jako pierwszy krok.

Zróbmy prosty przykład. W przypadku kompilowania usługi dla sondy obejmującej całe województwa można utworzyć partycję dla każdego miasta w powiatzie. Następnie można przechowywać głosy dla każdej osoby w mieście na partycji, która odnosi się do tego miasta. Rysunek 3 ilustruje zbiór osób i miasto, w którym się znajdują.

![Prosta partycja](./media/service-fabric-concepts-partitioning/cities.png)

W miarę jak populacja miejscowości różni się w zależności od tego, co można zrobić w przypadku niektórych partycji zawierających wiele danych (np. Seattle) i innych partycji z bardzo małym stanem (np. Kirkland). Co to jest wpływ istnienia partycji z nierównomiernymi ilościami stanu?

Jeśli myślisz o tym przykładzie ponownie, możesz łatwo zobaczyć, że partycja, która przechowuje głosy dla Seattle, uzyska więcej ruchu niż Kirkland. Domyślnie Service Fabric zapewnia, że w każdym węźle istnieje taka sama liczba replik podstawowych i pomocniczych. Można więc zakończyć z węzłami, które przechowują repliki, które obsługują większy ruch i inne, które obsługują mniejszy ruch. Najlepiej, aby uniknąć gorąca i zimnych miejsc, takich jak ten w klastrze.

Aby tego uniknąć, należy wykonać dwie czynności z punktu partycjonowania:

* Spróbuj podzielić stan na partycje, tak aby był równomiernie dystrybuowany we wszystkich partycjach.
* Raportowanie obciążenia dla każdej repliki usługi. (Aby uzyskać więcej informacji na temat, zobacz artykuł dotyczący [metryk i obciążenia](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric zapewnia możliwość raportowania obciążenia zużywanego przez usługi, takie jak ilość pamięci lub liczba rekordów. W oparciu o raportowane metryki Service Fabric wykrywa, że niektóre partycje obsługują wyższe obciążenia niż inne i ponownie równoważą klaster, przenosząc repliki do bardziej odpowiednich węzłów, tak aby cały węzeł nie był przeciążony.

Czasami nie można znać ilości danych znajdujących się w danej partycji. W związku z tym zaleca się wykonanie obu tych czynności — najpierw przez zastosowanie strategii partycjonowania, która rozkłada dane równomiernie między partycjami i sekundę, przez raportowanie obciążeń.  Pierwsza metoda zapobiega sytuacjom opisanym w przykładzie głosowania, podczas gdy druga pomaga w rozpewnieniu tymczasowych różnic między dostępem lub obciążeniem w czasie.

Innym aspektem planowania partycji jest wybranie poprawnej liczby partycji.
Z perspektywy Service Fabric nie ma nic, co uniemożliwia rozpoczęcie pracy z większą liczbą partycji niż przewidywane dla Twojego scenariusza.
W rzeczywistości zakłada się, że maksymalna liczba partycji jest prawidłowym podejściem.

W rzadkich przypadkach może się okazać konieczna potrzeba większej liczby partycji niż początkowo. Ponieważ nie można zmienić liczby partycji po fakcie, należy zastosować niektóre zaawansowane podejścia do partycji, takie jak tworzenie nowego wystąpienia usługi tego samego typu usługi. Należy również zaimplementować część logiki po stronie klienta, która kieruje żądania do poprawnego wystąpienia usługi na podstawie wiedzy po stronie klienta, którą musi zachować kod klienta.

Inną kwestią dla planowania partycjonowania jest dostęp do zasobów komputera. W miarę jak stan musi być dostępny i zapisany, można wykonać następujące czynności:

* Limity przepustowości sieci
* Limity pamięci systemowej
* Limity magazynu dyskowego

Co się stanie w przypadku uruchamiania ograniczeń zasobów w uruchomionym klastrze? Odpowiedź polega na tym, że można po prostu skalować klaster w celu uwzględnienia nowych wymagań.

[Przewodnik planowania pojemności](service-fabric-capacity-planning.md) zawiera wskazówki dotyczące sposobu określania, ile węzłów potrzebuje klaster.

## <a name="get-started-with-partitioning"></a>Wprowadzenie do partycjonowania
W tej sekcji opisano, jak rozpocząć pracę z partycjonowaniem usługi.

Service Fabric oferuje trzy Schematy partycji:

* Partycjonowanie w zakresie (nazywane w inny sposób jako UniformInt64Partition).
* Nazwane partycjonowanie. Aplikacje korzystające z tego modelu zwykle mają dane, które można przetworzyć w określonym zestawie. Niektóre typowe przykłady pól danych używanych jako klucze partycji nazwanych to regiony, kody pocztowe, grupy klientów lub inne granice biznesowe.
* Partycjonowanie pojedyncze. Partycje pojedyncze są zwykle używane, gdy usługa nie wymaga dodatkowego routingu. Na przykład usługi bezstanowe korzystają domyślnie z tego schematu partycjonowania.

Schematy partycjonowania nazwane i singleton są specjalnymi formularzami partycji z zakresem. Domyślnie szablony programu Visual Studio dla Service Fabric używają partycjonowania z zakresem, ponieważ jest to najbardziej powszechny i przydatny. Pozostała część tego artykułu koncentruje się na schemacie partycjonowania w zakresie.

### <a name="ranged-partitioning-scheme"></a>Schemat partycjonowania w zakresie
Służy do określania zakresu liczb całkowitych (identyfikowanego za pomocą klucza niskiego i wysokiego) oraz liczby partycji (n). Tworzy n partycji, każdy odpowiada dla nienakładających się podzakresów ogólnego zakresu kluczy partycji. Na przykład schemat partycjonowania z zakresem o niskim kluczu równy 0, wysoki klucz 99, a liczba 4 utworzy cztery partycje, jak pokazano poniżej.

![Partycjonowanie zakresu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Typowym podejściem jest utworzenie skrótu opartego na unikatowym kluczu w zestawie danych. Typowymi przykładami kluczy będzie numer identyfikacyjny pojazdu (VIN), identyfikator pracownika lub unikatowy ciąg. Za pomocą tego unikatowego klucza, można wygenerować kod skrótu, modulo zakres klucza do użycia jako klucz. Można określić górne i dolne granice dozwolonego zakresu kluczy.

### <a name="select-a-hash-algorithm"></a>Wybierz algorytm wyznaczania wartości skrótu
Istotną częścią mieszania jest wybranie algorytmu wyznaczania wartości skrótu. Należy wziąć pod uwagę, czy celem jest grupowanie podobnych kluczy blisko siebie (mieszanie poufne), czy działanie powinno być rozproszone w szerokim stopniu dla wszystkich partycji (mieszanie dystrybucji), co jest bardziej popularne.

Charakterystyka dobrego algorytmu skrótu dystrybucji jest łatwa do obliczenia, ma kilka kolizji i równomiernie rozkłada klucze. Dobrym przykładem wydajnego algorytmu wyznaczania wartości skrótu jest algorytm wyznaczania wartości skrótu [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) .

Dobrym zasobem dla opcji ogólnego algorytmu kodu skrótu jest [Strona Wikipedia w funkcjach mieszania](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Tworzenie usługi stanowej z wieloma partycjami
Utwórzmy swoją pierwszą niezawodną usługę stanową z wieloma partycjami. W tym przykładzie utworzysz prostą aplikację, w której chcesz przechowywać wszystkie nazwiska, które zaczynają się od tej samej litery w tej samej partycji.

Przed zapisaniem dowolnego kodu należy wziąć pod uwagę partycje i klucze partycji. Potrzebujesz 26 partycji (po jednym dla każdej litery w alfabecie), ale co o niskim i wysokim kluczu?
Ponieważ firma Microsoft chce mieć jedną partycję na literę, można użyć 0 jako klucza niskiego i 25 jako klucza wysokiego, ponieważ każda litera jest własnym kluczem.

> [!NOTE]
> Jest to uproszczony scenariusz, ponieważ w rzeczywistości dystrybucja będzie nierówna. Nazwiska zaczynające się od liter "S" lub "M" są bardziej typowe niż te, które zaczynają się od "X" lub "Y".
> 
> 

1. Otwórz program **Visual Studio**  >  **plik**  >  **Nowy**  >  **projekt**.
2. W oknie dialogowym **Nowy projekt** wybierz aplikację Service Fabric.
3. Wywoływanie projektu "AlphabetPartitions".
4. W oknie dialogowym **Tworzenie usługi** wybierz pozycję Usługa **stanowa** i Wywołaj ją "alfabet. Processing".
5. Ustaw liczbę partycji. Otwórz plik Applicationmanifest.xml znajdujący się w folderze ApplicationPackageRoot projektu AlphabetPartitions i zaktualizuj parametr Processing_PartitionCount do 26, jak pokazano poniżej.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Należy również zaktualizować właściwości LowKey i HighKey elementu klasy statefulservice w ApplicationManifest.xml, jak pokazano poniżej.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Aby można było uzyskać dostęp do usługi, Otwórz punkt końcowy na porcie przez dodanie elementu punktu końcowego ServiceManifest.xml (znajdującego się w folderze PackageRoot) usługi alfabetu. Processing, jak pokazano poniżej:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Teraz usługa jest skonfigurowana do nasłuchiwania wewnętrznego punktu końcowego z 26 partycjami.
7. Następnie należy zastąpić `CreateServiceReplicaListeners()` metodę klasy przetwarzania.
   
   > [!NOTE]
   > W tym przykładzie przyjęto założenie, że używasz prostego HttpCommunicationListener. Aby uzyskać więcej informacji na temat niezawodnej komunikacji z usługą, zobacz [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Zalecany wzorzec dla adresu URL, na którym nasłuchuje replika, to następujący format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Dlatego chcesz skonfigurować odbiornik komunikacji do nasłuchiwania na prawidłowych punktach końcowych i z tym wzorcem.
   
    Wiele replik tej usługi może być hostowanych na tym samym komputerze, więc ten adres musi być unikatowy dla repliki. To dlatego, że identyfikator partycji i identyfikator repliki są w adresie URL. Odbiornika HttpListener może nasłuchiwać wielu adresów na tym samym porcie, o ile prefiks adresu URL jest unikatowy.
   
    Dodatkowy identyfikator GUID jest przeznaczony dla zaawansowanej sytuacji, w której repliki pomocnicze także nasłuchują na żądania tylko do odczytu. W takim przypadku warto upewnić się, że nowy unikatowy adres jest używany podczas przechodzenia z podstawowego do pomocniczego, aby wymusić klientom ponowne rozwiązanie adresu. "+" jest używany w tym miejscu jako adres, dzięki czemu replika nasłuchuje na wszystkich dostępnych hostach (IP, FQDN, localhost itp.) Poniższy kod przedstawia przykład.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Warto również zauważyć, że opublikowany adres URL różni się nieco od prefiksu adresu URL nasłuchiwania.
    Adres URL nasłuchiwania jest przyznany do odbiornika HttpListener. Opublikowany adres URL to adres URL, który jest publikowany w Usługa nazewnictwa Service Fabric, który jest używany do odnajdowania usług. Klienci będą proszeni o ten adres za poorednictwem tej usługi odnajdywania. Aby nawiązać połączenie, wymagany jest adres IP lub nazwa FQDN węzła. Należy więc zamienić znak "+" na adres IP lub nazwę FQDN węzła, jak pokazano powyżej.
9. Ostatnim krokiem jest dodanie logiki przetwarzania do usługi, jak pokazano poniżej.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` odczytuje wartości parametru ciągu zapytania używanego do wywołania partycji i wywołań, `AddUserAsync` Aby dodać LastName do niezawodnego słownika `dictionary` .
10. Dodajmy do projektu usługę bezstanową, aby zobaczyć, jak można wywołać określoną partycję.
    
    Ta usługa służy jako prosty interfejs sieci Web, który akceptuje nazwisko jako parametr ciągu zapytania, określa klucz partycji i wysyła go do alfabetu. przetwarzanie usługi do przetworzenia.
11. W oknie dialogowym **Tworzenie usługi** wybierz pozycję Usługa **bezstanowa** i Wywołaj ją "alfabet. Web", jak pokazano poniżej.
    
    ![Zrzut ekranu usługi bezstanowej](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Zaktualizuj informacje o punkcie końcowym w ServiceManifest.xml usługi alfabet. WebApi, aby otworzyć port, jak pokazano poniżej.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Musisz zwrócić kolekcję ServiceInstanceListeners w sieci Web klasy. Ponownie możesz wybrać opcję wdrożenia prostego HttpCommunicationListener.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Teraz musisz zaimplementować logikę przetwarzania. HttpCommunicationListener wywołuje się, `ProcessInputRequest` gdy żądanie się znajduje. Dodajmy Poniższy kod.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Przejdźmy do niego krok po kroku. Kod odczytuje pierwszą literę parametru ciągu zapytania `lastname` do znaku. Następnie określa klucz partycji dla tego listu, odejmując wartość szesnastkową `A` od wartości szesnastkowej z ostatniej litery "imiona".
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Należy pamiętać, że w tym przykładzie używamy 26 partycji z jednym kluczem partycji na partycję.
    Następnie uzyskujemy partycję usługi `partition` dla tego klucza przy użyciu `ResolveAsync` metody dla `servicePartitionResolver` obiektu. `servicePartitionResolver` jest zdefiniowane jako
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync`Metoda przyjmuje identyfikator URI usługi, klucz partycji i token anulowania jako parametry. Identyfikator URI usługi dla usługi przetwarzania to `fabric:/AlphabetPartitions/Processing` . Następnie uzyskujemy punkt końcowy partycji.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Na koniec tworzymy adres URL punktu końcowego oraz ciąg QueryString i Wywołaj usługę przetwarzania.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po zakończeniu przetwarzania zapiszemy dane wyjściowe.
15. Ostatnim krokiem jest przetestowanie usługi. Program Visual Studio używa parametrów aplikacji do wdrożenia lokalnego i w chmurze. Aby przetestować usługę z 26 partycjami lokalnie, należy zaktualizować `Local.xml` plik w folderze elementu applicationparameters projektu AlphabetPartitions, jak pokazano poniżej:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po zakończeniu wdrażania możesz sprawdzić, czy usługa i wszystkie jej partycje w Service Fabric Explorer.
    
    ![Zrzut ekranu Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. W przeglądarce można przetestować logikę partycjonowania, wprowadzając polecenie `http://localhost:8081/?lastname=somename` . Zobaczysz, że wszystkie nazwiska zaczynające się od tej samej litery są przechowywane w tej samej partycji.
    
    ![Zrzut ekranu przeglądarki](./media/service-fabric-concepts-partitioning/samplerunning.png)

Kompletne rozwiązanie kodu użytego w tym artykule jest dostępne tutaj: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat usług Service Fabric Services:

* [Łączenie się z usługami w Service Fabric i komunikowanie się z nimi](service-fabric-connect-and-communicate-with-services.md)
* [Dostępność usług Service Fabric Services](service-fabric-availability-services.md)
* [Skalowalność usług Service Fabric Services](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
