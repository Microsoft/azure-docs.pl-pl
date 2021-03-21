---
title: Tworzenie kopii zapasowych maszyn z systemem Windows przy użyciu agenta MARS
description: Użyj agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702772"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Tworzenie kopii zapasowych plików i folderów systemu Windows Server na platformie Azure

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn z systemem Windows przy użyciu usługi [Azure Backup](backup-overview.md) i agenta Microsoft Azure Recovery Services (MARS). Usługa MARS jest również znana jako agent Azure Backup.

Ten artykuł obejmuje następujące zagadnienia:

> [!div class="checklist"]
>
> * Sprawdzenie wymagań wstępnych
> * Utwórz zasady i harmonogram tworzenia kopii zapasowych.
> * Wykonaj kopię zapasową na żądanie.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się [, jak Azure Backup używa agenta Mars do tworzenia kopii zapasowych maszyn z systemem Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Dowiedz się więcej o [architekturze tworzenia kopii zapasowych](backup-architecture.md#architecture-back-up-to-dpmmabs) , w której jest uruchomiony agent Mars na pomocniczym serwerze serwera usługi mab lub Data Protection Manager.
* Zapoznaj się z [obsługiwanymi informacjami i możliwościami tworzenia kopii zapasowych](backup-support-matrix-mars-agent.md) przez agenta Mars.
* [Sprawdź dostęp do Internetu](install-mars-agent.md#verify-internet-access) na maszynach, dla których chcesz utworzyć kopię zapasową.
* Jeśli Agent MARS nie jest zainstalowany, Dowiedz się, jak go zainstalować w [tym miejscu](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy należy wykonać migawki danych w celu utworzenia punktów odzyskiwania. Określa również, jak długo mają być przechowywane punkty odzyskiwania. Agent MARS służy do konfigurowania zasad tworzenia kopii zapasowych.

Azure Backup nie zajmie czasu letniego (DST) na konto. Ta wartość domyślna może spowodować niezgodność między rzeczywistym czasem a zaplanowaną godziną wykonywania kopii zapasowej.

Aby utworzyć nowe zasady kopii zapasowych:

1. Po pobraniu i zarejestrowaniu agenta MARS Otwórz konsolę agenta. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.  

1. W obszarze **Akcje** wybierz pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. W Kreatorze harmonogramu tworzenia kopii zapasowej wybierz pozycję **Rozpocznij**  >  **dalej**.
1. W obszarze **Wybierz elementy do utworzenia kopii zapasowej** wybierz pozycję **Dodaj elementy**.

    ![Dodaj elementy do kopii zapasowej](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. W polu **Wybierz elementy** wybierz elementy do utworzenia kopii zapasowej, a następnie wybierz przycisk **OK**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na stronie **Wybierz elementy do utworzenia kopii zapasowej** wybierz pozycję **dalej**.
1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych** Określ, kiedy mają być wykonywane codziennie lub cotygodniowe kopie zapasowe. Następnie wybierz przycisk **Dalej**.

    * Punkt odzyskiwania jest tworzony podczas tworzenia kopii zapasowej.
    * Liczba punktów odzyskiwania utworzonych w danym środowisku zależy od harmonogramu tworzenia kopii zapasowych.
    * Możesz zaplanować do trzech codziennych kopii zapasowych dziennie. W poniższym przykładzie występują dwa codzienne kopie zapasowe, jeden o północy i jeden o godzinie 6:00 PM.

        ![Skonfiguruj dzienny harmonogram tworzenia kopii zapasowych](./media/backup-configure-vault/day-schedule.png)

    * Można uruchamiać cotygodniowe kopie zapasowe. W poniższym przykładzie kopie zapasowe są wykonywane co alternatywa Niedziela i środa o 9:30 AM i 1:00 AM.

        ![Konfigurowanie tygodniowego harmonogramu tworzenia kopii zapasowych](./media/backup-configure-vault/week-schedule.png)

1. Na stronie **Wybierz zasady przechowywania** Określ, w jaki sposób mają być przechowywane historyczne kopie danych. Następnie wybierz przycisk **Dalej**.

    * Ustawienia przechowywania określają, które punkty odzyskiwania mają być przechowywane i jak długo mają być przechowywane.
    * W przypadku ustawienia przechowywania dziennego należy wskazać, że w określonym czasie przechowywania najnowszy punkt odzyskiwania zostanie zachowany przez określoną liczbę dni. Można też określić miesięczne zasady przechowywania, aby wskazać, że punkt odzyskiwania utworzony na 30. każdy miesiąc powinien być przechowywany przez 12 miesięcy.
    * Przechowywanie codziennie i cotygodniowe punkty odzyskiwania zwykle pokrywają się z harmonogramem tworzenia kopii zapasowych. W związku z tym, gdy harmonogram wyzwala kopię zapasową, punkt odzyskiwania tworzony przez kopię zapasową jest przechowywany przez czas, który określa dzienne lub cotygodniowe zasady przechowywania.
    * W poniższym przykładzie:

        * Codzienne kopie zapasowe o północy i 6:00 PM są przechowywane przez siedem dni.
        * Kopie zapasowe wykonane w sobotę o północy i 6:00 PM są przechowywane przez cztery tygodnie.
        * Kopie zapasowe wykonane w ostatniej sobotę miesiąca o północy i 6:00 PM są przechowywane przez 12 miesięcy.
        * Kopie zapasowe wykonane w ostatniej sobotę marca są przechowywane przez 10 lat.

        ![Przykład zasad przechowywania](./media/backup-configure-vault/retention-example.png)

1. Na stronie **Wybierz typ początkowej kopii zapasowej** Zdecyduj, czy chcesz pobrać początkową kopię zapasową za pośrednictwem sieci, czy użyć kopii zapasowej offline. Aby pobrać początkową kopię zapasową za pośrednictwem sieci, wybierz pozycję **automatycznie za pośrednictwem sieci**  >  .

    Aby uzyskać więcej informacji na temat kopii zapasowej offline, zobacz [używanie Azure Data Box do tworzenia kopii zapasowych w trybie offline](offline-backup-azure-data-box.md).

    ![Wybierz typ początkowej kopii zapasowej](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na stronie **potwierdzenie** Przejrzyj informacje, a następnie wybierz pozycję **Zakończ**.

    ![Potwierdź typ kopii zapasowej](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Po zakończeniu tworzenia harmonogramu tworzenia kopii zapasowych przez kreatora wybierz pozycję **Zamknij**.

    ![Wyświetlanie postępu harmonogramu kopii zapasowych](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Utwórz zasady na każdym komputerze, na którym zainstalowano agenta.

### <a name="do-the-initial-backup-offline"></a>Wykonywanie początkowej kopii zapasowej w trybie offline

Początkową kopię zapasową można uruchomić automatycznie za pośrednictwem sieci lub można utworzyć kopię zapasową w trybie offline. Rozmieszczanie w trybie offline dla początkowej kopii zapasowej jest przydatne, jeśli masz duże ilości danych, które będą wymagały przetransferowania dużej przepustowości sieci.

Aby wykonać transfer w trybie offline:

1. Zapisz dane kopii zapasowej w lokalizacji tymczasowej.
1. Użyj narzędzia AzureOfflineBackupDiskPrep, aby skopiować dane z lokalizacji tymczasowej do co najmniej jednego dysku SATA.

    Narzędzie tworzy zadanie importu platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Import/Export](../import-export/storage-import-export-service.md).
1. Wyślij dyski SATA do centrum danych platformy Azure.

    W centrum danych dane dysku są kopiowane na konto usługi Azure Storage. Azure Backup kopiuje dane z konta magazynu do magazynu, a przyrostowe kopie zapasowe są zaplanowane.

Aby uzyskać więcej informacji na temat umieszczania w trybie offline, zobacz [używanie Azure Data Box do tworzenia kopii zapasowych w trybie offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Włącz ograniczanie sieci

Można kontrolować sposób, w jaki Agent MARS używa przepustowości sieci przez włączenie ograniczania przepustowości sieci. Ograniczanie przepustowości jest przydatne, jeśli trzeba utworzyć kopię zapasową danych w godzinach pracy, ale chcesz kontrolować, ile przepustowości używa działanie tworzenia kopii zapasowej i przywracania.

Funkcja ograniczania sieci w Azure Backup używa [Quality of Service (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) w lokalnym systemie operacyjnym.

Ograniczanie sieci dla kopii zapasowych jest dostępne w systemie Windows Server 2012 lub nowszym oraz w systemie Windows 8 i nowszych. Systemy operacyjne powinny mieć zainstalowane najnowsze dodatki Service Pack.

Aby włączyć ograniczanie sieci:

1. W agencie MARS wybierz pozycję **Zmień właściwości**.
1. Na karcie **ograniczanie** wybierz pozycję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**.

    ![Konfigurowanie ograniczania sieci dla operacji tworzenia kopii zapasowych](./media/backup-configure-vault/throttling-dialog.png)
1. Określ dozwoloną przepustowość w godzinach pracy i godzinach wolnych. Wartości przepustowości zaczynają się od 512 KB/s i mogą trafiać do 1 023 MB/s. Następnie wybierz przycisk **OK**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

1. W agencie MARS wybierz pozycję **Wykonaj kopię zapasową teraz**.

    ![Utwórz kopię zapasową teraz w systemie Windows Server](./media/backup-configure-vault/backup-now.png)

1. Jeśli Agent MARS ma wersję 2.0.9169.0 lub nowszą, można ustawić niestandardową datę przechowywania. W sekcji **Zachowaj kopię zapasową** do wybierz datę z kalendarza.

   ![Dostosowywanie daty przechowywania przy użyciu kalendarza](./media/backup-configure-vault/mars-ondemand.png)

1. Na stronie **potwierdzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz kopię zapasową**.
1. Wybierz pozycję **Zamknij** , aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem tworzenia kopii zapasowej, Kreator będzie nadal działać w tle.

Po zakończeniu początkowej kopii zapasowej w konsoli kopia zapasowa zostanie wyświetlony stan **ukończono zadanie** .

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Skonfiguruj zachowanie przechowywania zasad tworzenia kopii zapasowych na żądanie

> [!NOTE]
> Te informacje dotyczą tylko wersji agenta MARS starszej niż 2.0.9169.0.
>

| Backup — opcja harmonogramu | Czas przechowywania danych
| -- | --
| Dzień | **Domyślne przechowywanie**: równoważne "przechowywanie w dniach dla codziennych kopii zapasowych". <br/><br/> **Wyjątek**: Jeśli dzienna zaplanowana kopia zapasowa ustawiona na przechowywanie długoterminowe (tygodnie, miesiące lub lata) zakończy się niepowodzeniem, kopia zapasowa na żądanie, która jest wyzwalana bezpośrednio po awarii, będzie brana pod uwagę w przypadku przechowywania długoterminowego. W przeciwnym razie Następna zaplanowana kopia zapasowa jest traktowana do długoterminowego przechowywania.<br/><br/> **Przykładowy scenariusz**: zaplanowana kopia zapasowa w czwartek o godzinie 8:00 kończy się niepowodzeniem. Ta kopia zapasowa była brana pod uwagę przez cotygodniowe, comiesięczne lub coroczne przechowywanie. Dlatego pierwsza kopia zapasowa na żądanie wyzwolona przed następną zaplanowaną kopią zapasową w piątek o godzinie 8:00 jest automatycznie oznaczana jako cotygodniowe, miesięczne lub coroczne przechowywanie. Ta kopia zapasowa zastępuje kopie zapasowe w czwartek 8:00.
| Tydzień | **Domyślne przechowywanie**: jeden dzień. Kopie zapasowe na żądanie pobierane dla źródła danych, które mają cotygodniowe zasady tworzenia kopii zapasowych, są usuwane w następnym dniu. Są one usuwane, nawet jeśli są najnowszymi kopiami zapasowymi źródła danych. <br/><br/> **Wyjątek**: Jeśli cotygodniowa zaplanowana kopia zapasowa ustawiona na przechowywanie długoterminowe (tygodnie, miesiące lub lata) zakończy się niepowodzeniem, kopia zapasowa na żądanie, która jest wyzwalana bezpośrednio po awarii, zostanie uznana za długoterminowe przechowywanie danych. W przeciwnym razie Następna zaplanowana kopia zapasowa jest traktowana do długoterminowego przechowywania. <br/><br/> **Przykładowy scenariusz**: zaplanowana kopia zapasowa w czwartek o godzinie 8:00 kończy się niepowodzeniem. Ta kopia zapasowa była brana pod uwagę w przypadku przechowywania miesięcznego lub rocznego. Dlatego pierwsza kopia zapasowa na żądanie, która jest wyzwalana przed następną zaplanowaną kopią zapasową w czwartek o godzinie 8:00, zostanie automatycznie oznaczona jako comiesięczna lub roczna. Ta kopia zapasowa zastępuje kopie zapasowe w czwartek 8:00.

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad kopii zapasowych](#create-a-backup-policy).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić pliki na platformie Azure](backup-azure-restore-windows-server.md).
* Znajdź [typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md)
