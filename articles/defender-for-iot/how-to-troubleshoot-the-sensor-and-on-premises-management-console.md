---
title: Rozwiązywanie problemów z czujnikiem i lokalną konsolą zarządzania
description: Rozwiązywanie problemów z czujnikiem i lokalną konsolą zarządzania w celu wyeliminowania wszelkich problemów, które mogą wystąpić.
ms.date: 03/14/2021
ms.topic: article
ms.openlocfilehash: 83ffec959a5b38e9f4fd4d7d0dcfd3b1b9faa096
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785448"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Rozwiązywanie problemów z czujnikiem i lokalną konsolą zarządzania

W tym artykule opisano podstawowe narzędzia do rozwiązywania problemów z czujnikiem i lokalną konsolą zarządzania. Oprócz elementów opisanych tutaj można sprawdzić kondycję systemu w następujący sposób:

**Alerty**: alert jest tworzony, gdy interfejs czujnika monitorujący ruch nie działa. 

**SNMP**: kondycja czujnika jest monitorowana za poorednictwem protokołu SNMP. Usługa Azure Defender for IoT reaguje na zapytania protokołu SNMP wysyłane z autoryzowanego serwera monitorowania. 

**Powiadomienia systemowe**: gdy konsola zarządzania kontroluje czujnik, można przesłać dalej alerty dotyczące nieudanych kopii zapasowych czujnika i czujników rozłączonych.

## <a name="sensor-troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów z czujnikiem

### <a name="investigate-password-failure-at-initial-sign-in"></a>Zbadaj niepowodzenie hasła przy początkowej rejestracji

Po pierwszym zalogowaniu się do wstępnie skonfigurowanego czujnika strzałek należy wykonać odzyskiwanie hasła.

Aby odzyskać hasło:

1. Na ekranie logowania do usługi Defender for IoT wybierz pozycję  **Odzyskiwanie hasła**. Zostanie otwarty ekran **Odzyskiwanie hasła** .

1. Wybierz opcję **CyberX** lub **support** i skopiuj unikatowy identyfikator.

1. Przejdź do Azure Portal i wybierz pozycję **Lokacje i czujniki**.  

1. Wybierz kartę **odzyskiwanie na lokalnym komputerze konsoli zarządzania** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Wybierz przycisk Odzyskaj lokalne zarządzanie, aby pobrać plik odzyskiwania.":::

1. Wprowadź unikatowy identyfikator otrzymany na ekranie **odzyskiwania hasła** i wybierz polecenie **Odzyskaj**. `password_recovery.zip`Plik zostanie pobrany.

    > [!NOTE]
    > Nie zmieniaj pliku odzyskiwania hasła. Jest to podpisany plik i nie będzie działał w przypadku naruszenia go.

1. Na ekranie **Odzyskiwanie hasła** wybierz pozycję **Przekaż**. Zostanie otwarte okno **odzyskiwanie pliku odzyskiwania hasła** .

1. Wybierz pozycję **Przeglądaj** , aby zlokalizować `password_recovery.zip` plik, lub przeciągnij `password_recovery.zip` do okna.

