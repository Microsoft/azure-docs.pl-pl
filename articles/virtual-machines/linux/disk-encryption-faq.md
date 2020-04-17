---
title: Często zadawane pytania — szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące szyfrowania dysków microsoft azure dla maszyn wirtualnych IaaS systemu Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 753d606e1fc2dc966c970a210cf6fc5066d5ed83
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460141"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Linux — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące szyfrowania dysków platformy Azure dla maszyn wirtualnych systemu Linux. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Co to jest szyfrowanie dysków Azure dla maszyn wirtualnych z systemem Linux?

Usługa Azure Disk Encryption for Linux VMs używa funkcji dm-crypt systemu Linux, aby zapewnić pełne szyfrowanie dysku systemu operacyjnego* i dysków z danymi. Ponadto zapewnia szyfrowanie dysku zasobów efemeryczny podczas korzystania z [funkcji EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Przepływy zawartości zaszyfrowane z maszyny Wirtualnej do wewnętrznej bazy danych magazynu. W ten sposób zapewnia szyfrowanie end-to-end za pomocą klucza zarządzanego przez klienta.
 
Zobacz [Obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest szyfrowanie dysków azure w ogólnej dostępności (GA)?

Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux jest ogólnie dostępne we wszystkich regionach publicznych platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie środowiska użytkownika są dostępne za pomocą szyfrowania dysków platformy Azure?

Usługa Azure Disk Encryption GA obsługuje szablony usługi Azure Resource Manager, usługi Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Różne środowiska użytkownika zapewniają elastyczność. Dostępne są trzy różne opcje włączania szyfrowania dysku dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat środowiska użytkownika i wskazówki krok po kroku dostępne w programie Szyfrowanie dysków platformy Azure, zobacz [Scenariusze szyfrowania dysków platformy Azure dla systemu Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje szyfrowanie dysków platformy Azure?

Szyfrowanie dysków maszyn wirtualnych za pomocą szyfrowania dysków platformy Azure jest bezpłatne, ale istnieją opłaty związane z korzystaniem z usługi Azure Key Vault. Aby uzyskać więcej informacji na temat kosztów usługi Azure Key Vault, zobacz stronę [cennika usługi Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak rozpocząć korzystanie z szyfrowania dysków platformy Azure?

Aby rozpocząć, przeczytaj [omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemy operacyjne obsługują szyfrowanie dysków platformy Azure?

W [artykule Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md) wymieniono [rozmiary maszyn wirtualnych](disk-encryption-overview.md#supported-vms) i systemy operacyjne [maszyn wirtualnych](disk-encryption-overview.md#supported-operating-systems) obsługujące szyfrowanie dysków platformy Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy mogę szyfrować zarówno woluminy rozruchowe, jak i ilości danych za pomocą szyfrowania dysków platformy Azure?

Tak, można szyfrować zarówno woluminy rozruchowe, jak i ilości danych lub można zaszyfrować wolumin danych bez konieczności szyfrowania woluminu systemu operacyjnego. 

Po zaszyfrowaniu woluminu systemu operacyjnego wyłączenie szyfrowania na woluminie systemu operacyjnego nie jest obsługiwane. W przypadku maszyn wirtualnych z systemem Linux w zestawie skalowania można szyfrować tylko wolumin danych.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować niezamontowany wolumin za pomocą szyfrowania dysków platformy Azure?

Nie, szyfrowanie dysków platformy Azure szyfruje tylko zainstalowane woluminy.

## <a name="what-is-storage-server-side-encryption"></a>Co to jest szyfrowanie po stronie serwera magazynu?

Szyfrowanie po stronie serwera magazynu szyfruje dyski zarządzane platformy Azure w usłudze Azure Storage. Dyski zarządzane są domyślnie szyfrowane za pomocą szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę (stan na 10 czerwca 2017 r.). Szyfrowanie dysków zarządzanych za pomocą własnych kluczy można zarządzać, określając klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz: [Szyfrowanie po stronie serwera dysków zarządzanych platformy Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Czym różni się szyfrowanie dysków platformy Azure od szyfrowania po stronie magazynu za pomocą klucza zarządzanego przez klienta i kiedy należy używać każdego rozwiązania?

Usługa Azure Disk Encryption zapewnia szyfrowanie end-to-end dla dysku systemu operacyjnego, dysków danych i efemerycznego dysku zasobów z kluczem zarządzanym przez klienta.
- Jeśli twoje wymagania obejmują szyfrowanie wszystkich powyższych i end-to-end szyfrowania, należy użyć szyfrowania dysków platformy Azure. 
- Jeśli wymagania obejmują szyfrowanie tylko danych w pozostałej części za pomocą klucza zarządzanego przez klienta, użyj [szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta](disk-encryption.md). Nie można zaszyfrować dysku przy pomocą szyfrowania po stronie serwera azure disk i magazynu za pomocą kluczy zarządzanych przez klienta. 
- Jeśli twoja dystrybucja systemu Linux nie jest wymieniona w [obsługiwanych systemach operacyjnych dla szyfrowania dysków azure](disk-encryption-overview.md#supported-operating-systems) lub używasz scenariusza wywoływanego w [nieobsługiwana scenariusze dla systemu Windows,](disk-encryption-linux.md#unsupported-scenarios)należy wziąć pod uwagę [szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta](disk-encryption.md).
- Jeśli zasady organizacji umożliwiają szyfrowanie zawartości w spoczynku za pomocą klucza zarządzanego przez platformę Azure, nie jest wymagana żadna akcja — zawartość jest domyślnie szyfrowana. W przypadku dysków zarządzanych zawartość wewnątrz magazynu jest domyślnie szyfrowana za pomocą szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę. Kluczem jest zarządzana przez usługę Azure Storage. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, wystarczy wywołać to samo polecenie, które pierwotnie użyto do włączenia szyfrowania dysku, określając inny magazyn kluczy. Aby obrócić klucz szyfrowania klucza, należy wywołać to samo polecenie, które było pierwotnie używane do włączania szyfrowania dysku, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej używano [szyfrowania dysków platformy Azure za pomocą aplikacji Usługi Azure AD,](disk-encryption-linux-aad.md) określając poświadczenia usługi Azure AD w celu zaszyfrowania tej maszyny Wirtualnej, musisz nadal używać tej opcji do szyfrowania maszyny wirtualnej. Nie można użyć szyfrowania dysków platformy Azure na tej zaszyfrowanej maszynie wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że odłączenie się od aplikacji AAD dla tej zaszyfrowanej maszyny Wirtualnej nie jest jeszcze obsługiwane.

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

Istnieją wymagania wstępne dotyczące szyfrowania dysków platformy Azure. Zobacz [zawartość usługi Azure Disk Encryption with Azure AD,](disk-encryption-linux-aad.md) aby utworzyć aplikację usługi Azure Active Directory, utworzyć nową przechowalnię kluczy lub skonfigurować istniejącą przechowalnię kluczy w celu umożliwienia szyfrowania i ochrony wpisów tajnych i kluczy. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Czy szyfrowanie dysków platformy Azure przy użyciu aplikacji usługi Azure AD (poprzednia wersja) jest nadal obsługiwane?
Tak. Szyfrowanie dysku przy użyciu aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się użycie nowej metody, a nie szyfrowanie za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy mogę migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD do szyfrowania bez aplikacji usługi Azure AD?
  Obecnie nie istnieje bezpośrednia ścieżka migracji dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD do szyfrowania bez aplikacji usługi Azure AD. Ponadto nie ma bezpośredniej ścieżki od szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jaka wersja programu Azure PowerShell obsługuje szyfrowanie dysków platformy Azure?

Użyj najnowszej wersji zestawu SDK programu Azure PowerShell, aby skonfigurować szyfrowanie dysków platformy Azure. Pobierz najnowszą wersję programu [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Szyfrowanie dysków platformy Azure *nie* jest obsługiwane przez zestaw SDK platformy Azure w wersji 1.1.0.

> [!NOTE]
> Rozszerzenie podglądu szyfrowania dysku systemu Linux Azure "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" jest przestarzałe. To rozszerzenie zostało opublikowane w wersji zapoznawczej szyfrowania dysku platformy Azure. Nie należy używać wersji zapoznawczej rozszerzenia w testowaniu lub wdrożeniu produkcyjnym.

> W scenariuszach wdrażania, takich jak Usługa Azure Resource Manager (ARM), w których konieczne jest wdrożenie rozszerzenia szyfrowania dysku platformy Azure dla maszyny Wirtualnej systemu Linux, aby włączyć szyfrowanie na maszynie wirtualnej IaaS systemu Linux, należy użyć obsługiwanego rozszerzenia produkcji szyfrowania dysków platformy Azure "Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Czy mogę zastosować szyfrowanie dysków platformy Azure na niestandardowym obrazie systemu Linux?

Nie można zastosować szyfrowania dysków platformy Azure na niestandardowym obrazie systemu Linux. Obsługiwane są tylko obrazy z systemem Linux dla obsługiwanych dystrybucji wywoływanych wcześniej. Niestandardowe obrazy linuksa nie są obecnie obsługiwane.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Czy mogę zastosować aktualizacje do maszyny Wirtualnej Linux Red Hat, która korzysta z aktualizacji yum?

Tak, możesz wykonać aktualizację yum na maszynie wirtualnej Red Hat Linux.  Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysków platformy Azure w sieci izolowanej](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co to jest zalecany przepływ pracy szyfrowania dysków platformy Azure dla systemu Linux?

Następujący przepływ pracy jest zalecane, aby mieć najlepsze wyniki w systemie Linux:
* Zacznij od niezmodyfikowanego obrazu galerii stockowej odpowiadającego potrzebnej dystrybucji systemu operacyjnego i wersji
* Śmięk ć wszystkie zainstalowane dyski, które będą szyfrowane.  Ta kopii zapasowej umożliwia odzyskiwanie, jeśli występuje błąd, na przykład jeśli maszyna wirtualna jest ponownie uruchomiony przed zakończeniem szyfrowania.
* Szyfrowanie (może potrwać kilka godzin lub nawet dni w zależności od charakterystyki maszyny Wirtualnej i rozmiaru wszystkich dołączonych dysków z danymi)
* Dostosuj i dodaj oprogramowanie do obrazu w razie potrzeby.

Jeśli ten przepływ pracy nie jest możliwy, poleganie na [szyfrowaniu usługi storage service](../../storage/common/storage-service-encryption.md) (SSE) w warstwie konta magazynu platformy może być alternatywą dla pełnego szyfrowania dysku przy użyciu dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "Bek Volume" lub "/mnt/azure_bek_disk"?

"Wolumin Bek" to lokalny wolumin danych, który bezpiecznie przechowuje klucze szyfrowania zaszyfrowanych maszyn wirtualnych platformy Azure.
> [!NOTE]
> Nie należy usuwać ani edytować zawartości tego dysku. Nie należy odinstalowywaj dysku, ponieważ obecność klucza szyfrowania jest wymagana dla operacji szyfrowania na maszynie wirtualnej IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakiej metody szyfrowania używa szyfrowanie dysków Azure?

Usługa Azure Disk Encryption używa domyślnego odszyfrowania aes-xts-plain64 z 256-bitowym kluczem głównym woluminu.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Jeśli używam EncryptFormatAll i określi wszystkie typy woluminów, czy usunie dane na dyskach danych, które już zaszyfrowaliśmy?
Nie, dane nie zostaną usunięte z dysków danych, które są już zaszyfrowane przy użyciu szyfrowania dysków platformy Azure. Podobnie jak EncryptFormatAll nie ponownie zaszyfrować dysk systemu operacyjnego, nie będzie ponownie szyfrować już zaszyfrowanego dysku danych. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Czy system plików XFS jest obsługiwany?
Szyfrowanie dysków systemu operacyjnego XFS jest obsługiwane.

Szyfrowanie dysków danych XFS jest obsługiwane tylko wtedy, gdy używany jest parametr EncryptFormatAll. Spowoduje to sformatowanie woluminu, wymazując wszystkie dane wcześniej tam. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy mogę wykonać kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Usługa Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [Tworzenie kopii zapasowych i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Przywracanie zaszyfrowanej maszyny Wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie mogę zadać pytania lub przekazać opinię?

Możesz zadawać pytania lub przekazywać opinie na [forum szyfrowania dysków platformy Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się więcej o najczęstszych pytaniach związanych z szyfrowaniem dysków platformy Azure. Aby uzyskać więcej informacji na temat tej usługi, zobacz następujące artykuły:

- [Omówienie usługi Azure Disk Encryption](disk-encryption-overview.md)
- [Stosowanie szyfrowania dysku w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie danych platformy Azure w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
