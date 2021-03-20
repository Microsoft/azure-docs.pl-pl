---
title: Dodawanie punktu końcowego HTTPS przy użyciu Kestrel
description: W ramach tego samouczka dowiesz się, jak dodać punkt końcowy HTTPS do usługi internetowej frontonu platformy ASP.NET Core za pomocą usługi Kestrel i wdrożyć aplikację w klastrze.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: c675f8ece8369bcfc0055343221ac82aea59dec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326239"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Samouczek: Dodawanie punktu końcowego HTTPS do usługi frontonu internetowego interfejsu API platformy ASP.NET Core za pomocą usługi Kestrel

Ten samouczek jest trzecią częścią serii.  Dowiesz się, jak włączyć protokół HTTPS w usłudze platformy ASP.NET Core działającej w usłudze Service Fabric. Po zakończeniu będziesz mieć aplikację do głosowania z usługą internetową frontonu ASP.NET Core z włączonym protokołem HTTPS nasłuchującą na porcie 443. Jeśli nie chcesz ręcznie tworzyć aplikacji do głosowania w sekcji [Tworzenie aplikacji platformy .NET w usłudze Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md), możesz [pobrać kod źródłowy](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) ukończonej aplikacji.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie punktu końcowego HTTPS w usłudze
> * Konfigurowanie usługi Kestrel do używania protokołu HTTPS
> * Instalowanie certyfikatu TLS/SSL w zdalnych węzłach klastra
> * Udzielanie dostępu USŁUGA SIECIOWA do klucza prywatnego certyfikatu
> * Otwieranie portu 443 w module równoważenia obciążenia platformy Azure
> * Wdrażanie aplikacji w klastrze zdalnym

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji platformy .NET w usłudze Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Dodawanie punktu końcowego protokołu HTTPS do usługi frontonu platformy ASP.NET Core
> * [Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/) w wersji 16,5 lub nowszej przy użyciu obciążeń deweloperskich i **ASP.NET** na **platformie Azure** .
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Uzyskaj certyfikat lub utwórz certyfikat programistyczny z podpisem własnym

W przypadku aplikacji produkcyjnych należy używać certyfikatu z [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority). Dla celów projektowania i testowania możesz utworzyć i używać certyfikatu z podpisem własnym. Zestaw SDK usługi Service Fabric zawiera skrypt *CertSetup.ps1*, który tworzy certyfikat z podpisem własnym, a następnie importuje go do magazynu certyfikatów `Cert:\LocalMachine\My`. Otwórz wiersz polecenia jako administrator i uruchom następujące polecenie, aby utworzyć certyfikat z podmiotem „CN=mytestcert”:

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Jeśli masz już plik PFX certyfikatu, uruchom następujące polecenie, aby zaimportować certyfikat do magazynu certyfikatów `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definiowanie punktu końcowego HTTPS w manifeście usługi

Uruchom program Visual Studio jako **administrator** i otwórz rozwiązanie do głosowania. W Eksploratorze rozwiązań otwórz plik *VotingWeb/PackageRoot/ServiceManifest.xml*. Manifest usługi definiuje punkty końcowe usługi.  Znajdź sekcję **Punkty końcowe** i edytuj istniejący punkt końcowy „Punktu końcowy usługi”.  Zmień nazwę na „EndpointHttps”, ustaw protokół *https*, typ *dane wejściowe* i port *443*.  Zapisz zmiany.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS

W Eksploratorze rozwiązań otwórz plik *VotingWeb/VotingWeb.cs*.  Skonfiguruj usługę Kestrel do używania protokołu HTTPS i wyszukiwania certyfikatu w magazynie `Cert:\LocalMachine\My`. Dodaj następujące instrukcje using:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Zaktualizuj element `ServiceInstanceListener`, aby używał nowego punktu końcowego *EndpointHttps* i nasłuchiwał na porcie 443. Podczas konfigurowania hosta internetowego w celu używania serwera Kestrel musisz skonfigurować usługę Kestrel do nasłuchiwania adresów IPv6 we wszystkich interfejsach sieciowych: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Dodaj również następującą metodę, tak aby usługa Kestrel mogła znaleźć certyfikat w magazynie `Cert:\LocalMachine\My` przy użyciu podmiotu.  

Zastąp ciąg „&lt;your_CN_value&gt;” wartością „mytestcert”, jeśli utworzono certyfikat z podpisem własnym za pomocą polecenia programu PowerShell, lub użyj nazwy pospolitej (CN) certyfikatu.
Należy pamiętać, że w przypadku lokalnego wdrożenia, `localhost` preferowane jest użycie "CN = localhost", aby uniknąć wyjątków uwierzytelniania.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>Przyznaj usłudze sieciowej dostęp do klucza prywatnego certyfikatu

W poprzednim kroku zaimportowano certyfikat do magazynu `Cert:\LocalMachine\My` na komputerze dewelopera.  Teraz jawnie Nadaj kontu z uruchomioną usługą (usługa sieciowa domyślnie) dostęp do klucza prywatnego certyfikatu. Ten krok można wykonać ręcznie (przy użyciu narzędzia certlm. msc), ale lepiej uruchomić skrypt programu PowerShell, [konfigurując skrypt uruchamiania](service-fabric-run-script-at-service-startup.md) w **SetupEntryPoint** manifestu usługi.

>[!NOTE]
> Service Fabric obsługuje deklarowanie certyfikatów punktów końcowych za pomocą odcisku palca lub nazwy pospolitej podmiotu. W takim przypadku środowisko uruchomieniowe skonfiguruje powiązanie i przylistuje listę ACL klucza prywatnego certyfikatu do tożsamości, z której jest uruchomiona usługa. Środowisko uruchomieniowe będzie również monitorować certyfikat pod kątem zmian/odnowień i odpowiednio zmienić listę ACL odpowiedniego klucza prywatnego.

### <a name="configure-the-service-setup-entry-point"></a>Konfigurowanie punktu wejścia usługi instalatora

W Eksploratorze rozwiązań otwórz plik *VotingWeb/PackageRoot/ServiceManifest.xml*.  W sekcji **CodePackage** dodaj węzeł **SetupEntryPoint**, a następnie węzeł **ExeHost**.  W węźle **ExeHost** ustaw element **Program** na „Setup.bat” i element **WorkingFolder** na wartość „CodePackage”.  Po uruchomieniu usługi VotingWeb skrypt Setup.bat jest wykonywany w folderze CodePackage przed uruchomieniem pliku VotingWeb.exe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Dodawanie partii i skryptów instalacji programu PowerShell

Aby uruchomić program PowerShell z punktu **SetupEntryPoint**, możesz uruchomić plik PowerShell.exe w pliku wsadowym, który wskazuje plik programu PowerShell. Najpierw dodaj plik wsadowy do projektu usługi.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy element **VotingWeb** i wybierz pozycję **Dodaj**->**Nowy element** i dodaj nowy plik o nazwie „Setup.bat”.  Edytuj plik *Setup.bat* pliku i dodaj następujące polecenie:

```cmd
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Zmodyfikuj właściwości pliku *Setup.bat*, aby ustawić opcję **Kopiuj do katalogu wyjściowego** na wartość „Kopiuj, jeśli nowszy”.

