---
title: Zarządzanie czujnikami z lokalnej konsoli zarządzania
description: Dowiedz się, jak zarządzać czujnikami z konsoli zarządzania, w tym do aktualizowania wersji czujników, wypychania ustawień systemowych do czujników oraz włączania i wyłączania aparatów w czujnikach.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 4706fbcf58b8e5f5eed6532f9a08f65c7716e07e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781929"
---
# <a name="manage-sensors-from-the-management-console"></a>Zarządzanie czujnikami z poziomu konsoli zarządzania

W tym artykule opisano sposób zarządzania czujnikami z poziomu konsoli zarządzania programu, w tym:

- Wypychanie ustawień systemowych do czujników

- Włączanie i wyłączanie aparatów w czujnikach

- Aktualizuj wersje czujnika

## <a name="push-configurations"></a>Konfiguracje wypychane

Można zdefiniować różne ustawienia systemowe i automatycznie zastosować je do czujników, które są połączone z konsolą zarządzania programu. Pozwala to zaoszczędzić czas i zapewnić usprawnione ustawienia w ramach czujników przedsiębiorstwa.

Można zdefiniować następujące ustawienia systemowe czujnika z poziomu konsoli zarządzania:

- Serwer poczty

- Monitorowanie MIB usługi SNMP

- Active Directory

- Ustawienia DNS

- Podsieci

- Aliasy portów

Aby zastosować ustawienia systemowe:

1. W lewym okienku konsoli wybierz pozycję **Ustawienia systemowe**.

2. W okienku **Konfiguruj czujniki** wybierz jedną z opcji.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Opcje ustawienia systemowego czujnika.":::

   W poniższym przykładzie opisano sposób definiowania parametrów serwera poczty dla czujników przedsiębiorstwa.

3. Wybierz pozycję **serwer poczty**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Wybierz serwer poczty z ekranu ustawienia systemu.":::

4. Wybierz czujnik po lewej stronie.

5. Ustaw parametry serwera poczty i wybierz opcję **Duplikuj**. Obok każdego elementu w drzewie czujników pojawia się pole wyboru.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Upewnij się, że pola wyboru są wybrane dla czujników.":::

6. W drzewie czujników wybierz elementy, do których chcesz zastosować konfigurację.

7. Wybierz pozycję **Zapisz**.

## <a name="update-versions"></a>Wersje aktualizacji

Można zaktualizować kilka czujników jednocześnie w lokalnej konsoli zarządzania.

Aby zaktualizować kilka czujników:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Przejdź do usługi Azure Defender dla IoT.

3. Przejdź do strony **aktualizacje** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Zrzut ekranu przedstawiający widok pulpitu nawigacyjnego aktualizacje.":::

4. Wybierz pozycję **Pobierz** z sekcji **czujniki** i Zapisz plik.

5. Zaloguj się do konsoli zarządzania i wybierz pozycję **Ustawienia systemowe**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Zrzut ekranu przedstawiający menu Administracja, w którym można wybrać ustawienia systemowe.":::

6. Oznacz czujniki, które chcesz zaktualizować, w sekcji **Konfiguracja aparatu czujników** , a następnie wybierz pozycję **Aktualizacje automatyczne**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Dwie czujniki pokazujące tryb uczenia i aktualizacje automatyczne.":::

7. Wybierz pozycję **Zapisz zmiany**.

8. W okienku **uaktualnianie wersji czujników** wybierz opcję :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Ekran uaktualnienia wersji czujnika, aby wyświetlić pliki.":::

9. Zostanie otwarte okno dialogowe **przekazywania pliku** . Przekaż pobrany plik ze strony **aktualizacje** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Wybierz przycisk Przeglądaj, aby przekazać plik.":::

10. W trakcie procesu aktualizacji w oknie **Zarządzanie lokacją** zostanie wyświetlony stan aktualizacji każdego czujnika.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Obserwuj postęp aktualizacji.":::

## <a name="update-threat-intelligence-packages"></a>Aktualizowanie pakietów analizy zagrożeń 

Pakiet danych dla analizy zagrożeń jest dostarczany z każdą nową usługą Defender for IoT lub w razie potrzeby między wersjami. Pakiet zawiera podpisy (w tym podpisy złośliwego oprogramowania), CVEs i inną zawartość zabezpieczeń. 

Możesz ręcznie przekazać ten plik ze strony **aktualizacji** portalu usługi Defender for IoT i automatycznie zaktualizować go do czujników. 

Aby zaktualizować dane analizy zagrożeń: 

1. Przejdź do strony **aktualizacje** usługi Defender for IoT. 

