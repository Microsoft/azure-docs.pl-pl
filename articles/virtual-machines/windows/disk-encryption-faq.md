---
title: Często zadawane pytania — szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych IaaS systemu Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: b71384e0a42af5481af7b17b91cd0b1d0ed82ee8
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082598"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące szyfrowania dysków platformy Azure dla maszyn wirtualnych z systemem Windows. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Co to jest szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Windows?

Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Windows używa funkcji bitlocker systemu Windows, aby zapewnić pełne szyfrowanie dysku dysku operacyjnego i dysków z danymi. Ponadto zapewnia szyfrowanie dysku zasobu efemerycznego, gdy [parametr VolumeType to Wszystko](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  Przepływy zawartości zaszyfrowane z maszyny Wirtualnej do wewnętrznej bazy danych magazynu. W ten sposób zapewnia szyfrowanie end-to-end za pomocą klucza zarządzanego przez klienta.
 
Zobacz [Obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest szyfrowanie dysków azure w ogólnej dostępności (GA)?

Szyfrowanie dysków platformy Azure jest ogólnie dostępne we wszystkich regionach publicznych platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie środowiska użytkownika są dostępne za pomocą szyfrowania dysków platformy Azure?

Usługa Azure Disk Encryption GA obsługuje szablony usługi Azure Resource Manager, usługi Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Różne środowiska użytkownika zapewniają elastyczność. Dostępne są trzy różne opcje włączania szyfrowania dysku dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat środowiska użytkownika i wskazówek krok po kroku dostępnych w programie Szyfrowanie dysków platformy Azure, zobacz [Scenariusze szyfrowania dysków platformy Azure dla systemu Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje szyfrowanie dysków platformy Azure?

Szyfrowanie dysków maszyn wirtualnych za pomocą szyfrowania dysków platformy Azure jest bezpłatne, ale istnieją opłaty związane z korzystaniem z usługi Azure Key Vault. Aby uzyskać więcej informacji na temat kosztów usługi Azure Key Vault, zobacz stronę [cennika usługi Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak rozpocząć korzystanie z szyfrowania dysków platformy Azure?

Aby rozpocząć, przeczytaj [omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemy operacyjne obsługują szyfrowanie dysków platformy Azure?

W [artykule Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md) wymieniono [rozmiary maszyn wirtualnych](disk-encryption-overview.md#supported-vms) i systemy operacyjne [maszyn wirtualnych](disk-encryption-overview.md#supported-operating-systems) obsługujące szyfrowanie dysków platformy Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy mogę szyfrować zarówno woluminy rozruchowe, jak i ilości danych za pomocą szyfrowania dysków platformy Azure?

Można szyfrować zarówno woluminy rozruchowe, jak i dane, ale nie można szyfrować danych bez uprzedniego szyfrowania woluminu systemu operacyjnego.

Po zaszyfrowaniu woluminu systemu operacyjnego wyłączenie szyfrowania na woluminie systemu operacyjnego nie jest obsługiwane.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować niezamontowany wolumin za pomocą szyfrowania dysków platformy Azure?

Nie, szyfrowanie dysków platformy Azure szyfruje tylko zainstalowane woluminy.

## <a name="what-is-storage-server-side-encryption"></a>Co to jest szyfrowanie po stronie serwera magazynu?

Szyfrowanie po stronie serwera magazynu szyfruje dyski zarządzane platformy Azure w usłudze Azure Storage. Dyski zarządzane są domyślnie szyfrowane za pomocą szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę (stan na 10 czerwca 2017 r.). Szyfrowanie dysków zarządzanych za pomocą własnych kluczy można zarządzać, określając klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz [Szyfrowanie po stronie serwera dysków zarządzanych platformy Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Czym różni się szyfrowanie dysków platformy Azure od szyfrowania po stronie magazynu za pomocą klucza zarządzanego przez klienta i kiedy należy używać każdego rozwiązania?

Usługa Azure Disk Encryption zapewnia szyfrowanie end-to-end dla dysku systemu operacyjnego, dysków danych i efemerycznego dysku zasobów z kluczem zarządzanym przez klienta.

- Jeśli twoje wymagania obejmują szyfrowanie wszystkich powyższych i end-to-end szyfrowania, należy użyć szyfrowania dysków platformy Azure. 
- Jeśli wymagania obejmują szyfrowanie tylko danych w pozostałej części za pomocą klucza zarządzanego przez klienta, użyj [szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta](disk-encryption.md). Nie można zaszyfrować dysku za pomocą szyfrowania po stronie serwera azure disk i magazynu za pomocą kluczy zarządzanych przez klienta.
- Jeśli używasz scenariusza wywoływanego w [nieobsługiwana scenariusze dla systemu Windows,](disk-encryption-windows.md#unsupported-scenarios)należy wziąć pod uwagę [szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta](disk-encryption.md). 
- Jeśli zasady organizacji umożliwiają szyfrowanie zawartości w spoczynku za pomocą klucza zarządzanego przez platformę Azure, nie jest wymagana żadna akcja — zawartość jest domyślnie szyfrowana. W przypadku dysków zarządzanych zawartość wewnątrz magazynu jest domyślnie szyfrowana za pomocą szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę. Kluczem jest zarządzana przez usługę Azure Storage. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, wystarczy wywołać to samo polecenie, które pierwotnie użyto do włączenia szyfrowania dysku, określając inny magazyn kluczy. Aby obrócić klucz szyfrowania klucza, należy wywołać to samo polecenie, które było pierwotnie używane do włączania szyfrowania dysku, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej używano [szyfrowania dysków platformy Azure za pomocą aplikacji Usługi Azure AD,](disk-encryption-windows-aad.md) określając poświadczenia usługi Azure AD w celu zaszyfrowania tej maszyny Wirtualnej, musisz nadal używać tej opcji do szyfrowania maszyny wirtualnej. Nie można użyć szyfrowania dysków platformy Azure na tej zaszyfrowanej maszynie wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że odłączenie się od aplikacji AAD dla tej zaszyfrowanej maszyny Wirtualnej nie jest jeszcze obsługiwane.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Jak dodać lub usunąć klucz szyfrowania klucza, jeśli nie użyłem go pierwotnie?

Aby dodać klucz szyfrowania klucza, należy ponownie wywołać polecenie enable, przekazując parametr klucza szyfrowania klucza. Aby usunąć klucz szyfrowania klucza, należy ponownie wywołać polecenie enable bez parametru klucza szyfrowania klucza.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Czy szyfrowanie dysków platformy Azure umożliwia przywiezienie własnego klucza (BYOK)?

Tak, możesz podać własne klucze szyfrowania kluczy. Te klucze są chronione w usłudze Azure Key Vault, która jest magazynem kluczy dla szyfrowania dysków platformy Azure. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Czy mogę użyć klucza szyfrowania klucza utworzonego przez platformę Azure?

Tak, możesz użyć usługi Azure Key Vault do wygenerowania klucza szyfrowania klucza do szyfrowania dysku platformy Azure. Te klucze są chronione w usłudze Azure Key Vault, która jest magazynem kluczy dla szyfrowania dysków platformy Azure. Aby uzyskać więcej informacji na temat klucza szyfrowania klucza, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Czy mogę używać lokalnej usługi zarządzania kluczami lub modułu HSM do ochrony kluczy szyfrowania?

Nie można użyć lokalnej usługi zarządzania kluczami lub modułu HSM do ochrony kluczy szyfrowania za pomocą szyfrowania dysków platformy Azure. Usługi Azure Key Vault można używać tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne do skonfigurowania szyfrowania dysków platformy Azure?

Istnieją wymagania wstępne dotyczące szyfrowania dysków platformy Azure. Zobacz artykuł [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure,](disk-encryption-key-vault.md) aby utworzyć nową przechowalnię kluczy lub skonfigurować istniejącą przechowalnię kluczy w celu umożliwienia szyfrowania dysku oraz ochrony wpisów tajnych i kluczy. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jakie są wymagania wstępne, aby skonfigurować szyfrowanie dysków platformy Azure za pomocą aplikacji usługi Azure AD (poprzednia wersja)?

Istnieją wymagania wstępne dotyczące szyfrowania dysków platformy Azure. Zobacz [zawartość usługi Azure Disk Encryption with Azure AD,](disk-encryption-windows-aad.md) aby utworzyć aplikację usługi Azure Active Directory, utworzyć nową przechowalnię kluczy lub skonfigurować istniejącą przechowalnię kluczy w celu umożliwienia szyfrowania i ochrony wpisów tajnych i kluczy. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Czy szyfrowanie dysków platformy Azure przy użyciu aplikacji usługi Azure AD (poprzednia wersja) jest nadal obsługiwane?
Tak. Szyfrowanie dysku przy użyciu aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się użycie nowej metody, a nie szyfrowanie za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy mogę migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD do szyfrowania bez aplikacji usługi Azure AD?
  Obecnie nie istnieje bezpośrednia ścieżka migracji dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD do szyfrowania bez aplikacji usługi Azure AD. Ponadto nie ma bezpośredniej ścieżki od szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jaka wersja programu Azure PowerShell obsługuje szyfrowanie dysków platformy Azure?

Użyj najnowszej wersji zestawu SDK programu Azure PowerShell, aby skonfigurować szyfrowanie dysków platformy Azure. Pobierz najnowszą wersję programu [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Szyfrowanie dysków platformy Azure *nie* jest obsługiwane przez zestaw SDK platformy Azure w wersji 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "Bek Volume" lub "/mnt/azure_bek_disk"?

"Wolumin Bek" to lokalny wolumin danych, który bezpiecznie przechowuje klucze szyfrowania zaszyfrowanych maszyn wirtualnych platformy Azure.

> [!NOTE]
> Nie należy usuwać ani edytować zawartości tego dysku. Nie należy odinstalowywaj dysku, ponieważ obecność klucza szyfrowania jest wymagana dla operacji szyfrowania na maszynie wirtualnej IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakiej metody szyfrowania używa szyfrowanie dysków Azure?

Szyfrowanie dysków platformy Azure wybiera metodę szyfrowania w funkcji BitLocker na podstawie wersji systemu Windows w następujący sposób:

| Wersje systemu Windows                 | Wersja | Metoda szyfrowania        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 lub więcej  | >=1511 |XTS-AES 256 bitów           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bit *              |
| System Windows Server 2008R2            |        |AES 256 bit z dyfuzorem |

\*AES 256 bit z dyfuzorem nie jest obsługiwany w systemie Windows 2012 i nowszych.

Aby określić wersję systemu operacyjnego Windows, uruchom narzędzie "winver" na maszynie wirtualnej.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Jeśli używam EncryptFormatAll i określi wszystkie typy woluminów, czy usunie dane na dyskach danych, które już zaszyfrowaliśmy?
Nie, dane nie zostaną usunięte z dysków danych, które są już zaszyfrowane przy użyciu szyfrowania dysków platformy Azure. Podobnie jak EncryptFormatAll nie ponownie zaszyfrować dysk systemu operacyjnego, nie będzie ponownie szyfrować już zaszyfrowanego dysku danych. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy mogę wykonać kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Usługa Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [Tworzenie kopii zapasowych i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup](../../backup/backup-azure-vms-encryption.md).  Przywracanie zaszyfrowanej maszyny Wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie mogę zadać pytania lub przekazać opinię?

Możesz zadawać pytania lub przekazywać opinie na [forum szyfrowania dysków platformy Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się więcej o najczęstszych pytaniach związanych z szyfrowaniem dysków platformy Azure. Aby uzyskać więcej informacji na temat tej usługi, zobacz następujące artykuły:

- [Omówienie usługi Azure Disk Encryption](disk-encryption-overview.md)
- [Stosowanie szyfrowania dysku w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
