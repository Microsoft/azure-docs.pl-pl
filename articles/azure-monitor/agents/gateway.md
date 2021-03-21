---
title: Łączenie komputerów przy użyciu bramy Log Analytics | Microsoft Docs
description: Podłącz urządzenia i Operations Manager monitorowane komputery przy użyciu bramy Log Analytics do wysyłania danych do usługi Azure Automation i Log Analytics, jeśli nie mają dostępu do Internetu.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: bae48dc78eb6973e5bce4d535091bc330c4c897f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509034"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Łączenie komputerów bez dostępu do Internetu przy użyciu bramy Log Analytics w programie Azure Monitor

>[!NOTE]
>Zgodnie z Microsoft Operations Management Suite (OMS) przejściami do monitora Microsoft Azure, terminologia zmienia się. Ten artykuł odnosi się do bramy pakietu OMS jako bramy usługi Azure Log Analytics. 
>

W tym artykule opisano sposób konfigurowania komunikacji z Azure Automation i Azure Monitor przy użyciu bramy Log Analytics, gdy komputery połączone bezpośrednio lub monitorowane przez Operations Manager nie mają dostępu do Internetu. 

Brama Log Analytics jest serwerem proxy przesyłania dalej HTTP, który obsługuje Tunelowanie HTTP przy użyciu polecenia HTTP CONNECT. Ta brama wysyła dane do Azure Automation i Log Analytics obszaru roboczego w Azure Monitor w imieniu komputerów, które nie mogą bezpośrednio łączyć się z Internetem. 

Brama Log Analytics obsługuje:

* Raportowanie do tych samych Log Analytics obszarów roboczych skonfigurowanych dla każdego agenta w tle i skonfigurowanych przy użyciu Azure Automation hybrydowych procesów roboczych elementów Runbook.  
* Komputery z systemem Windows, na których Microsoft Monitoring Agent jest podłączony bezpośrednio do Log Analytics obszaru roboczego w Azure Monitor.
* Komputery z systemem Linux, na których Agent Log Analytics dla systemu Linux jest podłączony bezpośrednio do Log Analytics obszaru roboczego w Azure Monitor.  
* System Center Operations Manager 2012 z dodatkiem SP1 z pakietem UR7, Operations Manager 2012 R2 z UR3 lub grupą zarządzania w Operations Manager 2016 lub nowszym, która jest zintegrowana z Log Analytics.  

Niektóre zasady zabezpieczeń IT nie zezwalają na połączenie internetowe komputerów sieciowych. Te niepołączone komputery mogą wskazywać na przykład urządzenia do sprzedaży (POS) lub serwery obsługujące usługi IT. Aby podłączyć te urządzenia do Azure Automation lub Log Analytics obszaru roboczego, tak aby można było nimi zarządzać i monitorować, należy skonfigurować je do bezpośredniego komunikowania się z bramą Log Analytics. Brama Log Analytics może odbierać informacje o konfiguracji i przesyłać dalej dane w ich imieniu. Jeśli komputery są skonfigurowane z agentem Log Analytics, aby połączyć się bezpośrednio z obszarem roboczym Log Analytics, komputery będą komunikować się z bramą Log Analytics.  

Brama usługi Log Analytics transferuje dane bezpośrednio z agentów do usługi. Nie analizuje on żadnych przesyłanych danych, a Brama nie buforuje danych w przypadku utraty łączności z usługą. Gdy brama nie może komunikować się z usługą, Agent kontynuuje uruchamianie i kolejkuje zebrane dane na dysku monitorowanego komputera. Po przywróceniu połączenia agent wysyła dane przechowywane w pamięci podręcznej zebrane do Azure Monitor.

Po zintegrowaniu Operations Manager grupy zarządzania z Log Analytics, serwery zarządzania można skonfigurować do łączenia się z bramą Log Analytics w celu odbierania informacji o konfiguracji i wysyłania zebranych danych w zależności od włączonego rozwiązania.  Operations Manager agenci wysyłają dane do serwera zarządzania. Na przykład agenci mogą wysyłać alerty Operations Manager, dane oceny konfiguracji, dane przestrzeni wystąpienia oraz dane pojemności. Inne dane o dużej ilości danych, takie jak dzienniki Internet Information Services (IIS), dane wydajności i zdarzenia zabezpieczeń, są wysyłane bezpośrednio do bramy Log Analytics. 

