---
title: Przewodnik rozwiązywania problemów z szyfrowaniem dysków platformy Azure
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z szyfrowaniem dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 11c1e0bf10725173a2a341addf4c3f845bbb7fba
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085692"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z szyfrowaniem dysków platformy Azure

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji i administratorów chmury, których organizacje używają szyfrowania dysków platformy Azure. Ten artykuł ułatwia rozwiązywanie problemów związanych z szyfrowaniem dysku.

Przed podjęciem któregokolwiek z poniższych kroków, najpierw upewnij się, że maszyny wirtualne, które próbujesz zaszyfrować, należą do [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems)i że spełnione są wszystkie wymagania wstępne:

- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące zasad grupy](disk-encryption-overview.md#group-policy-requirements)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Rozwiązywanie problemów z szyfrowaniem dysku platformy Azure za zaporą

Gdy łączność jest ograniczona przez zaporę, wymagania serwera proxy lub ustawienia sieciowej grupy zabezpieczeń (NSG), możliwość wykonywania potrzebnych zadań przez rozszerzenie może zostać zakłócona. To zakłócenie może spowodować komunikaty o stanie, takie jak "Stan rozszerzenia nie jest dostępny na maszynie wirtualnej." W oczekiwanych scenariuszach szyfrowanie nie powiedzie się. Sekcje, które należy wykonać mają pewne typowe problemy z zaporą, które można zbadać.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie zastosowane ustawienia sieciowej grupy zabezpieczeń muszą nadal zezwalać punktowi końcowemu na spełnienie wymagań wstępnych dotyczących konfiguracji sieci [udokumentowanych dla](disk-encryption-overview.md#networking-requirements) szyfrowania dysku.

### <a name="azure-key-vault-behind-a-firewall"></a>Usługa Azure Key Vault za zaporą

Gdy szyfrowanie jest włączone przy użyciu [poświadczeń usługi Azure AD,](disk-encryption-windows-aad.md#)docelowa maszyna wirtualna musi zezwalać na łączność z punktami końcowymi usługi Azure Active Directory i punktami końcowymi usługi Key Vault. Bieżące punkty końcowe uwierzytelniania usługi Azure Active Directory są obsługiwane w sekcjach 56 i 59 z dokumentacji [adresów URL i zakresów adresów IP usługi Office 365.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Instrukcje usługi Key Vault znajdują się w dokumentacji dotyczącej sposobu uzyskiwania dostępu do [usługi Azure Key Vault za zaporą.](../../key-vault/general/access-behind-firewall.md)

### <a name="azure-instance-metadata-service"></a>Usługa metadanych wystąpienia platformy Azure 
Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure,](../windows/instance-metadata-service.md) który używa dobrze znanego nie rutowalnej adresu IP (`169.254.169.254`), który jest dostępny tylko z poziomu maszyny Wirtualnej.  Konfiguracje serwera proxy, które zmieniają lokalny ruch HTTP na ten adres (na przykład dodawanie nagłówka X-Forwarded-For) nie są obsługiwane.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Rozwiązywanie problemów z rdzeniem serwera systemu Windows Server 2016

W systemie Windows Server 2016 Server Core składnik bdehdcfg nie jest domyślnie dostępny. Ten składnik jest wymagany przez szyfrowanie dysków platformy Azure. Służy do dzielenia woluminu systemowego z woluminu systemu operacyjnego, który odbywa się tylko raz przez cały okres eksploatacji maszyny Wirtualnej. Te pliki binarne nie są wymagane podczas późniejszych operacji szyfrowania.

Aby obejść ten problem, skopiuj następujące cztery pliki z maszyny Wirtualnej Centrum danych systemu Windows Server 2016 do tej samej lokalizacji na serwerze Core:

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

1. To polecenie tworzy partycję systemową o rozmiarze 550 MB. Uruchom ponownie system.

1. Użyj programu DiskPart, aby sprawdzić woluminy, a następnie kontynuować.  

Przykład:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Rozwiązywanie problemów ze stanem szyfrowania 

Portal może wyświetlać dysk jako zaszyfrowany nawet po jego niezaszyfrowaniu w maszynie wirtualnej.  Taka możliwość może wystąpić, gdy polecenia niskiego poziomu są używane do bezpośredniego odszyfrowywania dysku z poziomu maszyny Wirtualnej, zamiast używać poleceń zarządzania szyfrowaniem usługi Azure Disk wyższego poziomu.  Polecenia wyższego poziomu nie tylko odszyfrowywać dysk z poziomu maszyny Wirtualnej, ale poza maszyną wirtualną aktualizują również ważne ustawienia szyfrowania na poziomie platformy i ustawienia rozszerzenia skojarzone z maszyną wirtualną.  Jeśli nie są one przechowywane w wyrównaniu, platforma nie będzie w stanie poprawnie zgłosić stanu szyfrowania lub aprowizować maszyny Wirtualnej.   

Aby wyłączyć szyfrowanie dysków platformy Azure za pomocą programu PowerShell, należy użyć [funkcji Disable-AzVMDiskEncryption,](/powershell/module/az.compute/disable-azvmdiskencryption) a następnie [usunąć-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Uruchomienie Remove-AzVMDiskEncryptionExtension przed wyłączeniem szyfrowania zakończy się niepowodzeniem.

Aby wyłączyć szyfrowanie dysków platformy Azure za pomocą interfejsu wiersza polecenia, użyj [funkcji szyfrowania az vm disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie dowiesz się więcej o niektórych typowych problemach w szyfrowaniu dysków platformy Azure i o tym, jak rozwiązać te problemy. Aby uzyskać więcej informacji na temat tej usługi i jej możliwości, zobacz następujące artykuły:

- [Stosowanie szyfrowania dysku w usłudze Azure Security Center](../../security-center/security-center-apply-disk-encryption.md)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
