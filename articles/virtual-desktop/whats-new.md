---
title: Co nowego w programie Virtual Desktop systemu Windows? — Azure
description: Nowe funkcje i aktualizacje produktów dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: overview
ms.date: 02/23/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: a2afd47db01725c56f4fe571f1b464818653081d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737498"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Co nowego w programie Virtual Desktop systemu Windows?

Regularnie aktualizacje pulpitu wirtualnego systemu Windows. W tym artykule znajdują się informacje o tym, jak:

- Najnowsze aktualizacje
- Nowe funkcje
- Ulepszenia istniejących funkcji
- Poprawki błędów

Ten artykuł jest aktualizowany co miesiąc. Upewnij się, że w tym miejscu często należy zaznaczyć, aby zachować nowe aktualizacje.

## <a name="client-updates"></a>Aktualizacje klienta

Zapoznaj się z tymi artykułami, aby dowiedzieć się więcej o aktualizacjach naszych klientów dla pulpitu wirtualnego systemu Windows i Usługi pulpitu zdalnego:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Sieć Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="fslogix-updates"></a>Aktualizacje FSLogix

Chcesz wiedzieć o najnowszych aktualizacjach dla FSLogix? Zapoznaj się z [nowościami w witrynie FSLogix](/fslogix/whats-new).

## <a name="february-2021"></a>Luty 2021 r.

Poniżej przedstawiono zmiany w lutym 2021.

### <a name="portal-experience"></a>Środowisko portalu

Ulepszono środowisko Azure Portal w następujący sposób:

- Tryb opróżniania zbiorczego na hostach na karcie Siatka hosta sesji. 
- Dołączenie do aplikacji MSIX jest teraz dostępne w publicznej wersji zapoznawczej.
- Informacje o stałej puli hostów dla trybu ciemnego.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Magazyn metadanych UE teraz w publicznej wersji zapoznawczej

