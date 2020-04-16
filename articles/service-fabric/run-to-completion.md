---
title: Semantyka RunToCompletion w sieci szkieletowej usług
description: W tym artykule Opis semantyki RunToCompletion w sieci szkieletowej usług.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431295"
---
# <a name="runtocompletion"></a>RunToCompletion (RunToCompletion)

Począwszy od wersji 7.1, sieć szkieletowa usług obsługuje semantykę **RunToCompletion** dla [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa.][guest-executables-introduction-link] Te semantyka umożliwiają aplikacje i usługi, które wypełniają zadanie i kończą, w przeciwieństwie do zawsze uruchomionych aplikacji i usług.

Przed przystąpieniem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji sieci szkieletowej usług][application-model-link] i [modelem hostingu sieci szkieletowej usług.][hosting-model-link]

> [!NOTE]
> Semantyka RunToCompletion nie jest obecnie obsługiwana dla usług napisanych przy użyciu modelu programowania [reliable services.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>Semantyka i specyfikacja RunToCompletion
Semantyka RunToCompletion może być określona jako **ExecutionPolicy** podczas [importowania ServiceManifest][application-and-service-manifests-link]. Określone zasady są dziedziczone przez wszystkie Pakiety kodu składające się z ServiceManifest. Poniższy fragment kodu ApplicationManifest.xml zawiera przykład.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** umożliwia następujące dwa atrybuty:
* **Typ:** **RunToCompletion** jest obecnie jedyną dozwoloną wartością dla tego atrybutu.
* **Uruchom ponownie:** Ten atrybut określa zasady ponownego uruchamiania, który jest stosowany do CodePackages obejmujące ServicePackage, na niepowodzenie. A CodePackage zamykania z **kodem zakończenia niezerowe** jest uważany za nie powiodło się. Dozwolone wartości dla tego atrybutu są **OnFailure** i **Nigdy** z **OnFailure** jest domyślna.

Z zasadami ponownego uruchamiania **ustawionymi na OnFailure**, jeśli dowolne CodePackage nie powiedzie się **(kod zakończenia niezerowego),** zostanie ponownie uruchomiony, z wycofywaniem między powtarzającymi się błędami. Z zasadami ponownego uruchamiania **ustawionymi**na Nigdy , jeśli dowolne CodePackage nie powiedzie się, stan wdrożenia DeployedServicePackage jest oznaczony jako **Failed,** ale inne pakiety kodu mogą kontynuować wykonywanie. Jeśli wszystkie Pakiety kodu obejmujące ServicePackage uruchomić do pomyślnego zakończenia **(kod zakończenia 0),** stan wdrożenia DeployedServicePackage jest oznaczony jako **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Kompletny przykład przy użyciu semantyki RunToCompletion

Przyjrzyjmy się kompletny przykład przy użyciu semantyki RunToCompletion.

> [!IMPORTANT]
> W poniższym przykładzie przyjęto założenie, że należy zapoznać się z tworzeniem [aplikacji kontenerów systemu Windows przy użyciu sieci szkieletowej usług i platformy Docker][containers-getting-started-link].
>
> W tym przykładzie odwołuje się mcr.microsoft.com/windows/nanoserver:1809. Kontenery systemu Windows Server nie są zgodne ze wszystkimi wersjami systemu operacyjnego hosta. Aby dowiedzieć się więcej, zobacz [Zgodność wersji kontenera systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

W następujący sposób ServiceManifest.xml opisano ServicePackage składający się z dwóch Pakietów Kodu, które reprezentują kontenery. *RunToCompletionCodePackage1* tylko rejestruje komunikat do **stdout** i kończy pracę. *RunToCompletionCodePackage2* pinguje adres sprzężenia zwrotnego na chwilę, a następnie kończy pracę z kodem zakończenia **0,** **1** lub **2**.

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

W poniższym pliku ApplicationManifest.xml opisano aplikację opartą na omówieniu powyżej pliku ServiceManifest.xml. Określa **RunToCompletion** **ExecutionPolicy** for *WindowsRunToCompletionServicePackage* z zasadą ponownego uruchamiania **OnFailure**. Po aktywacji *WindowsRunToCompletionServicePackage*, jego składowe Pakiety Kodowe zostaną uruchomione. *RunToCompletionCodePackage1* należy zakończyć pomyślnie przy pierwszej aktywacji. Jednak *RunToCompletionCodePackage2* może zakończyć się **niepowodzeniem (kod zakończenia niezerowego),** w którym to przypadku zostanie ponownie uruchomiony, ponieważ zasadą ponownego uruchamiania jest **OnFailure**.

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Wykonywanie zapytań o stan wdrożenia pakietu DeployedServicePackage
Stan wdrożenia deployedServicePackage można zbadać z programu PowerShell przy użyciu [get-ServiceFabricDeployedServicePackage][deployed-service-package-link] lub z języka C# przy użyciu [interfejsu API FabricClient][fabric-client-link] [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Zagadnienia dotyczące korzystania z semantyki Funkcji Doukończenia

Następujące punkty należy zauważyć dla bieżącej obsługi RunToCompletion.
* Te semantyka są obsługiwane tylko dla [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa.][guest-executables-introduction-link]
* Scenariusze uaktualniania dla aplikacji z semantyka RunToCompletion nie są dozwolone. Użytkownicy powinni w razie potrzeby usuwać i odtwarzać takie aplikacje.
* Zdarzenia trybu failover może spowodować CodePackages do ponownego wykonania po pomyślnym zakończeniu, w tym samym węźle lub innych węzłów klastra. Przykładami zdarzeń trybu failover są, ponowne uruchomienie węzła i uaktualnienia środowiska uruchomieniowego sieci szkieletowej usług w węźle.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby uzyskać powiązane informacje.

* [Tkanina serwisowa i kontenery.][containers-introduction-link]
* [Sieć szkieletowa usług i pliki wykonywalne gościa.][guest-executables-introduction-link]

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
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