Jeśli co najmniej jeden serwer bramy Operations Manager jest wdrożony w celu monitorowania niezaufanych systemów w sieci obwodowej lub izolowanej sieci, te serwery nie mogą komunikować się z bramą Log Analytics.  Serwery bramy Operations Manager mogą zgłosić tylko serwer zarządzania programu.  W przypadku skonfigurowania Operations Manager grupy zarządzania do komunikowania się z bramą Log Analytics informacje o konfiguracji serwera proxy są automatycznie dystrybuowane do każdego komputera zarządzanego przez agenta, który jest skonfigurowany do zbierania danych dziennika dla Azure Monitor, nawet jeśli ustawienie jest puste.

Aby zapewnić wysoką dostępność dla bezpośrednio połączonych lub grup zarządzania operacjami, które komunikują się z obszarem roboczym Log Analytics za pośrednictwem bramy, użyj funkcji równoważenia obciążenia sieciowego (NLB) do przekierowywania i dystrybucji ruchu między wieloma serwerami bramy. W ten sposób, jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innego dostępnego węzła.  

Komputer z uruchomioną bramą Log Analytics wymaga Log Analytics Windows Agent do zidentyfikowania punktów końcowych usługi, z którymi musi komunikować się brama. Agent musi również skierować bramę do raportowania do tych samych obszarów roboczych, do których są skonfigurowane agenci lub Operations Manager grupy zarządzania za bramą. Ta konfiguracja umożliwia bramie i agentowi komunikowanie się z przypisanym obszarem roboczym.

Brama może być wieloadresowa do czterech obszarów roboczych. Jest to całkowita liczba obszarów roboczych obsługiwanych przez agenta systemu Windows.  

