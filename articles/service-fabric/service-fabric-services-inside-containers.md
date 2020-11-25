---
title: Konteneryzowanie usługi Azure Service Fabric w systemie Windows
description: Dowiedz się, jak konteneryzowanie Reliable Services Service Fabric i usługi Reliable Actors w systemie Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999811"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Konteneryzowanie usług Reliable Services i Reliable Actors usługi Service Fabric w systemie Windows

Service Fabric obsługuje mikrousługi konteneryzowania Service Fabric (Reliable Services i niezawodne usługi oparte na aktorze). Aby uzyskać więcej informacji, zobacz [kontenery usługi Service Fabric](service-fabric-containers-overview.md).

Ten dokument zawiera wskazówki dotyczące uruchamiania usługi w kontenerze systemu Windows.

> [!NOTE]
> Obecnie ta funkcja działa tylko dla systemu Windows. Aby uruchamiać kontenery, klaster musi działać w systemie Windows Server 2016 z kontenerami.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Kroki umożliwiające konteneryzowanie aplikacji Service Fabric

1. Otwórz aplikację Service Fabric w programie Visual Studio.

2. Dodaj klasę [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) do projektu. Kod w tej klasie jest pomocnikem do prawidłowego ładowania plików binarnych środowiska uruchomieniowego Service Fabric wewnątrz aplikacji podczas uruchamiania wewnątrz kontenera.

3. Dla każdego pakietu kodu, który chcesz konteneryzowanie, zainicjuj moduł ładujący w punkcie wejścia programu. Dodaj statyczny Konstruktor przedstawiony w poniższym fragmencie kodu do pliku punktu wejścia programu.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Kompilowanie i [pakowanie](service-fabric-package-apps.md#Package-App) projektu. Aby skompilować i utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w Eksplorator rozwiązań i wybierz polecenie **pakiet** .

5. Dla każdego pakietu kodu, który należy konteneryzowanie, uruchom skrypt programu PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Użycie jest następujące:

    Pełny .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Skrypt tworzy folder z artefaktami Docker w $dockerPackageOutputDirectoryPath. Zmodyfikuj wygenerowane pliku dockerfile na `expose` dowolne porty, Uruchom skrypty instalacyjne i tak dalej. na podstawie Twoich potrzeb.

6. Następnie musisz [skompilować](service-fabric-get-started-containers.md#Build-Containers) i [wypchnąć](service-fabric-get-started-containers.md#Push-Containers) pakiet kontenera Docker do repozytorium.

7. Zmodyfikuj ApplicationManifest.xml i ServiceManifest.xml, aby dodać obraz kontenera, informacje o repozytorium, uwierzytelnianie rejestru i mapowanie między portami i hostami. Aby zmodyfikować manifesty, zobacz [Tworzenie aplikacji kontenera Service Fabric platformy Azure](service-fabric-get-started-containers.md). Definicja pakietu kodu w manifeście usługi musi zostać zamieniona na odpowiedni obraz kontenera. Upewnij się, że punkt wejścia został zmieniony na typ ContainerHost.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Dodaj mapowanie portów do hosta dla replikatora i punktu końcowego usługi. Ponieważ oba te porty są przypisane w czasie wykonywania przez Service Fabric, ContainerPort jest ustawiona na zero, aby użyć przypisanego portu do mapowania.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Aby skonfigurować tryb izolacji kontenera, zobacz [Configure izolacj Mode]( ./service-fabric-get-started-containers.md#configure-isolation-mode). System Windows obsługuje dwa tryby izolacji dla kontenerów: tryb procesu oraz tryb funkcji Hyper-V. Poniższe fragmenty kodu pokazują, jak tryb izolacji jest określony w pliku manifestu aplikacji.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Domyślnie aplikacje Service Fabric mają dostęp do środowiska uruchomieniowego Service Fabric, w postaci punktu końcowego akceptującego żądania specyficzne dla aplikacji. Należy rozważyć wyłączenie tego dostępu, gdy aplikacja hostuje niezaufany kod. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania w zakresie zabezpieczeń w Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Aby wyłączyć dostęp do środowiska uruchomieniowego Service Fabric, należy dodać następujące ustawienie w sekcji zasady manifestu aplikacji odpowiadające zaimportowanemu Manifestowi usługi w następujący sposób:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Aby przetestować tę aplikację, należy wdrożyć ją w klastrze z systemem w wersji 5,7 lub nowszej. W przypadku środowiska uruchomieniowego w wersji 6,1 lub niższej należy edytować i zaktualizować ustawienia klastra, aby włączyć tę funkcję w wersji zapoznawczej. Wykonaj kroki opisane w tym [artykule](service-fabric-cluster-fabric-settings.md) , aby dodać wyświetlone ustawienie dalej.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Następnie [Wdróż](service-fabric-deploy-remove-applications.md) zmodyfikowany pakiet aplikacji w tym klastrze.

Teraz należy mieć Service Fabric aplikację, na której działa klaster.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-get-started-containers.md).
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.