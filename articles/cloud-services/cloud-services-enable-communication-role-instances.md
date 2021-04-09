---
title: Komunikacja dla ról w Cloud Services (klasyczny) | Microsoft Docs
description: Wystąpienia roli w Cloud Services mogą mieć punkty końcowe (http, https, TCP, UDP) zdefiniowane dla nich, które komunikują się z zewnętrznym lub innym wystąpieniem roli.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 82aa1579a1f7feb36732153341e1eacf266a7218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743036"
---
# <a name="enable-communication-for-role-instances-in-azure-cloud-services-classic"></a>Włączanie komunikacji dla wystąpień ról w usłudze Azure Cloud Services (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Role usługi w chmurze komunikują się za poorednictwem połączeń wewnętrznych i zewnętrznych. Połączenia zewnętrzne są nazywane **wejściowymi punktami końcowymi** , gdy wewnętrzne połączenia są nazywane **wewnętrznymi punktami końcowymi**. W tym temacie opisano sposób modyfikowania [definicji usługi](cloud-services-model-and-package.md#csdef) w celu tworzenia punktów końcowych.

## <a name="input-endpoint"></a>Wejściowy punkt końcowy
Wejściowy punkt końcowy jest używany, gdy chcesz uwidocznić port zewnętrzny. Należy określić typ protokołu i Port punktu końcowego, który ma zastosowanie zarówno do portów zewnętrznych, jak i wewnętrznych dla punktu końcowego. Jeśli chcesz, możesz określić inny port wewnętrzny dla punktu końcowego z atrybutem [localport](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) .

Wejściowy punkt końcowy może używać następujących protokołów: **http, https, TCP, UDP**.

Aby utworzyć wejściowy punkt końcowy, Dodaj element podrzędny **InputEndpoint** do elementu **Endpoints** roli sieć Web lub proces roboczy.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Wejściowy punkt końcowy wystąpienia
Wejściowe punkty końcowe wystąpienia są podobne do wejściowych punktów końcowych, ale umożliwiają mapowanie określonych portów publicznych dla poszczególnych wystąpień ról przy użyciu przekazywania portów w usłudze równoważenia obciążenia. Można określić jeden port publiczny lub zakres portów.

Wejściowy punkt końcowy wystąpienia może używać tylko protokołu **TCP** lub **UDP** jako protokołu.

Aby utworzyć wejściowy punkt końcowy wystąpienia, Dodaj element podrzędny **InstanceInputEndpoint** do elementu **Endpoints** roli sieć Web lub proces roboczy.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Wewnętrzny punkt końcowy
Wewnętrzne punkty końcowe są dostępne dla komunikacji między wystąpieniami. Port jest opcjonalny i w przypadku pominięcia jest przypisywany port dynamiczny do punktu końcowego. Można użyć zakresu portów. Istnieje ograniczenie pięciu wewnętrznych punktów końcowych dla każdej roli.

Wewnętrzny punkt końcowy może używać następujących protokołów: **http, TCP, UDP, any**.

Aby utworzyć wewnętrzny wejściowy punkt końcowy, Dodaj element podrzędny **InternalEndpoint** do elementu **Endpoints** roli sieć Web lub proces roboczy.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Można również użyć zakresu portów.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Role procesów roboczych a role sieci Web
Istnieje jedna drobna różnica z punktami końcowymi podczas pracy z rolami procesów roboczych i sieci Web. Rola sieci Web musi mieć co najmniej jeden wejściowy punkt końcowy przy użyciu protokołu **http** .

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Używanie zestawu SDK platformy .NET do uzyskiwania dostępu do punktu końcowego
Biblioteka zarządzana platformy Azure udostępnia metody dla wystąpień ról do komunikowania się w czasie wykonywania. Z kodu działającego w ramach wystąpienia roli można pobrać informacje o istnieniu innych wystąpień roli i ich punktów końcowych, a także informacje o bieżącym wystąpieniu roli.

> [!NOTE]
> Można pobrać tylko informacje o wystąpieniach ról uruchomionych w usłudze w chmurze, które definiują co najmniej jeden wewnętrzny punkt końcowy. Nie można uzyskać danych o wystąpieniach ról uruchomionych w innej usłudze.
> 
> 

Można użyć właściwości [Instances](/previous-versions/azure/reference/ee741904(v=azure.100)) , aby pobrać wystąpienia roli. Najpierw użyj [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) , aby zwrócić odwołanie do bieżącego wystąpienia roli, a następnie użyj właściwości [role](/previous-versions/azure/reference/ee741918(v=azure.100)) , aby zwrócić odwołanie do samej roli.

