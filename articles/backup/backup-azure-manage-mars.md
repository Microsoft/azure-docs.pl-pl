---
title: Monitorowanie kopii zapasowych agenta MARS i zarządzanie nimi
description: Dowiedz się, jak monitorować kopie zapasowe agenta Microsoft Azure Recovery Services (MARS) przy użyciu usługi Azure Backup service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515026"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Zarządzanie Microsoft Azure kopii zapasowych agenta usługi Azure Backup Recovery Services (MARS)

W tym artykule opisano sposób zarządzania plikami i folderami, których kopię zapasową można samodzielnie Microsoft Azure agenta usługi Recovery Services.

## <a name="modify-a-backup-policy"></a>Modyfikowanie zasad kopii zapasowych

Podczas modyfikowania zasad tworzenia kopii zapasowych można dodawać nowe elementy, usuwać istniejące elementy z kopii zapasowej lub wykluczać pliki z kopii zapasowej przy użyciu ustawień wykluczania.

- **Opcja Dodaj elementy** służy tylko do dodawania nowych elementów do kopii zapasowej. Aby usunąć istniejące elementy, użyj **opcji Usuń elementy** lub Ustawienia **wykluczenia.**  
- **Opcja Usuń** elementy umożliwia usunięcie elementów z kopii zapasowej.
  - Użyj **ustawień wykluczeń,** aby usunąć wszystkie elementy w woluminie zamiast **usunąć elementy**.
  - Wyczyszczenie wszystkich opcji w woluminie powoduje, że stare kopie zapasowe elementów są zachowywane zgodnie z ustawieniami przechowywania w czasie ostatniej kopii zapasowej, bez zakresu modyfikacji.
  - Ponowne zaznaczenie tych elementów prowadzi do tworzenia pierwszej pełnej kopii zapasowej, a nowe zmiany zasad nie są stosowane do starych kopii zapasowych.
  - Usunięcie zaznaczenia całego woluminu zachowuje przeszłe kopie zapasowe bez żadnego zakresu modyfikowania zasad przechowywania.
- **Ustawienia wykluczenia** używają tej opcji, aby wykluczyć określone elementy z kopii zapasowej.

### <a name="add-new-items-to-existing-policy"></a>Dodawanie nowych elementów do istniejących zasad

