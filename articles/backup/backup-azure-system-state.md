---
title: Tworzenie kopii zapasowej stanu systemu Windows na platformie Azure
description: Dowiedz się, jak utworzyć kopię zapasową stanu systemu komputerów z systemem Windows Server na platformie Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332897"
---
# <a name="back-up-windows-system-state-to-azure"></a>Tworzenie kopii zapasowej stanu systemu Windows na platformie Azure

W tym artykule wyjaśniono, jak utworzyć kopię zapasową stanu systemu Windows Server na platformie Azure. Zawarto przeprowadzić Cię przez podstawowe informacje.

Jeśli chcesz dowiedzieć się więcej o usłudze Azure Backup, przeczytaj to [omówienie](backup-overview.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) umożliwiające dostęp do dowolnej usługi Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Ustawianie nadmiarowości przechowywania dla magazynu

Po utworzeniu magazynu usług Recovery Services upewnij się, że nadmiarowość magazynu została skonfigurowana w preferowany sposób.

1. W okienku **Recovery Services magazynów** wybierz nowy magazyn.

    ![Wybieranie nowego magazynu z listy magazynów usług Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Po wybraniu magazynu okienko **magazyn Recovery Services** jest zawężane, a okienko ustawień (*które ma nazwę magazynu w górnej części*) i Otwórz okienko szczegółów magazynu.

    ![Wyświetlanie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. W okienku ustawienia nowego magazynu Użyj slajdu pionowego, aby przewinąć w dół do sekcji Zarządzanie, a następnie wybierz pozycję **infrastruktura kopii zapasowych**.
    Zostanie otwarte okienko infrastruktura tworzenia kopii zapasowych.
3. W okienku infrastruktura kopii zapasowych wybierz pozycję **Konfiguracja kopii zapasowej** , aby otworzyć okienko **Konfiguracja kopii zapasowej** .

    ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Wybierz odpowiednią opcję replikacji dla magazynu.

    ![Opcje konfiguracji magazynu](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Domyślnie magazyn jest nadmiarowy geograficznie. Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, kontynuuj korzystanie z magazynu **geograficznie nadmiarowego**. Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure. Więcej informacji o opcjach [geograficznie](../storage/common/storage-redundancy.md#geo-redundant-storage)nadmiarowych, [lokalnie nadmiarowych](../storage/common/storage-redundancy.md#locally-redundant-storage) i [strefowo](../storage/common/storage-redundancy.md#zone-redundant-storage) nadmiarowych magazynów można znaleźć w tym [omówieniu nadmiarowości magazynu](../storage/common/storage-redundancy.md).

Po utworzeniu magazynu należy skonfigurować go do tworzenia kopii zapasowych stanu systemu Windows.

## <a name="configure-the-vault"></a>Konfigurowanie magazynu

1. W okienku Magazyn Recovery Services (dla właśnie utworzonego magazynu) w sekcji Wprowadzenie wybierz pozycję **kopia zapasowa**, a następnie w okienku **wprowadzenie z kopią zapasową** wybierz pozycję **cel kopii zapasowej**.

    ![Otwieranie ustawień kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Zostanie otwarte okienko **cel kopii zapasowej** .

    ![Otwórz okienko cel kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Z menu **Gdzie działa Twoje obciążenie?** wybierz pozycję **Lokalnie**.

    Należy wybrać **lokalne** , ponieważ serwer z systemem Windows lub Windows jest komputerem fizycznym, który nie znajduje się na platformie Azure.

3. Z menu **co chcesz utworzyć kopię zapasową?** wybierz pozycję **stan systemu**, a następnie wybierz **przycisk OK**.

    ![Konfigurowanie plików i folderów](./media/backup-azure-system-state/backup-goal-system-state.png)

    Po wybraniu przycisku **OK**obok pozycji **cel kopii zapasowej**pojawi się znacznik wyboru i zostanie otwarte okienko **Przygotowywanie infrastruktury** .

    ![Cel kopii zapasowej został skonfigurowany, następnym krokiem jest przygotowanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. W okienku **Przygotowywanie infrastruktury** wybierz pozycję **Pobierz agenta dla systemu Windows Server lub klienta systemu Windows**.

    ![Przygotuj infrastrukturę](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Jeśli używasz systemu Windows Server Essential, wybierz pobieranie agenta dla systemu Windows Server Essential. Zostanie wyświetlone menu rozwijane z monitem o uruchomienie lub zapisanie pliku MARSAgentInstaller.exe.

    ![Okno dialogowe MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. W menu podręcznym pobieranie wybierz pozycję **Zapisz**.

    Domyślnie plik **MARSagentinstaller.exe** jest zapisywany w folderze Pobrane. Po zakończeniu działania Instalatora zobaczysz okno podręczne z pytaniem, czy chcesz uruchomić Instalatora, czy otworzyć folder.

    ![Instalator MARS został ukończony](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Nie musisz jeszcze instalować agenta. Agenta można zainstalować po pobraniu poświadczeń magazynu.

6. W okienku **Przygotowywanie infrastruktury** wybierz pozycję **Pobierz**.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Poświadczenia magazynu są pobierane do folderu **pobierania** . Po zakończeniu pobierania poświadczeń magazynu zobaczysz okno podręczne z pytaniem, czy chcesz otworzyć lub zapisać poświadczenia. Wybierz pozycję **Zapisz**. Jeśli przypadkowo wybierzesz pozycję **Otwórz**, pozwól, aby okno dialogowe, które próbuje otworzyć poświadczenia magazynu, zakończy się niepowodzeniem. Nie będzie można otworzyć poświadczeń magazynu. Przejdź do następnego kroku. Poświadczenia magazynu znajdują się w folderze **pobierania** .

    ![Zakończenie pobierania poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Poświadczenia magazynu muszą być zapisane tylko w lokalizacji lokalnej dla systemu Windows Server, na którym zamierzasz korzystać z agenta.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

> [!NOTE]
> Włączanie tworzenia kopii zapasowej za pomocą Azure Portal jest niedostępne. Użyj agenta Microsoft Azure Recovery Services, aby utworzyć kopię zapasową stanu systemu Windows Server.
>

1. Zlokalizuj i kliknij dwukrotnie plik **MARSagentinstaller.exe** w folderze Pobrane (lub innej lokalizacji).

    Instalator wyświetli serię komunikatów w miarę wypakowywania, instalowania i rejestrowania agenta usługi Recovery Services.

    ![Uruchamianie Instalatora agenta usługi Recovery Services — poświadczenia](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Wykonaj kroki kreatora instalacji agenta usługi Microsoft Azure Recovery Services. Aby zakończyć pracę kreatora, wykonaj następujące czynności:

   * Wybierz lokalizację folderu instalacji i folderu pamięci podręcznej.
   * Podaj informacje o serwerze proxy, jeśli korzystasz z niego do łączenia się z Internetem.
   * Podaj swoją nazwę użytkownika i hasło, jeśli korzystasz z uwierzytelnionego serwera proxy.
   * Udostępnianie pobranych poświadczeń magazynu
   * Zapisz hasło szyfrowania w bezpiecznej lokalizacji.

     > [!NOTE]
     > Jeśli utracisz lub zapomnisz hasło, firma Microsoft nie może odzyskać danych kopii zapasowej. Zapisz plik w bezpiecznej lokalizacji. Jest to wymagane do przywrócenia kopii zapasowej.
     >
     >

Agent jest teraz zainstalowany, a maszyna zarejestrowana w magazynie. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="back-up-windows-server-system-state"></a>Tworzenie kopii zapasowej stanu systemu Windows Server

Początkowa kopia zapasowa obejmuje dwa zadania:

* Planowanie tworzenia kopii zapasowej
* Tworzenie kopii zapasowej stanu systemu po raz pierwszy

Aby utworzyć początkową kopię zapasową, użyj agenta usługi Microsoft Azure Recovery Services.

> [!NOTE]
> Można utworzyć kopię zapasową stanu systemu w systemie Windows Server 2008 R2 przy użyciu systemu Windows Server 2016. Tworzenie kopii zapasowej stanu systemu nie jest obsługiwane w jednostkach SKU klienta. Stan systemu nie jest wyświetlany jako opcja dla klientów z systemem Windows lub Windows Server 2008 z dodatkiem SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Aby zaplanować zadanie tworzenia kopii zapasowej

1. Otwórz agenta usługi Microsoft Azure Recovery Services. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

    ![Uruchamianie agenta usługi Azure Recovery Services](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. W agencie Recovery Services wybierz pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na stronie **wprowadzenie** kreatora planowania kopii zapasowych wybierz pozycję **dalej**.

4. Na stronie **Wybierz elementy do utworzenia kopii zapasowej** wybierz pozycję **Dodaj elementy**.

5. Wybierz pozycję **stan systemu** , a następnie wybierz przycisk **OK**.

6. Wybierz opcję **Dalej**.

7. Wybierz żądaną częstotliwość tworzenia kopii zapasowych i zasady przechowywania kopii zapasowych stanu systemu na kolejnych stronach.

8. Na stronie Potwierdzenie przejrzyj informacje, a następnie wybierz pozycję **Zakończ**.

9. Po zakończeniu tworzenia harmonogramu tworzenia kopii zapasowych przez kreatora wybierz pozycję **Zamknij**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Aby utworzyć kopię zapasową stanu systemu Windows Server po raz pierwszy

1. Upewnij się, że nie ma żadnych oczekujących aktualizacji dla systemu Windows Server, które wymagają ponownego uruchomienia.

2. W agencie Recovery Services wybierz pozycję **Wykonaj kopię zapasową teraz** , aby zakończyć początkowe umieszczanie w sieci.

    ![Tworzenie kopii zapasowej systemu Windows Server teraz](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Wybierz pozycję **stan systemu** na ekranie **Wybieranie elementu kopii zapasowej** , który zostanie wyświetlony, a następnie wybierz przycisk **dalej**.

4. Na stronie Potwierdzenie przejrzyj ustawienia, które zostaną użyte przez Kreatora natychmiastowego tworzenia kopii zapasowej do utworzenia kopii zapasowej maszyny. Następnie wybierz pozycję **Utwórz kopię zapasową**.

5. Wybierz pozycję **Zamknij** , aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem procesu tworzenia kopii zapasowej, kreator będzie nadal działać w tle.
    > [!NOTE]
    > Agent MARS jest wyzwalany `SFC /verifyonly` w ramach sprawdzania przed każdą kopią zapasową stanu systemu. Ma to na celu zapewnienie, że pliki kopii zapasowej w ramach stanu systemu mają poprawne wersje odpowiadające wersji systemu Windows. Dowiedz się więcej o funkcji System File Checker (SFC) w [tym artykule](/windows-server/administration/windows-commands/sfc).
    >

Po zakończeniu tworzenia początkowej kopii zapasowej w konsoli usługi Backup zostanie wyświetlony stan **Ukończono zadanie**.

  ![Początkowa replikacja została zakończona](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Masz pytania?

Jeśli masz pytania, [Prześlij nam swoją opinię](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tworzeniu kopii zapasowej maszyn z systemem Windows](backup-windows-with-mars-agent.md).
* Teraz, po wykonaniu kopii zapasowej stanu systemu Windows Server, możesz [zarządzać magazynami i serwerami](backup-azure-manage-windows-server.md).
* Jeśli chcesz przywrócić kopię zapasową, w tym artykule znajdziesz informacje dotyczące [przywracania plików na maszynę z systemem Windows](backup-azure-restore-windows-server.md).
