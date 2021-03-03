---
title: Rozwiązanie danych telekomunikacyjnych w Azure Monitor | Microsoft Docs
description: Dane przewodowe to skonsolidowane dane dotyczące sieci i wydajności z komputerów z agentami Log Analytics. Dane sieciowe są połączone z danymi Twojego dziennika, aby ułatwić korelowanie danych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 5981a5f136d613ffcedda86797d807d2eecfab0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713630"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Rozwiązanie Wire Data 2.0 (wersja zapoznawcza) w Azure Monitor

![Symbol rozwiązania Dane o komunikacji sieciowej](media/wire-data/wire-data2-symbol.png)

Dane przewodowe to skonsolidowane dane dotyczące sieci i wydajności zebrane z komputerów połączonych z systemem Windows i Linux z agentem Log Analytics, w tym te monitorowane przez Operations Manager w danym środowisku. Dane sieciowe są łączone z innymi danymi Twojego dziennika, aby ułatwić korelowanie danych.

Oprócz agenta Log Analytics rozwiązanie do transmisji danych używa agentów zależności firmy Microsoft instalowanych na komputerach w infrastrukturze IT. Agenci Dependency Agent monitorują dane sieciowe wysyłane do i z Twoich komputerów dla poziomów sieci 2 i 3 w [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), włącznie z różnymi używanymi protokołami i portami. Dane są następnie wysyłane do Azure Monitor przy użyciu agentów.  

>[!NOTE]
>Rozwiązanie do przesyłania danych o komunikacji sieciowej zostało zastąpione [Service map rozwiązaniem](../vm/service-map.md).  Zarówno Agent Log Analytics, jak i Agent zależności, służą do zbierania danych połączenia sieciowego do Azure Monitor. 
> 
>Istniejący klienci korzystający z rozwiązania do przesyłania danych w sieci mogą nadal z niego korzystać. Opublikujemy wskazówki dotyczące osi czasu migracji na potrzeby przenoszenia do Service Map.
>
>Nowi klienci powinni zainstalować [rozwiązanie Service map](../vm/service-map.md) lub [szczegółowe informacje o maszynie wirtualnej](../vm/vminsights-overview.md).  Zestaw danych Service Map jest porównywalny z danymi telekomunikacyjnymi.  Usługi VM Insights zawierają zestaw danych Service Map z dodatkowymi danymi i funkcjami wydajności na potrzeby analizy. 


Domyślnie Azure Monitor rejestruje dane dotyczące procesora CPU, pamięci, dysku i wydajności sieci z liczników wbudowanych w systemy Windows i Linux, a także inne liczniki wydajności, które można określić. Gromadzenie danych sieciowych i innych odbywa się w czasie rzeczywistym dla każdego agenta, łącznie z podsieciami i protokołami poziomu aplikacji używanymi przez komputer.  Rozwiązanie Dane o komunikacji sieciowej bierze pod uwagę dane sieciowe na poziomie aplikacji, a nie niżej, w warstwie transportu TCP.  Rozwiązanie nie bierze pod uwagę pojedynczych komunikatów potwierdzeń i synchronizacji.  Po zakończeniu uzgadniania połączenie jest uznawane za aktywne i oznaczane jako Połączono. To połączenie pozostaje aktywne, dopóki obie strony zgadzają się, że gniazdo jest otwarte i dane można przekazywać tam i z powrotem.  Po obu stronach połączenie zostanie oznaczone jako odłączone.  W związku z tym zliczana jest tylko przepustowość pomyślnie zakończonych pakietów. Pakiety wysłane ponownie lub zakończone niepowodzeniem nie są raportowane.

