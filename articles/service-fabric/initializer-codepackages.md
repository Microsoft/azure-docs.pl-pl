---
title: CodePackages inicjatora w Service Fabric
description: Opisuje CodePackages inicjatora w Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258970"
---
# <a name="initializer-codepackages"></a>Pakiety CodePackage inicjatora

Począwszy od wersji 7,1, Service Fabric obsługuje **inicjator CodePackages** dla [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa][guest-executables-introduction-link] . Inicjator CodePackages zapewnia możliwość wykonania inicjalizacji w zakresie servicepackage przed rozpoczęciem wykonywania innych operacji CodePackages. Ich relacje z pakietem servicepackage są analogiczne do [SetupEntryPoint][setup-entry-point-link] CodePackage.

Przed przejściem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji Service Fabric][application-model-link] i [Service Fabric modelem hostingu][hosting-model-link].

> [!NOTE]
> Inicjatory CodePackages nie są obecnie obsługiwane w przypadku usług pisanych przy użyciu modelu programowania [Reliable Services][reliable-services-link] .
 
## <a name="semantics"></a>Semantyka

Oczekiwany jest CodePackage inicjatora do **pomyślnego ukończenia (kod zakończenia 0)**. CodePackage inicjatora nie powiodło się, dopóki nie zakończy się pomyślnie. Wiele CodePackages inicjatorów jest dozwolonych i jest wykonywanych w celu **pomyślnego zakończenia**, **sekwencyjne** **w określonej kolejności** , zanim inne CodePackages w servicepackage zakończą wykonywanie.

## <a name="specifying-initializer-codepackages"></a>Określanie CodePackages inicjatora
Można oznaczyć CodePackage jako inicjator przez ustawienie atrybutu **inicjatora** na **true** w elemencie servicemanifest. Jeśli istnieje wiele CodePackages inicjatora, ich kolejność wykonywania może być określona za pomocą atrybutu **ExecOrder** . **ExecOrder** musi być nieujemną liczbą całkowitą i jest prawidłowy tylko dla inicjatora CodePackages. CodePackages inicjatora o niższych wartościach **ExecOrder** są wykonywane jako pierwsze. Jeśli **ExecOrder** nie zostanie określony dla inicjatora CodePackage, przyjmuje się wartość domyślną 0. Nie określono względnej kolejności wykonywania inicjatora CodePackages z tą samą wartością **ExecOrder** .

Poniższy fragment kodu servicemanifest opisuje trzy CodePackages dwa, które są oznaczone jako inicjatory. Gdy ten pakiet servicepackage jest aktywowany, *InitCodePackage0* jest wykonywane jako pierwsze, ponieważ ma najniższą wartość **ExecOrder**. Po pomyślnym zakończeniu (kod zakończenia 0) z *InitCodePackage0*, *InitCodePackage1* jest wykonywane. Na koniec po pomyślnym *InitCodePackage1*zakończeniu InitCodePackage1 *WorkloadCodePackage* jest wykonywane.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Ukończ przykład przy użyciu inicjatora CodePackages

Spójrzmy na kompletny przykład przy użyciu inicjatora CodePackages.

> [!IMPORTANT]
> W poniższym przykładzie założono znajomość tworzenia [aplikacji kontenera systemu Windows przy użyciu Service Fabric i platformy Docker][containers-getting-started-link].
>
> Ten przykład odwołuje się do mcr.microsoft.com/windows/nanoserver:1809. Kontenery systemu Windows Server nie są zgodne ze wszystkimi wersjami systemu operacyjnego hosta. Aby dowiedzieć się więcej, zobacz [zgodność wersji kontenera systemu Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Poniższy ServiceManifest.xml kompiluje się w opisany wcześniej fragmencie kodu servicemanifest. *InitCodePackage0*, *InitCodePackage1* i *WorkloadCodePackage* są CodePackages, które reprezentują kontenery. Po aktywacji *InitCodePackage0* jest wykonywane najpierw. Rejestruje on komunikat do pliku i kończy pracę. Następnie jest wykonywana *InitCodePackage1* , która również rejestruje komunikat w pliku i kończy pracę. Na koniec *WorkloadCodePackage* rozpoczyna wykonywanie. Rejestruje także komunikat do pliku, wyprowadza zawartość pliku do **stdout** , a następnie wysyła polecenie ping do nieograniczonego.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

W poniższym ApplicationManifest.xml opisano aplikację opartą na ServiceManifest.xml opisanej powyżej. Należy pamiętać, że ta sama instalacja **woluminu** jest taka sama dla wszystkich kontenerów, tj. **C:\WorkspaceOnHost** jest instalowana w **C:\WorkspaceOnContainer** we wszystkich trzech kontenerach. Efektem netto jest to, że wszystkie kontenery są zapisywane w tym samym pliku dziennika w kolejności, w której zostały aktywowane.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Po pomyślnym aktywowaniu pakietu servicepackage zawartość **C:\WorkspaceOnHost\log.txt** powinna być następująca.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
