---
title: Tworzenie maszyn wirtualnych i zarządzanie nimi w usłudze DevTest Labs przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać usługi Azure DevTest Labs do tworzenia maszyn wirtualnych i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 95e0add8ce14e47c609b1ae951673c261316293f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763545"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Tworzenie maszyn wirtualnych i zarządzanie nimi za pomocą usługi DevTest Labs przy użyciu interfejsu wiersza polecenia platformy Azure
Ten przewodnik Szybki start poprowadzi Cię przez proces tworzenia, uruchamiania, łączenia, aktualizowania i czyszczenia maszyny dewelopera w laboratorium. 

Przed rozpoczęciem:

* Jeśli laboratorium nie zostało utworzone, instrukcje można znaleźć [tutaj.](devtest-lab-create-lab.md)

* [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Aby rozpocząć, uruchom az login, aby utworzyć połączenie z platformą Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Tworzenie i weryfikowanie maszyny wirtualnej 
Przed wykonaniem poleceń powiązanych z usługą DevTest Labs ustaw odpowiedni kontekst platformy Azure za pomocą `az account set` polecenia :

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Polecenie tworzenia maszyny wirtualnej to: `az lab vm create` . Wymagana jest grupa zasobów laboratorium, nazwa laboratorium i nazwa maszyny wirtualnej. Pozostałe argumenty zmieniają się w zależności od typu maszyny wirtualnej.

Następujące polecenie tworzy obraz oparty na systemie Windows z witryny Azure Market Place. Nazwa obrazu jest taka sama, jak podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Następujące polecenie tworzy maszynę wirtualną na podstawie obrazu niestandardowego dostępnego w laboratorium:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Argument **typu obrazu został zmieniony** z **galerii** na **niestandardowy**. Nazwa obrazu jest podobna do tej, która zostanie wyświetlony w przypadku utworzenia maszyny wirtualnej w Azure Portal.

Następujące polecenie tworzy maszynę wirtualną z obrazu z witryny Marketplace z uwierzytelnianiem SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Maszyny wirtualne można również tworzyć na podstawie formuł, ustawiając parametr **typu** obrazu na **formułę**. Jeśli musisz wybrać konkretną sieć wirtualną dla maszyny wirtualnej, użyj parametrów **vnet-name** i **subnet.** Aby uzyskać więcej informacji, zobacz [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="verify-that-the-vm-is-available"></a>Sprawdź, czy maszyna wirtualna jest dostępna.
Użyj polecenia `az lab vm show` , aby sprawdzić, czy maszyna wirtualna jest dostępna, zanim rozpoczniesz i połączysz się z maszyną wirtualną. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Uruchamianie maszyny wirtualnej i łączenie się z maszyną wirtualną
Następujące przykładowe polecenie uruchamia maszynę wirtualną:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Nawiązywanie połączenia z maszyną wirtualną: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) [lub Pulpit zdalny](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualizowanie maszyny wirtualnej
Następujące przykładowe polecenie stosuje artefakty do maszyny wirtualnej:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Lista artefaktów dostępnych w laboratorium

Aby wyświetlić listę artefaktów dostępnych na maszynie wirtualnej w laboratorium, uruchom następujące polecenia.

**Cloud Shell — PowerShell:** zwróć uwagę na użycie przycisku backtick ( ) przed wartością $ w $expand \` (tj. "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell — Bash:** zwróć uwagę na użycie znaku ukośnika ( ) przed \\ znakiem $ w poleceniu . 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Przykładowe dane wyjściowe: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Zatrzymywanie i usuwanie maszyny wirtualnej    
Następujące przykładowe polecenie zatrzymuje maszynę wirtualną.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Usuwanie maszyny wirtualnej.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Następne kroki
Zobacz następującą zawartość: Dokumentacja interfejsu [wiersza polecenia platformy Azure dla Azure DevTest Labs](/cli/azure/lab). 
