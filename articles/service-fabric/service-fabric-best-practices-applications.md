---
title: Najlepsze rozwiązania dotyczące projektowania aplikacji Service Fabric platformy Azure
description: Najlepsze rozwiązania i zagadnienia związane z projektowaniem opracowywania aplikacji i usług za pomocą usługi Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538502"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Najlepsze rozwiązania dotyczące projektowania aplikacji Service Fabric platformy Azure

W tym artykule przedstawiono najlepsze rozwiązania dotyczące tworzenia aplikacji i usług na platformie Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Zapoznaj się z Service Fabric
* Zapoznaj się z tym artykułem, aby [dowiedzieć się więcej o Service Fabric?](service-fabric-content-roadmap.md) .
* Przeczytaj o [scenariuszach aplikacji Service Fabric](service-fabric-application-scenarios.md).
* Zapoznaj się z opcjami modelu programowania, odczytując [Service Fabric model programowania — Omówienie](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Wskazówki dotyczące projektowania aplikacji
Zapoznaj się z [ogólną architekturą](/azure/architecture/reference-architectures/microservices/service-fabric) Service Fabric aplikacji i [zagadnieniami](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)dotyczącymi projektowania.

### <a name="choose-an-api-gateway"></a>Wybieranie bramy interfejsu API
Użyj usługi bramy interfejsu API, która komunikuje się z usługami zaplecza, które można następnie skalować w poziomie. Używane są najbardziej popularne usługi bramy interfejsu API:

- [API Management platformy Azure](./service-fabric-api-management-overview.md) [zintegrowane z Service Fabric](./service-fabric-tutorial-deploy-api-management.md).
- [Azure IoT Hub](../iot-hub/index.yml) lub [Azure Event Hubs](../event-hubs/index.yml), przy użyciu [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) do odczytywania z partycji centrum zdarzeń.
- [Træfik zwrotny serwer proxy](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)przy użyciu [dostawcy Service Fabric platformy Azure](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Application Gateway platformy Azure](../application-gateway/index.yml).

   > [!NOTE] 
   > Application Gateway platformy Azure nie są bezpośrednio zintegrowane z Service Fabric. Jest to zazwyczaj preferowany wybór usługi Azure API Management.
- Twoja własna [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) Brama aplikacji sieci Web.

### <a name="stateless-services"></a>Usługi bezstanowe
Zalecamy, aby zawsze zacząć od tworzenia bezstanowych usług przy użyciu [Reliable Services](./service-fabric-reliable-services-introduction.md) i przechowywania stanu w usłudze Azure database, Azure Cosmos DB lub Azure Storage. Stan zewnętrzny to bardziej znane podejście dla większości deweloperów. Takie podejście umożliwia również korzystanie z funkcji zapytania w sklepie.  

### <a name="when-to-use-stateful-services"></a>Kiedy należy używać usług stanowych
Należy wziąć pod uwagę usługi stanowe, gdy istnieje scenariusz dla małych opóźnień i należy utrzymać dane blisko obliczeń. Niektóre przykładowe scenariusze obejmują urządzenia wieloosiowe IoT, stan gier, stan sesji, buforowanie danych z bazy danych i długotrwałe przepływy pracy do śledzenia wywołań do innych usług.

Wybierz ramy czasowe przechowywania danych:

- **Dane w pamięci podręcznej**. Użyj buforowania, gdy opóźnienie do magazynów zewnętrznych jest problemem. Użyj usługi stanowej jako własnej pamięci podręcznej danych lub Rozważ użycie [rozproszonej pamięci podręcznej SoCreate Service Fabric](https://github.com/SoCreate/service-fabric-distributed-cache). W tym scenariuszu nie trzeba pamiętać, Jeśli utracisz wszystkie dane w pamięci podręcznej.
- **Dane powiązane z czasem**. W tym scenariuszu należy zapewnić, że dane mają być zbliżone do obliczeń przez pewien czas, ale można spowodować utratę danych w przypadku *awarii*. Na przykład w wielu rozwiązaniach IoT dane muszą zostać zbliżone do obliczeń, na przykład w przypadku obliczania średniej temperatury w ciągu ostatnich kilku dni, ale jeśli dane zostaną utracone, określone zarejestrowane punkty danych nie są ważne. Ponadto w tym scenariuszu zazwyczaj nie ma opieki nad tworzeniem kopii zapasowych poszczególnych punktów danych. Można tworzyć kopie zapasowe obliczonych wartości średnich, które są okresowo zapisywane w magazynie zewnętrznym.  
- **Długoterminowe dane**. Niezawodne kolekcje mogą trwale przechowywać dane. Jednak w takim przypadku należy przygotować się [do odzyskiwania po awarii](./service-fabric-disaster-recovery.md), w tym [skonfigurować okresowe zasady tworzenia kopii zapasowych](./service-fabric-backuprestoreservice-configure-periodic-backup.md) dla klastrów. W efekcie należy skonfigurować co się stanie, Jeśli klaster zostanie zniszczony w awarii, gdzie trzeba utworzyć nowy klaster i jak wdrożyć nowe wystąpienia aplikacji i odzyskać je z najnowszej kopii zapasowej.

Oszczędzaj koszty i zwiększaj dostępność:
- Możesz obniżyć koszty, korzystając z usług stanowych, ponieważ nie są naliczane koszty dostępu do danych i transakcji ze sklepu zdalnego, a ponieważ nie musisz używać innej usługi, takiej jak Azure cache for Redis.
- Korzystanie z usług stanowych przede wszystkim do magazynowania, a nie dla obliczeń jest kosztowne i nie jest zalecane. Należy traktować usługi stanowe jako zasoby obliczeniowe z tanim magazynem lokalnym.
- Usuwając zależności od innych usług, można poprawić dostępność usługi. Zarządzanie stanem o wysokiej dostępności w klastrze izoluje użytkownika od innych przestojów usługi lub problemów z opóźnieniem.

## <a name="how-to-work-with-reliable-services"></a>Jak korzystać z Reliable Services
Service Fabric Reliable Services pozwala łatwo tworzyć bezstanowe i stanowe usługi. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Reliable Services](./service-fabric-reliable-services-introduction.md).
- Zawsze należy przestrzegać [tokenu anulowania](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) w `RunAsync()` metodzie dla usług bezstanowych i stanowych oraz `ChangeRole()` metody dla usług stanowych. Jeśli tego nie zrobisz, Service Fabric nie wiadomo, czy można zamknąć usługę. Na przykład jeśli nie honoruje token anulowania, może wystąpić dużo dłuższy czas uaktualniania aplikacji.
-    Otwieraj i zamykaj [odbiorniki komunikacji](./service-fabric-reliable-services-communication.md) w odpowiednim czasie i honoruje tokeny anulowania.
-    Nigdy nie mieszaj kodu synchronizacji z kodem asynchronicznym. Na przykład nie należy używać `.GetAwaiter().GetResult()` w wywołaniach asynchronicznych. Używaj asynchronicznie *w całej drodze* za pomocą stosu wywołań.

## <a name="how-to-work-with-reliable-actors"></a>Jak korzystać z Reliable Actors
Service Fabric Reliable Actors umożliwia łatwe tworzenie stanowych i zwirtualizowanych aktorów. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Reliable Actors](./service-fabric-reliable-actors-introduction.md).

