---
title: Najlepsze rozwiązania w zakresie zabezpieczeń pulpitu wirtualnego systemu Windows — Azure
description: Najlepsze rozwiązania związane z utrzymywaniem bezpieczeństwa środowiska pulpitu wirtualnego systemu Windows.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0ddbd4b798d37498af92cec40af6a80a88115fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014897"
---
# <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Pulpit wirtualny systemu Windows to zarządzana usługa pulpitu wirtualnego, która zapewnia wiele możliwości zabezpieczania organizacji. W ramach wdrożenia pulpitu wirtualnego systemu Windows firma Microsoft zarządza częścią usług w imieniu klienta. Usługa ma wiele wbudowanych zaawansowanych funkcji zabezpieczeń, takich jak łączenie zwrotne, co zmniejsza ryzyko związane z udostępnianiem pulpitów zdalnych z dowolnego miejsca.

W tym artykule opisano dodatkowe czynności, które można wykonać jako administrator, aby zapewnić bezpieczeństwo wdrożeń pulpitów wirtualnych systemu Windows dla klientów.

## <a name="security-responsibilities"></a>Obowiązki związane z zabezpieczeniami

Co sprawia, że usługi w chmurze różnią się od tradycyjnych lokalnych infrastruktury pulpitu wirtualnego (VDIs), jak obsługują obowiązki związane z zabezpieczeniami. Na przykład w tradycyjnym lokalnym infrastrukturze VDI klient jest odpowiedzialny za wszystkie aspekty zabezpieczeń. Jednak w większości usług w chmurze te obowiązki są współużytkowane przez klienta i firmę.

W przypadku korzystania z pulpitu wirtualnego systemu Windows ważne jest, aby zrozumieć, że w przypadku, gdy niektóre składniki są już zabezpieczone w danym środowisku, należy skonfigurować inne obszary w taki sposób, aby odpowiadały potrzebom bezpieczeństwa organizacji.

Poniżej przedstawiono wymagania dotyczące zabezpieczeń, które są odpowiedzialne za wdrożenie pulpitu wirtualnego systemu Windows:

| Konieczność zabezpieczeń | Czy klient jest odpowiedzialny za to? |
|---------------|:-------------------------:|
|Tożsamość|Tak|
|Urządzenia użytkowników (aplikacje mobilne i komputery)|Tak|
|Zabezpieczenia aplikacji|Tak|
|System operacyjny hosta sesji|Tak|
|Konfiguracja wdrożenia|Tak|
|Funkcje kontrolne sieci|Tak|
|Płaszczyzna kontroli wirtualizacji|Nie|
|Hosty fizyczne|Nie|
|Sieć fizyczna|Nie|
|Fizyczne centrum danych|Nie|

Wymagania dotyczące zabezpieczeń, dla których klient nie odpowiada, są obsługiwane przez firmę Microsoft.

## <a name="azure-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń platformy Azure

Windows Virtual Desktop to usługa na platformie Azure. Aby zmaksymalizować bezpieczeństwo wdrożenia pulpitu wirtualnego systemu Windows, należy upewnić się, że istnieje również ochrona otaczającej infrastruktury platformy Azure i płaszczyzny zarządzania. Aby zabezpieczyć infrastrukturę, należy rozważyć sposób, w jaki pulpit wirtualny systemu Windows mieści się w większym ekosystemie platformy Azure. Aby dowiedzieć się więcej na temat ekosystemu platformy Azure, zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](../security/fundamentals/best-practices-and-patterns.md).

W tej sekcji opisano najlepsze rozwiązania dotyczące zabezpieczania ekosystemu platformy Azure.

### <a name="enable-azure-security-center"></a>Włącz Azure Security Center

Zalecamy włączenie Azure Security Center Standard dla subskrypcji, maszyn wirtualnych, magazynów kluczy i kont magazynu.

Za pomocą Azure Security Center Standard można:

* Zarządzaj lukami w zabezpieczeniach.
* Oceń zgodność ze wspólnymi platformami, takimi jak PCI.
* Wzmocnienie całkowitego bezpieczeństwa środowiska.