Teraz udostępniamy publiczną wersję zapoznawczą geografii Europa (UE) jako opcję magazynu dla metadanych usługi w systemie Windows Virtual Desktop. Klienci mogą wybrać między Europa Zachodnia lub północnym podczas tworzenia obiektów usługi. Obiekty i metadane usługi dla pul hostów będą przechowywane w lokalizacji geograficznej platformy Azure skojarzonej z każdym regionem. Aby dowiedzieć się więcej, Przeczytaj [nasz wpis w blogu przedstawiający publiczną wersję zapoznawczą](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Zespoły dotyczące aktualizacji wtyczki pulpitu wirtualnego systemu Windows

Ulepszono jakość połączeń wideo w dodatku plug-in Windows Virtual Desktop przez rozpoznajenie najczęściej raportowanych problemów, takich jak w przypadku nagłego przechodzenia ekranu lub wideo i dźwiękowego. Te ulepszenia powinny zwiększyć wydajność widoku pojedynczego wideo z aktywnym przełączaniem głośników. Rozwiązano również problem polegający na tym, że urządzenia sprzętowe ze znakami specjalnymi nie były dostępne w programie Teams.

## <a name="january-2021"></a>Styczeń 2021 r.

Poniżej przedstawiono zmiany w styczniu 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nowa oferta pulpitu wirtualnego systemu Windows

Nowi klienci zapisują 30% kosztów obliczeniowych usług pulpitu wirtualnego systemu Windows dla maszyn wirtualnych z serii D i BS przez maksymalnie 90 dni w przypadku korzystania z natywnego rozwiązania firmy Microsoft. Tę ofertę można zrealizować w Azure Portal przed 31 marca 2021. Dowiedz się więcej na [stronie oferty pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/offer/).

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules zmiana wartości 

W Azure Resource Manager zagnieżdżonym szablonie zmieniono wartość domyślną networkSecurityGroupRules z obiektu na tablicę. Pozwoli to zapobiec występowaniu błędów, jeśli używasz managedDisks-customimagevm.json bez określania wartości dla networkSecurityGroupRules. To nie jest istotna zmiana i jest zgodna z poprzednimi wersjami.

### <a name="fslogix-hotfix-update"></a>Aktualizacja poprawki FSLogix

Firma Microsoft udostępniła FSLogix w wersji 2009 HF_01 (2.9.7654.46150) w celu rozwiązywania problemów z poprzednią wersją (2.9.7621.30127). Zalecamy zaprzestanie korzystania z poprzedniej wersji i aktualizowanie FSLogix najszybciej, jak to możliwe.

Aby uzyskać więcej informacji, zobacz informacje o wersji w artykule [co nowego w programie FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150).

### <a name="azure-portal-experience-improvements"></a>Ulepszenia środowiska Azure Portal

Wprowadzono następujące udoskonalenia dotyczące środowiska Azure Portalowego:

- Teraz można dodać poświadczenia administratora lokalnego maszyny wirtualnej bezpośrednio zamiast konieczności dodawania konta lokalnego utworzonego przy użyciu poświadczeń konta przyłączanie do domeny Active Directory.
- Użytkownicy mogą teraz wyświetlać listę przypisań poszczególnych użytkowników i grup w osobnych kartach.
- Numer wersji agenta wirtualnego systemu Windows jest teraz widoczny w temacie Omówienie pul hostów w maszynie wirtualnej.
- Dodano usuwanie zbiorcze dla pul hostów i grup aplikacji.
- Teraz można włączyć lub wyłączyć tryb opróżniania dla wielu hostów sesji w puli hostów.
- Usunięto pole publiczny adres IP ze strony szczegółów maszyny wirtualnej.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Rozwiązywanie problemów z agentem usług pulpitu wirtualnego systemu Windows

Niedawno został skonfigurowany [Przewodnik rozwiązywania problemów z agentem pulpitu wirtualnego systemu Windows](troubleshoot-agent.md) , aby pomóc klientom, którzy napotykali typowe problemy.

### <a name="microsoft-defender-for-endpoint-integration"></a>Integracja usługi Microsoft Defender for Endpoint

Integracja usługi Microsoft Defender for Endpoint jest teraz ogólnie dostępna. Ta funkcja umożliwia maszynom wirtualnym z systemem Windows Korzystanie z tego samego środowiska badania co na lokalnym komputerze z systemem Windows 10. Jeśli używasz wielu sesji systemu Windows 10 Enterprise, usługa Microsoft Defender dla punktów końcowych będzie obsługiwać maksymalnie 50 połączeń użytkowników współbieżnych, co zapewnia oszczędność kosztów dla wielu sesji systemu Windows 10 Enterprise i zaufanie usługi Microsoft Defender dla punktów końcowych. Aby uzyskać więcej informacji, zapoznaj się z naszym [wpisem w blogu](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linia bazowa zabezpieczeń Azure dla pulpitu wirtualnego systemu Windows

Niedawno opublikowano [artykuł dotyczący podstawy zabezpieczeń Azure](security-baseline.md) dla pulpitu wirtualnego systemu Windows, do którego chcemy zwrócić uwagę. Te wytyczne zawierają informacje dotyczące sposobu zastosowania testu porównawczego zabezpieczeń platformy Azure w wersji 2,0 do pulpitu wirtualnego systemu Windows. W ramach testu porównawczego zabezpieczeń platformy Azure opisano ustawienia i praktyki, które zalecamy wykorzystać do zabezpieczenia rozwiązań w chmurze na platformie Azure.

## <a name="december-2020"></a>Grudzień 2020 r.

Poniżej przedstawiono zmiany w grudniu 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor dla pulpitu wirtualnego systemu Windows

Publiczna wersja zapoznawcza Azure Monitor dla pulpitu wirtualnego systemu Windows jest teraz dostępna. Ta nowa funkcja obejmuje niezawodny pulpit nawigacyjny utworzony w oparciu o Azure Monitor skoroszyty, aby ułatwić specjalistom IT zrozumieć swoje środowiska pulpitu wirtualnego systemu Windows. Zapoznaj [się z ogłoszeniem w naszym blogu,](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) Aby uzyskać więcej szczegółów. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager zmianę szablonu 

W najnowszej aktualizacji usunięto wszystkie parametry publicznego adresu IP z szablonu Azure Resource Manager na potrzeby tworzenia i inicjowania obsługi administracyjnej pul hostów. Zdecydowanie zalecamy uniknięcie używania publicznych adresów IP dla pulpitu wirtualnego systemu Windows, aby zachować bezpieczeństwo wdrożenia. Jeśli Twoje wdrożenie korzysta z publicznych adresów IP, należy zmienić ich konfigurację tak, aby korzystały z prywatnych adresów IP. w przeciwnym razie wdrożenie nie będzie działało prawidłowo.

### <a name="msix-app-attach-public-preview"></a>Dołączanie publicznej wersji zapoznawczej aplikacji MSIX 

Dołączenie do aplikacji MSIX to inna usługa, która rozpoczęła swoją publiczną wersję zapoznawczą w tym miesiącu. Dołączenie do aplikacji MSIX to usługa, która dynamicznie przedstawia aplikacje MSIX na maszynach wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows. Zapoznaj [się z ogłoszeniem w naszym blogu,](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) Aby uzyskać więcej szczegółów. 

### <a name="screen-capture-protection"></a>Ochrona przechwytywania ekranu 

W tym miesiącu oznaczono również początek publicznej wersji zapoznawczej ochrony przed przechwytywaniem ekranu. Ta funkcja służy do zapobiegania przechwytywaniu poufnych informacji w punktach końcowych klienta. Spróbuj wykonać ochronę za pomocą przechwytywania ekranu, przechodząc do [tej strony](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Wbudowane role

Dodaliśmy nowe wbudowane role dla pulpitu wirtualnego systemu Windows na potrzeby uprawnień administratora. Aby uzyskać więcej informacji, zobacz [wbudowane role dla pulpitu wirtualnego systemu Windows](rbac.md). 

### <a name="application-group-limit-increase"></a>Zwiększenie limitu grupy aplikacji

Zwiększono domyślny limit grup aplikacji dla Azure Active Directory dzierżawy do grup 200.

### <a name="client-updates-for-december-2020"></a>Aktualizacje klienta z grudnia 2020

Udostępnimy nowe wersje następujących klientów: 

- Android
- macOS
- Windows

Aby uzyskać więcej informacji o aktualizacjach klientów, zobacz [aktualizacje klienta](whats-new.md#client-updates).

## <a name="november-2020"></a>Listopad 2020 r.

### <a name="azure-portal-experience"></a>Środowisko Azure Portal

Rozwiązano dwie usterki w Azure Portal środowisku użytkownika:

- Przyjazna nazwa aplikacji klasycznej nie jest już zapisywana w przepływie pracy "Dodawanie maszyny wirtualnej".
- Karta Host sesji zostanie teraz załadowana, Jeśli hosty sesji są częścią zestawów skalowania.

### <a name="fslogix-client-version-2009"></a>FSLogix Client, wersja 2009 

Firma Microsoft udostępniła nową wersję klienta FSLogix z wieloma poprawkami i ulepszeniami. Dowiedz się więcej w [naszym wpisie w blogu](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Publiczna wersja zapoznawcza RDP shortpath

Protokół RDP shortpath wprowadza bezpośrednią łączność z hostem sesji usług pulpitu wirtualnego systemu Windows przy użyciu sieci VPN typu punkt-lokacja i połączenia między lokacjami oraz ExpressRoute. Wprowadzono również protokół transportowy URCP. Shortpath RDP został zaprojektowany z myślą o skróceniu opóźnień i przeskoków sieci w celu poprawienia środowiska użytkownika. Dowiedz się więcej na temat [SHORTPATH RDP pulpitu wirtualnego systemu Windows](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>AZ. DesktopVirtualization, wersja 2.0.1

Wydano wersję 2.0.1 poleceń cmdlet pulpitu wirtualnego systemu Windows. Ta aktualizacja obejmuje polecenia cmdlet, które umożliwiają zarządzanie dołączaniem aplikacji MSIX. Nową wersję można pobrać w [Galerii programu PowerShell](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Aktualizacje Azure Advisor

Azure Advisor ma teraz nowe zalecenie dotyczące wskazówek dotyczących bliskości na pulpicie wirtualnym systemu Windows oraz nowe zalecenie dotyczące optymalizowania wydajności w pulach hostów z równoważeniem obciążenia. Dowiedz się więcej w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Październik 2020 r.

Poniżej przedstawiono zmiany w październiku 2020:

### <a name="improved-performance"></a>Większa wydajność

- Zoptymalizowano wydajność przez zmniejszenie opóźnienia połączenia w ramach następujących lokalizacje geograficzne Azure:
    - Szwajcaria
    - Kanada

Teraz możesz użyć [szacowania doświadczenia](https://azure.microsoft.com/services/virtual-desktop/assessment/) do oszacowania jakości środowiska użytkownika w tych obszarach.

### <a name="azure-government-cloud-availability"></a>Dostępność w chmurze Azure Government

Chmura Azure Government jest teraz ogólnie dostępna. Dowiedz się więcej w [naszym wpisie w blogu](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Aktualizacje Azure Portal pulpitu wirtualnego systemu Windows

Wprowadziliśmy pewne aktualizacje Azure Portal pulpitu wirtualnego systemu Windows:

- Naprawiono błąd resourceID, który uniemożliwił użytkownikom otwieranie karty "sesje".
- Ulepszony interfejs użytkownika na karcie "hosty sesji".
- Naprawiono ustawienia "wartości domyślne", "użyteczność" i "Przywróć domyślne" w obszarze właściwości protokołu RDP.
- Na wszystkich kartach wprowadzono spójne funkcje "Remove" i "Delete".
- Portal weryfikuje teraz nazwy aplikacji w przepływie pracy "Dodawanie aplikacji".
- Rozwiązano problem polegający na tym, że dane eksportowania hosta sesji nie zostały wyrównane w kolumnach.
- Rozwiązano problem polegający na tym, że portal nie może pobrać sesji użytkownika.
- Rozwiązano problem podczas pobierania hosta sesji, który wystąpił podczas tworzenia maszyny wirtualnej w innej grupie zasobów.
- Zaktualizowano kartę "Host sesji", aby wyświetlić listę sesji aktywnych i rozłączonych.
- Karta "aplikacje" ma teraz strony.
- Rozwiązano problem polegający na tym, że tekst "wymaga wiersza polecenia" został niepoprawnie wyświetlony na karcie "Lista aplikacji".
- Rozwiązano problem polegający na tym, że w portalu nie można wdrożyć pul hostów lub maszyn wirtualnych przy użyciu niemieckiej wersji językowej galerii obrazów udostępnionych.

### <a name="client-updates-for-october-2020"></a>Aktualizacje klienta z października 2020

Udostępnimy nowe wersje klientów. Zobacz następujące artykuły, aby dowiedzieć się więcej:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Aby uzyskać więcej informacji o innych klientach, zobacz [aktualizacje klientów](#client-updates).

## <a name="september-2020"></a>Wrzesień 2020

Poniżej przedstawiono zmiany we wrześniu 2020:

- Zoptymalizowano wydajność przez zmniejszenie opóźnienia połączenia w ramach następujących lokalizacje geograficzne Azure:
    - Niemcy
    - Republika Południowej Afryki (tylko w przypadku środowisk weryfikacji)

Teraz możesz użyć [szacowania doświadczenia](https://azure.microsoft.com/services/virtual-desktop/assessment/) do oszacowania jakości środowiska użytkownika w tych obszarach.

- Wydano wersję 1.2.1364 klienta klasycznego systemu Windows dla pulpitu wirtualnego systemu Windows. W tej aktualizacji wprowadziliśmy następujące zmiany:
    - Rozwiązano problem polegający na tym, że logowanie jednokrotne (SSO) nie działało w systemie Windows 7.
    - Rozwiązano problem, który spowodował, że klient rozłączył się, gdy użytkownik, który włączył optymalizację nośnika dla zespołów próbował wywołać lub dołączyć do spotkania zespołów, podczas gdy inna aplikacja ma otwarty strumień audio w trybie wyłączności.
    - Rozwiązano problem polegający na tym, że zespoły nie wyliczają urządzeń audio lub wideo, gdy została włączona Optymalizacja multimediów dla zespołów.
    - Dodano "potrzebna pomoc z ustawieniami?" link do strony ustawień pulpitu.
    - Rozwiązano problem z przyciskiem "Subskrybuj", który wystąpił podczas korzystania z ciemnych motywów o wysokim kontraście.
    
- Dzięki ogromnej pomocy od naszych użytkowników Naprawiono dwa krytyczne problemy dotyczące klienta Pulpit zdalny Microsoft Store. Będziemy nadal przeglądać Opinie i rozwiązywać problemy, ponieważ rozszerzamy nasze etapowe wersje klienta do większej liczby użytkowników na całym świecie.
    
- Dodaliśmy nową funkcję, która umożliwia zmianę lokalizacji maszyny wirtualnej, obrazu, grupy zasobów, nazwy prefiksu i konfiguracji sieci w ramach przepływu pracy w celu dodania maszyny wirtualnej do wdrożenia w Azure Portal.

- Specjaliści IT mogą teraz zarządzać hybrydowymi maszynami wirtualnymi z systemem Windows 10, które są przyłączone do Azure Active Directory w programie Microsoft Endpoint Manager. Aby dowiedzieć się więcej, zobacz [nasz wpis w blogu](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Sierpień 2020 r.

Poniżej przedstawiono zmiany w sierpniu 2020:

- Ulepszono wydajność w celu zmniejszenia opóźnień połączenia w następujących regionach świadczenia usługi Azure: 

    - Zjednoczone Królestwo
    - Francja
    - Norwegia
    - Korea Południowa

   Możesz użyć [szacowania doświadczenia](https://azure.microsoft.com/services/virtual-desktop/assessment/) , aby uzyskać ogólny pomysł na to, jak te zmiany wpłyną na użytkowników.

- Klient Pulpit zdalny Microsoft Store (v 10.2.1522 +) jest teraz ogólnie dostępny! Ta wersja klienta Microsoft Store Pulpit zdalny jest zgodna z pulpitem wirtualnym systemu Windows. Wprowadzono również odświeżone przepływy interfejsu użytkownika w celu ulepszenia środowiska użytkownika. Ta aktualizacja obejmuje tryby projektowania Fluent, jasne i ciemne oraz wiele innych atrakcyjnych zmian. Klient został również ponownie zapisany pod kątem używania tego samego aparatu protokołu RDP (Remote Desktop Protocol) jako klientów z systemem iOS, macOS i Android. Pozwala to nam dostarczać nowe funkcje z większą szybkością na wszystkich platformach. [Pobierz klienta](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) i spróbuj ponownie.

- Rozwiązano problem w programie Teams Client (wersja 1.3.00.21759), w którym klient pokazał tylko strefy czasowej UTC w rozmowie, kanałach i kalendarzu. Zaktualizowany klient wyświetla teraz strefę czasową sesji zdalnej.

- Azure Advisor jest teraz częścią pulpitu wirtualnego systemu Windows. Podczas uzyskiwania dostępu do pulpitu wirtualnego systemu Windows za pomocą Azure Portal można zobaczyć zalecenia dotyczące optymalizowania środowiska pulpitu wirtualnego systemu Windows. Więcej informacji znajduje się w [Azure Advisor](azure-advisor.md).

- Interfejs wiersza polecenia platformy Azure obsługuje teraz pulpit wirtualny systemu Windows ( `az desktopvirtualization` ), aby ułatwić automatyzację wdrożeń pulpitów wirtualnych systemu Windows. Zapoznaj się z [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true) , aby uzyskać listę poleceń rozszerzenia.

- Zaktualizowaliśmy szablony wdrażania, aby zapewnić ich pełną zgodność z interfejsami Azure Resource Manager pulpitu wirtualnego systemu Windows. Szablony można znaleźć w witrynie [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Portal US Gov pulpitu wirtualnego systemu Windows jest teraz dostępny w publicznej wersji zapoznawczej. Aby dowiedzieć się więcej, zapoznaj się z [naszym ogłoszeniem](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Lipiec 2020 r.  

W lipcu była dostępna Ogólna dostępność usług pulpitu wirtualnego systemu Windows z integracją usługi Azure Resource Management.

Poniżej przedstawiono zmiany w nowej wersji: 

- Wartość "Jesień 2019 Release" jest teraz znana jako "pulpit wirtualny systemu Windows (klasyczny)", podczas gdy "2020 sprężynowe wydanie" jest teraz tylko "pulpitem wirtualnym systemu Windows". Aby uzyskać więcej informacji, zapoznaj się z [wpisem w blogu](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Aby dowiedzieć się więcej o nowych funkcjach, zapoznaj się z [wpisem w blogu](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aktualizacja narzędzia skalowania automatycznego

Najnowsza wersja narzędzia skalowania automatycznego, która była w wersji zapoznawczej, jest teraz ogólnie dostępna. To narzędzie używa konta usługi Azure Automation i aplikacji logiki platformy Azure, aby automatycznie zamykać i ponownie uruchamiać maszyny wirtualne hosta sesji (VM) w puli hostów, zmniejszając koszty związane z infrastrukturą. Dowiedz się więcej na temat [skalowania hostów sesji przy użyciu Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Teraz można wykonać następujące czynności w Azure Portal na pulpicie wirtualnym systemu Windows: 

- Bezpośrednie przypisywanie użytkowników do osobistych hostów sesji usług pulpitu  
- Zmień ustawienie środowiska weryfikacji dla pul hostów 

### <a name="diagnostics"></a>Diagnostyka

Opublikowano kilka nowych wstępnie skompilowanych zapytań dla obszaru roboczego Log Analytics. Aby uzyskać dostęp do zapytań, przejdź do pozycji **dzienniki** i w obszarze **kategoria** wybierz pozycję **pulpit wirtualny systemu Windows**. Dowiedz się więcej na temat [używania funkcji diagnostyki log Analytics](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Aktualizacja klienta Pulpit zdalny dla systemu Android

[Klient pulpit zdalny dla systemu Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) obsługuje teraz połączenia pulpitu wirtualnego systemu Windows. Począwszy od wersji 10.0.7, klient systemu Android jest wyposażony w nowy interfejs użytkownika w celu ulepszenia środowiska użytkownika. Klient integruje się również z Microsoft Authenticator na urządzeniach z systemem Android w celu włączenia dostępu warunkowego podczas subskrybowania obszarów roboczych pulpitu wirtualnego systemu Windows.  

Poprzednia wersja Pulpit zdalny Client jest teraz nazywana "Pulpit zdalny 8". Wszystkie istniejące połączenia w starszej wersji klienta zostaną bezproblemowo przeniesione do nowego klienta. Nowy klient został ponownie zapisany w tym samym podstawowym aparacie protokołu RDP jako klienci z systemem iOS i macOS, co pozwala na szybsze wydanie nowych funkcji na wszystkich platformach. 

### <a name="teams-update"></a>Aktualizacja zespołów

Wprowadziliśmy ulepszenia usługi Microsoft Teams dla pulpitu wirtualnego systemu Windows. Co najważniejsze, pulpit wirtualny systemu Windows obsługuje teraz optymalizację audio i wideo dla klienta klasycznego systemu Windows. Przekierowanie zwiększa czas oczekiwania przez tworzenie bezpośrednich ścieżek między użytkownikami, gdy używają dźwięku lub wideo w wywołaniach i spotkaniach. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i wygładzają dźwięk. Dowiedz się więcej na [temat używania zespołów na pulpicie wirtualnym systemu Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Czerwiec 2020 r.

W ciągu ostatniego miesiąca wprowadziliśmy pulpit wirtualny systemu Windows z integracją Azure Resource Manager w wersji zapoznawczej. Ta aktualizacja ma wiele ciekawych nowych funkcji, które chcielibyśmy poznać. W tym miejscu nowości nowe dla tej wersji pulpitu wirtualnego systemu Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Pulpit wirtualny systemu Windows jest teraz zintegrowany z usługą Azure Resource Manager

Pulpit wirtualny systemu Windows jest teraz zintegrowany z usługą Azure Resource Manager. W najnowszej aktualizacji wszystkie obiekty pulpitu wirtualnego systemu Windows są teraz Azure Resource Manager zasoby. Ta aktualizacja jest również zintegrowana z kontrolą dostępu opartą na rolach (Azure RBAC). Zobacz [co to jest Azure Resource Manager?](../azure-resource-manager/management/overview.md) , aby dowiedzieć się więcej.

Oto, co to jest ta zmiana:

- Pulpit wirtualny systemu Windows jest teraz zintegrowany z Azure Portal. Oznacza to, że możesz zarządzać wszystko bezpośrednio w portalu, nie musisz mieć wymaganych narzędzi PowerShell, Web Apps ani innych firm. Aby rozpocząć, zapoznaj się z naszym samouczkiem w witrynie [Tworzenie puli hostów za pomocą Azure Portal](create-host-pools-azure-marketplace.md).

- Przed tą aktualizacją można publikować tylko usługi RemoteApp i pulpity dla poszczególnych użytkowników. Za pomocą Azure Resource Manager można teraz publikować zasoby w Azure Active Directory grupach.

- Starsza wersja pulpitu wirtualnego systemu Windows ma cztery wbudowane role administracyjne, które można przypisać do dzierżawy lub puli hostów. Role te działają teraz w [kontroli dostępu opartej na rolach platformy Azure (Azure RBAC)](../role-based-access-control/overview.md). Te role można zastosować do każdego obiektu Azure Resource Manager pulpitu wirtualnego systemu Windows, który umożliwia posiadanie pełnego, rozbudowanego modelu delegowania.

- W ramach tej aktualizacji nie jest już konieczne wielokrotne uruchamianie szablonu Azure Marketplace lub GitHub, aby rozszerzyć pulę hostów. Wystarczy rozszerzyć pulę hostów, aby przejść do puli hostów w Azure Portal i wybrać pozycję **+ Dodaj** , aby wdrożyć dodatkowe hosty sesji.

- Wdrożenie puli hostów jest obecnie w pełni zintegrowane z [galerią udostępnionych obrazów systemu Azure](../virtual-machines/shared-image-galleries.md). Galeria obrazów udostępnionych to oddzielna usługa platformy Azure, która przechowuje definicje obrazów maszyn wirtualnych (VM), w tym przechowywanie wersji obrazu. Można również użyć replikacji globalnej do kopiowania i wysyłania obrazów do innych regionów świadczenia usługi Azure na potrzeby lokalnego wdrożenia.

- Funkcje monitorowania, które zostały użyte do wykonania za pomocą programu PowerShell lub aplikacji internetowej usługi diagnostycznej, zostały przeniesione do Log Analytics w Azure Portal. Dostępne są również dwie opcje wizualizacji raportów. Można uruchamiać zapytania Kusto i używać skoroszytów do tworzenia raportów wizualnych.

- Nie musisz już wykonywać Azure Active Directory (Azure AD), aby wyrazić zgodę na korzystanie z pulpitu wirtualnego systemu Windows. W tej aktualizacji dzierżawa usługi Azure AD w ramach subskrypcji platformy Azure uwierzytelnia użytkowników i udostępnia kontrolki kontroli RBAC platformy Azure dla administratorów.

### <a name="powershell-support"></a>Obsługa programu PowerShell

Dodaliśmy nowe polecenia cmdlet AzWvd do Azure PowerShell AZ module z tą aktualizacją. Ten nowy moduł jest obsługiwany w programie PowerShell Core, który działa na platformie .NET Core.

Aby zainstalować moduł, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie modułu programu PowerShell dla pulpitu wirtualnego systemu Windows](powershell-module.md).

Listę dostępnych poleceń można także znaleźć w [dokumentacji programu PowerShell AzWvd](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true).

Aby uzyskać więcej informacji o nowych funkcjach, zapoznaj się z [naszym wpisem w blogu](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Dodatkowe bramy

Dodaliśmy nowy klaster bramy w Republice Południowej Afryki, aby zmniejszyć opóźnienie połączenia.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na pulpicie wirtualnym systemu Windows (wersja zapoznawcza)

Wprowadziliśmy kilka ulepszeń usługi Microsoft Teams dla systemu Windows Virtual Desktop. Co najważniejsze, pulpit wirtualny systemu Windows obsługuje teraz funkcję przekierowywania audio i wizualizacji dla wywołań. Przekierowanie zwiększa czas oczekiwania przez tworzenie bezpośrednich ścieżek między użytkownikami podczas wywoływania przy użyciu dźwięku lub wideo. Mniejsza odległość oznacza mniejszą liczbę przeskoków, co sprawia, że wywołania wyglądają i wygładzają dźwięk.

Aby dowiedzieć się więcej, zobacz [nasz wpis w blogu](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z przyszłymi planami w [Microsoft 365 Windows Virtual Desktop plan](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
