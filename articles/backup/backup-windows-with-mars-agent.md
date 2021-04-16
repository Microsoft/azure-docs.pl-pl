---
title: Back up Windows machines by using the MARS agent (Kopię zapasową maszyn z systemem Windows przy użyciu agenta MARS)
description: Użyj agenta Microsoft Azure Recovery Services (MARS) do obsługi kopii zapasowej maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519174"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Back up Windows Server files and folders to Azure

W tym artykule opisano sposób kopii zapasowej maszyn z systemem Windows przy [użyciu usługi Azure Backup](backup-overview.md) i agenta Microsoft Azure Recovery Services (MARS). Mars jest również nazywany agentem Azure Backup agenta.

Ten artykuł obejmuje następujące zagadnienia:

> [!div class="checklist"]
>
> * Sprawdzenie wymagań wstępnych
> * Utwórz zasady tworzenia kopii zapasowych i harmonogram.
> * Wykonywanie kopii zapasowej na żądanie.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się, [Azure Backup używa agenta MARS do obsługi kopii zapasowej maszyn z systemem Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Dowiedz się więcej o [architekturze kopii zapasowej,](backup-architecture.md#architecture-back-up-to-dpmmabs) która uruchamia agenta MARS na pomocniczym serwerze USŁUGI MABS Data Protection Manager serwera.
* Zapoznaj [się z obsługiwanymi i kopiami, których kopię zapasową](backup-support-matrix-mars-agent.md) można utworzyć za pomocą agenta MARS.
* [Sprawdź dostęp do](install-mars-agent.md#verify-internet-access) Internetu na maszynach, których kopię zapasową chcesz wrócić.
* Jeśli agent MARS nie jest zainstalowany, dowiedz się, jak go zainstalować [tutaj.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają, kiedy należy tworzyć migawki danych w celu utworzenia punktów odzyskiwania. Określa również, jak długo mają być zachowaj punkty odzyskiwania. Agent MARS umożliwia skonfigurowanie zasad tworzenia kopii zapasowych.

Azure Backup nie uwzględnia automatycznie czasu letniego (DST). To ustawienie domyślne może spowodować pewne rozbieżności między czasem rzeczywistym i zaplanowanym czasem tworzenia kopii zapasowej.

Aby utworzyć nowe zasady kopii zapasowych:

1. Po pobraniu i zarejestrowaniu agenta MARS otwórz konsolę agenta. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.  

1. W **obszarze Akcje** wybierz pozycję Zaplanuj tworzenie kopii **zapasowej.**

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. W Kreatorze tworzenia kopii zapasowej harmonogramu wybierz pozycję **Wprowadzenie**  >  **Dalej.**
1. W **obszarze Wybierz elementy do kopii zapasowej** wybierz pozycję Dodaj **elementy.**

    ![Dodawanie elementów do kopii zapasowej](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. W polu **Wybierz elementy** wybierz elementy do kopii zapasowej, a następnie wybierz przycisk **OK.**

    ![Wybieranie elementów do kopii zapasowej](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na stronie **Wybieranie elementów do kopii zapasowej** wybierz pozycję **Dalej.**
1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych** określ, kiedy należy tworzyć codzienne lub cotygodniowe kopie zapasowe. Następnie wybierz przycisk **Dalej**.

    * Punkt odzyskiwania jest tworzony podczas tworzenia kopii zapasowej.
    * Liczba punktów odzyskiwania utworzonych w środowisku zależy od harmonogramu tworzenia kopii zapasowych.
    * Można zaplanować maksymalnie trzy codzienne kopie zapasowe dziennie. W poniższym przykładzie mają miejsce dwa codzienne kopie zapasowe: jedna o północy i jedna o godzinie 18:00.

        ![Konfigurowanie codziennego harmonogramu tworzenia kopii zapasowych](./media/backup-configure-vault/day-schedule.png)

    * Można również uruchamiać cotygodniowe kopie zapasowe. W poniższym przykładzie kopie zapasowe są podejmowane w każdą alternatywną niedzielę i środę o 9:30 i 1:00.

        ![Konfigurowanie cotygodniowego harmonogramu tworzenia kopii zapasowych](./media/backup-configure-vault/week-schedule.png)

1. Na stronie **Wybierz zasady przechowywania** określ sposób przechowywania historycznych kopii danych. Następnie wybierz przycisk **Dalej**.

    * Ustawienia przechowywania określają, które punkty odzyskiwania mają być przechowywane i jak długo mają być przechowywane.
    * W przypadku ustawienia dziennego przechowywania należy wskazać, że o godzinie określonej dla dziennego przechowywania najnowszy punkt odzyskiwania zostanie zachowany przez określoną liczbę dni. Można też określić zasady przechowywania miesięcznego, aby wskazać, że punkt odzyskiwania utworzony 30 dnia każdego miesiąca powinien być przechowywany przez 12 miesięcy.
    * Przechowywanie codziennych i cotygodniowych punktów odzyskiwania zwykle pokrywa się z harmonogramem tworzenia kopii zapasowych. Dlatego gdy harmonogram wyzwala tworzenie kopii zapasowej, punkt odzyskiwania, który tworzy kopia zapasowa, jest przechowywany przez czas określony przez zasady przechowywania dziennego lub tygodniowego.
    * W poniższym przykładzie:

        * Codzienne kopie zapasowe o północy i 18:00 są przechowywane przez siedem dni.
        * Kopie zapasowe wykonane w sobotę o północy i 18:00 są przechowywane przez cztery tygodnie.
        * Kopie zapasowe wykonane w ostatnią sobotę miesiąca o północy i 18:00 są przechowywane przez 12 miesięcy.
        * Kopie zapasowe wykonane w ostatnią sobotę marca są przechowywane przez 10 lat.

        ![Przykład zasad przechowywania](./media/backup-configure-vault/retention-example.png)

1. Na stronie **Wybieranie typu początkowej kopii zapasowej** zdecyduj, czy chcesz wykonać początkową kopię zapasową przez sieć, czy użyć kopii zapasowej w trybie offline. Aby wykonać początkową kopię zapasową za pośrednictwem sieci, wybierz pozycję **Automatycznie przez sieć**  >  **Dalej.**

    Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej w trybie offline, zobacz Use Azure Data Box for offline backup (Używanie kopii [zapasowej w trybie offline)](offline-backup-azure-data-box.md).

    ![Wybieranie typu początkowej kopii zapasowej](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na stronie **Potwierdzenie** przejrzyj informacje, a następnie wybierz pozycję **Zakończ.**

    ![Potwierdzanie typu kopii zapasowej](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Po zakończeniu tworzenia harmonogramu tworzenia kopii zapasowych przez kreatora wybierz pozycję **Zamknij.**

    ![Wyświetlanie postępu harmonogramu tworzenia kopii zapasowych](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Utwórz zasady na każdej maszynie, na której zainstalowano agenta.

### <a name="do-the-initial-backup-offline"></a>Wykonywanie początkowej kopii zapasowej w trybie offline

Możesz automatycznie uruchomić początkową kopię zapasową za pośrednictwem sieci lub utworzyć kopię zapasową w trybie offline. Wstępne inicjowanie kopii zapasowej w trybie offline jest przydatne, jeśli masz duże ilości danych, które wymagają dużej przepustowości sieci do transferu.

Aby wykonać transfer w trybie offline:

1. Zapisz dane kopii zapasowej w lokalizacji przejściowej.
1. Użyj narzędzia AzureOfflineBackupDiskPrep, aby skopiować dane z lokalizacji przejściowej na co najmniej jeden dysk SATA.

    Narzędzie tworzy zadanie importu platformy Azure. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Import/Export.](../import-export/storage-import-export-service.md)
1. Wyślij dyski SATA do centrum danych platformy Azure.

    W centrum danych dane dysku są kopiowane na konto usługi Azure Storage. Azure Backup kopiuje dane z konta magazynu do magazynu, a zaplanowane są przyrostowe kopie zapasowe.

Aby uzyskać więcej informacji na temat iniekcjowania w trybie offline, zobacz Use Azure Data Box for offline backup (Używanie Azure Data Box [kopii zapasowej offline).](offline-backup-azure-data-box.md)

### <a name="enable-network-throttling"></a>Włączanie ograniczania przepustowości sieci

Możesz kontrolować sposób, w jaki agent MARS używa przepustowości sieci, włączając ograniczanie przepustowości sieci. Ograniczanie jest przydatne, jeśli musisz utworzyć kopię zapasową danych w godzinach pracy, ale chcesz kontrolować przepustowość używaną przez działanie tworzenia kopii zapasowej i przywracania.

Ograniczanie przepustowości sieci w Azure Backup korzysta [Quality of Service (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) w lokalnym systemie operacyjnym.

Ograniczanie przepustowości sieci dla kopii zapasowych jest dostępne w systemie Windows Server 2012 i nowszych oraz na Windows 8 i nowszych. W systemach operacyjnych powinny być uruchomione najnowsze dodatki Service Pack.

Aby włączyć ograniczanie przepustowości sieci:

1. W agencie MARS wybierz pozycję **Zmień właściwości**.
1. Na karcie **Ograniczanie przepustowości wybierz** pozycję Włącz ograniczanie użycia przepustowości internetowej dla operacji tworzenia kopii **zapasowej.**

    ![Konfigurowanie ograniczania przepustowości sieci dla operacji tworzenia kopii zapasowych](./media/backup-configure-vault/throttling-dialog.png)
1. Określ dozwoloną przepustowość w godzinach pracy i poza godzinami pracy. Wartości przepustowości zaczynają się od 512 Kb/s i są do 1023 Mb/s. Następnie wybierz przycisk **OK**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

1. W agencie MARS wybierz pozycję Back Up Now (Teraz **możesz wrócić do kopii zapasowej).**

    ![Teraz możesz wrócić do kopii zapasowej w systemie Windows Server](./media/backup-configure-vault/backup-now.png)

1. Jeśli wersja agenta MARS to 2.0.9169.0 lub nowsza, możesz ustawić niestandardową datę przechowywania. W sekcji **Zachowaj kopię zapasową do** wybierz datę z kalendarza.

   ![Dostosowywanie daty przechowywania przy użyciu kalendarza](./media/backup-configure-vault/mars-ondemand.png)

1. Na stronie **Potwierdzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Kopii zapasowej**.
1. Wybierz **pozycję Zamknij,** aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem tworzenia kopii zapasowej, kreator będzie nadal działać w tle.

Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli kopia **zapasowa** zostanie wyświetlony stan Ukończono zadanie.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Konfigurowanie zachowania przechowywania zasad kopii zapasowych na żądanie

> [!NOTE]
> Te informacje dotyczą tylko wersji agenta MARS starszych niż 2.0.9169.0.
>

| Opcja Harmonogram tworzenia kopii zapasowych | Czas przechowywania danych
| -- | --
| Dzień | **Przechowywanie domyślne:** równoważne "przechowywanie w dniach dla codziennych kopii zapasowych". <br/><br/> **Wyjątek:** Jeśli zaplanowana kopia zapasowa ustawiona na potrzeby długoterminowego przechowywania (tygodni, miesięcy lub lat) nie powiedzie się, kopia zapasowa na żądanie, która zostanie wyzwolona bezpośrednio po awarii, zostanie uznana za długoterminową. W przeciwnym razie do przechowywania długoterminowego jest rozważana następna zaplanowana kopia zapasowa.<br/><br/> **Przykładowy scenariusz:** Zaplanowana kopia zapasowa w czwartek o godzinie 8:00 nie powiodła się. Tę kopię zapasową należy uwzględnić w przypadku przechowywania tygodniowego, miesięcznego lub miesięcznego. Dlatego pierwsza kopia zapasowa na żądanie wyzwolona przed następną zaplanowaną kopią zapasową w piątek o godzinie 8:00 jest automatycznie oznaczana na potrzeby przechowywania tygodniowego, miesięcznego lub miesięcznego. Ta kopia zapasowa zastępuje kopię zapasową w czwartek o godzinie 8:00.
| Tydzień | **Domyślny okres przechowywania:** jeden dzień. Kopie zapasowe na żądanie, które są podejmowane dla źródła danych, które ma cotygodniowe zasady tworzenia kopii zapasowych, zostaną usunięte następnego dnia. Są one usuwane, nawet jeśli są to najnowsze kopie zapasowe źródła danych. <br/><br/> **Wyjątek:** Jeśli cotygodniowa zaplanowana kopia zapasowa ustawiona na potrzeby długoterminowego przechowywania (tygodni, miesięcy lub lat) zakończy się niepowodzeniem, kopia zapasowa na żądanie, która jest wyzwalana bezpośrednio po awarii, jest rozważana na potrzeby przechowywania długoterminowego. W przeciwnym razie do przechowywania długoterminowego jest rozważana następna zaplanowana kopia zapasowa. <br/><br/> **Przykładowy scenariusz:** Zaplanowana kopia zapasowa w czwartek o godzinie 8:00 nie powiodła się. Tę kopię zapasową należy uwzględnić w przypadku przechowywania miesięcznego lub miesięcznego. Dlatego pierwsza kopia zapasowa na żądanie, która jest wyzwalana przed następną zaplanowaną kopią zapasową w czwartek o godzinie 8:00, jest automatycznie oznaczana na potrzeby przechowywania miesięcznego lub miesięcznego. Ta kopia zapasowa zastępuje kopię zapasową w czwartek o godzinie 8:00.

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad kopii zapasowych.](#create-a-backup-policy)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić pliki na platformie Azure.](backup-azure-restore-windows-server.md)
* Znajdź [często zadawane pytania dotyczące kopii zapasowej plików i folderów](backup-azure-file-folder-backup-faq.yml)
