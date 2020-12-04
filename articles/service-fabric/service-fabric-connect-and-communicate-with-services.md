---
title: Łączenie się z usługami na platformie Azure i komunikowanie się z nimi Service Fabric
description: Dowiedz się, jak rozwiązywać i komunikować się z usługami w Service Fabric.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576302"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Łączenie się z usługami w Service Fabric i komunikowanie się z nimi
W Service Fabric usługa działa w dowolnym miejscu w klastrze Service Fabric, zazwyczaj rozmieszczona na wielu maszynach wirtualnych. Może być przenoszony z jednego miejsca do innego przez właściciela usługi lub automatycznie przez Service Fabric. Usługi nie są statycznie powiązane z określonym komputerem lub adresem.

Aplikacja Service Fabric zwykle składa się z wielu różnych usług, w przypadku których każda usługa wykonuje wyspecjalizowane zadanie. Te usługi mogą komunikować się ze sobą, aby utworzyć kompletną funkcję, na przykład renderowanie różnych części aplikacji sieci Web. Istnieją również aplikacje klienckie, które łączą się z usługami i komunikują się z nimi. W tym dokumencie omówiono sposób konfigurowania komunikacji z usługami i między nimi w programie Service Fabric.

## <a name="bring-your-own-protocol"></a>Korzystanie z własnego protokołu
Service Fabric pomaga zarządzać cyklem życia usług, ale nie podejmuje decyzji o działaniach usług. Obejmuje to komunikację. Gdy usługa zostanie otwarta przez Service Fabric, jest to Twoja usługa, która umożliwia skonfigurowanie punktu końcowego dla żądań przychodzących przy użyciu dowolnego protokołu lub stosu komunikacji. Usługa nasłuchuje na normalnym adresie **IP: adresu portu** przy użyciu dowolnego schematu adresowania, takiego jak identyfikator URI. Wiele wystąpień usług lub replik może współużytkować proces hosta, w takim przypadku należy użyć różnych portów lub mechanizmu udostępniania portów, takiego jak http.sys sterownika jądra w systemie Windows. W obu przypadkach każde wystąpienie usługi lub replika w procesie hosta musi mieć unikatową adres.

![punkty końcowe usługi][1]

## <a name="service-discovery-and-resolution"></a>Odnajdywanie i rozpoznawanie usług
W systemie rozproszonym usługi mogą przechodzić między kolejnymi maszynami. Może się to zdarzyć z różnych przyczyn, w tym równoważenia zasobów, uaktualnień, przełączeń w tryb failover lub skalowanie w poziomie. Oznacza to, że adresy punktów końcowych usługi zmieniają się w miarę przenoszenia usługi do węzłów z różnymi adresami IP i mogą być otwierane na różnych portach, jeśli usługa używa dynamicznie wybranego portu.

![Dystrybucja usług][7]

Service Fabric zapewnia usługę odnajdywania i rozpoznawania o nazwie Usługa nazewnictwa. Usługa nazewnictwa utrzymuje tabelę, która mapuje nazwane wystąpienia usługi na adresy punktów końcowych, na których nasłuchują. Wszystkie nazwane wystąpienia usługi w Service Fabric mają unikatowe nazwy reprezentowane jako identyfikatory URI, na przykład `"fabric:/MyApplication/MyService"` . Nazwa usługi nie zmienia się w okresie istnienia usługi, ale tylko adresy punktów końcowych, które mogą ulec zmianie podczas przenoszenia usług. Jest to analogiczne do witryn sieci Web, które mają stałe adresy URL, ale mogą zmieniać adres IP. Podobnie jak w przypadku systemu DNS w sieci Web, który rozpoznaje adresy IP witryny sieci Web, Service Fabric ma rejestrator, który mapuje nazwy usług na adres punktu końcowego.

![Diagram pokazujący, że Service Fabric ma rejestrator, który mapuje nazwy usług na adres punktu końcowego.][2]

Rozwiązywanie i łączenie z usługami obejmuje następujące kroki uruchamiane w pętli:

* **Rozwiąż**: Uzyskaj punkt końcowy, który opublikował usługę z usługa nazewnictwa.
* **Połącz**: Nawiąż połączenie z usługą za pośrednictwem dowolnego protokołu używanego w tym punkcie końcowym.
* **Ponów** próbę: próba połączenia może zakończyć się niepowodzeniem z dowolnej liczby przyczyn, na przykład jeśli usługa została przeniesiona od czasu ostatniego rozpoznania adresu punktu końcowego. W takim przypadku należy ponowić próbę wykonania powyższych kroków rozwiązywania i łączenia, a ten cykl jest powtarzany do momentu pomyślnego nawiązania połączenia.