- Należy poważnie rozważyć użycie komunikatów pub/sub między uczestnikami w celu skalowania aplikacji. Narzędzia, które udostępniają tę usługę, obejmują [Service Fabric SoCreate typu open source](https://service-fabric-pub-sub.socreate.it/) i [Azure Service Bus](/azure/service-bus/).
- Ustaw stan aktora jako [szczegółowy, jak to możliwe](./service-fabric-reliable-actors-state-management.md#best-practices).
- Zarządzaj [cyklem życia aktora](./service-fabric-reliable-actors-state-management.md#best-practices). Usuń aktory, jeśli nie będą używane ponownie. Usuwanie niepotrzebnych aktorów jest szczególnie ważne w przypadku używania [dostawcy stanu lotnego](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication), ponieważ wszystkie stany są przechowywane w pamięci.
- Ze względu [na sposób współbieżności](./service-fabric-reliable-actors-introduction.md#concurrency)aktory najlepiej używać jako obiektów niezależnych. Nie twórz grafów wywołań metod synchronicznych (z których każde prawdopodobnie stanie się oddzielnym wywołaniem sieciowym) lub Utwórz cykliczne żądania aktora. Znacznie wpływają na wydajność i skalowalność.
- Nie mieszaj kodu synchronizacji z kodem asynchronicznym. Aby zapobiec problemom z wydajnością, należy regularnie używać Async.
- Nie należy wykonywać długotrwałych wywołań w aktorach. Długotrwałe wywołania będą blokować inne wywołania do tego samego aktora z powodu współbieżności opartej na włączeniu.
- Jeśli komunikujesz się z innymi usługami przy użyciu [Service Fabric komunikacji zdalnej](./service-fabric-reliable-services-communication-remoting.md) i tworzysz `ServiceProxyFactory` , Utwórz fabrykę na poziomie [aktora usługi](./service-fabric-reliable-actors-using.md) , a *nie* na poziomie aktora.


## <a name="application-diagnostics"></a>Diagnostyka aplikacji
Zapoznaj się z gruntownym dodawaniem [rejestrowania aplikacji](./service-fabric-diagnostics-event-generation-app.md) w wywołaniach usługi. Pomoże to zdiagnozować scenariusze, w których usługi wywołują siebie nawzajem. Na przykład, gdy wywołania B wywołania C wywołania D, wywołanie może zakończyć się niepowodzeniem w dowolnym miejscu. Jeśli nie masz wystarczającej liczby dzienników, błędy są trudne do zdiagnozowania. Jeśli usługi są zbyt duże ze względu na woluminy wywołań, pamiętaj, aby co najmniej rejestrować błędy i ostrzeżenia.

## <a name="iot-and-messaging-applications"></a>Aplikacje IoT i Messaging
Podczas odczytywania wiadomości z [usługi azure IoT Hub](../iot-hub/index.yml) lub [platformy Azure Event Hubs](../event-hubs/index.yml)Użyj  [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor integruje się z Reliable Services Service Fabric, aby zachować stan odczytu z partycji centrum zdarzeń i wypchnąć nowe komunikaty do usług za pośrednictwem `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Wskazówki dotyczące projektowania na platformie Azure
* Odwiedź [centrum architektury platformy Azure](/azure/architecture/microservices/) , aby uzyskać wskazówki dotyczące projektowania [mikrousług na platformie Azure](/azure/architecture/microservices/).

* Odwiedź stronę [wprowadzenie do platformy Azure](/gaming/azure/) , aby uzyskać wskazówki dotyczące projektowania w zakresie [korzystania z Service Fabric w usługach gier](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
