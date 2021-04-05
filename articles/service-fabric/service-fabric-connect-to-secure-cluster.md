---
title: Bezpieczne nawiązywanie połączenia z klastrem usługi Azure Service Fabric
description: Zawiera opis sposobu uwierzytelniania dostępu klienta do klastra Service Fabric i zabezpieczania komunikacji między klientami a klastrem.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791125"
---
# <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Gdy klient łączy się z węzłem klastra Service Fabric, można uwierzytelniać klienta i nawiązać bezpieczną komunikację przy użyciu zabezpieczeń certyfikatów lub Azure Active Directory (AAD). To uwierzytelnianie zapewnia, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrażać aplikacje oraz wykonywać zadania zarządzania.  Podczas tworzenia klastra należy wcześniej włączyć zabezpieczenia certyfikatu lub usługi AAD w klastrze.  Aby uzyskać więcej informacji na temat scenariuszy zabezpieczeń klastra, zobacz [zabezpieczenia klastra](service-fabric-cluster-security.md). W przypadku łączenia się z klastrem zabezpieczonym przy użyciu certyfikatów [Skonfiguruj certyfikat klienta](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na komputerze, który nawiązuje połączenie z klastrem. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Nawiązywanie połączenia z bezpiecznym klastrem przy użyciu interfejsu wiersza polecenia platformy Azure Service Fabric (sfctl)

Istnieje kilka różnych sposobów łączenia się z bezpiecznym klastrem przy użyciu interfejsu wiersza polecenia Service Fabric (sfctl). Podczas uwierzytelniania przy użyciu certyfikatu klienta szczegóły certyfikatu muszą być zgodne z certyfikatem wdrożonym w węzłach klastra. Jeśli certyfikat ma urzędy certyfikacji, należy dodatkowo określić zaufane urzędy certyfikacji.

Możesz połączyć się z klastrem przy użyciu `sfctl cluster select` polecenia.

Certyfikaty klienta można określić w dwóch różnych sposobów, jako certyfikat i parę kluczy lub jako pojedynczy plik PFX. W przypadku plików PEM chronionych hasłem zostanie wyświetlony monit o wprowadzenie hasła. Jeśli certyfikat klienta został uzyskany jako plik PFX, najpierw przekonwertuj plik PFX na plik PEM przy użyciu poniższego polecenia. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Jeśli plik PFX nie jest chroniony hasłem, użyj parametru Pass-Pass: dla ostatniego parametru.

Aby określić certyfikat klienta jako plik PEM, określ ścieżkę pliku w `--pem` argumencie. Na przykład:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

W przypadku plików PEM chronionych hasłem przed uruchomieniem dowolnego polecenia zostanie wyświetlony monit o podanie hasła.

Aby określić certyfikat, para kluczy Użyj `--cert` argumentów i, `--key` Aby określić ścieżki plików do każdego odpowiedniego pliku.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Czasami certyfikaty używane do zabezpieczania klastrów testowych lub deweloperów nie przechodzą pomyślnie weryfikacji certyfikatu. Aby pominąć weryfikację certyfikatu, określ `--no-verify` opcję. Na przykład:

> [!WARNING]
> Nie należy używać `no-verify` opcji podczas nawiązywania połączenia z klastrami Service Fabric produkcyjnych.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ponadto można określić ścieżki do katalogów zaufanych certyfikatów urzędów certyfikacji lub pojedynczych certyfikatów. Aby określić te ścieżki, użyj `--ca` argumentu. Na przykład:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po nawiązaniu połączenia należy [uruchomić inne polecenia sfctl](service-fabric-cli.md) w celu współdziałania z klastrem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Nawiązywanie połączenia z klastrem przy użyciu programu PowerShell
Przed wykonaniem operacji w klastrze za pomocą programu PowerShell należy najpierw nawiązać połączenie z klastrem. Połączenie z klastrem jest używane dla wszystkich kolejnych poleceń w danej sesji programu PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Nawiązywanie połączenia z niezabezpieczonym klastrem

Aby nawiązać połączenie z niezabezpieczonym klastrem, podaj adres punktu końcowego klastra do polecenia **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązywanie połączenia z bezpiecznym klastrem przy użyciu Azure Active Directory