Aby dowiedzieć się więcej, zobacz Dołączanie [subskrypcji platformy Azure do usługi Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Poprawianie wskaźnika bezpieczeństwa

Metoda Secure Score zawiera zalecenia i najlepsze rozwiązanie w zakresie poprawy ogólnego bezpieczeństwa. Zalecenia te mają na celu ułatwienie wyboru, które najważniejsze są najważniejsze, a opcje szybkiej naprawy ułatwiają szybkie rozwiązywanie potencjalnych luk w zabezpieczeniach. Te zalecenia również są aktualizowane w miarę upływu czasu, dzięki czemu można uzyskać aktualną obsługę bezpieczeństwa środowiska. Aby dowiedzieć się więcej, zobacz [ulepszanie bezpiecznego oceny w Azure Security Center](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Najlepsze rozwiązania w zakresie zabezpieczeń pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows ma wiele wbudowanych formantów zabezpieczeń. Ta sekcja zawiera informacje o kontrolkach zabezpieczeń, których można użyć w celu zapewnienia bezpieczeństwa użytkowników i danych.

### <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników i administratorów w programie Virtual Desktop systemu Windows zwiększa bezpieczeństwo całego wdrożenia. Aby dowiedzieć się więcej, zobacz [Włączanie usługi Azure AD Multi-Factor Authentication dla pulpitu wirtualnego systemu Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Włącz dostęp warunkowy

Włączenie [dostępu warunkowego](../active-directory/conditional-access/overview.md) umożliwia zarządzanie ryzykiem przed udzieleniem użytkownikom dostępu do środowiska pulpitu wirtualnego systemu Windows. W przypadku podejmowania decyzji o tym, którzy użytkownicy mają udzielić dostępu, zalecamy także rozważenie, kto to użytkownik, w jaki sposób loguje się i które urządzenie używa.

### <a name="collect-audit-logs"></a>Zbierz dzienniki inspekcji

Włączenie zbierania dzienników inspekcji umożliwia wyświetlenie aktywności użytkowników i administratorów związanych z pulpitem wirtualnym systemu Windows. Przykłady dzienników inspekcji kluczy są następujące:

-   [Dziennik aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory dziennik aktywności](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Hosty sesji](../azure-monitor/agents/agent-windows.md)
-   [Dziennik diagnostyczny pulpitu wirtualnego systemu Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Dzienniki Key Vault](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Korzystanie z usługi RemoteApp

Wybierając model wdrażania, można udostępnić użytkownikom zdalnym dostęp do całych pulpitów wirtualnych lub tylko do wybranych aplikacji. Aplikacje zdalne i usługi RemoteApp zapewniają bezproblemowe środowisko pracy z aplikacjami na pulpicie wirtualnym. Usługi RemoteApp zmniejszają ryzyko, umożliwiając użytkownikowi współdziałanie z podzbiorem maszyny zdalnej uwidocznionej przez aplikację.

### <a name="monitor-usage-with-azure-monitor"></a>Monitoruj użycie przy użyciu Azure Monitor

Monitoruj użycie i dostępność usługi pulpitu wirtualnego systemu Windows za pomocą [Azure monitor](https://azure.microsoft.com/services/monitor/). Należy rozważyć utworzenie [alertów kondycji usługi](../service-health/alerts-activity-log-service-notifications-portal.md) dla usługi pulpitów wirtualnych systemu Windows w celu otrzymywania powiadomień w przypadku wystąpienia zdarzenia wpływającego na usługę.

## <a name="session-host-security-best-practices"></a>Najlepsze rozwiązania w zakresie zabezpieczeń hosta sesji

Hosty sesji są maszynami wirtualnymi, które działają w ramach subskrypcji platformy Azure i sieci wirtualnej. Ogólne zabezpieczenia wdrożenia pulpitu wirtualnego systemu Windows są zależne od kontroli zabezpieczeń, które są umieszczane na hostach sesji. W tej sekcji opisano najlepsze rozwiązania dotyczące zabezpieczania hostów sesji.

### <a name="enable-screen-capture-protection-preview"></a>Włącz ochronę przechwytywania ekranu (wersja zapoznawcza)

Funkcja ochrony przechwytywania ekranu uniemożliwia przechwycenie poufnych informacji w punktach końcowych klienta. Po włączeniu tej funkcji zawartość zdalna zostanie automatycznie zablokowana lub ukryta na zrzutach ekranu i w udziałach ekranów. Będzie on również ukryty przed złośliwym oprogramowaniem, które może stale przechwytywać zawartość ekranu. Zalecamy wyłączenie przekierowania schowka, aby zapobiec kopiowaniu zdalnej zawartości do punktów końcowych podczas korzystania z tej funkcji.

Te zasady są wymuszane na poziomie hosta przez skonfigurowanie klucza rejestru. Aby włączyć te zasady, Otwórz program PowerShell i Ustaw klucz rejestru **fEnableScreenCaptureProtection** , uruchamiając to polecenie cmdlet:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Aby przetestować tę nową funkcję:

- Upewnij się, że pule hostów są obsługiwane w środowisku walidacji.
- Upewnij się, że pobrano i zainstalowano klienta klasycznego systemu Windows w wersji 1.2.1526 lub nowszej.

>[!NOTE]
>W trakcie okresu zapoznawczego ta funkcja obsługuje tylko pełne połączenia pulpitu z punktów końcowych systemu Windows 10.

### <a name="enable-endpoint-protection"></a>Włącz program Endpoint Protection

Aby chronić wdrożenie przed znanym złośliwym oprogramowaniem, zalecamy włączenie programu Endpoint Protection na wszystkich hostach sesji. Można użyć programu antywirusowego Windows Defender lub program innej firmy. Aby dowiedzieć się więcej, zobacz [Przewodnik wdrażania programu antywirusowego Windows Defender w środowisku infrastruktury VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

W przypadku rozwiązań profilu, takich jak FSLogix lub inne rozwiązania instalujące pliki VHD, zalecamy wykluczenie rozszerzeń plików VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instalowanie produktu wykrywania i reagowania punktu końcowego

Zalecamy zainstalowanie produktu wykrywania i reagowania punktów końcowych (EDR), aby zapewnić zaawansowane możliwości wykrywania i reagowania. W przypadku systemów operacyjnych serwera z włączonym [Azure Security Centerm](../security-center/security-center-services.md) zainstalowanie produktu EDR spowoduje wdrożenie usługi Defender ATP. W przypadku systemów operacyjnych klienta można wdrożyć usługę [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) lub produkt innej firmy dla tych punktów końcowych.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Włącz oceny zagrożeń i luk w zabezpieczeniach

Zidentyfikowanie luk w zabezpieczeniach systemów operacyjnych i aplikacji ma kluczowe znaczenie dla zapewnienia bezpieczeństwa środowiska. Azure Security Center może ułatwić identyfikację problemów za pomocą ocen luk w zabezpieczeniach systemów operacyjnych serwera. Można również użyć usługi Defender ATP, która zapewnia zarządzanie zagrożeniami i lukami w systemach operacyjnych dla komputerów stacjonarnych. Możesz również użyć produktów innych firm, jeśli jest to nachylone, chociaż zalecamy korzystanie z usługi Azure Security Center i usługi Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Poprawianie luk w zabezpieczeniach oprogramowania w środowisku

Po zidentyfikowaniu luki w zabezpieczeniach należy ją zastosować. Dotyczy to również środowisk wirtualnych, które obejmują uruchomione systemy operacyjne, wdrożone w nich aplikacje, a także obrazy, z których tworzone są nowe maszyny. Postępuj zgodnie z informacjami o poprawkach dla dostawcy i stosuj poprawki w odpowiednim czasie. Zalecamy stosowanie poprawek do obrazów podstawowych w celu zapewnienia, że nowo wdrożone maszyny są tak bezpieczne, jak to możliwe.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Ustanawianie maksymalnych zasad czasu nieaktywności i rozłączania

Wyrejestrowywanie użytkowników, gdy są nieaktywne, zachowuje zasoby i uniemożliwia dostęp przez nieautoryzowanych użytkowników. Zalecamy, aby przekroczenie limitu czasu zwiększać produktywność użytkowników oraz użycie zasobów. W przypadku użytkowników, którzy współpracują z aplikacjami bezstanowymi, należy rozważyć bardziej agresywne zasady, które wyłączają maszyny i zachowują zasoby Odłączanie długotrwałych aplikacji, które nadal działają, jeśli użytkownik jest bezczynny, na przykład symulacja lub renderowanie CAD, może przerwać pracę użytkownika i może nawet wymagać ponownego uruchomienia komputera.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Konfigurowanie blokad ekranu dla bezczynnych sesji

Można zapobiec niepożądanemu dostępowi do systemu przez skonfigurowanie pulpitu wirtualnego systemu Windows do blokowania ekranu komputera w czasie bezczynności i wymaganie uwierzytelnienia do odblokowania.

### <a name="establish-tiered-admin-access"></a>Ustanów dostęp administratora warstwowego

Zalecamy, aby nie przyznawać użytkownikom dostępu administratora do pulpitów wirtualnych. Jeśli potrzebujesz pakietów oprogramowania, zalecamy udostępnienie ich za poorednictwem narzędzi do zarządzania konfiguracją, takich jak Microsoft Endpoint Manager. W środowisku wielu sesji zalecamy, aby nie zezwalać użytkownikom na bezpośrednie Instalowanie oprogramowania.

### <a name="consider-which-users-should-access-which-resources"></a>Zastanów się, którzy użytkownicy powinni uzyskiwać dostęp do zasobów

Należy rozważyć hosty sesji jako rozszerzenie istniejącego wdrożenia pulpitu. Zalecamy kontrolowanie dostępu do zasobów sieciowych w taki sam sposób jak w przypadku innych komputerów stacjonarnych w środowisku, takich jak używanie segmentacji sieci i filtrowanie. Domyślnie hosty sesji mogą łączyć się z dowolnym zasobem w Internecie. Istnieje kilka sposobów ograniczania ruchu, w tym Używanie zapory platformy Azure, sieciowych urządzeń wirtualnych lub serwerów proxy. Jeśli musisz ograniczyć ruch, upewnij się, że dodano odpowiednie reguły, aby pulpit wirtualny systemu Windows mógł prawidłowo funkcjonować.

### <a name="manage-office-pro-plus-security"></a>Zarządzanie zabezpieczeniami pakietu Office Pro Plus

Oprócz zabezpieczania hostów sesji należy również zabezpieczyć aplikacje działające w nich. Pakiet Office Pro Plus jest jednym z najpopularniejszych aplikacji wdrożonych na hostach sesji. Aby ulepszyć zabezpieczenia wdrażania pakietu Office, zalecamy korzystanie z [doradcy zasad zabezpieczeń](/DeployOffice/overview-of-security-policy-advisor) dla Microsoft 365 aplikacji dla przedsiębiorstw. To narzędzie identyfikuje zasady, które można zastosować do wdrożenia, aby zwiększyć bezpieczeństwo. Doradca zasad zabezpieczeń zaleca również stosowanie zasad na podstawie ich wpływu na bezpieczeństwo i wydajność.

### <a name="other-security-tips-for-session-hosts"></a>Inne porady dotyczące zabezpieczeń dla hostów sesji

Ograniczając możliwości systemu operacyjnego, można wzmocnić zabezpieczenia hostów sesji. Oto kilka rzeczy, które możesz wykonać:

- Sterowanie przekierowaniem urządzeń przez przekierowanie dysków, drukarek i urządzeń USB na urządzenie lokalne użytkownika w sesji pulpitu zdalnego. Zalecamy oszacowanie wymagań dotyczących zabezpieczeń i sprawdzenie, czy te funkcje powinny być wyłączone.

- Ogranicz dostęp do Eksploratora Windows, ukrywając mapowania dysków lokalnych i zdalnych. Zapobiega to odnajdywaniu przez użytkowników niepożądanych informacji o konfiguracji systemu i użytkownikach.

- Unikaj bezpośredniego dostępu RDP do hostów sesji w danym środowisku. Jeśli potrzebujesz bezpośredniego dostępu RDP do celów administracyjnych lub rozwiązywania problemów, Włącz dostęp [just in Time](../security-center/security-center-just-in-time.md) , aby ograniczyć potencjalną podatność na ataki na hoście sesji.

- Przyznaj użytkownikom ograniczone uprawnienia, gdy uzyskują dostęp do lokalnych i zdalnych systemów plików. Można ograniczyć uprawnienia, upewniając się, że lokalne i zdalne systemy plików korzystają z list kontroli dostępu z najniższymi uprawnieniami. Dzięki temu użytkownicy mogą uzyskiwać dostęp tylko do potrzebnych informacji i nie mogą zmieniać ani usuwać zasobów o znaczeniu krytycznym.

- Zapobiegaj uruchamianiu niechcianego oprogramowania na hostach sesji. Można włączyć blokadę aplikacji w celu zapewnienia dodatkowych zabezpieczeń na hostach sesji, dzięki czemu można uruchamiać tylko te aplikacje, które są dozwolone na hoście.

## <a name="windows-virtual-desktop-support-for-trusted-launch"></a>Obsługa pulpitu wirtualnego systemu Windows na potrzeby zaufanego uruchamiania

Zaufane uruchomienia to Gen2 maszyny wirtualne platformy Azure z ulepszonymi funkcjami zabezpieczeń, które mają na celu ochronę przed "dolnymi zagrożeniami stosu" przy użyciu wektorów ataków, takich jak rootkits, zestawy rozruchowe i złośliwe oprogramowanie na poziomie jądra. Poniżej przedstawiono ulepszone funkcje zabezpieczeń zaufanego uruchamiania, z których wszystkie są obsługiwane w systemie Windows Virtual Desktop. Aby dowiedzieć się więcej o zaufanych uruchomieniach, odwiedź stronę [Zaufane uruchomienia dla maszyn wirtualnych platformy Azure (wersja zapoznawcza)](../virtual-machines/trusted-launch.md).

### <a name="secure-boot"></a>Bezpieczny rozruch

Bezpieczny rozruch jest trybem obsługiwanym przez oprogramowanie układowe platformy, które chroni oprogramowanie układowe przed rootkits i rozruchem opartym na złośliwym oprogramowaniu. Ten tryb umożliwia tylko podpisane systemów operacyjnych i sterowniki do uruchomienia maszyny. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>Monitoruj integralność rozruchową przy użyciu zaświadczania zdalnego

Zaświadczanie zdalne to doskonały sposób na sprawdzenie kondycji maszyn wirtualnych. Zaświadczanie zdalne weryfikuje, że mierzone rekordy rozruchowe są obecne, autentyczne i pochodzące z Trusted Platform Module wirtualnej (vTPM). W ramach kontroli kondycji zapewnia ona pewność kryptograficzną, że platforma została prawidłowo uruchomiona. 

### <a name="vtpm"></a>vTPM

VTPM to zwirtualizowana wersja Trusted Platform Module sprzętowego (TPM) z wystąpieniem wirtualnym modułu TPM na MASZYNę wirtualną. vTPM umożliwia zdalne zaświadczanie przez wykonywanie pomiarów integralności całego łańcucha rozruchowego maszyny wirtualnej (UEFI, system operacyjny, system i sterowniki). 

Zalecamy włączenie vTPM na korzystanie z zdalnego zaświadczania na maszynach wirtualnych. Po włączeniu funkcji vTPM można także włączyć funkcję BitLocker, która zapewnia szyfrowanie pełnego woluminu w celu ochrony danych przechowywanych w spoczynku. Wszystkie funkcje korzystające z programu vTPM będą powodować wpisy tajne powiązane z określoną maszyną wirtualną. Gdy użytkownicy łączą się z usługą pulpitu wirtualnego systemu Windows w scenariuszu w puli, użytkownicy mogą być przekierowywani do dowolnej maszyny wirtualnej w puli hostów. W zależności od tego, jak ta funkcja jest zaprojektowana, może mieć to wpływ.

>[!NOTE]
>Funkcji BitLocker nie należy używać do szyfrowania określonego dysku, na którym są przechowywane dane profilu FSLogix.

### <a name="virtualization-based-security"></a>Zabezpieczenia oparte na wirtualizacji

Zabezpieczenia oparte na wirtualizacji (VBS) używają funkcji hypervisor do tworzenia i izolowania bezpiecznego regionu pamięci, która jest niedostępna dla systemu operacyjnego. Hypervisor-Protected integralność kodu (zasady wymagające WYMUSZONEJ) i Windows Defender Credential Guard używają narzędzia VBS do zapewnienia zwiększonej ochrony przed lukami w zabezpieczeniach. 

#### <a name="hypervisor-protected-code-integrity"></a>Hypervisor-Protected integralność kodu

ZASADY wymagające WYMUSZONEJ to zaawansowane środki zaradcze, które używają VBS do ochrony procesów trybu jądra systemu Windows przed iniekcją i wykonywaniem złośliwych lub niezweryfikowanych kodów.

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Funkcja Windows Defender Credential Guard używa funkcji VBS do izolowania i ochrony kluczy tajnych, dzięki czemu tylko uprzywilejowane oprogramowanie systemowe może uzyskiwać do nich dostęp. Zapobiega to nieautoryzowanemu dostępowi do tych kluczy tajnych i ataków kradzieży poświadczeń, takich jak ataki typu Pass-the-hash.

### <a name="deploy-trusted-launch-in-your-windows-virtual-desktop-environment"></a>Wdrażanie zaufanego uruchomienia w środowisku pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows nie obsługuje obecnie automatycznego konfigurowania zaufanych uruchomień podczas procesu instalacji puli hostów. Aby można było korzystać z zaufanego uruchomienia w środowisku pulpitu wirtualnego systemu Windows, należy wdrożyć zaufane uruchomienia normalnie, a następnie ręcznie dodać maszynę wirtualną do żądanej puli hostów.

## <a name="nested-virtualization"></a>Zagnieżdżone wirtualizacji

Następujące systemy operacyjne obsługują uruchomioną wirtualizację zagnieżdżoną na pulpicie wirtualnym systemu Windows:

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Wielosesyjna obsługa systemu Windows 10 Enterprise.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć uwierzytelnianie wieloskładnikowe, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego](set-up-mfa.md).
