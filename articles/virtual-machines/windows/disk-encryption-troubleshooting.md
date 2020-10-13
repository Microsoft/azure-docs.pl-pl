---
title: Przewodnik rozwiązywania problemów Azure Disk Encryption
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z Microsoft Azure szyfrowaniem dysków dla maszyn wirtualnych z systemem Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 45c64261b60d88557e63cb8f9af922fd7aa71ad4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977905"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów Azure Disk Encryption

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji i administratorów chmury, których organizacje używają Azure Disk Encryption. Ten artykuł ma na celu pomoc w rozwiązywaniu problemów związanych z szyfrowaniem dysków.

Przed wykonaniem poniższych czynności należy najpierw upewnić się, że maszyny wirtualne, które mają zostać zaszyfrowane, znajdują się wśród [obsługiwanych rozmiarów maszyn wirtualnych i w systemach operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems)oraz że zostały spełnione wszystkie wymagania wstępne:

- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące zasad grupy](disk-encryption-overview.md#group-policy-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów Azure Disk Encryption za zaporą

Gdy łączność jest ograniczona przez ustawienia zapory, wymagania serwera proxy lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), zdolność rozszerzenia do wykonywania wymaganych zadań może być zakłócona. Zakłócenia te mogą spowodować komunikaty o stanie, takie jak "stan rozszerzenia nie jest dostępny na maszynie wirtualnej". W oczekiwanych scenariuszach szyfrowanie nie powiedzie się. Poniższe sekcje zawierają kilka typowych problemów z zaporą, które mogą być zbadane.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie ustawienia sieciowej grupy zabezpieczeń, które są stosowane, muszą nadal zezwalać, aby punkt końcowy spełniał udokumentowane [wymagania wstępne](disk-encryption-overview.md#networking-requirements) konfiguracji sieci na potrzeby szyfrowania dysku.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault za zaporą

Gdy szyfrowanie jest włączane przy użyciu [poświadczeń usługi Azure AD](disk-encryption-windows-aad.md#), docelowa maszyna wirtualna musi zezwalać na połączenie z punktami końcowymi Azure Active Directory i Key Vault punktów końcowych. Bieżące punkty końcowe uwierzytelniania Azure Active Directory są utrzymywane w sekcjach 56 i 59 [Microsoft 365 adresów URL i zakresów adresów IP](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Instrukcje Key Vault są dostępne w dokumentacji dotyczącej sposobu [uzyskiwania dostępu Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure](../windows/instance-metadata-service.md) , w którym jest używany dobrze znany adres IP bez obsługi routingu ( `169.254.169.254` ), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.  Konfiguracje serwera proxy, które modyfikują lokalny ruch HTTP na ten adres (na przykład dodanie nagłówka X-forwardd-for) nie są obsługiwane.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Rozwiązywanie problemów z systemem Windows Server 2016 Server Core

W systemie Windows Server 2016 Server Core składnik BdeHdCfg nie jest dostępny domyślnie. Ten składnik jest wymagany przez Azure Disk Encryption. Służy do dzielenia woluminu systemowego z woluminu systemu operacyjnego, który jest wykonywany tylko raz dla czasu życia maszyny wirtualnej. Te pliki binarne nie są wymagane podczas późniejszych operacji szyfrowania.

Aby obejść ten problem, Skopiuj następujące cztery pliki z maszyny wirtualnej centrum danych systemu Windows Server 2016 do tej samej lokalizacji na serwerze Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Wprowadź następujące polecenie:

   ```
   bdehdcfg.exe -target default
   ```

1. To polecenie tworzy partycję systemową 550 MB. Uruchom ponownie system.

1. Użyj narzędzia DiskPart, aby sprawdzić woluminy, a następnie wybierz pozycję wykonaj.  

Na przykład:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

W portalu może być wyświetlany dysk zaszyfrowany nawet po zaszyfrowaniu go w ramach maszyny wirtualnej.  Taka sytuacja może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego wyszyfrowania dysku z poziomu maszyny wirtualnej, a nie przy użyciu Azure Disk Encryption polecenia zarządzania wyższego poziomu.  Polecenia wyższego poziomu nie tylko deszyfrują dysk z maszyny wirtualnej, ale poza maszyną wirtualną również aktualizują ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one zachowane, platforma nie będzie mogła zgłosić stanu szyfrowania ani zainicjować obsługi maszyny wirtualnej.

Aby wyłączyć Azure Disk Encryption za pomocą programu PowerShell, użyj polecenia [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) , a następnie polecenie [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension zanim szyfrowanie zostanie wyłączone, zakończy się niepowodzeniem.

Aby wyłączyć Azure Disk Encryption przy użyciu interfejsu wiersza polecenia, użyj polecenie [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## 



## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto więcej informacji o niektórych typowych problemach w Azure Disk Encryption i sposobach rozwiązywania tych problemów. Aby uzyskać więcej informacji o tej usłudze i jej możliwościach, zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w Azure Security Center](../../security-center/asset-inventory.md)
- [Szyfrowanie danych magazynowanych platformy Azure](../../security/fundamentals/encryption-atrest.md)