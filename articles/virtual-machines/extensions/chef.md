---
title: Rozszerzenie Chef dla maszyn wirtualnych platformy Azure
description: Wdróż klienta Chef na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej Chef.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554789"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Rozszerzenie maszyny wirtualnej Chef dla systemów Linux i Windows

Firma Chef Software udostępnia platformę automatyzacji w metodyce DevOps dla systemów Linux i Windows, która umożliwia zarządzanie konfiguracjami zarówno serwerów fizycznych, jak i wirtualnych. Rozszerzenie maszyny wirtualnej Chef jest rozszerzeniem umożliwiającym Chef na maszynach wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Chef jest obsługiwane na wszystkich [obsługiwanych systemach operacyjnych](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) na platformie Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie maszyny wirtualnej Chef wymaga, aby docelowa maszyna wirtualna była połączona z Internetem w celu pobrania ładunku klienta Chef z usługi Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Chef. Rozszerzenie wymaga co najmniej adresu URL serwera Chef, nazwy klienta weryfikacji i klucza weryfikacji dla serwera Chef; te wartości można znaleźć w `knife.rb` pliku w starter-kit.zip pobranym podczas instalowania [Chef Automatyzuj](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) lub autonomicznego [serwera Chef](https://downloads.chef.io/chef-server). Ponieważ klucz weryfikacji powinien być traktowany jako dane poufne, powinien być skonfigurowany w ramach elementu **protectedSettings** , co oznacza, że zostanie on odszyfrowany tylko na docelowej maszynie wirtualnej.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Podstawowe wartości właściwości

| Nazwa | Wartość/przykład | Typ danych
| ---- | ---- | ----
| apiVersion | `2017-12-01` | ciąg (Date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | ciąg |
| typ | `LinuxChefClient` (Linux), `ChefClient` (system Windows) | ciąg |
| typeHandlerVersion | `1210.13` | ciąg (Double) |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość/przykład | Typ danych | Wymagane?
| ---- | ---- | ---- | ----
| Ustawienia/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | ciąg (URL) | Y |
| Ustawienia/bootstrap_options/validation_client_name | `myorg-validator` | ciąg | Y |
| Ustawienia/runlist | `recipe[mycookbook::default]` | ciąg | Y |

### <a name="protected-settings"></a>Ustawienia chronione

| Nazwa | Przykład | Typ danych | Wymagane?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | ciąg | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Za pomocą szablonów można wdrożyć co najmniej jedną maszynę wirtualną, zainstalować klienta Chef, nawiązać połączenie z serwerem Chef i wykonać konfigurację początkową na serwerze, zgodnie z definicją z [listy uruchamiania](https://docs.chef.io/run_lists.html)

Przykładowy szablon Menedżer zasobów, który zawiera rozszerzenie maszyny wirtualnej Chef, można znaleźć w [galerii szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

Konfiguracja JSON rozszerzenia maszyny wirtualnej może być zagnieżdżona w ramach zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższego poziomu szablonu JSON Menedżer zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu dla zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia maszyny wirtualnej Chef na istniejącej maszynie wirtualnej. Zastąp **validation_key** zawartością klucza weryfikacji (ten plik jako `.pem` rozszerzenie).  Zastąp **validation_client_name**, **chef_server_url** i **run_list** tymi wartościami z `knife.rb` pliku w zestawie startowym.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny wirtualnej. | |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [pliku Readme rozszerzenia maszyny wirtualnej Chef](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> W przypadku wszystkich innych elementów bezpośrednio związanych z Chef skontaktuj się z [pomocą techniczną Chef](https://www.chef.io/support/).

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
