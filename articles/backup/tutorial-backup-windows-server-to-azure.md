---
title: Samouczek — Tworzenie kopii zapasowej systemu Windows Server na platformie Azure
description: Ten samouczek zawiera szczegółowe instrukcje tworzenia kopii zapasowej lokalnych serwerów z systemem Windows w magazynie usługi Recovery Services.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88261909"
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

1. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**, a następnie na liście usług wpisz **Recovery Services**. Wybierz **Recovery Services magazynów**.

   ![Otwórz magazyn Recovery Services](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. W menu **magazyny Recovery Services** wybierz pozycję **Dodaj**.

   ![Podaj informacje dotyczące magazynu](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. W menu **Magazyn usługi Recovery Services**:

    * Wpisz *myRecoveryServicesVault* w polu **Nazwa**.
    * Identyfikator bieżącej subskrypcji znajduje się w polu **Subskrypcja**.
    * W sekcji **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie pozycję *myResourceGroup*. Jeśli grupa zasobów *myResourceGroup* nie istnieje, wybierz pozycję **Utwórz nową** i wpisz nazwę *myResourceGroup*.
    * W menu rozwijanym **Lokalizacja** wybierz pozycję *Europa Zachodnia*.
    * Wybierz pozycję **Utwórz** , aby utworzyć magazyn Recovery Services.

Po utworzeniu magazynu pojawi się on na liście magazynów Usług odzyskiwania.

## <a name="download-recovery-services-agent"></a>Pobieranie agenta usługi Recovery Services

Agent usługi Microsoft Azure Recovery Services (MARS) tworzy skojarzenie między serwerem z systemem Windows a magazynem usługi Recovery Services. Poniższa procedura przedstawia sposób pobierania agenta na serwer.

1. Wybierz magazyn **myRecoveryServicesVault** z listy magazynów usługi Recovery Services, aby otworzyć jego pulpit nawigacyjny.

   ![Wybierz magazyn, aby otworzyć pulpit nawigacyjny](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. W menu pulpitu nawigacyjnego magazynu wybierz pozycję **kopia zapasowa**.

3. W menu **Cel kopii zapasowej**:

   * dla **gdzie jest uruchomione Twoje obciążenie?** wybierz pozycję **lokalna**
   * Na liście **Co ma zawierać kopia zapasowa?** zaznacz pozycje **Pliki i foldery** oraz **Stan systemu**.

   ![Menu cel kopii zapasowej](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Wybierz pozycję **Przygotuj infrastrukturę** , aby otworzyć menu **Przygotuj infrastrukturę** .

5. W menu **Przygotuj infrastrukturę** wybierz pozycję **Pobierz agenta dla systemu Windows Server lub klienta systemu Windows** , aby pobrać *MARSAgentInstaller.exe*.

    ![Pobierz agenta dla systemu Windows Server lub klienta systemu Windows](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Instalator otworzy oddzielne okno przeglądarki i pobierze plik **MARSAgentInstaller.exe**.

6. Przed uruchomieniem pobranego pliku, w menu Przygotuj infrastrukturę wybierz pozycję **Pobierz** i Zapisz plik **poświadczeń magazynu** . Poświadczenia magazynu są wymagane do połączenia agenta usługi MARS z magazynem usługi Recovery Services.

    ![Pobieranie poświadczeń magazynu](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Znajdź i kliknij dwukrotnie pobrany plik **MARSagentinstaller.exe**.
2. Zostanie wyświetlony **Kreator instalacji agenta usługi Microsoft Azure Recovery Services**. Podczas przechodzenia przez kreatora podaj poniższe informacje po wyświetleniu monitu i wybierz pozycję **zarejestruj**.
   * Lokalizacja folderu instalacji i folderu pamięci podręcznej.
   * Informacje o serwerze proxy, jeśli korzystasz z niego do łączenia się z Internetem.
   * Nazwa użytkownika i hasło, jeśli korzystasz z uwierzytelnionego serwera proxy.

     ![Kreator instalacji agenta Microsoft Azure Recovery Services](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. Na końcu kreatora wybierz pozycję przechodzenie **do rejestracji** i podaj plik **poświadczeń magazynu** pobrany w poprzedniej procedurze.

4. Po wyświetleniu monitu podaj hasło szyfrowania na potrzeby szyfrowania kopii zapasowych serwera z systemem Windows. Zapisz hasło w bezpiecznej lokalizacji, ponieważ firma Microsoft nie może odzyskać hasła, jeśli zostało utracone.

5. Wybierz pozycję **Zakończ**.

## <a name="configure-backup-and-retention"></a>Konfigurowanie tworzenia i przechowywania kopii zapasowych

Agent usługi Microsoft Azure Recovery Services umożliwia zaplanowanie tworzenia kopii zapasowych serwera z systemem Windows na platformie Azure. Wykonaj następujące czynności na serwerze, na który został pobrany agent.

1. Otwórz agenta usługi Microsoft Azure Recovery Services. Aby go znaleźć, wyszukaj na maszynie łańcuch **Microsoft Azure Backup**.

2. W konsoli agenta Recovery Services wybierz pozycję **Zaplanuj wykonywanie kopii zapasowej** w **okienku Akcje**.

    ![Planowanie kopii zapasowej](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Wybierz pozycję **dalej** , aby przejść do strony **Wybierz elementy do utworzenia kopii zapasowej** .

4. Wybierz pozycję **Dodaj elementy** , a następnie w otwartym oknie dialogowym wybierz pozycję **stan systemu** i pliki lub foldery, dla których chcesz utworzyć kopię zapasową. Następnie wybierz przycisk **OK**.

5. Wybierz opcję **Dalej**.

6. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych (stan systemu)** określ godzinę, lub tydzień, w którym mają być wyzwalane kopie zapasowe stanu systemu, a następnie wybierz przycisk **dalej**.

7. Na stronie **Wybierz zasady przechowywania (stan systemu)** wybierz zasady przechowywania kopii zapasowej dla stanu systemu i wybierz przycisk **dalej**.

8. W ten sam sposób wybierz harmonogram i zasady przechowywania kopii zapasowej wybranych plików i folderów.

9. Na stronie **Wybierz typ początkowej kopii zapasowej** wybierz opcję **automatycznie przez sieć**, a następnie wybierz przycisk **dalej**.

10. Na stronie **potwierdzenie** Przejrzyj informacje, a następnie wybierz pozycję **Zakończ**.

11. Po zakończeniu tworzenia harmonogramu tworzenia kopii zapasowych przez kreatora wybierz pozycję **Zamknij**.

## <a name="perform-an-on-demand-backup"></a>Wykonywanie kopii zapasowej na żądanie

Harmonogram został ustalony podczas wykonywania zadań tworzenia kopii zapasowej. Jednak nie wykonano kopii zapasowej serwera. Najlepszym rozwiązaniem w zakresie odzyskiwania po awarii jest wykonanie kopii zapasowej na żądanie w celu zapewnienia odporności danych dla serwera.

1. W konsoli agenta Microsoft Azure Recovery Services wybierz pozycję **Wykonaj kopię zapasową teraz**.

    ![Wykonaj kopię zapasową teraz](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. W kreatorze **tworzenia kopii zapasowej teraz** wybierz jeden z **plików i folderów** lub **stanu systemu** , którego kopię zapasową chcesz utworzyć, a następnie wybierz pozycję **dalej** .
3. Na stronie **Potwierdzenie** przejrzyj ustawienia, które zostaną użyte przez kreatora **Utwórz kopię zapasową teraz** do utworzenia kopii zapasowej serwera. Następnie wybierz pozycję **Utwórz kopię zapasową**.
4. Wybierz pozycję **Zamknij** , aby zamknąć kreatora. Jeśli zamkniesz kreatora przed zakończeniem procesu tworzenia kopii zapasowej, kreator będzie nadal działać w tle.
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
