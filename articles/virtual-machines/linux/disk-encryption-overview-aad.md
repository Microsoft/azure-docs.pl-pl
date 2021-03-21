---
title: Azure Disk Encryption z wymaganiami wstępnymi aplikacji usługi Azure AD (poprzednia wersja)
description: Ten artykuł zawiera dodatkowe informacje dotyczące Azure Disk Encryption dla maszyn wirtualnych z systemem Linux z dodatkowymi wymaganiami i wymaganiami wstępnymi dotyczącymi Azure Disk Encryption z usługą Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558342"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption z Azure Active Directory (AD) (poprzednia wersja)

Nowa wersja Azure Disk Encryption eliminuje konieczność podawania parametru aplikacji Azure Active Directory (Azure AD) w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby uzyskać instrukcje dotyczące włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.

Ten artykuł zawiera dodatkowe informacje dotyczące [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md) z dodatkowymi wymaganiami i wymaganiami wstępnymi dotyczącymi Azure Disk Encryption z usługą Azure AD (poprzednie wydanie).

Informacje w tych sekcjach pozostają takie same:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Sieci i zasady grupy

Aby włączyć funkcję Azure Disk Encryption przy użyciu starszej składni parametrów usługi AAD, maszyny wirtualne "infrastruktura jako usługa" (IaaS) muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci: 
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure AD, \[ login.microsoftonline.com \] .
  - Aby zapisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).
  - W systemie Windows, jeśli protokół TLS 1,0 został jawnie wyłączony i wersja platformy .NET nie została zaktualizowana do 4,6 lub nowszej, następująca zmiana w rejestrze umożliwia Azure Disk Encryption wybranie nowszej wersji protokołu TLS:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Zasady grupy
 - Rozwiązanie Azure Disk Encryption używa funkcji ochrony klucza zewnętrznego funkcji BitLocker dla maszyn wirtualnych z systemem Windows IaaS. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychanie żadnych zasad grupy, które wymuszają funkcje ochrony modułu TPM. Aby uzyskać informacje na temat zasady grupy opcji **Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM**, zobacz [BitLocker zasady grupy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowym zasady grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika dla informacji odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption nie powiedzie się, gdy niestandardowe ustawienia zasady grupy funkcji BitLocker są niezgodne. Na maszynach, które nie mają poprawnego ustawienia zasad, Zastosuj nowe zasady, Wymuś aktualizację nowych zasad (gpupdate.exe/Force), a następnie uruchom ponownie, jeśli jest to wymagane. 

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania 

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednie wydanie)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Następne kroki

- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
- [Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Linux (poprzednia wersja)](disk-encryption-linux-aad.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
