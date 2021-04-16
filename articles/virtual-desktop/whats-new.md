---
title: Co nowego w programie Windows Virtual Desktop? — Azure
description: Nowe funkcje i aktualizacje produktów dla Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 242dd2476783a3d1db6b74452d35e87c5cd189b6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516556"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Co nowego w programie Windows Virtual Desktop?

Windows Virtual Desktop regularnie przeprowadzać aktualizacje. Ten artykuł zawiera informacje na temat:

- Najnowsze aktualizacje
- Nowe funkcje
- Ulepszenia istniejących funkcji
- Poprawki błędów

Ten artykuł jest aktualizowany co miesiąc. Pamiętaj, aby często sprawdzać tutaj, aby być na bieżąco z nowymi aktualizacjami.

## <a name="client-updates"></a>Aktualizacje klienta

Zapoznaj się z tymi artykułami, aby dowiedzieć się więcej o aktualizacjach dla naszych klientów Windows Virtual Desktop i Usługi pulpitu zdalnego:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Sieć Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows Virtual Desktop aktualizacji agenta

Agent Windows Virtual Desktop co najmniej raz w miesiącu.

Oto, co zmieniło się w Windows Virtual Desktop Agent:

- Wersja 1.0.2990.800: ta aktualizacja została wydana 13 kwietnia 2021 r. i ma następujące zmiany:
    - Zaktualizowano komunikaty o błędach agenta.
    - Dodaje wyjątek, który uniemożliwia instalowanie agentów innych niż Windows 7 na komputerach wirtualnych z systemem Windows 7.
    - Zaktualizowano logikę usługi pulsu.
- Wersja 1.0.2944.1400: ta aktualizacja została wydana 7 kwietnia 2021 r. i ma następujące zmiany:
    - Linki do przewodnika rozwiązywania problemów Windows Virtual Desktop Agent w dziennikach podglądu zdarzeń dotyczące błędów agenta.
    - Dodano dodatkowy wyjątek w celu lepszej obsługi błędów.
    - Dodano WVDAgentUrlTool.exe, która umożliwia klientom sprawdzanie wymaganych adresów URL, do których mogą uzyskać dostęp.
- Wersja 1.0.2866.1500: ta aktualizacja została wydana 26 marca 2021 r. i rozwiązuje problem z sprawdzaniem kondycji stosu.
- Wersja 1.0.2800.2802: ta aktualizacja została wydana 10 marca 2021 r. i ma ogólne ulepszenia i poprawki błędów.
- Wersja 1.0.2800.2800: ta aktualizacja została wydana 2 marca 2021 r. i rozwiązuje problem z połączeniem odwrotnym.
- Wersja 1.0.2800.2700: ta aktualizacja została wydana 10 lutego 2021 r. i zawiera ogólne ulepszenia i poprawki błędów.
- Wersja 1.0.2800.2700: ta aktualizacja została wydana 4 lutego 2021 r. i rozwiązuje problem z aranżacją odmowy dostępu.

## <a name="fslogix-updates"></a>Aktualizacje produktu FSLogix

Zastanawiasz się nad najnowszymi aktualizacjami produktu FSLogix? Zapoznaj się [z nowościami w produktu FSLogix.](/fslogix/whats-new)

## <a name="march-2021"></a>Marzec 2021 r.

Oto, co zmieniło się w marcu 2021 r.

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Aktualizacje interfejsu użytkownika Azure Portal dla Windows Virtual Desktop

W celu Windows Virtual Desktop dla Azure Portal:

- Włączyliśmy nowe opcje dostępności (zestaw dostępności i strefy) dla przepływów pracy w celu tworzenia pul hostów i dodawania maszyn wirtualnych.
- Rozwiązaliśmy problem, który oznaczał, że host ze stanem "Wymaga pomocy" był niedostępny. Teraz obok hosta będzie wyświetlana ikona ostrzeżenia.
- Włączyliśmy sortowanie dla aktywnych sesji.
- Teraz możesz wysyłać komunikaty do określonych użytkowników lub wylogować się z tych użytkowników na karcie szczegółów hosta.
- Zmieniliśmy pole maksymalnego limitu sesji.
- Dodaliśmy ścieżkę weryfikacji do przepływu pracy w celu utworzenia puli hostów.
- Podczas tworzenia puli hostów osobistych można teraz używać najnowszej Windows 10 obrazu.

### <a name="generation-2-images-and-trusted-launch"></a>Obrazy generacji 2 i zaufane uruchamianie

