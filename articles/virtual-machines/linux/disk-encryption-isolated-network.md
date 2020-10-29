---
title: Azure Disk Encryption w sieci izolowanej
description: W tym artykule poznasz wskazówki dotyczące rozwiązywania problemów z Microsoft Azure szyfrowaniem dysków na maszynach wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1d7e019e7759e22e945bddee477a4cb77f17350b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913827"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption w sieci izolowanej

Gdy łączność jest ograniczona przez ustawienia zapory, wymagania serwera proxy lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), zdolność rozszerzenia do wykonywania wymaganych zadań może być zakłócona. Zakłócenia te mogą spowodować komunikaty o stanie, takie jak "stan rozszerzenia nie jest dostępny na maszynie wirtualnej".

## <a name="package-management"></a>Zarządzanie pakietami

Azure Disk Encryption zależy od wielu składników, które zazwyczaj są instalowane w ramach włączania ADE, jeśli jeszcze nie istnieją. Te pakiety muszą być wstępnie zainstalowane lub dostępne lokalnie, gdy jest za zaporą lub w inny sposób odizolowany od Internetu.

Poniżej przedstawiono pakiety niezbędne do każdej dystrybucji. Aby zapoznać się z pełną listą obsługiwanych typów dystrybucje i woluminów, zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04** : lsscsi, psmisc, at, cryptsetup-bin, Python-część, Python-sześć, procps, grub-PC-bin
- **CentOS 7,2-7,7** : lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-reszyfrowania, pyparted, procps-ng, util-linux
- **CentOS 6,8** : lsscsi, psmisc, lvm2, UUID, at, cryptsetup-reszyfrowania, pyparted, Python — sześć
- **RedHat 7,2-7,7** : lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-reszyfrowania, procps-ng, util-linux
- **RedHat 6,8** : lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup-reszyfruj
- **openSUSE 42,3, SLES 12-SP4, 12-SP3** : lsscsi, cryptsetup

W systemie Red Hat, gdy wymagany jest serwer proxy, należy się upewnić, że usługi Subscription-Manager i yum są poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [temat Rozwiązywanie problemów z usługą Subscription-Manager i yum](https://access.redhat.com/solutions/189533).  

Gdy pakiety są instalowane ręcznie, muszą być również ręcznie uaktualnione po wydaniu nowych wersji.

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie ustawienia sieciowej grupy zabezpieczeń, które są stosowane, muszą nadal zezwalać, aby punkt końcowy spełniał udokumentowane wymagania wstępne konfiguracji sieci na potrzeby szyfrowania dysku.  Zobacz [Azure Disk Encryption: wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption z usługą Azure AD (poprzednia wersja)

W przypadku korzystania [z Azure Disk Encryption z usługą Azure AD (poprzednia wersja)](disk-encryption-overview-aad.md) [Biblioteka Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) będzie musiała zostać zainstalowana ręcznie dla wszystkich dystrybucje (oprócz pakietów odpowiednich dla dystrybucji, jak [wymieniono powyżej](#package-management)).

Gdy szyfrowanie jest włączane przy użyciu [poświadczeń usługi Azure AD](disk-encryption-linux-aad.md), docelowa maszyna wirtualna musi zezwalać na połączenie z punktami końcowymi Azure Active Directory i Key Vault punktów końcowych. Bieżące punkty końcowe uwierzytelniania Azure Active Directory są utrzymywane w sekcjach 56 i 59 [Microsoft 365 adresów URL i zakresów adresów IP](/microsoft-365/enterprise/urls-and-ip-address-ranges) . Instrukcje Key Vault są dostępne w dokumentacji dotyczącej sposobu [uzyskiwania dostępu Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure](instance-metadata-service.md) , w którym jest używany dobrze znany adres IP bez obsługi routingu ( `169.254.169.254` ), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.  Konfiguracje serwera proxy, które modyfikują lokalny ruch HTTP na ten adres (na przykład dodanie nagłówka X-forwardd-for) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej kroków [rozwiązywania problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
- [Szyfrowanie danych magazynowanych platformy Azure](../../security/fundamentals/encryption-atrest.md)
