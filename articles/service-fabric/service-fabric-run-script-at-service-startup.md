---
title: Uruchom skrypt po uruchomieniu usługi Service Fabric platformy Azure
description: Dowiedz się, jak skonfigurować zasady dla punktu wejścia Instalatora usługi Service Fabric i uruchomić skrypt podczas uruchamiania usługi.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012531"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Uruchamianie skryptu uruchamiania usługi za pomocą użytkownika lokalnego lub konta systemowego
Przed uruchomieniem pliku wykonywalnego usługi Service Fabric może być konieczne uruchomienie instalacji lub konfiguracji.  Na przykład Konfigurowanie zmiennych środowiskowych. Możesz określić skrypt do uruchomienia przed uruchomieniem pliku wykonywalnego usługi w manifeście usługi dla usługi. Konfigurując zasady RunAs dla punktu wejścia Instalatora usługi, można zmienić konto, w którym jest uruchamiany plik wykonywalny Instalatora.  Oddzielny punkt wejścia Instalatora umożliwia uruchamianie konfiguracji o wysokim poziomie uprawnień przez krótki czas, dzięki czemu plik wykonywalny hosta usługi nie musi działać z wysokim poziomem uprawnień przez dłuższy czas.

Punkt wejścia Instalatora (**SetupEntryPoint** w [manifeście usługi](service-fabric-application-and-service-manifests.md)) to uprzywilejowany punkt wejścia, który domyślnie jest uruchamiany z tymi samymi poświadczeniami co Service Fabric (zazwyczaj konto *NetworkService* ) przed jakimkolwiek innym punktem wejścia. Plik wykonywalny określony przez **punkt wejścia** jest zwykle długotrwałym hostem usługi. Plik wykonywalny **punktu wejścia** jest uruchamiany po pomyślnym zamknięciu pliku wykonywalnego **SetupEntryPoint** . Proces wynikający z tego procesu jest monitorowany i uruchamiany ponownie z **SetupEntryPoint** , jeśli kiedykolwiek się zakończy lub ulegnie awarii. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurowanie punktu wejścia usługi instalatora
Poniżej znajduje się prosty przykład manifestu usługi dla bezstanowej usługi, która określa skrypt Instalatora *MySetup.bat* w usłudze **SetupEntryPoint**.  **Argumenty** są używane do przekazywania argumentów do skryptu podczas jego uruchamiania.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurowanie zasad dla punktu wejścia Instalatora usługi
Domyślnie plik wykonywalny punktu wejścia Instalatora usługi jest uruchamiany w ramach tych samych poświadczeń co Service Fabric (zazwyczaj konto *NetworkService* ).  W manifeście aplikacji można zmienić uprawnienia zabezpieczeń, aby uruchomić skrypt uruchamiania w ramach lokalnego konta systemowego lub konta administratora.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurowanie zasad przy użyciu lokalnego konta systemowego
Poniższy przykład manifestu aplikacji pokazuje, jak skonfigurować punkt wejścia Instalatora usługi do uruchomienia w ramach konta administratora użytkownika (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Najpierw utwórz sekcję **podmiotów zabezpieczeń** z nazwą użytkownika, taką jak SetupAdminUser. Konto użytkownika SetupAdminUser jest członkiem grupy systemowej Administratorzy.

Następnie w sekcji **ServiceManifestImport** Skonfiguruj zasady służące do zastosowania tego podmiotu zabezpieczeń do **SetupEntryPoint**. Te zasady mówią, Service Fabric, że po uruchomieniu pliku **MySetup.bat** powinna być uruchamiana jako SetupAdminUser (z uprawnieniami administratora). Ze względu na to, że zasady *nie* zostały zastosowane do głównego punktu wejścia, kod w **MyServiceHost.exe** jest uruchamiany w ramach konta **NetworkService** systemu. Jest to domyślne konto, na którym są uruchamiane wszystkie punkty wejścia usługi.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurowanie zasad przy użyciu kont systemu lokalnego
Często zaleca się uruchomienie skryptu uruchamiania przy użyciu lokalnego konta systemowego, a nie konta administratora. Uruchamianie zasad RunAs jako członek grupy Administratorzy zwykle nie działa prawidłowo, ponieważ komputery mają domyślnie włączoną funkcję User Access Control (UAC). W takich przypadkach zaleca się uruchomienie SetupEntryPoint jako LocalSystem, a nie jako użytkownika lokalnego dodanego do grupy administratorów. W poniższym przykładzie przedstawiono ustawienie SetupEntryPoint do uruchamiania jako LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> W przypadku klastrów systemu Linux w celu uruchomienia usługi lub punktu wejścia Instalatora jako **katalogu głównego** można określić wartość  **AccountType** jako **System lokalny**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Uruchamianie skryptu z punktu wejścia Instalatora
Teraz Dodaj skrypt uruchamiania do projektu, aby uruchomić go z uprawnieniami administratora. 

W programie Visual Studio kliknij prawym przyciskiem myszy projekt usługi i Dodaj nowy plik o nazwie *MySetup.bat*.

Następnie upewnij się, że plik *MySetup.bat* jest zawarty w pakiecie usługi. Domyślnie nie jest to. Zaznacz plik, kliknij prawym przyciskiem myszy, aby uzyskać menu kontekstowe, a następnie wybierz polecenie **Właściwości**. W oknie dialogowym właściwości upewnij się, że w polu **Kopiuj do katalogu wyjściowego** jest ustawiona wartość **Kopiuj, jeśli nowszy**. Zobacz poniższy zrzut ekranu.

![Plik wsadowy programu Visual Studio CopyToOutput for SetupEntryPoint][image1]

Teraz Edytuj plik *MySetup.bat* i Dodaj następujące polecenia ustawiające systemową zmienną środowiskową i Wyprowadź plik tekstowy:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Następnie Skompiluj i Wdróż rozwiązanie w lokalnym klastrze projektowym. Po uruchomieniu usługi, jak pokazano w [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), można zobaczyć, że plik MySetup.bat zakończył się pomyślnie na dwa sposoby. Otwórz wiersz polecenia programu PowerShell i wpisz:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Następnie należy zwrócić uwagę na nazwę węzła, w którym usługa została wdrożona i uruchomiona w [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Na przykład węzeł 2. Następnie przejdź do folderu workowego wystąpienia aplikacji, aby znaleźć plik out.txt, który pokazuje wartość **pliku TestVariable**. Na przykład jeśli ta usługa została wdrożona do węzła 2, można przejść do tej ścieżki dla elementu **Webapplicationtype**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Uruchamianie poleceń programu PowerShell z punktu wejścia Instalatora
Aby uruchomić program PowerShell z punktu **SetupEntryPoint** , można uruchomić **PowerShell.exe** w pliku wsadowym, który wskazuje plik programu PowerShell. Najpierw Dodaj plik programu PowerShell do projektu usługi — na przykład **MySetup.ps1**. Pamiętaj, aby ustawić właściwość *Copy If nowszej* , tak aby plik został również uwzględniony w pakiecie usługi. Poniższy przykład przedstawia przykładowy plik wsadowy, który uruchamia plik programu PowerShell o nazwie MySetup.ps1, który ustawia zmienną środowiskową systemową o nazwie **pliku TestVariable**.

MySetup.bat uruchomić pliku programu PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

W pliku programu PowerShell Dodaj następujące polecenie, aby ustawić zmienną środowiskową system:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Domyślnie po uruchomieniu pliku wsadowego sprawdzany jest folder aplikacji o nazwie **Work** for Files. W takim przypadku, gdy MySetup.bat zostanie uruchomiony, chcemy znaleźć plik MySetup.ps1 w tym samym folderze, który jest folderem **pakietu kodu** aplikacji. Aby zmienić ten folder, ustaw folder roboczy:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Lokalne debugowanie skryptu uruchomieniowego przy użyciu przekierowania konsoli
Czasami jest to przydatne w celu debugowania, aby zobaczyć dane wyjściowe konsoli z uruchamiania skryptu instalacyjnego. Zasady przekierowywania konsoli można ustawić w punkcie wejścia Instalatora w manifeście usługi, który zapisuje dane wyjściowe do pliku. Plik wyjściowy jest zapisywana w folderze aplikacji o nazwie **log** w węźle klastra, w którym aplikacja została wdrożona i uruchomiona. 

> [!WARNING]
> Nigdy nie należy używać zasad przekierowania konsoli w aplikacji wdrożonej w środowisku produkcyjnym, ponieważ może to mieć wpływ na tryb failover aplikacji. Służy on *tylko* do lokalnego projektowania i debugowania.  
> 
> 

Poniższy przykład manifestu usługi pokazuje ustawienie przekierowania konsoli o wartości FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Jeśli zmienisz teraz plik MySetup.ps1, aby napisać **echo** polecenie, spowoduje to zapis do pliku wyjściowego na potrzeby debugowania:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Po debugowaniu skryptu natychmiast usuń te zasady przekierowywania konsoli.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Informacje o zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md)
* [Omówienie modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
