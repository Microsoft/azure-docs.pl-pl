---
title: Tworzenie i konfigurowanie magazynów usługi Recovery Services
description: W tym artykule dowiesz się, jak tworzyć i konfigurować magazyny usługi Recovery Services, w których są przechowywane kopie zapasowe i punkty odzyskiwania. Dowiedz się, jak przywrócić dane w regionie pomocniczym za pomocą funkcji przywracania między regionami.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518579"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Tworzenie i konfigurowanie magazynu usługi Recovery Services

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości magazynu

Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania tego magazynu.

> [!NOTE]
> Przed **skonfigurowaniem** kopii zapasowych w magazynie należy zmienić typ replikacji magazynu (lokalnie nadmiarowy/geograficznie nadmiarowy) dla magazynu usługi Recovery Services. Po skonfigurowaniu kopii zapasowej opcja modyfikowania jest wyłączona.
>
>- Jeśli kopia zapasowa nie została jeszcze skonfigurowana, wykonaj [następujące](#set-storage-redundancy) kroki, aby przejrzeć i zmodyfikować ustawienia.
>- Jeśli masz już skonfigurowaną kopię zapasową i musisz przejść z grs do LRS, zapoznaj się z [tymi obejściami.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. W **okienku Magazyny usługi Recovery Services** wybierz nowy magazyn. W sekcji **Ustawienia** wybierz pozycję **Właściwości.**
1. W **obszarze Właściwości** w obszarze Konfiguracja kopii **zapasowej** wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu, a następnie wybierz pozycję **Zapisz.**

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Zalecamy, aby w przypadku korzystania z platformy Azure jako podstawowego punktu końcowego magazynu kopii zapasowych nadal używać domyślnego ustawienia **geograficznie nadmiarowego.**
   - Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
   - Dowiedz się więcej o [nadmiarowości geograficznej](../storage/common/storage-redundancy.md#geo-redundant-storage) [i](../storage/common/storage-redundancy.md#locally-redundant-storage) lokalnej.
   - Jeśli potrzebujesz dostępności danych bez przestojów w regionie, co gwarantuje rezydencję danych, wybierz magazyn [strefowo nadmiarowy.](../storage/common/storage-redundancy.md#zone-redundant-storage)

>[!NOTE]
>Ustawienia replikacji magazynu dla magazynu nie mają znaczenia dla kopii zapasowej udziału plików platformy Azure, ponieważ bieżące rozwiązanie jest oparte na migawkach i nie ma żadnych danych przesyłanych do magazynu. Migawki są przechowywane na tym samym koncie magazynu, w których jest przechowywany udział plików z kopią zapasową.

## <a name="set-cross-region-restore"></a>Ustawianie przywracania między regionami

Opcja przywracania **Przywracanie między regionami (CRR)** umożliwia przywracanie danych w regionie pomocniczym [sparowanego platformy Azure.](../best-practices-availability-paired-regions.md)

Obsługuje ona następujące źródła danych:

- Maszyny wirtualne platformy Azure (ogólna dostępność)
- Bazy danych SQL hostowane na maszynach wirtualnych platformy Azure (wersja zapoznawcza)
- SAP HANA baz danych hostowanych na maszyn wirtualnych platformy Azure (wersja zapoznawcza)

Przywracanie między regionami umożliwia:

- przeprowadzanie awarii w przypadku wymagania inspekcji lub zgodności
- przywracanie danych w przypadku awarii w regionie podstawowym

Podczas przywracania maszyny wirtualnej można przywrócić maszynę wirtualną lub jej dysk. Jeśli przywracasz z baz danych SQL/SAP HANA hostowanych na maszyn wirtualnych platformy Azure, możesz przywrócić bazy danych lub ich pliki.

Aby wybrać tę funkcję, wybierz pozycję Włącz przywracanie **między regionami** w **okienku Konfiguracja kopii zapasowej.**

Ponieważ ten proces jest na poziomie magazynu, istnieją [implikacje cenowe](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Przed rozpoczęciem:
>
>- Przejrzyj macierz [obsługi,](backup-support-matrix.md#cross-region-restore) aby uzyskać listę obsługiwanych typów zarządzanych i regionów.
>- Funkcja przywracania między regionami (CRR) dla maszyn wirtualnych platformy Azure jest teraz ogólnie dostępna we wszystkich publicznych regionach platformy Azure.
>- Przywracanie między regionami dla baz danych SQL i SAP HANA jest w wersji zapoznawczej we wszystkich publicznych regionach platformy Azure.
>- CRR to funkcja opt-in na poziomie magazynu dla dowolnego magazynu GRS (domyślnie wyłączona).
>- Po wybraniu opcji może upłynieć do 48 godzin, aż elementy kopii zapasowej będą dostępne w regionach pomocniczych.
>- Obecnie magazyn CRR dla maszyn wirtualnych platformy Azure jest obsługiwany Azure Resource Manager maszyn wirtualnych platformy Azure i zaszyfrowanych maszyn wirtualnych platformy Azure. Klasyczne maszyny wirtualne platformy Azure nie będą obsługiwane. Jeśli dodatkowe typy zarządzania obsługują magazyn CRR, zostaną **one automatycznie** zarejestrowane.
>- Obecnie nie **można** przywrócić przywracania między regionami do grs lub LRS po zainicjowaniu ochrony po raz pierwszy.
>- Obecnie cel [punktu punktu dostępu w](azure-backup-glossary.md#rpo-recovery-point-objective) regionie pomocniczym jest do 12 godzin od regionu podstawowego, mimo że replikacja magazynu geograficznie nadmiarowego dostępnego do odczytu [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) wynosi 15 minut.

### <a name="configure-cross-region-restore"></a>Konfigurowanie przywracania między regionami

Magazyn utworzony z nadmiarowością GRS zawiera opcję skonfigurowania funkcji przywracania między regionami. Każdy magazyn GRS będzie mieć transparent, który będzie zawierał link do dokumentacji. Aby skonfigurować magazyn CRR, przejdź do okienka Konfiguracja kopii zapasowej, które zawiera opcję włączenia tej funkcji.

 ![Transparent konfiguracji kopii zapasowej](./media/backup-azure-arm-restore-vms/banner.png)

1. W portalu przejdź do magazynu usługi Recovery Services, a > **właściwości** (w **obszarze Ustawienia).**
1. W **obszarze Konfiguracja kopii zapasowej** wybierz pozycję **Aktualizuj**.
1. Wybierz **pozycję Włącz przywracanie między regionami w tym magazynie,** aby włączyć tę funkcję.

   ![Włączanie przywracania między regionami](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Zobacz następujące artykuły, aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania za pomocą CRR:

- [Przywracanie między regionami dla maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Przywracanie między regionami dla baz danych SQL](restore-sql-database-azure-vm.md#cross-region-restore)
- [Przywracanie między regionami dla baz SAP HANA danych](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Ustawianie ustawień szyfrowania

Domyślnie dane w magazynie usługi Recovery Services są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć to szyfrowanie, nie są wymagane żadne jawne akcje. Dotyczy to wszystkich obciążeń, których kopię zapasową obowiązuje w magazynie usługi Recovery Services.  Aby zaszyfrować dane kopii zapasowej w tym magazynie, możesz wybrać opcję przyniesienia własnego klucza. Jest to nazywane kluczami zarządzanymi przez klienta. Jeśli chcesz zaszyfrować dane kopii zapasowej przy użyciu własnego klucza, należy określić klucz szyfrowania, zanim dowolny element będzie chroniony w tym magazynie. Po włączeniu szyfrowania za pomocą klucza nie można go cofnąć.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurowanie magazynu do szyfrowania przy użyciu kluczy zarządzanych przez klienta

Aby skonfigurować magazyn do szyfrowania przy użyciu kluczy zarządzanych przez klienta, należy wykonać następujące kroki w następującej kolejności:

1. Włączanie tożsamości zarządzanej dla magazynu usługi Recovery Services

1. Przypisz do magazynu uprawnienia dostępu do klucza szyfrowania w Azure Key Vault

1. Włączanie usuwania nie softowego i ochrony przed przeczyszczaniem na Azure Key Vault

1. Przypisywanie klucza szyfrowania do magazynu usługi Recovery Services

Instrukcje dotyczące każdego z tych kroków można znaleźć [w tym artykule.](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)

## <a name="modifying-default-settings"></a>Modyfikowanie ustawień domyślnych

Przed skonfigurowaniem kopii zapasowych w magazynie zdecydowanie zalecamy zapoznanie się z ustawieniami domyślnymi **Typ replikacji magazynu** i **Ustawienia zabezpieczeń**.

- **Domyślny typ replikacji** magazynu to **Geograficznie nadmiarowy** (GRS). Po skonfigurowaniu kopii zapasowej opcja modyfikowania jest wyłączona.
  - Jeśli kopia zapasowa nie została jeszcze skonfigurowana, wykonaj [następujące](#set-storage-redundancy) kroki, aby przejrzeć i zmodyfikować ustawienia.
  - Jeśli masz już skonfigurowaną kopię zapasową i musisz przejść z grs do LRS, zapoznaj się z [tymi obejściami.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **Usuwanie nieumyślne** domyślnie ma wartość **Włączone** w nowo utworzonych magazynach, aby chronić dane kopii zapasowej przed przypadkowym lub złośliwym usunięciem. [Wykonaj następujące kroki,](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) aby przejrzeć i zmodyfikować ustawienia.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Jak zmienić grs na LRS po skonfigurowaniu kopii zapasowej

Przed podjęciem decyzji o przejście z magazynu GRS do magazynu lokalnie nadmiarowego (LRS) zapoznaj się z różnicami między niższym kosztem a wyższą trwałością danych, które pasują do twojego scenariusza. Jeśli musisz przejść z grs do LRS, masz dwie opcje. Zależą one od wymagań biznesowych dotyczących przechowywania danych kopii zapasowej:

- [Nie trzeba zachowywać danych z poprzedniej kopii zapasowej](#dont-need-to-preserve-previous-backed-up-data)
- [Musi zachować poprzednie dane kopii zapasowej](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nie trzeba zachowywać danych z poprzedniej kopii zapasowej

Aby chronić obciążenia w nowym magazynie LRS, należy usunąć bieżącą ochronę i dane w magazynie GRS i ponownie skonfigurować kopie zapasowe.

>[!WARNING]
>Następująca operacja jest destruktywna i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

Zatrzymaj i usuń bieżącą ochronę magazynu GRS:

1. Wyłącz usuwanie nie soft we właściwościach magazynu GRS. Wykonaj [następujące kroki,](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) aby wyłączyć usuwanie nie soft.

1. Zatrzymaj ochronę i usuń kopie zapasowe z istniejącego magazynu GRS. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **Elementy kopii zapasowej.** Elementy wymienione w tym miejscu, które należy przenieść do magazynu LRS, muszą zostać usunięte wraz z danymi kopii zapasowej. Zobacz, jak [usuwać chronione elementy w chmurze i](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) [usuwać chronione elementy lokalnie.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

1. Jeśli planujesz przeniesienie usług AFS (udziałów plików platformy Azure), serwerów SQL lub serwerów SAP HANA, musisz je również wyrejestrować. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **Infrastruktura kopii zapasowych.** Zobacz, jak [wyrejestrować](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)serwer SQL, wyrejestrować konto magazynu skojarzone z udziałami plików platformy [Azure](manage-afs-backup.md#unregister-a-storage-account)i [wyrejestrować SAP HANA wystąpienia .](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

1. Po usunięciu ich z magazynu GRS kontynuuj konfigurowanie kopii zapasowych obciążenia w nowym magazynie LRS.

#### <a name="must-preserve-previous-backed-up-data"></a>Musi zachować poprzednie dane kopii zapasowej

Jeśli chcesz zachować bieżące chronione dane w magazynie GRS i kontynuować ochronę w nowym magazynie LRS, istnieją ograniczone opcje dla niektórych obciążeń:

- W przypadku usługi MARS można [zatrzymać ochronę z zachowaniem danych](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) i zarejestrować agenta w nowym magazynie LRS.

  - Azure Backup usługa będzie nadal zachowywać wszystkie istniejące punkty odzyskiwania magazynu GRS.
  - Aby zachować punkty odzyskiwania w magazynie GRS, musisz zapłacić.
  - Będzie można przywrócić dane kopii zapasowej tylko dla niewydajnych punktów odzyskiwania w magazynie GRS.
  - W magazynie LRS należy utworzyć nową replikę początkową danych.

- W przypadku maszyny wirtualnej platformy Azure możesz zatrzymać ochronę z zachowaniem danych dla maszyny wirtualnej w magazynie GRS, przenieść maszynę wirtualną do innej grupy zasobów, a następnie chronić maszynę wirtualną w magazynie LRS. [](backup-azure-manage-vms.md#stop-protecting-a-vm) Zobacz [wskazówki i ograniczenia dotyczące](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) przenoszenia maszyny wirtualnej do innej grupy zasobów.

  Maszyna wirtualna może być chroniona tylko w jednym magazynie na raz. Jednak maszyna wirtualna w nowej grupie zasobów może być chroniona w magazynie LRS, ponieważ jest uważana za inną maszynę wirtualną.

  - Azure Backup usługa zachowa punkty odzyskiwania, których kopię zapasową w magazynie GRS.
  - Aby zachować punkty odzyskiwania w magazynie GRS, musisz zapłacić (zobacz cennik usługi [Azure Backup,](azure-backup-pricing.md) aby uzyskać szczegółowe informacje).
  - W razie potrzeby będzie można przywrócić maszynę wirtualną z magazynu GRS.
  - Pierwszą kopią zapasową w magazynie LRS maszyny wirtualnej w nowym zasobie będzie replika początkowa.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny usługi Recovery Services.
[Dowiedz się więcej o](backup-azure-delete-vault.md) Usuń magazyny usługi Recovery Services.