Obrazy Azure Marketplace generacji 2 dla Windows 10 Enterprise i Windows 10 Enterprise wiele sesji. Te obrazy umożliwiają korzystanie z zaufanych maszyn wirtualnych uruchamiania. Dowiedz się więcej o maszynach wirtualnych 2. generacji z tematu [Should I create a generation 1 or 2 virtual machine](../virtual-machines/generation-2.md)(Czy należy utworzyć maszynę wirtualną generacji 1, czy 2). Aby dowiedzieć się, jak aprowizować maszyny Windows Virtual Desktop z zaufanym uruchamianiem, zobacz [nasz wpis w witrynie TechCo w witrynie TechCo w witrynie](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170).

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FsLogix jest teraz wstępnie zainstalowany na Windows 10 Enterprise obrazów z wieloma sesjami

Na podstawie opinii klientów skonfigurowaliśmy nową wersję obrazu usługi Windows 10 Enterprise z wieloma sesjami, który ma już nieskonfigurowane wersje produktu FSLogix. Mamy nadzieję, że ułatwi to Windows Virtual Desktop wdrożenia.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor dla Windows Virtual Desktop jest teraz ogólnie dostępne

Azure Monitor dla Windows Virtual Desktop jest teraz ogólnie dostępny publicznie. Ta funkcja jest zautomatyzowaną usługą, która monitoruje wdrożenia i umożliwia wyświetlanie zdarzeń, kondycji i sugestii dotyczących rozwiązywania problemów w jednym miejscu. Aby uzyskać więcej informacji, zapoznaj się [z naszą dokumentacją](azure-monitor.md) lub zapoznaj się z naszym wpisem w witrynie [TechCo w witrynie](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861).

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>Aktualizacje aplikacji Teams na platformie Windows Virtual Desktop z marca 2021 Windows Virtual Desktop

W aplikacji Teams w 2018 r. wdązyliśmy następujące Windows Virtual Desktop:

- Poprawiliśmy wydajność jakości wideo w wywołaniach i trybie 2x2.
- Zmniejszyliśmy wykorzystanie procesora CPU o 5–10% (w zależności od generacji procesora CPU) dzięki użyciu sprzętowego odciążania przetwarzania wideo (CPUP).
- Starsze maszyny mogą teraz używać dekodowania sprzętowego i STANDARDOWEGO w celu bezproblemowego wyświetlania większej liczby przychodzących strumieni wideo w trybie 2x2.
- Zaktualizowaliśmy stos WebRTC z M74 do M88, aby uzyskać lepszą wydajność synchronizacji av i mniej przejściowych problemów.
- Zastąpiliśmy nasz koder oprogramowania H264 openH264 (system operacyjny używany w aplikacji Teams w internecie), co zwiększyło jakość wideo kamery wychodzącej.
- 30 marca włączyliśmy tryb 2x2 dla serwera usługi Teams dla ogółu osób. Tryb 2x2 wyświetla jednocześnie maksymalnie cztery przychodzące strumienie wideo.

### <a name="start-vm-on-connect-public-preview"></a>Uruchamianie maszyny wirtualnej w oknie Łączenie z publiczną podglądem

