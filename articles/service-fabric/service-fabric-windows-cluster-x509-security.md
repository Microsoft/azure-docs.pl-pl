---
title: Zabezpieczanie klastra w systemie Windows przy użyciu certyfikatów
description: Bezpieczna komunikacja w ramach autonomicznego lub lokalnego klastra usługi Azure Service Fabric, a także między klientami a klastrem.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791041"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpieczanie klastra autonomicznego w systemie Windows za pomocą certyfikatów X. 509
W tym artykule opisano sposób zabezpieczania komunikacji między różnymi węzłami autonomicznego klastra systemu Windows. Opisano w nim również sposób uwierzytelniania klientów łączących się z tym klastrem za pomocą certyfikatów X. 509. Uwierzytelnianie zapewnia, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożonych aplikacji oraz wykonywać zadania zarządzania. Zabezpieczenia certyfikatów należy włączyć w klastrze podczas tworzenia klastra.  

Aby uzyskać więcej informacji o zabezpieczeniach klastra, takich jak zabezpieczenia typu węzeł-węzeł, zabezpieczenia klienta do węzła i kontrola dostępu oparta na rolach, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Których certyfikatów potrzebujesz?
Aby rozpocząć pracę z programem, [Pobierz pakiet Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednego z węzłów w klastrze. W pobranym pakiecie znajdziesz ClusterConfig.X509.MultiMachine.jspliku. Otwórz plik i zapoznaj się z sekcją zabezpieczenia w sekcji Właściwości:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

W tej sekcji opisano certyfikaty, które należy zabezpieczyć autonomiczny klaster systemu Windows. Jeśli określisz certyfikat klastra, ustaw wartość ClusterCredentialType na _x509_. W przypadku określenia certyfikatu serwera dla połączeń zewnętrznych należy ustawić ServerCredentialType na _x509_. Chociaż nie jest to wymagane, zaleca się, aby oba te certyfikaty były prawidłowo zabezpieczonym klastrem. Jeśli te wartości są ustawiane na *x509*, należy również określić odpowiednie certyfikaty lub Service Fabric zgłasza wyjątek. W niektórych scenariuszach warto określić tylko _ClientCertificateThumbprints_ lub _ReverseProxyCertificate_. W tych scenariuszach nie trzeba ustawiać _ClusterCredentialType_ lub _ServerCredentialType_ na _x509_.


> [!NOTE]
> [Odcisk palca](https://en.wikipedia.org/wiki/Public_key_fingerprint) to podstawowa tożsamość certyfikatu. Aby uzyskać odcisk palca tworzonych certyfikatów, zobacz [Pobieranie odcisku palca certyfikatu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
> 
> 

W poniższej tabeli wymieniono certyfikaty, które są potrzebne w konfiguracji klastra:

| **Ustawienie CertificateInformation** | **Opis** |
| --- | --- |
| ClusterCertificate |Zalecane dla środowiska testowego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Do uaktualnienia można użyć dwóch różnych certyfikatów, podstawowych i pomocniczych. Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisk palca i pomocniczy w zmiennych ThumbprintSecondary. |
| ClusterCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć jednej lub dwóch wspólnych nazw certyfikatów klastra. CertificateIssuerThumbprint odnosi się do odcisku palca wystawcy tego certyfikatu. Jeśli jest używany więcej niż jeden certyfikat o tej samej nazwie pospolitej, można określić wiele odcisków palców wystawcy.|
| ClusterCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu klastra. W tej sekcji można podać nazwę pospolitą wystawcy i odpowiadającą jej nazwę magazynu zamiast określania odcisku palca wystawcy w obszarze ClusterCertificateCommonNames.  Dzięki temu można łatwo przerzucać certyfikaty wystawców klastra. Jeśli jest używany więcej niż jeden certyfikat klastra, można określić wielu wystawców. Puste IssuerCommonName zezwala na wszystkie certyfikaty w odpowiednich magazynach określonych w obszarze X509StoreNames.|
| ServerCertificate |Zalecane dla środowiska testowego. Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. Dla wygody możesz użyć tego samego certyfikatu dla ClusterCertificate i ServerCertificate. Do uaktualnienia można użyć dwóch różnych certyfikatów serwera, podstawowego i pomocniczego. Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisk palca i pomocniczy w zmiennych ThumbprintSecondary. |
| ServerCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. CertificateIssuerThumbprint odnosi się do odcisku palca wystawcy tego certyfikatu. Jeśli jest używany więcej niż jeden certyfikat o tej samej nazwie pospolitej, można określić wiele odcisków palców wystawcy. Dla wygody możesz użyć tego samego certyfikatu dla ClusterCertificateCommonNames i ServerCertificateCommonNames. Można użyć jednej lub dwóch wspólnych nazw certyfikatów serwera. |
| ServerCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu serwera. W tej sekcji można podać nazwę pospolitą wystawcy i odpowiadającą jej nazwę magazynu zamiast określania odcisku palca wystawcy w obszarze ServerCertificateCommonNames.  Dzięki temu można łatwo przerzucać certyfikaty wystawcy serwera. Jeśli jest używany więcej niż jeden certyfikat serwera, można określić wielu wystawców. Puste IssuerCommonName zezwala na wszystkie certyfikaty w odpowiednich magazynach określonych w obszarze X509StoreNames.|
| ClientCertificateThumbprints |Zainstaluj ten zestaw certyfikatów na uwierzytelnionych klientach. Na maszynach, które mają zezwalać na dostęp do klastra, można zainstalować wiele różnych certyfikatów klienta. Ustaw odcisk palca każdego certyfikatu w zmiennej CertificateThumbprint. Jeśli ustawisz wartość IsAdmin na *true*, klient z zainstalowanym tym certyfikatem może wykonywać działania związane z zarządzaniem administratorami w klastrze. Jeśli administrator ma *wartość false*, klient z tym certyfikatem może wykonywać akcje tylko dla praw dostępu użytkownika, zwykle tylko do odczytu. Aby uzyskać więcej informacji na temat ról, zobacz [Service Fabric kontroli dostępu opartej na rolach](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |Ustaw nazwę pospolitą pierwszego certyfikatu klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odciskiem palca dla wystawcy tego certyfikatu. Aby dowiedzieć się więcej na temat typowych nazw i wystawcy, zobacz [Work with Certificates](/dotnet/framework/wcf/feature-details/working-with-certificates). |
| ClientCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu klienta (role administratora i nie administratora). W tej sekcji można podać nazwę pospolitą wystawcy i odpowiadającą jej nazwę magazynu zamiast określania odcisku palca wystawcy w obszarze ClientCertificateCommonNames.  Dzięki temu można łatwo przerzucać certyfikaty wystawcy klienta. Jeśli jest używany więcej niż jeden certyfikat klienta, można określić wielu wystawców. Puste IssuerCommonName zezwala na wszystkie certyfikaty w odpowiednich magazynach określonych w obszarze X509StoreNames.|
| ReverseProxyCertificate |Zalecane dla środowiska testowego. Ten opcjonalny certyfikat można określić, jeśli chcesz zabezpieczyć [zwrotny serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że reverseProxyEndpointPort jest ustawiony w elementów NodeType, jeśli używasz tego certyfikatu. |
| ReverseProxyCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten opcjonalny certyfikat można określić, jeśli chcesz zabezpieczyć [zwrotny serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że reverseProxyEndpointPort jest ustawiony w elementów NodeType, jeśli używasz tego certyfikatu. |

Poniżej przedstawiono przykładową konfigurację klastra, w której zostały podane certyfikaty klastra, serwera i klienta. W przypadku certyfikatów klaster/serwer/elementu reverseproxy nie można skonfigurować jednocześnie odcisku palca i nazwy pospolitej dla tego samego typu certyfikatu.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Przerzucanie certyfikatów
Jeśli używasz nazwy pospolitej certyfikatu zamiast odcisku palca, przerzucanie certyfikatów nie wymaga uaktualnienia konfiguracji klastra. W przypadku uaktualnień odcisków palca wystawcy upewnij się, że nowa lista odcisków palców jest przecinana ze starą listą Najpierw należy przeprowadzić uaktualnienie konfiguracji z nowymi odciskami palców wystawcy, a następnie zainstalować nowe certyfikaty (certyfikat klastra/serwera i certyfikaty wystawców) w magazynie. Należy zachować stary certyfikat wystawcy w magazynie certyfikatów przez co najmniej dwie godziny po zainstalowaniu nowego certyfikatu wystawcy.
Jeśli używasz magazynów wystawcy, nie trzeba wykonywać żadnych uaktualnień konfiguracji w celu przerzucenia certyfikatu wystawcy. Zainstaluj nowy certyfikat wystawcy z ostatnią datą wygaśnięcia w odpowiednim magazynie certyfikatów i usuń stary certyfikat wystawcy po kilku godzinach.

## <a name="acquire-the-x509-certificates"></a>Pozyskiwanie certyfikatów X. 509
Aby zabezpieczyć komunikację w klastrze, należy najpierw uzyskać certyfikaty X. 509 dla węzłów klastra. Ponadto w celu ograniczenia połączenia z tym klastrem z autoryzowanymi komputerami/użytkownikami należy uzyskać i zainstalować certyfikaty dla komputerów klienckich.

W przypadku klastrów z uruchomionym obciążeniem produkcyjnym należy użyć certyfikatu X. 509 podpisanego przez [urząd certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority), aby zabezpieczyć klaster. Aby uzyskać więcej informacji na temat uzyskiwania tych certyfikatów, zobacz [jak uzyskać certyfikat](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

Istnieje wiele właściwości, które musi mieć certyfikat, aby zapewnić prawidłowe działanie:

* Dostawca certyfikatu musi być dostawcą **usług kryptograficznych Microsoft Enhanced RSA i AES**

* Podczas tworzenia klucza RSA upewnij się, że klucz to **2048 bitów**.

* Rozszerzenie użycie klucza ma wartość **podpis cyfrowy, szyfrowanie klucza (a0)**

* Rozszerzenie rozszerzonego użycia klucza ma wartości **uwierzytelnianie serwera** (OID: 1.3.6.1.5.5.7.3.1) i **uwierzytelnianie klienta** (OID: 1.3.6.1.5.5.7.3.2)

W przypadku klastrów używanych do celów testowych można wybrać opcję użycia certyfikatu z podpisem własnym.

Dodatkowe pytania można znaleźć w tematach [często zadawanych pytań dotyczących certyfikatów](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions).

## <a name="optional-create-a-self-signed-certificate"></a>Opcjonalne: Tworzenie certyfikatu z podpisem własnym
Jednym ze sposobów utworzenia certyfikatu z podpisem własnym, który może być zabezpieczony prawidłowo, jest użycie skryptu CertSetup.ps1 w folderze Service Fabric SDK w katalogu C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edytuj ten plik, aby zmienić domyślną nazwę certyfikatu. (Poszukaj wartości CN = ServiceFabricDevClusterCert). Uruchom ten skrypt jako `.\CertSetup.ps1 -Install` .

Teraz Wyeksportuj certyfikat do pliku PFX z chronionym hasłem. Najpierw należy uzyskać odcisk palca certyfikatu. 
1. Z menu **Start** Uruchom polecenie **Zarządzaj certyfikatami komputera**. 

2. Przejdź do folderu **lokalnego komputera** i Znajdź utworzony certyfikat. 

3. Kliknij dwukrotnie certyfikat, aby go otworzyć, wybierz kartę **szczegóły** , a następnie przewiń w dół do pola **odcisk palca** . 

4. Usuń spacje i skopiuj wartość odcisku palca do poniższego polecenia programu PowerShell. 

5. Zmień `String` wartość na odpowiednie bezpieczne hasło, aby je chronić, a następnie uruchom następujące polecenie w programie PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Aby wyświetlić szczegóły certyfikatu zainstalowanego na komputerze, uruchom następujące polecenie programu PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternatywnie, jeśli masz subskrypcję platformy Azure, wykonaj kroki opisane w temacie [Tworzenie klastra Service Fabric przy użyciu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalowanie certyfikatów
Po przypisaniu certyfikatów można je zainstalować w węzłach klastra. W węzłach musi być zainstalowany najnowszy program Windows PowerShell 3. x. Powtórz te czynności na każdym węźle dla certyfikatów klastra i serwera oraz wszystkich dodatkowych certyfikatów.

1. Skopiuj plik PFX lub pliki do węzła.

2. Otwórz okno programu PowerShell jako administrator i wprowadź następujące polecenia. Zastąp *$PSWD* hasłem użytym do utworzenia tego certyfikatu. Zastąp *$PfxFilePath* pełną ścieżką pliku PFX skopiowanego do tego węzła.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teraz ustaw kontrolę dostępu dla tego certyfikatu tak, aby proces Service Fabric, który działa w ramach konta usługi sieciowej, mógł go użyć, uruchamiając następujący skrypt. Podaj odcisk palca certyfikatu i **usługi sieciowej** dla konta usługi. Możesz sprawdzić, czy listy ACL certyfikatu są poprawne, otwierając certyfikat w **menu Start**  >  **Zarządzanie certyfikatami komputera** i przeglądając **wszystkie zadania**  >  **Zarządzaj kluczami prywatnymi**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Powtórz poprzednie kroki dla każdego certyfikatu serwera. Można również użyć tych kroków, aby zainstalować certyfikaty klienta na maszynach, które mają zezwalać na dostęp do klastra.

## <a name="create-the-secure-cluster"></a>Tworzenie bezpiecznego klastra
Po skonfigurowaniu sekcji Zabezpieczenia ClusterConfig.X509.MultiMachine.jsw pliku można przejoć do sekcji [Tworzenie klastra](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) , aby skonfigurować węzły i utworzyć klaster autonomiczny. Pamiętaj, aby podczas tworzenia klastra używać ClusterConfig.X509.MultiMachine.jspliku. Na przykład polecenie może wyglądać następująco:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po pomyślnym uruchomieniu bezpiecznego autonomicznego klastra systemu Windows i skonfigurowaniu uwierzytelnionych klientów w celu nawiązania z nim połączenia wykonaj kroki opisane w sekcji [nawiązywanie połączenia z klastrem przy użyciu programu PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) . Na przykład:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Następnie można uruchomić inne polecenia programu PowerShell, aby pracować z tym klastrem. Na przykład można uruchomić polecenie [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) , aby wyświetlić listę węzłów w tym bezpiecznym klastrze.


Aby usunąć klaster, Połącz się z węzłem w klastrze, w którym został pobrany pakiet Service Fabric, Otwórz wiersz polecenia i przejdź do folderu pakietu. Teraz uruchom następujące polecenie:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Niepoprawna konfiguracja certyfikatu może uniemożliwić przejście klastra podczas wdrażania. Aby automatycznie zdiagnozować problemy z zabezpieczeniami, zapoznaj się z **dziennikami Podgląd zdarzeń aplikacji i usług** w  >  **witrynie Microsoft-Service Fabric**.
> 
> 
