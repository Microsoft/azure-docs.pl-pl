---
title: Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
description: Najlepsze rozwiązania i zagadnienia dotyczące projektowania dotyczące bezpiecznego utrzymywania klastrów i aplikacji platformy Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 90ffd1c01411982f56aed3332c499aa0c10b8a94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257602"
---
# <a name="azure-service-fabric-security"></a>Zabezpieczenia usługi Azure Service Fabric 

Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących [zabezpieczeń platformy Azure](../security/index.yml), zobacz [najlepsze rozwiązania dotyczące usługi Azure Service Fabric Security](../security/fundamentals/service-fabric-best-practices.md)

## <a name="key-vault"></a>Usługa Key Vault

[Azure Key Vault](../key-vault/index.yml) to zalecana usługa zarządzania kluczami tajnymi dla aplikacji i klastrów usługi Azure Service Fabric.
> [!NOTE]
> Jeśli certyfikaty/wpisy tajne z Key Vault są wdrażane do zestawu skalowania maszyn wirtualnych jako klucz tajny zestawu skalowania maszyn wirtualnych, wówczas Key Vault i zestaw skalowania maszyn wirtualnych muszą być wspólnie zlokalizowane.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Utwórz certyfikat Service Fabric wystawiony przez urząd certyfikacji

Certyfikat Azure Key Vault można utworzyć lub zaimportować do Key Vault. Po utworzeniu certyfikatu Key Vault klucz prywatny jest tworzony wewnątrz Key Vault i nigdy nie jest narażony na właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w Key Vault:

- Utwórz certyfikat z podpisem własnym, aby utworzyć parę kluczy publiczny-prywatny i skojarzyć ją z certyfikatem. Certyfikat zostanie podpisany przy użyciu własnego klucza. 
- Utwórz ręcznie nowy certyfikat, aby utworzyć parę kluczy publiczny-prywatny i wygenerować żądanie podpisania certyfikatu X. 509. Żądanie podpisania może być podpisane przez Urząd rejestrowania lub urząd certyfikacji. Podpisany certyfikat x509 można scalić z parą kluczy oczekujących w celu ukończenia certyfikatu KV w Key Vault. Chociaż ta metoda wymaga większej liczby kroków, zapewnia większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony w systemie i jest ograniczony do Key Vault. Wyjaśniono to na poniższym diagramie. 

Zobacz [metody tworzenia certyfikatów magazynu kluczy platformy Azure](../key-vault/certificates/create-certificate.md) , aby uzyskać dodatkowe szczegóły.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Wdrażanie certyfikatów Key Vault w Service Fabric klastrach zestawów skalowania maszyn wirtualnych