1. Na **stronie Akcje** wybierz pozycję Zaplanuj tworzenie kopii **zapasowej.**

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na **karcie Wybierz element zasad** wybierz pozycję Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i **folderów,** a następnie wybierz pozycję **Dalej.**

    ![Wybieranie elementów zasad](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na **karcie Modyfikowanie lub zatrzymywanie tworzenia kopii zapasowej** wybierz pozycję Wprowadzaj zmiany w **elementach kopii zapasowej lub godzinach,** a następnie wybierz pozycję **Dalej.**

    ![Modyfikowanie lub planowanie tworzenia kopii zapasowej](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na **karcie Wybierz elementy do kopii zapasowej** wybierz pozycję Dodaj **elementy,** aby dodać elementy, których kopię zapasową chcesz utworzyć.

    ![Modyfikowanie lub planowanie tworzenia kopii zapasowej w celu dodawania elementów](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. W **oknie Wybieranie** elementów wybierz pliki lub foldery, które chcesz dodać, a następnie wybierz przycisk **OK.**

    ![Wybieranie elementów](./media/backup-azure-manage-mars/select-item.png)

6. Wykonaj następne kroki i wybierz pozycję **Zakończ,** aby zakończyć operację.

### <a name="add-exclusion-rules-to-existing-policy"></a>Dodawanie reguł wykluczeń do istniejących zasad

Możesz dodać reguły wykluczania, aby pominąć pliki i foldery, dla których nie chcesz wrócić do kopii zapasowej. Można to zrobić podczas definiowania nowych zasad lub modyfikowania istniejących zasad.

1. W okienku Akcje wybierz pozycję **Zaplanuj tworzenie kopii zapasowej.** Przejdź do pozycji **Wybierz elementy do kopii zapasowej i** wybierz pozycję Ustawienia **wykluczenia.**

    ![Ustawienia wykluczenia](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. W **ustawieniach wykluczenia** wybierz pozycję **Dodaj wykluczenie.**

    ![Dodawanie wykluczenia](./media/backup-azure-manage-mars/add-exclusion.png)

3. Z **listy Wybierz elementy do wykluczenia** przejrzyj pliki i foldery, wybierz elementy, które chcesz wykluczyć, a następnie wybierz przycisk **OK.**

    ![Wybieranie elementów do wykluczenia](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Domyślnie wszystkie **podfoldery w** wybranych folderach są wykluczone. Możesz to zmienić, wybierając pozycję **Tak** lub **Nie.** Można edytować i określać typy plików do wykluczenia, jak pokazano poniżej:

    ![Wybieranie typów podfolderów](./media/backup-azure-manage-mars/subfolders-type.png)

5. Wykonaj następne kroki i wybierz pozycję **Zakończ,** aby zakończyć operację.

### <a name="remove-items-from-existing-policy"></a>Usuwanie elementów z istniejących zasad

1. W okienku Akcje wybierz pozycję **Zaplanuj tworzenie kopii zapasowej.** Przejdź do pozycji **Wybierz elementy, aby utworzyć kopię zapasową**. Z listy wybierz pliki i foldery, które chcesz usunąć z harmonogramu tworzenia kopii zapasowych, a następnie wybierz **pozycję Usuń elementy.**

    ![Wybieranie elementów do usunięcia](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Podczas całkowitego usuwania woluminu z zasad należy zachować ostrożność.  Jeśli musisz dodać go ponownie, będzie on traktowany jako nowy wolumin. Następna zaplanowana kopia zapasowa wykona początkową kopię zapasową (pełną kopię zapasową) zamiast przyrostowej kopii zapasowej. Jeśli chcesz tymczasowo usunąć i dodać elementy później, zaleca  się użycie ustawień  wykluczeń zamiast usuwania elementów w celu zapewnienia przyrostowej kopii zapasowej zamiast pełnej kopii zapasowej.

2. Wykonaj następne kroki i wybierz pozycję **Zakończ,** aby zakończyć operację.

## <a name="stop-protecting-files-and-folder-backup"></a>Zatrzymywanie ochrony kopii zapasowej plików i folderów

Istnieją dwa sposoby zatrzymania ochrony kopii zapasowej plików i folderów:

- **Zatrzymaj ochronę i zachowaj dane kopii zapasowej.**
  - Ta opcja spowoduje zatrzymanie ochrony wszystkich przyszłych zadań tworzenia kopii zapasowej.
  - Azure Backup będzie nadal zachowywać wszystkie istniejące punkty odzyskiwania.  
  - Będzie można przywrócić dane kopii zapasowej dla niewydajnych punktów odzyskiwania.
  - Jeśli zdecydujesz się wznowić ochronę, możesz użyć opcji *Harmonogram ponownego włączania kopii zapasowych.* Następnie dane zostaną zachowane na podstawie nowych zasad przechowywania.
- **Zatrzymaj ochronę i usuń dane kopii zapasowej.**
  - Ta opcja spowoduje, że wszystkie przyszłe zadania tworzenia kopii zapasowej nie będą chronić danych i będą usuwać wszystkie punkty odzyskiwania.
  - Otrzymasz wiadomość e-mail z alertem o usunięciu danych kopii zapasowej z komunikatem Twoje dane dla tego elementu *kopii zapasowej zostały usunięte. Te dane będą tymczasowo* dostępne przez 14 dni, po czym zostaną trwale usunięte, a zalecana akcja Ponowne włączania ochrony elementu kopii zapasowej w ciągu 14 dni w celu *odzyskania danych.*
  - Aby wznowić ochronę, należy ponownie w ciągu 14 dni od operacji usuwania.

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymywanie ochrony i zachowywanie danych kopii zapasowej

1. Otwórz konsolę zarządzania usługą MARS, przejdź do **okienka Akcje** i wybierz **pozycję Zaplanuj tworzenie kopii zapasowej.**

    ![Wybieranie harmonogramu tworzenia kopii zapasowej](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stronie **Wybierz element zasad** wybierz pozycję Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i **folderów, a** następnie wybierz pozycję **Dalej.**

    ![Wybieranie elementu zasad](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stronie **Modyfikowanie lub zatrzymywanie zaplanowanej** kopii zapasowej wybierz pozycję Zatrzymaj używanie tego harmonogramu tworzenia kopii zapasowych, ale zachowaj przechowywane kopie zapasowe do momentu, gdy harmonogram **zostanie ponownie aktywowany.** Następnie wybierz pozycję **Dalej**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Na **stronie Wstrzymaj zaplanowaną** kopię zapasową przejrzyj informacje i wybierz pozycję **Zakończ.**

    ![Wstrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. W **chmurze Modyfikowanie postępu** tworzenia kopii zapasowej sprawdź, czy zaplanowana pauza kopii zapasowej jest w stanie sukcesu, a następnie **wybierz pozycję Zamknij,** aby zakończyć.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymywanie ochrony i usuwanie danych kopii zapasowej

1. Otwórz konsolę zarządzania usługą MARS, przejdź do **okienka Akcje** i wybierz pozycję **Zaplanuj tworzenie kopii zapasowej.**
2. Na stronie **Modyfikowanie lub zatrzymywanie zaplanowanej kopii zapasowej** wybierz pozycję Zatrzymaj korzystanie z tego harmonogramu tworzenia kopii zapasowych i **usuń wszystkie przechowywane kopie zapasowe.** Następnie wybierz pozycję **Dalej**.

    ![Modyfikowanie lub zatrzymywanie zaplanowanej kopii zapasowej.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymywanie zaplanowanej kopii zapasowej** wybierz pozycję **Zakończ.**

    ![Zatrzymaj zaplanowaną kopię zapasową i wybierz pozycję Zakończ](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. W tym celu najpierw zaloguj się do Azure Portal.
5. Przejdź do **właściwości ustawień magazynu** usługi Recovery  >    >  Services.
6. W **obszarze Numer PIN zabezpieczeń** wybierz pozycję **Generuj**. Skopiuj ten numer PIN. Numer PIN jest ważny tylko przez pięć minut.
7. W konsoli zarządzania wklej numer PIN, a następnie wybierz przycisk **OK.**

    ![Wygeneruj numer PIN zabezpieczeń.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikowanie postępu tworzenia** kopii zapasowej zostanie wyświetlony następujący komunikat: Usunięte dane kopii zapasowej *będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte.*  

    ![Modyfikowanie postępu tworzenia kopii zapasowej](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki z portalu.

## <a name="re-enable-protection"></a>Ponowne włączanie ochrony

Jeśli zatrzymano ochronę przy zachowaniu danych i padła decyzja o wznowieniu ochrony, można ponownie włączyć harmonogram tworzenia kopii zapasowych przy użyciu modyfikowania zasad tworzenia kopii zapasowych.

1. Na **stronie Akcje** wybierz pozycję Zaplanuj tworzenie kopii **zapasowej.**
1. Wybierz **pozycję Włącz ponownie harmonogram tworzenia kopii zapasowych. Możesz również zmodyfikować elementy kopii zapasowej lub godziny i** wybrać pozycję **Dalej.**<br>

    ![Harmonogram ponownego włączania kopii zapasowych](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Na **stronie Wybierz elementy do kopii zapasowej** wybierz pozycję **Dalej.**

    ![Wybieranie elementów do kopii zapasowej](./media/backup-azure-manage-mars/re-enable-next.png)
1. W **chmurze Określ harmonogram tworzenia** kopii zapasowych określ harmonogram tworzenia kopii zapasowych, a następnie wybierz pozycję **Dalej.**
1. Na **stronie Wybierz zasady przechowywania** określ czas przechowywania i wybierz pozycję **Dalej.**
1. Na koniec na **ekranie Potwierdzenie** przejrzyj szczegóły zasad i wybierz pozycję **Zakończ.**

## <a name="re-generate-passphrase"></a>Ponowne generowanie hasła

Hasło służy do szyfrowania i odszyfrowywania danych podczas kopii zapasowej lub przywracania maszyny lokalnej lub lokalnej przy użyciu agenta MARS na platformie Azure lub z platformy Azure. Jeśli hasło zostanie utracone lub zapomnisz, możesz ponownie wygenerować hasło (pod warunkiem, że maszyna jest nadal zarejestrowana w magazynie usługi Recovery Services i kopia zapasowa jest skonfigurowana) przez następujące kroki:

1. Z konsoli agenta MARS przejdź do **okienka Akcje** Zmień właściwości  >  **>.** Następnie przejdź do **karty Szyfrowanie.**<br>
1. Zaznacz **pole wyboru Zmień hasło.**<br>
1. Wprowadź nowe hasło lub wybierz pozycję **Generuj hasło.**
1. Wybierz **pozycję Przeglądaj,** aby zapisać nowe hasło.

    ![Wygeneruj hasło.](./media/backup-azure-manage-mars/passphrase.png)

1. Wybierz **przycisk OK,** aby zastosować zmiany.  Jeśli funkcja [zabezpieczeń jest](./backup-azure-security-feature.md#enable-security-features) włączona na Azure Portal magazynu usługi Recovery Services, zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń. Aby otrzymać numer PIN, wykonaj kroki opisane w tym [artykule.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Wklej numer PIN zabezpieczeń z portalu i wybierz przycisk **OK,** aby zastosować zmiany.<br>

    ![Wklejanie numeru PIN zabezpieczeń](./media/backup-azure-manage-mars/passphrase2.png)
1. Upewnij się, że hasło jest bezpiecznie zapisane w lokalizacji alternatywnej (innej niż maszyna źródłowa), najlepiej w Azure Key Vault. Śledź wszystkie hasła, jeśli masz wiele maszyn, których kopię zapasową kopię zapasową mają agenci MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Zarządzanie danymi kopii zapasowej dla niedostępnych maszyn

W tej sekcji omówiono scenariusz, w którym maszyna źródłowa chroniona za pomocą usług MARS nie jest już dostępna, ponieważ została usunięta, uszkodzona, zainfekowana złośliwym oprogramowaniem/oprogramowaniem wymuszającym okup lub zlikwidowana.

W przypadku tych maszyn usługa Azure Backup gwarantuje, że najnowszy punkt odzyskiwania nie wygaśnie (czyli nie zostanie wyczyszowany) zgodnie z regułami przechowywania określonymi w zasadach tworzenia kopii zapasowych. W związku z tym można bezpiecznie przywrócić maszynę.  Rozważ następujące scenariusze, które można wykonać na danych kopii zapasowej:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenariusz 1. Maszyna źródłowa jest niedostępna i nie trzeba już przechowywać danych kopii zapasowej

- Dane kopii zapasowej można usunąć z usługi Azure Portal, korzystając z procedury opisanej [w tym artykule.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenariusz 2. Maszyna źródłowa jest niedostępna i należy zachować dane kopii zapasowej

Zarządzanie zasadami tworzenia kopii zapasowych dla usługi MARS odbywa się za pośrednictwem konsoli usługi MARS, a nie za pośrednictwem portalu. Jeśli musisz rozszerzyć ustawienia przechowywania dla istniejących punktów odzyskiwania przed ich wygaśnięciem, musisz przywrócić maszynę, zainstalować konsolę MARS i rozszerzyć zasady.

- Aby przywrócić maszynę, wykonaj następujące kroki:
  1. [Przywracanie maszyny wirtualnej do alternatywnej maszyny docelowej](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Utwórz ponownie maszynę docelową z taką samą nazwą hosta jak maszyna źródłowa
  1. Zainstaluj agenta i zarejestruj się ponownie w tym samym magazynie przy użyciu tego samego hasła
  1. Uruchom klienta MARS, aby wydłużyć czas przechowywania zgodnie z wymaganiami
- Nowo przywrócona maszyna chroniona za pomocą usługi MARS będzie nadal tworzyć kopie zapasowe.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Konfigurowanie programu antywirusowego dla agenta MARS

Zalecamy następującą konfigurację oprogramowania antywirusowego, aby uniknąć konfliktów z działaniem agenta MARS.

1. **Dodaj wykluczenia ścieżek:** Aby uniknąć spadku wydajności i możliwych konfliktów, wyklucz następujące ścieżki z monitorowania w czasie rzeczywistym przez oprogramowanie antywirusowe:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` podfoldery i
    1. **Folder scratch:** jeśli folder plików scratch nie znajduje się w standardowej lokalizacji, dodaj go również do wykluczeń.  [Zobacz tutaj, aby uzyskać instrukcje](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) określania lokalizacji folderu plików scratch.
1. **Dodawanie wykluczeń** binarnych: Aby uniknąć pogorszenia wydajności działania kopii zapasowej i konsoli, wyklucz procesy dla następujących plików binarnych z monitorowania w czasie rzeczywistym przez oprogramowanie antywirusowe:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Chociaż wykluczenie tych ścieżek będzie wystarczające w przypadku większości oprogramowania antywirusowego, niektóre mogą nadal zakłócać operacje agenta MARS. Jeśli widzisz nieoczekiwane błędy, tymczasowo odinstaluj oprogramowanie antywirusowe i monitoruj go, aby sprawdzić, czy problem zmiejść. Jeśli to rozwiąże problem, skontaktuj się z dostawcą oprogramowania antywirusowego, aby uzyskać pomoc w odpowiedniej konfiguracji produktu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o obsługiwanych scenariuszach i ograniczeniach, zapoznaj się z [macierzą obsługi agenta MARS.](./backup-support-matrix-mars-agent.md)
- Dowiedz się więcej o [zachowaniu przechowywania zasad kopii zapasowych na żądanie.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
- Aby uzyskać odpowiedzi na często zadawane pytania, zobacz często [zadawane pytania dotyczące agenta MARS.](backup-azure-file-folder-backup-faq.yml)
