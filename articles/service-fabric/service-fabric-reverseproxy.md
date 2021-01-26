---
title: Zwrotny serwer proxy Service Fabric platformy Azure
description: Użyj zwrotnego serwera proxy Service Fabric do komunikacji z mikrousługami z wewnątrz i na zewnątrz klastra.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791108"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Zwrotny serwer proxy na platformie Azure Service Fabric
Zwrotny serwer proxy wbudowany w usługę Azure Service Fabric ułatwia mikrousługi działające w klastrze Service Fabric odnajdywania i komunikowania się z innymi usługami, które mają punkty końcowe http.

## <a name="microservices-communication-model"></a>Model komunikacji mikrousług
Mikrousługi w Service Fabric działają na podzestawie węzłów w klastrze i mogą migrować między węzłami z różnych powodów. W efekcie punkty końcowe dla mikrousług mogą zmieniać się dynamicznie. Aby odnajdywać i komunikować się z innymi usługami w klastrze, należy wykonać następujące czynności:

1. Rozpoznaj lokalizację usługi za pomocą usługi nazewnictwa.
2. Połącz się z usługą.
3. Zawiń poprzednie kroki w pętli implementującej rozwiązanie usługi i zasady ponawiania, aby zastosować je do błędów połączenia

Aby uzyskać więcej informacji, zobacz [łączenie się z usługami i komunikowanie się z nimi](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikacja przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy to usługa, która działa w każdym węźle i obsługuje rozpoznawanie punktów końcowych, automatyczne ponawianie próby i inne błędy połączeń w imieniu usług klienta. Zwrotny serwer proxy można skonfigurować w taki sposób, aby zastosować różne zasady w miarę obsługi żądań z usług klienta. Użycie zwrotnego serwera proxy umożliwia usłudze klienta korzystanie z dowolnych bibliotek komunikacji HTTP po stronie klienta i nie wymaga specjalnej rozdzielczości i logiki ponawiania w usłudze. 

Zwrotny serwer proxy uwidacznia jeden lub więcej punktów końcowych w węźle lokalnym dla usług klienta, które mają być używane do wysyłania żądań do innych usług.

![Komunikacja wewnętrzna][1]

> [!NOTE]
> **Obsługiwane platformy**
>
> Zwrotny serwer proxy w Service Fabric obecnie obsługuje następujące platformy
> * *Klaster systemu Windows*: system Windows 8 lub nowszy lub windows Server 2012 lub nowszy
> * *Klaster systemu Linux*: zwrotny serwer proxy nie jest obecnie dostępny dla klastrów systemu Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Osiąganie mikrousług spoza klastra
Domyślnym zewnętrznym modelem komunikacji dla mikrousług jest model, w którym nie można uzyskać dostępu do poszczególnych usług bezpośrednio od klientów zewnętrznych. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), czyli granica sieci między mikrousługami i klientami zewnętrznymi, wykonuje translację adresów sieciowych i przekazuje zewnętrzne żądania do wewnętrznego adresu IP: punkty końcowe portów. Aby punkt końcowy mikrousługi był dostępny bezpośrednio dla klientów zewnętrznych, należy najpierw skonfigurować Load Balancer, aby przekazywać ruch do każdego portu używanego przez usługę w klastrze. Co więcej, większość mikrousług, szczególnie mikrousługi stanowe, nie działa na wszystkich węzłach klastra. Mikrousługi mogą przechodzić między węzłami w trybie failover. W takich przypadkach Load Balancer nie może efektywnie ustalić lokalizacji węzła docelowego replik, do których powinien przesłać ruch.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Osiąganie mikrousług za pośrednictwem zwrotnego serwera proxy spoza klastra
Zamiast konfigurować port pojedynczej usługi w Load Balancer, można skonfigurować tylko port zwrotnego serwera proxy w programie Load Balancer. Ta konfiguracja umożliwia klientom spoza klastra dostęp do usług znajdujących się w klastrze przy użyciu zwrotnego serwera proxy bez dodatkowej konfiguracji.

![Komunikacja zewnętrzna][0]