Każdy agent musi mieć łączność sieciową z bramą, aby agenci mogli automatycznie przesyłać dane do i z bramy. Należy unikać instalowania bramy na kontrolerze domeny. W przypadku komputerów z systemem Linux, które znajdują się za serwerem bramy, nie można użyć metody [instalacji skryptu otoki](../agents/agent-linux.md#install-the-agent-using-wrapper-script) w celu zainstalowania agenta log Analytics dla systemu Linux. Agent musi być pobierany ręcznie, kopiowany na komputer i instalowany ręcznie, ponieważ Brama obsługuje tylko komunikację z wymienionymi wcześniej usługami platformy Azure.

Na poniższym diagramie przedstawiono dane przepływające od agentów bezpośrednich za pośrednictwem bramy do Azure Automation i Log Analytics. Konfiguracja serwera proxy agenta musi być zgodna z portem, z którym jest skonfigurowana Brama Log Analytics.  

![Diagram bezpośredniej komunikacji agenta z usługami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z Operations Manager grupy zarządzania do Log Analytics.   

![Diagram komunikacji Operations Manager z Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurowanie systemu

Komputery wyznaczeni do uruchomienia bramy Log Analytics muszą mieć następującą konfigurację:

* Windows 10, Windows 8.1 lub Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 lub Windows Server 2008
* Microsoft .NET Framework 4.5
* Co najmniej 4-rdzeniowy procesor i 8 GB pamięci 
* [Agent log Analytics dla systemu Windows](../agents/agent-windows.md) , który jest skonfigurowany do raportowania w tym samym obszarze roboczym, co agenci, którzy komunikują się za pomocą bramy

### <a name="language-availability"></a>Dostępność języka

Brama Log Analytics jest dostępna w następujących językach:

- Chiński (uproszczony)
- Chiński (tradycyjny)
- Czeski
- Niderlandzki
- Angielski
- Francuski
- Niemiecki
- Węgierski
- Włoski
- japoński
- koreański
- polski
- portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański (międzynarodowy)

### <a name="supported-encryption-protocols"></a>Obsługiwane protokoły szyfrowania

Brama Log Analytics obsługuje tylko Transport Layer Security (TLS) 1,0, 1,1 i 1,2.  Nie obsługuje Secure Sockets Layer (SSL).  Aby zapewnić bezpieczeństwo danych podczas przesyłania do Log Analytics, skonfiguruj bramę do użycia co najmniej protokołu TLS 1,2. Starsze wersje protokołu TLS lub SSL są narażone na ataki. Chociaż obecnie dopuszczają zgodność z poprzednimi wersjami, unikaj ich używania.  

Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Obsługiwana liczba połączeń agentów

W poniższej tabeli przedstawiono około liczby agentów, które mogą komunikować się z serwerem bramy. Pomoc techniczna jest oparta na agentach, które przesyłają około 200 KB danych co 6 sekund. Dla każdego testowanego agenta ilość danych jest około 2,7 GB dziennie.

|Brama |Obsługiwane agenci (przybliżony)|  
|--------|----------------------------------|  
|Procesor: procesor Intel Xeon E5-2660 v3 \@ 2,6 GHz 2 rdzeni<br> Pamięć: 4 GB<br> Przepustowość sieci: 1 GB/s| 600|  
|Procesor: procesor Intel Xeon E5-2660 v3 \@ 2,6 GHz 4 rdzeni<br> Pamięć: 8 GB<br> Przepustowość sieci: 1 GB/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Pobierz bramę Log Analytics

Pobierz najnowszą wersję pliku instalacyjnego bramy Log Analytics z centrum pobierania Microsoft ([link do pobierania](https://go.microsoft.com/fwlink/?linkid=837444)) lub Azure Portal.

Aby uzyskać bramę Log Analytics z Azure Portal, wykonaj następujące kroki:

1. Przejrzyj listę usług, a następnie wybierz pozycję **log Analytics**. 
1. Wybierz obszar roboczy.
1. W bloku obszaru roboczego w obszarze **Ogólne** wybierz pozycję **Szybki Start**. 
1. W obszarze **Wybierz źródło danych, aby połączyć się z obszarem roboczym** wybierz pozycję **komputery**.
1. W bloku **agenta bezpośredniego** wybierz pozycję **Pobierz log Analytics bramę**.
 
   ![Zrzut ekranu przedstawiający kroki pobierania bramy Log Analytics](./media/gateway/download-gateway.png)

lub 

1. W bloku obszaru roboczego w obszarze **Ustawienia** wybierz pozycję **Ustawienia zaawansowane**.
1. Przejdź do pozycji **połączone źródła**  >  **serwery z systemem Windows** i wybierz pozycję **Pobierz log Analytics bramę**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Zainstaluj bramę Log Analytics przy użyciu Kreatora instalacji

Aby zainstalować bramę przy użyciu Kreatora instalacji, wykonaj następujące kroki. 

1. W folderze docelowym kliknij dwukrotnie pozycję **Log Analytics gateway.msi**.
1. Na **stronie powitalnej** wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający stronę powitalną w Kreatorze instalacji bramy](./media/gateway/gateway-wizard01.png)

1. Na stronie **Umowa licencyjna** zaznacz opcję **Akceptuję warunki umowy licencyjnej** , aby wyrazić zgodę na postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz przycisk **dalej**.
1. Na stronie **port i adres serwera proxy** :

   a. Wprowadź numer portu TCP, który ma być używany przez bramę. Instalator używa tego numeru portu w celu skonfigurowania reguły ruchu przychodzącego w zaporze systemu Windows.  Wartość domyślna to 8080.
      Prawidłowy zakres numeru portu to od 1 do 65535. Jeśli dane wejściowe nie należą do tego zakresu, zostanie wyświetlony komunikat o błędzie.

   b. Jeśli serwer, na którym zainstalowano bramę, musi komunikować się za pomocą serwera proxy, wprowadź adres serwera proxy, pod którym Brama musi nawiązać połączenie. Na przykład wprowadź `http://myorgname.corp.contoso.com:80`.  Jeśli to pole pozostanie puste, Brama będzie podejmować próby nawiązania bezpośredniego połączenia z Internetem.  Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.

   c. Wybierz pozycję **Next** (Dalej).

   ![Zrzut ekranu przedstawiający konfigurację serwera proxy bramy](./media/gateway/gateway-wizard02.png)

1. Jeśli nie masz włączonej Microsoft Update, zostanie wyświetlona strona Microsoft Update i będzie można ją włączyć. Wybierz pozycję, a następnie kliknij przycisk **dalej**. W przeciwnym razie przejdź do następnego kroku.
1. Na stronie **folder docelowy** pozostaw folder domyślny C:\Program Files\OMS Gateway lub wprowadź lokalizację, w której chcesz zainstalować bramę. Następnie wybierz przycisk **Dalej**.
1. Na stronie **gotowy do instalacji** wybierz pozycję **Zainstaluj**. Jeśli Kontrola konta użytkownika żąda uprawnień do instalacji, wybierz pozycję **tak**.
1. Po zakończeniu instalacji wybierz pozycję **Zakończ**. Aby sprawdzić, czy usługa jest uruchomiona, Otwórz przystawkę Services. msc i sprawdź, czy **brama pakietu OMS** jest wyświetlana na liście usług i czy jej stan jest **uruchomiony**.

   ![Zrzut ekranu usług lokalnych pokazujący, że brama pakietu OMS jest uruchomiona](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instalowanie bramy Log Analytics przy użyciu wiersza polecenia

Pobrany plik dla bramy to pakiet Instalator Windows obsługujący instalację dyskretną z poziomu wiersza polecenia lub innej metody zautomatyzowanej. Jeśli nie znasz standardowych opcji wiersza polecenia dla Instalator Windows, zobacz [Opcje wiersza polecenia](/windows/desktop/msi/command-line-options).
 
W poniższej tabeli przedstawiono parametry obsługiwane przez Instalatora.

|Parametry| Uwagi|
|----------|------| 
|NUMER_PORTU | Numer portu TCP dla bramy do nasłuchiwania |
|SERWERA proxy | Adres IP serwera proxy |
|INSTALLDIR | W pełni kwalifikowana ścieżka do określenia katalogu instalacyjnego plików oprogramowania bramy |
|USERNAME | Identyfikator użytkownika do uwierzytelnienia na serwerze proxy |
|HASŁO | Hasło identyfikatora użytkownika do uwierzytelniania przy użyciu serwera proxy |
|LicenseAccepted | Określ wartość **1** , aby potwierdzić zaakceptowanie umowy licencyjnej |
|HASAUTH | Określ wartość **1** , jeśli określono parametry nazwy użytkownika/hasła |
|HASPROXY | Określ wartość **1** podczas określania adresu IP dla parametru **proxy** |

Aby zainstalować bramę w trybie dyskretnym i skonfigurować ją z określonym adresem serwera proxy, numer portu, wpisz następujące polecenie:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Użycie opcji wiersza polecenia/Qn powoduje ukrycie Instalatora,/QB pokazuje Instalatora podczas instalacji dyskretnej.  

Jeśli musisz podać poświadczenia w celu uwierzytelnienia z serwerem proxy, wpisz następujące polecenie:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Po zakończeniu instalacji można potwierdzić, że ustawienia są akceptowane (z wyłączeniem nazwy użytkownika i hasła) przy użyciu następujących poleceń cmdlet programu PowerShell:

- **Get-OMSGatewayConfig** — zwraca port TCP, na którym Brama jest skonfigurowana do nasłuchiwania.
- **Get-OMSGatewayRelayProxy** — zwraca adres IP serwera proxy, który został skonfigurowany do komunikacji z usługą.

## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego

Bramę można skonfigurować pod kątem wysokiej dostępności przy użyciu funkcji równoważenia obciążenia sieciowego (NLB), korzystając z usługi równoważenia obciążenia sieciowego firmy Microsoft [(NLB)](/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)lub sprzętowych modułów równoważenia obciążenia. Moduł równoważenia obciążenia zarządza ruchem przez przekierowanie żądanych połączeń z agentów Log Analytics lub serwerów zarządzania Operations Manager między swoimi węzłami. Jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innych węzłów.

### <a name="microsoft-network-load-balancing"></a>Równoważenie obciążenia sieciowego firmy Microsoft

Aby dowiedzieć się, jak projektować i wdrażać klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [równoważenie obciążenia sieciowego](/windows-server/networking/technologies/network-load-balancing). Poniższe kroki opisują sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1. Zaloguj się na serwerze z systemem Windows, który jest członkiem klastra równoważenia obciążenia sieciowego przy użyciu konta administracyjnego.  
2. Otwórz Menedżera równoważenia obciążenia sieciowego w Menedżer serwera, kliknij przycisk **Narzędzia**, a następnie kliknij pozycję **Menedżer równoważenia obciążenia sieciowego**.
3. Aby połączyć serwer bramy Log Analytics z zainstalowanym Microsoft Monitoring Agent, kliknij prawym przyciskiem myszy adres IP klastra, a następnie kliknij polecenie **Dodaj hosta do klastra**. 

    ![Menedżer równoważenia obciążenia sieciowego — Dodawanie hosta do klastra](./media/gateway/nlb02.png)
 
4. Wprowadź adres IP serwera bramy, z którym chcesz nawiązać połączenie. 

    ![Menedżer równoważenia obciążenia sieciowego — Dodawanie hosta do klastra: łączenie](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Aby dowiedzieć się, jak projektować i wdrażać Azure Load Balancer, zobacz [co to jest Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Aby wdrożyć podstawowy moduł równoważenia obciążenia, wykonaj czynności opisane w tym [przewodniku szybki start](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) , z wyjątkiem kroków opisanych w sekcji **Tworzenie serwerów zaplecza**.   

> [!NOTE]
> Skonfigurowanie Azure Load Balancer przy użyciu **podstawowej jednostki SKU** wymaga, aby maszyny wirtualne platformy Azure należały do zestawu dostępności. Aby dowiedzieć się więcej na temat zestawów dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/availability.md). Aby dodać istniejące maszyny wirtualne do zestawu dostępności, zapoznaj się z poleceniem [set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po utworzeniu modułu równoważenia obciążenia należy utworzyć pulę zaplecza, która dystrybuuje ruch do jednego lub większej liczby serwerów bramy. Wykonaj kroki opisane w sekcji artykuł szybkiego startu [Tworzenie zasobów dla modułu równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Podczas konfigurowania sondy kondycji należy ją skonfigurować tak, aby korzystała z portu TCP serwera bramy. Sonda kondycji dynamicznie dodaje lub usuwa serwery bramy z rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na testy kondycji. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurowanie agenta Log Analytics i grupy zarządzania Operations Manager

W tej sekcji zobaczysz, jak skonfigurować bezpośrednio połączone Log Analytics agentów, Operations Manager grupę zarządzania lub Azure Automation hybrydowych procesów roboczych elementów Runbook z bramą Log Analytics do komunikowania się z Azure Automation lub Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurowanie autonomicznego agenta Log Analytics

Podczas konfigurowania agenta Log Analytics należy zamienić wartość serwera proxy na adres IP serwera bramy Log Analytics i numer portu. Jeśli wdrożono wiele serwerów bramy za modułem równoważenia obciążenia, konfiguracja serwera proxy agenta Log Analytics jest wirtualnym adresem IP modułu równoważenia obciążenia.  

>[!NOTE]
>Aby zainstalować agenta Log Analytics na komputerach bramy i systemu Windows, które bezpośrednio łączą się z Log Analytics, zobacz [łączenie komputerów z systemem Windows z usługą log Analytics na platformie Azure](../agents/agent-windows.md). Aby połączyć komputery z systemem Linux, zobacz [łączenie komputerów z systemem Linux w Azure monitor](../agents/agent-linux.md). 
>

Po zainstalowaniu agenta na serwerze bramy skonfiguruj go w celu raportowania do obszaru roboczego lub agentów obszaru roboczego, które komunikują się z bramą. Jeśli Log Analytics Agent systemu Windows nie jest zainstalowany w bramie, zdarzenie 300 jest zapisywane w dzienniku zdarzeń bramy pakietu OMS, wskazując, że należy zainstalować agenta. Jeśli Agent jest zainstalowany, ale nie został skonfigurowany do raportowania w tym samym obszarze roboczym co agenci, którzy komunikują się z nim, zdarzenie 105 jest zapisywane w tym samym dzienniku, co wskazuje, że Agent na bramie musi być skonfigurowany do raportowania do tego samego obszaru roboczego co agenci, którzy komunikują się z bramą.

Po zakończeniu konfiguracji należy ponownie uruchomić usługę **bramy OMS** , aby zastosować zmiany. W przeciwnym razie Brama odrzuci agentów próbujących komunikować się z usługą Log Analytics i zgłosi zdarzenie 105 w dzienniku zdarzeń bramy pakietu OMS. Taka sytuacja występuje również po dodaniu lub usunięciu obszaru roboczego z konfiguracji agenta na serwerze bramy.

Informacje dotyczące hybrydowego procesu roboczego elementu Runbook usługi Automation można znaleźć [w temacie Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Skonfiguruj Operations Manager, gdzie wszyscy agenci używają tego samego serwera proxy

Operations Manager konfiguracja serwera proxy jest automatycznie stosowana do wszystkich agentów, które zgłaszają Operations Manager, nawet jeśli ustawienie jest puste.  

Aby móc obsługiwać Operations Manager przy użyciu bramy pakietu OMS, musisz dysponować:

* Microsoft Monitoring Agent (wersja 8.0.10900.0 lub nowsza) zainstalowana na serwerze bramy OMS i skonfigurowana za pomocą tych samych Log Analytics obszarów roboczych, do których jest skonfigurowana Grupa zarządzania.
* Łączność z Internetem. Alternatywnie Brama pakietu OMS musi być podłączona do serwera proxy, który jest połączony z Internetem.

> [!NOTE]
> Jeśli nie określisz żadnej wartości dla bramy, puste wartości są przekazywane do wszystkich agentów.
>

Jeśli Operations Manager grupy zarządzania jest rejestrowany przy pierwszej Log Analytics obszarze roboczym, nie zobaczysz opcji, aby określić konfigurację serwera proxy dla grupy zarządzania w konsoli operacje. Ta opcja jest dostępna tylko wtedy, gdy grupa zarządzania została zarejestrowana w usłudze.  

Aby skonfigurować integrację, zaktualizuj konfigurację serwera proxy systemu przy użyciu narzędzia Netsh w systemie, w którym uruchomiono konsolę operacje, oraz na wszystkich serwerach zarządzania w grupie zarządzania. Wykonaj następujące kroki:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień:

   a. Wybierz pozycję **Start** i wpisz **cmd**.  

   b. Kliknij prawym przyciskiem myszy **wiersz polecenia** i wybierz polecenie **Uruchom jako administrator**.  

1. Wprowadź następujące polecenie:

   `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu integracji z Log Analytics Usuń zmiany, uruchamiając polecenie `netsh winhttp reset proxy` . Następnie w konsoli operacje Użyj opcji **Konfiguruj serwer proxy** , aby określić serwer bramy log Analytics. 

1. W konsoli Operations Manager w obszarze **Pakiet Operations Management Suite** wybierz pozycję **połączenie**, a następnie wybierz pozycję **Konfiguruj serwer proxy**.

   ![Zrzut ekranu przedstawiający Operations Manager, pokazując wybór opcji Konfiguruj serwer proxy](./media/gateway/scom01.png)

1. Wybierz opcję **Użyj serwera proxy, aby uzyskać dostęp do pakietu Operations Management Suite** , a następnie wprowadź adres IP serwera bramy log Analytics lub wirtualnego adresu IP modułu równoważenia obciążenia. Ostrożnie Rozpocznij od prefiksu `http://` .

   ![Zrzut ekranu przedstawiający Operations Manager, pokazujący adres serwera proxy](./media/gateway/scom02.png)

1. Wybierz pozycję **Zakończ**. Grupa zarządzania Operations Manager jest teraz skonfigurowana do komunikowania się za pomocą serwera bramy do usługi Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurowanie Operations Manager, gdzie określeni agenci używają serwera proxy

W przypadku dużych lub złożonych środowisk można chcieć używać tylko określonych serwerów (lub grup) do korzystania z serwera bramy Log Analytics.  W przypadku tych serwerów nie można zaktualizować agenta Operations Manager bezpośrednio, ponieważ ta wartość jest zastępowana przez wartość globalną grupy zarządzania.  Zamiast tego należy zastąpić regułę użytą do wypchnięcia tych wartości.  

> [!NOTE] 
> Użyj tej techniki konfiguracji, jeśli chcesz zezwolić na wiele serwerów bramy Log Analytics w danym środowisku.  Można na przykład wymagać określenia określonych serwerów bramy Log Analytics w zależności od regionu.
>

Aby skonfigurować określone serwery lub grupy do korzystania z serwera bramy Log Analytics: 

1. Otwórz konsolę Operations Manager i wybierz obszar roboczy **Tworzenie** .  
1. W obszarze roboczym Tworzenie wybierz pozycję **reguły**. 
1. Na pasku narzędzi Operations Manager wybierz przycisk **zakres** . Jeśli ten przycisk jest niedostępny, upewnij się, że w okienku **monitorowanie** został wybrany obiekt, a nie folder. W oknie dialogowym **zakres obiektów pakietu administracyjnego** zostanie wyświetlona lista wspólnych klas, grup lub obiektów przeznaczonych do użycia. 
1. W polu **Wyszukaj** wpisz **Usługa kondycji** i wybierz go z listy. Wybierz przycisk **OK**.  
1. Wyszukaj **regułę ustawień serwera proxy usługi Advisor**. 
1. Na pasku narzędzi Operations Manager wybierz pozycję **zastąpienia** , a następnie wskaż polecenie **Przesłoń Rule\For określony obiekt klasy: usługa kondycji** i wybierz obiekt z listy.  Lub Utwórz grupę niestandardową zawierającą obiekt usługi kondycji serwerów, do których chcesz zastosować to zastąpienie. Następnie Zastosuj zastąpienie do grupy niestandardowej.
1. W oknie dialogowym **Właściwości przesłonięcia** Dodaj znacznik wyboru w kolumnie **Przesłoń** obok parametru **WebProxyAddress** .  W polu **wartość zastąpienia** wprowadź adres URL serwera bramy log Analytics. Ostrożnie Rozpocznij od prefiksu `http://` .  

    >[!NOTE]
    > Nie musisz włączać reguły. Jest już ona zarządzana automatycznie przy użyciu zastąpienia w pakiecie administracyjnym "Zastąp" programu Microsoft System Center Advisor, który jest przeznaczony dla grupy programu Microsoft System Center Advisor Monitoring Server.
    > 

1. Wybierz pakiet administracyjny z listy **Wybierz docelowy pakiet administracyjny** lub Utwórz nowy niezapieczętowany pakiet administracyjny, wybierając pozycję **Nowy**. 
1. Po zakończeniu wybierz pozycję **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurowanie dla hybrydowych procesów roboczych elementów Runbook usługi Automation

Jeśli w środowisku istnieją hybrydowe procesy robocze elementów Runbook usługi Automation, wykonaj następujące kroki, aby skonfigurować bramę do obsługi procesów roboczych.

Zapoznaj się z sekcją [Konfigurowanie sieci](../../automation/automation-hybrid-runbook-worker.md#network-planning) w dokumentacji usługi Automation, aby znaleźć adres URL dla każdego regionu.

Jeśli komputer jest zarejestrowany jako hybrydowy proces roboczy elementu Runbook automatycznie, na przykład jeśli rozwiązanie Update Management jest włączone dla co najmniej jednej maszyny wirtualnej, wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych środowiska uruchomieniowego zadania do listy dozwolonych hostów w bramie Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy Log Analytics przy użyciu następującego polecenia cmdlet programu PowerShell: `Restart-Service OMSGatewayService`

Jeśli komputer jest przyłączony do Azure Automation przy użyciu polecenia cmdlet hybrydowego rejestrowania procesu roboczego elementu Runbook, wykonaj następujące czynności:

1. Dodaj adres URL rejestracji usługi agenta do listy dozwolonych hostów w bramie Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Dodaj adresy URL usługi danych środowiska uruchomieniowego zadania do listy dozwolonych hostów w bramie Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne polecenia cmdlet programu PowerShell

Za pomocą poleceń cmdlet można wykonać zadania w celu zaktualizowania ustawień konfiguracji bramy Log Analytics. Przed użyciem poleceń cmdlet programu należy się upewnić, że:

1. Zainstaluj bramę Log Analytics (Microsoft Instalator Windows).
1. Otwórz okno konsoli programu PowerShell.
1. Zaimportuj moduł, wpisując następujące polecenie: `Import-Module OMSGateway`
1. Jeśli w poprzednim kroku nie wystąpił błąd, moduł został pomyślnie zaimportowany i można użyć poleceń cmdlet. Wprowadź wartość `Get-Module OMSGateway`
1. Po użyciu poleceń cmdlet, aby wprowadzić zmiany, uruchom ponownie usługę bramy OMS.

Błąd w kroku 3 oznacza, że moduł nie został zaimportowany. Ten błąd może wystąpić, gdy program PowerShell nie może odnaleźć modułu. Moduł można znaleźć w ścieżce instalacji usługi OMS Gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Polecenie cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagany) <br> Wartość |Zmienia konfigurację usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy przekazywania (nadrzędnego) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło (bezpieczny ciąg) |Ustawia adres (i poświadczenia) serwera proxy przekazywania (nadrzędnego) |1. Ustaw serwer proxy przekaźnika i poświadczenie:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw serwer proxy przekaźnika, który nie wymaga uwierzytelniania: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienie serwera proxy przekazywania:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera aktualnie dozwolony Host (tylko lokalnie skonfigurowany dozwolony host, nie pobiera automatycznie dozwolonych hostów) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (wymagane) |Dodaje hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (wymagane) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagany) |Dodaje certyfikat klienta podmiot do listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagany) |Usuwa podmiot certyfikatu klienta z listy dozwolonych |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera aktualnie dozwolonych podmiotów certyfikatów klienta (tylko lokalnie skonfigurowany dozwolony podmiot, nie pobiera automatycznie pobranych przedmiotów) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby zbierać zdarzenia zarejestrowane przez bramę, należy zainstalować agenta Log Analytics.

![Zrzut ekranu przedstawiający listę Podgląd zdarzeń w dzienniku bramy Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Identyfikatory i opisy zdarzeń bramy Log Analytics

W poniższej tabeli przedstawiono identyfikatory i opisy zdarzeń dotyczących zdarzeń dziennika bramy Log Analytics.

| **ID (Identyfikator)** | **Opis** |
| --- | --- |
| 400 |Dowolny błąd aplikacji, który nie ma określonego identyfikatora. |
| 401 |Nieprawidłowa konfiguracja. Na przykład, listenPort = "text" zamiast liczby całkowitej. |
| 402 |Wyjątek podczas analizowania komunikatów uzgadniania protokołu TLS. |
| 403 |Błąd sieci. Na przykład nie można nawiązać połączenia z serwerem docelowym. |
| 100 |Informacje ogólne. |
| 101 |Usługa została uruchomiona. |
| 102 |Usługa została zatrzymana. |
| 103 |Odebrano polecenie HTTP CONNECT od klienta. |
| 104 |To nie jest polecenie HTTP CONNECT. |
| 105 |Serwer docelowy nie znajduje się na liście dozwolonych lub port docelowy nie jest bezpieczny (443). <br> <br> Upewnij się, że Agent MMA na serwerze bramy OMS i agenci, którzy komunikują się z bramą pakietu OMS, są połączeni z tym samym obszarem roboczym Log Analytics. |
| 105 |BŁĄD TcpConnection — nieprawidłowy certyfikat klienta: CN = Gateway. <br><br> Upewnij się, że korzystasz z usługi OMS Gateway w wersji 1.0.395.0 lub nowszej. Upewnij się również, że Agent MMA na serwerze bramy OMS i agenci komunikują się z bramą OMS są połączeni z tym samym obszarem roboczym Log Analytics. |
| 106 |Nieobsługiwana wersja protokołu TLS/SSL.<br><br> Brama Log Analytics obsługuje tylko protokoły TLS 1,0, TLS 1,1 i 1,2. Nie obsługuje protokołu SSL.|
| 107 |Sesja TLS została zweryfikowana. |

### <a name="performance-counters-to-collect"></a>Liczniki wydajności do zebrania

W poniższej tabeli przedstawiono liczniki wydajności dostępne dla bramy Log Analytics. Użyj monitora wydajności, aby dodać liczniki.

| **Nazwa** | **Opis** |
| --- | --- |
| Log Analytics Brama/aktywne połączenie z klientem |Liczba aktywnych połączeń sieci klienta (TCP) |
| Log Analytics Brama/liczba błędów |Liczba błędów |
| Log Analytics Gateway/podłączony klient |Liczba podłączonych klientów |
| Liczba bram Log Analytics/odrzucenia |Liczba odrzuconych z powodu dowolnego błędu walidacji protokołu TLS |

![Zrzut ekranu przedstawiający interfejs Log Analytics Gateway, pokazujący liczniki wydajności](./media/gateway/counters.png)

## <a name="assistance"></a>Pomoc

Po zalogowaniu się do Azure Portal można uzyskać pomoc dotyczącą bramy Log Analytics lub dowolnej innej usługi lub funkcji platformy Azure.
Aby uzyskać pomoc, wybierz ikonę znaku zapytania w prawym górnym rogu portalu i wybierz pozycję **nowe żądanie obsługi**. Następnie uzupełnij nowy formularz żądania pomocy technicznej.

![Zrzut ekranu przedstawiający nowe żądanie obsługi](./media/gateway/support.png)

## <a name="next-steps"></a>Następne kroki

[Dodawanie źródeł danych](./../agents/agent-data-sources.md) w celu zbierania danych z połączonych źródeł i przechowywania danych w obszarze roboczym log Analytics.