Po nawiązaniu połączenia z wystąpieniem roli programowo przy użyciu zestawu .NET SDK można stosunkowo łatwo uzyskać dostęp do informacji o punkcie końcowym. Na przykład po połączeniu się już z określonym środowiskiem roli można uzyskać port określonego punktu końcowego za pomocą tego kodu:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Właściwość **Instances** zwraca kolekcję obiektów **RoleInstance** . Ta kolekcja zawsze zawiera bieżące wystąpienie. Jeśli rola nie definiuje wewnętrznego punktu końcowego, kolekcja zawiera bieżące wystąpienie, ale nie inne wystąpienia. Liczba wystąpień roli w kolekcji zawsze będzie wynosić 1 w przypadku, gdy dla roli nie jest zdefiniowany żaden wewnętrzny punkt końcowy. Jeśli rola definiuje wewnętrzny punkt końcowy, jego wystąpienia są wykrywalne w czasie wykonywania, a liczba wystąpień w kolekcji będzie odpowiadać liczbie wystąpień określonych dla roli w pliku konfiguracji usługi.

> [!NOTE]
> Biblioteka zarządzana przez platformę Azure nie zapewnia możliwości określania kondycji innych wystąpień roli, ale można wdrożyć takie oceny kondycji samodzielnie, jeśli usługa wymaga tej funkcji. Za pomocą [Diagnostyka Azure](cloud-services-dotnet-diagnostics.md) można uzyskać informacje o uruchomionych wystąpieniach roli.
> 
> 

Aby określić numer portu wewnętrznego punktu końcowego w wystąpieniu roli, można użyć [`InstanceEndpoints`](/previous-versions/azure/reference/ee741917(v=azure.100)) właściwości, aby zwrócić obiekt słownika zawierający nazwy punktów końcowych i odpowiadające im adresy IP i porty. [`IPEndpoint`](/previous-versions/azure/reference/ee741919(v=azure.100))Właściwość zwraca adres IP i port dla określonego punktu końcowego. `PublicIPEndpoint`Właściwość zwraca Port punktu końcowego ze zrównoważonym obciążeniem. Część adresu IP `PublicIPEndpoint` właściwości nie jest używana.

Oto przykład, który iteruje wystąpienia roli.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Oto przykład roli procesu roboczego, który pobiera punkt końcowy uwidoczniony za pomocą definicji usługi i zaczyna nasłuchiwanie połączeń.

> [!WARNING]
> Ten kod będzie działał tylko dla wdrożonej usługi. W przypadku uruchamiania w emulatorze obliczeń platformy Azure elementy konfiguracji usługi, które tworzą bezpośrednie punkty końcowe portu (elementy **InstanceInputEndpoint** ), są ignorowane.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Reguły ruchu sieciowego w celu kontrolowania komunikacji z rolami
Po zdefiniowaniu wewnętrznych punktów końcowych można dodać reguły ruchu sieciowego (w oparciu o utworzone punkty końcowe), aby kontrolować sposób komunikacji między wystąpieniami ról. Na poniższym diagramie przedstawiono niektóre typowe scenariusze kontrolowania komunikacji między rolami:

![Scenariusze reguł ruchu sieciowego](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenariusze reguł ruchu sieciowego")

Poniższy przykład kodu pokazuje definicje ról dla ról pokazanych na poprzednim diagramie. Każda definicja roli zawiera co najmniej jeden zdefiniowany wewnętrzny punkt końcowy:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Ograniczenie komunikacji między rolami może odbywać się przy użyciu wewnętrznych punktów końcowych zarówno z ustalonych, jak i automatycznie przypisanych portów.
> 
> 

Domyślnie po zdefiniowaniu wewnętrznego punktu końcowego komunikacja może przepływać z dowolnej roli do wewnętrznego punktu końcowego roli bez żadnych ograniczeń. Aby ograniczyć komunikację, należy dodać element **NetworkTrafficRules** do elementu **ServiceDefinition** w pliku definicji usługi.

### <a name="scenario-1"></a>Scenariusz 1
Zezwalaj tylko na ruch sieciowy z **WebRole1** do **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenariusz 2
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1** i **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenariusz 3
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1** i **WorkerRole1** do **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenariusz 4
Zezwala tylko na ruch sieciowy z **WebRole1** do **WorkerRole1**, **WebRole1** do **WorkerRole2** i **WorkerRole1** do **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Odwołanie do schematu XML dla elementów użytych powyżej można znaleźć [tutaj](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Następne kroki
Przeczytaj więcej na temat [modelu](cloud-services-model-and-package.md)usługi w chmurze.