1. Po wybraniu opcji **dalej** zostanie wyświetlone hasło użytkownika i wygenerowanego przez system hasła do konsoli zarządzania.

    > [!NOTE]
    > Gdy logujesz się do czujnika lub lokalnej konsoli zarządzania po raz pierwszy, zostanie ona połączona z subskrypcją, z którą nawiązano połączenie. Jeśli musisz zresetować hasło dla użytkownika CyberX lub pomocy technicznej, musisz wybrać tę subskrypcję. Aby uzyskać więcej informacji na temat odzyskiwania CyberX lub obsługi hasła użytkownika, zobacz [resetowanie haseł](how-to-create-and-manage-users.md#resetting-passwords).

### <a name="investigate-a-lack-of-traffic"></a>Badanie braku ruchu

Wskaźnik pojawia się u góry konsoli, gdy Czujnik rozpoznaje, że nie ma ruchu na jednym ze skonfigurowanych portów. Ten wskaźnik jest widoczny dla wszystkich użytkowników.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Zrzut ekranu alertu informującego, że nie wykryto żadnego ruchu.":::
 
Gdy ten komunikat zostanie wyświetlony, możesz sprawdzić, gdzie nie ma ruchu. Upewnij się, że kabel span jest połączony i nie wprowadzono żadnych zmian w architekturze span.  

Aby uzyskać pomoc techniczną i informacje dotyczące rozwiązywania problemów, skontaktuj się [Pomoc techniczna firmy Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Sprawdź wydajność systemu 

Po wdrożeniu nowego czujnika lub na przykład czujnik działa wolno lub nie wyświetla żadnych alertów, można sprawdzić wydajność systemu.

Aby sprawdzić wydajność systemu:

1. Na pulpicie nawigacyjnym upewnij się, że `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Zrzut ekranu przedstawiający przykładowy pulpit nawigacyjny."::: 

1. Z menu po stronie wybierz pozycję **urządzenia**.

1. W oknie **urządzenia** upewnij się, że urządzenia są wykrywane.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Upewnij się, że urządzenia zostały odnalezione.":::

1. Z menu po stronie wybierz pozycję **wyszukiwanie danych**.

1. W oknie **wyszukiwanie danych** wybierz pozycję **wszystkie** i Wygeneruj raport.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Generowanie nowego raportu przy użyciu funkcji wyszukiwania danych.":::

1. Upewnij się, że raport zawiera dane.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Upewnij się, że raport zawiera dane.":::

1. Z menu po stronie wybierz pozycję **trendy & Statystyka**.

1. W oknie **trendy & statystyk** wybierz pozycję **Dodaj widżet**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Dodaj widżet, zaznaczając go.":::

1. Dodaj widżet i upewnij się, że zawiera on dane.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Upewnij się, że element widget pokazuje dane.":::

1. Z menu po stronie wybierz pozycję **alerty**. Zostanie wyświetlone okno **alerty** .

1. Upewnij się, że alerty zostały utworzone.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Upewnij się, że alerty zostały utworzone.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Zbadaj brak oczekiwanych alertów

Jeśli okno **alerty** nie zawiera oczekiwanego alertu, sprawdź następujące kwestie:

- Sprawdź, czy ten sam alert jest już wyświetlany w oknie **alertów** jako reakcja na inne wystąpienie zabezpieczeń. Jeśli tak, a ten alert nie został jeszcze obsłużony, konsola czujnika nie pokazuje nowego alertu.

- Upewnij się, że ten alert nie został wykluczony przy użyciu reguł **wykluczania alertów** w konsoli zarządzania programu. 

### <a name="investigate-widgets-that-show-no-data"></a>Zbadaj widżety, które nie zawierają danych

Gdy widżety w oknie **trendy & dane statystyczne** nie zawierają żadnych danych, wykonaj następujące czynności:

- [Sprawdź wydajność systemu](#check-system-performance).

- Upewnij się, że ustawienia czasu i regionu są prawidłowo skonfigurowane i nie są ustawione na przyszły czas. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Zbadaj mapę urządzeń, która pokazuje tylko urządzenia rozgłaszające

Gdy urządzenia widoczne na mapie są widoczne jako niepołączone ze sobą, coś może być niewłaściwe w konfiguracji portu SPAN. Oznacza to, że może być widoczne tylko emitowanie urządzeń i brak ruchu emisji pojedynczej.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Wyświetl urządzenia rozgłaszania.":::

W takim przypadku należy sprawdzić, czy można zobaczyć tylko ruch emisji. Następnie poproszony inżynier sieci, aby naprawił konfigurację zakresu portów, aby można było zobaczyć ruch emisji pojedynczej.

Aby sprawdzić, czy jest wyświetlany tylko ruch emisji:

- Na ekranie **wyszukiwania danych** Utwórz raport przy użyciu opcji **wszystkie** . Następnie sprawdź, czy w raporcie pojawia się tylko ruch związany z emisją i multiemisją (bez ruchu emisji pojedynczej).

Oraz

- Zarejestruj PCAP bezpośrednio z przełącznika lub podłącz komputer przenośny przy użyciu programu Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Łączenie czujnika z NTP

Aby połączyć się z NTP, można skonfigurować czujnik autonomiczny i konsolę zarządzania programu z czujnikami związanymi z nim.

Aby podłączyć czujnik autonomiczny do NTP:

- [Skontaktuj się z zespołem pomocy technicznej, aby uzyskać pomoc](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Aby połączyć czujnik kontrolowany przez konsolę zarządzania z NTP:

- Połączenie z usługą NTP jest konfigurowane w konsoli zarządzania programu. Wszystkie czujniki, które Konsola zarządzania kontroluje automatyczne pobieranie połączenia NTP.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Zbadaj, kiedy urządzenia nie są wyświetlane na mapie, lub masz wiele alertów związanych z Internetem

Czasami urządzenia ICS są konfigurowane z zewnętrznymi adresami IP. Te urządzenia ICS nie są wyświetlane na mapie. Zamiast urządzeń, Chmura internetowa pojawia się na mapie. Adresy IP tych urządzeń są zawarte w obrazie chmury.

Innym wskazaniem tego samego problemu jest wyświetlenie wielu alertów związanych z Internetem.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Wiele alertów związanych z Internetem.":::

Aby naprawić konfigurację:

1. Kliknij prawym przyciskiem myszy ikonę chmury na mapie urządzenia i wybierz pozycję **Eksportuj adresy IP**. Skopiuj publiczne zakresy, które są prywatne, i Dodaj je do listy podsieć. Aby uzyskać więcej informacji, zobacz [Configure Subnets](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Generuj nowy raport wyszukiwania danych dla połączeń internetowych.

1. W raporcie wyszukiwania danych wybierz pozycję, :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: Aby wprowadzić tryb administratora i usunąć adresy IP urządzeń usługi ICS.

### <a name="tweak-the-sensors-quality-of-service"></a>Dostrajanie jakości usług czujnika

Aby zapisać zasoby sieciowe, można ograniczyć przepustowość interfejsu używaną przez czujnik do codziennych procedur.

Aby ograniczyć przepustowość interfejsu, należy użyć `cyberx-xsense-limit-interface` Narzędzia interfejsu wiersza polecenia, które należy uruchomić z uprawnieniami sudo. Narzędzie uzyskuje następujące argumenty:

  - `* -i`: interfejsy (przykład: eth0).

  - `* -l`: limit (przykład: 30 kbit/1 Mbit). Można używać następujących jednostek przepustowości: KB/s, MB/s, kbit, Mbit lub BPS.

  - `* -c`: Wyczyść (aby wyczyścić ograniczenie przepustowości interfejsu).

Aby dostosować jakość usługi:

1. Zaloguj się do interfejsu wiersza polecenia czujnika jako dla użytkownika usługi Defender for IoT i wprowadź polecenie `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Na przykład: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > W przypadku urządzenia fizycznego należy użyć interfejsu EM1.

1. Aby wyczyścić ograniczenie interfejsu, wprowadź `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów z lokalną konsolą zarządzania

### <a name="investigate-a-lack-of-expected-alerts"></a>Zbadaj brak oczekiwanych alertów

Jeśli oczekiwany alert nie jest wyświetlany w oknie **alertów** , sprawdź następujące kwestie:

- Sprawdź, czy ten sam alert jest już wyświetlany w oknie **alertów** jako reakcja na inne wystąpienie zabezpieczeń. Jeśli tak, a ten alert nie został jeszcze obsłużony, nie jest wyświetlany nowy Alert.

- Sprawdź, czy ten alert nie został wykluczony przy użyciu reguł **wykluczania alertów** w lokalnej konsoli zarządzania.  

### <a name="tweak-the-quality-of-service"></a>Dostosowywanie jakości usług

Aby zapisać zasoby sieciowe, można ograniczyć liczbę alertów wysyłanych do systemów zewnętrznych (takich jak wiadomości e-mail lub SIEM) w jednej operacji synchronizacji między urządzeniem a lokalną konsolą zarządzania.

Domyślna wartość wynosi 50. Oznacza to, że w jednej sesji komunikacji między urządzeniem a lokalną konsolą zarządzania w systemach zewnętrznych nie będzie więcej niż 50 alertów. 

Aby ograniczyć liczbę alertów, użyj `notifications.max_number_to_report` Właściwości dostępnej w `/var/cyberx/properties/management.properties` . Po zmianie tej właściwości nie jest wymagane ponowne uruchomienie komputera.

Aby dostosować jakość usługi:

1. Zaloguj się jako użytkownik usługi Defender for IoT. 

1. Sprawdź wartości domyślne:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Wyświetlane są następujące wartości domyślne:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Edytuj ustawienia domyślne:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Edytuj ustawienia następujących wierszy:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Zapisz zmiany. Nie jest wymagane ponowne uruchomienie.

## <a name="export-information-for-troubleshooting"></a>Eksportuj informacje na potrzeby rozwiązywania problemów

Oprócz narzędzi do monitorowania i analizowania sieci można wysyłać informacje do zespołu pomocy technicznej w celu dalszej analizy. Podczas eksportowania dzienników czujnik automatycznie generuje hasło jednorazowe (OTP), unikatowe dla eksportowanych dzienników, w osobnym pliku tekstowym. 

Aby wyeksportować dzienniki:

1. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**.

1. Wybierz pozycję **Eksportuj dzienniki**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Wyeksportuj dziennik do obsługi systemu.":::

1. W polu **Nazwa pliku** wprowadź nazwę pliku, który ma być używany do eksportowania dziennika. Wartość domyślna to bieżąca data.

1. Aby określić, które dane mają zostać wyeksportowane, wybierz kategorie danych:  

    | Eksportuj kategorię | Opis |
    |--|--|
    | **Dzienniki systemu operacyjnego** | Wybierz tę opcję, aby uzyskać informacje o stanie systemu operacyjnego. |
    | **Dzienniki instalacji/uaktualniania** | Wybierz tę opcję w celu zbadania parametrów konfiguracji instalacji i uaktualniania. |
    | **Dane wyjściowe Sanity systemu** | Wybierz tę opcję, aby sprawdzić wydajność systemu. |
    | **Dzienniki oddziałów** | Wybierz tę opcję, aby zezwolić na zaawansowaną inspekcję protokołu. |
    | **Zrzuty jądra systemu operacyjnego** | Wybierz tę opcję, aby wyeksportować zrzut pamięci jądra. Zrzut pamięci jądra zawiera całą pamięć używaną przez jądro w momencie wystąpienia problemu, który wystąpił w tym jądrze. Rozmiar pliku zrzutu jest mniejszy od całkowitego zrzutu pamięci. Zwykle plik zrzutu ma rozmiar o jeden trzeci od rozmiaru pamięci fizycznej w systemie. |
    | **Przekazywanie dzienników** | Wybierz tę opcję w celu zbadania reguł przekazywania. |
    | **Dzienniki SNMP** | Wybierz tę opcję, aby uzyskać informacje o sprawdzaniu kondycji SNMP. |
    | **Podstawowe Dzienniki aplikacji** | Wybierz tę opcję, aby wyeksportować dane dotyczące podstawowej konfiguracji i operacji aplikacji. |
    | **Komunikacja z dziennikami CM** | Wybierz tę opcję, jeśli występują ciągłe problemy lub przerwy w działaniu połączenia z konsolą zarządzania. |
    | **Dzienniki aplikacji sieci Web** | Wybierz tę opcję, aby uzyskać informacje o wszystkich żądaniach wysyłanych z interfejsu internetowego aplikacji. |
    | **Kopia zapasowa systemu** | Wybierz tę opcję, aby wyeksportować kopię zapasową wszystkich danych systemowych na potrzeby badania dokładnego stanu systemu. |
    | **Statystyka odcięć** | Wybierz tę opcję, aby umożliwić zaawansowaną inspekcję statystyk protokołów. |
    | **Dzienniki bazy danych** | Wybierz tę opcję, aby wyeksportować dzienniki z systemowej bazy danych. Badanie dzienników systemu pomaga identyfikować problemy z systemem. |
    | **Konfiguracja** | Wybierz tę opcję, aby wyeksportować informacje o wszystkich konfigurowalnych parametrach, aby upewnić się, że wszystko zostało poprawnie skonfigurowane. |

1. Aby wybrać wszystkie opcje, wybierz pozycję **Zaznacz wszystko** obok pozycji **Kategorie**.

1. Wybierz pozycję **Eksportuj dzienniki**.

Wyeksportowane dzienniki zostaną dodane do listy **zarchiwizowane dzienniki** . Wyślij OTP do zespołu pomocy technicznej w oddzielnym komunikacie i średnim z wyeksportowanych dzienników. Zespół pomocy technicznej będzie mógł wyodrębnić wyeksportowane dzienniki tylko przy użyciu unikatowego uwierzytelniania OTP, który jest używany do szyfrowania dzienników.

Lista zarchiwizowanych dzienników może zawierać maksymalnie pięć elementów. Jeśli liczba elementów na liście wykracza poza tę liczbę, najwcześniejszy element zostanie usunięty.

## <a name="see-also"></a>Zobacz też

- [Wyświetlanie alertów](how-to-view-alerts.md)

- [Konfigurowanie monitorowania SNMP MIB](how-to-set-up-snmp-mib-monitoring.md)

- [Opis zdarzeń odłączenia czujnika](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
