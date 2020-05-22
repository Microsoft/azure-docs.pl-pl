---
title: Przegląd Change Tracking Azure Automation i spisu
description: W tym artykule opisano Change Tracking i funkcję spisu, która pomaga identyfikować zmiany oprogramowania i usług firmy Microsoft, które występują w danym środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: ab091ba413a8429a8fea131c643cceee7007f927
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744362"
---
# <a name="change-tracking-and-inventory-overview"></a>Przegląd Change Tracking i spisu

W tym artykule przedstawiono Change Tracking i spis w Azure Automation. Ta funkcja śledzi zmiany w maszynach wirtualnych i infrastrukturze serwerów, aby ułatwić identyfikowanie problemów operacyjnych i środowiska przy użyciu oprogramowania zarządzanego przez Menedżera pakietów dystrybucji. Elementy, które są śledzone przez Change Tracking i spis obejmują: 

- Oprogramowanie systemu Windows
- Oprogramowanie systemu Linux (pakiety)
- Pliki systemów Windows i Linux
- Klucze rejestru systemu Windows
- Usługi firmy Microsoft
- Demony systemu Linux

> [!NOTE]
> Aby śledzić zmiany właściwości Azure Resource Manager, zobacz [historię zmian](../governance/resource-graph/how-to/get-resource-changes.md)grafu zasobów platformy Azure.

Change Tracking i zapasy pobierają swoje dane z Azure Monitor. Maszyny wirtualne połączone z obszarami roboczymi Log Analytics używają agentów Log Analytics do zbierania danych o zmianach w zainstalowanym oprogramowaniu, usługach firmy Microsoft, rejestrze i plikach systemu Windows oraz wszystkich demonach z systemem Linux na monitorowanych serwerach. Gdy dane są dostępne, agenci wysyłają je do Azure Monitor do przetwarzania. Azure Monitor stosuje logikę do odbieranych danych, rejestruje ją i udostępnia. 

Funkcja Change Tracking i spis włącza zarówno obszary działania śledzenia zmian, jak i spisu w programie Azure Automation. Ponieważ oba obszary używają tego samego agenta Log Analytics, proces dodawania maszyny wirtualnej jest taki sam w każdym obszarze funkcjonalnym. 

> [!NOTE]
> Aby korzystać z funkcji Change Tracking i spisu, należy zlokalizować wszystkie maszyny wirtualne w tej samej subskrypcji i regionie konta usługi Automation.

Change Tracking i spis nie obsługują obecnie następujących elementów:

* Rekursja śledzenia rejestru systemu Windows
* Systemy plików sieciowych
* Różne metody instalacji
* *pliki **. exe** dla systemu Windows

Inne ograniczenia:

* Kolumny **Maksymalny rozmiar pliku** i wartości są nieużywane w bieżącej implementacji.
* Jeśli zbierasz więcej niż 2500 plików w cyklu zbierania danych o 30 minutach, może to spowodować spadek wydajności śledzenia zmian i spisu.
* Gdy ruch sieciowy jest wysoki, wyświetlanie rekordów może potrwać do 6 godzin.
* Jeśli zmodyfikujesz konfigurację podczas zamykania komputera, komputer może opublikować zmiany należące do poprzedniej konfiguracji.

W Change Tracking i spisu są obecnie występują następujące problemy:

