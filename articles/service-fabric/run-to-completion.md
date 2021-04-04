---
title: Semantyka RunToCompletion w Service Fabric
description: Opisuje semantykę RunToCompletion w Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000270"
---
# <a name="runtocompletion"></a>RunToCompletion

Począwszy od wersji 7,1, Service Fabric obsługuje semantykę **RunToCompletion** dla [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa][guest-executables-introduction-link] . Te semantyki umożliwiają aplikacje i usługi, które ukończą zadanie i zakończą działanie, w przeciwieństwie do, zawsze działających aplikacji i usług.

Przed przejściem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji Service Fabric][application-model-link] i [Service Fabric modelem hostingu][hosting-model-link].

> [!NOTE]
> Semantyka RunToCompletion nie jest obecnie obsługiwana w przypadku usług pisanych przy użyciu modelu programowania [Reliable Services][reliable-services-link] .
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion, semantyka i Specyfikacja
Semantykę RunToCompletion można określić jako **ExecutionPolicy** podczas importowania elementu [servicemanifest][application-and-service-manifests-link]. Określone zasady są dziedziczone przez wszystkie CodePackages tworzące servicemanifest. Poniższy fragment kodu ApplicationManifest.xml zawiera przykład.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** zezwala na następujące dwa atrybuty:
* **Typ:** **RunToCompletion** jest obecnie jedyną dozwoloną wartością dla tego atrybutu.
* **Uruchom ponownie:** Ten atrybut określa zasady ponownego uruchamiania, które są stosowane do CodePackages zawierającego pakiet servicepackage, w przypadku niepowodzenia. CodePackage kończący się **niezerowym kodem zakończenia** jest traktowany jako zakończony niepowodzeniem. Dozwolone wartości tego atrybutu to **OnFailure** i **nigdy nie** są **domyślne** .

Gdy zasady ponownego uruchamiania zostały ustawione na wartość **OnFailure (niepowodzenie**), w przypadku niepowodzenia CodePackage **(niezerowy kod zakończenia)** zostanie on ponownie uruchomiony z powrotem z tyłu między powtarzanymi awariami. Gdy zasady ponownego uruchamiania mają wartość **nigdy**, w przypadku niepowodzenia CodePackage stan wdrożenia DeployedServicePackage zostanie oznaczony jako **Niepowodzenie** , ale inne CodePackages mogą kontynuować wykonywanie. Jeśli wszystkie CodePackages tworzące pakiet servicepackage do pomyślnego ukończenia **(kod zakończenia 0)**, stan wdrożenia DeployedServicePackage zostanie oznaczony jako **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Ukończ przykład przy użyciu semantyki RunToCompletion

Spójrzmy na kompletny przykład przy użyciu semantyki RunToCompletion.

> [!IMPORTANT]
> W poniższym przykładzie założono znajomość tworzenia [aplikacji kontenera systemu Windows przy użyciu Service Fabric i platformy Docker][containers-getting-started-link].
>
> Ten przykład odwołuje się do mcr.microsoft.com/windows/nanoserver:1809. Kontenery systemu Windows Server nie są zgodne ze wszystkimi wersjami systemu operacyjnego hosta. Aby dowiedzieć się więcej, zobacz [zgodność wersji kontenera systemu Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

W poniższym ServiceManifest.xml opisano pakiet servicepackage składający się z dwóch CodePackages, które reprezentują kontenery. *RunToCompletionCodePackage1* po prostu rejestruje komunikat do strumienia **stdout** i kończy pracę. *RunToCompletionCodePackage2* wysyła polecenie ping do adresu sprzężenia zwrotnego, a następnie kończy działanie z kodem zakończenia równym **0**, **1** lub **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

W poniższym ApplicationManifest.xml opisano aplikację opartą na ServiceManifest.xml opisanej powyżej. Określa **RunToCompletion** **ExecutionPolicy** dla *WindowsRunToCompletionServicePackage* z zasadami ponownego uruchamiania o wartości **OnFailure**. Po aktywacji *WindowsRunToCompletionServicePackage* zostanie uruchomiony jego składnik CodePackages. *RunToCompletionCodePackage1* powinna zakończyć się powodzeniem przy pierwszej aktywacji. Jednak *RunToCompletionCodePackage2* może zakończyć się niepowodzeniem **(kod zakończenia inny niż zero)**, w tym przypadku zostanie on ponownie uruchomiony, ponieważ zasady ponownego uruchamiania **są błędne**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Wykonywanie zapytania dotyczącego stanu wdrożenia elementu DeployedServicePackage
Ze stanem wdrożenia DeployedServicePackage można wykonywać zapytania z programu PowerShell przy użyciu polecenia [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] lub z języka C# przy użyciu [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, URI, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Zagadnienia dotyczące korzystania z semantyki RunToCompletion

W przypadku bieżącej obsługi RunToCompletion należy zwrócić uwagę na następujące kwestie.
* Te semantyki są obsługiwane tylko w przypadku [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa][guest-executables-introduction-link] .
* Scenariusze uaktualniania dla aplikacji z semantyką RunToCompletion są niedozwolone. Użytkownicy powinni usunąć i ponownie utworzyć takie aplikacje, w razie potrzeby.
* Zdarzenia trybu failover mogą spowodować ponowne uruchomienie CodePackages po pomyślnym zakończeniu, w tym samym węźle lub w innych węzłach klastra. Przykłady zdarzeń trybu failover to, że węzeł jest ponownie uruchamiany i Service Fabric uaktualnienia środowiska uruchomieniowego w węźle.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi pokrewnych informacji.

* [Service Fabric i kontenery.][containers-introduction-link]
* [Service Fabric i pliki wykonywalne gościa.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
