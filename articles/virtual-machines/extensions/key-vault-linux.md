---
title: Azure Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux
description: Wdróż agenta, który przeprowadza automatyczne odświeżanie Key Vault certyfikatów na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: add2d515e4f8e8c56a98a7292e137e601332d10c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410863"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux

Rozszerzenie maszyny wirtualnej Key Vault umożliwia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. Rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy.  Po wykryciu zmiany rozszerzenie pobiera i instaluje odpowiednie certyfikaty. Rozszerzenie maszyny wirtualnej Key Vault jest publikowane i obsługiwane przez firmę Microsoft, obecnie na maszynach wirtualnych z systemem Linux. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Key Vault dla systemu Linux. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Key Vault obsługuje te dystrybucje systemu Linux:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- SUSE-15 

### <a name="supported-certificate-content-types"></a>Obsługiwane typy zawartości certyfikatów

- #12 PKCS
- PEM

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Key Vault. Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są traktowane jako informacje bez wpływu na bezpieczeństwo. Rozszerzenie wymaga listy monitorowanych wpisów tajnych, częstotliwości sondowania i docelowego magazynu certyfikatów. Są to:  
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
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Adresy URL obserwowanych certyfikatów powinny mieć postać `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Wynika to z `/secrets` faktu, że ścieżka zwraca pełny certyfikat, w tym klucz prywatny, `/certificates` podczas gdy ścieżka nie jest. Więcej informacji o certyfikatach można znaleźć tutaj: [Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | ciąg |
| type | KeyVaultForLinux | ciąg |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | ciąg |
| certificateStoreName | MY | ciąg |
| linkOnRenewal | fałsz | wartość logiczna |
| certificateStoreLocation  | LocalMachine | ciąg |
| requiredInitialSync | true | wartość logiczna |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | Tablica ciągów


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga odświeżenia certyfikatów po wdrożeniu. Rozszerzenie można wdrożyć na poszczególnych maszynach wirtualnych lub w zestawach skalowania maszyn wirtualnych. Schemat i konfiguracja są wspólne dla obu typów szablonów. 

Konfiguracja JSON rozszerzenia maszyny wirtualnej musi być zagnieżdżona w ramach fragmentu zasobów maszyny wirtualnej szablonu, w odniesieniu `"resources": []` do szablonu maszyny wirtualnej, a w przypadku zestawu skalowania maszyn wirtualnych w obszarze `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` obiekt.

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
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Wdrożenie Azure PowerShell

Azure PowerShell można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault do istniejącej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
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
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
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
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Należy pamiętać o następujących ograniczeniach/wymaganiach:
- Ograniczenia Key Vault:
  - Musi istnieć w czasie wdrożenia 
  - Zasady dostępu Key Vault są ustawione dla tożsamości VM/VMSS przy użyciu pliku MSI


## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