* Aktualizacje poprawek nie są zbierane na maszynach podstawowych RS3 systemu Windows Server 2016.
* Demony systemu Linux mogą wyświetlać zmieniony stan nawet wtedy, gdy nie nastąpiła żadna zmiana. Ten problem występuje ze względu na sposób, w jaki `SvcRunLevels` dane w dzienniku Azure monitor [zmianakonfiguracji](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) są przechwytywane.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Change Tracking i spis są obsługiwane we wszystkich systemach operacyjnych, które spełniają wymagania dotyczące Log Analytics agenta. Wersje systemu operacyjnego Windows, które są obsługiwane oficjalnie to Windows Server 2008 z dodatkiem SP1 lub nowszym oraz Windows 7 z dodatkiem SP1 lub nowszym. Obsługiwane są również różne systemy operacyjne Linux. Zobacz [Omówienie agenta log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Change Tracking i spis wymagają wyłącznie adresów sieciowych wymienionych w następnej tabeli. Komunikacja z tymi adresami korzysta z portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Change Tracking i interfejs użytkownika spisu

Użyj Change Tracking i spisu w Azure Portal, aby wyświetlić podsumowanie zmian dla monitorowanych komputerów. Ta funkcja jest dostępna po wybraniu jednej z opcji dodawania maszyn wirtualnych do **śledzenia zmian** lub **spisu** w obszarze **Zarządzanie konfiguracją** na koncie usługi Automation.  

![Pulpit nawigacyjny Change Tracking](./media/change-tracking/change-tracking-dash01.png)

Listy rozwijane są dostępne w górnej części pulpitu nawigacyjnego, aby ograniczyć wykres śledzenia zmian i szczegółowe informacje na podstawie typu zmiany i przedziałów czasu. Możesz również kliknąć i przeciągnąć na wykres, aby wybrać niestandardowy zakres czasu. 

Możesz kliknąć zmianę lub zdarzenie, aby wyświetlić jego szczegóły. Dostępne typy zmian to:

* Zdarzenia
* Demonów
* Pliki
* Rejestr
* Oprogramowanie
* Usługi firmy Microsoft

Możesz dodawać, modyfikować lub usuwać każdą zmianę. W poniższym przykładzie można zobaczyć zmianę typu uruchamiania usługi z ręcznego na automatycznie.

![Szczegóły Change Tracking](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>Śledzenie zmian plików

Do śledzenia zmian w plikach w systemach Windows i Linux Change Tracking i spisu są stosowane skróty MD5 plików. Funkcja używa skrótów do wykrywania, czy zmiany zostały wprowadzone od ostatniego spisu.

## <a name="tracking-of-file-content-changes"></a>Śledzenie zmian zawartości plików

Change Tracking i spis umożliwia wyświetlenie zawartości pliku systemu Windows lub Linux przed zmianą pliku i po nim. Dla każdej zmiany pliku Change Tracking i spis przechowuje zawartość pliku na [koncie usługi Azure Storage](../storage/common/storage-create-storage-account.md). Podczas śledzenia pliku można wyświetlić jego zawartość przed zmianą lub po niej. Zawartość można wyświetlić w postaci wbudowanej lub obok siebie. 

![Wyświetlanie zmian w pliku](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Śledzenie kluczy rejestru

Change Tracking i spis umożliwia monitorowanie zmian w kluczach rejestru. Monitorowanie pozwala wskazać punkty rozszerzalności, w których można aktywować kod i złośliwe oprogramowanie innych firm. W poniższej tabeli wymieniono wstępnie skonfigurowane (ale nie włączone) klucze rejestru. Aby śledzić te klucze, należy włączyć każdą z nich.

> [!div class="mx-tdBreakAll"]
> |Klucz rejestru | Przeznaczenie |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skrypty uruchamiane podczas uruchamiania.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skrypty uruchamiane przy zamykaniu.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoruje klucze, które są ładowane przed zalogowaniem się użytkownika do konta systemu Windows. Ten klucz jest używany dla aplikacji 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje zmiany w ustawieniach aplikacji.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu **Explorer. exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory dla rejestracji obsługi nakładki ikon.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację obsługi nakładki ikon dla aplikacji 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nowe wtyczki obiektów pomocników przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do Document Object Model (DOM) bieżącej strony i kontrolowania nawigacji.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nowe wtyczki obiektów pomocników przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do Document Object Model (DOM) bieżącej strony i kontrolowania nawigacji dla aplikacji 32-bitowych działających na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitoruje nowe rozszerzenia programu Internet Explorer, takie jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitoruje nowe rozszerzenia programu Internet Explorer, takie jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi dla aplikacji 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32-bitowe sterowniki skojarzone z wavemapper, wave1 i Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 i vidc. Podobnie jak w sekcji [Drivers] w pliku **System. ini** .
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32-bitowe sterowniki skojarzone z wavemapper, wave1 i Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 i vidc dla aplikacji 32-bitowych uruchomionych na komputerach 64-bitowych. Podobnie jak w sekcji [Drivers] w pliku **System. ini** .
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje listę znanych lub powszechnie używanych systemowych bibliotek DLL. Ten system uniemożliwia osobom wykorzystywanie słabych uprawnień katalogu aplikacji przez porzucanie w systemie plików DLL systemu koń trojański.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje listę pakietów, które mogą odbierać powiadomienia o zdarzeniach z usługi **Winlogon. exe**, model obsługi logowania interaktywnego dla systemu Windows.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Obsługa monitorowania integralności plików w Azure Security Center

Change Tracking i spis używają [Azure Security Center monitorowania integralności plików (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Podczas gdy program FIM monitoruje tylko pliki i rejestry, pełna funkcja Change Tracking i spisu obejmuje również śledzenie dla:

- Zmiany oprogramowania
- Usługi firmy Microsoft
- Demony systemu Linux

## <a name="recursion-support"></a>Obsługa rekursji

Change Tracking i spis obsługuje rekursję, co pozwala na określenie symboli wieloznacznych, aby uprościć śledzenie w katalogach. Rekursja udostępnia również zmienne środowiskowe umożliwiające śledzenie plików w środowiskach z wieloma lub dynamicznymi nazwami dysków. Poniższa lista zawiera typowe informacje, które należy znać podczas konfigurowania rekursji:

* Do śledzenia wielu plików wymagane są symbole wieloznaczne.
* Symboli wieloznacznych można używać tylko w ostatnim segmencie ścieżki, na przykład ** \\ plik c:\Folder*** lub **/etc/*. conf**.
* Jeśli zmienna środowiskowa ma nieprawidłową ścieżkę, walidacja powiedzie się, ale ścieżka kończy się niepowodzeniem podczas wykonywania.
* Należy unikać ogólnych nazw ścieżek podczas ustawiania ścieżki, ponieważ ten typ ustawienia może spowodować przechodzenie przez zbyt wiele folderów.

## <a name="change-tracking-and-inventory-data-collection"></a>Zbieranie danych dotyczących Change Tracking i spisu

W następnej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian obsługiwanych przez Change Tracking i spis. Dla każdego typu migawka danych bieżącego stanu jest również odświeżana co najmniej co 24 godziny.

| **Zmiana typu** | **Częstotliwość** |
| --- | --- |
| Rejestr systemu Windows | 50 minut |
| Plik systemu Windows | 30 minut |
| Plik systemu Linux | 15 minut |
| Usługi firmy Microsoft | 10 sekund do 30 minut</br> Wartość domyślna: 30 minut |
| Demony systemu Linux | 5 minut |
| Oprogramowanie systemu Windows | 30 minut |
| Oprogramowanie systemu Linux | 5 minut |

W poniższej tabeli przedstawiono limity śledzonych elementów na maszynę dla Change Tracking i spisu.

| **Zasób** | **Limit** |
|---|---|---|
|Plik|500|
|Rejestr|250|
|Oprogramowanie systemu Windows (bez poprawek) |250|
|Pakiety systemu Linux|1250|
|Usługi|250|
|Demonów|250|

Średnie użycie danych Log Analytics dla maszyny przy użyciu Change Tracking i spisu wynosi około 40 MB miesięcznie, w zależności od środowiska. Korzystając z funkcji użycie i szacowane koszty w obszarze roboczym Log Analytics, można wyświetlić dane pozyskane przez Change Tracking i spis na wykresie użycia. Możesz użyć tego widoku danych, aby oszacować użycie danych i określić, jak ma to wpływ na rachunek. Zobacz temat [Omówienie kosztów użytkowania i szacowania](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs).  

### <a name="microsoft-service-data"></a>Dane usługi firmy Microsoft

Domyślna częstotliwość zbierania danych dla usług firmy Microsoft to 30 minut. Częstotliwość można skonfigurować za pomocą suwaka na karcie **usługi firmy Microsoft** w obszarze **Edytuj ustawienia**. 

![Suwak usług firmy Microsoft](./media/change-tracking/windowservices.png)

Aby zoptymalizować wydajność, Agent Log Analytics śledzi jedynie zmiany. Ustawienie wysokiego progu może pominąć zmiany, jeśli usługa powróci do stanu pierwotnego. Ustawienie częstotliwości na mniejszą wartość umożliwia przechwycenie zmian, które mogą zostać pominięte w przeciwnym razie.

> [!NOTE]
> Gdy Agent może śledzić zmiany w przedziale czasowym na 10 sekund, dane w Azure Portal nadal trwają kilka minut. Zmiany, które wystąpiły w czasie wyświetlania w portalu, są nadal śledzone i rejestrowane.

## <a name="support-for-alerts-on-configuration-state"></a>Obsługa alertów dotyczących stanu konfiguracji

Kluczową możliwością Change Tracking i spisu jest alert dotyczący zmian stanu konfiguracji środowiska hybrydowego. Wiele przydatnych akcji jest dostępnych do wyzwalania w odpowiedzi na alerty, na przykład akcji w usłudze Azure Functions, elementów Runbook usługi Automation, elementy webhook i podobne. Alerty dotyczące zmian w pliku **C:\windows\System32\drivers\etc\HOSTS** na komputerze to jedna dobra aplikacja dla Change Tracking i danych spisu. Istnieje wiele więcej scenariuszy dotyczących alertów, łącznie z scenariuszami zapytań zdefiniowanymi w następnej tabeli. 

|Zapytanie  |Opis  |
|---------|---------|
|Zmianakonfiguracji <br>&#124; gdzie ConfigChangeType = = "Files" i FileSystemPath zawiera "c: \\ Windows \\ system32 \\ Drivers \\ "|Przydatne do śledzenia zmian w plikach krytycznych dla systemu.|
|Zmianakonfiguracji <br>&#124;, gdzie FieldsChanged zawiera "FileContentChecksum" i FileSystemPath = = "c: \\ Windows \\ system32 \\ Drivers \\ itp. \\ hosts"|Przydatne do śledzenia modyfikacji plików konfiguracji kluczy.|
|Zmianakonfiguracji <br>&#124; gdzie ConfigChangeType = = "Microsoft Services" i SvcName zawiera "W3SVC" i SvcState = = "zatrzymana"|Przydatne do śledzenia zmian w usługach krytycznych dla systemu.|
|Zmianakonfiguracji <br>&#124;, gdzie ConfigChangeType = = "demony" i SvcName zawierają "SSH" i SvcState! = "uruchomiona"|Przydatne do śledzenia zmian w usługach krytycznych dla systemu.|
|Zmianakonfiguracji <br>&#124; gdzie ConfigChangeType = = "oprogramowanie" i ChangeCategory = = "dodane"|Przydatne w przypadku środowisk, które wymagają blokowania konfiguracji oprogramowania.|
|ConfigurationData <br>&#124;, gdzie Oprogramowaniename zawiera "Monitoring Agent" i CurrentVersion! = "8.0.11081.0"|Przydatne do wyświetlania maszyn, na których zainstalowano nieaktualną lub niezgodną wersję oprogramowania. To zapytanie raportuje o ostatnim raportowanym stanie konfiguracji, ale nie raportuje zmian.|
|Zmianakonfiguracji <br>&#124;, gdzie RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ oprogramowania \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Przydatne do śledzenia zmian kluczowych kluczy programu antywirusowego.|
|Zmianakonfiguracji <br>&#124; gdzie RegistryKey zawiera @ "HKEY_LOCAL_MACHINE \\ system \\ CurrentControlSet \\ Services \\ SharedAccess \\ Parameters \\ firewallpolicy"| Przydatne do śledzenia zmian w ustawieniach zapory.|

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie Change Tracking i spisem](change-tracking-file-contents.md)
* [Dzienniki wyszukiwania w dziennikach Azure Monitor](../log-analytics/log-analytics-log-searches.md)
* [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md)
* [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure](automation-tutorial-troubleshoot-changes.md)