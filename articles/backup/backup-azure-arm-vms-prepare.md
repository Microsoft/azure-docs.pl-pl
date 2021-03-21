---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services
description: Zawiera opis sposobu tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu Azure Backup
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: f6fe2f629742e15e62dfc13106e92623a4b45add
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172754"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie Recovery Services przy użyciu usługi [Azure Backup](backup-overview.md) .

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Przygotowywanie maszyn wirtualnych platformy Azure.
> * Utwórz magazyn.
> * Odnajdywanie maszyn wirtualnych i Konfigurowanie zasad tworzenia kopii zapasowych.
> * Włącz tworzenie kopii zapasowych dla maszyn wirtualnych platformy Azure.
> * Uruchom proces tworzenia początkowej kopii zapasowej.

> [!NOTE]
> W tym artykule opisano sposób konfigurowania magazynu i wybierania maszyn wirtualnych do utworzenia kopii zapasowej. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Alternatywnie można [utworzyć kopię zapasową pojedynczej maszyny wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z ustawień maszyny wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowych maszyny wirtualnej platformy Azure.
* [Dowiedz się więcej o](backup-azure-vms-introduction.md) Kopia zapasowa maszyny wirtualnej platformy Azure i rozszerzenie kopii zapasowej.
* Przed skonfigurowaniem kopii zapasowej [zapoznaj się z matrycą pomocy technicznej](backup-support-matrix-iaas.md) .

Ponadto istnieje kilka rzeczy, które mogą być konieczne w pewnych okolicznościach:

* **Zainstaluj agenta maszyny wirtualnej na maszynie wirtualnej**: Azure Backup tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na tym komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne [ręczne zainstalowanie agenta](#install-the-vm-agent).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie magazyny korzystają z [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zalecamy użycie GRS.
* Dla tańszej opcji można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) .
* [Magazyn strefowo nadmiarowy (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikuje dane w [strefach dostępności](../availability-zones/az-overview.md#availability-zones), gwarantując miejsce zamieszkania i odporność danych w tym samym regionie.

Zmodyfikuj typ replikacji magazynu w następujący sposób:

1. W nowym magazynie wybierz pozycję **Właściwości** w sekcji **Ustawienia** .
2. W obszarze **Właściwości** w obszarze **Konfiguracja kopii zapasowej** wybierz pozycję **Aktualizuj**.
3. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

      ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu magazynu i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, należy ponownie utworzyć magazyn.

## <a name="apply-a-backup-policy"></a>Stosowanie zasad kopii zapasowych

Skonfiguruj zasady tworzenia kopii zapasowych dla magazynu.

1. W magazynie wybierz pozycję **+ kopia zapasowa** w sekcji **Przegląd** .

   ![Przycisk Kopia zapasowa](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. W **celu utworzenia kopii zapasowej**,  >  **gdzie jest uruchomione Twoje obciążenie?** wybierz pozycję **Azure**. W **czym chcesz utworzyć kopię zapasową?** wybierz pozycję **maszyna wirtualna**  >   **OK**. Spowoduje to zarejestrowanie rozszerzenia maszyny wirtualnej w magazynie.

   ![Okienka celu tworzenia kopii zapasowych i tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. W obszarze **zasady tworzenia kopii zapasowych** wybierz zasady, które chcesz skojarzyć z magazynem.
    * Zasady domyślne tworzą kopię zapasową maszyny wirtualnej raz dziennie. Codzienne kopie zapasowe są przechowywane przez 30 dni. Migawki odzyskiwania natychmiastowego są przechowywane przez dwa dni.

      ![Domyślne zasady kopii zapasowych](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Jeśli nie chcesz używać zasad domyślnych, wybierz pozycję **Utwórz nową**, a następnie utwórz zasady niestandardowe zgodnie z opisem w następnej procedurze.

1. W obszarze **Virtual Machines** wybierz pozycję **Dodaj**.

      ![Dodaj maszyny wirtualne](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. Zostanie otwarte okienko **Wybieranie maszyn wirtualnych** . Wybierz Maszyny wirtualne, których kopię zapasową chcesz utworzyć przy użyciu zasad. Następnie wybierz przycisk **OK**.

   * Wybrane maszyny wirtualne zostały zweryfikowane.
   * Maszyny wirtualne można wybrać tylko w tym samym regionie, w którym znajduje się magazyn.
   * Kopie zapasowe maszyn wirtualnych można wykonywać tylko w pojedynczym magazynie.

     ![Okienko "Wybieranie maszyn wirtualnych"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Wszystkie maszyny wirtualne w tym samym regionie i w ramach subskrypcji w ramach magazynu są dostępne do skonfigurowania kopii zapasowej. Podczas konfigurowania kopii zapasowej można przejść do nazwy maszyny wirtualnej i jej grupy zasobów, nawet jeśli nie masz wymaganych uprawnień do tych maszyn wirtualnych. Jeśli maszyna wirtualna jest w stanie nietrwałego usuwania, nie będzie widoczna na tej liście. Jeśli musisz ponownie włączyć ochronę maszyny wirtualnej, musisz poczekać, aż okres usuwania nietrwałego wygaśnie lub wycofał usunięcie maszyny wirtualnej z listy nietrwałych usuniętych. Aby uzyskać więcej informacji, zobacz [artykuł usuwanie nietrwałego oprogramowania dla maszyn wirtualnych](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. W obszarze **kopia zapasowa** wybierz pozycję **Włącz kopię zapasową**. Spowoduje to wdrożenie zasad w magazynie i na maszynach wirtualnych, a następnie zainstalowanie rozszerzenia kopii zapasowej na agencie maszyny wirtualnej działającym na maszynie wirtualnej platformy Azure.

Po włączeniu kopii zapasowej:

* Usługa Backup instaluje rozszerzenie kopii zapasowej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona.
* Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem tworzenia kopii zapasowych.
* Gdy wykonywane są kopie zapasowe, należy pamiętać, że:
  * Uruchomiona maszyna wirtualna ma największą szansę na przechwycenie punktu odzyskiwania spójnego na poziomie aplikacji.
  * Jednak nawet jeśli maszyna wirtualna jest wyłączona, zostanie utworzona kopia zapasowa. Taka maszyna wirtualna jest nazywana maszyną wirtualną w trybie offline. W takim przypadku punkt odzyskiwania będzie spójny z awarią.
* Jawna łączność wychodząca nie jest wymagana, aby umożliwić tworzenie kopii zapasowych maszyn wirtualnych platformy Azure.

### <a name="create-a-custom-policy"></a>Tworzenie zasad niestandardowych

Jeśli wybrano opcję utworzenia nowych zasad tworzenia kopii zapasowych, Wypełnij ustawienia zasad.

1. W polu **Nazwa zasad** Określ zrozumiałą nazwę.
2. W polu **harmonogram tworzenia kopii zapasowych** Określ, kiedy mają być pobierane kopie zapasowe. Codzienne lub cotygodniowe wykonywanie kopii zapasowych maszyn wirtualnych platformy Azure.
3. W obszarze **natychmiastowe przywracanie** Określ, jak długo mają być przechowywane migawki lokalnie przywracane.
    * Podczas przywracania kopia zapasowa dysków maszyny wirtualnej jest kopiowana z magazynu w sieci do lokalizacji magazynu odzyskiwania. Za pomocą natychmiastowego przywracania można korzystać z lokalnie przechowywanych migawek wykonanych podczas zadania tworzenia kopii zapasowej, bez czekania na przesłanie danych kopii zapasowej do magazynu.
    * Migawki do natychmiastowego przywrócenia można zachować przez od 1 do pięciu dni. Ustawienie domyślne to dwa dni.
4. W obszarze **Zakres przechowywania** Określ, jak długo mają być przechowywane codziennie lub cotygodniowe punkty kopii zapasowych.
5. W obszarze **przechowywanie miesięcznego punktu kopii** zapasowej i **przechowywanie rocznego punktu kopii zapasowej** Określ, czy chcesz przechowywać kopię zapasową codziennie, czy cotygodniowe kopie zapasowe.
6. Wybierz **przycisk OK** , aby zapisać zasady.

    ![Nowe zasady tworzenia kopii zapasowych](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup nie obsługuje automatycznego dopasowywania zegara w przypadku zmian w ramach zmiany czasu dla kopii zapasowych maszyny wirtualnej platformy Azure. Po wystąpieniu zmian czasu należy ręcznie zmodyfikować zasady tworzenia kopii zapasowych zgodnie z potrzebami.

## <a name="trigger-the-initial-backup"></a>Wyzwalanie początkowej kopii zapasowej

Początkowa kopia zapasowa będzie uruchamiana zgodnie z harmonogramem, ale można ją uruchomić od razu w następujący sposób:

1. W menu magazyn wybierz pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej** wybierz pozycję **maszyna wirtualna platformy Azure**.
3. Na liście **elementy kopii zapasowej** wybierz wielokropek (...).
4. Wybierz pozycję **Utwórz kopię zapasową teraz**.
5. W obszarze **kopia zapasowa** Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie wybierz przycisk **OK**.
6. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej**  >  **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.

## <a name="verify-backup-job-status"></a>Sprawdź stan zadania tworzenia kopii zapasowej

Szczegóły zadania kopii zapasowej dla każdej kopii zapasowej maszyny wirtualnej składają się z dwóch faz, fazy **migawki** i fazy **transferu danych do magazynu** .<br/>
Faza migawki gwarantuje dostępność punktu odzyskiwania przechowywanego wraz z dyskami do **natychmiastowego przywrócenia** i jest dostępna przez maksymalnie pięć dni w zależności od przechowywania migawek skonfigurowanego przez użytkownika. Transfer danych do magazynu tworzy punkt odzyskiwania w magazynie do długoterminowego przechowywania. Transfer danych do magazynu rozpoczyna się dopiero po zakończeniu fazy migawki.

  ![Stan zadania tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Istnieją dwa **podzadania** uruchomione w zapleczu — jeden dla zadania tworzenia kopii zapasowej frontonu, który można sprawdzić w okienku szczegółów **zadania tworzenia kopii zapasowej** w następujący sposób:

  ![Zadanie tworzenia kopii zapasowej stanu zadania podrzędnego](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Zakończenie fazy **transferu danych do magazynu** może zająć wiele dni w zależności od rozmiaru dysków, liczby zmian na dysk i kilku innych czynników.

Stan zadania może się różnić w zależności od następujących scenariuszy:

**Zdjęcie** | **Transferowanie danych do magazynu** | **Stan zadania**
--- | --- | ---
Ukończone | W toku | W toku
Ukończone | Pominięto | Ukończone
Ukończone | Ukończone | Ukończone
Ukończone | Niepowodzenie | Ukończono z ostrzeżeniem
Niepowodzenie | Niepowodzenie | Niepowodzenie

Teraz dzięki tej możliwości dla tej samej maszyny wirtualnej dwa kopie zapasowe mogą działać równolegle, ale w każdej fazie (migawka, transfer danych do magazynu) można uruchomić tylko jedno zadanie podrzędne. Tak więc w scenariuszach, w których zadanie tworzenia kopii zapasowej w toku spowodowało niepowodzenie tworzenia kopii zapasowej w następnym dniu, zostanie ono wyeliminowane w tej funkcji. Kopie zapasowe kolejnych dni mogą mieć ukończoną migawkę **, podczas gdy** zadanie tworzenia kopii zapasowej w poprzednim dniu jest pomijane.
Przyrostowy punkt odzyskiwania utworzony w magazynie będzie przechwytywać wszystkie zmiany z najnowszego punktu odzyskiwania utworzonego w magazynie. Użytkownik nie ma wpływu na koszty.

## <a name="optional-steps"></a>Kroki opcjonalne

### <a name="install-the-vm-agent"></a>Instalowanie agenta maszyny wirtualnej

Azure Backup tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Jeśli maszyna wirtualna została utworzona na podstawie obrazu portalu Azure Marketplace, Agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę wirtualną lub migrujesz maszynę lokalną, może być konieczne ręczne zainstalowanie agenta, zgodnie z podsumowaniem w tabeli.

**VM** | **Szczegóły**
--- | ---
**Windows** | 1. [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) plik msi agenta.<br/><br/> 2. Zainstaluj program z uprawnieniami administratora na komputerze.<br/><br/> 3. Sprawdź instalację. W *C:\WindowsAzure\Packages* na maszynie wirtualnej kliknij prawym przyciskiem **** myszy pozycję  >  **Właściwości** WaAppAgent.exe. Na karcie **szczegóły** **Wersja produktu** powinna mieć wartość 2.6.1198.718 lub wyższą.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, a [następnie ponownie zainstaluj agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Zainstaluj program przy użyciu KCO lub pakietu DEB z repozytorium pakietu dystrybucji. Jest to preferowana metoda instalowania i uaktualniania agenta systemu Linux platformy Azure. Wszyscy [pozatwierdzeni dostawcy dystrybucji](../virtual-machines/linux/endorsed-distros.md) integrują pakiet agenta platformy Azure z systemem Linux z obrazami i repozytoriami. Agent jest dostępny w serwisie [GitHub](https://github.com/Azure/WALinuxAgent), ale nie zalecamy instalacji.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, że nie są uruchomione żadne operacje tworzenia kopii zapasowej, i zaktualizuj pliki binarne.

## <a name="next-steps"></a>Następne kroki

* Rozwiązywanie problemów z [agentami maszyn wirtualnych platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopiami zapasowymi maszyny wirtualnej platformy Azure](backup-azure-vms-troubleshoot.md).
* [Przywróć](backup-azure-arm-restore-vms.md) Maszyny wirtualne platformy Azure.