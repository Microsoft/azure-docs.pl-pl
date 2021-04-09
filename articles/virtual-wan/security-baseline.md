---
title: Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN
description: Podstawowa linia bazowa sieci WAN zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726737"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN

Ta linia bazowa zabezpieczeń stosuje wskazówki z [usługi Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure wirtualnej sieci WAN. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące wirtualnej sieci WAN. **Formanty** nie mają zastosowania do wirtualnej sieci WAN zostały wykluczone.

Aby sprawdzić, jak wirtualne sieci WAN są całkowicie mapowane na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń sieci WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: Microsoft Azure wirtualna sieć WAN oferuje niestandardowe możliwości routingu i oferuje szyfrowanie dla ruchu ExpressRoute. Wszystkie Zarządzanie trasami jest dostarczane przez router koncentratora wirtualnego, co umożliwia także łączność między sieciami wirtualnymi. Szyfrowanie ruchu ExpressRoute za pomocą wirtualnej sieci WAN zapewnia zaszyfrowane przechodzenie między sieciami lokalnymi i sieciami wirtualnymi platformy Azure przez ExpressRoute, bez przechodzenia przez publiczny Internet ani publicznych adresów IP. 

- [Szyfrowanie ruchu ExpressRoute](virtual-wan-about.md#encryption)

- [Korzystanie z prywatnego linku w wirtualnej sieci WAN](howto-private-link.md)

- [Niestandardowe scenariusze routingu](scenario-any-to-any.md)

- [Dokumentacja tabeli tras niestandardowych](how-to-virtual-hub-routing.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem 

**Wskazówki**: Microsoft Azure ExpressRoute oferuje prywatną łączność z wirtualną siecią WAN platformy Azure. Ponieważ połączenia ExpressRoute nie przechodzą przez publiczny Internet, ExpressRoute oferuje większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. Wirtualna sieć prywatna może również służyć do łączenia się z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja (S2S) lub sieci VPN typu punkt-lokacja (P2S).

- [ExpressRoute w wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)

- [Omówienie sieci VPN między lokacjami](virtual-wan-site-to-site-portal.md)

- [Omówienie połączeń sieci VPN między lokacjami](virtual-wan-point-to-site-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: wirtualna sieć WAN nie ujawnia żadnych punktów końcowych sieci zewnętrznych, które wymagają ich zabezpieczenia przy użyciu konwencjonalnej ochrony sieci. Możesz chronić zasoby w sieciach wirtualnych szprych (dowolna Sieć wirtualna połączona z koncentratorem wirtualnym) przy użyciu usług ochrony sieci wirtualnej. 

Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. 

Wybierz DDoS Protection udostępnione przez platformę Azure, aby chronić swoje zasoby przed atakami w sieci wirtualnych platformy Azure. Użyj Azure Security Center, aby wykryć niepowodowane konfiguracje związane z zasobami związanymi z siecią.

- [Dokumentacja zapory platformy Azure](../firewall/index.yml)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: wirtualna sieć WAN to usługa zarządzana przez firmę Microsoft. Nie oferuje natywnych możliwości wykrywania intruzów ani zapobiegania włamaniom. Dostępne są jednak funkcje zabezpieczeń dostępne dla wirtualnej sieci WAN za pomocą zapory platformy Azure, aby umożliwić jednolity punkt kontroli zasad. Można utworzyć zasady zapory platformy Azure i połączyć je z wirtualnym koncentratorem sieci WAN, aby zezwolić istniejącemu wirtualnemu koncentratorowi platformy Azure na działanie jako zabezpieczonego koncentratora wirtualnego, z uwzględnieniem wymaganych zasobów zapory.

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Uprość reguły zabezpieczeń sieci, wykorzystując Virtual Network Tagi usługi do definiowania kontroli dostępu do sieci w grupach zabezpieczeń sieci lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi w polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Dokumentacja zapory platformy Azure](../firewall/index.yml)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: bezpieczna usługa nazw domen (DNS)

**Wskazówki**: bezpieczne funkcje systemu DNS są udostępniane wirtualnej sieci WAN przy użyciu zapory platformy Azure. Skonfiguruj zaporę platformy Azure, która będzie działać jako serwer proxy DNS, który jest pośrednikiem dla żądań DNS od maszyn wirtualnych klienta do serwera DNS. W przypadku niestandardowych konfiguracji serwera DNS Włącz serwer proxy DNS, aby uniknąć niezgodności rozpoznawania nazw DNS, i Włącz w pełni kwalifikowane filtrowanie nazw domen w regułach sieci. 

- [Dokumentacja zapory platformy Azure](../firewall/index.yml)

- [Ustawienia usługi DNS zapory platformy Azure](../firewall/dns-settings.md)

- [Używanie zapory platformy Azure jako usługi przesyłania dalej DNS z linkiem prywatnym](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem dla usług platformy Azure. łącznie z wirtualną siecią WAN. Normalizuj usługę Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machines (Linux i Windows), Azure Key Vault, platforma jako usługa (PaaS) i oprogramowanie jako usługa (SaaS).
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej

Zabezpiecz usługę Azure AD jako wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Oceń swoją tożsamość i stan zabezpieczeń dzięki funkcji oceny zabezpieczeń z Security Center, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań firmy Microsoft. W razie potrzeby Zaimplementuj zalecenia dotyczące najlepszych rozwiązań firmy Microsoft dotyczące ulepszeń stan zabezpieczeń.

Usługa Azure AD obsługuje również tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej. 

Zapoznaj się z informacjami dotyczącymi korzystania z usługi Azure AD w scenariuszach połączeń VPN typu punkt-lokacja w przywoływanych linkach.

- [Utwórz dzierżawę usługi Azure Active Directory (AD) dla połączeń protokołu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurowanie uwierzytelniania Azure Active Directory dla sieci VPN użytkownika](virtual-wan-point-to-site-azure-ad.md)

- [Dzierżawa w usłudze Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: obecnie uwierzytelnianie Azure Active Directory (Azure AD) jest zapewniane przez integrację z wirtualną siecią VPN typu punkt-lokacja.

Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem dla usług platformy Azure. Usługa Azure AD obsługuje kontrole silnego uwierzytelniania z uwierzytelnianiem wieloskładnikowym i silnymi metodami bez hasła.

W przypadku kontroli silnego uwierzytelniania usługa Azure AD zaleca następujące czynności:

- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w Azure Security Center z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń. Wymuś uwierzytelnianie wieloskładnikowe na wszystkich, wybierz opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka

- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła. Należą do nich:, Windows Hello dla firm, Microsoft Authenticator App i lokalne metody uwierzytelniania, takie jak karty inteligentne

Upewnij się, że najwyższego poziomu metody silnego uwierzytelniania są używane na potrzeby użytkowników z uprawnieniami administratora i uprzywilejowanego, a następnie przechodzące zasady silnego uwierzytelniania do innych użytkowników.

- [Jak włączyć usługę MFA w sieci VPN P2S dla wirtualnej sieci WAN](openvpn-azure-ad-mfa.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) dla użytkowników sieci VPN (punkt-lokacja) przy użyciu uwierzytelniania usługi Azure AD. Skonfiguruj uwierzytelnianie wieloskładnikowe na poszczególnych użytkownikach lub Skorzystaj z uwierzytelniania wieloskładnikowego z dostępem warunkowym. Dostęp warunkowy umożliwia dokładniejszą kontrolę nad sposobem promowania drugiego czynnika. Może zezwalać na przypisanie uwierzytelniania wieloskładnikowego tylko do sieci VPN i wykluczać inne aplikacje powiązane z dzierżawą usługi Azure AD. 

Należy pamiętać, że uwierzytelnianie usługi Azure AD jest dostępne tylko dla bram korzystających z protokołu OpenVPN i klientów z systemem Windows.

- [Co to jest dostęp warunkowy?](../active-directory/conditional-access/overview.md)

- [Konfigurowanie uwierzytelniania Azure Active Directory dla sieci VPN użytkownika](virtual-wan-point-to-site-azure-ad.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

**Wskazówki**: sieć VPN typu lokacja-lokacja w wirtualnej sieci WAN korzysta z kluczy wstępnych (PSK), które są wykrywane, tworzone i zarządzane przez klienta w Azure Key Vault. Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego wpisu tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: usługa Azure Virtual WAN korzysta z kontroli dostępu opartej na rolach (Azure RBAC), aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Należy również ograniczyć dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępu do firmy, takich jak kontrolery domena usługi Active Directory, narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

**Wskazówki**: Użyj sieci VPN typu punkt-lokacja, sieci VPN typu lokacja-lokacja i zaszyfrowanej trasy Express z wirtualną siecią WAN w celu uzyskania wymagań dotyczących łączności. Szyfrowanie sieci VPN chroni dane przesyłane z ataków "poza pasmem" (takich jak przechwytywanie ruchu), aby upewnić się, że osoby atakujące nie mogą odczytywać ani modyfikować danych. 

- [Sieć VPN typu punkt-lokacja](virtual-wan-point-to-site-portal.md)

- [Sieć VPN typu lokacja-lokacja](virtual-wan-site-to-site-portal.md)

- [Zaszyfrowane ExpressRoute](vpn-over-expressroute.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Z tego względu informacje o zabezpieczeniach i ryzyka muszą zawsze zostać zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Uwaga: Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym. Wirtualna sieć WAN platformy Azure obsługuje również wdrożenia zasobów oparte na Azure Resource Managerach, za pomocą których można eksportować szablony zasobów. 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure monitor, aby utworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość korzystania z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: sieć VPN typu punkt-lokacja z wirtualną siecią WAN jest zintegrowana z usługą Azure Active Directory (Azure AD). Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, SIEM platformy Azure, narzędziami do monitorowania i monitorowaniem. Są to:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Użyj Azure Security Center, aby utworzyć alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, w tym przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń należy używać modułu ochrony przed zagrożeniami w programie Security Center, aby zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: sieć VPN typu punkt-lokacja z wirtualną siecią WAN jest zintegrowana z usługą Azure Active Directory (Azure AD). Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, SIEM platformy Azure, narzędziami do monitorowania i monitorowaniem. Są to:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Użyj Azure Security Center, aby utworzyć alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, w tym przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń należy używać modułu ochrony przed zagrożeniami w programie Security Center, aby zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: monitorowanie wirtualnej sieci WAN platformy Azure przy użyciu Azure monitor. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. Wpisy dziennika aktywności są zbierane domyślnie i mogą być wyświetlane w Azure Portal. Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć dzienników aktywności platformy Azure (znanych wcześniej jako dzienników operacyjnych i dzienników inspekcji).

Wiele dzienników diagnostycznych jest również dostępnych dla wirtualnej sieci WAN i można je skonfigurować dla wirtualnego zasobu sieci WAN przy użyciu Azure Portal.  Możesz zdecydować się na wysyłanie do Log Analytics, przesyłanie strumieniowe do centrum zdarzeń lub po prostu archiwizowanie na koncie magazynu. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

- [Dzienniki i metryki usługi Azure Firewall](../firewall/logs-and-metrics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: dzienniki aktywności platformy Azure, włączone automatycznie, zawierają wszystkie operacje zapisu (Put, post, Delete) dla wirtualnych zasobów sieci WAN platformy Azure z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz dzienniki zasobów platformy Azure dla wirtualnej sieci WAN. Za pomocą Azure Security Center i Azure Policy można włączyć zbieranie danych dzienników zasobów i dzienników. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: Włączanie rejestrowania zabezpieczeń dla wirtualnej sieci WAN przy użyciu Azure monitor. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. Wpisy dziennika aktywności są zbierane domyślnie i mogą być wyświetlane w Azure Portal. Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć dzienników aktywności platformy Azure (znanych wcześniej jako dzienników operacyjnych i dzienników inspekcji). 

Wiele dzienników diagnostycznych jest również dostępnych dla wirtualnej sieci WAN i można je skonfigurować dla wirtualnego zasobu sieci WAN przy użyciu Azure Portal. Wysyłaj do Log Analytics, przesyłaj strumieniowo do centrum zdarzeń lub po prostu archiwizowanie na koncie magazynu. Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub informacji o zabezpieczeniach i rozwiązania do zarządzania zdarzeniami innych firm. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

- [Dzienniki i metryki usługi Azure Firewall](../firewall/logs-and-metrics.md)

Zabezpieczenia wirtualnej sieci WAN platformy Azure są udostępniane za pomocą zapory platformy Azure. 

- [Dokumentacja zapory platformy Azure](../firewall/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Skonfiguruj przechowywanie dzienników zgodnie z wymaganiami dotyczącymi zgodności, regulacji i wymagań firmy. W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center alertów i zaleceń — eksport](../security-center/continuous-export.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówka**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, zaktualizował te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt ds. zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>RZ-3: wykrywanie i analiza — tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki**: Upewnij się, że istnieje proces tworzenia alertów o wysokiej jakości i pomiaru jakości alertów. Pozwala to na wyciągnięcie wniosków z poprzednich zdarzeń i określić priorytety alertów dla analityków, aby nie tracili czasu na alerty fałszywie dodatnie. 

Alerty o wysokiej jakości mogą być tworzone w oparciu o doświadczenia z poprzednich zdarzeń, sprawdzone źródła społecznościowe i narzędzia przeznaczone do generowania i czyszczenia alertów poprzez łączenie i korelację różnych źródeł sygnałów. 

Usługa Azure Security Center oferuje alerty o wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>RZ-4: wykrywanie i analiza — badanie zdarzenia

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń w celu uzyskania pełnego obrazu tego, co się stało. Należy zbierać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w ramach całego łańcucha zagrożeń, aby uniknąć efektu martwego pola.  Należy również upewnić się, że szczegółowe informacje i wnioski są rejestrowane dla innych analityków i na potrzeby przyszłych badań.  

Źródła danych do badania obejmują scentralizowane źródła dzienników, które są już zbierane w ramach usług i działających systemów, ale mogą również obejmować:

- Dane sieciowe — użyj dzienników przepływów sieciowych grup zabezpieczeń, usługi Azure Network Watcher i usługi Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj natywnej możliwości zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji migawki w ramach usług platformy Azure lub w ramach własnego oprogramowania, aby utworzyć migawki uruchomionych systemów.

Usługa Azure Sentinel zapewnia szeroką analizę danych dla praktycznie każdego źródła dzienników i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje analityczne podczas badania mogą być powiązane ze zdarzeniami pod kątem śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informacje diagnostyczne pomocy technicznej oraz kolekcja zrzutów pamięci platformy Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Badanie zdarzeń za pomocą usługi Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Poziom ważności bazuje na ocenie pewności usługi Security Center względem ustalenia lub danych analitycznych użytych do wygenerowania alertu, a także poziomu pewności, że za działaniem, które doprowadziło do alertu, stał złośliwy zamiar.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="endpoint-security"></a>Zabezpieczenia punktu końcowego

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

**Wskazówki**: klienci nie mogą jawnie konfigurować ustawień wykrywania i reagowania punktów końcowych. Jednak Virtual Machines używany w ofercie wirtualnej sieci WAN platformy Azure używają tych funkcji. Więcej informacji na temat tych ogólnych funkcji znajduje się w łączach, do których istnieją odwołania. 

- [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Usługa Microsoft Defender ATP dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Usługa Microsoft Defender ATP dla serwerów spoza systemu Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania elementami zawartości i ochrony danych 

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię monitorowania i ochrony systemów i danych. Ustalaj priorytety odnajdywania, oceny, ochrony i monitorowania danych oraz systemów o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis elementów zawartości 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo elementów zawartości w całym cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z natywnych dla platformy Azure oraz oferowanych przez inne firmy funkcji ochrony danych

-   Wymagania w zakresie szyfrowania danych dla przypadków użycia dotyczących danych przesyłanych i danych magazynowanych

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](../security/benchmarks/security-controls-v2-asset-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówka**: Ustanów metodę zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej zgodny ze strategią segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca brzegowego ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i środowisku lokalnym

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, architektura sieci referencyjnej)

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](../security/benchmarks/security-controls-v2-network-security.md)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](../security/benchmarks/security-controls-v2-identity-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Należy stworzyć strategię rejestrowania i reagowania na zagrożenia w celu szybkiego wykrywania i usuwania zagrożeń przy jednoczesnym spełnieniu wymogów zgodności. Potraktuj priorytetowo kwestię udostępnienia analitykom alertów o wysokiej jakości i bezproblemowego środowiska, tak aby mogli skupić się na zagrożeniach, a nie na wdrażaniu i ręcznych krokach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji w zakresie operacji zabezpieczeń (SecOps) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

-   Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](../security/benchmarks/security-controls-v2-incident-response.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)