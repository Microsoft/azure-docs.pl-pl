---
title: Wykonywanie akcji przy użyciu centrum kopii zapasowych
description: W tym artykule wyjaśniono, jak wykonywać akcje przy użyciu centrum kopii zapasowych
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506494"
---
# <a name="perform-actions-using-backup-center"></a>Wykonywanie akcji przy użyciu centrum kopii zapasowych

Centrum kopii zapasowych umożliwia wykonywanie akcji związanych z tworzeniem kopii zapasowej z poziomu centralnego interfejsu bez konieczności nawigowania do pojedynczego magazynu. Niektóre akcje, które można wykonać z poziomu centrum kopii zapasowych, to:

* Konfigurowanie kopii zapasowej dla źródeł danych
* Przywracanie wystąpienia kopii zapasowej
* Tworzenie nowego magazynu
* Utwórz nowe zasady kopii zapasowych
* Wyzwalanie kopii zapasowej na żądanie dla wystąpienia kopii zapasowej
* Zatrzymaj kopię zapasową wystąpienia kopii zapasowej

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Centrum kopii zapasowych jest obecnie obsługiwane w przypadku kopii zapasowych maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure, SAP HANA w ramach kopii zapasowej maszyny wirtualnej platformy Azure, Azure Files Azure Database for PostgreSQL tworzenia
* Zapoznaj się z [matrycą pomocy technicznej](backup-center-support-matrix.md) , aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy.

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

W przypadku tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, SQL na maszynach wirtualnych platformy Azure, SAP HANA na maszynach wirtualnych platformy Azure lub Azure Files, należy użyć magazynu Recovery Services. W przypadku tworzenia kopii zapasowych baz danych platformy Azure dla serwera PostgreSQL należy użyć magazynu kopii zapasowych. 

