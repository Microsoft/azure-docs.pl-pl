---
title: Azure Disk Encryption dla systemu Linux
description: Wdraża Azure Disk Encryption dla systemu Linux na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792385"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption dla systemu Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Omówienie

Azure Disk Encryption korzysta z podsystemu dm-crypt w systemie Linux w celu zapewnienia pełnego szyfrowania dysków w wybranych dystrybucjach [systemu Linux na platformie Azure.](../linux/disk-encryption-overview.md)  To rozwiązanie jest zintegrowane z Azure Key Vault do zarządzania kluczami szyfrowania dysków i wpisami tajnymi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz Azure Disk Encryption dla maszyn wirtualnych z systemem [Linux,](../linux/disk-encryption-overview.md)a w szczególności następujące sekcje:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Dodatkowe wymagania dotyczące maszyny wirtualnej](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Wymagania dotyczące sieci](../linux/disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schemat rozszerzenia

Istnieją dwie wersje schematu rozszerzenia dla Azure Disk Encryption (ADE):
- Wersja 1.1 — nowsze zalecane schematy, które nie używają Azure Active Directory (AAD).
- v0.1 — starszy schemat, który wymaga Azure Active Directory właściwości (AAD). 

Aby wybrać schemat docelowy, właściwość musi być równa wersji schematu, `typeHandlerVersion` którego chcesz użyć.

### <a name="schema-v11-no-aad-recommended"></a>Schemat 1.1: brak usługi AAD (zalecane)

Schemat w wersji 1.1 jest zalecany i nie wymaga Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schemat 0.1: z usługą AAD 

Schemat 0.1 wymaga i `AADClientID` lub `AADClientSecret` `AADClientCertificate` .

Przy użyciu `AADClientSecret` programu :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Przy użyciu `AADClientCertificate` programu :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | data |
| publisher | Microsoft.Azure.Security | ciąg |
| typ | AzureDiskEncryptionForLinux | ciąg |
| typeHandlerVersion | 1.1, 0.1 | int |
| (Schemat 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (Schemat 0.1) AADClientSecret | hasło | ciąg |
| (Schemat 0.1) AADClientCertificate | Odcisk palca | ciąg |
| (opcjonalnie) (Schemat 0.1) Hasło | hasło | ciąg |
| DiskFormatQuery | {"dev_path":","name":","file_system":"} | Słownik JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | ciąg | 
| (Opcjonalnie — domyślnie RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | ciąg |
| KeyVaultURL | url | ciąg |
| KeyVaultResourceId | url | ciąg |
| (opcjonalnie) KeyEncryptionKeyURL | url | ciąg |
| (opcjonalnie) KekVaultResourceId | url | ciąg |
| (opcjonalnie) SequenceVersion | uniqueidentifier | ciąg |
| Typ woluminu | System operacyjny, dane, wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Przykład wdrożenia szablonu opartego na schemacie w wersji 1.1 można znaleźć w szablonie Szybkiego startu platformy Azure [201-encrypt-running-linux-vm-without-aad.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

Przykład wdrożenia szablonu opartego na schemacie w wersji 0.1 można znaleźć w szablonie Szybkiego startu platformy Azure [201-encrypt-running-linux-vm.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

>[!WARNING]
> - Jeśli wcześniej zaszyfrowano maszynę wirtualną Azure Disk Encryption z usługą Azure AD, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej.
> - Podczas szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uznawana za niedostępną. Zdecydowanie zalecamy unikanie logowania za pomocą SSH w trakcie szyfrowania, aby uniknąć problemów z blokowaniem otwartych plików, do których będzie trzeba uzyskać dostęp podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia cmdlet [get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) programu PowerShell lub polecenia wiersza polecenia [pokaż szyfrowanie maszyny wirtualnej.](/cli/azure/vm/encryption#az_vm_encryption_show) Ten proces może potrwać kilka godzin w przypadku woluminu systemu operacyjnego o pojemności 30 GB oraz dodatkowy czas szyfrowania woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że jest używana cała opcja formatu szyfrowania. 
> - Wyłączanie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko w przypadku woluminów danych. Nie jest obsługiwana na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

>[!NOTE]
> Ponadto jeśli parametr ma wartość Wszystkie, dyski danych będą szyfrowane tylko `VolumeType` wtedy, gdy są poprawnie zainstalowane.

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problemy, zapoznaj się z [Azure Disk Encryption rozwiązywania problemów.](../linux/disk-encryption-troubleshooting.md)

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na platformie Azure w [witrynie MSDN Stack Overflow forach.](https://azure.microsoft.com/support/community/) 

Alternatywnie możesz złożyć zdarzenie pomocy technicznej platformy Azure. Przejdź do pomocy [technicznej platformy Azure i](https://azure.microsoft.com/support/options/) wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomoc techniczna platformy Azure, przeczytaj [często zadawane pytania dotyczące Microsoft Azure pomocy technicznej.](https://azure.microsoft.com/support/faq/)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [Virtual machine extensions and features for Linux (Rozszerzenia i](features-linux.md)funkcje maszyny wirtualnej dla systemu Linux).
* Aby uzyskać więcej informacji na temat Azure Disk Encryption dla systemu Linux, zobacz [Maszyny wirtualne z systemem Linux.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)