## <a name="connecting-to-other-services"></a>Łączenie z innymi usługami
Usługi łączące się ze sobą w klastrze zwykle mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. Aby ułatwić łączenie się między usługami, Service Fabric zapewnia dodatkowe usługi korzystające z Usługa nazewnictwa. Usługa DNS i Zwrotna usługa serwera proxy.


### <a name="dns-service"></a>Usługa DNS
Ponieważ wiele usług, w szczególności usługi kontenerowe, może mieć istniejącą nazwę adresu URL, można je rozwiązać przy użyciu standardowego protokołu DNS (a nie protokołu Usługa nazewnictwa) jest bardzo wygodny, szczególnie w scenariuszach aplikacji "Unieś i Shift". Jest to dokładnie działanie usługi DNS. Umożliwia mapowanie nazw DNS na nazwę usługi, a tym samym rozwiązanie adresów IP punktów końcowych. 

Jak pokazano na poniższym diagramie, usługa DNS uruchomiona w klastrze Service Fabric mapuje nazwy DNS na nazwy usług, które następnie są rozwiązywane przez Usługa nazewnictwa w celu zwrócenia adresów punktów końcowych, z którymi ma zostać nawiązane połączenie. Nazwa DNS usługi jest udostępniana w momencie tworzenia. 

![Diagram przedstawiający sposób, w jaki usługa DNS, gdy działa w klastrze Service Fabric, mapuje nazwy DNS na nazwy usług, które następnie są rozwiązywane przez Usługa nazewnictwa w celu zwrócenia adresów punktu końcowego w celu nawiązania połączenia.][9]

Aby uzyskać więcej informacji na temat korzystania z usługi DNS, zobacz artykuł [Usługa DNS na platformie Azure Service Fabric](service-fabric-dnsservice.md) .

### <a name="reverse-proxy-service"></a>Usługa zwrotnego serwera proxy
Zwrotny serwer proxy odnosi się do usług w klastrze, które ujawniają punkty końcowe HTTP, w tym HTTPS. Zwrotny serwer proxy znacznie upraszcza wywoływanie innych usług i ich metod przy użyciu określonego formatu identyfikatora URI i obsługuje Rozwiązywanie problemów, łączenie i ponawianie prób wymagane przez jedną usługę do komunikowania się z innym przy użyciu Usługa nazewnictwa. Innymi słowy, ukrywa Usługa nazewnictwa od użytkownika podczas wywoływania innych usług, dzięki czemu jest to proste jako wywołanie adresu URL.

![Diagram pokazujący, w jaki sposób zwrotny serwer proxy odnosi się do usług w klastrze, które ujawnia punkty końcowe HTTP, w tym HTTPS.][10]

Aby uzyskać więcej informacji na temat korzystania z usługi zwrotnego serwera proxy, zobacz artykuł [zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md) .

## <a name="connections-from-external-clients"></a>Połączenia od klientów zewnętrznych
Usługi łączące się ze sobą w klastrze zwykle mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. W niektórych środowiskach klaster może jednak znajdować się za modułem równoważenia obciążenia, który kieruje ruchem przychodzącym przez ograniczony zestaw portów. W takich przypadkach usługi mogą nadal komunikować się ze sobą i rozwiązywać adresy przy użyciu Usługa nazewnictwa, ale należy podjąć dodatkowe kroki, aby umożliwić zewnętrznym klientom łączenie się z usługami.

## <a name="service-fabric-in-azure"></a>Service Fabric na platformie Azure
Klaster Service Fabric na platformie Azure jest umieszczony za Azure Load Balancer. Cały ruch zewnętrzny do klastra musi przechodzić przez moduł równoważenia obciążenia. Moduł równoważenia obciążenia automatycznie przekaże ruch przychodzący na dany port do losowego *węzła* , który ma ten sam port otwarty. Azure Load Balancer wie tylko o portach otwartych w *węzłach*, nie wie o portach otwartych przez poszczególne *usługi*.

![Topologia Azure Load Balancer i Service Fabric][3]

Na przykład w celu akceptowania ruchu zewnętrznego na porcie **80** należy skonfigurować następujące elementy:

1. Napisz usługę, która nasłuchuje na porcie 80. Skonfiguruj port 80 w ServiceManifest.xml usługi i Otwórz odbiornik w usłudze, na przykład samodzielny serwer sieci Web.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Utwórz klaster Service Fabric na platformie Azure i określ port **80** jako niestandardowy port punktu końcowego dla typu węzła, który będzie hostować usługę. Jeśli masz więcej niż jeden typ węzła, możesz skonfigurować *ograniczenie umieszczania* w usłudze, aby upewnić się, że działa tylko na typie węzła, który ma otwarty niestandardowy port punktu końcowego.

    ![Otwieranie portu dla typu węzła][4]
