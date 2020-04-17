---
title: Szyfrowanie dysków platformy Azure w sieci izolowanej
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z szyfrowaniem dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460124"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Szyfrowanie dysków platformy Azure w sieci izolowanej

Gdy łączność jest ograniczona przez zaporę, wymagania serwera proxy lub ustawienia sieciowej grupy zabezpieczeń (NSG), możliwość wykonywania potrzebnych zadań przez rozszerzenie może zostać zakłócona. To zakłócenie może spowodować komunikaty o stanie, takie jak "Stan rozszerzenia nie jest dostępny na maszynie wirtualnej."

## <a name="package-management"></a>Zarządzanie pakietami

Szyfrowanie dysków platformy Azure zależy od wielu składników, które są zazwyczaj instalowane jako część włączenia usługi ADE, jeśli nie są już obecne. Gdy za zaporą lub w inny sposób odizolowane od Internetu, te pakiety muszą być wstępnie zainstalowane lub dostępne lokalnie.

Oto pakiety niezbędne dla każdej dystrybucji. Aby uzyskać pełną listę obsługiwanych dystrybucji i typów woluminów, zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

W programie Red Hat, gdy wymagany jest serwer proxy, należy upewnić się, że menedżer subskrypcji i mniam są poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [Jak rozwiązać problemy z menedżerem subskrypcji i mniam](https://access.redhat.com/solutions/189533).  

Gdy pakiety są instalowane ręcznie, muszą być również uaktualniane ręcznie w miarę zwalniania nowych wersji.

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie zastosowane ustawienia sieciowej grupy zabezpieczeń muszą nadal zezwalać punktowi końcowemu na spełnienie wymagań wstępnych dotyczących konfiguracji sieci udokumentowanych dla szyfrowania dysku.  Zobacz [Szyfrowanie dysków platformy Azure: Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)

W przypadku korzystania [z usługi Azure Disk Encryption z usługą Azure AD (poprzednia wersja)](disk-encryption-overview-aad.md) [biblioteka usługi Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) musi zostać zainstalowana ręcznie dla wszystkich dystrybucji (oprócz pakietów odpowiednich dla dystrybucji, jak [wymieniono powyżej).](#package-management)

Gdy szyfrowanie jest włączone przy użyciu [poświadczeń usługi Azure AD,](disk-encryption-linux-aad.md)docelowa maszyna wirtualna musi zezwalać na łączność z punktami końcowymi usługi Azure Active Directory i punktami końcowymi usługi Key Vault. Bieżące punkty końcowe uwierzytelniania usługi Azure Active Directory są obsługiwane w sekcjach 56 i 59 z dokumentacji [adresów URL i zakresów adresów IP usługi Office 365.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Instrukcje usługi Key Vault znajdują się w dokumentacji dotyczącej sposobu uzyskiwania dostępu do [usługi Azure Key Vault za zaporą.](../../key-vault/general/access-behind-firewall.md)

### <a name="azure-instance-metadata-service"></a>Usługa metadanych wystąpienia platformy Azure 

Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure,](instance-metadata-service.md) `169.254.169.254`który używa dobrze znanego nie rutowalny adres IP ( ), który można uzyskać tylko z poziomu maszyny Wirtualnej.  Konfiguracje serwera proxy, które zmieniają lokalny ruch HTTP na ten adres (na przykład dodawanie nagłówka X-Forwarded-For) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej kroków rozwiązywania [problemów z szyfrowaniem dysków platformy Azure](disk-encryption-troubleshooting.md)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
