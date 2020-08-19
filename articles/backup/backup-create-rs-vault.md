---
title: Tworzenie i konfigurowanie magazynów Recovery Services
description: W tym artykule dowiesz się, jak tworzyć i konfigurować magazyny Recovery Services, w których są przechowywane kopie zapasowe i punkty odzyskiwania.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 7084fb9b599e127fac2b8c75748448d37d3f5365
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586192"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Tworzenie i Konfigurowanie magazynu Recovery Services

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Ustawianie nadmiarowości magazynu

Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania magazynu.

> [!NOTE]
> Przed skonfigurowaniem kopii zapasowych w magazynie należy zmienić **Typ replikacji magazynu** (lokalnie nadmiarowy lub geograficznie nadmiarowy) dla magazynu usługi Recovery Services. Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona.
>
>- Jeśli nie skonfigurowano jeszcze kopii zapasowej, [wykonaj następujące kroki](#set-storage-redundancy) , aby przejrzeć i zmodyfikować ustawienia.
>- Jeśli kopia zapasowa została już skonfigurowana i musi zostać przeniesiona z GRS do LRS, [zapoznaj się z tymi obejściami](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. W okienku **Recovery Services magazynów** wybierz nowy magazyn. W sekcji **Ustawienia** wybierz pozycję  **Właściwości**.
1. W obszarze **Właściwości**w obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Zalecamy, aby Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj, aby użyć domyślnego ustawienia **geograficznie nadmiarowego** .
   - Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
   - Dowiedz się więcej o nadmiarowości [geograficznym](../storage/common/storage-redundancy.md) i [lokalnym](../storage/common/storage-redundancy.md) .

>[!NOTE]
>Ustawienia replikacji magazynu dla magazynu nie są odpowiednie dla kopii zapasowej udziału plików platformy Azure, ponieważ bieżące rozwiązanie jest oparte na migawce i nie przeniesiono danych do magazynu. Migawki są przechowywane na tym samym koncie magazynu co kopia zapasowa udziału plików.

## <a name="set-cross-region-restore"></a>Ustaw przywracanie między regionami

Jako jedna z opcji przywracania, przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [sparowanym regionem platformy Azure](../best-practices-availability-paired-regions.md). Ta opcja umożliwia:

- Przeprowadzaj drążenie w przypadku spełnienia wymagań dotyczących inspekcji lub zgodności
- Przywróć maszynę wirtualną lub jej dysk w przypadku awarii w regionie podstawowym.

Aby wybrać tę funkcję, wybierz pozycję **Włącz przywracanie między regionami** w okienku **Konfiguracja kopii zapasowej** .

W przypadku tego procesu istnieją wpływ na ceny, w jakim znajduje się na poziomie magazynu.

>[!NOTE]
>Przed rozpoczęciem:
>
>- Zapoznaj się z [matrycą pomocy technicznej](backup-support-matrix.md#cross-region-restore) , aby zapoznać się z listą obsługiwanych typów i regionów zarządzanych.
>- Funkcja przywracania między regionami (CRR) jest teraz wyświetlana we wszystkich publicznych regionach platformy Azure.
>- CRR to funkcja wyboru poziomu magazynu dla dowolnego magazynu GRS (domyślnie wyłączona).
>- Po przypisaniu elementów kopii zapasowych w regionach pomocniczych może upłynąć do 48 godzin.
>- Obecnie CRR jest obsługiwana tylko dla typu zarządzania kopiami zapasowymi — ARM Azure VM (klasyczna maszyna wirtualna platformy Azure nie będzie obsługiwana).  Gdy dodatkowe typy zarządzania obsługują CRR, zostaną one **automatycznie** zarejestrowane.
>- Nie można obecnie przywrócić operacji przywracania między regionami do GRS lub LRS po zainicjowaniu ochrony po raz pierwszy.

### <a name="configure-cross-region-restore"></a>Konfigurowanie przywracania między regionami

Magazyn utworzony za pomocą nadmiarowości GRS zawiera opcję konfigurowania funkcji przywracania między regionami. Każdy magazyn GRS będzie miał baner, który zostanie połączony z dokumentacją. Aby skonfigurować CRR dla magazynu, przejdź do okienka Konfiguracja kopii zapasowej zawierającego opcję włączenia tej funkcji.

 ![Transparent konfiguracji kopii zapasowej](./media/backup-azure-arm-restore-vms/banner.png)

1. W portalu przejdź do obszaru Recovery Services ustawienia > magazynu > właściwości.
2. Aby włączyć tę funkcję, wybierz opcję **Włącz przywracanie między regionami w tym magazynie** .

   ![Przed wybraniem opcji Włącz przywracanie między regionami w tym magazynie](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Po wybraniu opcji Włącz przywracanie między regionami w tym magazynie](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Dowiedz się [, jak wyświetlać elementy kopii zapasowej w regionie pomocniczym](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Dowiedz się [, jak przywrócić w regionie pomocniczym](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Dowiedz się, jak [monitorować zadania przywracania regionu pomocniczego](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="set-encryption-settings"></a>Ustawianie ustawień szyfrowania

Domyślnie dane w magazynie Recovery Services są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Z punktu końcowego nie są wymagane żadne jawne akcje umożliwiające włączenie tego szyfrowania i ma zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.  Możesz wybrać opcję przydzielenia własnego klucza do szyfrowania danych kopii zapasowej w tym magazynie. Jest to nazywane kluczami zarządzanymi przez klienta. Jeśli chcesz zaszyfrować dane kopii zapasowej przy użyciu własnego klucza, należy określić klucz szyfrowania, aby wszystkie elementy były chronione do tego magazynu. Po włączeniu szyfrowania przy użyciu klucza nie można go cofnąć.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurowanie magazynu do szyfrowania przy użyciu kluczy zarządzanych przez klienta

Aby skonfigurować magazyn do szyfrowania przy użyciu kluczy zarządzanych przez klienta, należy postępować zgodnie z następującą kolejnością:

1. Włącz zarządzaną tożsamość magazynu Recovery Services

1. Przypisz uprawnienia do magazynu, aby uzyskać dostęp do klucza szyfrowania w Azure Key Vault

1. Włącz ochronę przed usuwaniem i przeczyszczaniem na Azure Key Vault

1. Przypisywanie klucza szyfrowania do magazynu Recovery Services

Instrukcje dotyczące każdego z tych kroków można znaleźć [w tym artykule](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Modyfikowanie ustawień domyślnych

Zdecydowanie zalecamy przejrzenie ustawień domyślnych dla **typu replikacji magazynu** i **ustawień zabezpieczeń** przed skonfigurowaniem kopii zapasowych w magazynie.

- **Typ replikacji magazynu** jest domyślnie ustawiony na **Geograficznie nadmiarowy** (GRS). Po skonfigurowaniu kopii zapasowej opcja Modyfikuj jest wyłączona.
  - Jeśli nie skonfigurowano jeszcze kopii zapasowej, [wykonaj następujące kroki](#set-storage-redundancy) , aby przejrzeć i zmodyfikować ustawienia.
  - Jeśli kopia zapasowa została już skonfigurowana i musi zostać przeniesiona z GRS do LRS, [zapoznaj się z tymi obejściami](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- Funkcja **usuwania nietrwałego** jest **włączana** domyślnie dla nowo utworzonych magazynów w celu ochrony danych kopii zapasowej przed przypadkowym lub złośliwym usunięciem. [Wykonaj następujące kroki](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) , aby przejrzeć i zmodyfikować ustawienia.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Jak zmienić z GRS na LRS po skonfigurowaniu kopii zapasowej

Przed podjęciem decyzji o przejściu z GRS na magazyn lokalnie nadmiarowy (LRS) zapoznaj się z różnicami między niższym kosztem i wyższą trwałością danych, które pasują do Twojego scenariusza. Jeśli musisz przejść z GRS do LRS, będziesz mieć dwie możliwości. Są one zależne od wymagań firmy, aby zachować dane kopii zapasowej:

- [Nie trzeba zachować poprzedniej kopii zapasowej danych](#dont-need-to-preserve-previous-backed-up-data)
- [Należy zachować poprzednie dane kopii zapasowej](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nie trzeba zachować poprzedniej kopii zapasowej danych

Aby chronić obciążenia w nowym magazynie LRS, należy usunąć bieżącą ochronę i dane w magazynie GRS i ponownie skonfigurować kopie zapasowe.

>[!WARNING]
>Następująca operacja jest destrukcyjne i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

Zatrzymaj i Usuń bieżącą ochronę magazynu GRS:

1. Wyłącz usuwanie nietrwałe we właściwościach magazynu GRS. Wykonaj następujące [kroki](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) , aby wyłączyć usuwanie nietrwałe.

1. Zatrzymaj ochronę i usuń kopie zapasowe z istniejącego magazynu GRS. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **elementy kopii zapasowej**. Elementy wymienione w tym miejscu, które muszą zostać przeniesione do magazynu LRS, muszą zostać usunięte wraz z danymi kopii zapasowych. Zobacz jak [usunąć chronione elementy w chmurze](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) i [usunąć chronione elementy lokalnie](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Jeśli planujesz przenoszenie programu AFS (udziały plików platformy Azure), serwerów SQL lub serwerów SAP HANA, należy również je wyrejestrować. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **infrastruktura zapasowa**. Zobacz jak [wyrejestrować serwer SQL](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [wyrejestrować konto magazynu skojarzone z udziałami plików platformy Azure](manage-afs-backup.md#unregister-a-storage-account)i [wyrejestrować wystąpienie SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Po usunięciu z magazynu GRS, kontynuuj konfigurowanie kopii zapasowych dla obciążenia w nowym magazynie LRS.

#### <a name="must-preserve-previous-backed-up-data"></a>Należy zachować poprzednie dane kopii zapasowej

Jeśli potrzebujesz zachować bieżące chronione dane w magazynie GRS i kontynuować ochronę w nowym magazynie LRS, dostępne są ograniczone opcje dla niektórych obciążeń:

- W przypadku usługi MARS można [zatrzymać ochronę z zachowaniem zachowanych danych](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) i zarejestrować agenta w nowym magazynie LRS.

  - Usługa Azure Backup będzie nadal utrzymywać wszystkie istniejące punkty odzyskiwania magazynu GRS.
  - Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w magazynie GRS.
  - Będzie można przywrócić dane z kopii zapasowej tylko dla niewygasłych punktów odzyskiwania w magazynie GRS.
  - Należy utworzyć nową początkową replikę danych w magazynie LRS.

- W przypadku maszyny wirtualnej platformy Azure można [zatrzymać ochronę z zachowaniem Zachowaj dane](backup-azure-manage-vms.md#stop-protecting-a-vm) dla maszyny wirtualnej w magazynie GRS, przenieść maszynę wirtualną do innej grupy zasobów, a następnie chronić maszynę wirtualną w magazynie LRS. Zapoznaj się ze [wskazówkami i ograniczeniami](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) dotyczącymi przeniesienia maszyny wirtualnej do innej grupy zasobów.

  Maszyna wirtualna może być chroniona tylko w jednym magazynie naraz. Jednak maszyna wirtualna w nowej grupie zasobów może być chroniona w magazynie LRS, ponieważ jest traktowana jako inna maszyna wirtualna.

  - Usługa Azure Backup zachowa te punkty odzyskiwania, których kopia zapasowa została utworzona w magazynie GRS.
  - Musisz zanieść opłaty za przechowywanie punktów odzyskiwania w magazynie GRS (zobacz [cennik Azure Backup](azure-backup-pricing.md) , aby uzyskać szczegółowe informacje).
  - W razie potrzeby będzie można przywrócić maszynę wirtualną z magazynu GRS.
  - Pierwsza kopia zapasowa w magazynie LRS maszyny wirtualnej w nowym zasobie będzie repliką początkową.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](backup-azure-recovery-services-vault-overview.md) Magazyny Recovery Services.
[Dowiedz się więcej o](backup-azure-delete-vault.md) Usuń magazyny Recovery Services.