Jeśli zdarzało Ci się korzystać z programu [sFlow](http://www.sflow.org/) albo innego oprogramowania z [protokołem NetFlow firmy Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), statystyki i dane wyświetlane w danych o komunikacji sieciowej będą wyglądać znajomo.

Do niektórych typów wbudowanych zapytań przeszukiwania dzienników należą:

- Agenci udostępniający dane o komunikacji sieciowej
- Adres IP agentów udostępniających dane o komunikacji sieciowej
- Komunikacja wychodząca według adresów IP
- Liczba wysłanych bajtów według protokołów aplikacji
- Liczba bajtów wysłanych przez usługę aplikacji
- Bajty odebrane przez różne protokoły
- Całkowita liczba bajtów wysłanych i odebranych według wersji protokołu IP
- Średnie opóźnienie dla połączeń, które zostały wiarygodnie zmierzone
- Procesy komputera, które zainicjowały lub odebrały ruch sieciowy
- Ilość ruchu sieciowego dla procesu

Podczas wyszukiwania przy użyciu danych o komunikacji sieciowej możesz filtrować i grupować dane, aby wyświetlić informacje o najczęściej używanych agentach i protokołach. Możesz też sprawdzić, kiedy niektóre komputery (adresy IP/adresy MAC) komunikowały się ze sobą, ile to trwało i ile danych wysłano. Zasadniczo możesz przeglądać metadane dotyczące ruchu sieciowego, przeszukując je.

Warto jednak podkreślić, że prezentowane są metadane, a więc niekoniecznie będzie to przydatne przy rozwiązywaniu bardziej złożonych problemów. Dane przewodowe w Azure Monitor nie są pełnym przechwyceniem danych sieci.  Nie są one przeznaczone do rozwiązywania problemów na poziomie pakietów. Zaletą korzystania z agenta w porównaniu z innymi metodami zbierania jest to, że nie trzeba instalować urządzeń, ponownie konfigurować przełączników sieciowych ani wykonywać skomplikowanych konfiguracji. Dane o komunikacji sieciowej są po prostu oparte na agencie — instalujesz agenta na komputerze i będzie on monitorować własny ruch sieciowy. Inną zaletą jest możliwość monitorowania obciążeń działających u dostawców chmury lub dostawców usług hostingowych albo na platformie Microsoft Azure, gdy użytkownik nie jest właścicielem warstwy sieci szkieletowej.

## <a name="connected-sources"></a>Połączone źródła

Rozwiązanie Dane o komunikacji sieciowej pobiera swoje dane z agenta Microsoft Dependency Agent. Agent zależności zależy od agenta Log Analytics do połączeń Azure Monitor. Oznacza to, że na serwerze musi być zainstalowany i skonfigurowany Agent Log Analytics przy użyciu agenta zależności. W poniższej tabeli opisano połączone źródła obsługiwane przez rozwiązanie Dane o komunikacji sieciowej.

| **Połączone źródło** | **Obsługiwane** | **Opis** |
| --- | --- | --- |
| Agenci dla systemu Windows | Tak | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z komputerów z agentami systemu Windows. <br><br> Oprócz [agenta log Analytics dla systemu Windows](../agents/agent-windows.md)agenci systemu Windows wymagają programu Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](../vm/vminsights-enable-overview.md#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Agenci dla systemu Linux | Tak | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z komputerów z agentami systemu Linux.<br><br> Oprócz [agenta log Analytics dla systemu Linux](../vm/quick-collect-linux-computer.md)agenci z systemem Linux wymagają programu Microsoft Dependency Agent. Zobacz [obsługiwane systemy operacyjne](../vm/vminsights-enable-overview.md#supported-operating-systems), gdzie znajdziesz pełną listę wersji systemu operacyjnego. |
| Grupa zarządzania programu System Center Operations Manager | Tak | Rozwiązanie Dane o komunikacji sieciowej analizuje i gromadzi dane z agentów systemu Windows i Linux w połączonej [grupie zarządzania programu System Center Operations Manager](../agents/om-agents.md). <br><br> Wymagane jest bezpośrednie połączenie z komputera agenta System Center Operations Manager, aby Azure Monitor. |
| Konto magazynu Azure | Nie | Rozwiązanie Dane o komunikacji sieciowej gromadzi dane z komputerów agenta, więc nie ma od niego żadnych danych do gromadzenia z usługi Azure Storage. |

W systemie Windows Microsoft Monitoring Agent (MMA) jest używany przez oba System Center Operations Manager i Azure Monitor do zbierania i wysyłania danych. W zależności od kontekstu Agent jest nazywany agentem System Center Operations Manager, agentem Log Analytics, MMA lub agentem bezpośrednim. System Center Operations Manager i Azure Monitor zapewniają nieco różne wersje MMA. Te wersje mogą posłużyć do System Center Operations Manager, Azure Monitor lub do obu tych raportów.

W systemie Linux Agent Log Analytics dla systemu Linux zbiera dane i wysyła je do Azure Monitor. Danych z sieci można używać na serwerach z agentami podłączonymi bezpośrednio do Azure Monitor lub na serwerach łączących się z Azure Monitor za pośrednictwem System Center Operations Manager grup zarządzania.

Agent zależności nie przesyła żadnych danych i nie wymaga żadnych zmian w zaporach ani portach. Dane w danych telekomunikacyjnych są zawsze przesyłane przez agenta Log Analytics do Azure Monitor, bezpośrednio lub przez bramę Log Analytics.

![diagram agenta](./media/wire-data/agents.png)

Jeśli jesteś użytkownikiem System Center Operations Manager z grupą zarządzania połączoną z Azure Monitor:

- Gdy agenci System Center Operations Manager mogą uzyskać dostęp do Internetu w celu nawiązania połączenia z Azure Monitor, nie jest wymagana żadna dodatkowa konfiguracja.
- Należy skonfigurować bramę Log Analytics do pracy z System Center Operations Manager, gdy System Center Operations Manager agenci nie będą mogli uzyskać dostępu Azure Monitor przez Internet.

Jeśli komputery z systemem Windows lub Linux nie mogą łączyć się bezpośrednio z usługą, należy skonfigurować agenta Log Analytics, aby łączył się z Azure Monitor przy użyciu bramy Log Analytics. Bramę usługi Log Analytics można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Wymagania wstępne

- Wymaga oferty rozwiązania [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Jeśli używasz poprzedniej wersji rozwiązania danych o komunikacji sieciowej, musisz ją najpierw usunąć. Jednak wszystkie dane przechwycone poprzez oryginalne rozwiązanie Dane o komunikacji sieciowej są nadal dostępne za pośrednictwem rozwiązania Dane o komunikacji sieciowej 2.0 i przeszukiwania dzienników.
- Do zainstalowania lub odinstalowania agenta zależności wymagane są uprawnienia administratora.
- Agent zależności musi być zainstalowany na komputerze z 64-bitowym systemem operacyjnym.

### <a name="operating-systems"></a>Systemy operacyjne

W poniższych sekcjach wymieniono obsługiwane systemy operacyjne dla agenta zależności. Rozwiązanie Dane o komunikacji sieciowej nie obsługuje 32-bitowych architektur dla żadnego systemu operacyjnego.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- System Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 z dodatkiem R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Pulpit systemu Windows

- System Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux
W poniższych sekcjach przedstawiono obsługiwane systemy operacyjne dla agenta zależności w systemie Linux.  

- Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
- Niestandardowe wydania jądra, takie jak PAE i Xen, nie są obsługiwane dla żadnej dystrybucji systemu Linux. Na przykład system z ciągiem wydania "2.6.16.21-0,8-Xen" nie jest obsługiwany.
- Niestandardowe jądra, łącznie z ponownymi kompilacjami standardowych jąder, nie są obsługiwane.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7,4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6,9 | 2.6.32-696 |
| 6,10 | 2.6.32 — 754 |

##### <a name="centosplus"></a>CentOSPlus
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6,9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6,10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | jądro 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | jądro 4,15. * |
| 16,04 | 4,4.\*<br>4,8.\*<br>4,10.\*<br>4,11.\*<br>4,13.\* |
| 14,04 | 3,13.\*<br>4,4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
| 12 Z DODATKIEM SP2 | 4,4. * |
| 12 SP3 | 4,4. * |

### <a name="dependency-agent-downloads"></a>Pobieranie agenta zależności

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby skonfigurować rozwiązanie Dane o komunikacji sieciowej dla Twoich obszarów roboczych.

1. Włącz rozwiązanie Activity Log Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań monitorowania z Galeria rozwiązań](../insights/solutions.md).
2. Zainstaluj agenta zależności na każdym komputerze, na którym chcesz pobrać dane. Agent zależności może monitorować połączenia z bezpośrednimi sąsiadami, dzięki czemu na każdym komputerze może nie być potrzebny Agent.

> [!NOTE]
> Do nowych obszarów roboczych nie można dodać poprzedniej wersji rozwiązania Dane o komunikacji sieciowej. Jeśli masz włączone oryginalne rozwiązanie Dane o komunikacji sieciowej, możesz nadal z niego korzystać. Jednak aby używać rozwiązania Dane o komunikacji sieciowej 2.0, należy najpierw usunąć oryginalną wersję.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Zainstaluj agenta zależności w systemie Windows

Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia administratora.

Agent zależności jest instalowany na komputerach z systemem Windows za pomocą InstallDependencyAgent-Windows.exe. Uruchomienie tego pliku wykonywalnego bez żadnych opcji powoduje uruchomienie kreatora, którego polecenia należy wykonywać w celu przeprowadzenia interaktywnej instalacji.

Wykonaj następujące kroki, aby zainstalować agenta zależności na każdym komputerze z systemem Windows:

1. Zainstaluj agenta Log Analytics, wykonując czynności opisane w temacie [zbieranie danych z komputerów z systemem Windows hostowanych w danym środowisku](../agents/agent-windows.md).
2. Pobierz agenta zależności systemu Windows przy użyciu linku w poprzedniej sekcji, a następnie uruchom go za pomocą następującego polecenia: `InstallDependencyAgent-Windows.exe`
3. Użyj kreatora, aby zainstalować agenta.
4. Jeśli uruchomienie agenta zależności nie powiedzie się, Sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Windows katalog dziennika to %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Wiersz polecenia systemu Windows

Użyj opcji z poniższej tabeli, aby przeprowadzić instalację z poziomu wiersza polecenia. Aby wyświetlić listę flag instalacji, uruchom instalatora, używając flagi /? w następujący sposób.

InstallDependencyAgent-Windows.exe /?

| **Flaga** | **Opis** |
| --- | --- |
| <code>/?</code> | Pobierz listę opcji wiersza polecenia. |
| <code>/S</code> | Przeprowadź instalację cichą bez monitowania użytkownika. |

Pliki dla agenta zależności systemu Windows są domyślnie umieszczane w folderze C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux

Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.

Agent zależności jest instalowany na komputerach z systemem Linux za pomocą InstallDependencyAgent-Linux64. bin, skryptu powłoki z samowyodrębniającym się plikiem binarnym. Plik ten można uruchomić przy użyciu polecenia _sh_ lub dodać uprawnienia do wykonywania do samego pliku.

Wykonaj następujące kroki, aby zainstalować agenta zależności na każdym komputerze z systemem Linux:

1. Zainstaluj agenta Log Analytics, wykonując czynności opisane w temacie [zbieranie danych z komputerów z systemem Linux hostowanych w danym środowisku](../vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Pobierz agenta zależności systemu Linux przy użyciu linku w poprzedniej sekcji, a następnie zainstaluj go jako element główny przy użyciu następującego polecenia: sh InstallDependencyAgent-Linux64. bin
3. Jeśli uruchomienie agenta zależności nie powiedzie się, Sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Dla agentów systemu Linux katalogiem dziennika jest: /var/opt/microsoft/dependency-agent/log.

Aby wyświetlić listę flag instalacji, uruchom program instalacyjny z flagą `-help` w następujący sposób.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Flaga** | **Opis** |
| --- | --- |
| <code>-help</code> | Pobierz listę opcji wiersza polecenia. |
| <code>-s</code> | Przeprowadź instalację cichą bez monitowania użytkownika. |
| <code>--check</code> | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Pliki agenta zależności są umieszczane w następujących katalogach:

| **Pliki** | **Lokalizacja** |
| --- | --- |
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Aby łatwo wdrożyć agenta zależności na wielu serwerach jednocześnie, można użyć skryptu. Poniższe przykłady skryptów umożliwiają pobranie i zainstalowanie agenta zależności w systemie Windows lub Linux.

#### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Skrypt powłoki dla systemu Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Aby wdrożyć agenta zależności za pomocą konfiguracji żądanego stanu, można użyć modułu xPSDesiredStateConfiguration i bitu kodu, takiego jak następujące:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Odinstalowywanie agenta zależności

Skorzystaj z poniższych sekcji, aby usunąć agenta zależności.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalowywanie agenta zależności w systemie Windows

Administrator może odinstalować agenta zależności dla systemu Windows za pomocą panelu sterowania.

Administrator może również uruchomić Agent\Uninstall.exe zależności w celu odinstalowania agenta zależności.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalowywanie agenta zależności w systemie Linux

Aby całkowicie odinstalować agenta zależności z systemu Linux, należy usunąć samego agenta i łącznik, który jest instalowany automatycznie z agentem. Oba te elementy można usunąć przy użyciu następującego pojedynczego polecenia:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pakiety administracyjne

Gdy rozwiązanie Dane o komunikacji sieciowej zostanie uaktywnione w obszarze roboczym usługi Log Analytics, pakiet administracyjny o wielkości 300 KB jest wysyłany do wszystkich serwerów z systemem Windows w tym obszarze roboczym. Jeśli używasz agentów programu System Center Operations Manager w [połączonej grupie zarządzania](../agents/om-agents.md), pakiet administracyjny monitora zależności zostanie wdrożony z programu System Center Operations Manager. Jeśli agenci są połączeni bezpośrednio, Azure Monitor dostarcza pakiet administracyjny.

Pakiet administracyjny nosi nazwę Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Jest on zapisywany w folderze: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Źródło danych, którego używa pakiet administracyjny, to: % Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Użycie rozwiązania

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

- Rozwiązanie Dane o komunikacji sieciowej pozyskuje dane z komputerów z systemem Windows Server 2012 R2, Windows 8.1 i z nowszymi systemami operacyjnymi.
- Na komputerach, z których chcesz pozyskiwać dane o komunikacji sieciowej, wymagana jest platforma Microsoft .NET Framework 4.0 lub nowsza.
- Dodaj rozwiązanie danych sieci do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań monitorowania z Galeria rozwiązań](../insights/solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.
- Jeśli chcesz wyświetlić dane o komunikacji sieciowej dla konkretnego rozwiązania, to rozwiązanie musi być już dodane do Twojego obszaru roboczego.

Po zainstalowaniu agentów i rozwiązania w Twoim obszarze roboczym pojawi się kafelek Dane o komunikacji sieciowej 2.0.

![Kafelek Dane o komunikacji sieciowej](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Używanie rozwiązania Dane o komunikacji sieciowej 2.0

Na stronie **Omówienie** Twojego obszaru roboczego usługi Log Analytics w witrynie Azure Portal kliknij kafelek **Dane o komunikacji sieciowej 2.0**, aby otworzyć pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej. Na pulpicie nawigacyjnym znajdują się bloki wymienione w poniższej tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Możesz uruchomić przeszukiwanie dzienników, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole bloku lub kliknij nagłówek bloku.

| **Blok** | **Opis** |
| --- | --- |
| Agenci przechwytujący ruch sieciowy | Pokazuje liczbę agentów, którzy przechwytują ruch sieciowy, i listę 10 najważniejszych komputerów przechwytujących ruch. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kliknij komputer na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę przechwyconych bajtów. |
| Lokalne podsieci | Pokazuje liczbę lokalnych podsieci, które zostały odnalezione przez agentów.  Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, które wyświetli listę wszystkich podsieci wraz z liczbą bajtów przesłanych w ramach każdej z nich. Kliknij podsieć na liście, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów przesłanych w tej podsieci. |
| Protokoły poziomu aplikacji | Pokazuje liczbę używanych protokołów poziomu aplikacji wykrytych przez agentów. Kliknij liczbę, aby uruchomić przeszukiwanie dzienników dla <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kliknij protokół, aby uruchomić przeszukiwanie dzienników zwracające całkowitą liczbę bajtów wysłanych przy użyciu tego protokołu. |

![Pulpit nawigacyjny rozwiązania Dane o komunikacji sieciowej](./media/wire-data/wire-data-dash.png)

Możesz użyć bloku **Agenci przechwytujący ruch sieciowy**, aby ustalić, jaka część przepustowości sieci jest zużywana przez komputery. Ten blok może pomóc łatwo odnaleźć _najbardziej rozgadany_ komputer w Twoim środowisku. Takie komputery mogą być przeciążone, działać inaczej niż zwykle lub zużywać więcej zasobów sieciowych niż zwykle.

![Zrzut ekranu przedstawiający blok ruch sieciowy z agentów przechwytywania na pulpicie nawigacyjnym Wire Data 2.0, w którym jest wyświetlana przepustowość sieci używana przez poszczególne komputery.](./media/wire-data/log-search-example01.png)

Analogicznie możesz użyć bloku **Podsieci lokalne**, aby określić, ile ruchu sieciowego przechodzi przez Twoje podsieci. Użytkownicy często definiują podsieci wokół najważniejszych obszarów dla swoich aplikacji. Ten blok oferuje wgląd w te obszary.

![Zrzut ekranu bloku podsieci lokalnych na pulpicie nawigacyjnym Wire Data 2.0 przedstawiający przepustowość sieci wykorzystywaną przez każdy LocalSubnet.](./media/wire-data/log-search-example02.png)

Blok **Protokoły poziomu aplikacji** przydaje się, ponieważ pomaga dowiedzieć się, jakie protokoły są używane. Na przykład możesz spodziewać się, że protokół SSH nie będzie używany w Twoim środowisku sieciowym. Wyświetlanie informacji dostępnych w bloku może szybko potwierdzić lub obalić Twoje oczekiwania.

![Zrzut ekranu przedstawiający blok protokołów poziomu aplikacji na pulpicie nawigacyjnym Wire Data 2.0, w którym jest wyświetlana przepustowość sieci używana przez poszczególne protokoły.](./media/wire-data/log-search-example03.png)

Warto również wiedzieć, czy ruch w ramach protokołu zwiększa się, czy też zmniejsza wraz z upływem czasu. Jeśli na przykład wzrasta ilość danych przesyłanych przez aplikację, być może warto zwrócić uwagę.

## <a name="input-data"></a>Dane wejściowe

Dane o komunikacji sieciowej gromadzą metadane dotyczące ruchu sieciowego przy użyciu agentów, którzy zostali włączeni. Każdy agent wysyła dane co około 15 s.

## <a name="output-data"></a>Dane wyjściowe

Rekord o typie _WireData_ jest tworzony dla każdego typu danych wejściowych. Rekordy rozwiązania Dane o komunikacji sieciowej mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|---|---|
| Computer (Komputer) | Nazwa komputera, gdzie są gromadzone dane |
| TimeGenerated | Czas rekordu |
| LocalIP | Adres IP komputera lokalnego |
| SessionState | Połączone lub rozłączone |
| ReceivedBytes | Liczba odebranych bajtów |
| ProtocolName | Nazwa używanego protokołu sieciowego |
| IPVersion | Wersja protokołu IP |
| Kierunek | Ruch przychodzący lub wychodzący |
| MaliciousIP | Adres IP znanego złośliwego źródła |
| Ważność | Ważność podejrzanego złośliwego oprogramowania |
| RemoteIPCountry | Kraj/region zdalnego adresu IP |
| ManagementGroupName | Nazwa grupy zarządzania programu Operations Manager |
| SourceSystem | Źródło, gdzie zostały zebrane dane |
| SessionStartTime | Godzina rozpoczęcia sesji |
| SessionEndTime | Godzina zakończenia sesji |
| LocalSubnet | Podsieć, gdzie zostały zebrane dane |
| LocalPortNumber | Numer portu lokalnego |
| RemoteIP | Zdalny adres IP używany przez komputer zdalny |
| RemotePortNumber | Numer portu używany przez zdalny adres IP |
| SessionID | Unikatowa wartość, która identyfikuje sesję komunikacji między dwoma adresami IP |
| SentBytes | Liczba wysłanych bajtów |
| TotalBytes | Całkowita liczba bajtów wysłanych podczas sesji |
| ApplicationProtocol | Typ użytego protokołu sieciowego   |
| ProcessId | Identyfikator procesu systemu Windows |
| ProcessName | Ścieżka i nazwa pliku procesu |
| RemoteIPLongitude | Wartość długości geograficznej IP |
| RemoteIPLatitude | Wartość szerokości geograficznej IP |

## <a name="next-steps"></a>Następne kroki

- [Wyszukaj dzienniki](../logs/log-query-overview.md), aby wyświetlić szczegółowe rekordy wyszukiwania rozwiązania Dane o komunikacji sieciowej.

