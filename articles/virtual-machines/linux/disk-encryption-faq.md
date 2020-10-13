---
title: Często zadawane pytania — Azure Disk Encryption dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 06a84621c4d6abbf361eba232c776c167dc46265
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970613"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Azure Disk Encryption dla maszyn wirtualnych z systemem Linux — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Disk Encryption dla maszyn wirtualnych z systemem Linux. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure Disk Encryption przegląd](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Co to jest Azure Disk Encryption dla maszyn wirtualnych z systemem Linux?

Azure Disk Encryption dla maszyn wirtualnych z systemem Linux używa funkcji dm-crypt systemu Linux w celu zapewnienia pełnego szyfrowania dysków z dysku OS * i dysków danych. Ponadto zapewnia szyfrowanie dysku tymczasowego przy użyciu [funkcji EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Zawartość jest zaszyfrowana z maszyny wirtualnej do zaplecza magazynu. W związku z tym zapewnia to kompleksowe szyfrowanie za pomocą klucza zarządzanego przez klienta.
 
Zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest Azure Disk Encryption ogólnie dostępna?

Azure Disk Encryption dla maszyn wirtualnych z systemem Linux jest ogólnie dostępna we wszystkich regionach publicznych platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie środowiska użytkownika są dostępne w Azure Disk Encryption?

Azure Disk Encryption GA obsługuje Azure Resource Manager szablonów, Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Różne środowiska użytkownika zapewniają elastyczność. Dostępne są trzy różne opcje włączania szyfrowania dysków dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat środowiska użytkownika i wskazówki krok po kroku dostępne w Azure Disk Encryption, zobacz [Azure Disk Encryption scenariusze dla systemu Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje Azure Disk Encryption?

Nie jest naliczana opłata za szyfrowanie dysków maszyn wirtualnych za pomocą Azure Disk Encryption, ale naliczane są opłaty związane z użyciem Azure Key Vault. Aby uzyskać więcej informacji na temat Azure Key Vault kosztów, zobacz stronę [cennika Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak mogę zacząć korzystać z Azure Disk Encryption?

Aby rozpocząć, zapoznaj się z [omówieniem Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemów operacyjnych Azure Disk Encryption obsługiwać?

W artykule [przegląd Azure Disk Encryption](disk-encryption-overview.md) przedstawiono [rozmiary maszyn wirtualnych](disk-encryption-overview.md#supported-vms) i [systemy operacyjne maszyn wirtualnych](disk-encryption-overview.md#supported-operating-systems) obsługujące Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy mogę zaszyfrować woluminy rozruchowe i dane za pomocą Azure Disk Encryption?

Tak, można szyfrować woluminy rozruchowe i dane albo szyfrować wolumin danych bez konieczności wcześniejszego szyfrowania woluminu systemu operacyjnego. 

Po zaszyfrowaniu woluminu systemu operacyjnego wyłączenie szyfrowania na woluminie systemu operacyjnego nie jest obsługiwane. W przypadku maszyn wirtualnych z systemem Linux w zestawie skalowania można zaszyfrować tylko wolumin danych.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować wolumin odinstalowany z Azure Disk Encryption?

Nie, Azure Disk Encryption szyfruje tylko woluminy zainstalowane.

## <a name="what-is-storage-server-side-encryption"></a>Co to jest szyfrowanie po stronie serwera magazynu?

Szyfrowanie po stronie serwera magazynu szyfruje dyski zarządzane platformy Azure w usłudze Azure Storage. Dyski zarządzane są domyślnie szyfrowane za pomocą szyfrowania po stronie serwera z kluczem zarządzanym przez platformę (od 10 czerwca 2017). Można zarządzać szyfrowaniem dysków zarządzanych przy użyciu własnych kluczy, określając klucz zarządzany przez klienta. Aby uzyskać więcej informacji, zobacz: [szyfrowanie po stronie serwera dla usługi Azure Managed disks](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Jak różni się Azure Disk Encryption od szyfrowania po stronie serwera magazynu z kluczem zarządzanym przez klienta i kiedy należy używać poszczególnych rozwiązań?

Azure Disk Encryption zapewnia kompleksowe szyfrowanie dysku systemu operacyjnego, dysków danych i dysku tymczasowego przy użyciu klucza zarządzanego przez klienta.
- Jeśli wymagania obejmują szyfrowanie wszystkich powyższych i kompleksowych danych szyfrowania, użyj Azure Disk Encryption. 
- Jeśli wymagania obejmują szyfrowanie tylko danych przechowywanych w kluczu zarządzanym przez klienta, należy użyć [szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta](disk-encryption.md). Nie można zaszyfrować dysku z szyfrowaniem po stronie serwera Azure Disk Encryption i magazynu przy użyciu kluczy zarządzanych przez klienta. 
- Jeśli dystrybucji systemu Linux nie jest wymieniony w obszarze [obsługiwane systemy operacyjne dla Azure Disk Encryption](disk-encryption-overview.md#supported-operating-systems) lub używasz scenariusza o nazwie out w [scenariuszach nieobsługiwanych w systemie Windows](disk-encryption-linux.md#unsupported-scenarios), weź pod uwagę [szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta](disk-encryption.md).
- Jeśli zasady organizacji umożliwiają szyfrowanie zawartości przechowywanej przy użyciu klucza zarządzanego przez platformę Azure, nie jest wymagana żadna akcja — zawartość jest domyślnie szyfrowana. W przypadku dysków zarządzanych zawartość wewnątrz magazynu jest domyślnie szyfrowana przy użyciu szyfrowania po stronie serwera za pomocą klucza zarządzanego przez platformę. Klucz jest zarządzany przez usługę Azure Storage. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak mogę obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, po prostu wywołaj to samo polecenie, które zostało użyte wcześniej do włączenia szyfrowania dysku, określając inny Key Vault. Aby obrócić klucz szyfrowania klucza, wywołaj to samo polecenie, które zostało użyte pierwotnie, aby włączyć szyfrowanie dysków, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](disk-encryption-linux-aad.md) przez określenie poświadczeń usługi Azure AD do zaszyfrowania tej maszyny wirtualnej, należy użyć tej opcji w celu ZASZYFROWANIA maszyny wirtualnej. Nie można użyć Azure Disk Encryption dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.

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

Istnieją wymagania wstępne dotyczące Azure Disk Encryption. Aby utworzyć aplikację Azure Active Directory, należy zapoznać się z tematem [Azure Disk Encryption with Azure AD](disk-encryption-linux-aad.md) . w tym celu należy utworzyć nowy magazyn kluczy lub skonfigurować istniejący magazyn kluczy na potrzeby dostępu do szyfrowania dysku w celu włączenia szyfrowania i zabezpieczenia kluczy tajnych. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Czy Azure Disk Encryption korzystanie z aplikacji usługi Azure AD (poprzednia wersja) jest nadal obsługiwane?
Tak. Szyfrowanie dysków za pomocą aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się używanie nowej metody zamiast szyfrowania za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy mogę migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD w celu szyfrowania bez użycia aplikacji usługi Azure AD?
  Obecnie nie ma ścieżki migracji bezpośredniej dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD w celu szyfrowania bez aplikacji usługi Azure AD. Ponadto nie istnieje bezpośrednia ścieżka z szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji usługi AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakiej wersji Azure PowerShell Azure Disk Encryption obsługiwać?

Użyj najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption *nie* jest obsługiwana przez zestaw Azure SDK w wersji 1.1.0.

> [!NOTE]
> Rozszerzenie "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" systemu Linux Azure Disk Encryption Preview jest przestarzałe. To rozszerzenie zostało opublikowane na potrzeby wersji zapoznawczej usługi Azure Disk Encryption. Nie należy używać wersji zapoznawczej rozszerzenia w testowaniu lub wdrożeniu produkcyjnym.

> W przypadku scenariuszy wdrażania, takich jak Azure Resource Manager (ARM), w których istnieje potrzeba wdrożenia rozszerzenia Azure Disk Encryption dla maszyny wirtualnej z systemem Linux, aby włączyć szyfrowanie na maszynie wirtualnej z systemem Linux IaaS, należy użyć rozszerzenia "Microsoft. Azure. Security. AzureDiskEncryptionForLinux" usługi Azure Disk Encryption.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Czy mogę zastosować Azure Disk Encryption w moim niestandardowym obrazie systemu Linux?

Nie można zastosować Azure Disk Encryption w niestandardowym obrazie systemu Linux. Obsługiwane są tylko obrazy z galerii systemu Linux dla obsługiwanych dystrybucji, które zostały wywołane wcześniej. Niestandardowe obrazy systemu Linux nie są obecnie obsługiwane.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Czy mogę zastosować aktualizacje do maszyny wirtualnej z systemem Linux Red Hat, która używa aktualizacji Yum?

Tak, możesz wykonać aktualizację yum na maszynie wirtualnej z systemem Red Hat Linux.  Aby uzyskać więcej informacji, zobacz [Azure Disk Encryption w sieci izolowanej](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co to jest zalecany przepływ pracy szyfrowania dysków Azure dla systemu Linux?

Następujący przepływ pracy zaleca się, aby uzyskać najlepsze wyniki w systemie Linux:
* Rozpocznij od niezmodyfikowanego obrazu galerii zasobów odpowiadającego wymaganej dystrybucji systemu operacyjnego i wersji
* Wykonaj kopię zapasową wszystkich zainstalowanych dysków, które będą szyfrowane.  Ta kopia zapasowa umożliwia odzyskiwanie, jeśli wystąpi awaria, na przykład jeśli maszyna wirtualna zostanie ponownie uruchomiona przed ukończeniem szyfrowania.
* Szyfrowanie (może zająć kilka godzin, a nawet dni w zależności od charakterystyki i rozmiaru maszyny wirtualnej)
* Dostosuj i Dodaj oprogramowanie do obrazu w razie potrzeby.

Jeśli ten przepływ pracy nie jest możliwy, poleganie na [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md) (SSE) w warstwie konta magazynu platformy może być alternatywą dla pełnego szyfrowania dysków przy użyciu DM-Crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "klucz szyfrowania bloków Volume" lub "/mnt/azure_bek_disk"?

"Wolumin klucz szyfrowania bloków" to wolumin danych lokalnych, który bezpiecznie przechowuje klucze szyfrowania zaszyfrowanych maszyn wirtualnych platformy Azure.
> [!NOTE]
> Nie usuwaj ani nie edytuj żadnej zawartości na tym dysku. Nie należy odinstalowywać dysku, ponieważ jest wymagana obecność klucza szyfrowania dla każdej operacji szyfrowania na maszynie wirtualnej IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakiej metody szyfrowania Azure Disk Encryption używać?

Azure Disk Encryption używa odszyfrowywania domyślnego AES-XTS-plain64 z kluczem głównym woluminu 256-bitowego.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Jeśli korzystam z EncryptFormatAll i określisz wszystkie typy woluminów, spowoduje to wymazanie danych na dyskach danych, które zostały już zaszyfrowane?
Nie. nie można wymazać danych z dysków danych, które są już zaszyfrowane przy użyciu Azure Disk Encryption. Podobnie jak EncryptFormatAll nie szyfruje dysku systemu operacyjnego, nie szyfruje już jeszcze zaszyfrowanego dysku danych. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Czy system plików XFS jest obsługiwany?
Szyfrowanie dysków systemu operacyjnego XFS jest obsługiwane.

Szyfrowanie dysków danych XFS jest obsługiwane tylko wtedy, gdy używany jest parametr EncryptFormatAll. Spowoduje to przeformatowanie woluminu, wymazanie wcześniej istniejących danych. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy można utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanej maszyny wirtualnej w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [wykonywanie kopii zapasowej i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](../../backup/backup-azure-vms-encryption.md).  Przywracanie zaszyfrowanej maszyny wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie mogę zadawać pytania lub przekazać opinię?

Możesz zadawać pytania lub przekazać informacje zwrotne na [stronie pytania&pytań Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono kilka częstych pytań dotyczących Azure Disk Encryption. Aby uzyskać więcej informacji na temat tej usługi, zobacz następujące artykuły:

- [Omówienie usługi Azure Disk Encryption](disk-encryption-overview.md)
- [Zastosuj szyfrowanie dysków w Azure Security Center](../../security-center/asset-inventory.md)
- [Szyfrowanie danych magazynowanych platformy Azure](../../security/fundamentals/encryption-atrest.md)