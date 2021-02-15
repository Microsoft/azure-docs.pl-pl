---
title: Azure Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows
description: Wdróż agenta wykonującego automatyczne odświeżanie Key Vault wpisów tajnych na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f7c8a7eb06490a46e1c5b633944dcd596fa08515
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093628"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows

Rozszerzenie maszyny wirtualnej Key Vault umożliwia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. W związku z tym rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy, a po wykryciu zmiany pobiera i instaluje odpowiednie certyfikaty. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Key Vault dla systemu Windows. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Key Vault obsługuje poniższe wersje systemu Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

Rozszerzenie maszyny wirtualnej Key Vault jest również obsługiwane na niestandardowej lokalnej maszynie wirtualnej, która została przekazana i przekonwertowana na wyspecjalizowany obraz do użycia na platformie Azure przy użyciu instalacji podstawowej systemu Windows Server 2019.

### <a name="supported-certificate-content-types"></a>Obsługiwane typy zawartości certyfikatów

- #12 PKCS
- PEM

## <a name="prerequisites"></a>Wymagania wstępne

  - Key Vault wystąpienie z certyfikatem. Zobacz [tworzenie Key Vault](../../key-vault/general/quick-create-portal.md)
  - Maszyna wirtualna musi mieć przypisaną [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md)
  - Zasady dostępu Key Vault muszą być ustawione przy użyciu wpisów tajnych `get` i `list` uprawnień dla tożsamości ZARZĄDZANEJ maszyny wirtualnej/VMSS w celu pobrania części certyfikatu klucza tajnego. Zobacz [Jak przeprowadzić uwierzytelnianie, aby Key Vault](../../key-vault/general/authentication.md) i [przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  Virtual Machine Scale Sets powinna mieć następujące ustawienie tożsamości:

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - Rozszerzenie AKV powinno mieć to ustawienie:

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Key Vault. Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są uznawane za informacje publiczne. Rozszerzenie wymaga listy monitorowanych certyfikatów, częstotliwości sondowania i docelowego magazynu certyfikatów. W szczególności:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
| typ | KeyVaultForWindows | ciąg |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | ciąg |
| certificateStoreName | MY | ciąg |
| linkOnRenewal | fałsz | boolean |
| certificateStoreLocation  | LocalMachine lub CurrentUser (z uwzględnieniem wielkości liter) | ciąg |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | Tablica ciągów
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
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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

> [!Note] 
> Korzystanie z tej funkcji jest niezgodne z szablonem usługi ARM, który tworzy tożsamość przypisaną do systemu i aktualizuje zasady dostępu Key Vault przy użyciu tej tożsamości. Wykonanie tej czynności spowoduje zakleszczenie, ponieważ nie będzie można zaktualizować zasad dostępu do magazynu, dopóki nie zostaną uruchomione wszystkie rozszerzenia. Przed wdrożeniem należy zamiast tego użyć *pojedynczej tożsamości pliku MSI przypisanej do użytkownika* i wstępnie listy ACL magazynów z tą tożsamością.

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
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault na istniejącej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. 
 
* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```azurecli
       # Start the deployment
         az vm extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Należy pamiętać o następujących ograniczeniach/wymaganiach:
- Ograniczenia Key Vault:
  - Musi istnieć w czasie wdrożenia 
  - Zasady dostępu Key Vault muszą być ustawione dla tożsamości VM/VMSS przy użyciu tożsamości zarządzanej. Zobacz [Jak przeprowadzić uwierzytelnianie, aby Key Vault](../../key-vault/general/authentication.md) i [przypisać zasady dostępu Key Vault](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

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
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).