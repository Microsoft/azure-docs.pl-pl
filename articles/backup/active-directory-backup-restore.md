---
title: Tworzenie kopii zapasowych i przywracanie Active Directory
description: Dowiedz się, jak utworzyć kopię zapasową i przywrócić Active Directory kontrolerów domeny.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733557"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Tworzenie kopii zapasowych i przywracanie Active Directory kontrolerów domeny

Tworzenie kopii zapasowej Active Directory i zapewnianie pomyślnych operacji przywracania w przypadku uszkodzenia, naruszenia lub awarii jest krytyczną częścią Active Directory konserwacji.

W tym artykule opisano odpowiednie procedury tworzenia kopii zapasowych i przywracania Active Directory kontrolerów domeny z Azure Backup, niezależnie od tego, czy są to maszyny wirtualne platformy Azure, czy serwery lokalne. Omówiono w nim scenariusz, w którym należy przywrócić cały kontroler domeny do stanu w momencie tworzenia kopii zapasowej. Aby sprawdzić, który scenariusz przywracania jest odpowiedni dla Ciebie, zobacz [ten artykuł](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> W tym artykule nie omówiono przywracania elementów z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Informacje dotyczące przywracania Azure Active Directory użytkowników znajdują się w [tym artykule](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Najlepsze rozwiązania

- Upewnij się, że utworzono kopię zapasową co najmniej jednego kontrolera domeny. Jeśli wykonujesz kopię zapasową więcej niż jednego kontrolera domeny, upewnij się, że jest wykonywana kopia zapasowa wszystkich ról, które mają [role FSMO (elastyczna operacja pojedynczego wzorca)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) .

- Często Twórz kopie zapasowe Active Directory. Kopia zapasowa nigdy nie powinna być większa niż okres istnienia reliktu (domyślnie 60 dni), ponieważ obiekty starsze niż okres istnienia reliktu będą "schowane" i nie są już uznawane za ważne.

- Należy wyczyścić plan odzyskiwania po awarii, który zawiera instrukcje przywracania kontrolerów domeny. Aby przygotować się do przywrócenia lasu Active Directory, Przeczytaj [Przewodnik dotyczący odzyskiwania lasu Active Directory](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Jeśli trzeba przywrócić kontroler domeny i mieć w domenie pozostały kontroler domeny, można utworzyć nowy serwer zamiast przywracania z kopii zapasowej. Dodaj rolę serwera **Active Directory Domain Services** do nowego serwera, aby uczynić go kontrolerem domeny w istniejącej domenie. Następnie Active Directory dane zostaną zreplikowane na nowy serwer. Aby usunąć poprzedni kontroler domeny z Active Directory, wykonaj kroki opisane [w tym artykule](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) , aby przeprowadzić Oczyszczanie metadanych.

>[!NOTE]
>Azure Backup nie obejmuje przywracania na poziomie elementu dla Active Directory. Jeśli chcesz przywrócić usunięte obiekty i uzyskać dostęp do kontrolera domeny, użyj [Active Directory kosza](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Jeśli ta metoda jest niedostępna, można użyć kopii zapasowej kontrolera domeny do przywrócenia usuniętych obiektów za pomocą narzędzia **ntdsutil.exe** , jak wyjaśniono [tutaj](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Informacje o wykonywaniu autorytatywnego przywracania folderu SYSVOL znajdują się w [tym artykule](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Tworzenie kopii zapasowych kontrolerów domeny maszyny wirtualnej platformy Azure

Jeśli kontroler domeny jest maszyną wirtualną platformy Azure, możesz utworzyć kopię zapasową serwera przy użyciu [kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-vms-introduction.md).

Przeczytaj informacje [na temat zagadnień operacyjnych dotyczących zwirtualizowanych kontrolerów domeny](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) , aby zapewnić pomyślne tworzenie kopii zapasowych (i przyszłe przywrócenie) kontrolerów domeny maszyn wirtualnych platformy Azure.

## <a name="backing-up-on-premises-domain-controllers"></a>Tworzenie kopii zapasowych lokalnych kontrolerów domeny

Aby utworzyć kopię zapasową lokalnego kontrolera domeny, należy wykonać kopię zapasową danych stanu systemu serwera.

- Jeśli używasz usługi MARS, postępuj zgodnie z [tymi instrukcjami](backup-azure-system-state.md).
- Jeśli używasz serwera usługi MAB (Azure Backup Server), postępuj zgodnie z [tymi instrukcjami](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Przywracanie lokalnych kontrolerów domeny (z stanu systemu lub z maszyn wirtualnych) do chmury platformy Azure nie jest obsługiwane. Jeśli chcesz włączyć tryb failover z lokalnego środowiska Active Directory na platformie Azure, rozważ użycie [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Przywracanie Active Directory

Active Directory dane można przywrócić w jednym z dwóch trybów: **autorytatywnych** lub **nieautorytatywnych**. W przypadku przywracania autorytatywnego przywrócone dane Active Directory zastąpią dane znajdujące się na pozostałych kontrolerach domeny w lesie.

Jednak w tym scenariuszu odbudowujemy kontroler domeny w istniejącej domenie, dlatego należy wykonać przywracanie **nieautorytatywne** .

Podczas przywracania serwer zostanie uruchomiony w trybie przywracania usług katalogowych (DSRM). Musisz podać hasło administratora dla trybu przywracania usług katalogowych.

>[!NOTE]
>Jeśli hasło trybu DSRM zostanie zapomniane, można je zresetować przy użyciu [tych instrukcji](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Przywracanie kontrolerów domeny maszyny wirtualnej platformy Azure

Aby przywrócić kontroler domeny maszyny wirtualnej platformy Azure, zobacz [Przywracanie maszyn wirtualnych kontrolera domeny](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Jeśli przywracasz maszynę wirtualną z jednym kontrolerem domeny lub wiele maszyn wirtualnych kontrolera domeny w jednej domenie, przywróć je podobnie jak każda inna maszyna wirtualna. Tryb przywracania usług katalogowych (DSRM) jest również dostępny, dlatego wszystkie Active Directory scenariusze odzyskiwania są dostępne.

Jeśli musisz przywrócić pojedynczą maszynę wirtualną kontrolera domeny w konfiguracji wielu domen, Przywróć dyski i Utwórz maszynę wirtualną za [pomocą programu PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Jeśli przywracasz ostatni pozostały kontroler domeny w domenie lub przywracasz wiele domen w jednym lesie, zalecamy [odzyskanie lasu](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Zwirtualizowane kontrolery domeny z systemu Windows 2012 korzystają z [zabezpieczeń opartych na wirtualizacji](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Dzięki tym zabezpieczeniom usługa Active Directory rozumie, czy przywrócona maszyna wirtualna jest kontrolerem domeny, i wykonuje kroki niezbędne do przywrócenia danych Active Directory.

### <a name="restoring-on-premises-domain-controllers"></a>Przywracanie lokalnych kontrolerów domeny

Aby przywrócić lokalny kontroler domeny, postępuj zgodnie z instrukcjami w temacie Przywracanie stanu systemu do systemu Windows Server przy użyciu wskazówek dotyczących [zagadnień dotyczących odzyskiwania stanu systemu na kontrolerze domeny](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi dla Azure Backup](backup-support-matrix.md)