2. Pobierz i Zapisz plik.

3. Zaloguj się do konsoli zarządzania. 

4. W menu po stronie wybierz pozycję **Ustawienia systemowe**. 

5. Wybierz czujniki, które powinny otrzymywać aktualizacje w sekcji **konfiguracyjnej aparatu czujnika** .  

6. W sekcji **Wybierz dane analizy zagrożeń** wybierz znak plus ( **+** ). 

7. Przekaż pobrany pakiet ze strony **aktualizacje** usługi Defender for IoT.

## <a name="understand-sensor-disconnection-events"></a>Opis zdarzeń odłączenia czujnika

Okno **Site Manager** wyświetla informacje o rozłączeniu, jeśli czujniki rozłączą się od przypisanej do lokalnej konsoli zarządzania. Dostępne są następujące informacje dotyczące odłączenia czujnika:

- "Lokalna Konsola zarządzania nie może przetwarzać danych otrzymanych z czujnika".

- "Liczba wykrytych dryfów. Lokalna Konsola zarządzania została odłączona od czujnika "".

- "Czujnik nie komunikuje się z lokalną konsolą zarządzania. Sprawdź łączność sieciową lub sprawdzanie poprawności certyfikatu ".

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Zrzut ekranu przedstawiający widok strefy 1.":::

Możesz wysyłać alerty do stron trzecich z informacjami o rozłączonych czujników. Aby uzyskać więcej informacji, zobacz [przekazywanie alertów dotyczących błędów czujnika](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Włączanie lub wyłączanie czujników

Czujniki są chronione przez pięć aparatów usługi Defender for IoT. Można włączać lub wyłączać Aparaty dla powiązanych czujników.

| Aparat | Opis | Przykładowy scenariusz |
|--|--|--|
| Aparat naruszenia protokołu | Naruszenie protokołu występuje, gdy wartości struktury pakietu lub pola nie są zgodne ze specyfikacją protokołu. | Alert "Niedozwolona operacja MODBUS (kod funkcji zero)". Ten alert wskazuje, że urządzenie podstawowe wysłało żądanie z kodem funkcji 0 do urządzenia pomocniczego. Nie jest to dozwolone zgodnie ze specyfikacją protokołu, a urządzenie pomocnicze może nieprawidłowo obsłużyć dane wejściowe. |
| Aparat naruszenia zasad | Naruszenie zasad występuje w odróżnieniu od zachowania linii bazowej zdefiniowanej w zasadach zapoznania lub skonfigurowanych. | Alert "nieautoryzowany agent użytkownika HTTP". Ten alert wskazuje na to, że aplikacja, która nie została wybrana lub zatwierdzona przez zasady, jest używana jako klient HTTP na urządzeniu. Może to być Nowa przeglądarka lub aplikacja sieci Web na tym urządzeniu. |
| Aparat złośliwego oprogramowania | Aparat złośliwego oprogramowania wykrywa złośliwe działanie sieciowe. | Alert "podejrzenie złośliwego działania (Stuxnet)". Ten alert wskazuje, że czujnik znalazł podejrzaną aktywność sieci znaną jako powiązaną ze złośliwym oprogramowaniem Stuxnet, który jest zaawansowanym trwałym zagrożeniem mającym na celu kontrolę przemysłową i sieci SCADA. |
| Aparat anomalii | Aparat złośliwego oprogramowania wykrywa anomalię w działaniu sieciowym. | "Okresowe zachowanie w kanale komunikacji". Jest to składnik, który sprawdza połączenia sieciowe i wyszukuje okresowe lub cykliczne zachowanie transmisji danych, która jest wspólna w sieciach przemysłowych. |
| Aparat operacyjny | Ten aparat wykrywa zdarzenia operacyjne lub wadliwe jednostki. | `Device is Suspected to be Disconnected (Unresponsive)` wiadom. Ten alert jest wyzwalany, gdy urządzenie nie odpowiada na żądania ze wstępnie zdefiniowanego okresu. Może to wskazywać na zamknięcie urządzenia, odłączenie lub nieprawidłowe działanie.
|

Aby włączyć lub wyłączyć Aparaty dla czujników połączonych:

1. W lewym okienku konsoli wybierz pozycję **Ustawienia systemowe**.

2. W sekcji **Konfiguracja aparatu czujnika** wybierz opcję **Włącz** lub **Wyłącz** dla aparatów.
         
3. Wybierz pozycję **Zapisz zmiany**.

   Czerwony wykrzyknik pojawia się, jeśli występuje niezgodność włączonych aparatów na jednym z czujników przedsiębiorstwa. Aparat mógł zostać wyłączony bezpośrednio z czujnika.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Niezgodność włączonych aparatów."::: 

## <a name="define-sensor-backup-schedules"></a>Definiowanie harmonogramów tworzenia kopii zapasowych

Możesz planować kopie zapasowe czujnika i wykonywać kopie zapasowe czujnika na żądanie z lokalnej konsoli zarządzania. Pomaga to chronić przed awarią dysku twardego i utratą danych.

- Co to jest kopia zapasowa: konfiguracje i dane.

- Nie wykonano kopii zapasowej: PCAP pliki i dzienniki. Można ręcznie utworzyć kopię zapasową i przywrócić PCAPs i dzienniki.

Domyślnie są automatycznie tworzone kopie zapasowe czujników o godzinie 3:00. Funkcja harmonogramu tworzenia kopii zapasowych dla czujników pozwala zbierać te kopie zapasowe i przechowywać je w lokalnej konsoli zarządzania lub na zewnętrznym serwerze kopii zapasowych. Kopiowanie plików z czujników do lokalnej konsoli zarządzania odbywa się za pośrednictwem zaszyfrowanego kanału.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Widok ekranu tworzenia kopii zapasowej czujnika.":::

Gdy domyślna lokalizacja kopii zapasowej czujnika zostanie zmieniona, lokalna Konsola zarządzania automatycznie pobiera pliki z nowej lokalizacji w czujniku lub lokalizacji zewnętrznej, pod warunkiem, że konsola ma uprawnienia dostępu do tej lokalizacji. 

Gdy czujniki nie są zarejestrowane w lokalnej konsoli zarządzania, okno dialogowe **harmonogram tworzenia kopii zapasowej czujnika** wskazuje, że nie są zarządzane żadne czujniki.  

Proces przywracania jest taki sam, niezależnie od tego, gdzie są przechowywane pliki.

### <a name="backup-storage-for-sensors"></a>Magazyn kopii zapasowych dla czujników

Lokalnej konsoli zarządzania można użyć do przechowywania maksymalnie dziewięciu kopii zapasowych w każdym zarządzanym czujniku, pod warunkiem, że pliki kopii zapasowej nie przekraczają maksymalnej ilości miejsca do wykonania kopii zapasowej, która została przypisana. 

Dostępne miejsce jest obliczane na podstawie modelu konsoli zarządzania, z którym pracujesz: 

- **Model produkcyjny**: magazyn domyślny to 40 GB; Limit wynosi 100 GB. 

- **Średni model**: magazyn domyślny to 20 GB; Limit wynosi 50 GB. 

- **Model laptopa**: magazyn domyślny to 10 GB; Limit wynosi 25 GB. 

- **Model wąski**: magazyn domyślny to 2 GB; Limit wynosi 4 GB. 

- **Model porności**: magazyn domyślny to 10 GB; Limit wynosi 25 GB. 

Domyślna alokacja jest wyświetlana w oknie dialogowym **harmonogram tworzenia kopii zapasowej czujnika** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Ekran Edycja konfiguracji serwera poczty.":::

Nie ma limitu magazynowania podczas tworzenia kopii zapasowej na serwerze zewnętrznym. Należy jednak zdefiniować górny limit alokacji w   >  polu **ścieżka niestandardowa** harmonogramu kopii zapasowej czujnika. Obsługiwane są następujące cyfry i znaki: `/, a-z, A-Z, 0-9, and _` . 

Poniżej przedstawiono informacje dotyczące przekroczenia limitów magazynowania alokacji:

- W przypadku przekroczenia przydzielonych miejsc do magazynowania nie jest wykonywana kopia zapasowa czujnika. 

- Jeśli wykonujesz kopię zapasową więcej niż jednego czujnika, konsola zarządzania próbuje pobrać pliki czujników dla czujników zarządzanych.  

- Jeśli pobieranie z jednego czujnika przekracza limit, konsola zarządzania próbuje pobrać informacje o kopii zapasowej z następnego czujnika. 

W przypadku przekroczenia liczby zdefiniowanych kopii zapasowych najstarsza kopia zapasowa jest usuwana w celu uwzględnienia nowego pliku.

Pliki kopii zapasowej czujnika są automatycznie nazwane w następującym formacie: `<sensor name>-backup-version-<version>-<date>.tar` . Na przykład: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Aby utworzyć kopię zapasową czujników:

1. Wybierz pozycję **Zaplanuj kopię zapasową czujnika** z okna **Ustawienia systemu** . Czujniki zarządzane przez lokalną konsolę zarządzania są wyświetlane w oknie dialogowym **harmonogram tworzenia kopii zapasowej czujnika** .  

2. Włącz przełącznik **Zbieraj kopie zapasowe** .  

3. Wybierz interwał kalendarzowy, datę i strefę czasową. Format czasu jest oparty na zegarze 24-godzinnym. Na przykład wprowadź 6:00 PM jako **18:00**. 

4. W polu **alokacja magazynu kopii zapasowych** wprowadź magazyn, który chcesz przydzielić dla kopii zapasowych. Po przekroczeniu maksymalnego miejsca zostanie wyświetlone powiadomienie.

5. W polu **Zachowaj ostatnie** wskaż liczbę kopii zapasowych na czujnik, który ma zostać zachowany. Po przekroczeniu limitu najstarsza kopia zapasowa zostanie usunięta.  

6. Wybierz lokalizację kopii zapasowej:  

   - Aby utworzyć kopię zapasową w lokalnej konsoli zarządzania, wyłącz przełącznik **ścieżki niestandardowej** . Domyślna lokalizacja to `/var/cyberx/sensor-backups`.  

   - Aby utworzyć kopię zapasową na serwerze zewnętrznym, włącz przełącznik **ścieżki niestandardowej** i wprowadź lokalizację. Obsługiwane są następujące cyfry i znaki: `/, a-z, A-Z, 0-9, and, _` . 

7. Wybierz pozycję **Zapisz**. 

Aby natychmiast utworzyć kopię zapasową: 

- Wybierz pozycję **Utwórz kopię zapasową teraz**. Lokalna Konsola zarządzania tworzy i zbiera pliki kopii zapasowej czujnika. 

### <a name="receiving-backup-notifications-for-sensors"></a>Otrzymywanie powiadomień o kopiach zapasowych dla czujników 

Okno dialogowe **harmonogram tworzenia kopii zapasowej czujnika** i dziennik kopii zapasowej automatycznie wyświetlają informacje o sukcesach i błędach kopii zapasowych.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Wyświetlaj czujniki i tam, gdzie znajdują się i wszystkie istotne informacje.":::

Mogą wystąpić błędy, ponieważ:    

- Nie znaleziono pliku kopii zapasowej. 

- Znaleziono plik, ale nie można go pobrać.  

- Wystąpił błąd połączenia sieciowego. 

- Nie ma wystarczającej ilości miejsca przydzieloną do lokalnej konsoli zarządzania, aby zakończyć tworzenie kopii zapasowej.  

Gdy wystąpi awaria, można wysyłać powiadomienia e-mail, aktualizacje dziennika systemu i powiadomienia systemowe. W tym celu Utwórz regułę przekazywania w **powiadomieniach systemowych**. 

### <a name="restoring-sensors"></a>Przywracanie czujników 

Kopie zapasowe można przywracać z lokalnej konsoli zarządzania i przy użyciu interfejsu wiersza polecenia.  

Aby przywrócić z konsoli programu: 

- Wybierz pozycję **Przywróć obraz** z okna ustawienia **systemu czujnika** .

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Przywróć kopię zapasową obrazu.":::

  Następnie Konsola wyświetla błędy przywracania.  

Aby przywrócić za pomocą interfejsu wiersza polecenia: 

- Zaloguj się do konta administracyjnego i wprowadź `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Zapisz kopię zapasową czujnika na zewnętrznym serwerze SMB

Aby skonfigurować serwer SMB, aby można było zapisać kopię zapasową czujnika na dysku zewnętrznym: 

1. Utwórz folder udostępniony na zewnętrznym serwerze SMB. 

2. Pobierz ścieżkę do folderu, nazwę użytkownika i hasło wymagane do uzyskania dostępu do serwera SMB. 

3. W usłudze Defender for IoT Utwórz katalog kopii zapasowych: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Edytuj fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Edytuj lub Utwórz poświadczenia do udostępnienia. Są to poświadczenia serwera SMB: 

   `sudo nano /etc/samba/user` 

6. Dodaj:  

   `username=<user name>` 

   `password=<password>` 

7. Zainstaluj katalog: 

   `sudo mount -a` 

8. Skonfiguruj katalog kopii zapasowej w folderze udostępnionym w usłudze Defender dla czujnika IoT:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Ustaw `Backup.shared_location` wartość `<backup_folder_name_on_cyberx_server>` .

## <a name="see-also"></a>Zobacz też

[Zarządzanie pojedynczymi czujnikami](how-to-manage-individual-sensors.md)
