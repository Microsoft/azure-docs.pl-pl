---
title: Często zadawane pytania — Azure Disk Encryption dla maszyn wirtualnych z systemem Windows
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6371a6bd0c4b8151c97a892ec70789429b4bda75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562201"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Azure Disk Encryption dla często zadawanych pytań dotyczących maszyn wirtualnych z systemem Windows

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Disk Encryption maszyn wirtualnych z systemem Windows. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure Disk Encryption przegląd](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Co to jest Azure Disk Encryption dla maszyn wirtualnych z systemem Windows?

Azure Disk Encryption dla maszyn wirtualnych z systemem Windows używa funkcji BitLocker systemu Windows w celu zapewnienia pełnego szyfrowania dysków systemu operacyjnego i dysków z danymi. Ponadto zapewnia szyfrowanie dysku tymczasowego, gdy [parametr volumetype ma wartość All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  Zawartość jest zaszyfrowana z maszyny wirtualnej do zaplecza magazynu. W związku z tym zapewnia to kompleksowe szyfrowanie za pomocą klucza zarządzanego przez klienta.
 
Zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest Azure Disk Encryption ogólnie dostępna?

Azure Disk Encryption jest ogólnie dostępna we wszystkich regionach publicznych platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie środowiska użytkownika są dostępne w Azure Disk Encryption?

Azure Disk Encryption GA obsługuje Azure Resource Manager szablonów, Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Różne środowiska użytkownika zapewniają elastyczność. Dostępne są trzy różne opcje włączania szyfrowania dysków dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat środowiska użytkownika i wskazówki krok po kroku dostępne w Azure Disk Encryption, zobacz [Azure Disk Encryption scenariusze dla systemu Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje Azure Disk Encryption?

Nie jest naliczana opłata za szyfrowanie dysków maszyn wirtualnych za pomocą Azure Disk Encryption, ale naliczane są opłaty związane z użyciem Azure Key Vault. Aby uzyskać więcej informacji na temat Azure Key Vault kosztów, zobacz stronę [cennika Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak mogę zacząć korzystać z Azure Disk Encryption?

Aby rozpocząć, zapoznaj się z [omówieniem Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemów operacyjnych Azure Disk Encryption obsługiwać?

W artykule [przegląd Azure Disk Encryption](disk-encryption-overview.md) przedstawiono [rozmiary maszyn wirtualnych](disk-encryption-overview.md#supported-vms) i [systemy operacyjne maszyn wirtualnych](disk-encryption-overview.md#supported-operating-systems) obsługujące Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy mogę zaszyfrować woluminy rozruchowe i dane za pomocą Azure Disk Encryption?

Można zaszyfrować woluminy rozruchowe i dane, ale nie można szyfrować danych bez wcześniejszego szyfrowania woluminu systemu operacyjnego.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować wolumin odinstalowany z Azure Disk Encryption?

Nie, Azure Disk Encryption szyfruje tylko woluminy zainstalowane.

## <a name="what-is-storage-server-side-encryption"></a>Co to jest szyfrowanie po stronie serwera magazynu?

Szyfrowanie po stronie serwera magazynu szyfruje dyski zarządzane platformy Azure w usłudze Azure Storage. Dyski zarządzane są domyślnie szyfrowane za pomocą szyfrowania po stronie serwera z kluczem zarządzanym przez platformę (od 10 czerwca 2017). Można zarządzać szyfrowaniem dysków zarządzanych przy użyciu własnych kluczy, określając klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie serwera dla usługi Azure Managed disks](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Jak różni się Azure Disk Encryption od szyfrowania po stronie serwera magazynu z kluczem zarządzanym przez klienta i kiedy należy używać poszczególnych rozwiązań?

Azure Disk Encryption zapewnia kompleksowe szyfrowanie dysku systemu operacyjnego, dysków danych i dysku tymczasowego z kluczem zarządzanym przez klienta.

- Jeśli wymagania obejmują szyfrowanie wszystkich powyższych i kompleksowych danych szyfrowania, użyj Azure Disk Encryption. 
- Jeśli wymagania obejmują szyfrowanie tylko danych przechowywanych w kluczu zarządzanym przez klienta, należy użyć [szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta](../disk-encryption.md). Nie można zaszyfrować dysku z szyfrowaniem po stronie serwera Azure Disk Encryption i magazynu przy użyciu kluczy zarządzanych przez klienta.
- W przypadku korzystania ze scenariusza o nazwie z [nieobsługiwanymi scenariuszami dla systemu Windows](disk-encryption-windows.md#unsupported-scenarios)należy rozważyć [szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta](../disk-encryption.md). 
- Jeśli zasady organizacji umożliwiają szyfrowanie zawartości przechowywanej przy użyciu klucza zarządzanego przez platformę Azure, nie jest wymagana żadna akcja — zawartość jest domyślnie szyfrowana. W przypadku dysków zarządzanych zawartość wewnątrz magazynu jest domyślnie szyfrowana przy użyciu szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę. Klucz jest zarządzany przez usługę Azure Storage. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak mogę obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, po prostu wywołaj to samo polecenie, które zostało użyte wcześniej do włączenia szyfrowania dysku, określając inny Key Vault. Aby obrócić klucz szyfrowania klucza, wywołaj to samo polecenie, które zostało użyte pierwotnie, aby włączyć szyfrowanie dysków, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](disk-encryption-windows-aad.md) przez określenie poświadczeń usługi Azure AD do zaszyfrowania tej maszyny wirtualnej, należy użyć tej opcji w celu ZASZYFROWANIA maszyny wirtualnej. Nie można użyć Azure Disk Encryption dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Jak mogę dodać lub usunąć klucz szyfrowania klucza, jeśli nie był pierwotnie używany?

Aby dodać klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie, przekazując klucz szyfrowania klucza. Aby usunąć klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie bez parametru klucza szyfrowania klucza.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Czy Azure Disk Encryption umożliwia korzystanie z własnego klucza (BYOK)?

Tak, możesz podać własne klucze szyfrowania kluczy. Te klucze są chronione w Azure Key Vault, który jest magazynem kluczy dla Azure Disk Encryption. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Czy mogę użyć klucza szyfrowania klucza utworzonego przez platformę Azure?

Tak, możesz użyć Azure Key Vault, aby wygenerować klucz szyfrowania klucza do użycia w usłudze Azure Disk Encryption. Te klucze są chronione w Azure Key Vault, który jest magazynem kluczy dla Azure Disk Encryption. Aby uzyskać więcej informacji o kluczu szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Czy można używać lokalnej usługi zarządzania kluczami lub modułu HSM do ochrony kluczy szyfrowania?

Nie można użyć lokalnej usługi zarządzania kluczami lub modułu HSM do zabezpieczenia kluczy szyfrowania za pomocą Azure Disk Encryption. Usługi Azure Key Vault można używać tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne dotyczące konfigurowania Azure Disk Encryption?

Istnieją wymagania wstępne dotyczące Azure Disk Encryption. Zapoznaj się z artykułem [Tworzenie i Konfigurowanie magazynu kluczy na potrzeby Azure Disk Encryption](disk-encryption-key-vault.md) artykułu w celu utworzenia nowego magazynu kluczy lub skonfigurowania istniejącego magazynu kluczy na potrzeby dostępu do szyfrowania dysków w celu włączenia szyfrowania i zabezpieczenia kluczy tajnych oraz klucza. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jakie są wymagania wstępne dotyczące konfigurowania Azure Disk Encryption za pomocą aplikacji usługi Azure AD (poprzednie wydanie)?

Istnieją wymagania wstępne dotyczące Azure Disk Encryption. Aby utworzyć aplikację Azure Active Directory, należy zapoznać się z tematem [Azure Disk Encryption with Azure AD](disk-encryption-windows-aad.md) . w tym celu należy utworzyć nowy magazyn kluczy lub skonfigurować istniejący magazyn kluczy na potrzeby dostępu do szyfrowania dysku w celu włączenia szyfrowania i zabezpieczenia kluczy tajnych. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Czy Azure Disk Encryption korzystanie z aplikacji usługi Azure AD (poprzednia wersja) jest nadal obsługiwane?
Tak. Szyfrowanie dysków za pomocą aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się używanie nowej metody zamiast szyfrowania za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy mogę migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD w celu szyfrowania bez użycia aplikacji usługi Azure AD?
  Obecnie nie ma ścieżki migracji bezpośredniej dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD w celu szyfrowania bez aplikacji usługi Azure AD. Ponadto nie istnieje bezpośrednia ścieżka z szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji usługi AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakiej wersji Azure PowerShell Azure Disk Encryption obsługiwać?

Użyj najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption *nie* jest obsługiwana przez zestaw Azure SDK w wersji 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "klucz szyfrowania bloków Volume" lub "/mnt/azure_bek_disk"?

"Wolumin klucz szyfrowania bloków" to wolumin danych lokalnych, który bezpiecznie przechowuje klucze szyfrowania zaszyfrowanych maszyn wirtualnych platformy Azure.

> [!NOTE]
> Nie usuwaj ani nie edytuj żadnej zawartości na tym dysku. Nie należy odinstalowywać dysku, ponieważ jest wymagana obecność klucza szyfrowania dla każdej operacji szyfrowania na maszynie wirtualnej IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakiej metody szyfrowania Azure Disk Encryption używać?

Azure Disk Encryption wybiera metodę szyfrowania w funkcji BitLocker na podstawie wersji systemu Windows w następujący sposób:

| Wersje systemu Windows                 | Wersja | Metoda szyfrowania        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 lub nowszy  | >= 1511 |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |Bit AES 256 *              |
| 2008R2 systemu Windows Server            |        |Bit AES 256 z rozpraszaniem |

\* Bit AES 256 z rozpraszaniem nie jest obsługiwany w systemie Windows 2012 i nowszych.

Aby określić wersję systemu operacyjnego Windows, uruchom narzędzie "winver" na maszynie wirtualnej.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy można utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanej maszyny wirtualnej w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [wykonywanie kopii zapasowej i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](../../backup/backup-azure-vms-encryption.md).  Przywracanie zaszyfrowanej maszyny wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie mogę zadawać pytania lub przekazać opinię?

Możesz zadawać pytania lub przekazać informacje zwrotne na [stronie pytania&pytań Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono kilka częstych pytań dotyczących Azure Disk Encryption. Aby uzyskać więcej informacji na temat tej usługi, zobacz następujące artykuły:

- [Omówienie usługi Azure Disk Encryption](disk-encryption-overview.md)
- [Zastosuj szyfrowanie dysków w Azure Security Center](../../security-center/asset-inventory.md)
- [Szyfrowanie danych magazynowanych platformy Azure](../../security/fundamentals/encryption-atrest.md)