3. Po utworzeniu klastra Skonfiguruj Azure Load Balancer w grupie zasobów klastra, aby przekazywać ruch na porcie 80. Podczas tworzenia klastra za pomocą Azure Portal jest on automatycznie konfigurowany dla każdego skonfigurowanego niestandardowego portu punktu końcowego.

    ![Zrzut ekranu, który podświetla pole portu zaplecza w obszarze reguły równoważenia obciążenia.][5]
4. Azure Load Balancer używa sondy, aby określić, czy ruch ma być wysyłany do określonego węzła. Sonda okresowo sprawdza punkt końcowy w każdym węźle, aby określić, czy węzeł odpowiada. Jeśli sonda nie otrzyma odpowiedzi po upływie skonfigurowanej liczby razy, moduł równoważenia obciążenia zatrzymuje wysyłanie ruchu do tego węzła. Podczas tworzenia klastra za pomocą Azure Portal sonda jest konfigurowana automatycznie dla każdego skonfigurowanego niestandardowego portu punktu końcowego.

    ![Przekazuj ruch w Azure Load Balancer][8]

Należy pamiętać, że Azure Load Balancer i sondy wiedzą tylko o *węzłach*, a nie na *usługach* uruchomionych w węzłach. Azure Load Balancer będzie zawsze wysyłać ruch do węzłów, które reagują na sondę, dlatego należy zadbać o to, aby usługi były dostępne w węzłach, które mogą reagować na sondę.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Wbudowane opcje interfejsu API komunikacji
Struktura Reliable Services jest dostarczana z kilkoma wstępnie skompilowanymi opcjami komunikacji. Decyzja o tym, który z nich będzie działał najlepiej, zależy od wyboru modelu programowania, struktury komunikacji i języka programowania, w którym są zapisywane usługi.

* **Brak określonego protokołu:**  Jeśli nie masz określonego wyboru struktury komunikacyjnej, ale chcesz szybko uzyskać coś i uruchamiania, to idealna opcja dla Ciebie jest [usługą komunikacji zdalnej](service-fabric-reliable-services-communication-remoting.md), która pozwala na używanie jednoznacznie określonych wywołań procedur zdalnych dla Reliable Services i Reliable Actors. Jest to najprostszy i najszybszy sposób rozpoczęcia komunikacji z usługą. Zdalna usługa obsługuje rozpoznawanie adresów usług, połączeń, ponowień i obsługi błędów. Jest ona dostępna dla aplikacji C# i Java.
* **Http**: w przypadku komunikacji z językiem niezależny od protokół http zapewnia standardową branżę z narzędziami i serwerami http dostępnymi w wielu różnych językach, które są obsługiwane przez Service Fabric. Usługi mogą korzystać z dowolnego dostępnego stosu HTTP, w tym [interfejsu API sieci Web ASP.NET](./service-fabric-reliable-services-communication-aspnetcore.md) dla aplikacji języka C#. Klienci zapisanie w języku C# mogą korzystać z `ICommunicationClient` `ServicePartitionClient` klas i, natomiast w przypadku języka Java należy używać `CommunicationClient` `FabricServicePartitionClient` klas i [w celu rozpoznawania usług, połączeń HTTP i pętli ponawiania](service-fabric-reliable-services-communication.md).
* **WCF**: Jeśli masz istniejący kod, który używa programu WCF jako struktury komunikacji, możesz użyć programu `WcfCommunicationListener` dla strony serwera i `WcfCommunicationClient` i `ServicePartitionClient` dla klienta. Jest to jednak dostępne tylko dla aplikacji w języku C# w klastrach opartych na systemie Windows. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący [implementacji stosu komunikacyjnego opartego na technologii WCF](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Korzystanie z niestandardowych protokołów i innych platform komunikacji
Usługi mogą używać dowolnego protokołu lub platformy do komunikacji, niezależnie od tego, czy jest to niestandardowy protokół binarny dla gniazd TCP Sockets, czy przesyłania strumieniowego zdarzeń za pośrednictwem [platformy azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [platformy Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric udostępnia interfejsy API komunikacji, do których można podłączyć stos komunikacji, podczas gdy cała pozostała do odnalezienia i nawiązania połączenia jest podzielona od użytkownika. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący [modelu komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md) .

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat pojęć i interfejsów API dostępnych w [modelu komunikacji Reliable Services](service-fabric-reliable-services-communication.md), a następnie szybko Rozpocznij pracę z usługami [zdalnymi](service-fabric-reliable-services-communication-remoting.md) , aby dowiedzieć się, jak napisać odbiornik komunikacyjny przy użyciu [interfejsu API sieci Web z własnym hostem Owin](./service-fabric-reliable-services-communication-aspnetcore.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
