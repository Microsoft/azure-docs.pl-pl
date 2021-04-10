---
title: Azure Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux
description: Wdróż agenta, który przeprowadza automatyczne odświeżanie Key Vault certyfikatów na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582174"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux

Rozszerzenie maszyny wirtualnej Key Vault umożliwia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. Rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy.  Po wykryciu zmiany rozszerzenie pobiera i instaluje odpowiednie certyfikaty. Rozszerzenie maszyny wirtualnej Key Vault jest publikowane i obsługiwane przez firmę Microsoft, obecnie na maszynach wirtualnych z systemem Linux. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Key Vault dla systemu Linux. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Key Vault obsługuje te dystrybucje systemu Linux:

- Ubuntu-1804
- SUSE-15 

> [!NOTE]
> Aby uzyskać rozszerzone funkcje zabezpieczeń, przygotuj się do uaktualnienia systemów Ubuntu-1604 i Debian-9, ponieważ te wersje zbliżają się do końca wydłużonego okresu wsparcia.
> 

### <a name="supported-certificate-content-types"></a>Obsługiwane typy zawartości certyfikatów

- #12 PKCS
- PEM


## <a name="prerequisities"></a>Wymagania wstępne
  - Key Vault wystąpienie z certyfikatem. Zobacz [tworzenie Key Vault](../../key-vault/general/quick-create-portal.md)
  - Maszyna wirtualna/VMSS musi mieć przypisaną [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md)
  - Zasady dostępu Key Vault muszą być ustawione przy użyciu wpisów tajnych `get` i `list` uprawnień dla tożsamości ZARZĄDZANEJ maszyny wirtualnej/VMSS w celu pobrania części certyfikatu klucza tajnego. Zobacz [Jak przeprowadzić uwierzytelnianie, aby Key Vault](../../key-vault/general/authentication.md) i [przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  VMSS powinny mieć następujące ustawienie tożsamości: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - Rozszerzenie AKV powinno mieć to ustawienie: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="key-vault-vm-extension-version"></a>Wersja rozszerzenia maszyny wirtualnej Key Vault
* Ubuntu-18,04 i SUSE-15 użytkownicy mogą wybrać uaktualnienie wersji rozszerzenia maszyny wirtualnej magazynu kluczy, aby `V2.0` uzyskać pełną funkcję pobierania łańcucha certyfikatów. Certyfikaty wystawcy (pośrednie i główne) będą dołączane do certyfikatu liścia w pliku PEM.

* Jeśli wolisz uaktualnić do programu `v2.0` , musisz `v1.0` najpierw usunąć program, a następnie zainstalować `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  Flaga--w wersji 2,0 jest opcjonalna, ponieważ Najnowsza wersja zostanie zainstalowana domyślnie.   

* Jeśli maszyna wirtualna ma certyfikaty pobrane przez program v 1.0, usunięcie rozszerzenia AKVVM v 1.0 nie spowoduje usunięcia pobranych certyfikatów.  Po zainstalowaniu programu v 2.0 istniejące certyfikaty nie zostaną zmodyfikowane.  Należy usunąć pliki certyfikatu lub przekroczyć certyfikat, aby pobrać plik PEM z pełnym łańcuchem na maszynie wirtualnej.




## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Key Vault. Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są traktowane jako informacje bez wpływu na bezpieczeństwo. Rozszerzenie wymaga listy monitorowanych wpisów tajnych, częstotliwości sondowania i docelowego magazynu certyfikatów. W szczególności:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Adresy URL obserwowanych certyfikatów powinny mieć postać `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Wynika to z faktu, że `/secrets` ścieżka zwraca pełny certyfikat, w tym klucz prywatny, podczas gdy `/certificates` ścieżka nie jest. Więcej informacji o certyfikatach można znaleźć tutaj: [Key Vault Certificates](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> Właściwość "authenticationSettings" jest **wymagana** tylko w przypadku maszyn wirtualnych z **tożsamościami przypisanymi przez użytkownika**.
> Określa tożsamość do użycia podczas uwierzytelniania do Key Vault.


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | data |
| publisher | Microsoft.Azure.KeyVault | ciąg |
| typ | KeyVaultForLinux | ciąg |
| typeHandlerVersion | 2.0 | int |
| pollingIntervalInS | 3600 | ciąg |
| certificateStoreName | Jest on ignorowany w systemie Linux | ciąg |
| linkOnRenewal | fałsz | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | ciąg |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | Tablica ciągów
| msiEndpoint | http://169.254.169.254/metadata/identity | ciąg |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | ciąg |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga odświeżenia certyfikatów po wdrożeniu. Rozszerzenie można wdrożyć na poszczególnych maszynach wirtualnych lub w zestawach skalowania maszyn wirtualnych. Schemat i konfiguracja są wspólne dla obu typów szablonów. 

Konfiguracja JSON rozszerzenia maszyny wirtualnej musi być zagnieżdżona w ramach fragmentu zasobów maszyny wirtualnej szablonu, `"resources": []` w odniesieniu do szablonu maszyny wirtualnej, a w przypadku zestawu skalowania maszyn wirtualnych w obszarze `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` obiekt.

 > [!NOTE]
> Rozszerzenie maszyny wirtualnej wymaga przypisania tożsamości zarządzanej przez system lub użytkownika do uwierzytelniania w magazynie kluczy.  Zobacz [Jak przeprowadzić uwierzytelnianie, aby Key Vault i przypisać zasady dostępu Key Vault.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Porządkowanie rozszerzeń
Rozszerzenie maszyny wirtualnej Key Vault obsługuje kolejność rozszerzeń, jeśli są skonfigurowane. Domyślnie po rozpoczęciu sondowania rozszerzenia raporty zostały pomyślnie uruchomione. Można go jednak skonfigurować tak, aby czekał na pomyślne pobranie pełnej listy certyfikatów przed rozpoczęciem raportowania. Jeśli inne rozszerzenia zależą od zainstalowania przed rozpoczęciem pełnego zestawu certyfikatów, włączenie tego ustawienia umożliwi tym rozszerzeniu zadeklarować zależność na rozszerzeniu Key Vault. Uniemożliwi to uruchomienie tych rozszerzeń, dopóki nie zostaną zainstalowane wszystkie certyfikaty, od których zależą. Rozszerzenie ponowi próbę pobrania początkowego przez nieograniczony czas i pozostanie w `Transitioning` stanie.

Aby je włączyć, ustaw następujące elementy:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Korygując Korzystanie z tej funkcji jest niezgodne z szablonem usługi ARM, który tworzy tożsamość przypisaną do systemu i aktualizuje zasady dostępu Key Vault przy użyciu tej tożsamości. Wykonanie tej czynności spowoduje zakleszczenie, ponieważ nie będzie można zaktualizować zasad dostępu do magazynu, dopóki nie zostaną uruchomione wszystkie rozszerzenia. Przed wdrożeniem należy zamiast tego użyć *pojedynczej tożsamości pliku MSI przypisanej do użytkownika* i wstępnie listy ACL magazynów z tą tożsamością.

## <a name="azure-powershell-deployment"></a>Wdrożenie Azure PowerShell
> [!WARNING]
> Klienci programu PowerShell często dodają `\` do `"` programu w settings.js, na którym spowoduje to niepowodzenie akvvm_service z powodu błędu: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Azure PowerShell można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault do istniejącej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault na istniejącej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. 
 
* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Należy pamiętać o następujących ograniczeniach/wymaganiach:
- Ograniczenia Key Vault:
  - Musi istnieć w czasie wdrożenia 
  - Zasady dostępu Key Vault muszą być ustawione dla tożsamości VM/VMSS przy użyciu tożsamości zarządzanej. Zobacz [Jak przeprowadzić uwierzytelnianie, aby Key Vault](../../key-vault/general/authentication.md) i [przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-cli.md).

### <a name="frequently-asked-questions"></a>Często zadawane pytania

* Czy istnieje limit liczby observedCertificates można skonfigurować?
  Nie, Key Vault rozszerzenie maszyny wirtualnej nie ma limitu liczby observedCertificates.


### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Interfejs wiersza polecenia platformy Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>Dzienniki i konfiguracja

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Korzystanie z link symboliczny

Linki symboliczne lub linków symbolicznych są zasadniczo zaawansowanymi skrótami. Aby uniknąć monitorowania folderu i automatycznego uzyskiwania najnowszego certyfikatu, możesz użyć tego link symboliczny `([VaultName].[CertificateName])` do pobrania najnowszej wersji certyfikatu w systemie Linux.

### <a name="frequently-asked-questions"></a>Często zadawane pytania

* Czy istnieje limit liczby observedCertificates można skonfigurować?
  Nie, Key Vault rozszerzenie maszyny wirtualnej nie ma limitu liczby observedCertificates.

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
