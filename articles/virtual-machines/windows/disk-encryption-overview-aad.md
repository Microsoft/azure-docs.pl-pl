---
title: Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd668fcac3751715fbe91c9aeff98583c9d03d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458917"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)

**Nowa wersja szyfrowania dysków platformy Azure eliminuje konieczność dostarczania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysku maszyny Wirtualnej. W nowej wersji nie są już wymagane do podawania poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysku maszyn wirtualnych przy użyciu nowej wersji, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane ze składnią usługi AAD.**

W tym artykule [uzupełnia szyfrowanie dysków platformy Azure dla maszyn wirtualnych](disk-encryption-overview.md) systemu Windows o dodatkowe wymagania i wymagania wstępne dotyczące szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja). Sekcja [Obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems) pozostają takie same.

## <a name="networking-and-group-policy"></a>Zasady sieci i grupy

**Aby włączyć funkcję szyfrowania dysku platformy Azure przy użyciu starszej składni parametrów AAD, maszyny wirtualne IaaS muszą spełniać następujące wymagania konfiguracji sieciowego punktu końcowego:** 
  - Aby uzyskać token do łączenia się z magazynem kluczy, maszyna wirtualna IaaS \[musi mieć\]możliwość nawiązania połączenia z punktem końcowym usługi Azure Active Directory, login.microsoftonline.com .
  - Aby zapisać klucze szyfrowania w magazynie kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu platformy Azure, który obsługuje repozytorium rozszerzenia platformy Azure i konto magazynu platformy Azure, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozpoznać poprzedni identyfikator URI i skonfigurować określoną regułę, aby umożliwić łączność wychodzącą z adresami IP. Aby uzyskać więcej informacji, zobacz [Usługa Azure Key Vault za zaporą](../../key-vault/key-vault-access-behind-firewall.md).
  - Maszyna wirtualna do zaszyfrowania musi być skonfigurowana do używania protokołu TLS 1.2 jako protokołu domyślnego. Jeśli TLS 1.0 został jawnie wyłączony, a wersja .NET nie została zaktualizowana do wersji 4.6 lub nowszej, następująca zmiana rejestru umożliwi ADE wybranie nowszej wersji protokołu TLS:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Zasady grupy:**
 - Rozwiązanie szyfrowania dysków platformy Azure używa funkcji Ochrony kluczy zewnętrznych funkcji BitLocker dla maszyn wirtualnych IaaS systemu Windows. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychaj żadnych zasad grupy wymuszanych przez ochronę modułu TPM. Aby uzyskać informacje na temat zasad grupy dla opcji "Zezwalaj na funkcje BitLocker bez zgodnego modułu TPM", zobacz [Odwołanie do zasad grupy funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: Konfigurowanie przechowywania danych odzyskiwania funkcji [BitLocker przez użytkownika — > Zezwalaj na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Szyfrowanie dysków platformy Azure zakończy się niepowodzeniem, gdy niestandardowe ustawienia zasad grupy dla funkcji BitLocker są niezgodne. Na komputerach, które nie miały prawidłowego ustawienia zasad, zastosuj nowe zasady, wymuś aktualizację nowej zasady (gpupdate.exe /force), a następnie może być wymagane ponowne uruchomienie.  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące przechowywania kluczy szyfrowania  

Szyfrowanie dysków platformy Azure wymaga usługi Azure Key Vault do kontrolowania kluczy szyfrowania dysku i zarządzania nimi oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie platformy Azure i subskrypcji.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja).](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>Następne kroki

- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
- [Włącz szyfrowanie dysków platformy Azure za pomocą usługi Azure AD na maszynach wirtualnych z systemem Windows (poprzednia wersja)](disk-encryption-windows-aad.md)
- [Wymagane szyfrowanie dysków platformy Azure skrypt interfejsu wiersza polecenia](https://github.com/ejarvi/ade-cli-getting-started)
- [Wymagania wstępne szyfrowania dysków platformy Azure w programie PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