Aby wdrożyć certyfikaty z podwyższego poziomu magazynu kluczy do zestawu skalowania maszyn wirtualnych, użyj zestawu skalowania maszyn wirtualnych [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Poniżej przedstawiono właściwości szablonu Menedżer zasobów:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Magazyn musi być włączony dla wdrożenia szablonu Menedżer zasobów.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Stosowanie listy Access Control (ACL) do certyfikatu dla klastra Service Fabric

[Rozszerzenia zestawu skalowania maszyn wirtualnych](/cli/azure/vmss/extension?view=azure-cli-latest) Wydawca Microsoft. Azure. servicefabric służy do skonfigurowania zabezpieczeń węzłów.
Aby zastosować listę ACL do certyfikatów dla procesów klastra Service Fabric, użyj następujących właściwości szablonu Menedżer zasobów:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpiecz certyfikat klastra Service Fabric według nazwy pospolitej

Aby zabezpieczyć klaster Service Fabric przy użyciu certyfikatu `Common Name` , należy użyć właściwości szablonu Menedżer zasobów [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)w następujący sposób:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Klastry Service Fabric będą używać pierwszego ważnego certyfikatu, który znajduje się w magazynie certyfikatów hosta. W systemie Windows będzie to certyfikat z najnowszą datą wygaśnięcia zgodną z nazwą pospolitą i odciskiem palca wystawcy.

Domeny platformy Azure, takie jak * \<YOUR SUBDOMAIN\> . cloudapp.Azure.com lub \<YOUR SUBDOMAIN\> . trafficmanager.NET, należą do firmy Microsoft. Urzędy certyfikacji nie będą wystawiać certyfikatów dla domen dla nieautoryzowanych użytkowników. Większość użytkowników będzie musiał zakupić domenę od rejestratora lub być uprawnionym administratorem domeny, aby Urząd certyfikacji wystawiał certyfikat z tą nazwą pospolitą.

Aby uzyskać dodatkowe informacje na temat konfigurowania usługi DNS w celu rozpoznania domeny w adresie IP firmy Microsoft, zapoznaj się z tematem Konfigurowanie [Azure DNS do hostowania domeny](../dns/dns-delegate-domain-azure-dns.md).

> [!NOTE]
> Po delegowaniu serwerów nazw domen do Azure DNS serwerów nazw strefy, Dodaj następujące dwa rekordy do strefy DNS:
> - Rekord "A" dla WIERZCHOŁKa domeny, który nie jest `Alias record set` do wszystkich adresów IP, które zostaną rozpoznane przez domenę niestandardową.
> - Rekord "C" dla nieobsługiwanych domen podrzędnych Microsoft, które nie są `Alias record set` . Można na przykład użyć nazwy DNS Traffic Manager lub Load Balancer.

Aby zaktualizować Portal tak, aby wyświetlał niestandardową nazwę DNS klastra Service Fabric `"managementEndpoint"` , zaktualizuj następujące właściwości szablonu Service Fabric klastra Menedżer zasobów:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Szyfrowanie wartości tajnych pakietu Service Fabric

Typowe wartości, które są szyfrowane w pakietach Service Fabric, obejmują poświadczenia Azure Container Registry (ACR), zmienne środowiskowe, ustawienia i klucze kont magazynu wtyczki woluminu platformy Azure.

Aby [skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Windows](./service-fabric-application-secret-management-windows.md):

Wygeneruj certyfikat z podpisem własnym na potrzeby szyfrowania klucza tajnego:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Skorzystaj z instrukcji w temacie [Deploy Key Vault Certificates, aby Service Fabric klastrami zestawu skalowania maszyn wirtualnych](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) w celu wdrażania Key Vault certyfikatów na Virtual Machine Scale Sets klastra Service Fabric.

Zaszyfruj klucz tajny przy użyciu następującego polecenia programu PowerShell, a następnie zaktualizuj manifest aplikacji Service Fabric z zaszyfrowaną wartością:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Aby [skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Linux](./service-fabric-application-secret-management-linux.md):

Wygeneruj certyfikat z podpisem własnym na potrzeby szyfrowania wpisów tajnych:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Skorzystaj z instrukcji w temacie [Deploy Key Vault Certificates, aby Service Fabric zestawy skalowania maszyn wirtualnych klastra](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) Service Fabric Virtual Machine Scale Sets.

Zaszyfruj klucz tajny przy użyciu poniższych poleceń, a następnie zaktualizuj manifest aplikacji Service Fabric z zaszyfrowaną wartością:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po zaszyfrowaniu chronionych wartości [Określ zaszyfrowane klucze tajne w aplikacji Service Fabric](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application)i [Odszyfruj zaszyfrowane klucze tajne z kodu usługi](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Uwzględnij certyfikat w aplikacjach Service Fabric

Aby zapewnić aplikacji dostęp do wpisów tajnych, Uwzględnij certyfikat przez dodanie elementu **SecretsCertificate** do manifestu aplikacji.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Uwierzytelnianie aplikacji Service Fabric w zasobach platformy Azure przy użyciu tożsamość usługi zarządzanej (MSI)

Aby dowiedzieć się więcej o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).
Klastry usługi Azure Service Fabric są hostowane na Virtual Machine Scale Sets, które obsługują [tożsamość usługi zarządzanej](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
Aby uzyskać listę usług, których można użyć do uwierzytelniania za pomocą pliku MSI, zobacz [usługi platformy Azure, które obsługują uwierzytelnianie Azure Active Directory](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).


Aby włączyć tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyn wirtualnych, zadeklaruj następującą `"Microsoft.Compute/virtualMachinesScaleSets"` Właściwość:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Aby uzyskać więcej informacji, zobacz [co to są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity)

Jeśli utworzono  [tożsamość zarządzaną przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity), zadeklaruj następujący zasób w szablonie, aby przypisać go do zestawu skalowania maszyn wirtualnych. Zamień na `\<USERASSIGNEDIDENTITYNAME\>` nazwę utworzonej tożsamości zarządzanej przez użytkownika:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Aby aplikacja Service Fabric mogła korzystać z tożsamości zarządzanej, należy udzielić uprawnień do zasobów platformy Azure wymaganych do uwierzytelnienia.
Następujące polecenia zapewniają dostęp do zasobu platformy Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

W kodzie aplikacji Service Fabric [Uzyskaj token dostępu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) dla Azure Resource Manager, wykonując pozostałe czynności:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Aplikacja Service Fabric może następnie użyć tokenu dostępu do uwierzytelniania w zasobach platformy Azure, które obsługują Active Directory.
Poniższy przykład pokazuje, jak to zrobić dla Cosmos DB zasobów:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Linie bazowe zabezpieczeń systemu Windows
[Zalecamy wdrożenie standardowej konfiguracji branżowej, która jest szeroko znana i dobrze sprawdzona, takich jak linie bazowe zabezpieczeń firmy Microsoft, a nie do samodzielnego tworzenia linii bazowej](/windows/security/threat-protection/windows-security-baselines); Opcja aprowizacji tych danych na Virtual Machine Scale Sets polega na użyciu programu obsługi rozszerzeń konfiguracji żądanego stanu (DSC) platformy Azure w celu skonfigurowania maszyn wirtualnych w trybie online, tak aby korzystały z oprogramowania produkcyjnego.

## <a name="azure-firewall"></a>Azure Firewall
[Zapora systemu Azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury.](../firewall/overview.md) Dzięki temu można ograniczyć ruch wychodzący HTTP/S do określonej listy w pełni kwalifikowanych nazw domen (FQDN), w tym symboli wieloznacznych. Ta funkcja nie wymaga zakończenia protokołu TLS/SSL. Zalecane jest użycie [tagów FQDN zapory platformy Azure](../firewall/fqdn-tags.md) dla aktualizacji systemu Windows i włączenie ruchu sieciowego do punktów końcowych Windows Update firmy Microsoft przez zaporę. [Wdrażanie zapory platformy Azure przy użyciu szablonu](../firewall/deploy-template.md) zawiera przykład definicji szablonu zasobów Microsoft. Network/azureFirewalls. Reguły zapory, które są wspólne dla aplikacji Service Fabric, to Zezwalanie na następujące sieci wirtualne klastrów:

- * download.microsoft.com
- * servicefabric.azure.com
- *.core.windows.net

Te reguły zapory uzupełniają dozwolone grupy zabezpieczeń sieci wychodzącej, które będą obejmować usługi servicefabric i magazyn, jako dozwolone miejsca docelowe z sieci wirtualnej.

## <a name="tls-12"></a>TLS 1.2

[Platforma Microsoft Azure zaleca](https://azure.microsoft.com/updates/azuretls12/) , aby wszyscy klienci ukończyli migrację do rozwiązań, które obsługują protokół TLS (Transport Layer security) 1,2 i aby upewnić się, że protokół TLS 1,2 jest używany domyślnie.

Usługi platformy Azure, w tym [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493), ukończyły pracę inżynierową, aby usunąć zależność od protokołów TLS 1.0/1.1 i zapewnić klientom pełną pomoc techniczną, którzy chcą mieć skonfigurowane obciążenia, aby akceptować i inicjować tylko połączenia TLS 1,2.

Klienci powinni skonfigurować obciążenia obsługiwane przez platformę Azure i aplikacje lokalne, które współdziałają z usługami platformy Azure, aby domyślnie korzystać z protokołu TLS 1,2. Poniżej przedstawiono sposób [konfigurowania Service Fabric węzłów klastra i aplikacji](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) do korzystania z określonej wersji protokołu TLS.

## <a name="windows-defender"></a>Windows Defender 

Domyślnie program antywirusowy Windows Defender jest instalowany w systemie Windows Server 2016. Aby uzyskać szczegółowe informacje, zobacz Program [antywirusowy Windows Defender w systemie Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Interfejs użytkownika jest domyślnie instalowany w przypadku niektórych jednostek SKU, ale nie jest to wymagane. Aby zmniejszyć wpływ wydajności i obciążenia zasobów poniesione przez usługę Windows Defender, a jeśli zasady zabezpieczeń umożliwiają wykluczenie procesów i ścieżek dla oprogramowania open source, zadeklaruj następujące rozszerzenie zestawu skalowania maszyn wirtualnych Menedżer zasobów właściwości szablonu, aby wykluczyć klaster Service Fabric z skanów:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Jeśli nie korzystasz z usługi Windows Defender, zapoznaj się z dokumentacją dotyczącą ochrony przed złośliwym kodem. Usługa Windows Defender nie jest obsługiwana w systemie Linux.

## <a name="platform-isolation"></a>Izolacja platformy
Domyślnie Service Fabric aplikacje uzyskują dostęp do środowiska uruchomieniowego Service Fabric, który jest używany w różnych formularzach: [zmienne środowiskowe](service-fabric-environment-variables-reference.md) wskazujące ścieżki plików na hoście odpowiadające plikom aplikacji i sieci szkieletowej, punkt końcowy komunikacji między procesami, który akceptuje żądania specyficzne dla aplikacji, oraz certyfikat klienta, który jest oczekiwany przez aplikację do uwierzytelniania w sieci szkieletowej. W przypadku, gdy usługa hostuje sam niezaufany kod, zaleca się wyłączenie tego dostępu do środowiska uruchomieniowego SF — o ile nie jest to konieczne jawnie. Dostęp do środowiska uruchomieniowego jest usuwany przy użyciu następującej deklaracji w sekcji zasady manifestu aplikacji: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra z systemem Linux](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
