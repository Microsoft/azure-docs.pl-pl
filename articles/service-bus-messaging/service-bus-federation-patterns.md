---
title: Wzorce zadań replikacji komunikatów — Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera szczegółowe wskazówki dotyczące implementowania określonych wzorców zadań replikacji komunikatów
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657504"
---
# <a name="message-replication-tasks-patterns"></a>Wzorce zadań replikacji komunikatów

Omówienie [Federacji](service-bus-federation-overview.md) i usługi [replikatora](service-bus-federation-replicator-functions.md) wyjaśniają racjonalne znaczenie dla i podstawowych elementów zadań związanych z replikacją oraz zaleca się zapoznanie się z nimi przed kontynuowaniem pracy z tym artykułem.

W tym artykule szczegółowo przedstawiono wskazówki dotyczące implementacji kilku wzorców wyróżnionych w sekcji Przegląd. 

## <a name="replication"></a>Replikacja 

Wzorzec replikacji kopiuje komunikaty z jednej kolejki lub tematu do następnego lub z kolejki lub tematu do innego miejsca docelowego, takiego jak centrum zdarzeń. Komunikaty są przekazywane bez wprowadzania żadnych modyfikacji ładunku komunikatów. 

Implementacja tego wzorca jest objęta [replikacją komunikatów do i z](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) przykładu Azure Service Bus.

### <a name="sequences-and-order-preservation"></a>Sekwencje i zachowywanie kolejności

Model replikacji nie ma na celu zachowywania bezwzględnej kolejności komunikatów dla kolejki źródłowej lub tematu w docelowej kolejce lub temacie, ale również koncentruje się na zapewnieniu względnej kolejności komunikatów, w których ta aplikacja jest wymagana. Aplikacja umożliwia to przez włączenie obsługi sesji dla jednostki źródłowej i grupowanie powiązanych komunikatów przy użyciu tego samego [klucza sesji](message-sessions.md).

Wbudowane funkcje replikacji obsługujące sesję zapewniają, że sekwencje komunikatów z tym samym identyfikatorem sesji pobrane z jednostki źródłowej są przesyłane do docelowej kolejki lub tematu jako partia w oryginalnej sekwencji i z tym samym identyfikatorem sesji. 

### <a name="service-assigned-metadata"></a>Metadane przypisane do usługi 

Metadane przypisane do usługi wiadomości uzyskanych z kolejki źródłowej lub tematu, oryginalna godzina i numer sekwencyjny są zastępowane przez nowe wartości przypisane do usługi w docelowej kolejce lub temacie, ale z domyślnymi zadaniami replikacji, które są dostępne w naszych przykładach, oryginalne wartości są zachowywane we właściwościach użytkownika: `repl-enqueue-time` (ISO8601 ciąg) i `repl-sequence` .

Te właściwości są typu String i zawierają wartość skonwertowanej odpowiednich oryginalnych właściwości.  Jeśli komunikat zostanie przekierowany wielokrotnie, metadane bezpośrednie źródła są dołączane do już istniejących właściwości, z wartościami oddzielonymi średnikami. 

### <a name="failover"></a>Tryb failover

W przypadku korzystania z replikacji na potrzeby odzyskiwania po awarii w celu ochrony przed regionalnymi komunikatami o dostępności w usłudze Service Bus lub przed zakłóceniami sieci każdy taki scenariusz awarii będzie wymagał przełączenia w tryb failover z jednej kolejki lub tematu do kolejnego, poinformowania producentów i/lub konsumentów o konieczności użycia pomocniczego punktu końcowego.

W przypadku wszystkich scenariuszy trybu failover zakłada się, że wymagane elementy przestrzeni nazw są strukturalnie identyczne, co oznacza, że kolejki i tematy są identyczne o nazwie i reguły sygnatur dostępu współdzielonego i/lub reguły kontroli dostępu oparte na rolach zostały skonfigurowane w taki sam sposób. Można utworzyć (i zaktualizować) pomocniczą przestrzeń nazw, postępując zgodnie ze [wskazówkami dotyczącymi przesuwania przestrzeni nazw](move-across-regions.md) i pomijania kroku oczyszczania.

Aby wymusić przełączenie producentów i konsumentów, należy wprowadzić informacje o tym, która przestrzeń nazw ma być dostępna do wyszukania w lokalizacji, która jest łatwa do osiągnięcia i zaktualizowania. Jeśli producenci lub konsumenci napotykają częste lub trwałe błędy, powinni skonsultować się z tą lokalizacją i dostosować ich konfigurację. Istnieje wiele sposobów, aby udostępnić tę konfigurację, ale od tego momentu podaliśmy dwa elementy: DNS i udziały plików.

#### <a name="dns-based-failover-configuration"></a>Konfiguracja trybu failover oparta na systemie DNS