![Ustawianie właściwości pliku][image1]

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy element **VotingWeb** i wybierz pozycję **Dodaj**->**Nowy element** i dodaj nowy plik o nazwie „SetCertAccess.ps1”.  Edytuj plik *SetCertAccess.ps1* i dodaj następujący skrypt:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Zmodyfikuj właściwości pliku *SetCertAccess.ps1*, aby ustawić opcję **Kopiuj do katalogu wyjściowego** na wartość „Kopiuj, jeśli nowszy”.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Uruchamianie skryptu instalatora jako administrator lokalny

Domyślnie plik wykonywalny punktu wejścia instalatora usługi jest uruchamiany z tymi samymi poświadczeniami co usługa Service Fabric (zazwyczaj konto NetworkService). Plik *SetCertAccess.ps1* wymaga uprawnień administratora. W manifeście aplikacji możesz zmienić uprawnienia zabezpieczeń do uruchamiania skryptu uruchamiania przy użyciu konta administratora lokalnego.

W Eksploratorze rozwiązań otwórz plik *Voting/ApplicationPackageRoot/ApplicationManifest.xml*. Najpierw utwórz sekcję **Podmioty zabezpieczeń** i dodaj nowego użytkownika (na przykład „SetupAdminUser”). Dodaj konto użytkownika SetupAdminUser do grupy systemowej Administratorzy.
Następnie w sekcji **ServiceManifestImport** pliku VotingWebPkg skonfiguruj zasady **RunAsPolicy**, aby zastosować podmiot zabezpieczeń SetupAdminUser do punktu wejścia instalacji. Te zasady informują usługę Service Fabric, że plik Setup.bat jest uruchamiany jako SetupAdminUser (z uprawnieniami administratora).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
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

## <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji

W Eksplorator rozwiązań wybierz aplikację do **głosowania** i ustaw właściwość **adres URL aplikacji** na wartość "https: \/ /localhost: 443".

