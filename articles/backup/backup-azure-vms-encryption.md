---
title: Twojnie kopii zapasowych i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure
description: W tym artykule opisano sposób tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 98febe9f91cb4b71d546300d4e65ade073d19e67
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461773"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Twojnie kopii zapasowych i przywracanie zaszyfrowanej maszyny Wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia kopii zapasowych i przywracania maszyn wirtualnych systemu Windows lub Linux Azure z zaszyfrowanymi dyskami przy użyciu usługi [Azure Backup.](backup-overview.md)

Jeśli chcesz dowiedzieć się więcej o tym, jak usługa Azure Backup współdziała z maszynami wirtualnymi platformy Azure przed rozpoczęciem, zapoznaj się z tymi zasobami:

- [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowej maszyny Wirtualnej platformy Azure.
- [Dowiedz się więcej o](backup-azure-vms-introduction.md) Kopia zapasowa maszyny wirtualnej platformy Azure i rozszerzenie usługi Azure Backup.

## <a name="encryption-support"></a>Obsługa szyfrowania

Usługa Azure Backup obsługuje tworzenie kopii zapasowych maszyn wirtualnych platformy Azure, które mają swoje dyski systemu operacyjnego/danych zaszyfrowane za pomocą szyfrowania dysków azure (ADE). Usługa ADE używa funkcji BitLocker do szyfrowania maszyn wirtualnych z systemem Windows oraz funkcji dm-crypt dla maszyn wirtualnych z systemem Linux. Usługa ADE integruje się z usługą Azure Key Vault w celu zarządzania kluczami szyfrowania dysku i wpisami tajnymi. Klucze szyfrowania kluczy kryjówki kluczy (CKE) mogą służyć do dodania dodatkowej warstwy zabezpieczeń, szyfrowania wpisów tajnych szyfrowania przed zapisaniem ich do usługi Key Vault.

Usługa Azure Backup może wykonać kopię zapasową i przywrócić maszyny wirtualne platformy Azure przy użyciu usługi ADE z aplikacją usługi Azure AD i bez niej, jak podsumowano w poniższej tabeli.

**Typ dysku maszyny Wirtualnej** | **ADE (BEK/dm-crypt)** | **ADE i KEK**
--- | --- | ---
**Niezarządzany** | Tak | Tak
**Zarządzane**  | Tak | Tak

- Dowiedz się więcej o [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/general/overview.md)i [KEKs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Przeczytaj [często](../security/azure-security-disk-encryption-faq.md) zadawane pytania dotyczące szyfrowania dysków maszyn wirtualnych platformy Azure.

### <a name="limitations"></a>Ograniczenia

- Można wyw kopii zapasowej i przywrócić zaszyfrowane maszyny wirtualne w ramach tej samej subskrypcji i regionu.
- Usługa Azure Backup obsługuje maszyny wirtualne szyfrowane przy użyciu kluczy autonomicznych. Każdy klucz, który jest częścią certyfikatu używanego do szyfrowania maszyny Wirtualnej nie jest obecnie obsługiwany.
- Można wykonać kopię zapasową i przywrócić zaszyfrowane maszyny wirtualne w ramach tej samej subskrypcji i regionu co magazyn kopii zapasowych usług odzyskiwania.
- Zaszyfrowanych maszyn wirtualnych nie można odzyskać na poziomie pliku/folderu. Aby przywrócić pliki i foldery, należy odzyskać całą maszynę wirtualną.
- Podczas przywracania maszyny Wirtualnej nie można użyć opcji [zastąp istniejącą maszynę wirtualną](backup-azure-arm-restore-vms.md#restore-options) dla zaszyfrowanych maszyn wirtualnych. Ta opcja jest obsługiwana tylko w przypadku niezaszyfrowanych dysków zarządzanych.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem wykonaj następujące czynności:

1. Upewnij się, że masz jedną lub więcej maszyn wirtualnych [z systemem Windows](../security/azure-security-disk-encryption-windows.md) lub [Linux](../virtual-machines/linux/disk-encryption-overview.md) z włączoną funkcją ADE.
2. [Przejrzyj macierz pomocy technicznej](backup-support-matrix-iaas.md) dla kopii zapasowej maszyny Wirtualnej platformy Azure
3. [Utwórz](backup-azure-arm-vms-prepare.md#create-a-vault) magazyn kopii zapasowych usług odzyskiwania, jeśli jej nie masz.
4. Jeśli włączysz szyfrowanie maszyn wirtualnych, które są już włączone do tworzenia kopii zapasowych, wystarczy zapewnić kopii zapasowej uprawnienia dostępu do usługi Key Vault, aby kopie zapasowe mogły być kontynuowane bez zakłóceń. [Dowiedz się więcej](#provide-permissions) o przypisywaniu tych uprawnień.

Ponadto, istnieje kilka rzeczy, które mogą być konieczne do zrobienia w pewnych okolicznościach:

- **Zainstaluj agenta maszyny Wirtualnej na maszynie Wirtualnej:** Usługa Azure Backup kopie zapasowe maszyn wirtualnych platformy Azure, instalując rozszerzenie agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure marketplace, agent jest instalowany i uruchamiany. W przypadku utworzenia niestandardowej maszyny wirtualnej lub migracji komputera lokalnego może być konieczne [ręczne zainstalowanie agenta.](backup-azure-arm-vms-prepare.md#install-the-vm-agent)

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

1. Jeśli magazyn kopii zapasowych usług odzyskiwania nie został jeszcze utworzony, postępuj zgodnie z [tymi instrukcjami](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Otwórz przechowalnię w portalu i wybierz **pozycję Kopia zapasowa** w sekcji **Wprowadzenie.**

    ![Blok zapasowy](./media/backup-azure-vms-encryption/select-backup.png)

3. W **celu** > kopii zapasowej Gdzie **Azure**jest**uruchomione obciążenie?**
4. W obszarze Co chcesz wykonać **Virtual machine** > **OK** **kopii zapasowej?**

      ![Ostrze scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. W **obszarze Zasady** > tworzenia kopii**zapasowych Wybierz zasady tworzenia kopii zapasowych**wybierz zasadę, którą chcesz skojarzyć z magazynem. Następnie kliknij przycisk **OK**.
    - Zasady tworzenia kopii zapasowych określają, kiedy kopie zapasowe są pobierane i jak długo są przechowywane.
    - Szczegóły domyślnych zasad znajdują się w menu rozwijanym.

    ![Otwarcie bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Jeśli nie chcesz używać zasad domyślnych, wybierz pozycję **Utwórz nowy**i [utwórz zasadę niestandardową](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Wybierz zaszyfrowane maszyny wirtualne, których tworzenie kopii zapasowej ma zostać utworzone przy użyciu zasad wyboru, a następnie wybierz przycisk **OK**.

      ![Wybieranie zaszyfrowanych maszyn wirtualnych](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Jeśli używasz usługi Azure Key Vault, na stronie przechowalni zostanie wyświetlony komunikat, że usługa Azure Backup potrzebuje dostępu tylko do odczytu do kluczy i wpisów tajnych w magazynie kluczy.

    - Jeśli zostanie wyświetlony ten komunikat, nie jest wymagana żadna akcja.

        ![Dostęp OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Jeśli zostanie wyświetlony ten komunikat, musisz ustawić uprawnienia zgodnie z [poniższą procedurą](#provide-permissions).

        ![Ostrzeżenie o dostępie](./media/backup-azure-vms-encryption/access-warning.png)

9. Kliknij **pozycję Włącz kopię zapasową,** aby wdrożyć zasady tworzenia kopii zapasowych w przechowalni, a następnie włączyć tworzenie kopii zapasowych dla wybranych maszyn wirtualnych.

## <a name="trigger-a-backup-job"></a>Wyzwalanie zadania tworzenia kopii zapasowej

Początkowa kopia zapasowa zostanie uruchomiona zgodnie z harmonogramem, ale można ją uruchomić natychmiast w następujący sposób:

1. W menu przechowalni kliknij polecenie **Zapasy elementów**.
2. W **obszarze Elementy kopii zapasowej**kliknij pozycję Maszyna **wirtualna platformy Azure**.
3. Na liście **Elementy kopii zapasowej** kliknij wielokropek (...).
4. Kliknij **pozycję Kopia zapasowa teraz**.
5. W **obszarze Kopia zapasowa teraz**użyj formantu kalendarza, aby wybrać ostatni dzień, w którym punkt odzyskiwania powinien zostać zachowany. Następnie kliknij przycisk **OK**.
6. Monitorowanie powiadomień portalu. Postęp zadania na pulpicie nawigacyjnym magazynu można monitorować > **Zadania** > kopii zapasowej**w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="provide-permissions"></a>Podaj uprawnienia

Maszyna wirtualna platformy Azure potrzebuje dostępu tylko do odczytu, aby uzyskać zapas zapasowy kluczy i wpisów tajnych wraz ze skojarzonymi maszynami wirtualnymi.

- Usługa Key Vault jest skojarzona z dzierżawą usługi Azure AD w ramach subskrypcji platformy Azure. Jeśli jesteś **użytkownikiem członkowskim,** usługa Azure Backup uzyskuje dostęp do usługi Key Vault bez dalszych działań.
- Jeśli jesteś **użytkownikiem-gościem,** musisz podać uprawnienia do usługi Azure Backup, aby uzyskać dostęp do magazynu kluczy.

Aby ustawić uprawnienia:

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**i wyszukaj **magazyny kluczy**.
2. Wybierz magazyn kluczy skojarzony z szyfrowaną maszyną wirtualną, której kopię zapasową.
3. Wybierz **pozycję Zasady** > dostępu**Dodaj nowy**.
4. Wybierz **pozycję Wybierz głównego zobowiązanego,** a następnie wpisz polecenie Zarządzanie **kopiami zapasowymi**.
5. Wybierz pozycję Wybierz >  **usługę zarządzania kopiami zapasowymi****.**

    ![Wybór usługi tworzenia kopii zapasowych](./media/backup-azure-vms-encryption/select-backup-service.png)

6. W **obszarze Dodaj zasady** > dostępu**Konfiguruj z szablonu (opcjonalnie)** wybierz pozycję **Azure Backup**.
    - Wymagane uprawnienia są wstępnie wypełnione dla **uprawnień klucza** i **uprawnień tajnego**.
    - Jeśli maszyna wirtualna jest szyfrowana tylko przy użyciu **bek,** usuń wybór **uprawnień klucza,** ponieważ potrzebujesz tylko uprawnień do wpisów tajnych.

    ![Wybór kopii zapasowej platformy Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Kliknij przycisk **OK**. **Usługa zarządzania kopiami zapasowymi** jest dodawana do **zasad programu Access**.

    ![Zasady dostępu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Kliknij **przycisk Zapisz,** aby zapewnić usługę Azure Backup z uprawnieniami.

## <a name="restore-an-encrypted-vm"></a>Przywracanie zaszyfrowanej maszyny Wirtualnej

Szyfrowane maszyny wirtualne można przywrócić w następujący sposób:

1. [Przywróć dysk maszyny Wirtualnej](backup-azure-arm-restore-vms.md#restore-disks).
2. Ponownie utworzyć wystąpienie maszyny wirtualnej, wykonując jedną z następujących czynności:
    1. Użyj szablonu, który jest generowany podczas operacji przywracania, aby dostosować ustawienia maszyny Wirtualnej i wyzwolić wdrożenie maszyny Wirtualnej. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Utwórz nową maszynę wirtualną z przywróconych dysków przy użyciu programu PowerShell. [Dowiedz się więcej](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. W przypadku maszyn wirtualnych z systemem Linux zainstaluj ponownie rozszerzenie ADE, aby dyski danych były otwarte i zainstalowane.

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, zapoznaj się z tymi artykułami:

- [Typowe błędy](backup-azure-vms-troubleshoot.md) podczas tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych platformy Azure.
- Problemy [z agentem/rozszerzeniem kopii zapasowej platformy Azure.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