W zależności od typu źródła danych, dla którego chcesz utworzyć kopię zapasową, postępuj zgodnie z odpowiednimi instrukcjami opisanymi poniżej.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Konfigurowanie kopii zapasowej w magazynie Recovery Services

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **+ kopia zapasowa** w górnej części karty **Przegląd** .

    ![Omówienie centrum kopii zapasowych](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Wybierz typ źródła danych, dla którego chcesz utworzyć kopię zapasową.

    ![Wybierz źródło danych, aby skonfigurować kopię zapasową maszyny wirtualnej](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Wybierz magazyn Recovery Services a następnie wybierz pozycję **Zastosuj**. Prowadzi to do pracy z konfiguracją kopii zapasowej, która jest identyczna z tą, która jest dostępna w magazynie Recovery Services. [Dowiedz się więcej na temat konfigurowania kopii zapasowych dla maszyn wirtualnych platformy Azure z magazynem Recovery Services](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Konfigurowanie kopii zapasowej w magazynie kopii zapasowych

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **+ kopia zapasowa** w górnej części karty **Przegląd** .
2. Wybierz typ źródła danych, dla którego chcesz utworzyć kopię zapasową (w tym przypadku Azure Database for PostgreSQL serwera).

    ![Wybierz źródło danych, aby skonfigurować Azure Database for PostgreSQL kopii zapasowej serwera](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Wybierz pozycję **Zastosuj**. Prowadzi to do konfiguracji kopii zapasowej, która jest identyczna z tą, która jest dostępna z magazynu kopii zapasowych. [Dowiedz się więcej o sposobie konfigurowania kopii zapasowej dla serwera Azure Database for PostgreSQL z magazynem kopii zapasowych](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Przywracanie wystąpienia kopii zapasowej

W zależności od typu źródła danych, które chcesz przywrócić, postępuj zgodnie z odpowiednimi instrukcjami opisanymi poniżej.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Jeśli przywracasz z magazynu Recovery Servicesowego

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **Przywróć** w górnej części karty **Przegląd** .

    ![Omówienie centrum kopii zapasowych w celu przywrócenia maszyny wirtualnej](./media/backup-center-actions/backup-center-overview-restore.png)

2. Wybierz typ źródła danych, które chcesz przywrócić.

    ![Wybieranie źródła danych na potrzeby przywracania maszyny wirtualnej](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Wybierz wystąpienie kopii zapasowej i wybierz pozycję **Wykonaj**. Prowadzi to do funkcji przywracania ustawień, która jest taka sama jak ta, która jest dostępna w magazynie Recovery Services. [Dowiedz się więcej o tym, jak przywrócić maszynę wirtualną platformy Azure z magazynem Recovery Services](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>W przypadku przywracania z magazynu kopii zapasowych

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **Przywróć** w górnej części karty **Przegląd** .
2. Wybierz typ źródła danych, które chcesz przywrócić (w tym przypadku Azure Database for PostgreSQL serwera).

    ![Wybierz źródło danych dla przywracania serwera Azure Database for PostgreSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Wybierz wystąpienie kopii zapasowej i wybierz pozycję **Wykonaj**. Prowadzi to do funkcji przywracania ustawień, która jest taka sama jak ta, która jest dostępna w magazynie Recovery Services. [Dowiedz się więcej o sposobie przywracania serwera Azure Database for PostgreSQL z magazynem kopii zapasowych](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Tworzenie nowego magazynu

Nowy magazyn można utworzyć, przechodząc do centrum kopii zapasowych i wybierając pozycję **+ Magazyn** w górnej części karty **Przegląd** .

![Utwórz magazyn](./media/backup-center-actions/backup-center-create-vault.png)

* [Dowiedz się więcej o tworzeniu magazynu usługi Recovery Services](backup-create-rs-vault.md)
* [Dowiedz się więcej o tworzeniu magazynu kopii zapasowych](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Utwórz nowe zasady kopii zapasowych

W zależności od typu źródła danych, dla którego chcesz utworzyć kopię zapasową, postępuj zgodnie z odpowiednimi instrukcjami opisanymi poniżej.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Jeśli tworzysz kopię zapasową w magazynie Recovery Services

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **+ zasady** w górnej części karty **Przegląd** .

    ![Omówienie centrum kopii zapasowych dla zasad tworzenia kopii zapasowych](./media/backup-center-actions/backup-center-overview-policy.png)

2. Wybierz typ źródła danych, dla którego chcesz utworzyć kopię zapasową.

    ![Wybierz źródło danych dla zasad tworzenia kopii zapasowej maszyny wirtualnej](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Wybierz magazyn usługi Recovery Services i wybierz pozycję **Zastosuj**. Prowadzi to do funkcji tworzenia zasad, która jest taka sama jak ta, która jest dostępna w magazynie Recovery Services. [Dowiedz się więcej na temat tworzenia nowych zasad tworzenia kopii zapasowych dla maszyny wirtualnej platformy Azure przy użyciu magazynu usługi Recovery Services](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>W przypadku tworzenia kopii zapasowej w magazynie kopii zapasowych

1. Przejdź do centrum kopii zapasowych i wybierz pozycję **+ zasady** w górnej części karty **Przegląd** .
2. Wybierz typ źródła danych, dla którego chcesz utworzyć kopię zapasową (w tym przypadku Azure Database for PostgreSQL serwera).

    ![Wybierz źródło danych dla zasad tworzenia kopii zapasowej serwera Azure Database for PostgreSQL](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Wybierz pozycję **Zastosuj**. Prowadzi to do środowiska tworzenia zasad, które jest identyczne z tymi, które są dostępne w magazynie kopii zapasowych. [Dowiedz się więcej na temat tworzenia nowych zasad tworzenia kopii zapasowych z magazynem kopii zapasowych](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Wykonywanie kopii zapasowej na żądanie dla wystąpienia kopii zapasowej

Centrum kopii zapasowych umożliwia wyszukiwanie wystąpień kopii zapasowych w ramach tworzenia kopii zapasowych i wykonywanie operacji tworzenia kopii zapasowej na żądanie.

Aby wyzwolić kopię zapasową na żądanie, przejdź do centrum kopii zapasowych i wybierz element menu **wystąpienia kopii zapasowej** . Wybranie tej opcji umożliwia wyświetlenie szczegółów wszystkich wystąpień kopii zapasowych, do których masz dostęp. Możesz wyszukać wystąpienie kopii zapasowej, dla którego chcesz utworzyć kopię zapasową. Kliknięcie prawym przyciskiem myszy elementu w siatce spowoduje otwarcie listy dostępnych akcji. Wybierz opcję **Utwórz kopię zapasową teraz** , aby wykonać kopię zapasową na żądanie.

![Kopia zapasowa na żądanie](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Dowiedz się więcej o wykonywaniu kopii zapasowych na żądanie dla Virtual Machines platformy Azure](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Dowiedz się więcej o wykonywaniu kopii zapasowych na żądanie dla Azure Database for PostgreSQL Server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Zatrzymaj kopię zapasową wystąpienia kopii zapasowej

Istnieją scenariusze, które mogą być potrzebne do zatrzymania tworzenia kopii zapasowej wystąpienia kopii zapasowej, na przykład, gdy nie istnieje kopia zapasowa bazowego zasobu.

Aby wyzwolić kopię zapasową na żądanie, przejdź do centrum kopii zapasowych i wybierz element menu **wystąpienia kopii zapasowej** . Wybranie tej opcji umożliwia wyświetlenie szczegółów wszystkich wystąpień kopii zapasowych, do których masz dostęp. Możesz wyszukać wystąpienie kopii zapasowej, dla którego chcesz utworzyć kopię zapasową. Kliknięcie prawym przyciskiem myszy elementu w siatce spowoduje otwarcie listy dostępnych akcji. Wybierz opcję **Zatrzymaj tworzenie kopii** zapasowej, aby zatrzymać tworzenie kopii zapasowej wystąpienia kopii zapasowej.

![Zatrzymywanie ochrony](./media/backup-center-actions/backup-center-stop-protection.png)

[Dowiedz się więcej na temat zatrzymywania tworzenia kopii zapasowej dla usługi Azure Virtual Machines](backup-azure-manage-vms.md#stop-protecting-a-vm)

[Dowiedz się więcej na temat zatrzymywania tworzenia kopii zapasowej serwera Azure Database for PostgreSQL](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i wykonywanie kopii zapasowych](backup-center-monitor-operate.md)
* [Zarządzanie brakiem kopii zapasowej](backup-center-govern-environment.md)
* [Uzyskaj wgląd w kopie zapasowe](backup-center-obtain-insights.md)
