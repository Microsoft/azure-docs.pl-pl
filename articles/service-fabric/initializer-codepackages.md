---
title: Pakiety kodu inicjatora w sieci szkieletowej usług
description: W tym artykule opisano pakiety kodu inicjatora w sieci szkieletowej usług.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430632"
---
# <a name="initializer-codepackages"></a>Pakiety kodu inicjatora

Począwszy od wersji 7.1, sieć szkieletowa usług obsługuje **pakiety kodu inicjatora** dla [kontenerów][containers-introduction-link] i aplikacji [wykonywalnych gościa.][guest-executables-introduction-link] Pakiety kodu inicjatora umożliwiają wykonywanie inicjalizacji w zakresie ServicePackage przed rozpoczęciem wykonywania innych pakietów kodu. Ich związek z ServicePackage jest analogiczna do tego, co [SetupEntryPoint][setup-entry-point-link] jest dla CodePackage.

Przed przystąpieniem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji sieci szkieletowej usług][application-model-link] i [modelem hostingu sieci szkieletowej usług.][hosting-model-link]

> [!NOTE]
> Pakiety kodu inicjatora nie są obecnie obsługiwane dla usług napisanych przy użyciu modelu programowania [reliable services.][reliable-services-link]
 
## <a name="semantics"></a>Semantyka

Oczekuje się, że pakiet kodu inicjatora zostanie uruchomiony do **pomyślnego zakończenia (kod zakończenia 0).** Nie powiodło się Initializer CodePackage jest ponownie uruchamiany, dopóki nie zostanie pomyślnie ukończony. Wiele pakietów kodu inicjatora są dozwolone i są wykonywane do **pomyślnego zakończenia,** **sekwencyjnie**, **w określonej kolejności** przed innymi Pakiety kodu w ServicePackage rozpocząć wykonywanie.

## <a name="specifying-initializer-codepackages"></a>Określanie pakietów kodów inicjatora
Można oznaczyć CodePackage jako inicjatora, ustawiając **atrybut inicjatora** **true** w ServiceManifest. Gdy istnieje wiele pakietów kodu inicjatora, ich kolejność wykonywania można określić za pomocą atrybutu **ExecOrder.** **ExecOrder** musi być nieadywersyfikatorem całkowitym i jest prawidłowy tylko dla pakietów kodowych inicjatora. Initializer CodePackages z niższymi wartościami **ExecOrder** są wykonywane jako pierwsze. Jeśli **ExecOrder** nie jest określony dla initializer CodePackage, przyjmuje się domyślną wartość 0. Względna kolejność wykonywania initializer CodePackages o tej samej wartości **ExecOrder** jest nieokreślony.

Następujący fragment servicemanifest opisuje trzy Pakiety kodu, z których dwa są oznaczone jako inicjatory. Gdy ten ServicePackage jest aktywowany, *InitCodePackage0* jest wykonywany jako pierwszy, ponieważ ma najniższą wartość **ExecOrder**. Po pomyślnym zakończeniu (kod zakończenia 0) *InitCodePackage0*, *InitCodePackage1* jest wykonywany. Na koniec po pomyślnym zakończeniu *InitCodePackage1,* *WorkloadCodePackage* jest wykonywany.

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
## <a name="complete-example-using-initializer-codepackages"></a>Kompletny przykład przy użyciu pakietów kodowych inicjatora

Przyjrzyjmy się kompletnym przykładzie przy użyciu initializer CodePackages.

> [!IMPORTANT]
> W poniższym przykładzie przyjęto założenie, że należy zapoznać się z tworzeniem [aplikacji kontenerów systemu Windows przy użyciu sieci szkieletowej usług i platformy Docker][containers-getting-started-link].
>
> W tym przykładzie odwołuje się mcr.microsoft.com/windows/nanoserver:1809. Kontenery systemu Windows Server nie są zgodne ze wszystkimi wersjami systemu operacyjnego hosta. Aby dowiedzieć się więcej, zobacz [Zgodność wersji kontenera systemu Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Następujące ServiceManifest.xml opiera się na ServiceManifest fragment kodu opisane wcześniej. *InitCodePackage0*, *InitCodePackage1* i *WorkloadCodePackage* są pakiety kodu, które reprezentują kontenery. Po aktywacji *InitCodePackage0* jest wykonywany jako pierwszy. Rejestruje komunikat do pliku i kończy pracę. Następnie *initCodePackage1* jest wykonywany, który również rejestruje komunikat do pliku i kończy pracę. Na koniec *workloadCodePackage* rozpoczyna wykonywanie. Rejestruje również wiadomość do pliku, wyprowadza zawartość pliku do **stdout,** a następnie pingi na zawsze.

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

W poniższym pliku ApplicationManifest.xml opisano aplikację opartą na omówieniu powyżej pliku ServiceManifest.xml. Należy zauważyć, że określa ten sam **mount woluminu** dla wszystkich kontenerów, czyli **C:\WorkspaceOnHost** jest zainstalowany w **C:\WorkspaceOnContainer** na wszystkich trzech kontenerów. Efekt netto jest, że wszystkie kontenery zapisu do tego samego pliku dziennika w kolejności, w jakiej są aktywowane.

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
Po pomyślnym aktywowaniu pakietu ServicePackage zawartość **języka C:\WorkspaceOnHost\log.txt** powinna być następująca.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

