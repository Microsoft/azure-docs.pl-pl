---
title: Uruchamianie usługi w ramach systemu i lokalnych kont zabezpieczeń
description: Dowiedz się, jak uruchomić aplikację Service Fabricową w ramach systemu i kont zabezpieczeń lokalnych.  Utwórz podmioty zabezpieczeń i Zastosuj zasady Run-As, aby bezpiecznie uruchamiać usługi.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75610129"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Uruchamianie usługi jako konta użytkownika lokalnego lub lokalnego konta systemowego
Za pomocą usługi Azure Service Fabric można zabezpieczyć aplikacje działające w klastrze pod różnymi kontami użytkowników. Domyślnie Service Fabric aplikacje są uruchamiane w ramach konta, w ramach którego działa proces Fabric.exe. Service Fabric oferuje również możliwość uruchamiania aplikacji na koncie użytkownika lokalnego lub systemu. Obsługiwane typy kont systemu lokalnego to **LocalUser**, **NetworkService**, **LocalService**i **LocalSystem**.  Jeśli używasz Service Fabric w klastrze autonomicznym systemu Windows, możesz uruchomić usługę w obszarze [Active Directory konta domeny](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

W manifeście aplikacji należy zdefiniować konta użytkowników wymagane do uruchamiania usług lub zabezpieczania zasobów w sekcji **podmiotów zabezpieczeń** . Można również zdefiniować i utworzyć grupy użytkowników, aby umożliwić zarządzanie jednym lub większą liczbą użytkowników. Jest to przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i potrzebne są typowe uprawnienia, które są dostępne na poziomie grupy.  Następnie użytkownicy odwołują się do zasad RunAs, które są stosowane do określonej usługi lub wszystkich usług w aplikacji. 

Domyślnie zasady RunAs są stosowane do głównego punktu wejścia.  Można również zastosować zasady RunAs do punktu wejścia Instalatora, jeśli konieczne jest [uruchomienie pewnych operacji konfiguracji z wysokim poziomem uprawnień w ramach konta systemowego](service-fabric-run-script-at-service-startup.md)lub głównego i instalacyjnego punktu wejścia.  

> [!NOTE] 
> Jeśli zastosujesz zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktów końcowych przy użyciu protokołu HTTP, należy określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych http i https](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Uruchamianie usługi jako użytkownik lokalny
Można utworzyć użytkownika lokalnego, którego można użyć do zabezpieczenia usługi w aplikacji. W przypadku określenia typu konta **LocalUser** w sekcji Principals manifestu aplikacji Service Fabric tworzy konta użytkowników lokalnych na maszynach, na których wdrożono aplikację. Domyślnie te konta nie mają tych samych nazw jak określone w manifeście aplikacji (na przykład *Customer3* w poniższym przykładzie manifestu aplikacji). Zamiast tego są one dynamicznie generowane i mają losowe hasła.

W sekcji **RunAsPolicy** dla **ServiceManifestImport**Określ konto użytkownika z sekcji **podmioty zabezpieczeń** , aby uruchomić pakiet kodu usługi.  Poniższy przykład pokazuje, jak utworzyć użytkownika lokalnego i zastosować zasady RunAs do głównego punktu wejścia:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Utwórz lokalną grupę użytkowników
Można utworzyć grupy użytkowników i dodać jednego lub kilku użytkowników do grupy. Jest to przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i że muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy. Poniższy przykład manifestu aplikacji przedstawia grupę lokalną o nazwie *LocalAdminGroup* , która ma uprawnienia administratora. Dwóch użytkowników, *Customer1* i *Customer2*, są członkami tej grupy lokalnej. W sekcji **ServiceManifestImport** zasady runas są stosowane do uruchamiania pakietu kodu *Stateful1Pkg* jako *Customer2*.  Do uruchamiania pakietu kodu *Web1Pkg* jako *Customer1*są stosowane inne zasady runas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Zastosuj zasady domyślne do wszystkich pakietów kodu usługi
Sekcja **DefaultRunAsPolicy** służy do określania domyślnego konta użytkownika dla wszystkich pakietów kodu, które nie mają zdefiniowanego określonego **RunAsPolicy** . Jeśli większość pakietów kodu, które są określone w manifeście usługi używanym przez aplikację, musi być uruchomiona w ramach tego samego użytkownika, aplikacja może po prostu zdefiniować domyślne zasady RunAs przy użyciu tego konta użytkownika. W poniższym przykładzie określono, że jeśli pakiet kodu nie ma określonego **RunAsPolicy** , pakiet kodu powinien działać w ramach użytkownika **MyDefaultAccount** określonego w sekcji podmioty zabezpieczeń.  Obsługiwane typy kont to LocalUser, NetworkService, LocalSystem i LocalService.  W przypadku korzystania z lokalnego użytkownika lub usługi należy również określić nazwę konta i hasło.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Lokalne debugowanie pakietu kodu przy użyciu przekierowania konsoli
Czasami jest to przydatne do debugowania w celu wyświetlenia danych wyjściowych konsoli z działającej usługi. Zasady przekierowywania konsoli można ustawić w punkcie wejścia w manifeście usługi, który zapisuje dane wyjściowe do pliku. Plik wyjściowy jest zapisywana w folderze aplikacji o nazwie **log** w węźle klastra, w którym aplikacja została wdrożona i uruchomiona. 

> [!WARNING]
> Nigdy nie należy używać zasad przekierowania konsoli w aplikacji wdrożonej w środowisku produkcyjnym, ponieważ może to mieć wpływ na tryb failover aplikacji. Służy on *tylko* do lokalnego projektowania i debugowania.  
> 
> 

Poniższy przykład manifestu usługi pokazuje, jak włączyć przekierowywanie konsoli za pomocą wartości FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Omówienie modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
