---
title: Włączanie szyfrowania dysków platformy Azure dla maszyn wirtualnych z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: b2fe7e4fbe4830567533053580880315ceface5d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458883"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows 

Usługa Azure Disk Encryption pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Używa funkcji [funkcji bitlocker](https://en.wikipedia.org/wiki/BitLocker) systemu Windows, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure (VM) i jest zintegrowany z [usługą Azure Key Vault,](../../key-vault/index.yml) aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi oraz zarządzanie nimi. 

Jeśli używasz [usługi Azure Security Center,](../../security-center/index.yml)zostaniesz powiadomiony, jeśli masz maszyny wirtualne, które nie są szyfrowane. Alerty są wyświetlane jako wysoka ważność, a zalecenie jest szyfrowanie tych maszyn wirtualnych.

![Alert szyfrowania dysku usługi Azure Security Center](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Szczegółowe informacje można znaleźć [w witrynie Azure Disk Encryption with Azure AD (previous release).](disk-encryption-overview-aad.md) 
> - Niektóre zalecenia mogą zwiększać użycie danych, sieci lub zasobów obliczeniowych, co spowoduje dodatkowe koszty licencji lub subskrypcji. Aby utworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.

Podstawy szyfrowania dysków platformy Azure dla systemu Windows można poznać w ciągu zaledwie kilku minut za pomocą [przewodnika Utwórz i zaszyfruj maszynę wirtualną systemu Windows za pomocą przewodnika Szybki start platformy Azure cli](disk-encryption-cli-quickstart.md) lub narzędzia [Utwórz i syfruj maszynę wirtualną systemu Windows za pomocą przewodnika Szybki start programu Azure Powershell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Obsługiwane maszyny wirtualne i systemy operacyjne

### <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Maszyny wirtualne z systemem Windows są dostępne w [różnych rozmiarach](sizes-general.md). Szyfrowanie dysków platformy Azure nie jest dostępne na [maszynach wirtualnych podstawowych i maszynach wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/series/)z pamięcią mniejszą niż 2 GB.

Szyfrowanie dysków platformy Azure jest również dostępne dla maszyn wirtualnych z magazynu w stanie Premium.

Szyfrowanie dysków platformy Azure nie jest dostępne na [maszynach wirtualnych generacji 2)](generation-2.md#generation-1-vs-generation-2-capabilities)i [maszynach wirtualnych z serii Lsv2](../lsv2-series.md)). Aby uzyskać więcej wyjątków, zobacz [Szyfrowanie dysków platformy Azure: Nieobsługiwowane scenariusze](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Klient systemu Windows: Windows 8 lub nowsze.
- Windows Server: Windows Server 2008 R2 i nowsze.  
 
> [!NOTE]
> System Windows Server 2008 R2 wymaga zainstalowania programu .NET Framework 4.5 w celu szyfrowania; zainstaluj go z witryny Windows Update z opcjonalną aktualizacją Microsoft .NET Framework 4.5.2 dla systemów opartych na systemie Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Systemy Windows Server 2012 R2 Core i Windows Server 2016 Core wymagają zainstalowania składnika bdehdcfg na maszynie wirtualnej w celu szyfrowania.


## <a name="networking-requirements"></a>Wymagania dotyczące sieci
Aby włączyć szyfrowanie dysków platformy Azure, maszyny wirtualne muszą spełniać następujące wymagania konfiguracji sieciowego punktu końcowego:
  - Aby uzyskać token do łączenia się z magazynem kluczy, maszyna wirtualna systemu \[Windows musi\]mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Active Directory login.microsoftonline.com .
  - Aby zapisać klucze szyfrowania w magazynie kluczy, maszyna wirtualna systemu Windows musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna systemu Windows musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu platformy Azure, który obsługuje repozytorium rozszerzenia platformy Azure i kontem magazynu platformy Azure, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozpoznać poprzedni identyfikator URI i skonfigurować określoną regułę, aby umożliwić łączność wychodzącą z adresami IP. Aby uzyskać więcej informacji, zobacz [Usługa Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Wymagania dotyczące zasad grupy

Szyfrowanie dysków platformy Azure używa funkcji Ochrony kluczy zewnętrznych funkcji BitLocker dla maszyn wirtualnych z systemem Windows. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychaj żadnych zasad grupy wymuszanych przez ochronę modułu TPM. Aby uzyskać informacje na temat zasad grupy dla opcji "Zezwalaj na funkcje BitLocker bez zgodnego modułu TPM", zobacz [Odwołanie do zasad grupy funkcji BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: Konfigurowanie przechowywania danych odzyskiwania funkcji [BitLocker przez użytkownika — > Zezwalaj na 256-bitowy klucz odzyskiwania](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Szyfrowanie dysków platformy Azure zakończy się niepowodzeniem, gdy niestandardowe ustawienia zasad grupy dla funkcji BitLocker są niezgodne. Na komputerach, które nie miały prawidłowego ustawienia zasad, zastosuj nowe zasady, wymuś aktualizację nowej zasady (gpupdate.exe /force), a następnie może być wymagane ponowne uruchomienie.

Szyfrowanie dysków platformy Azure zakończy się niepowodzeniem, jeśli zasady grupy na poziomie domeny zablokują algorytm AES-CBC, który jest używany przez funkcję BitLocker.

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące przechowywania kluczy szyfrowania  

Szyfrowanie dysków platformy Azure wymaga usługi Azure Key Vault do kontrolowania kluczy szyfrowania dysku i zarządzania nimi oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie platformy Azure i subskrypcji.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
W poniższej tabeli zdefiniowano niektóre typowe terminy używane w dokumentacji szyfrowania dysków platformy Azure:

| Terminologia | Definicja |
| --- | --- |
| W usłudze Azure Key Vault | Key Vault to kryptograficzna usługa zarządzania kluczami oparta na sprawdzonych przez federalne moduły zabezpieczeń fips (Federal Information Processing Standards). Te standardy pomagają chronić klucze kryptograficzne i poufne wpisy tajne. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oraz tworzenie i [konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md). |
| Interfejs wiersza polecenia platformy Azure | [Narzędzie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowane pod kątem zarządzania zasobami platformy Azure i administrowania nimi z wiersza polecenia.|
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) to uznana w branży technologia szyfrowania woluminów systemu Windows, która służy do włączania szyfrowania dysków na maszynach wirtualnych systemu Windows. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Można podać klucz chroniony sprzętowym modułem zabezpieczeń (HSM) lub klucz chroniony programowo. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oraz tworzenie i [konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md). |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [polecenia cmdlet programu Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Następne kroki

- [Szybki start — tworzenie i szyfrowanie maszyny Wirtualnej systemu Windows za pomocą interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Szybki start — tworzenie i szyfrowanie maszyny Wirtualnej systemu Windows za pomocą programu Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows](disk-encryption-windows.md)
- [Wymagane szyfrowanie dysków platformy Azure skrypt interfejsu wiersza polecenia](https://github.com/ejarvi/ade-cli-getting-started)
- [Wymagania wstępne szyfrowania dysków platformy Azure w programie PowerShell](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](disk-encryption-key-vault.md)