Aby nawiązać połączenie z bezpiecznym klastrem używającym Azure Active Directory do autoryzacji dostępu administratora klastra, podaj odcisk palca certyfikatu klastra i Użyj flagi *usługi azureactivedirectory* .  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązywanie połączenia z zabezpieczonym klastrem przy użyciu certyfikatu klienta
Uruchom następujące polecenie programu PowerShell, aby nawiązać połączenie z bezpiecznym klastrem używającym certyfikatów klienta do autoryzacji dostępu administratora. 

#### <a name="connect-using-certificate-common-name"></a>Połącz przy użyciu nazwy pospolitej certyfikatu
Podaj nazwę pospolitą certyfikatu klastra i nazwę pospolitą certyfikatu klienta, którym udzielono uprawnień do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne z certyfikatem w węzłach klastra.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* to nazwa pospolita certyfikatu serwera zainstalowanego w węzłach klastra. *FindValue* jest nazwą pospolitą certyfikatu klienta administratora. Po wprowadzeniu parametrów polecenie wygląda podobnie do poniższego przykładu:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Połącz przy użyciu odcisku palca certyfikatu
Podaj odcisk palca certyfikatu klastra i odcisk palca certyfikatu klienta z przyznanymi uprawnieniami do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne z certyfikatem w węzłach klastra.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* to odcisk palca certyfikatu serwera zainstalowanego w węzłach klastra. *FindValue* to odcisk palca certyfikatu klienta administratora.  Po wprowadzeniu parametrów polecenie wygląda podobnie do poniższego przykładu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Nawiązywanie połączenia z bezpiecznym klastrem przy użyciu Active Directory systemu Windows
Jeśli klaster autonomiczny został wdrożony przy użyciu zabezpieczeń usługi AD, Połącz się z klastrem, dołączając przełącznik "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Nawiązywanie połączenia z klastrem przy użyciu interfejsów API FabricClient
Zestaw Service Fabric SDK udostępnia klasę [FabricClient](/dotnet/api/system.fabric.fabricclient) do zarządzania klastrem. Aby skorzystać z interfejsów API FabricClient, Pobierz pakiet NuGet Microsoft. servicefabric.

### <a name="connect-to-an-unsecure-cluster"></a>Nawiązywanie połączenia z niezabezpieczonym klastrem

Aby nawiązać połączenie ze zdalnym niezabezpieczonym klastrem, Utwórz wystąpienie FabricClient i podaj adres klastra:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

W przypadku kodu działającego z poziomu klastra, na przykład w niezawodnej usłudze, Utwórz FabricClient *bez* określania adresu klastra. FabricClient nawiązuje połączenie z lokalną bramą zarządzania w węźle, w którym aktualnie działa kod, unikając dodatkowego przeskoku sieci.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązywanie połączenia z zabezpieczonym klastrem przy użyciu certyfikatu klienta

Węzły w klastrze muszą mieć prawidłowe certyfikaty, których nazwa pospolita lub nazwa DNS w sieci SAN pojawia się we [Właściwości RemoteCommonNames](/dotnet/api/system.fabric.x509credentials) ustawionym na [FabricClient](/dotnet/api/system.fabric.fabricclient). Poniższy proces umożliwia wzajemne uwierzytelnianie między klientem a węzłami klastra.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Interaktywnie nawiązywanie połączenia z bezpiecznym klastrem za pomocą Azure Active Directory

Poniższy przykład używa Azure Active Directory dla tożsamości klienta i certyfikatu serwera dla tożsamości serwera.

Po nawiązaniu połączenia z klastrem zostanie automatycznie wyświetlone okno dialogowe umożliwiające Logowanie interakcyjne.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Nawiązywanie połączenia z bezpiecznym klastrem nieinteraktywnie przy użyciu Azure Active Directory

Poniższy przykład korzysta z Microsoft. IdentityModel. clients. ActiveDirectory, Version: 2.19.208020213.

