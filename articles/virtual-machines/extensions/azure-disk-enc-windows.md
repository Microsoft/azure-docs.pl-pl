---
title: Azure Disk Encryption dla systemu Windows
description: Wdraża Azure Disk Encryption na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 361751107212d556aaee6886902d2798c014ff39
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180187"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption dla systemu Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Przegląd

Azure Disk Encryption wykorzystuje funkcję BitLocker, aby zapewnić pełne szyfrowanie dysków na maszynach wirtualnych platformy Azure z systemem Windows.  To rozwiązanie jest zintegrowane z usługą Azure Key Vault w celu zarządzania kluczami i wpisami tajnymi dysków w ramach subskrypcji magazynu kluczy. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../windows/disk-encryption-overview.md), w tym w następujących sekcjach:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](../windows/disk-encryption-overview.md#networking-requirements)
- [Wymagania zasady grupy](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schemat rozszerzenia

Istnieją dwie wersje schematu rozszerzeń dla Azure Disk Encryption (ADE):
- v 2.2 — nowszy zalecany schemat, który nie korzysta z właściwości Azure Active Directory (AAD).
- v 1.1 — starszy schemat, który wymaga właściwości Azure Active Directory (AAD). 

Aby wybrać schemat docelowy, `typeHandlerVersion` należy ustawić właściwość równą wersji schematu, którego chcesz użyć.

### <a name="schema-v22-no-aad-recommended"></a>Schemat v 2.2: Brak usługi AAD (zalecane)

Schemat v 2.2 jest zalecany dla wszystkich nowych maszyn wirtualnych i nie wymaga Azure Active Directory właściwości.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schemat v 1.1: z usługą AAD 

Schemat 1,1 wymaga `aadClientID` , a `aadClientSecret` lub `AADClientCertificate` i nie jest zalecany dla nowych maszyn wirtualnych.

Przy użyciu `aadClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Przy użyciu `AADClientCertificate` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft. Azure. Security | ciąg |
| typ | AzureDiskEncryption | ciąg |
| typeHandlerVersion | 2,2, 1,1 | ciąg |
| (schemat 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schemat 1,1) AADClientSecret | hasło | ciąg |
| (schemat 1,1) AADClientCertificate | odcisk palca | ciąg |
| EncryptionOperation | EnableEncryption | ciąg | 
| (opcjonalne-domyślne RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | ciąg |
| KeyVaultURL | url | ciąg |
| KeyVaultResourceId | url | ciąg |
| obowiązkowe KeyEncryptionKeyURL | url | ciąg |
| obowiązkowe KekVaultResourceId | url | ciąg |
| obowiązkowe SequenceVersion | uniqueidentifier | ciąg |
| Liczba woluminów | System operacyjny, dane, wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Przykład wdrażania szablonów opartych na schemacie v 2.2 można znaleźć w temacie szablon szybkiego startu platformy Azure [201 — szyfrowanie-uruchomione-Windows-VM-bez-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Aby zapoznać się z przykładem wdrożenia szablonu opartego na schemacie v 1.1, zobacz szablon szybkiego startu platformy Azure [201 — szyfrowanie-uruchomione-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Jeśli `VolumeType` parametr jest ustawiony na wartość All, dyski danych będą szyfrowane tylko wtedy, gdy są poprawnie sformatowane. 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Rozwiązywanie problemów można znaleźć w [przewodniku rozwiązywania problemów Azure Disk Encryption](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). 

Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](features-windows.md).
* Aby uzyskać więcej informacji na temat Azure Disk Encryption dla systemu Windows, zobacz [maszyny wirtualne z systemem Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