> [!WARNING]
> Po skonfigurowaniu portu zwrotnego serwera proxy w Load Balancer wszystkie mikrousługi w klastrze, które uwidaczniają punkt końcowy HTTP, są adresowane spoza klastra. Oznacza to, że mikrousługi mające być wewnętrzne mogą być odnajdywane przez określonego złośliwego użytkownika. Jest to potencjalnie przyczyną poważnych luk w zabezpieczeniach, które mogą zostać wykorzystane; na przykład:
>
> * Złośliwy użytkownik może uruchomić atak typu "odmowa usługi", wielokrotnie wywołując usługę wewnętrzną, która nie ma wystarczająco niezawodnej powierzchni ataku.
> * Złośliwy użytkownik może dostarczyć wadliwe pakiety do usługi wewnętrznej, powodując niezamierzone zachowanie.
> * Usługa, która ma być wewnętrzna, może zwracać informacje prywatne lub poufne, które nie są przeznaczone do ujawnienia usług poza klastrem, dzięki czemu ujawniają te poufne informacje złośliwemu użytkownikowi. 
>
> Pamiętaj, aby w pełni zrozumieć i wyeliminować potencjalne konsekwencje dotyczące zabezpieczeń klastra i uruchomione na nim aplikacje przed przystąpieniem do publicznego przywróceniu portu serwera proxy. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Format identyfikatora URI dla usług adresowania przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy używa określonego formatu identyfikatora URI (Uniform Resource Identifier) do identyfikowania partycji usługi, do której ma zostać przesłane żądanie przychodzące:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Zwrotny serwer proxy można skonfigurować tak, aby akceptował ruch HTTP lub HTTPS. W przypadku przekazywania HTTPS zapoznaj się z tematem łączenie się z [bezpieczną usługą za pomocą zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md) po zakończeniu instalacji zwrotnego serwera proxy do nasłuchiwania przy użyciu protokołu HTTPS.
* W **pełni kwalifikowana nazwa domeny (FQDN) klastra | wewnętrzny adres IP:** W przypadku klientów zewnętrznych można skonfigurować zwrotny serwer proxy w taki sposób, aby był osiągalny za pomocą domeny klastra, na przykład mycluster.eastus.cloudapp.azure.com. Domyślnie zwrotny serwer proxy działa w każdym węźle. W przypadku ruchu wewnętrznego można uzyskać dostęp do zwrotnego serwera proxy na hoście lokalnym lub w dowolnym wewnętrznym adresie IP węzła, na przykład 10.0.0.1.
* **Port:** Jest to port, na przykład 19081, który został określony dla zwrotnego serwera proxy.
* **ServiceInstanceName:** Jest to w pełni kwalifikowana nazwa wdrożonego wystąpienia usługi, do którego próbujesz uzyskać dostęp bez "sieci szkieletowej:/" równaniu. Na przykład, aby uzyskać dostęp do *sieci szkieletowej:/MojaApl/WebService/* Service, należy użyć *MojaApl/* moje usługi.

    W nazwie wystąpienia usługi jest rozróżniana wielkość liter. Użycie innej wielkości liter dla nazwy wystąpienia usługi w adresie URL powoduje, że żądania kończą się niepowodzeniem z 404 (nie znaleziono).
* **Ścieżka sufiksu:** Jest to rzeczywista ścieżka URL, na przykład *myapi/Values/Add/3* dla usługi, z którą chcesz nawiązać połączenie.
* **PartitionKey:** W przypadku usługi partycjonowanej jest to obliczony klucz partycji partycji, która ma zostać osiągnięta. Należy zauważyć, że *nie* jest to identyfikator GUID identyfikatora partycji. Ten parametr nie jest wymagany w przypadku usług, które korzystają z schematu partycji pojedynczej.
* **PartitionKind:** To jest schemat partycji usług. Może to być "Int64Range" lub "nazwa". Ten parametr nie jest wymagany w przypadku usług, które korzystają z schematu partycji pojedynczej.
* Nr **odbiornika** Punkty końcowe usługi mają postać {"Endpoints": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}}. Gdy usługa ujawnia wiele punktów końcowych, identyfikuje punkt końcowy, do którego żądanie klienta powinno być przekazywane. Można to pominąć, jeśli usługa ma tylko jeden odbiornik.
* **TargetReplicaSelector** Określa sposób, w jaki powinna zostać wybrana replika docelowa lub wystąpienie.
  * Gdy usługa docelowa jest stanowa, TargetReplicaSelector może być jedną z następujących: "PrimaryReplica", "RandomSecondaryReplica" lub "RandomReplica". Jeśli ten parametr nie jest określony, wartością domyślną jest "PrimaryReplica".
  * Gdy usługa docelowa jest bezstanowa, zwrotny serwer proxy wybiera losowo wystąpienie partycji usługi, aby przesłać żądanie do programu.
* **Limit czasu:**  Określa limit czasu żądania HTTP utworzonego przez zwrotny serwer proxy do usługi w imieniu żądania klienta. Wartość domyślna to 120 sekund. Jest to opcjonalny parametr.

### <a name="example-usage"></a>Przykład użycia
Na przykład przyjrzyjmy się usłudze *Fabric:/MojaApl/WebService* , która otwiera odbiornik http przy następującym adresie URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Poniżej przedstawiono zasoby usługi:

* `/index.html`
* `/api/users/<userId>`

Jeśli usługa korzysta z schematu partycjonowania singleton, parametry ciągu zapytania *PartitionKey* i *PartitionKind* nie są wymagane i można uzyskać dostęp do usługi przy użyciu bramy jako:

* Zewnętrznie `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Wewnętrznie `http://localhost:19081/MyApp/MyService`

Jeśli usługa używa jednorodnego schematu partycjonowania Int64, parametry ciągu zapytania *PartitionKey* i *PartitionKind* muszą być używane do uzyskania dostępu do partycji usługi:

* Zewnętrznie `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Aby dotrzeć do zasobów udostępnianych przez usługę, wystarczy umieścić ścieżkę zasobu po nazwie usługi w adresie URL:

* Zewnętrznie `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Następnie Brama przekaże te żądania do adresu URL usługi:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Specjalna obsługa usług udostępniania portów
Service Fabric zwrotny serwer proxy próbuje ponownie rozpoznać adres usługi i ponowić próbę żądania, gdy nie można nawiązać połączenia z usługą. Ogólnie rzecz biorąc, gdy nie można skontaktować się z usługą, wystąpienie usługi lub replika została przeniesiona do innego węzła w ramach normalnego cyklu życia. W takim przypadku zwrotny serwer proxy może otrzymać błąd połączenia sieciowego wskazujący, że punkt końcowy nie jest już otwarty na pierwotnie rozwiązanym adresie.

Jednak repliki lub wystąpienia usługi mogą współużytkować proces hosta i mogą również udostępniać port w przypadku hostowania przez serwer sieci Web oparty na http.sys, w tym:

* [System .NET. odbiornika HttpListener](/dotnet/api/system.net.httplistener)
* [ASP.NET Core webListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

W takiej sytuacji jest możliwe, że serwer sieci Web jest dostępny w procesie hosta i odpowiada na żądania, ale rozpoznane wystąpienie usługi lub replika nie jest już dostępna na hoście. W takim przypadku Brama otrzyma odpowiedź HTTP 404 z serwera sieci Web. W rezultacie odpowiedź HTTP 404 może mieć dwie odrębne znaczenie:

- #1 przypadku: adres usługi jest poprawny, ale zasób, którego zażądał użytkownik, nie istnieje.
- #2 przypadku: adres usługi jest niepoprawny, a zasób żądany przez użytkownika może istnieć w innym węźle.

Pierwszym przypadkiem jest normalne HTTP 404, który jest traktowany jako błąd użytkownika. Jednak w drugim przypadku użytkownik zażądał zasobu, który już istnieje. Zwrotny serwer proxy nie mógł go zlokalizować, ponieważ sama usługa została przeniesiona. Zwrotny serwer proxy musi ponownie rozpoznać adres i ponowić próbę żądania.

Zwrotny serwer proxy w ten sposób wymaga sposobu rozróżnienia między tymi dwoma przypadkami. Aby wprowadzić takie rozróżnienie, wymagana jest Wskazówka z serwera.

* Domyślnie zwrotny serwer proxy zakłada przypadek #2 i próbuje rozwiązać i wydać żądanie ponownie.
* Aby wskazać przypadek #1 do zwrotnego serwera proxy, usługa powinna zwrócić następujący nagłówek odpowiedzi HTTP:

  `X-ServiceFabric : ResourceNotFound`

Ten nagłówek odpowiedzi HTTP wskazuje normalną sytuację HTTP 404, w której żądany zasób nie istnieje i zwrotny serwer proxy nie będzie próbować ponownie rozpoznać adresu usługi.

## <a name="special-handling-for-services-running-in-containers"></a>Specjalna obsługa usług działających w kontenerach

W przypadku usług działających wewnątrz kontenerów można użyć zmiennej środowiskowej, `Fabric_NodeIPOrFQDN` Aby utworzyć [adres URL zwrotnego serwera proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) , jak w poniższym kodzie:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Dla klastra lokalnego domyślnie `Fabric_NodeIPOrFQDN` jest ustawiona wartość "localhost". Uruchom klaster lokalny z `-UseMachineName` parametrem, aby upewnić się, że kontenery mogą uzyskać dostęp do zwrotnego serwera proxy uruchomionego w węźle. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego do debugowania kontenerów](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Usługi Service Fabric, które są uruchamiane w ramach kontenerów Docker Compose, wymagają specjalnej *sekcji portów* Docker-Compose. yml http: lub https: Configuration. Aby uzyskać więcej informacji, zobacz [Docker Compose obsługa wdrażania w usłudze Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj [i skonfiguruj zwrotny serwer proxy w klastrze](service-fabric-reverseproxy-setup.md).
* [Skonfiguruj przekazywanie do bezpiecznej usługi HTTP przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnozowanie zdarzeń zwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md)
* Zobacz przykład komunikacji HTTP między usługami w [przykładowym projekcie w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedur z Reliable Services komunikacji zdalnej](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN w Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Komunikacja WCF przy użyciu Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