Zapisz wszystkie pliki i naciśnij klawisz F5, aby uruchomić aplikację lokalnie.  Po wdrożeniu aplikacji zostanie otwarta przeglądarka sieci Web https: \/ /localhost: 443. Jeśli używasz certyfikatu z podpisem własnym, zobaczysz ostrzeżenie, że komputer nie ufa zabezpieczeniom tej witryny internetowej.  Kontynuuj przechodzenie do strony internetowej.

![Zrzut ekranu przykładowej aplikacji do głosowania Service Fabric w oknie przeglądarki z adresem URL https://localhost/ .][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Instalowanie certyfikatu w węzłach klastra

Przed wdrożeniem aplikacji na platformie Azure należy zainstalować certyfikat w magazynie `Cert:\LocalMachine\My` wszystkich węzłów klastra zdalnego.  Usługi można przenosić do różnych węzłów klastra.  Po uruchomieniu usługi internetowej frontonu w węźle klastra skrypt uruchamiania wyszuka certyfikat i skonfiguruje uprawnienia dostępu.

Najpierw należy wyeksportować certyfikat do pliku PFX. Otwórz aplikację certlm. msc i przejdź do apletu  > **Certyfikaty** osobiste.  Kliknij prawym przyciskiem myszy certyfikat *mytestcert*, a następnie wybierz pozycję **Wszystkie zadania**>**Eksportowanie**.

![Eksportowanie certyfikatu][image4]

W kreatorze eksportu wybierz opcję **Tak, eksportuj klucz prywatny** i wybierz format Wymiana informacji osobistych (PFX).  Wyeksportuj plik do pliku *C:\Users\sfuser\votingappcert.pfx*.

Następnie Zainstaluj certyfikat w klastrze zdalnym, korzystając z [tych dostarczonych skryptów programu PowerShell](./scripts/service-fabric-powershell-add-application-certificate.md).

> [!Warning]
> Certyfikat z podpisem własnym jest wystarczający w przypadku programowania i testowania aplikacji. W przypadku aplikacji produkcyjnych należy użyć certyfikatu z [certyfikatu urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority) zamiast certyfikatu z podpisem własnym.

## <a name="open-port-443-in-the-azure-load-balancer-and-virtual-network"></a>Otwórz port 443 w module równoważenia obciążenia platformy Azure i w sieci wirtualnej

Jeśli jeszcze tego nie zrobiono, otwórz port 443 w module równoważenia obciążenia.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

Wykonaj te same czynności dla skojarzonej sieci wirtualnej.

```powershell
$rulename="allowAppPort$port"
$nsgname="voting-vnet-security"
$RGname="voting_RG"
$port=443

# Get the NSG resource
$nsg = Get-AzNetworkSecurityGroup -Name $nsgname -ResourceGroupName $RGname

# Add the inbound security rule.
$nsg | Add-AzNetworkSecurityRuleConfig -Name $rulename -Description "Allow app port" -Access Allow `
    -Protocol * -Direction Inbound -Priority 3891 -SourceAddressPrefix "*" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange $port

# Update the NSG.
$nsg | Set-AzNetworkSecurityGroup
```

## <a name="deploy-the-application-to-azure"></a>Wdrożenie aplikacji na platformie Azure

Zapisz wszystkie pliki, przełącz z debugowania na wydanie i naciśnij klawisz F6, aby ponownie skompilować rozwiązanie.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Voting (Głosowanie)** i wybierz polecenie **Opublikuj**. Wybierz punkt końcowy połączenia klastra utworzonego w sekcji [Wdrażanie aplikacji w klastrze](service-fabric-tutorial-deploy-app-to-party-cluster.md) lub wybierz inny klaster.  Kliknij pozycję **Opublikuj**, aby opublikować aplikację w klastrze zdalnym.

Po wdrożeniu aplikacji otwórz przeglądarkę internetową i przejdź do strony `https://mycluster.region.cloudapp.azure.com:443` (zaktualizuj adres URL przy użyciu punktu końcowego połączenia dla klastra). Jeśli używasz certyfikatu z podpisem własnym, zobaczysz ostrzeżenie, że komputer nie ufa zabezpieczeniom tej witryny internetowej.  Kontynuuj przechodzenie do strony internetowej.

![Zrzut ekranu przykładowej aplikacji do głosowania Service Fabric w oknie przeglądarki z adresem URL https://mycluster.region.cloudapp.azure.com:443 .][image3]

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie punktu końcowego HTTPS w usłudze
> * Konfigurowanie usługi Kestrel do używania protokołu HTTPS
> * Instalowanie certyfikatu TLS/SSL w zdalnych węzłach klastra
> * Udzielanie dostępu USŁUGA SIECIOWA do klucza prywatnego certyfikatu
> * Otwieranie portu 443 w module równoważenia obciążenia platformy Azure
> * Wdrażanie aplikacji w klastrze zdalnym

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
