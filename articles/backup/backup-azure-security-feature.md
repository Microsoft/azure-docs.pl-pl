---
title: Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe
description: Dowiedz się, jak używać funkcji zabezpieczeń w programie Azure Backup, aby tworzyć kopie zapasowe bardziej bezpieczne
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 5a408dc07e83e790a63f8a252d4ed3f84bf66be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181684"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe, które używają Azure Backup

Problemy dotyczące zabezpieczeń, takie jak złośliwe oprogramowanie, programy wymuszającego okup i wtargnięcie, zwiększają się. Te problemy z zabezpieczeniami mogą być kosztowne, w odniesieniu do pieniędzy i danych. Aby ochronić przed takimi atakami, Azure Backup teraz zapewnia funkcje zabezpieczeń, które ułatwiają ochronę hybrydowych kopii zapasowych. W tym artykule opisano sposób włączania i używania tych funkcji przy użyciu agenta Recovery Services platformy Azure i Azure Backup Server. Do tych funkcji należą:

- **Zapobieganie**. Dodatkowa warstwa uwierzytelniania jest dodawana zawsze wtedy, gdy jest wykonywane krytyczne operacje, takie jak zmiana hasła. Ta weryfikacja polega na zapewnieniu, że operacje te mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure.
- **Generowanie alertów**. Powiadomienie e-mail jest wysyłane do administratora subskrypcji po każdym wystąpieniu krytycznej operacji, takiej jak usuwanie danych kopii zapasowej. Ta wiadomość e-mail gwarantuje, że użytkownik będzie powiadamiany szybko o takich akcjach.
- **Odzyskiwanie**. Usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie, dlatego nie ma utraty danych, nawet jeśli wystąpi atak. Ponadto do ochrony przed uszkodzeniem danych są utrzymywane większą liczbę punktów odzyskiwania.

> [!NOTE]
> Jeśli używasz kopii zapasowej maszyny wirtualnej jako usługi (IaaS), nie należy włączać funkcji zabezpieczeń. Te funkcje nie są jeszcze dostępne do tworzenia kopii zapasowych maszyn wirtualnych IaaS, dlatego włączenie ich nie będzie miało żadnego wpływu. Funkcje zabezpieczeń powinny być włączone tylko wtedy, gdy są używane: <br/>
>
> - **Agent Azure Backup**. Minimalna wersja agenta: 2.0.9052. Po włączeniu tych funkcji należy przeprowadzić uaktualnienie do tej wersji agenta, aby wykonać krytyczne operacje. <br/>
> - **Azure Backup Server**. Minimalna wersja agenta Azure Backup: 2.0.9052 z aktualizacją Azure Backup Server Update 1. <br/>
> - **Data Protection Manager programu System Center**. Minimalna wersja agenta Azure Backup: 2.0.9052 z Data Protection Manager 2012 R2 UR12 lub Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Te funkcje są dostępne tylko dla magazynu Recovery Services. Wszystkie nowo utworzone magazyny Recovery Services mają domyślnie włączone te funkcje. W przypadku istniejących magazynów Recovery Services Użytkownicy włączają te funkcje, wykonując kroki opisane w poniższej sekcji. Po włączeniu funkcji mają one zastosowanie do wszystkich komputerów agentów Recovery Services, wystąpień Azure Backup Server i serwerów Data Protection Manager zarejestrowanych w magazynie. Włączenie tego ustawienia jest akcją jednorazową i nie można wyłączyć tych funkcji po ich włączeniu.
>

## <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Jeśli tworzysz magazyn Recovery Services, możesz użyć wszystkich funkcji zabezpieczeń. Jeśli pracujesz z istniejącym magazynem, Włącz funkcje zabezpieczeń, wykonując następujące czynności:

1. Zaloguj się do Azure Portal przy użyciu poświadczeń platformy Azure.
2. Wybierz pozycję **Przeglądaj**, a następnie wpisz **Recovery Services**.

    ![Zrzut ekranu przedstawiający opcję Azure Portal Browse](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Zostanie wyświetlona lista magazynów Usług odzyskiwania. Z tej listy wybierz magazyn. Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
3. Z listy elementów, które pojawiają się w magazynie, w obszarze **Ustawienia**wybierz pozycję **Właściwości**.

    ![Zrzut ekranu przedstawiający opcje magazynu Recovery Services](./media/backup-azure-security-feature/vault-list-properties.png)
4. W obszarze **Ustawienia zabezpieczeń**wybierz pozycję **Aktualizuj**.

    ![Zrzut ekranu przedstawiający właściwości magazynu Recovery Services](./media/backup-azure-security-feature/security-settings-update.png)

    Link aktualizacji otwiera okienko **Ustawienia zabezpieczeń** , które zawiera podsumowanie funkcji i umożliwia ich włączenie.
5. Z listy rozwijanej **zostały skonfigurowane Multi-Factor Authentication platformy Azure?** wybierz wartość, aby potwierdzić, że [usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)została włączona. Jeśli jest włączona, zostanie wyświetlony monit o uwierzytelnienie z innego urządzenia (na przykład telefonu komórkowego) podczas logowania się do Azure Portal.

   W przypadku wykonywania krytycznych operacji w usłudze Backup należy wprowadzić numer PIN zabezpieczeń, który jest dostępny na Azure Portal. Włączenie usługi Azure Multi-Factor Authentication powoduje dodanie warstwy zabezpieczeń. Dostęp do Azure Portal mają tylko autoryzowani użytkownicy z prawidłowymi poświadczeniami platformy Azure i uwierzytelniani z drugiego urządzenia.
6. Aby zapisać ustawienia zabezpieczeń, wybierz pozycję **Włącz** i wybierz pozycję **Zapisz**. Możesz wybrać opcję **Włącz** tylko po wybraniu wartości z listy czy **skonfigurować usługę Azure Multi-Factor Authentication?** w poprzednim kroku.

    ![Zrzut ekranu ustawień zabezpieczeń](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Odzyskaj usunięte dane kopii zapasowej

Kopia zapasowa zachowuje usunięte dane kopii zapasowej przez dodatkowe 14 dni i nie usuwa go natychmiast po wykonaniu operacji **Zatrzymaj tworzenie kopii zapasowej z usuwaniem danych** . Aby przywrócić te dane w ciągu 14 dni, wykonaj następujące czynności, w zależności od tego, czego używasz:

Dla użytkowników **usługi Azure Recovery Services Agent** :

1. Jeśli komputer, na którym wykonywane są kopie zapasowe, jest nadal dostępny, ponownie Włącz ochronę usuniętych źródeł danych i użyj funkcji [Odzyskaj dane na tym samym komputerze](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) w usłudze Azure Recovery Services, aby odzyskać ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten komputer nie jest dostępny, użyj funkcji [Odzyskaj do alternatywnej maszyny](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) , aby użyć innego komputera z usługą Azure Recovery Services, aby uzyskać te dane.

Dla **Azure Backup Server** użytkowników:

1. Jeśli serwer, na którym wykonywane są kopie zapasowe, jest nadal dostępny, ponownie Włącz ochronę usuniętych źródeł danych i użyj funkcji **Odzyskaj dane** , aby odzyskać ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten serwer nie jest dostępny, użyj funkcji [Odzyskaj dane z innego Azure Backup Server](backup-azure-alternate-dpm-server.md) , aby użyć innego wystąpienia Azure Backup Server w celu uzyskania tych danych.

Dla **Data Protection Manager** użytkowników:

1. Jeśli serwer, na którym wykonywane są kopie zapasowe, jest nadal dostępny, ponownie Włącz ochronę usuniętych źródeł danych i użyj funkcji **Odzyskaj dane** , aby odzyskać ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten serwer nie jest dostępny, [do pobrania tych danych Użyj innego](backup-azure-alternate-dpm-server.md) serwera Data Protection Manager.

## <a name="prevent-attacks"></a>Zapobiegaj atakom

Dodano testy, aby upewnić się, że tylko Prawidłowi użytkownicy mogą wykonywać różne operacje. Obejmują one Dodawanie dodatkowej warstwy uwierzytelniania i utrzymywanie minimalnego zakresu przechowywania na potrzeby odzyskiwania.

### <a name="authentication-to-perform-critical-operations"></a>Uwierzytelnianie w celu wykonywania operacji krytycznych

W ramach dodawania dodatkowej warstwy uwierzytelniania dla operacji krytycznych zostanie wyświetlony monit o wprowadzenie zabezpieczającego numeru PIN **w przypadku przetrzymywania ochrony przy użyciu operacji usuwania danych** i **zmiany hasła** .

> [!NOTE]
>
> Obecnie zabezpieczający numer PIN nie jest obsługiwany w przypadku **zatrzymywania ochrony przy użyciu usuwania danych** dla programu DPM i serwera usługi MAB.

Aby odebrać ten numer PIN:

1. Zaloguj się do Portalu Azure.
2. Przejdź do **Recovery Services**  >  **Settings**  >  **Właściwości**ustawień magazynu.
3. W obszarze **zabezpieczający numer PIN**wybierz pozycję **Generuj**. Spowoduje to otwarcie okienka zawierającego numer PIN, który ma zostać wprowadzony w interfejsie użytkownika agenta usługi Azure Recovery Services.
    Ten kod PIN jest prawidłowy tylko przez pięć minut i jest generowany automatycznie po upływie tego okresu.

### <a name="maintain-a-minimum-retention-range"></a>Utrzymywanie minimalnego zakresu przechowywania

Aby upewnić się, że zawsze jest dostępna prawidłowa liczba punktów odzyskiwania, dodano następujące sprawdzenia:

- W przypadku codziennego przechowywania należy wykonać co najmniej **siedem** dni przechowywania.
- W przypadku przechowywania cotygodniowego należy wykonać co najmniej **cztery** tygodnie przechowywania.
- W przypadku przechowywania miesięcznego należy wykonać co najmniej **trzy** miesiące przechowywania.
- W przypadku przechowywania corocznego należy wykonać co najmniej **jeden** rok przechowywania.

## <a name="notifications-for-critical-operations"></a>Powiadomienia dla operacji krytycznych

Zazwyczaj w przypadku wykonywania operacji krytycznej administrator subskrypcji otrzymuje powiadomienie e-mail z informacjami o tej operacji. Można skonfigurować dodatkowych adresatów wiadomości e-mail dla tych powiadomień przy użyciu Azure Portal.

Funkcje zabezpieczeń wymienione w tym artykule zapewniają mechanizmy obrony przed skierowanymi atakami. Co ważniejsze, jeśli wystąpi atak, te funkcje zapewniają możliwość odzyskania danych.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami

| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
| Zmiana zasad |Nie można zmodyfikować zasad tworzenia kopii zapasowych. Błąd: bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft. |**Przyczyna:**<br/>Ten błąd pojawia się, gdy ustawienia zabezpieczeń są włączone, próbując zmniejszyć zakres przechowywania poniżej wartości minimalnych określonych powyżej, a użytkownik jest w nieobsługiwanej wersji (obsługiwane wersje są określone w pierwszej notatce tego artykułu). <br/>**Zalecana akcja:**<br/> W takim przypadku należy ustawić okres przechowywania powyżej minimalnego podanego okresu przechowywania (siedem dni dziennie, czterech tygodni przez tydzień, trzy tygodnie przez rok i rok), aby kontynuować aktualizacje związane z zasadami. Opcjonalnie, preferowanym podejściem będzie zaktualizowanie agenta kopii zapasowej, Azure Backup Server i/lub UR do programu DPM w celu wykorzystania wszystkich aktualizacji zabezpieczeń. |
| Zmień hasło |Wprowadzony numer PIN zabezpieczeń jest niepoprawny. (IDENTYFIKATOR: 100130) Podaj poprawny zabezpieczający numer PIN, aby ukończyć tę operację. |**Przyczyna:**<br/> Ten błąd jest dostarczany po wprowadzeniu nieprawidłowego lub wygasłego numeru PIN zabezpieczeń podczas wykonywania operacji krytycznej (np. zmiany hasła). <br/>**Zalecana akcja:**<br/> Aby ukończyć tę operację, musisz wprowadzić prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do Recovery Services magazynu > ustawienia > właściwości > Generuj zabezpieczający numer PIN. Użyj tego numeru PIN, aby zmienić hasło. |
| Zmień hasło |Operacja nie powiodła się. IDENTYFIKATOR: 120002 |**Przyczyna:**<br/>Ten błąd pojawia się, gdy ustawienia zabezpieczeń są włączone, próba zmiany hasła i wykorzystasz z nieobsługiwanej wersji (prawidłowe wersje określone w pierwszej notatce tego artykułu).<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do minimalnej wersji: 2.0.9052, Azure Backup Server do aktualizacji minimalnej 1 i/lub DPM do minimum DPM 2012 R2 UR12 lub DPM 2016 UR2 (Pobierz linki poniżej), a następnie wprowadzić prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do Recovery Services magazynu > ustawienia > właściwości > Generuj zabezpieczający numer PIN. Użyj tego numeru PIN, aby zmienić hasło. |

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij pracę z magazynem usługi Azure Recovery Services](backup-azure-vms-first-look-arm.md) , aby włączyć te funkcje.
- [Pobierz najnowszego agenta Recovery Services platformy Azure](https://aka.ms/azurebackup_agent) , aby chronić komputery z systemem Windows i zabezpieczyć dane kopii zapasowej przed atakami.
- [Pobierz najnowszą Azure Backup Server,](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) aby pomóc w ochronie obciążeń i zabezpieczyć dane kopii zapasowej przed atakami.
- [Pobierz UR12 dla programu System center 2012 Data Protection Manager R2](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) lub [Pobierz UR2 dla programu system Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) , aby pomóc w ochronie obciążeń i zabezpieczyć dane kopii zapasowej przed atakami.
