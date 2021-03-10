---
title: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu API REST
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure, która używa Managed Disks i uwierzytelniania SSH przy użyciu interfejsu API REST platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 519939445e67f0f993662e2faf506eb186686156
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554568"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Tworzenie maszyny wirtualnej z systemem Linux korzystającej z uwierzytelniania SSH przy użyciu interfejsu API REST

Maszyna wirtualna z systemem Linux na platformie Azure składa się z różnych zasobów, takich jak dyski i interfejsy sieciowe, oraz definiuje parametry, takie jak lokalizacja, rozmiar i obraz systemu operacyjnego oraz ustawienia uwierzytelniania.

Możesz utworzyć maszynę wirtualną z systemem Linux za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure 2,0, wielu zestawów SDK platformy Azure, szablonów Azure Resource Manager i wielu narzędzi innych firm, takich jak rozwiązania ansible lub Terraform. Wszystkie te narzędzia ostatecznie używają interfejsu API REST w celu utworzenia maszyny wirtualnej z systemem Linux.

W tym artykule pokazano, jak za pomocą interfejsu API REST utworzyć maszynę wirtualną z systemem Linux Ubuntu 18,04-LTS z użyciem dysków zarządzanych i uwierzytelniania SSH.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed utworzeniem i przesłaniem żądania należy:

* `{subscription-id}`Dla subskrypcji
  * Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli)
* `{resourceGroupName}`Utworzony wcześniej czas
* [Interfejs sieci wirtualnej](../../virtual-network/virtual-network-network-interface.md) w tej samej grupie zasobów
* Para kluczy SSH (możesz [wygenerować nowe](mac-create-ssh-keys.md) , jeśli nie masz takiego)

## <a name="request-basics"></a>Podstawowe informacje o żądaniach

Aby utworzyć lub zaktualizować maszynę wirtualną, należy użyć następującej operacji *Put* :

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Oprócz `{subscription-id}` parametrów i należy `{resourceGroupName}` określić `{vmName}` ( `api-version` jest opcjonalne, ale ten artykuł został przetestowany przy użyciu `api-version=2017-12-01` )

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Content-Type:*  | Wymagany. Ustaw wartość `application/json`. |
| *Authorization:* | Wymagany. Ustaw na prawidłowy [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Aby uzyskać ogólne informacje na temat pracy z żądaniami interfejsu API REST, zobacz [składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do kompilowania treści żądania:

| Nazwa                       | Wymagany | Typ                                                                                | Opis  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | ciąg                                                                              | Lokalizacja zasobu. |
| name                       |          | ciąg                                                                              | Nazwa maszyny wirtualnej. |
| Właściwości. — obiekt hardwareprofile |          | [— Obiekt hardwareprofile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Określa ustawienia sprzętu dla maszyny wirtualnej. |
| Właściwości. obszarze storageprofile  |          | [Obszarze storageprofile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Określa ustawienia magazynu dla dysków maszyny wirtualnej. |
| Właściwości. osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Określa ustawienia systemu operacyjnego dla maszyny wirtualnej. |
| Właściwości. networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Określa interfejsy sieciowe maszyny wirtualnej. |

Przykładowa treść żądania jest poniżej. Upewnij się, że nazwa maszyny wirtualnej została określona `{computerName}` w `{name}` parametrach i, nazwa interfejsu sieciowego utworzonego w sekcji, nazwa `networkInterfaces` użytkownika w `adminUsername` i `path` i *publiczna* część pary kluczy SSH (na przykład `~/.ssh/id_rsa.pub` ) w `keyData` . Inne parametry, które warto zmodyfikować, obejmują `location` i `vmSize` .  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Aby uzyskać pełną listę dostępnych definicji w treści żądania, zobacz temat [maszyny wirtualne Tworzenie lub aktualizowanie definicji treści żądania](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Wysyłanie żądania

Aby wysłać to żądanie HTTP, można użyć klienta z preferencją. Możesz również użyć [Narzędzia w przeglądarce](/rest/api/compute/virtualmachines/createorupdate) , klikając przycisk **Wypróbuj** .

### <a name="responses"></a>Odpowiedzi

Istnieją dwie Pomyślne odpowiedzi na operację tworzenia lub aktualizowania maszyny wirtualnej:

| Nazwa        | Typ                                                                              | Opis |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Utworzono | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Utworzone     |

W przypadku zmniejszonej *201* odpowiedzi z poprzedniej przykładowej treści żądania, która tworzy maszynę wirtualną, zostanie przypisany *Identyfikator maszyny* wirtualnej, a *provisioningState* jest *tworzony*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [Przetwarzanie komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure lub innych narzędzi do zarządzania, takich jak interfejs wiersza polecenia platformy Azure lub Azure PowerShell, zobacz następujące tematy:

- [Interfejs API REST dostawcy obliczeń platformy Azure](/rest/api/compute/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/)
- [Moduł programu Azure PowerShell](/powershell/azure/)