Nowe ustawienie puli hostów, Uruchom maszynę wirtualną w oknie Połącz, jest teraz dostępne w publicznej wersji zapoznawczej. To ustawienie umożliwia włączanie maszyn wirtualnych w dowolnym momencie. Jeśli chcesz zaoszczędzić koszty, cofniesz alokację maszyn wirtualnych, konfigurując Azure Compute maszyn wirtualnych. Aby uzyskać więcej informacji, zapoznaj się [z naszym wpisem w blogu](https://aka.ms/wvdstartvmonconnect) i [dokumentacją](start-virtual-machine-connect.md).

### <a name="windows-virtual-desktop-specialty-certification"></a>Windows Virtual Desktop certyfikacji specjalizacji

Wydaliśmy wersję beta egzaminu AZ-140, który pozwoli Ci udowodnić swoją wiedzę na temat Windows Virtual Desktop na platformie Azure. Aby dowiedzieć się więcej, zapoznaj się [z naszym wpisem TechCo nie tylko](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107).

## <a name="february-2021"></a>Luty 2021 r.

Oto, co zmieniło się w lutym 2021 r.

### <a name="portal-experience"></a>Środowisko portalu

Ulepszyliśmy środowisko Azure Portal w następujący sposób:

- Tryb zbiorczego opróżniania na hostach na karcie siatki hosta sesji. 
- Dołączanie aplikacji MSIX jest teraz dostępne w publicznej wersji zapoznawczej.
- Naprawiono informacje o przeglądzie puli hostów dla trybu ciemnego.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Magazyn metadanych UE jest teraz w publicznej wersji zapoznawczej

Teraz hostemy publiczną podgląd lokalizacji geograficznej Europy (UE) jako opcję magazynu metadanych usługi w usłudze Windows Virtual Desktop. Podczas tworzenia obiektów usługi klienci mogą wybierać spośród regionów Europa Zachodnia i Europa Północna. Obiekty usługi i metadane pul hostów będą przechowywane w lokalizacji geograficznej platformy Azure skojarzonej z każdym regionem. Aby dowiedzieć się więcej, przeczytaj [nasz wpis w blogu z announcing the public preview (Publiczna wersja zapoznawcza).](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Aktualizacje wtyczki Windows Virtual Desktop Teams

Ulepszyliśmy jakość wywołań wideo we wtyczce Windows Virtual Desktop przez rozwiązanie najczęściej zgłaszanych problemów, takich jak nieoczekiwane zaciemnianie ekranu lub desynchronizowany dźwięk i wideo. Te ulepszenia powinny zwiększyć wydajność pojedynczego widoku wideo przy aktywnym przełączaniu osoby mówiącej. Rozwiązaliśmy również problem z tym, że urządzenia sprzętowe ze znakami specjalnymi nie były dostępne w aplikacji Teams.

## <a name="january-2021"></a>Styczeń 2021 r.

Oto, co zmieniło się w styczniu 2021 r.:

### <a name="new-windows-virtual-desktop-offer"></a>Nowa Windows Virtual Desktop oferty

Nowi klienci oszczędzają 30% na kosztach Windows Virtual Desktop obliczeniowych dla maszyn wirtualnych serii D i Bs przez maksymalnie 90 dni w przypadku korzystania z natywnego rozwiązania firmy Microsoft. Tę ofertę można zrealizować w Azure Portal przed 31 marca 2021 r. Dowiedz się więcej na [naszej Windows Virtual Desktop oferty.](https://azure.microsoft.com/services/virtual-desktop/offer/)

### <a name="networksecuritygrouprules-value-change"></a>Zmiana wartości networkSecurityGroupRules 

W Azure Resource Manager szablonu zmieniliśmy wartość domyślną dla obiektu networkSecurityGroupRules na tablicę. Zapobiegnie to błędom w przypadku użycia managedDisks-customimagevm.jsbez określania wartości networkSecurityGroupRules. Nie była to zmiana przerywana i jest zgodna z poprzednimi wersjami.

### <a name="fslogix-hotfix-update"></a>Aktualizacja poprawki fsLogix

W poprzedniej wersji (2.9.7621.30127) wydaliśmy fsLogix w wersji 2009 HF_01 (2.9.7654.46150). Zalecamy jak najszybciej zaprzestanie korzystania z poprzedniej wersji i zaktualizowanie produktu FSLogix.

Aby uzyskać więcej informacji, zobacz informacje o wersji w te [tematze What's new in FSLogix (Co nowego w produktu FSLogix).](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)

### <a name="azure-portal-experience-improvements"></a>Azure Portal ulepszenia interfejsu użytkownika

Wprowadzono następujące ulepszenia w Azure Portal użytkownika:

- Teraz możesz bezpośrednio dodać poświadczenia administratora lokalnej maszyny wirtualnej zamiast dodawać konto lokalne utworzone przy użyciu poświadczeń konta przyłączania do domeny usługi Active Directory.
- Użytkownicy mogą teraz wyświetlić listę przypisań poszczególnych użytkowników i grup na osobnych kartach dla poszczególnych użytkowników i grup.
- Numer wersji agenta usługi Windows Virtual Desktop jest teraz widoczny w przeglądzie maszyny wirtualnej dla pul hostów.
- Dodano usuwanie zbiorcze dla pul hostów i grup aplikacji.
- Teraz można włączyć lub wyłączyć tryb opróżniania dla wielu hostów sesji w puli hostów.
- Usunięto pole publicznego adresu IP ze strony szczegółów maszyny wirtualnej.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows Virtual Desktop rozwiązywanie problemów z agentem

Niedawno s skonfigurowanie przewodnika [rozwiązywania Windows Virtual Desktop Agent pomaga](troubleshoot-agent.md) klientom, którzy napotkali typowe problemy.

### <a name="microsoft-defender-for-endpoint-integration"></a>Integracja usługi Microsoft Defender dla punktu końcowego

Integracja usługi Microsoft Defender dla punktu końcowego jest teraz ogólnie dostępna. Ta funkcja zapewnia maszynom Windows Virtual Desktop wirtualne takie samo środowisko badania, jak lokalne maszyny Windows 10 wirtualnej. Jeśli używasz wielosesyjnej usługi Windows 10 Enterprise, usługa Microsoft Defender dla punktu końcowego będzie obsługiwać do 50 równoczesnych połączeń użytkowników, co zapewnia oszczędności kosztów korzystania z wielu sesji usługi Windows 10 Enterprise i pewność usługi Microsoft Defender dla punktu końcowego. Aby uzyskać więcej informacji, zapoznaj się z naszym [wpisem w blogu](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Windows Virtual Desktop

Niedawno opublikowaliśmy [artykuł o punkcie](security-baseline.md) odniesienia zabezpieczeń platformy Azure dla Windows Virtual Desktop, na który chcemy zwrócić Twoją uwagę. Te wytyczne zawierają informacje dotyczące sposobu stosowania testu porównawczego zabezpieczeń platformy Azure w wersji 2.0 do Windows Virtual Desktop. Test porównawczy zabezpieczeń platformy Azure opisuje ustawienia i rozwiązania, których zalecamy używać do zabezpieczania rozwiązań w chmurze na platformie Azure.

## <a name="december-2020"></a>Grudzień 2020 r.

Oto, co zmieniło się w grudniu 2020 r.: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor dla Windows Virtual Desktop

Publiczna wersja zapoznawcza Azure Monitor dla Windows Virtual Desktop jest teraz dostępna. Ta nowa funkcja zawiera niezawodny pulpit nawigacyjny zbudowany na podstawie skoroszytów Azure Monitor, aby ułatwić specjalistom IT zrozumienie ich Windows Virtual Desktop biznesowych. Aby uzyskać więcej informacji, zapoznaj się [z ogłoszeniem](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) w naszym blogu. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager szablonu 

W najnowszej aktualizacji usunęliśmy wszystkie parametry publicznego adresu IP z szablonu Azure Resource Manager tworzenia i aprowizowania pul hostów. Zdecydowanie zalecamy unikanie używania publicznych Windows Virtual Desktop w celu zapewnienia bezpieczeństwa wdrożenia. Jeśli wdrożenie opierało się na publicznych adresach IP, należy ponownie skonfigurować je tak, aby zamiast tego używać prywatnych adresów IP. W przeciwnym razie wdrożenie nie będzie działać prawidłowo.

### <a name="msix-app-attach-public-preview"></a>Dołączanie aplikacji MSIX w publicznej wersji zapoznawczej 

Dołączanie aplikacji MSIX to inna usługa, która rozpoczęła swoją publiczną podgląd w tym miesiącu. Dołączanie aplikacji MSIX to usługa, która dynamicznie przedstawia aplikacje MSIX maszynom Windows Virtual Desktop hosta sesji. Aby uzyskać więcej informacji, zapoznaj się [z ogłoszeniem](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) w naszym blogu. 

### <a name="screen-capture-protection"></a>Ochrona przechwytywania ekranu 

W tym miesiącu oznaczono również początek publicznej wersji zapoznawczej ochrony przechwytywania ekranu. Ta funkcja umożliwia zapobieganie przechwytywaniu poufnych informacji w punktach końcowych klienta. Spróbuj użyć ochrony przechwytywania ekranu, przechodząc [do tej strony](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Wbudowane role

Dodaliśmy nowe wbudowane role dla Windows Virtual Desktop uprawnień administratora. Aby uzyskać więcej informacji, zobacz [Wbudowane role dla Windows Virtual Desktop](rbac.md). 

### <a name="application-group-limit-increase"></a>Zwiększenie limitu grupy aplikacji

Zwiększyliśmy domyślny limit grup aplikacji na dzierżawę Azure Active Directory do 200 grup.

### <a name="client-updates-for-december-2020"></a>Aktualizacje klienta z grudnia 2020 r.

Wydaliśmy nowe wersje następujących klientów: 

- Android
- macOS
- Windows

Aby uzyskać więcej informacji na temat aktualizacji klienta, zobacz [Aktualizacje klienta](whats-new.md#client-updates).

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="azure-portal-experience"></a>Azure Portal użytkownika

Naprawiliśmy dwa błędy w Azure Portal użytkownika:

- Przyjazna nazwa aplikacji klasycznej nie jest już zastępowana w przepływie pracy "Dodawanie maszyny wirtualnej".
- Karta hosta sesji zostanie załadowana, jeśli hosty sesji są częścią zestawów skalowania.

### <a name="fslogix-client-version-2009"></a>Klient FSLogix, wersja 2009 

Wydaliśmy nową wersję klienta FSLogix z wieloma poprawkami i ulepszeniami. Dowiedz się więcej [z naszego wpisu w blogu](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Krótkościeżka RDP w publicznej wersji zapoznawczej

Skrót RDP wprowadza bezpośrednią łączność z hostem Windows Virtual Desktop przy użyciu sieci VPN typu punkt-lokacja i lokacja-lokacja oraz usługi ExpressRoute. Wprowadza również protokół transportu URCP. Skrót RDP zaprojektowano w celu zmniejszenia opóźnień i przeskoków sieciowych w celu poprawy komfortu użytkownika. Dowiedz się więcej [na Windows Virtual Desktop RDP Shortpath](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, wersja 2.0.1

Wydaliśmy wersję 2.0.1 Windows Virtual Desktop cmdlet. Ta aktualizacja obejmuje polecenia cmdlet, które umożliwiają zarządzanie dołączanie aplikacji MSIX. Nową wersję można pobrać z [galerii programu PowerShell.](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)

### <a name="azure-advisor-updates"></a>Azure Advisor aktualizacji

Azure Advisor ma teraz nowe zalecenie dotyczące wskazówek dotyczących odległości w sieci Windows Virtual Desktop oraz nowe zalecenie dotyczące optymalizacji wydajności w pulach hostów o zrównoważonym obciążeniu. Dowiedz się więcej w [witrynie internetowej platformy Azure.](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)

## <a name="october-2020"></a>Październik 2020 r.

Oto, co zmieniło się w październiku 2020 r.:

### <a name="improved-performance"></a>Większa wydajność

- Zoptymalizowaliśmy wydajność, zmniejszając opóźnienie połączenia w następujących lokalizacjach geograficznych platformy Azure:
    - Szwajcaria
    - Kanada

Teraz możesz użyć estymatora [szacowania doświadczenia,](https://azure.microsoft.com/services/virtual-desktop/assessment/) aby oszacować jakość obsługi użytkownika w tych obszarach.

### <a name="azure-government-cloud-availability"></a>Azure Government dostępności chmury

Usługa Azure Government Cloud jest teraz ogólnie dostępna. Dowiedz się więcej [z naszego wpisu w blogu](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure Portal aktualizacji

W programie wdaliśmy się w Windows Virtual Desktop Azure Portal:

- Naprawiono błąd resourceID, który uniemożliwiał użytkownikom otwieranie karty "Sesje".
- Usprawniliśmy interfejs użytkownika na karcie "Hosty sesji".
- Naprawiono ustawienia "Wartości domyślne", "Użyteczność" i "Przywróć wartości domyślne" we właściwościach protokołu RDP.
- Funkcje "Usuń" i "Usuń" stały się spójne na wszystkich kartach.
- Portal weryfikuje teraz nazwy aplikacji w przepływie pracy "Dodawanie aplikacji".
- Rozwiązano problem, który miał miejsce, gdy dane eksportu hosta sesji nie były wyrównane w kolumnach.
- Rozwiązano problem, który mógł dotyczyć sytuacji, w której portal nie mógł pobrać sesji użytkowników.
- Rozwiązano problem z pobieraniem hosta sesji, który miał miejsce, gdy maszyna wirtualna została utworzona w innej grupie zasobów.
- Zaktualizowano kartę "Host sesji", aby wyświetlić zarówno sesje aktywne, jak i rozłączone.
- Karta "Aplikacje" zawiera teraz strony.
- Rozwiązaliśmy problem, który oznaczał, że tekst "wymaga wiersza polecenia" nie był wyświetlany poprawnie na karcie "Lista aplikacji".
- Rozwiązano problem, który dotyczył sytuacji, w których portal nie mógł wdrożyć pul hostów ani maszyn wirtualnych podczas korzystania z wersji językowej Shared Image Gallery.

### <a name="client-updates-for-october-2020"></a>Aktualizacje klienta w październiku 2020 r.

Wydaliśmy nowe wersje klientów. Zobacz następujące artykuły, aby dowiedzieć się więcej:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Aby uzyskać więcej informacji o innych klientach, zobacz [Aktualizacje klienta](#client-updates).

## <a name="september-2020"></a>Wrzesień 2020

Oto, co zmieniło się we wrześniu 2020 r.:

- Zoptymalizowaliśmy wydajność, zmniejszając opóźnienie połączenia w następujących lokalizacjach geograficznych platformy Azure:
    - Niemcy
    - Republika Południowej Afryki (tylko w przypadku środowisk walidacji)

Teraz możesz użyć estymatora [szacowania](https://azure.microsoft.com/services/virtual-desktop/assessment/) doświadczenia, aby oszacować jakość obsługi użytkownika w tych obszarach.

- Wydaliśmy wersję 1.2.1364 klienta klasycznego systemu Windows dla Windows Virtual Desktop. W tej aktualizacji zostały wprowadzone następujące zmiany:
    - Rozwiązano problem, który oznaczał, że logowanie jednokrotne nie działało w systemie Windows 7.
    - Rozwiązano problem, który powodował rozłączanie klienta, gdy użytkownik, który włączył optymalizację multimediów dla usługi Teams, próbował wywołać spotkanie usługi Teams lub dołączyć do niego, gdy inna aplikacja miała strumień audio otwarty w trybie wyłączności.
    - Rozwiązano problem, który oznaczał, że aplikacja Teams nie wyliczała urządzeń audio i wideo po włączeniu optymalizacji multimediów dla usługi Teams.
    - Dodano komunikat "Potrzebujesz pomocy dotyczącej ustawień?" link do strony ustawień pulpitu.
    - Rozwiązano problem z przyciskiem "Subskrybuj", który miał miejsce w przypadku korzystania z ciemnych motywów o wysokim kontraście.
    
- Dzięki ogromnej pomocy ze strony naszych użytkowników rozwiązaliśmy dwa krytyczne problemy dotyczące Microsoft Store Pulpit zdalny klienta. Będziemy nadal przeglądać opinie i naprawiać problemy, ponieważ rozszerzamy etapowe wydanie klienta dla większej liczby użytkowników na całym świecie.
    
- Dodaliśmy nową funkcję, która umożliwia zmianę lokalizacji maszyny wirtualnej, obrazu, grupy zasobów, nazwy prefiksu i konfiguracji sieci w ramach przepływu pracy w celu dodania maszyny wirtualnej do wdrożenia w Azure Portal.

- Informatycy mogą teraz zarządzać Azure Active Directory maszynami Windows 10 Enterprise przy użyciu rozwiązania Microsoft Endpoint Manager. Aby dowiedzieć się więcej, zobacz [nasz wpis w blogu](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Sierpień 2020 r.

Oto, co zmieniło się w sierpniu 2020 r.:

- Ulepszyliśmy wydajność, aby zmniejszyć opóźnienie połączenia w następujących regionach świadczenia usługi Azure: 

    - Zjednoczone Królestwo
    - Francja
    - Norwegia
    - Korea Południowa

   Możesz użyć [estymatora do](https://azure.microsoft.com/services/virtual-desktop/assessment/) szacowania doświadczenia, aby uzyskać ogólne pojęcie o tym, jak te zmiany wpłyną na użytkowników.

- Klient Microsoft Store Pulpit zdalny (wersja 10.2.1522 lub nowsza) jest teraz ogólnie dostępny! Ta wersja klienta Microsoft Store Pulpit zdalny jest zgodna z Windows Virtual Desktop. Wprowadziliśmy również odświeżone przepływy interfejsu użytkownika, aby ulepszyć środowisko użytkownika. Ta aktualizacja obejmuje fluent design, tryb jasny i ciemny oraz wiele innych interesujących zmian. Przepisaliśmy również klienta tak, aby używać tego samego aparatu protokołu RDP (Remote Desktop Protocol) co klienci z systemami iOS, macOS i Android. Dzięki temu możemy szybciej dostarczać nowe funkcje na wszystkich platformach. [Pobierz klienta i](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) wypróbuj go!

- Rozwiązaliśmy problem w kliencie usługi Teams Desktop (wersja 1.3.00.21759), który pokazywał strefę czasową UTC tylko w rozmowie, kanałach i kalendarzu. Zaktualizowany klient wyświetla teraz strefę czasową sesji zdalnej.

- Azure Advisor jest teraz częścią Windows Virtual Desktop. Gdy uzyskujesz dostęp Windows Virtual Desktop za pośrednictwem Azure Portal, możesz zobaczyć zalecenia dotyczące optymalizacji środowiska Windows Virtual Desktop danych. Dowiedz się więcej na [Azure Advisor](azure-advisor.md).

- Interfejs wiersza polecenia platformy Azure obsługuje Windows Virtual Desktop ( ), aby ułatwić `az desktopvirtualization` automatyzowanie Windows Virtual Desktop wdrożeń. Zapoznaj się [z pulpitemwirtualizację,](/cli/azure/ext/desktopvirtualization/) aby uzyskać listę poleceń rozszerzenia.

- Zaktualizowaliśmy nasze szablony wdrażania, aby były w pełni zgodne z Windows Virtual Desktop Azure Resource Manager interfejsami. Szablony można znaleźć w witrynie [GitHub.](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)

- Portal Windows Virtual Desktop US Gov jest teraz w publicznej wersji zapoznawczej. Aby dowiedzieć się więcej, zobacz [nasze zawiadomienie](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Lipiec 2020 r.  

Lipiec był wtedy, Windows Virtual Desktop integracji z usługą Azure Resource Management stała się ogólnie dostępna.

Oto, co zmieniło się w tej nowej wersji: 

- Wersja "Fall 2019" jest teraz znana jako "Windows Virtual Desktop (klasyczna)", a wersja "Spring 2020" to teraz tylko "Windows Virtual Desktop". Aby uzyskać więcej informacji, zapoznaj się z [tym wpisem w blogu](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Aby dowiedzieć się więcej o nowych funkcjach, zapoznaj się z [tym wpisem w blogu.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) 

### <a name="autoscaling-tool-update"></a>Aktualizacja narzędzia skalowania automatycznego

Najnowsza wersja narzędzia skalowania automatycznego, która była dostępna w wersji zapoznawczej, jest teraz ogólnie dostępna. To narzędzie używa konta usługi Azure Automation i aplikacji logiki platformy Azure do automatycznego zamykania i ponownego uruchamiania maszyn wirtualnych hosta sesji w puli hostów, co zmniejsza koszty infrastruktury. Aby dowiedzieć się [więcej, zobacz Skalowanie hostów sesji przy użyciu Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Teraz możesz wykonać następujące czynności za pomocą Azure Portal w Windows Virtual Desktop: 

- Bezpośrednie przypisywanie użytkowników do osobistych hostów sesji pulpitu  
- Zmienianie ustawienia środowiska weryfikacji dla pul hostów 

### <a name="diagnostics"></a>Diagnostyka

Wydaliśmy kilka nowych wstępnie utworzonych zapytań dla obszaru roboczego usługi Log Analytics. Aby uzyskać dostęp do zapytań, przejdź do opcji **Dzienniki i** w **obszarze Kategoria** wybierz **pozycję Windows Virtual Desktop**. Aby dowiedzieć się [więcej, zobacz Używanie usługi Log Analytics na potrzeby funkcji diagnostyki.](diagnostics-log-analytics.md)

### <a name="update-for-remote-desktop-client-for-android"></a>Aktualizacja klienta Pulpit zdalny dla systemu Android

Klient [Pulpit zdalny dla systemu Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) obsługuje teraz Windows Virtual Desktop połączeń. Począwszy od wersji 10.0.7, klient systemu Android zawiera nowy interfejs użytkownika, aby ulepszyć środowisko użytkownika. Klient integruje się również z Microsoft Authenticator na urządzeniach z systemem Android, aby umożliwić dostęp warunkowy podczas subskrybowania Windows Virtual Desktop roboczych.  

Poprzednia wersja klienta Pulpit zdalny nosi teraz nazwę "Pulpit zdalny 8". Wszystkie istniejące połączenia z wcześniejszą wersją klienta zostaną bezproblemowo przeniesione do nowego klienta. Nowy klient został przepisany do tego samego podstawowego aparatu podstawowego protokołu RDP co klienci z systemami iOS i macOS, co przyspiesza wydanie nowych funkcji na wszystkich platformach. 

### <a name="teams-update"></a>Aktualizacja aplikacji Teams

Wprowadzono ulepszenia aplikacji Microsoft Teams dla Windows Virtual Desktop. Co najważniejsze, Windows Virtual Desktop obsługuje teraz optymalizację dźwięku i wideo dla klienta klasycznego systemu Windows. Przekierowywanie poprawia opóźnienie, tworząc bezpośrednie ścieżki między użytkownikami, gdy używają dźwięku lub wideo w połączeniach i spotkaniach. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i brzmią sprawniej. Dowiedz się więcej na stronie Use Teams on Windows Virtual Desktop (Korzystanie [z Windows Virtual Desktop).](teams-on-wvd.md)

## <a name="june-2020"></a>Czerwiec 2020 r.

W zeszłym miesiącu wprowadziliśmy nową Windows Virtual Desktop z Azure Resource Manager w wersji zapoznawczej. Ta aktualizacja zawiera wiele interesujących nowych funkcji, o których chcemy Cię opowiedzieć. Oto co nowego w tej wersji Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop jest teraz zintegrowana z Azure Resource Manager

Windows Virtual Desktop jest teraz zintegrowana z Azure Resource Manager. W najnowszej aktualizacji wszystkie Windows Virtual Desktop są teraz Azure Resource Manager zasobów. Ta aktualizacja jest również zintegrowana z kontrolą dostępu na podstawie ról (RBAC) platformy Azure. Zobacz [Co to jest Azure Resource Manager? aby](../azure-resource-manager/management/overview.md) dowiedzieć się więcej.

Oto, co ta zmiana robi dla Ciebie:

- Windows Virtual Desktop jest teraz zintegrowana z Azure Portal. Oznacza to, że możesz zarządzać wszystkim bezpośrednio w portalu, bez programu PowerShell, aplikacji internetowych ani narzędzi innych firm. Aby rozpocząć pracę, zapoznaj się z naszym samouczkiem na stronie Create a host pool with the Azure Portal (Tworzenie [puli hostów przy użyciu Azure Portal).](create-host-pools-azure-marketplace.md)

- Przed tą aktualizacją można było publikować tylko aplikacje RemoteApps i pulpity dla poszczególnych użytkowników. Dzięki Azure Resource Manager można teraz publikować zasoby w Azure Active Directory grupy.

- Wcześniejsza wersja Windows Virtual Desktop miała cztery wbudowane role administratora, które można przypisać do dzierżawy lub puli hostów. Te role znajdują się teraz w kontroli dostępu opartej na rolach [(RBAC) platformy Azure.](../role-based-access-control/overview.md) Te role można zastosować do każdego Windows Virtual Desktop Azure Resource Manager, co pozwala na pełny, rozbudowany model delegowania.

- W tej aktualizacji nie trzeba już wielokrotnie uruchamiać Azure Marketplace ani szablonu usługi GitHub, aby rozszerzyć pulę hostów. Wystarczy rozwinąć pulę hostów, aby przejść do puli hostów na stronie Azure Portal wybrać pozycję **+ Dodaj,** aby wdrożyć dodatkowe hosty sesji.

- Wdrożenie puli hostów jest teraz w pełni zintegrowane z [usługą Azure Shared Image Gallery](../virtual-machines/shared-image-galleries.md). Shared Image Gallery to oddzielna usługa platformy Azure, która przechowuje definicje obrazów maszyn wirtualnych, w tym obsługę wersji obrazów. Replikacji globalnej można również używać do kopiowania i wysyłania obrazów do innych regionów świadczenia usługi Azure w celu wdrożenia lokalnego.

- Funkcje monitorowania, które były kiedyś wykonywane za pomocą programu PowerShell lub aplikacji internetowej usługi diagnostyki, zostały teraz przeniesione do usługi Log Analytics w Azure Portal. Teraz masz również dwie opcje wizualizacji raportów. Możesz uruchamiać zapytania kusto i używać skoroszytów do tworzenia raportów wizualnych.

- Nie musisz już wyrażać zgody Azure Active Directory (Azure AD) na korzystanie z Windows Virtual Desktop. W tej aktualizacji dzierżawa usługi Azure AD w ramach subskrypcji platformy Azure uwierzytelnia użytkowników i udostępnia administratorom mechanizmy kontroli RBAC platformy Azure.

### <a name="powershell-support"></a>Obsługa programu PowerShell

W tej aktualizacji dodaliśmy nowe polecenia cmdlet AzWvd do Azure PowerShell Az. Ten nowy moduł jest obsługiwany w PowerShell Core, który działa na .NET Core.

Aby zainstalować moduł, wykonaj instrukcje z [tematu Konfigurowanie modułu programu PowerShell dla programu Windows Virtual Desktop](powershell-module.md).

Listę dostępnych poleceń można również znaleźć w tece referencyjnej programu [AzWvd programu PowerShell.](/powershell/module/az.desktopvirtualization/#desktopvirtualization)

Aby uzyskać więcej informacji na temat nowych funkcji, zapoznaj się z [naszym wpisem w blogu](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Dodatkowe bramy

Dodaliśmy nowy klaster bramy w Południowej Azja, aby zmniejszyć opóźnienie połączenia.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Aplikacja Microsoft Teams w Windows Virtual Desktop (wersja zapoznawcza)

Wprowadzono pewne ulepszenia usługi Microsoft Teams dla Windows Virtual Desktop. Co najważniejsze, Windows Virtual Desktop obsługuje teraz przekierowywanie audio i wizualne dla wywołań. Przekierowywanie poprawia opóźnienie, tworząc bezpośrednie ścieżki między użytkownikami podczas wywołania przy użyciu dźwięku lub wideo. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i brzmią sprawniej.

Aby dowiedzieć się więcej, zobacz [nasz wpis w blogu](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat planów na [przyszłość można Microsoft 365 Windows Virtual Desktop planie.](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)
