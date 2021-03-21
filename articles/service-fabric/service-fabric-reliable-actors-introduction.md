---
title: Omówienie Reliable Actors Service Fabric
description: Wprowadzenie do modelu programowania Reliable Actors Service Fabric na podstawie wzorca aktora wirtualnego.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576387"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Wprowadzenie do Service Fabric Reliable Actors
Reliable Actors to Service Fabric struktura aplikacji oparta na wzorcu [aktora wirtualnego](https://research.microsoft.com/en-us/projects/orleans/) . Interfejs API Reliable Actors zapewnia jednowątkowy model programowania oparty na skalowalności i gwarancjach niezawodności zapewnianych przez Service Fabric.

## <a name="what-are-actors"></a>Co to są aktorzy?
Aktor jest izolowaną, niezależną jednostką obliczeniową i stanem za pomocą jednowątkowego wykonania. [Wzorzec aktora](https://en.wikipedia.org/wiki/Actor_model) to model obliczeniowy dla systemów współbieżnych lub rozproszonych, w których duża liczba tych aktorów może być jednocześnie wykonywana i niezależnie od siebie. Aktory mogą komunikować się ze sobą i mogą tworzyć więcej aktorów.

### <a name="when-to-use-reliable-actors"></a>Kiedy używać Reliable Actors
Service Fabric Reliable Actors jest implementacją wzorca projektowego aktora. Podobnie jak w przypadku każdego wzorca projektowego, decyzja o tym, czy należy używać określonego wzorca, jest podejmowana na podstawie tego, czy problem z projektem oprogramowania pasuje do wzorca.

Chociaż wzorzec projekt aktora może być dobrze dopasowany do szeregu problemów z systemami rozproszonymi i scenariuszy, należy dokładnie rozważyć ograniczenia wzorca i struktury implementującej ją. Jako ogólne wskazówki Rozważmy wzór aktora do modelowania problemu lub scenariusza, jeśli:

* Przestrzeń problemu obejmuje dużą liczbę (w tysiącach lub więcej) małych, niezależnych i izolowanych jednostek stanu i logiki.
* Chcesz współpracować z obiektami jednowątkowymi, które nie wymagają znaczącej interakcji ze składnikami zewnętrznymi, w tym stanu zapytań w zestawie aktorów.
* Wystąpienia aktora nie blokują wywoływania z nieprzewidywalnymi opóźnieniami przez wystawienie operacji we/wy.

## <a name="actors-in-service-fabric"></a>Aktory w Service Fabric
W Service Fabric, aktory są zaimplementowane w Reliable Actors Framework: struktura aplikacji oparta na wzorcu aktora utworzona w oparciu o [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Każda usługa niezawodnej aktora jest w rzeczywistości podzielona na partycje, niezawodna usługa.

Każdy aktor jest definiowany jako wystąpienie typu aktora, tak samo jak obiekt .NET jest wystąpieniem typu .NET. Na przykład może istnieć typ aktora, który implementuje funkcje kalkulatora i może istnieć wiele aktorów tego typu, które są dystrybuowane w różnych węzłach w klastrze. Każdy taki aktor jest jednoznacznie identyfikowany przez identyfikator aktora.

## <a name="actor-lifetime"></a>Okres istnienia aktora
Service Fabric aktorzy są wirtualne, co oznacza, że ich okres istnienia nie jest powiązany z ich reprezentacją w pamięci. W związku z tym nie trzeba ich jawnie tworzyć ani niszczyć. Środowisko uruchomieniowe Reliable Actors automatycznie aktywuje aktora po raz pierwszy odbiera żądanie dla tego identyfikatora aktora. Jeśli aktor nie jest używany przez pewien czas, środowisko uruchomieniowe Reliable Actors bezużyteczne — zbiera obiekt w pamięci. Będzie również mieć wiedzę o istnieniu aktora, jeśli trzeba będzie ponownie aktywować go później. Aby uzyskać więcej informacji, zobacz [cykl życia aktora i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md).

Ta Abstrakcja okresu istnienia aktora wirtualnego przenosi pewne zastrzeżenia w wyniku wirtualnego modelu aktora, a w rzeczywistości implementacja Reliable Actors jest odbiegać od momentu tego modelu.

* Aktor jest uaktywniany automatycznie (powodując skonstruowanie obiektu aktora) podczas pierwszego wysyłania komunikatu do jego identyfikatora aktora. Po pewnym czasie obiekt aktora zostanie wyrzucony jako elementy bezużyteczne. W przyszłości ponowne użycie identyfikatora aktora powoduje, że tworzony jest nowy obiekt aktora. Stan aktora jest aktywny w okresie istnienia obiektu, gdy jest przechowywany w Menedżerze stanu.
* Wywołanie dowolnej metody aktora dla identyfikatora aktora aktywuje ten aktor. Z tego powodu typy aktorów mają Konstruktor wywoływany niejawnie przez środowisko uruchomieniowe. W związku z tym kod klienta nie może przekazać parametrów do konstruktora typu aktora, chociaż parametry mogą być przekazywane do konstruktora aktora za pomocą samej usługi. Wynikiem jest to, że aktory mogą być zbudowane w stanie częściowo zainicjowanym przez czas, w którym są wywoływane inne metody, jeśli aktor wymaga parametrów inicjujących od klienta. Nie istnieje pojedynczy punkt wejścia dla aktywacji aktora od klienta.
* Mimo że Reliable Actors niejawnie tworzyć obiekty aktora; Istnieje możliwość jawnego usunięcia aktora i jego stanu.

## <a name="distribution-and-failover"></a>Dystrybucja i tryb failover
Aby zapewnić skalowalność i niezawodność, Service Fabric dystrybuuje aktorów w całym klastrze i automatycznie migruje je z uszkodzonych węzłów do określonych w razie potrzeby. Jest to streszczenie w przypadku [partycjonowanej i niezawodnej usługi bezstanowej](service-fabric-concepts-partitioning.md). Dystrybucja, skalowalność, niezawodność i automatyczne przełączanie w tryb failover są dostępne na podstawie faktu, że aktory działają wewnątrz niezawodnej usługi o nazwie " *Usługa aktora*".

Aktory są dystrybuowane między partycjami usługi aktora, a te partycje są dystrybuowane między węzłami w klastrze Service Fabric. Każda partycja usługi zawiera zestaw aktorów. Service Fabric zarządza dystrybucją i trybem failover partycji usługi.

Na przykład usługa aktora z dziewięcioma partycjami wdrożonymi na trzech węzłach przy użyciu domyślnego położenia partycji aktora będzie dystrybuowana w następujący sposób:

![Dystrybucja Reliable Actors][2]

Struktura aktora zarządza ustawieniami schematu partycji i zakresu kluczy. Upraszcza to pewne możliwości, ale również zawiera pewne zagadnienia:

* Reliable Services umożliwia wybranie schematu partycjonowania, zakresu kluczy (przy użyciu schematu partycjonowania zakresu) i liczby partycji. Reliable Actors jest ograniczone do schematu partycjonowania zakresu (jednolity schemat Int64) i wymaga użycia pełnego zakresu klucza Int64.
* Domyślnie aktory są losowo umieszczane w partycjach, co powoduje jednorodną dystrybucję.
* Ponieważ aktory są losowo umieszczane, należy oczekiwać, że operacje aktora zawsze wymagają komunikacji sieciowej, w tym serializacji i deserializacji danych wywołań metod, ponoszenia opóźnień i obciążeń.
* W zaawansowanych scenariuszach można kontrolować umieszczanie partycji aktora przy użyciu identyfikatorów aktorów Int64, które są mapowane na określone partycje. Jednak może to spowodować niezrównoważoną dystrybucję aktorów między partycjami.

Aby uzyskać więcej informacji na temat partycjonowania usług aktora, zapoznaj się z [pojęciami partycjonowania dla aktorów](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikacja aktora
Interakcje aktora są zdefiniowane w interfejsie, który jest współużytkowany przez aktora implementujący interfejs, i klient, który pobiera serwer proxy do aktora za pośrednictwem tego samego interfejsu. Ponieważ ten interfejs jest używany do asynchronicznego wywoływania metod aktora, każda metoda w interfejsie musi być zwracająca zadanie.

Wywołania metod i ich odpowiedzi ostatecznie powodują żądania sieciowe w ramach klastra, dlatego argumenty i typy wyników zadań, które zwracają, muszą być serializowane przez platformę. W szczególności muszą mieć możliwość [serializacji kontraktu danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Serwer proxy aktora
Interfejs API klienta Reliable Actors zapewnia komunikację między wystąpieniem aktora a klientem aktora. Aby komunikować się z aktorem, klient tworzy obiekt proxy aktora, który implementuje interfejs aktora. Klient współdziała z aktorem przez wywoływanie metod na obiekcie serwera proxy. Serwer proxy aktora może służyć do komunikacji między klientem i aktorem.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Należy zauważyć, że dwie części informacji użytych do utworzenia obiektu serwera proxy aktora są IDENTYFIKATORem aktora i nazwą aplikacji. Identyfikator aktora jednoznacznie identyfikuje aktora, podczas gdy nazwa aplikacji identyfikuje [Service Fabric aplikację](service-fabric-reliable-actors-platform.md#application-model) , w której jest wdrażany aktor.

`ActorProxy`Klasa (C#)/ `ActorProxyBase` (Java) po stronie klienta wykonuje niezbędne rozwiązanie do lokalizowania aktora według identyfikatora i otwierania kanału komunikacyjnego z nim. Ponawia również próbę zlokalizowania aktora w przypadku awarii komunikacji i trybu failover. W związku z tym dostarczanie komunikatów ma następujące cechy:

* Dostarczanie komunikatów jest najlepszym rozwiązaniem.
* Aktory mogą odbierać duplikaty komunikatów z tego samego klienta.

## <a name="concurrency"></a>Współbieżność
Środowisko uruchomieniowe Reliable Actors zapewnia prosty model dostępu oparty na przewróceniu na potrzeby uzyskiwania dostępu do metod aktora. Oznacza to, że w dowolnym momencie nie można uaktywnić więcej niż jednego wątku w kodzie obiektu aktora. Dostęp oparty na włączeniu znacznie upraszcza współbieżne systemy, ponieważ nie ma potrzeby stosowania mechanizmów synchronizacji do dostępu do danych. Oznacza to również, że systemy muszą zostać zaprojektowane ze szczególnymi zagadnieniami dotyczącymi jednowątkowego charakteru dostępu dla każdego wystąpienia aktora.

* Pojedyncze wystąpienie aktora nie może przetworzyć więcej niż jednego żądania jednocześnie. Wystąpienie aktora może spowodować wąskie gardło przepływności, jeśli oczekiwane jest obsłużenie współbieżnych żądań.
* Aktory mogą zakleszczać się na siebie, jeśli istnieje cykliczne żądanie między dwoma aktorami, gdy żądanie zewnętrzne jest jednocześnie wykonywane do jednego z aktorów. Środowisko wykonawcze aktora będzie automatycznie przekroczyć limit czasu dla wywołań aktora i zgłosić wyjątek do obiektu wywołującego, aby przerwać możliwe sytuacje zakleszczenia.

![Komunikacja Reliable Actors][3]

### <a name="turn-based-access"></a>Dostęp oparty na włączeniu
Kolejka składa się z pełnego wykonania metody aktora w odpowiedzi na żądanie od innych aktorów lub klientów lub do całkowitego wykonania wywołania zwrotnego [Timer/przypomnienie](service-fabric-reliable-actors-timers-reminders.md) . Mimo że te metody i wywołania zwrotne są asynchroniczne, środowisko uruchomieniowe aktorów nie opuszcza ich. Należy całkowicie zakończyć działanie, aby można było włączyć nowe ustawienie. Innymi słowy, Metoda aktora lub wywołanie zwrotne czasomierz/przypomnienie, które jest aktualnie wykonywane, muszą być w pełni zakończone, zanim zostanie dozwolone nowe wywołanie metody lub wywołania zwrotnego. Metoda lub wywołanie zwrotne jest uznawane za zakończone, jeśli wykonanie zwróciło się z metody lub wywołania zwrotnego, a zadanie zwrócone przez metodę lub wywołanie zwrotne zostało zakończone. Warto naciskać, że współbieżność oparta na włączeniu jest przestrzegana nawet między różnymi metodami, czasomierzami i wywołaniami zwrotnymi.

Środowisko wykonawcze aktorów wymusza współbieżność opartą na przewróceniu przez uzyskanie blokady aktora na początku i zwolnienie blokady na końcu obrotu. W związku z tym współbieżność oparta na włączeniu jest wymuszana dla aktora, a nie między aktorami. Metody aktora i wywołania zwrotne typu Timer/przypomnienie mogą być wykonywane jednocześnie w imieniu różnych aktorów.

Poniższy przykład ilustruje powyższe koncepcje. Rozważmy typ aktora, który implementuje dwie metody asynchroniczne (Powiedz, *Metoda1* i *Method2*), czasomierz i przypomnienie. Na poniższym diagramie przedstawiono przykład osi czasu na potrzeby wykonywania tych metod i wywołań zwrotnych w imieniu dwóch aktorów (*ActorId1* i *ActorId2*), które należą do tego typu aktora.

![Współbieżność i dostęp oparty na włączeniu Reliable Actors Runtime][1]

Ten diagram jest zgodny z następującymi konwencjami:

* Każda linia pionowa pokazuje logiczny przepływ wykonywania metody lub wywołania zwrotnego w imieniu określonego aktora.
* Zdarzenia oznaczone w każdej linii pionowej są wykonywane w kolejności chronologicznej, a nowsze zdarzenia występują poniżej starszych.
* Różne kolory są używane na potrzeby osi czasu odpowiadających różnym aktorom.
* Wyróżnianie służy do wskazywania czasu trwania blokady aktora w imieniu metody lub wywołania zwrotnego.

Niektóre ważne kwestie, które należy wziąć pod uwagę:

* Podczas gdy *Metoda1* jest wykonywane w imieniu *ActorId2* w odpowiedzi na żądanie klienta *xyz789*, dociera inne żądanie klienta (*abc123*), które również wymaga, aby *Metoda1* zostało wykonane przez *ActorId2*. Jednak drugie wykonanie *Metoda1* nie rozpocznie się, dopóki nie zakończy się poprzednie wykonanie. Podobnie przypomnienie zarejestrowane przez *ActorId2* jest wyzwalane, podczas gdy *Metoda1* jest wykonywane w odpowiedzi na żądanie klienta *xyz789*. Wywołanie zwrotne przypomnienia jest wykonywane tylko po zakończeniu obu wykonań *Metoda1* . Wszystko to jest spowodowane wymuszaniem współbieżności opartej na przewróceniu dla *ActorId2*.
* Analogicznie współbieżność oparta na włączeniu jest również wymuszana dla *ActorId1*, jak pokazano w wyniku wykonania *Metoda1*, *Method2* i wywołania zwrotnego czasomierza w imieniu *ActorId1* , w sposób szeregowy.
* Wykonywanie *Metoda1* w imieniu *ActorId1* pokrywa się z jego wykonywaniem w imieniu *ActorId2*. Dzieje się tak, ponieważ współbieżność oparta na włączeniu jest wymuszana tylko w obrębie aktora, a nie między aktorami.
* W przypadku niektórych metod/wykonań wywołania zwrotnego `Task` (C#)/ `CompletableFuture` (Java) zwracanych przez metodę/wywołanie zwrotne po powrocie metody. W niektórych innych, operacja asynchroniczna została już zakończona przez czas, przez który zwraca metodę/wywołanie zwrotne. W obu przypadkach blokada dla aktora jest wydawana tylko po powrocie metody/wywołania zwrotnego i zakończenia operacji asynchronicznej.

### <a name="reentrancy"></a>Ponowne wejścia
Środowisko uruchomieniowe aktorów domyślnie zezwala na współużytkowania wątkowości. Oznacza to, że jeśli *obiekt aktora wywołuje metodę* w *aktorze B*, która z kolei wywołuje inną metodę w *aktorze a*, ta metoda może być uruchomiona. Jest to spowodowane tym, że jest to część tego samego kontekstu logicznego łańcucha wywołań. Wszystkie wywołania czasomierza i przypomnienia zaczynają się od nowego kontekstu wywołania logicznego. Aby uzyskać więcej informacji, zobacz [Reliable Actors współużytkowania wątkowości](service-fabric-reliable-actors-reentrancy.md) .

### <a name="scope-of-concurrency-guarantees"></a>Zakres gwarancji współbieżności
Środowisko wykonawcze aktorów zapewnia te gwarancje współbieżności w sytuacjach, w których kontroluje wywołania tych metod. Na przykład zapewnia te gwarancje dla wywołań metod, które są wykonywane w odpowiedzi na żądanie klienta, a także dla wywołań zwrotnych czasomierza i przypomnień. Jeśli jednak kod aktora bezpośrednio wywołuje te metody poza mechanizmami dostarczonymi przez środowisko uruchomieniowe aktorów, środowisko uruchomieniowe nie może zapewnić żadnych gwarancji współbieżności. Na przykład jeśli metoda jest wywoływana w kontekście niektórych zadań, które nie są skojarzone z zadaniem zwróconym przez metody aktora, środowisko uruchomieniowe nie może zapewnić gwarancji współbieżności. Jeśli metoda jest wywoływana z wątku tworzonego przez aktora, środowisko uruchomieniowe nie może również zapewnić gwarancji współbieżności. W związku z tym, aby wykonywać operacje w tle, aktory powinny korzystać z [czasomierzy aktora i przypomnień aktorów](service-fabric-reliable-actors-timers-reminders.md) , które respektują współbieżność.

## <a name="next-steps"></a>Następne kroki
Zacznij od utworzenia pierwszej Reliable Actorsej usługi:
   * [Wprowadzenie do Reliable Actors na platformie .NET](service-fabric-reliable-actors-get-started.md)
   * [Wprowadzenie do Reliable Actors w języku Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
