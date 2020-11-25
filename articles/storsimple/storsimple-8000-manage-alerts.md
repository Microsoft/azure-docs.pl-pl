---
title: Wyświetlanie alertów dla urządzenia z serii StorSimple 8000 i zarządzanie nimi
description: Opisuje warunki i ważność alertów StorSimple, sposób konfigurowania powiadomień o alertach oraz sposób używania usługi StorSimple Menedżer urządzeń do zarządzania alertami.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016849"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Używanie usługi StorSimple Menedżer urządzeń do wyświetlania alertów StorSimple i zarządzania nimi

## <a name="overview"></a>Omówienie

Blok **alertów** w usłudze StorSimple Menedżer urządzeń umożliwia przeglądanie i czyszczenie alertów związanych z urządzeniem StorSimple w czasie rzeczywistym. Z tego bloku można centralnie monitorować problemy z kondycją urządzeń StorSimple i ogólne rozwiązanie Microsoft Azure StorSimple.

W tym samouczku opisano typowe warunki alertu, poziomy ważności alertu i sposób konfigurowania powiadomień o alertach. Oprócz tego zawiera tabele szybkich odwołań alertów, które umożliwiają szybkie lokalizowanie określonego alertu i odpowiednie reagowanie.

![Strona alerty](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertów

Urządzenie StorSimple generuje alerty w odpowiedzi na różne warunki. Poniżej przedstawiono najczęściej spotykane typy warunków alertów:

* **Problemy ze sprzętem** — te alerty informują o kondycji sprzętu. Umożliwiają one sprawdzenie, czy są potrzebne uaktualnienia oprogramowania układowego, jeśli wystąpią problemy z interfejsem sieciowym lub występuje problem z jednym z dysków danych.
* **Problemy z łącznością** — te alerty występują w przypadku trudności związanych z transferem danych. Problemy z komunikacją mogą wystąpić podczas transferu danych do i z konta usługi Azure Storage lub z powodu braku łączności między urządzeniami a usługą StorSimple Menedżer urządzeń. Problemy z komunikacją są nieco trudne do rozwiązania, ponieważ istnieje wiele punktów awarii. Przed kontynuowaniem bardziej zaawansowanego rozwiązywania problemów należy zawsze sprawdzić, czy łączność sieciowa i dostęp do Internetu są dostępne. Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [obszaru Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są generowane, gdy system nie działa optymalnie, na przykład gdy jest w dużym obciążeniu.

Ponadto mogą pojawić się alerty związane z zabezpieczeniami, aktualizacjami lub niepowodzeńmi zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertu

Alerty mają różne poziomy ważności, w zależności od wpływu działania alertu i konieczności odpowiedzi na alert. Poziomy ważności to:

* **Krytyczny** — ten alert jest w odpowiedzi na warunek, który ma wpływ na pomyślną wydajność systemu. Wymagana jest akcja, aby upewnić się, że Usługa StorSimple nie została przerwana.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie został rozpoznany. Należy zbadać sytuację i podjąć wszelkie działania wymagane do wyczyszczenia problemu.
* **Informacje** — ten alert zawiera informacje, które mogą być przydatne do śledzenia systemu i zarządzania nim.

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Możesz wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail o warunkach alertów dla każdego z urządzeń z StorSimple. Ponadto możesz identyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w polu **inni adresaci wiadomości e-mail** , rozdzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresów e-mail na urządzenie.

Po włączeniu powiadomienia e-mail dla urządzenia członkowie listy powiadomień otrzymają wiadomość e-mail za każdym razem, gdy wystąpi alert krytyczny. Komunikaty będą wysyłane z *StorSimple-Alerts-noreply \@ mail.windowsazure.com* i opisują warunek alertu. Adresaci mogą kliknąć przycisk **Anuluj subskrypcję** , aby usunąć siebie z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aby włączyć powiadomienia e-mail o alertach dla urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie, które chcesz skonfigurować.
2. Przejdź do pozycji **Ustawienia**  >  **Ogólne** dla urządzenia.

   ![Blok alerty](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. W bloku **Ustawienia ogólne** przejdź do pozycji **Ustawienia alertu** i ustaw następujące opcje:
   
   1. W polu **Wyślij wiadomość e-mail z powiadomieniem** wybierz pozycję **tak**.
   2. W polu **Administratorzy usługi poczty e-mail** wybierz pozycję **tak** , aby administrator usługi i wszyscy współadministratorzy otrzymywali powiadomienia o alertach.
   3. W polu **inni adresaci wiadomości e-mail** wprowadź adresy e-mail wszystkich adresatów, którzy powinni otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie *ktoś \@ Somewhere.com*. Użyj średników, aby rozdzielić adresy e-mail. Można skonfigurować maksymalnie 20 adresów e-mail na urządzenie. 
      
3. Aby wysłać testową wiadomość e-mail z powiadomieniem, kliknij pozycję **Wyślij testową wiadomość e-mail**. Usługa StorSimple Menedżer urządzeń będzie wyświetlać komunikaty o stanie podczas przesyłania dalej powiadomienia testowego.

    ![Ustawienia alertu](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Po wysłaniu testowej wiadomości e-mail zobaczysz powiadomienie. 
   
    ![Wysłane wiadomości e-mail z powiadomieniem testowym o alertach](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Jeśli nie można wysłać komunikatu z powiadomieniem o testach, Usługa StorSimple Menedżer urządzeń będzie wyświetlała odpowiedni komunikat o błędzie. Poczekaj kilka minut, a następnie spróbuj ponownie wysłać testowy test powiadomienia. 

5. Po zakończeniu konfiguracji kliknij przycisk **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Alert testowy wysłano wiadomość e-mail z powiadomieniem 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Blok podsumowania usługi StorSimple Menedżer urządzeń zapewnia szybki wgląd w liczbę alertów na urządzeniach według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknięcie poziomu ważności powoduje otwarcie bloku **alerty** . Wyniki obejmują tylko alerty pasujące do tego poziomu ważności.

Kliknięcie alertu na liście zawiera dodatkowe szczegóły dotyczące alertu, w tym czas ostatniego zgłoszenia alertu, liczbę wystąpień alertu na urządzeniu oraz zalecaną akcję w celu rozwiązania alertu. W przypadku alertu sprzętowego zostanie również zidentyfikowany składnik sprzętowy.

![Przykład alertu sprzętowego](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Możesz skopiować szczegóły alertu do pliku tekstowego, jeśli trzeba wysłać informacje do pomoc techniczna firmy Microsoft. Po wykonaniu zalecenia i rozwiązaniu lokalnego stanu alertu należy wyczyścić alert z urządzenia, wybierając alert w bloku **alerty** , a następnie klikając przycisk **Wyczyść**. Aby wyczyścić wiele alertów, zaznacz poszczególne alerty, kliknij dowolną kolumnę poza kolumną **alertu** , a następnie kliknij przycisk **Wyczyść** po wybraniu wszystkich alertów do wyczyszczenia. Należy zauważyć, że niektóre alerty są automatycznie czyszczone po rozwiązaniu problemu lub gdy system aktualizuje alert przy użyciu nowych informacji.

Po kliknięciu przycisku **Wyczyść** będziesz mieć możliwość udostępnienia komentarzy dotyczących alertu i kroków, które należy wykonać w celu rozwiązania problemu. Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolone przy użyciu nowych informacji. W takim przypadku zostanie wyświetlony następujący komunikat.

![Wyczyść komunikat alertu](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortowanie i przeglądanie alertów

Możesz bardziej wydajnie uruchamiać raporty dotyczące alertów, aby można było je przeglądać i czyścić w grupach. Ponadto blok **alerty** może wyświetlać do 250 alertów. W przypadku przekroczenia tej liczby alertów nie wszystkie alerty będą wyświetlane w widoku domyślnym. Można połączyć następujące pola w celu dostosowania wyświetlanych alertów:

* **Stan** — można wyświetlić **aktywne** lub **wyczyszczone** alerty. Aktywne alerty są nadal wyzwalane w systemie, podczas gdy wyczyszczone alerty zostały ręcznie wyczyszczone przez administratora lub programowo wyczyszczone, ponieważ system zaktualizował warunek alertu o nowe informacje.
* **Ważność** — można wyświetlić alerty o wszystkich poziomach ważności (krytyczne, ostrzegawcze, informacje) lub tylko o określonej ważności, takie jak tylko alerty krytyczne.
* **Źródło** — można wyświetlić alerty ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jednego lub wszystkich urządzeń.
* **Zakres czasu** — przez określenie daty **od** i **do** oraz sygnatur czasowych można sprawdzić alerty w odpowiednim czasie.

![Lista alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Szybkie informacje o alertach

W poniższej tabeli wymieniono niektóre z Microsoft Azure StorSimple alertów, które mogą wystąpić, a także dodatkowe informacje i zalecenia, jeśli są dostępne. Alerty urządzeń StorSimple należą do jednej z następujących kategorii:

* [Alerty łączności w chmurze](#cloud-connectivity-alerts)
* [Alerty klastra](#cluster-alerts)
* [Alerty odzyskiwania po awarii](#disaster-recovery-alerts)
* [Alerty sprzętu](#hardware-alerts)
* [Alerty błędów zadań](#job-failure-alerts)
* [Alerty woluminu przypiętego lokalnie](#locally-pinned-volume-alerts)
* [Alerty sieciowe](#networking-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)
* [Alerty pakietu pomocy technicznej](#support-package-alerts)
* [Alerty środowiska obudowy](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności w chmurze

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można nawiązać połączenia z <*nazwą poświadczenia chmury*>. |Nie można nawiązać połączenia z kontem magazynu. |Prawdopodobnie wystąpił problem z łącznością z urządzeniem. Uruchom `Test-HcsmConnection` polecenie cmdlet z interfejsu programu Windows PowerShell dla StorSimple na urządzeniu, aby zidentyfikować i rozwiązać problem. Jeśli ustawienia są poprawne, problem może mieć poświadczenia konta magazynu, dla którego zgłoszono alert. W takim przypadku należy użyć `Test-HcsStorageAccountCredential` polecenia cmdlet, aby określić, czy występują problemy, które można rozwiązać.<ul><li>Sprawdź ustawienia sieci.</li><li>Sprawdź poświadczenia konta magazynu.</li></ul> |
| Nie odebrano pulsu z urządzenia przez ostatni <*numer*> minut. |Nie można nawiązać połączenia z urządzeniem. |Wygląda na to, że występuje problem z łącznością z urządzeniem. Użyj `Test-HcsmConnection` polecenia cmdlet z interfejsu programu Windows PowerShell dla StorSimple na urządzeniu, aby zidentyfikować i rozwiązać problem, lub skontaktuj się z administratorem sieci. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Zachowanie StorSimple w przypadku niepowodzenia łączności z chmurą

Co się stanie w przypadku niepowodzenia łączności z chmurą dla urządzenia z systemem StorSimple w środowisku produkcyjnym?

W przypadku niepowodzenia łączności z chmurą na urządzeniu produkcyjnym StorSimple, w zależności od stanu urządzenia, mogą wystąpić następujące kwestie:

* W **przypadku danych lokalnych na urządzeniu**: w pewnym czasie nie będzie zakłóceń, a odczyty będą nadal obsługiwane. Jednakże, ponieważ liczba oczekujących wzrostów systemu IOs i przekracza limit, odczyty mogą się nie powieść.

    W zależności od ilości danych na urządzeniu zapisy są również nadal wykonywane przez pierwsze kilka godzin po przerwaniu łączności z chmurą. Zapisy zostaną następnie obniżone i ostatecznie zaczynają się niepowodzeniem, jeśli łączność z chmurą zostanie zakłócona przez kilka godzin. (Na urządzeniu znajduje się tymczasowy magazyn danych do wypchnięcia do chmury). Ten obszar jest opróżniany, gdy dane są wysyłane. W przypadku niepowodzenia łączności dane w tym obszarze magazynu nie zostaną wypchnięte do chmury, a operacja we/wy zakończy się niepowodzeniem.
* W **przypadku danych w chmurze**: w przypadku większości błędów łączności w chmurze zwracany jest błąd. Po przywróceniu łączności system IOs zostanie wznowiony bez konieczności przełączenia woluminu w tryb online. W rzadkich przypadkach może być wymagana interwencja użytkownika w celu przywrócenia woluminu do trybu online z Azure Portal.
* **W trakcie wykonywania migawek w chmurze**: operacja jest ponawiana kilka razy w ciągu 4-5 godzin, a jeśli łączność nie zostanie przywrócona, migawki w chmurze zakończą się niepowodzeniem.

### <a name="cluster-alerts"></a>Alerty klastra

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Urządzenie jest w trybie konserwacji. |Urządzenie zostało przełączone w tryb failover z powodu wprowadzenia lub zakończenia trybu konserwacji. Jest to normalne i nie jest wymagana żadna akcja. Po potwierdzeniu tego alertu Wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Urządzenie układowe lub oprogramowanie oprogramowania zostało właśnie zaktualizowane. |Klaster był w trybie failover z powodu aktualizacji. Jest to normalne i nie jest wymagana żadna akcja. Po potwierdzeniu tego alertu Wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Kontroler został zamknięty lub ponownie uruchomiony. |Urządzenie zostało przełączone w tryb failover, ponieważ aktywny kontroler został zamknięty lub ponownie uruchomiony przez administratora. Nie jest wymagana żadna akcja. Po potwierdzeniu tego alertu Wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Planowana praca w trybie failover. |Sprawdź, czy to była planowana praca w trybie failover. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Nieplanowane przejście w tryb failover. |StorSimple jest skompilowany do automatycznego odzyskiwania z nieplanowanych przełączeń w tryb failover. Jeśli zobaczysz dużą liczbę tych alertów, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| Urządzenie przełączone w tryb failover do <> *nazwy urządzenia* . |Inna/nieznana przyczyna. |Jeśli zobaczysz dużą liczbę tych alertów, skontaktuj się z firmą pomoc techniczna firmy Microsoft. Po rozwiązaniu problemu Wyczyść ten alert na stronie alertów. |
| Krytyczna usługa urządzenia zgłasza stan jako niepowodzenie. |Błąd usługi ścieżki datapath. |Skontaktuj się pomoc techniczna firmy Microsoft w celu uzyskania pomocy. |
| Wirtualny adres IP dla interfejsu sieciowego <*Data #*> zgłasza stan jako niepowodzenie. |Inna/nieznana przyczyna. |Czasami warunki tymczasowe mogą spowodować te alerty. W takim przypadku ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| Wirtualny adres IP dla interfejsu sieciowego <*Data #*> zgłasza stan jako niepowodzenie. |Nazwa interfejsu: <*Data #*> adres IP `<IP address>` nie może zostać przełączony w tryb online, ponieważ w sieci wykryto zduplikowany adres IP. |Upewnij się, że zduplikowany adres IP został usunięty z sieci lub ponownie skonfiguruj interfejs przy użyciu innego adresu IP. |

### <a name="disaster-recovery-alerts"></a>Alerty odzyskiwania po awarii

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Operacje odzyskiwania nie mogą przywrócić wszystkich ustawień dla tej usługi. Dane konfiguracji urządzenia są w niespójnym stanie dla niektórych urządzeń. |Wykryto niespójność danych po odzyskiwaniu po awarii. |Zaszyfrowane dane w usłudze nie są zsynchronizowane z programem na urządzeniu. Autoryzuj urządzenie <*nazwę urządzenia*> z StorSimple Menedżer urządzeń, aby rozpocząć proces synchronizacji. Użyj interfejsu programu Windows PowerShell dla StorSimple, aby uruchomić `Restore-HcsmEncryptedServiceData` *nazwę urządzenia* <> polecenie cmdlet, dostarczając stare hasło jako dane wejściowe tego polecenia cmdlet w celu przywrócenia profilu zabezpieczeń. Następnie uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenie cmdlet, aby zaktualizować klucz szyfrowania danych usługi. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów. |

### <a name="hardware-alerts"></a>Alerty sprzętu

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Składnik sprzętowy <*Identyfikator składnika*> raporty stanu jako <*stanu*>. | |Czasami warunki tymczasowe mogą spowodować te alerty. Jeśli tak, ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. |
| Nieprawidłowe działanie kontrolera pasywnego. |Kontroler pasywny (pomocniczy) nie działa. |Urządzenie działa, ale jeden z kontrolerów nie działa prawidłowo. Spróbuj ponownie uruchomić ten kontroler. Jeśli problem nie zostanie rozwiązany, skontaktuj się z pomoc techniczna firmy Microsoft. |

### <a name="job-failure-alerts"></a>Alerty błędów zadań

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można utworzyć kopii zapasowej *identyfikatora grupy woluminów źródłowych*> <. |Zadanie tworzenia kopii zapasowej nie powiodło się. |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji tworzenia kopii zapasowej. Jeśli nie ma problemów z łącznością, być może osiągnięto maksymalną liczbę kopii zapasowych. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, i spróbuj ponownie wykonać operację. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Klonowanie *identyfikatorów źródłowego elementu kopii zapasowej* <> do <*numerów seryjnych woluminów docelowych*> nie powiodło się. |Zadanie klonowania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważna. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością z chmurą uniemożliwiają pomyślne zakończenie operacji klonowania. Jeśli nie ma problemów z łącznością, być może osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, i spróbuj ponownie wykonać operację. Po wykonaniu odpowiedniej akcji w celu rozwiązania problemu Wyczyść ten alert na stronie alertów. |
| Przywracanie *identyfikatorów elementu <źródłowej kopii zapasowej* nie powiodło się>. |Zadanie przywracania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważna. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością z chmurą uniemożliwiają pomyślne zakończenie operacji przywracania. Jeśli nie ma problemów z łącznością, być może osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, i spróbuj ponownie wykonać operację. Po wykonaniu odpowiedniej akcji w celu rozwiązania problemu Wyczyść ten alert na stronie alertów. |

### <a name="locally-pinned-volume-alerts"></a>Alerty woluminu przypiętego lokalnie

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można utworzyć woluminu lokalnego <*nazwy woluminu*>. |Zadanie tworzenia woluminu nie powiodło się. <*Komunikat o błędzie odpowiadający kodowi błędu nie powiodło się*>. |Problemy z łącznością mogą uniemożliwiać pomyślne zakończenie operacji tworzenia miejsca. Woluminy przypięte lokalnie są alokowane elastycznie i proces tworzenia miejsca obejmuje rozlanie woluminów warstwowych do chmury. Jeśli nie ma problemów z łącznością, być może wystąpiło lokalne miejsce na urządzeniu. Ustal, czy na urządzeniu istnieje miejsce, zanim ponowisz próbę wykonania tej operacji. |
| Rozszerzenie woluminu lokalnego <*nazwy woluminu*> nie powiodło się. |Zadanie modyfikacji woluminu nie powiodło się z powodu <*komunikatu o błędzie odpowiadającego> kod błędu* . |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji rozszerzania woluminu. Woluminy przypięte lokalnie są alokowane elastycznie i proces rozszerzania istniejącego obszaru obejmuje przelanie woluminów warstwowych do chmury. Jeśli nie ma problemów z łącznością, być może wystąpiło lokalne miejsce na urządzeniu. Ustal, czy na urządzeniu istnieje miejsce, zanim ponowisz próbę wykonania tej operacji. |
| Nie można przekonwertować *nazwy* woluminu <woluminu>. |Zadanie konwersji woluminu w celu konwersji typu woluminu z lokalnie przypięty do warstwowej nie powiodło się. |Konwersja woluminu z typu przypiętego lokalnie do warstwy nie może zostać ukończona. Upewnij się, że nie występują żadne problemy z łącznością uniemożliwiające pomyślne zakończenie operacji. Rozwiązywanie problemów z łącznością można znaleźć w [obszarze Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalny wolumin przypięty lokalnie został oznaczony jako wolumin warstwowy, ponieważ niektóre dane z woluminu przypiętego lokalnie zostały przelane do chmury podczas konwersji. Wynikowy wolumin warstwowy nadal zajmuje miejsce lokalne na urządzeniu, którego nie można odszukać w przyszłych woluminach lokalnych.<br>Rozwiąż wszelkie problemy z łącznością, Usuń alert i przekonwertuj ten wolumin z powrotem na oryginalny wolumin przypięty lokalnie, aby upewnić się, że wszystkie dane zostaną ponownie udostępnione lokalnie. |
| Nie można przekonwertować *nazwy* woluminu <woluminu>. |Zadanie konwersji woluminu w celu konwersji typu woluminu z warstwowego na przypięty lokalnie nie powiodło się. |Nie można ukończyć konwersji woluminu z typu warstwowego na przypięty lokalnie. Upewnij się, że nie występują żadne problemy z łącznością uniemożliwiające pomyślne zakończenie operacji. Rozwiązywanie problemów z łącznością można znaleźć w [obszarze Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalny wolumin warstwowy oznaczono teraz jako wolumin przypięty lokalnie w ramach procesu konwersji w dalszym ciągu ma dane znajdujące się w chmurze, podczas gdy nie można już ponownie zastrzec miejsca na urządzeniu dla tego woluminu dla przyszłych woluminów lokalnych.<br>Rozwiąż wszelkie problemy z łącznością, Usuń alert i przekonwertuj ten wolumin z powrotem na oryginalny typ woluminu warstwowego, aby zapewnić możliwość odrejestrowania lokalnego miejsca na urządzeniu. |
| Bliskie użycie lokalnego miejsca dla lokalnych migawek <*Nazwa grupy woluminów*> |Lokalne migawki dla zasad tworzenia kopii zapasowych mogą wkrótce zabraknąć miejsca i być unieważnione, aby zapobiec błędom zapisu hosta. |Częste migawki lokalne z dużą ilością danych w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowych powodują szybkie wykorzystanie lokalnego miejsca na urządzeniu. Usuń wszystkie migawki lokalne, które nie są już potrzebne. Ponadto należy zaktualizować harmonogramy migawek lokalnych dla tych zasad tworzenia kopii zapasowych, aby wykonywać mniej częste migawki lokalne i upewnić się, że migawki w chmurze są regularnie wykonywane. Jeśli te akcje nie zostaną wykonane, lokalne miejsce dla tych migawek może wkrótce ulec wyczerpaniu i system usunie je automatycznie, aby upewnić się, że zapisy hosta nadal będą przetwarzane pomyślnie. |
| Lokalne migawki dla <*nazw grup woluminów*> zostały unieważnione. |Lokalne migawki dla <*nazw grup woluminów*> zostały unieważnione, a następnie usunięte, ponieważ przekroczą lokalne miejsce na urządzeniu. |Aby upewnić się, że nie powtarza się to w przyszłości, przejrzyj harmonogramy migawek lokalnych dla tych zasad tworzenia kopii zapasowych i Usuń wszystkie migawki lokalne, które nie są już potrzebne. Częste migawki lokalne z dużą ilością danych w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowych mogą szybko korzystać z lokalnego miejsca na urządzeniu. |
| Przywracanie *identyfikatorów elementu <źródłowej kopii zapasowej* nie powiodło się>. |Zadanie przywracania zakończyło się niepowodzeniem. |Jeśli masz lokalnie przypięte lub kombinację lokalnie przypiętych woluminów warstwowych w tych zasadach tworzenia kopii zapasowych, Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważna. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością z chmurą uniemożliwiają pomyślne zakończenie operacji przywracania. Woluminy przypięte lokalnie, które zostały przywrócone w ramach tej grupy migawek, nie mają wszystkich pobranych danych do urządzenia i, jeśli w tej grupie migawek istnieje kombinacja woluminów przypiętych warstwowo i lokalnie, nie będą one zsynchronizowane ze sobą. Aby pomyślnie ukończyć operację przywracania, przełącz woluminy z tej grupy do trybu offline na hoście, a następnie spróbuj ponownie wykonać operację przywracania. Należy zauważyć, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania, zostaną utracone. |

### <a name="networking-alerts"></a>Alerty sieciowe

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można uruchomić usług StorSimple. |Błąd ścieżki datapath |Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |
| Wykryto zduplikowany adres IP dla elementu "Data0". | |System wykrył konflikt dla adresu IP "10.0.0.1". Zasób sieciowy "Data0" na urządzeniu jest w *\<device1>* trybie offline. Upewnij się, że ten adres IP nie jest używany przez żadną inną jednostkę w tej sieci. Aby rozwiązać problemy z siecią, przejdź do [obszaru Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Skontaktuj się z administratorem sieci, aby uzyskać pomoc w rozwiązaniu tego problemu. Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |
| Adres IPv4 (lub IPv6) dla elementu "Data0" jest w trybie offline. | |Zasób sieciowy "Data0" o adresie IP "10.0.0.1". i długość prefiksu "22" na urządzeniu *\<device1>* jest w trybie offline. Upewnij się, że porty przełącznika, do których jest podłączony ten interfejs są operacyjne. Aby rozwiązać problemy z siecią, przejdź do [obszaru Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki datapath |URLthat jest używany do uwierzytelniania jest nieosiągalny. Upewnij się, że reguły zapory zawierają wzorce URL określone dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów URL w Azure Portal, przejdź do protokołu https: \/ /aka.MS/SS-8000-Network-reqs. W przypadku korzystania z chmury Azure Government przejdź do wzorca adresu URL w protokole https: \/ /aka.MS/ss8000-gov-Network-reqs.|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Obciążenie urządzenia przekroczyło <*progową*>. |Wolniejsze niż oczekiwane czasy odpowiedzi. |Urządzenie zgłasza użycie w ramach dużego obciążenia wejścia/wyjścia. Może to spowodować, że urządzenie nie będzie działało, a także powinno. Przejrzyj obciążenia dołączone do urządzenia i ustal, czy istnieją jakieś, które można przenieść na inne urządzenie lub które nie są już potrzebne.|
| Nie można uruchomić usług StorSimple. |Błąd ścieżki datapath |Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Rozpoczęto sesję pomoc techniczna firmy Microsoft. |Sesja pomocy technicznej innej firmy. |Upewnij się, że ten dostęp jest autoryzowany. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Hasło> *elementu* <wygaśnie w <długość> *czasu* . |Zbliża się czas wygaśnięcia hasła. |Zmień hasło przed jego wygaśnięciem. |
| Brak informacji o konfiguracji zabezpieczeń dla *identyfikatora elementu* <>. | |Woluminy skojarzone z tym kontenerem woluminów nie mogą być używane do replikowania konfiguracji StorSimple. Aby upewnić się, że dane są bezpiecznie przechowywane, zalecamy usunięcie kontenera woluminów i wszystkich woluminów skojarzonych z kontenerem woluminów. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| <*Liczba* nieudanych prób logowania> nie powiodła się dla *identyfikatora elementu* <>. |Wiele nieudanych prób logowania. |Urządzenie może być atakowane lub autoryzowany użytkownik próbuje nawiązać połączenie przy użyciu niepoprawnego hasła.<ul><li>Skontaktuj się z autoryzowanymi użytkownikami i sprawdź, czy te próby pochodzą z wiarygodnego źródła. Jeśli nadal widzisz dużą liczbę nieudanych prób logowania, rozważ wyłączenie zdalnego zarządzania i skontaktowanie się z administratorem sieci. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów.</li><li>Sprawdź, czy wystąpienia Snapshot Manager są skonfigurowane przy użyciu poprawnego hasła. Po wykonaniu odpowiedniej akcji wyczyść ten alert na stronie alertów.</li></ul>Aby uzyskać więcej informacji, przejdź do [pozycji Zmienianie wygasłego hasła urządzenia](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Wystąpił co najmniej jeden błąd podczas zmiany klucza szyfrowania danych usługi. | |Napotkano błędy podczas zmiany klucza szyfrowania danych usługi. Po spełnieniu warunków błędu Uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenie cmdlet z interfejsu programu Windows PowerShell dla StorSimple na urządzeniu, aby zaktualizować usługę. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu Wyczyść ten alert na stronie alertów. |

### <a name="support-package-alerts"></a>Alerty pakietu pomocy technicznej

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie pakietu dla pomocy technicznej nie powiodło się. |StorSimple nie może wygenerować pakietu. |Spróbuj ponownie wykonać tę operację. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft. Po rozwiązaniu problemu Wyczyść ten alert na stronie alertów. |

### <a name="enclosure-environment-alerts"></a>Alerty środowiska obudowy

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Czujnik temperatury otoczenia składnika sprzętowego zgłasza stan jako niepowodzenie.  | Typ obudowy: Obudowa główna | Ten alert jest wyzwalany, gdy otoczenia poza temperaturą StorSimple przekracza akceptowalny zakres. Sprawdź otoczenia poza temperaturą lub przepływem powietrza z oddziału AC w centrum danych. Gdy temperatura powróci do normalnego, alert zostanie automatycznie wyczyszczony po upływie pewnego czasu. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft.   |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [błędach StorSimple i rozwiązywaniu problemów z wdrażaniem urządzeń](storsimple-8000-troubleshoot-deployment.md).