Jednym z kandydatów jest przechowywanie informacji w rekordach DNS SRV w kontrolowanej przez Ciebie systemie DNS i wskazanie odpowiednich punktów końcowych kolejki lub tematu. Należy pamiętać, że centra komunikatów nie zezwalają na bezpośrednie aliasowanie punktów końcowych przy użyciu rekordów CNAME, co oznacza, że serwer DNS będzie używany jako odporny na błędy adresy punktów końcowych, a nie do bezpośredniego rozpoznawania informacji o adresie IP. 

Załóżmy, że jesteś członkiem domeny `example.com` i, dla aplikacji, strefy `test.example.com` . W przypadku dwóch alternatywnych Service Bus teraz utworzysz dwie dalsze strefy zagnieżdżone i rekord SRV w każdym z nich. 

Rekordy SRV są zgodne ze wspólną Konwencją, poprzedzone `_azure_servicebus._amqp` i zawierają dwa rekordy punktów końcowych: jeden dla AMQP-over-TLS na porcie 5671 i jeden dla AMQP-over-WebSockets na porcie 443, oba wskazujące Service Bus punkt końcowy przestrzeni nazw odpowiadającej strefie.

| Strefa                 | Rekord SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

W strefie aplikacji utworzysz wpis CNAME wskazujący strefę podrzędną odpowiadającą nadrzędnej kolejce lub tematowi:

| Rekord CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Przy użyciu klienta DNS, który umożliwia jawne wykonywanie zapytań dotyczących rekordów CNAME i SRV (Wbudowani klienci języka Java i platformy .NET umożliwiają tylko proste rozpoznawanie nazw na adresy IP), można rozpoznać żądany punkt końcowy. W przypadku [DnsClient.NET](https://dnsclient.michaco.net/), na przykład, funkcja Lookup:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Funkcja zwraca wartość docelowej nazwy hosta zarejestrowanego dla portu 5671 strefy, która jest obecnie aliasem rekordu CNAME, jak pokazano powyżej. 

Wykonanie trybu failover wymaga edycji rekordu CNAME i wskazanie go w strefie alternatywnej. 

Zaletą korzystania z systemu DNS, a szczególnie [Azure DNS](../dns/dns-overview.md), jest to, że Azure DNS informacje są replikowane globalnie i w związku z tym odporne na awarie w jednym regionie.

Ta procedura jest podobna do sposobu działania [Service Bus geograficznego](service-bus-geo-dr.md) odzyskiwania po awarii, ale w pełni w ramach własnej kontrolki, a także współpracuje z aktywnymi/aktywnymi scenariuszami.

#### <a name="file-share-based-failover-configuration"></a>Konfiguracja trybu failover opartego na udziale plików

Najprostszą alternatywą dla korzystania z usługi DNS w celu udostępniania informacji o punkcie końcowym jest umieszczenie nazwy podstawowego punktu końcowego w pliku z tekstem zwykłym i obsłużynie pliku z infrastruktury, która jest niezawodna w odniesieniu do awarii i nadal zezwala na aktualizacje. 

Jeśli infrastruktura witryny sieci Web o wysokiej dostępności jest już uruchomiona z globalną dostępnością i replikacją zawartości, dodanie takiego pliku i ponowne opublikowanie go w przypadku konieczności przełączenia.

## <a name="merge"></a>Merge

Wzorzec scalania zawiera co najmniej jedno zadanie replikacji wskazujące jeden element docelowy, prawdopodobnie jednocześnie z regularnymi producentami, które wysyłają komunikaty do tego samego obiektu docelowego. 

Zmiany tego wzorca są następujące:
- Co najmniej dwie funkcje replikacji jednocześnie nabywają komunikaty z oddzielnych źródeł i wysyłają je do tego samego obiektu docelowego.
- Jedna większa funkcja replikacji, pobierająca komunikaty ze źródła, gdy element docelowy jest również używany bezpośrednio przez producentów. 
- Poprzedni wzorzec, ale wiadomości zdublowane między dwoma lub więcej tematów, co spowodowało, że te tematy zawierają te same komunikaty, niezależnie od miejsca, w którym są tworzone komunikaty.

Pierwsze dwie odmiany wzorców są proste i nie różnią się od zwykłych zadań replikacji.

Ostatni scenariusz wymaga ponownego replikowania już zreplikowanych komunikatów. Technika jest przedstawiona i wyjaśniona w przykładu aktywny/aktywny.

## <a name="editor"></a>Edytor

Wzorzec edytora kompiluje się w wzorcu [replikacji](#replication) , ale komunikaty są modyfikowane przed przesłaniem dalej. Przykłady dla takich modyfikacji:

- ***Transkodowanie*** — Jeśli zawartość komunikatu (nazywana również "treścią" lub "ładunek") dotarła ze źródła zakodowanego przy użyciu formatu *Apache Avro* lub z niezależnym formatem serializacji, jednak oczekiwany system posiadający obiekt docelowy to zawartość, która ma być zakodowana w *notacji JSON* , zadanie replikacji transkodowanej najpierw dekoduje ładunek z *Apache Avro* do grafu obiektów w pamięci, a następnie serializować ten wykres do formatu *JSON* dla przesyłanego dalej komunikatu. Transkodowanie obejmuje również zadania **kompresji zawartości** i dekompresji.
- ***Transformacja*** — komunikaty zawierające dane strukturalne mogą wymagać zmiany kształtu tych danych w celu łatwiejszego wykorzystania przez użytkowników podrzędnych. Może to dotyczyć takich zadań, jak spłaszczanie zagnieżdżonych struktur, oczyszczanie nadmiarowych elementów danych lub zmiana kształtu ładunku, aby dokładnie dopasować dany schemat.
- ***Przetwarzanie wsadowe*** — komunikaty mogą być odbierane w partiach (wiele komunikatów w jednym transferze) ze źródła, ale muszą być przekazywane pojedynczo do miejsca docelowego lub na odwrót. Zadanie może zatem przekazywać wiele komunikatów w oparciu o pojedynczy transfer komunikatów wejściowych lub agregowanie zestawu komunikatów, które są następnie przesyłane razem. 
- ***Walidacja*** — dane komunikatów ze źródeł zewnętrznych często muszą być sprawdzane, czy są zgodne z zestawem reguł, zanim będą mogły zostać przesłane dalej. Reguły mogą być wyrażone przy użyciu schematów lub kodu. komunikaty, które nie są zgodne, mogą zostać porzucone, z problemem zanotowanym w dziennikach lub mogą być przekazywane do specjalnego docelowego miejsca docelowego, aby można je było dalej obsłużyć.   
- ***Wzbogacanie*** danych komunikatów pochodzących z niektórych źródeł może wymagać wzbogacania z dodatkowym kontekstem, aby można było go używać w systemach docelowych. Może to oznaczać wyszukiwanie danych referencyjnych i osadzenie tych danych wraz z komunikatem lub dodanie informacji o źródle znanym przez zadanie replikacji, ale nie zawarte w komunikatach. 
- ***Filtrowanie*** — niektóre komunikaty przychodzące ze źródła mogą wymagać wstrzymania z elementu docelowego na podstawie pewnej reguły. Filtr testuje wiadomość względem reguły i odrzuca komunikat, jeśli komunikat nie jest zgodny z regułą. Filtrowanie zduplikowanych komunikatów przez zaobserwowanie pewnych kryteriów i usunięcie kolejnych komunikatów z tymi samymi wartościami jest formą filtrowania.
- ***Routing i partycjonowanie*** — niektóre zadania związane z replikacją mogą zezwalać na co najmniej dwa alternatywne cele oraz definiować reguły dla danego elementu docelowego replikacji dla konkretnej wiadomości na podstawie metadanych lub zawartości komunikatu. Specjalną formą routingu jest partycjonowanie, gdzie zadanie jawnie przypisuje partycje w jednym miejscu docelowym replikacji na podstawie reguł.
- ***Kryptografia*** — zadanie replikacji może być konieczne do odszyfrowania zawartości przychodzącej ze źródła i/lub zaszyfrowania zawartości przesłanej do miejsca docelowego i/lub może być konieczne zweryfikowanie integralności zawartości i metadanych względem podpisu przeprowadzonego w komunikacie lub dołączenie takiego podpisu. 
- ***Zaświadczanie*** — zadanie replikacji może dołączyć metadane, potencjalnie chronione przez podpis cyfrowy, do wiadomości, która zaświadcza, że wiadomość została odebrana za pośrednictwem określonego kanału lub w określonym czasie.     
- ***Łączenie łańcuchowe*** — zadanie replikacji może stosować podpisy do sekwencji komunikatów w taki sposób, aby integralność sekwencji była chroniona i nie można wykryć brakujących komunikatów.

Wszystkie te wzorce można zaimplementować przy użyciu Azure Functions, przy użyciu [wyzwalacza centrów komunikatów](../azure-functions/functions-bindings-service-bus-trigger.md) do uzyskiwania komunikatów oraz [powiązania kolejki lub tematu](../azure-functions/functions-bindings-service-bus-output.md) w celu ich dostarczania. 

## <a name="routing"></a>Routing

Wzorzec routingu kompiluje się w wzorcu [replikacji](#replication) , ale zamiast jednego źródła i jednego obiektu docelowego, zadanie replikacji ma wiele obiektów docelowych przedstawionych w tym miejscu w języku C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Funkcja routingu będzie uwzględniać metadane komunikatów i/lub ładunek wiadomości, a następnie wybrać jeden z dostępnych miejsc docelowych do wysłania.

## <a name="next-steps"></a>Następne kroki

- [aplikacje Replikator Message w Azure Functions][1]
- [Replikowanie komunikatów między Service Bus][2]
- [Replikowanie komunikatów do usługi Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub