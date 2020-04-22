---
title: Śledzenie zmian za pomocą usługi Azure Automation
description: Rozwiązanie do śledzenia zmian ułatwia identyfikowanie zmian oprogramowania i usługi Systemu Windows, które występują w twoim środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682970"
---
# <a name="track-environment-changes-with-change-tracking"></a>Śledzenie zmian w środowisku za pomocą śledzenia zmian

Ten artykuł ułatwia korzystanie z rozwiązania śledzenia zmian, aby łatwo identyfikować zmiany w środowisku. Rozwiązanie śledzi następujące zmiany konfiguracji, aby ułatwić zidentyfikowanie problemów operacyjnych:

- Oprogramowanie systemu Windows
- Oprogramowanie Linux (pakiety)
    >[!NOTE]
    >Śledzenie zmian śledzi tylko oprogramowanie zarządzane za pomocą menedżera pakietów dystrybucyjnych.

- Pliki z systemami Windows i Linux
- Klucze rejestru systemu Windows
- Usługi systemu Windows
- Demony Linuksa

Po włączeniu rozwiązania można wyświetlić podsumowanie zmian dla monitorowanych komputerów, wybierając pozycję **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją** na koncie automatyzacji.

> [!NOTE]
> Śledzenie zmian automatyzacji platformy Azure śledzi zmiany na maszynach wirtualnych. Aby śledzić zmiany właściwości usługi Azure Resource Manager, zobacz [Historia zmian](../governance/resource-graph/how-to/get-resource-changes.md)w grafie zasobów platformy Azure .

Można wyświetlić zmiany na komputerach, a następnie przejść do szczegółów dla każdego zdarzenia. Listy rozwijane są dostępne w górnej części wykresu, aby ograniczyć wykres i szczegółowe informacje na podstawie typu zmiany i zakresów czasu. Można również kliknąć i przeciągnąć na wykresie, aby wybrać niestandardowy zakres czasu. **Zmiana typu** będzie jedną z następujących wartości **Zdarzenia**, **Demony**, **Pliki**, **Rejestr**, **Oprogramowanie**, Usługi **systemu Windows**. Kategoria pokazuje typ zmiany i może być **dodawany, zmodyfikowany**lub **usunięty**. **Added**

![obraz pulpitu nawigacyjnego śledzenia zmian](./media/change-tracking/change-tracking-dash01.png)

Kliknięcie zmiany lub zdarzenia powoduje wyświetlenie szczegółowych informacji o tej zmianie. Jak widać na przykładzie, typ uruchamiania usługi został zmieniony z Ręcznie na Auto.

![obraz szczegółów śledzenia zmian](./media/change-tracking/change-tracking-details.png)

Zmiany w zainstalowanym oprogramowaniu, usługach systemu Windows, rejestrze systemu Windows i plikach oraz demonach systemu Linux na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze w celu przetworzenia. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane. Korzystając z informacji na pulpicie nawigacyjnym śledzenia zmian, można łatwo zobaczyć zmiany wprowadzone w infrastrukturze serwera.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

### <a name="windows-operating-systems"></a>Systemy operacyjne Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane przez agenta systemu Windows:

* Windows Server 2008 R2 lub nowszy

### <a name="linux-operating-systems"></a>Systemy operacyjne Linux

Następujące dystrybucje Linuksa są oficjalnie obsługiwane. Jednak agent systemu Linux może również działać na innych dystrybucjach, które nie są wymienione. O ile nie zaznaczono inaczej, wszystkie wersje pomocnicze są obsługiwane dla każdej wersji głównej na liście.

#### <a name="64-bit"></a>64-bitowy

* CentOS 6 i 7
* Amazon Linux 2017.09
* Oracle Linux 6 i 7
* Serwer Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32-bitowa

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14.04 LTS i 16.04 LTS

## <a name="limitations"></a>Ograniczenia

Rozwiązanie śledzenia zmian nie obsługuje obecnie następujących elementów:

* Rekursja do śledzenia rejestru systemu Windows
* Sieciowe systemy plików
* Różne metody instalacji
* *Pliki **exe** dla systemu Windows

Inne ograniczenia:

* **Maksymalny rozmiar pliku** kolumny i wartości są nieużywane w bieżącej implementacji.
* Jeśli zbierzesz więcej niż 2500 plików w 30-minutowym cyklu zbierania, wydajność rozwiązania może ulec pogorszeniu.
* Gdy ruch sieciowy jest wysoki, wyświetlanie rekordów zmian może potrwać do sześciu godzin.
* Jeśli zmodyfikujesz konfigurację podczas zamykania komputera, komputer może zaksięgować zmiany należące do poprzedniej konfiguracji.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie śledzenia zmian występuje obecnie następujące problemy:

* Aktualizacje poprawek nie są zbierane na komputerach rs3 systemu Windows Server 2016 Core.
* Demony Linuksa mogą wykazywać zmieniony stan, nawet jeśli nie nastąpiła żadna zmiana. Wynika to z `SvcRunLevels` sposobu przechwytywania pola.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Śledzenie zmian wymaga w szczególności następujących adresów. Komunikacja na te adresy używa portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Symbol wieloznaczny, rekursja i ustawienia środowiska

Rekursja umożliwia określenie symboli wieloznacznych w celu uproszczenia śledzenia między katalogami i zmiennych środowiskowych, aby umożliwić śledzenie plików w środowiskach z wieloma lub dynamicznymi nazwami dysków. Na poniższej liście znajdują się typowe informacje, które należy znać podczas konfigurowania rekursji:

* Symbole wieloznaczne są wymagane do śledzenia wielu plików.
* Symbole wieloznaczne mogą być używane tylko w ostatnim segmencie\\ścieżki, na przykład c:\folder file* lub /etc/*.conf.
* Jeśli zmienna środowiskowa ma nieprawidłową ścieżkę, sprawdzanie poprawności powiedzie się, ale ta ścieżka kończy się niepowodzeniem po uruchomieniu magazynu.
* Unikaj ścieżek ogólnych podczas ustawiania ścieżki, ponieważ tego typu ustawienie może spowodować przechodzenie zbyt wielu folderów.

## <a name="change-tracking-data-collection-details"></a>Zmień szczegóły zbierania danych śledzenia

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu migawka danych bieżącego stanu jest również odświeżana co najmniej co 24 godziny:

| **Zmień typ** | **Częstotliwość** |
| --- | --- |
| Rejestr systemu Windows | 50 min. |
| Plik systemu Windows | 30 minut |
| Plik Linuksa | 15 minut |
| Usługi systemu Windows | Od 10 sekund do 30 minut</br> Domyślnie: 30 minut |
| Demony Linuksa | 5 minut |
| Oprogramowanie systemu Windows | 30 minut |
| Oprogramowanie Linux | 5 minut |

W poniższej tabeli przedstawiono limity śledzonych towarów na maszynę do śledzenia zmian.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie systemu Windows|250|Nie zawiera poprawek oprogramowania|
|Pakiety Linuksa|1250||
|Usługi|250||
|Daemon|250||

Średnie użycie danych usługi Log Analytics dla komputera korzystającego z śledzenia zmian wynosi około 40 MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od środowiska. Zaleca się monitorowanie środowiska, aby zobaczyć dokładne użycie, które masz.

### <a name="windows-service-tracking"></a>Śledzenie usług systemu Windows

Domyślna częstotliwość zbierania dla usług systemu Windows wynosi 30 minut. Aby skonfigurować częstotliwość, przejdź do **śledzenia zmian**. W obszarze **Edytowanie ustawień** na karcie **Usługi systemu Windows** znajduje się suwak umożliwiający zmianę częstotliwości zbierania dla usług systemu Windows z 10 sekund na 30 minut. Przenieś pasek suwaka do żądanej częstotliwości i automatycznie go zapisuje.

![Suwak usług systemu Windows](./media/change-tracking/windowservices.png)

Agent śledzi tylko zmiany, co optymalizuje wydajność agenta. Ustawienie wysokiego progu może zostać pominięte, jeśli usługa powróci do stanu pierwotnego. Ustawienie częstotliwości na mniejszą wartość pozwala na wychwyt zmian, które mogą zostać pominięte w przeciwnym razie.

> [!NOTE]
> Agent może śledzić zmiany do 10-sekundowego interwału, ale wyświetlanie danych w portalu zajmuje kilka minut. Zmiany w czasie wyświetlania w portalu są nadal śledzone i rejestrowane.

### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Celem monitorowania zmian w kluczach rejestru jest określenie punktów rozszerzalności, w których można aktywować kod i złośliwe oprogramowanie innych firm. Na poniższej liście przedstawiono listę wstępnie skonfigurowanych kluczy rejestru. Te klucze są skonfigurowane, ale nie włączone. Aby śledzić te klucze rejestru, należy włączyć każdy z nich.

> [!div class="mx-tdBreakAll"]
> |Klucz rejestru | Przeznaczenie |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skrypty uruchamiane podczas uruchamiania.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skrypty uruchamiane przy zamykaniu systemu.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoruje klucze, które są ładowane, zanim użytkownik zaloguje się do swojego konta systemu Windows. Klucz jest używany w przypadku programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje zmiany w ustawieniach aplikacji.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje typowe wpisy autostartu, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w toku za pomocą explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację programu obsługi nakładek ikon.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację programu obsługi nakładek ikon dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory dla nowych wtyczek obiektów pomocnika przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do modelu obiektowego dokumentu (DOM) bieżącej strony i sterowania nawigacją.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitory dla nowych wtyczek obiektów pomocnika przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do modelu obiektowego dokumentu (DOM) bieżącej strony i sterowania nawigacją dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitory nowych rozszerzeń programu Internet Explorer, takich jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitory nowych rozszerzeń programu Internet Explorer, takich jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje sterowniki 32-bitowe związane z wavemapperem, wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc. Podobnie jak w sekcji [sterowniki] w SYSTEMIE. ini.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje sterowniki 32-bitowe związane z wavemapperem, wave1 i wave2, msacm.imaadpcm, .msadpcm, .msgsm610 i vidc dla programów 32-bitowych uruchomionych na komputerach 64-bitowych. Podobnie jak w sekcji [sterowniki] w SYSTEMIE. ini.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje listę znanych lub powszechnie używanych bibliotek DLL systemu; system ten uniemożliwia ludziom wykorzystywanie słabych uprawnień katalogu aplikacji przez upuszczenie w wersjach DLL systemu dll dla koni trojańskich.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje listę pakietów, które mogą odbierać powiadomienia o zdarzeniach od Winlogon, interaktywnego modelu obsługi logowania dla systemu operacyjnego Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Włącz śledzenie zmian

Aby rozpocząć śledzenie zmian, należy włączyć rozwiązanie śledzenia zmian. Istnieje wiele sposobów, aby na pokładzie maszyn, aby zmienić śledzenie. Poniżej przedstawiono zalecane i obsługiwane sposoby dołączania rozwiązania.

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Od przeglądania wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Z konta Automation](automation-onboard-solutions-from-automation-account.md)
* [Z elementem uruchamianym usługi Azure Automation](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Konfigurowanie śledzenia zmian

Aby dowiedzieć się, jak przydzielić komputery do rozwiązania, zobacz [Rozwiązania automatyzacji dołączania](automation-onboard-solutions-from-automation-account.md). Po wprowadzeniu komputera z rozwiązaniem śledzenia zmian można skonfigurować elementy do śledzenia. Po włączeniu nowego pliku lub klucza rejestru do śledzenia, jest włączony dla obu śledzenia zmian.

Do śledzenia zmian w plikach zarówno w systemie Windows, jak i linux, używane są skróty MD5 plików. Skróty tezy są następnie używane do wykrywania, czy zmiana została wy uczyniona od czasu ostatniego spisu.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Włącz monitorowanie integralności plików w usłudze Azure Security Center

Usługa Azure Security Center dodała monitorowanie integralności plików (FIM), które jest oparte na śledzeniu zmian platformy Azure. Podczas gdy FIM monitoruje tylko pliki i rejestry, pełne rozwiązanie śledzenia zmian obejmuje również:

- Zmiany w oprogramowaniu
- Usługi systemu Windows
- Demony Linuksa

Jeśli masz już włączoną funkcję FIM i chcesz wypróbować pełne rozwiązanie śledzenia zmian, musisz wykonać następujące kroki. Ustawienia nie są usuwane przez ten proces.

> [!NOTE]
> Włączenie pełnego rozwiązania do śledzenia zmian może spowodować dodatkowe opłaty, aby uzyskać więcej informacji, zobacz [Cennik automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

1. Usuń rozwiązanie do monitorowania, przechodząc do obszaru roboczego i lokalizując je na [liście zainstalowanych rozwiązań monitorujących](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij przycisk Usuń, jak opisano w [Usuń rozwiązanie monitorujące](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Włącz ponownie rozwiązanie, przechodząc do konta Automatyzacja i wybierając pozycję **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.
4. Potwierdź szczegóły ustawienia obszaru roboczego i kliknij przycisk **Włącz**.

## <a name="configure-file-content-change-tracking"></a>Konfigurowanie śledzenia zmian zawartości pliku

Zawartość można wyświetlić przed i po zmianie pliku za pomocą śledzenia zmian zawartości pliku. Ta funkcja jest dostępna dla plików Windows i Linux. Dla każdej zmiany pliku zawartość pliku jest przechowywana na koncie magazynu. Plik jest wyświetlany przed i po zmianie, w linii lub obok siebie. Aby dowiedzieć się więcej, zobacz [Wyświetlanie zawartości śledzonego pliku](change-tracking-file-contents.md).

![wyświetlanie zmian w pliku](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru systemu Windows do śledzenia

Aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie Śledzenie zmian wybierz pozycję **Rejestr systemu Windows**, a następnie kliknij przycisk + **Dodaj,** aby dodać nowy klucz rejestru do śledzenia.
3. W stronie **Dodaj rejestr systemu Windows do śledzenia zmian**wprowadź informacje dotyczące klucza do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa klucza rejestru do śledzenia.        |
|Grupa     | Nazwa grupy logicznego grupowania kluczy rejestru.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzenia klucza rejestru. Na przykład: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Konfigurowanie śledzenia plików w systemie Windows

Aby skonfigurować śledzenie plików na komputerach z systemem Windows, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie Śledzenie zmian wybierz pozycję **Pliki systemu Windows**, a następnie kliknij przycisk + **Dodaj,** aby dodać nowy plik do śledzenia.
3. W polu **Dodawanie pliku systemu Windows do śledzenia zmian**wprowadź informacje dotyczące pliku do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Wartość true, jeśli ustawienie jest stosowane, a False w inny sposób.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzenia pliku, na przykład **c:\temp\\\*.txt**<br>Można również użyć zmiennych środowiskowych, takich jak `%winDir%\System32\\\*.*`.       |
|Rekursja     | Wartość true if rekursion jest używany podczas poszukiwania elementu do śledzenia i False w inny sposób.        |
|Przekaż zawartość pliku dla wszystkich ustawień| True do przekazywania zawartości pliku na śledzone zmiany i False w inny sposób.|

## <a name="configure-file-tracking-on-linux"></a>Konfigurowanie śledzenia plików w systemie Linux

Aby skonfigurować śledzenie plików na komputerach z systemem Linux, należy wykonać następujące czynności:

1. Na koncie automatyzacji wybierz pozycję **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij **pozycję Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie Śledzenie zmian wybierz pozycję **Pliki systemu Linux**, a następnie kliknij przycisk + **Dodaj,** aby dodać nowy plik do śledzenia.
3. W polu **Dodaj plik linuksa do śledzenia zmian**wprowadź informacje dotyczące pliku lub katalogu, aby śledzić i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony.        |
|Grupa     | Nazwa grupy logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzenia pliku. Na przykład: "/etc/*.conf"       |
|Typ ścieżki     | Typ elementu do śledzenia, możliwe wartości to Plik i Katalog.        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje łącza symboliczne i nie zawiera plików/katalogów, do których się odwołuje.<br>**Wykonaj** - Następuje do łączy symbolicznych podczas rekursji, a także zawiera pliki /katalogi, do których się odwołuje.<br>**Zarządzanie** — podąża za dowiązaniem symbolicznym i umożliwia zmianę zwracanej zawartości.     |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

> [!NOTE]
> Opcja linków „Zarządzaj” nie jest zalecana. Pobieranie zawartości plików nie jest obsługiwane.

## <a name="search-logs"></a>Dzienniki wyszukiwania

Można wykonać różne wyszukiwania względem dzienników dla rekordów zmian. Po otwarciu strony Śledzenie zmian kliknij pozycję **Usługa Log Analytics**, spowoduje otwarcie strony Dzienniki. Poniższa tabela zawiera przykładowe wyszukiwanie dzienników rekordów zmian zebranych przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData (Dane konfiguracyjne)<br>&#124; gdzie ConfigDataType == "WindowsServices" i SvcStartupType == "Auto"<br>&#124; gdzie SvcState == "Zatrzymane"<br>&#124; podsumuj arg_max(TimeGenerated, *) przez SoftwareName, Komputer         | Pokazuje najnowsze rekordy zapasów dla Usług systemu Windows, które zostały ustawione na Automatyczne, ale zostały zgłoszone jako zatrzymane<br>Wyniki są ograniczone do najnowszego rekordu dla tego SoftwareName i Computer      |
|KonfiguracjaZmija<br>&#124; gdzie ConfigChangeType == "Oprogramowanie" i Zmiana Kategorii == "Usunięto"<br>&#124; zamówienie według timegenerated desc|Pokazuje rekordy zmian dla usuniętego oprogramowania|

## <a name="alert-on-changes"></a>Alert o zmianach

Kluczową możliwością śledzenia zmian jest alerty o stanie konfiguracji i wszelkich zmianach stanu konfiguracji środowiska hybrydowego. Poniższy przykład pokazuje, że plik **C:\windows\system32\drivers\etc\hosts** został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ system Windows używa go do rozpoznawania nazw hostów na adresy IP. Ta operacja ma pierwszeństwo przed systemem DNS i może spowodować problemy z łącznością lub przekierowanie ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/change-tracking/changes.png)

Aby przeanalizować tę zmianę dalej, przejdź do wyszukiwania dzienników, klikając pozycję **Usługa Log Analytics**. Po zakończeniu wyszukiwania w dzienniku wyszukaj `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`zmiany zawartości w pliku Hosts za pomocą kwerendy . Ta kwerenda wyszukuje zmiany, które obejmowały zmianę zawartości pliku dla plików, których w pełni kwalifikowana ścieżka zawiera słowo "hosty". Można również poprosić o konkretny plik, zmieniając część ścieżki do `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`jego w pełni kwalifikowanego formularza (np. ).

Po zwrócenie żądanych wyników, kliknij przycisk **Nowa reguła alertu** w wyszukiwaniu dziennika, aby otworzyć stronę tworzenia alertu. Można również przejść do tego środowiska za pośrednictwem **usługi Azure Monitor** w witrynie Azure portal. 

Sprawdź kwerendę ponownie i zmodyfikuj logikę alertu. W takim przypadku chcesz alertu, które mają być wyzwalane, jeśli istnieje nawet jedna zmiana wykryte we wszystkich komputerach w środowisku.

![Obraz przedstawiający kwerendę zmian do śledzenia zmian w pliku hosts](./media/change-tracking/change-query.png)

Po ustawieniu logiki warunku należy przypisać grupy akcji do wykonywania akcji w odpowiedzi na wyzwalany alert. W tym przypadku założyłem e-maile do wysłania i bilet ITSM do utworzenia.  Wiele innych przydatnych akcji można również podjąć, takich jak wyzwalanie funkcji platformy Azure, element runbook automatyzacji, element webhook lub aplikacji logiki.

![Obraz konfigurujący grupę akcji w celu ostrzeżenia o zmianie](./media/change-tracking/action-groups.png)

Po ustawieniu wszystkich parametrów i logiki możemy zastosować alert do środowiska.

### <a name="alert-suggestions"></a>Sugestie alertów

Podczas alertów o zmianach w pliku hosts jest jednym dobrym zastosowaniem alertów dla śledzenia zmian lub danych zapasów, istnieje wiele innych scenariuszy alertów, w tym przypadków zdefiniowanych wraz z ich przykładowych zapytań w sekcji poniżej.

|Zapytanie  |Opis  |
|---------|---------|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Pliki" i FileSystemPath\\zawiera\\"\\c:\\windows system32 sterowniki "|Przydatne do śledzenia zmian w systemowych plikach krytycznych|
|KonfiguracjaZmija <br>&#124; gdzie FieldsChanged zawiera "FileContentChecksum" i\\FileSystemPath ==\\"c:\\windows\\system32\\sterowniki itp.|Przydatne do śledzenia modyfikacji kluczowych plików konfiguracyjnych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "WindowsServices" i SvcName zawiera "w3svc" i SvcState == "Stopped"|Przydatne do śledzenia zmian w krytycznych usługach systemowych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Demony" i SvcName zawiera "ssh" i SvcState != "Running"|Przydatne do śledzenia zmian w krytycznych usługach systemowych|
|KonfiguracjaZmija <br>&#124; gdzie ConfigChangeType == "Oprogramowanie" i Zmiana Kategorii == "Dodano"|Przydatne w środowiskach, które wymagają zablokowanych konfiguracji oprogramowania|
|ConfigurationData (Dane konfiguracyjne) <br>&#124; gdzie SoftwareName zawiera "Agent monitorowania" i CurrentVersion != "8.0.11081.0"|Przydatne do wyświetlania, które maszyny mają zainstalowaną wersję oprogramowania przestarzałą lub niezgodną. Raportuje ostatni zgłoszony stan konfiguracji, a nie zmiany.|
|KonfiguracjaZmija <br>&#124; gdzie RegistryKey == @"HKEY_LOCAL_MACHINE\\OPROGRAMOWANIE\\\\Microsoft\\Windows\\CurrentVersion QualityCompat"| Przydatne do śledzenia zmian w kluczowych klawiszach antywirusowych|
|KonfiguracjaZmija <br>&#124; gdzie RegistryKey zawiera @"HKEY_LOCAL_MACHINE\\SYSTEM\\\\CurrentControlSet\\Services SharedAccess\\Parametry\\FirewallPolicy"| Przydatne do śledzenia zmian ustawień zapory|

## <a name="next-steps"></a>Następne kroki

Odwiedź samouczek na temat śledzenia zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów ze zmianami we własnym środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [wyszukiwania dzienników w dziennikach usługi Azure Monitor,](../log-analytics/log-analytics-log-searches.md) aby wyświetlić szczegółowe dane śledzenia zmian.
