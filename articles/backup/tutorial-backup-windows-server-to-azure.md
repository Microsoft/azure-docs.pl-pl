---
title: Samouczek — Tworzenie kopii zapasowej systemu Windows Server na platformie Azure
description: Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowej lokalnych serwerów z systemem Windows w magazynie usługi Recovery Services.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: 560a4907fc812aae027ad9e1a1c262fc994c0da9
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295545"
---
# <a name="back-up-windows-server-to-azure"></a>Tworzenie kopii zapasowej serwerów z systemem Windows na platformie Azure

Przy użyciu usługi Azure Backup można chronić serwer z systemem Windows przed uszkodzeniem, atakiem czy awarią. Usługa Azure Backup udostępnia proste narzędzie: agenta usługi Microsoft Azure Recovery Services (MARS). Agent usług MARS zainstalowany na serwerze z systemem Windows chroni pliki, foldery i konfigurację serwera przy użyciu stanu systemu Windows Server. W tym samouczku opisano, jak za pomocą agenta usług MARS utworzyć kopię zapasową serwera z systemem Windows na platformie Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Pobieranie i konfigurowanie agenta usług MARS
> * Konfigurowanie harmonogramu tworzenia i przechowywania kopii zapasowych serwera
> * Wykonaj kopię zapasową na żądanie

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Aby umożliwić tworzenie kopii zapasowych serwerów z systemem Windows należy najpierw utworzyć lokalizację przechowywania kopii zapasowych, nazywanych też punktami przywracania. [Magazyn usługi Recovery Services](backup-azure-recovery-services-vault-overview.md) to kontener na platformie Azure, w którym przechowywane są kopie zapasowe serwera z systemem Windows. Wykonaj poniższe czynności, aby utworzyć magazyn usługi Recovery Services w witrynie Azure Portal.

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**, a następnie na liście usług wpisz **Recovery Services**. Kliknij pozycję **Magazyny usługi Recovery Services**.

   ![Otwieranie magazynu usługi Recovery Services](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. W menu **Magazyny Usług odzyskiwania** kliknij pozycję **Dodaj**.

   ![Wprowadzanie informacji o magazynie](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. W menu **Magazyn usługi Recovery Services**:

    * Wpisz *myRecoveryServicesVault* w polu **Nazwa**.
    * Identyfikator bieżącej subskrypcji znajduje się w polu **Subskrypcja**.
    * W sekcji **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie pozycję *myResourceGroup*. Jeśli grupa zasobów *myResourceGroup* nie istnieje, wybierz pozycję **Utwórz nową** i wpisz nazwę *myResourceGroup*.
    * W menu rozwijanym **Lokalizacja** wybierz pozycję *Europa Zachodnia*.
    * Kliknij przycisk **Utwórz**, aby utworzyć magazyn usługi Recovery Services.

Po utworzeniu magazynu pojawi się on na liście magazynów Usług odzyskiwania.

## <a name="download-recovery-services-agent"></a>Pobieranie agenta usługi Recovery Services

Agent usługi Microsoft Azure Recovery Services (MARS) tworzy skojarzenie między serwerem z systemem Windows a magazynem usługi Recovery Services. Poniższa procedura przedstawia sposób pobierania agenta na serwer.

1. Wybierz magazyn **myRecoveryServicesVault** z listy magazynów usługi Recovery Services, aby otworzyć jego pulpit nawigacyjny.

   ![Wprowadzanie informacji o magazynie](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. W menu pulpitu nawigacyjnego magazynu kliknij pozycję **Kopia zapasowa**.

3. W menu **Cel kopii zapasowej**:

   * dla **gdzie jest uruchomione Twoje obciążenie?** wybierz pozycję **lokalna**
   * Na liście **Co ma zawierać kopia zapasowa?** zaznacz pozycje **Pliki i foldery** oraz **Stan systemu**.

   ![Wprowadzanie informacji o magazynie](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Kliknij pozycję **Przygotuj infrastrukturę**, aby otworzyć menu **Przygotowywanie infrastruktury**.

5. W menu **Przygotowanie infrastruktury** kliknij pozycję **Pobierz agenta systemu Windows Server lub Windows Client**, aby pobrać plik *MARSAgentInstaller.exe*.

    ![Przygotowywanie infrastruktury](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Instalator otworzy oddzielne okno przeglądarki i pobierze plik **MARSAgentInstaller.exe**.

6. Przed uruchomieniem pobranego pliku kliknij przycisk **Pobierz** w menu Przygotowywanie infrastruktury i zapisz plik **poświadczeń magazynu**. Poświadczenia magazynu są wymagane do połączenia agenta usługi MARS z magazynem usługi Recovery Services.

    ![Przygotowywanie infrastruktury](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Znajdź i kliknij dwukrotnie pobrany plik **MARSagentinstaller.exe**.
2. Zostanie wyświetlony **Kreator instalacji agenta usługi Microsoft Azure Recovery Services**. Podczas pracy z kreatorem wprowadź następujące informacje po wyświetleniu monitów, a następnie kliknij pozycję **Zarejestruj**.
   * Lokalizacja folderu instalacji i folderu pamięci podręcznej.
   * Informacje o serwerze proxy, jeśli korzystasz z niego do łączenia się z Internetem.
   * Nazwa użytkownika i hasło, jeśli korzystasz z uwierzytelnionego serwera proxy.

     ![Przygotowywanie infrastruktury](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. Po zakończeniu pracy z kreatorem kliknij pozycję **Przejdź do rejestracji** i wskaż plik **poświadczeń magazynu** pobrany w poprzedniej procedurze.

4. Po wyświetleniu monitu podaj hasło szyfrowania na potrzeby szyfrowania kopii zapasowych serwera z systemem Windows. Zapisz hasło w bezpiecznej lokalizacji, ponieważ w przypadku utraty hasła firma Microsoft nie będzie mogła go odzyskać.

5. Kliknij przycisk **Zakończ**.

## <a name="configure-backup-and-retention"></a>Konfigurowanie tworzenia i przechowywania kopii zapasowych

Agent usługi Microsoft Azure Recovery Services umożliwia zaplanowanie tworzenia kopii zapasowych serwera z systemem Windows na platformie Azure. Wykonaj następujące czynności na serwerze, na który został pobrany agent.

1. Otwórz agenta usługi Microsoft Azure Recovery Services. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

2. W konsoli agenta usługi Recovery Services kliknij pozycję **Zaplanuj wykonywanie kopii zapasowej** w **okienku akcji**.

    ![Przygotowywanie infrastruktury](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Kliknij przycisk **Dalej**, aby przejść do strony **Wybierz elementy, których kopię zapasową chcesz utworzyć**.

4. Kliknij pozycję **Dodaj elementy**, a następnie w otwartym oknie dialogowym zaznacz pozycję **Stan systemu** oraz pliki lub foldery, których kopię zapasową chcesz utworzyć. Następnie kliknij przycisk **OK**.

5. Kliknij przycisk **Dalej**.

6. Na stronie **Określ harmonogram tworzenia kopii zapasowych (Stan systemu)** określ czas w ciągu dnia lub tygodnia, gdy powinna być tworzona kopia zapasowa stanu systemu, a następnie kliknij przycisk **Dalej**.

7. Na stronie **Wybierz zasady przechowywania (Stan systemu)** wybierz zasady przechowywania kopii zapasowej stanu systemu, a następnie kliknij przycisk **Dalej**.

8. W ten sam sposób wybierz harmonogram i zasady przechowywania kopii zapasowej wybranych plików i folderów.

9. Na stronie **Wybierz typ początkowej kopii zapasowej** wybierz pozycję **Automatycznie przez sieć**, a następnie kliknij przycisk **Dalej**.

10. Przejrzyj informacje na stronie **Potwierdzenie**, a następnie kliknij przycisk **Zakończ**.

11. Po ukończeniu harmonogramu tworzenia kopii zapasowej przez kreatora kliknij przycisk **Zamknij**.

## <a name="perform-an-on-demand-backup"></a>Wykonywanie kopii zapasowej na żądanie

Został określony harmonogram uruchamiania zadań tworzenia kopii zapasowej. Nie utworzono jednak jeszcze kopii zapasowej serwera. Najlepszym rozwiązaniem w zakresie odzyskiwania po awarii jest utworzenie kopii zapasowej na żądanie w celu zapewnienia, że dane na serwerze będą odporne na awarie.

1. W konsoli agenta usługi Microsoft Azure Recovery Services kliknij pozycję **Utwórz kopię zapasową teraz**.

    ![Przygotowywanie infrastruktury](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. W kreatorze **Utwórz kopię zapasową teraz** wybierz **Pliki i foldery** lub **Stan systemu** do utworzenia kopii zapasowej, a następnie kliknij przycisk **Dalej**.
3. Na stronie **Potwierdzenie** przejrzyj ustawienia, które zostaną użyte przez kreatora **Utwórz kopię zapasową teraz** do utworzenia kopii zapasowej serwera. Następnie kliknij pozycję **Utwórz kopię zapasową**.
4. Kliknij przycisk **Zamknij**, aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem procesu tworzenia kopii zapasowej, kreator będzie nadal działać w tle.
5. Po zakończeniu tworzenia pierwszej kopii zapasowej w okienku **Zadania** konsoli agenta usług MARS zostanie wyświetlony stan **Ukończono zadanie**.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem wykonano następujące czynności przy użyciu witryny Azure Portal:

> [!div class="checklist"]
>
> * Tworzenie magazynu usługi Recovery Services
> * Pobieranie agenta usługi Microsoft Azure Recovery Services
> * Instalowanie agenta
> * Konfigurowanie kopii zapasowej serwera z systemem Windows
> * Wykonywanie kopii zapasowej na żądanie

Przejdź do następnego samouczka, aby zapoznać się z procedurą odzyskiwania plików z platformy Azure na serwer z systemem Windows.

> [!div class="nextstepaction"]
> [Przywracanie plików z platformy Azure na serwer z systemem Windows](./tutorial-backup-restore-files-windows-server.md)