Aby uzyskać więcej informacji na temat pozyskiwania tokenów usługi AAD, zobacz [Microsoft. IdentityModel. clients. ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Nawiązywanie połączenia z zabezpieczonym klastrem bez wcześniejszej znajomości metadanych przy użyciu Azure Active Directory

W poniższym przykładzie użyto funkcji pozyskiwania tokenów nieinteraktywnych, ale takie samo podejście może służyć do kompilowania niestandardowego środowiska pozyskiwania tokenów interaktywnych. Metadane Azure Active Directory wymaganych do pozyskiwania tokenu są odczytywane z konfiguracji klastra.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Nawiązywanie połączenia z bezpiecznym klastrem przy użyciu Service Fabric Explorer
Aby uzyskać dostęp do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) dla danego klastra, wskaż przeglądarkę:

`http://<your-cluster-endpoint>:19080/Explorer`

Pełny adres URL jest również dostępny w okienku podstawy klastra Azure Portal.

W przypadku nawiązywania połączenia z zabezpieczonym klastrem w systemie Windows lub OS X przy użyciu przeglądarki można zaimportować certyfikat klienta, a w przeglądarce zostanie wyświetlony monit o certyfikat używany do nawiązywania połączenia z klastrem.  Na maszynach z systemem Linux certyfikat będzie musiał zostać zaimportowany przy użyciu zaawansowanych ustawień przeglądarki (Każda przeglądarka ma inne mechanizmy) i wskazuje na lokalizację certyfikatu na dysku. Aby uzyskać więcej informacji, zapoznaj [się z artykułem Konfigurowanie certyfikatu klienta](#connectsecureclustersetupclientcert) .

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązywanie połączenia z bezpiecznym klastrem przy użyciu Azure Active Directory

Aby nawiązać połączenie z klastrem zabezpieczonym za pomocą usługi AAD, wskaż przeglądarkę:

`https://<your-cluster-endpoint>:19080/Explorer`

Zostanie automatycznie wyświetlony monit o zalogowanie się za pomocą usługi AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązywanie połączenia z zabezpieczonym klastrem przy użyciu certyfikatu klienta

Aby nawiązać połączenie z klastrem zabezpieczonym za pomocą certyfikatów, wskaż przeglądarkę:

`https://<your-cluster-endpoint>:19080/Explorer`

Zostanie automatycznie wyświetlony monit o wybranie certyfikatu klienta.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurowanie certyfikatu klienta na komputerze zdalnym

Co najmniej dwa certyfikaty powinny być używane do zabezpieczania klastra, jeden dla klastra i certyfikatu serwera, a drugi dla dostępu klienta.  Zalecamy również korzystanie z dodatkowych certyfikatów pomocniczych i certyfikatów dostępu klienta.  Aby zabezpieczyć komunikację między klientem a węzłem klastra przy użyciu zabezpieczeń certyfikatów, najpierw należy uzyskać i zainstalować certyfikat klienta. Certyfikat można zainstalować w magazynie osobistym (my) na komputerze lokalnym lub bieżącym użytkowniku.  Wymagany jest również odcisk palca certyfikatu serwera, aby klient mógł uwierzytelnić klaster.

* W systemie Windows: kliknij dwukrotnie plik PFX i postępuj zgodnie z monitami, aby zainstalować certyfikat w magazynie osobistym `Certificates - Current User\Personal\Certificates`. Alternatywnie możesz użyć polecenia programu PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Jeśli jest to certyfikat z podpisem własnym, należy go zaimportować do magazynu "zaufane osoby" maszyny, aby można było użyć tego certyfikatu do nawiązania połączenia z zabezpieczonym klastrem.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Na komputerach Mac: kliknij dwukrotnie plik PFX, a następnie postępuj zgodnie z monitami, aby zainstalować certyfikat w pęku kluczy.

## <a name="next-steps"></a>Następne kroki

* [Service Fabric proces uaktualniania klastra i oczekiwania z niego](service-fabric-cluster-upgrade.md)
* [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric wprowadzenie modelu kondycji](service-fabric-health-introduction.md)
* [Zabezpieczenia aplikacji i Uruchom jako](service-fabric-application-runas-security.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
