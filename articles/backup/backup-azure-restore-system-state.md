---
title: Przywracanie stanu systemu do systemu Windows Server
description: Objaśnienie krok po kroku dotyczące przywracania stanu systemu Windows Server z kopii zapasowej na platformie Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 39cac84c4a33c1da209d0a0cc7b0f8ac8ee390a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610789"
---
# <a name="restore-system-state-to-windows-server"></a>Przywróć stan systemu do systemu Windows Server

W tym artykule wyjaśniono, jak przywrócić kopie zapasowe stanu systemu Windows Server z magazynu usługi Azure Recovery Services. Aby przywrócić stan systemu, musisz mieć kopię zapasową stanu systemu (utworzoną przy użyciu instrukcji w temacie [Tworzenie kopii zapasowej stanu systemu](backup-azure-system-state.md#back-up-windows-server-system-state)i upewnić się, że zainstalowano [najnowszą wersję agenta Microsoft Azure Recovery Services (MARS)](https://aka.ms/azurebackup_agent). Proces odzyskiwania danych stanu systemu Windows Server z magazynu usługi Azure Recovery Services jest procesem dwuetapowym:

1. Przywróć stan systemu jako pliki z Azure Backup. Podczas przywracania stanu systemu jako plików z Azure Backup można:
   * Przywróć stan systemu na tym samym serwerze, na którym zostały wykonane kopie zapasowe, lub
   * Przywróć plik stanu systemu na alternatywny serwer.

2. Zastosuj przywrócone pliki stanu systemu do systemu Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Odzyskiwanie plików stanu systemu na tym samym serwerze

Poniższe kroki wyjaśniają, jak wycofać konfigurację systemu Windows Server do poprzedniego stanu. Przywrócenie konfiguracji serwera z powrotem do znanego, stabilnego stanu może być niezwykle cenne. Poniższe kroki przywracają stan systemu serwera z magazynu Recovery Services.

1. Otwórz przystawkę **Microsoft Azure Backup**. Jeśli nie wiesz, gdzie zainstalowano przystawkę, przeszukaj komputer lub serwer pod kątem **Microsoft Azure Backup**.

    Aplikacja klasyczna powinna zostać wyświetlona w wynikach wyszukiwania.

2. Kliknij pozycję **Odzyskaj dane** , aby uruchomić kreatora.

    ![Odzyskaj dane](./media/backup-azure-restore-windows-server/recover.png)

3. Aby przywrócić dane na tym samym serwerze lub komputerze, w okienku **wprowadzenie** wybierz pozycję **ten serwer ( `<server name>` )** , a następnie kliknij przycisk **dalej**.

    ![Wybierz tę opcję serwera, aby przywrócić dane na ten sam komputer](./media/backup-azure-restore-system-state/samemachine.png)

4. W okienku **Wybierz tryb odzyskiwania** wybierz pozycję **stan systemu** , a następnie kliknij przycisk **dalej**.

    ![Przeglądaj pliki](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. W kalendarzu w oknie **Wybieranie woluminu i daty** wybierz punkt odzyskiwania.

    Można przywrócić z dowolnego punktu odzyskiwania w czasie. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Po wybraniu daty, jeśli dostępne są wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

    ![Wolumin i Data](./media/backup-azure-restore-system-state/select-date.png)

6. Po wybraniu punktu odzyskiwania do przywrócenia kliknij przycisk **dalej**.

    Azure Backup instaluje lokalny punkt odzyskiwania i używa go jako woluminu odzyskiwania.

7. W następnym okienku określ miejsce docelowe dla odzyskiwanych plików stanu systemu, a następnie kliknij przycisk **Przeglądaj** , aby otworzyć Eksploratora Windows i znaleźć pliki i foldery, których chcesz użyć. Opcja **Utwórz kopie, aby mieć obie wersje**, tworzy kopie poszczególnych plików w istniejącym archiwum plików stanu systemu zamiast tworzyć kopię całego archiwum stanu systemu.

    ![Opcje odzyskiwania](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Sprawdź szczegóły odzyskiwania w okienku **potwierdzenia** i kliknij przycisk **Odzyskaj**.

   ![Kliknij przycisk Odzyskaj, aby potwierdzić akcję odzyskania](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Skopiuj katalog *WindowsImageBackup* w miejscu docelowym odzyskiwania do niekrytycznego woluminu serwera. Zwykle wolumin systemu operacyjnego Windows jest woluminem krytycznym.

10. Po pomyślnym zakończeniu odzyskiwania postępuj zgodnie z instrukcjami w sekcji, [Zastosuj przywrócone pliki stanu systemu do systemu Windows Server](backup-azure-restore-system-state.md), aby zakończyć proces odzyskiwania stanu systemu.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Odzyskiwanie plików stanu systemu do alternatywnego serwera

Jeśli system Windows Server jest uszkodzony lub niedostępny i chcesz przywrócić stan stabilny przez Odzyskiwanie stanu systemu Windows Server, można przywrócić stan systemu uszkodzonego serwera z innego serwera. Wykonaj następujące kroki, aby przywrócić stan systemu na osobnym serwerze.  

Terminologia użyta w tych krokach obejmuje:

* *Maszyna źródłowa* — oryginalna maszyna, z której wykonano kopię zapasową, która jest obecnie niedostępna.
* *Maszyna docelowa* — maszyna, do której dane są odzyskiwane.
* *Przykładowy magazyn* — magazyn Recovery Services, do którego zarejestrowano *maszynę źródłową* i *maszynę docelową* . <br/>

> [!NOTE]
> Kopie zapasowe wykonane z jednego komputera nie mogą zostać przywrócone do maszyny z wcześniejszą wersją systemu operacyjnego. Na przykład kopie zapasowe wykonane z komputera z systemem Windows Server 2016 nie mogą zostać przywrócone do systemu Windows Server 2012 R2. Jednak jest możliwe odwracanie. Do przywrócenia systemu Windows Server 2016 można użyć kopii zapasowych z systemu Windows Server 2012 R2.
>

1. Otwórz przystawkę **Microsoft Azure Backup** na *maszynie docelowej*.
2. Upewnij się, że *maszyna docelowa* i *maszyna źródłowa* są zarejestrowani do tego samego magazynu Recovery Services.
3. Kliknij pozycję **Odzyskaj dane** , aby zainicjować przepływ pracy.
4. Wybierz **inny serwer**

    ![Inny serwer](./media/backup-azure-restore-system-state/anotherserver.png)

5. Podaj plik poświadczeń magazynu, który odnosi się do *przykładowego magazynu*. Jeśli plik poświadczeń magazynu jest nieprawidłowy (lub wygasł), Pobierz nowy plik poświadczeń magazynu z *przykładowego magazynu* w Azure Portal. Po udostępnieniu pliku poświadczeń magazynu zostanie wyświetlony magazyn Recovery Services skojarzony z plikiem poświadczeń magazynu.

6. W okienku wybierz serwer kopii zapasowej wybierz *maszynę źródłową* z listy wyświetlanych maszyn.
7. W okienku wybierz tryb odzyskiwania wybierz pozycję **stan systemu** i kliknij przycisk **dalej**.

    ![Wyszukaj](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. W kalendarzu w okienku **Wybierz wolumin i datę** wybierz punkt odzyskiwania. Można przywrócić z dowolnego punktu odzyskiwania w czasie. **Pogrubione** daty wskazują dostępność co najmniej jednego punktu odzyskiwania. Po wybraniu daty, jeśli dostępne są wiele punktów odzyskiwania, wybierz konkretny punkt odzyskiwania z menu rozwijanego **czas** .

    ![Wyszukaj elementy](./media/backup-azure-restore-system-state/select-date.png)

9. Po wybraniu punktu odzyskiwania do przywrócenia kliknij przycisk **dalej**.

10. W okienku **Wybierz tryb odzyskiwania stanu systemu** określ miejsce docelowe odzyskiwania plików stanu systemu, a następnie kliknij przycisk **dalej**.

    ![Szyfrowanie](./media/backup-azure-restore-system-state/recover-as-files.png)

    Opcja **Utwórz kopie, aby mieć obie wersje**, tworzy kopie poszczególnych plików w istniejącym archiwum plików stanu systemu zamiast tworzyć kopię całego archiwum stanu systemu.

11. Sprawdź szczegóły odzyskiwania w okienku potwierdzenia i kliknij przycisk **Odzyskaj**.

    ![Kliknij przycisk Odzyskaj, aby potwierdzić proces odzyskiwania](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Skopiuj katalog *WindowsImageBackup* do niekrytycznego woluminu serwera (na przykład D: \) . Zwykle wolumin systemu operacyjnego Windows jest woluminem krytycznym.

13. Aby zakończyć proces odzyskiwania, należy skorzystać z poniższej sekcji, aby [zastosować przywrócone pliki stanu systemu w systemie Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Stosowanie przywróconego stanu systemu w systemie Windows Server

Po odzyskaniu stanu systemu jako plików przy użyciu usługi Azure Recovery Services Agent Użyj narzędzia Kopia zapasowa systemu Windows Server, aby zastosować odzyskany stan systemu do systemu Windows Server. Narzędzie Kopia zapasowa systemu Windows Server jest już dostępne na serwerze. Poniższe kroki wyjaśniają, jak zastosować odzyskany stan systemu.

1. Użyj następujących poleceń, aby ponownie uruchomić serwer w *trybie naprawy usług katalogowych*. W wierszu polecenia z podwyższonym poziomem uprawnień:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Po ponownym uruchomieniu Otwórz przystawkę Kopia zapasowa systemu Windows Server. Jeśli nie wiesz, gdzie zainstalowano przystawkę, przeszukaj komputer lub serwer pod kątem **kopia zapasowa systemu Windows Server**.

    Aplikacja klasyczna zostanie wyświetlona w wynikach wyszukiwania. Jeśli nie jest wyświetlany lub wystąpią błędy podczas otwierania aplikacji, należy zainstalować **funkcje kopia zapasowa systemu Windows Server**i składniki zależne znajdujące się poniżej, które są dostępne w **Kreatorze dodawania funkcji** w programie **Menedżer serwera**.

3. W przystawce wybierz pozycję **lokalna kopia zapasowa**.

    ![Wybierz pozycję lokalna kopia zapasowa do przywrócenia](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. W lokalnej konsoli kopii zapasowej w **okienku Akcje**kliknij polecenie **Odzyskaj** , aby otworzyć Kreatora odzyskiwania.

5. Wybierz opcję, kopię **zapasową przechowywaną w innej lokalizacji**, a następnie kliknij przycisk **dalej**.

   ![Wybierz, aby odzyskać do innego serwera](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Podczas określania typu lokalizacji wybierz opcję **zdalny folder udostępniony** , jeśli kopia zapasowa stanu systemu została odzyskana na innym serwerze. Jeśli stan systemu został odzyskany lokalnie, wybierz pozycję **dyski lokalne**.

    ![Wybierz, czy chcesz przeprowadzić odzyskiwanie z serwera lokalnego czy innego](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Wprowadź ścieżkę do katalogu *WindowsImageBackup* lub wybierz dysk lokalny zawierający ten katalog (na przykład D:\WindowsImageBackup) odzyskany jako część odzyskiwania plików stanu systemu za pomocą usługi Azure Recovery Services Agent i kliknij przycisk **dalej**.

    ![ścieżka do pliku udostępnionego](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Wybierz wersję stanu systemu, którą chcesz przywrócić, a następnie kliknij przycisk **dalej**.

9. W okienku wybierz typ odzyskiwania wybierz pozycję **stan systemu** i kliknij przycisk **dalej**.

10. W polu Lokalizacja odzyskiwania stanu systemu wybierz opcję **Oryginalna lokalizacja**, a następnie kliknij przycisk **dalej**.

11. Przejrzyj szczegóły potwierdzenia, sprawdź ustawienia ponownego rozruchu, a następnie kliknij przycisk **Odzyskaj** , aby zastosować przywrócone pliki stanu systemu.

    ![Uruchom pliki stanu systemu przywracania](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Specjalne zagadnienia dotyczące odzyskiwania stanu systemu na serwerze Active Directory

Kopia zapasowa stanu systemu zawiera Active Directory danych. Wykonaj następujące kroki, aby przywrócić poprzedni stan usługi domena usługi Active Directory (AD DS) z jej bieżącego stanu.

1. Uruchom ponownie kontroler domeny w trybie przywracania usług katalogowych (DSRM).
2. Wykonaj kroki opisane [tutaj](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) , aby użyć poleceń cmdlet kopia zapasowa systemu Windows Server do odzyskania AD DS.

## <a name="troubleshoot-failed-system-state-restore"></a>Rozwiązywanie problemów w przypadku niepowodzenia przywracania stanu systemu

Jeśli poprzedni proces stosowania stanu systemu nie zakończy się pomyślnie, użyj środowiska odzyskiwania systemu Windows (win RE) do odzyskania serwera z systemem Windows. Poniższe kroki wyjaśniają sposób odzyskiwania przy użyciu programu win RE. Tej opcji należy używać tylko wtedy, gdy system Windows Server nie uruchamia się normalnie po przywróceniu stanu systemu. Poniższy proces usuwa dane niesystemowe, należy zachować ostrożność.

1. Uruchom system Windows Server w środowisku odzyskiwania systemu Windows (win RE).

2. Wybierz pozycję Rozwiązywanie problemów z trzema dostępnymi opcjami.

    ![menu Otwieranie](./media/backup-azure-restore-system-state/winre-1.png)

3. Na ekranie **Opcje zaawansowane** wybierz pozycję **wiersz polecenia** i podaj nazwę użytkownika i hasło administratora serwera.

   ![menu Otwieranie](./media/backup-azure-restore-system-state/winre-2.png)

4. Podaj nazwę użytkownika i hasło administratora serwera.

    ![menu Otwieranie](./media/backup-azure-restore-system-state/winre-3.png)

5. Gdy otworzysz wiersz polecenia w trybie administratora, uruchom następujące polecenie, aby pobrać wersje kopii zapasowej stanu systemu.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Pobierz wersje kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-4.png)

6. Uruchom następujące polecenie, aby uzyskać wszystkie woluminy dostępne w kopii zapasowej.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Pobierz wersje kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-5.png)

7. Poniższe polecenie odzyska wszystkie woluminy, które są częścią kopii zapasowej stanu systemu. Należy zauważyć, że ten krok odzyskuje tylko woluminy krytyczne, które są częścią stanu systemu. Wszystkie dane niesystemowe są wymazywane.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Pobierz wersje kopii zapasowej stanu systemu](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Następne kroki

* Teraz, gdy odzyskasz pliki i foldery, możesz [zarządzać kopiami zapasowymi](backup-azure-manage-windows-server.md).
