---
title: Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN
description: Podstawowa linia bazowa sieci WAN zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029051"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN

Ta linia bazowa zabezpieczeń stosuje wskazówki z [usługi Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure wirtualnej sieci WAN. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące wirtualnej sieci WAN. **Formanty** nie mają zastosowania do wirtualnej sieci WAN zostały wykluczone.

Aby sprawdzić, jak wirtualne sieci WAN są całkowicie mapowane na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń sieci WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: Microsoft Azure wirtualna sieć WAN oferuje niestandardowe możliwości routingu i oferuje szyfrowanie dla ruchu ExpressRoute. Wszystkie Zarządzanie trasami jest dostarczane przez router koncentratora wirtualnego, co umożliwia także łączność między sieciami wirtualnymi. Szyfrowanie ruchu ExpressRoute za pomocą wirtualnej sieci WAN zapewnia zaszyfrowane przechodzenie między sieciami lokalnymi i sieciami wirtualnymi platformy Azure przez ExpressRoute, bez przechodzenia przez publiczny Internet ani publicznych adresów IP. 

- [Szyfrowanie ruchu ExpressRoute](virtual-wan-about.md#encryption)

- [Korzystanie z prywatnego linku w wirtualnej sieci WAN](howto-private-link.md)

- [Niestandardowe scenariusze routingu](scenario-any-to-any.md)

- [Dokumentacja tabeli tras niestandardowych](how-to-virtual-hub-routing.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem 

**Wskazówki**: Microsoft Azure ExpressRoute oferuje prywatną łączność z wirtualną siecią WAN platformy Azure. Ponieważ połączenia ExpressRoute nie przechodzą przez publiczny Internet, ExpressRoute oferuje większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. Wirtualna sieć prywatna może również służyć do łączenia się z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja (S2S) lub sieci VPN typu punkt-lokacja (P2S).

- [ExpressRoute w wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)

- [Omówienie sieci VPN między lokacjami](virtual-wan-site-to-site-portal.md)

- [Omówienie połączeń sieci VPN między lokacjami](virtual-wan-point-to-site-portal.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: wirtualna sieć WAN nie ujawnia żadnych punktów końcowych sieci zewnętrznych, które wymagają ich zabezpieczenia przy użyciu konwencjonalnej ochrony sieci. Możesz chronić zasoby w sieciach wirtualnych szprych (dowolna Sieć wirtualna połączona z koncentratorem wirtualnym) przy użyciu usług ochrony sieci wirtualnej. 

Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. 

Wybierz DDoS Protection udostępnione przez platformę Azure, aby chronić swoje zasoby przed atakami w sieci wirtualnych platformy Azure. Użyj Azure Security Center, aby wykryć niepowodowane konfiguracje związane z zasobami związanymi z siecią.

- [Dokumentacja zapory platformy Azure](/azure/firewall)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: wirtualna sieć WAN to usługa zarządzana przez firmę Microsoft. Nie oferuje natywnych możliwości wykrywania intruzów ani zapobiegania włamaniom. Dostępne są jednak funkcje zabezpieczeń dostępne dla wirtualnej sieci WAN za pomocą zapory platformy Azure, aby umożliwić jednolity punkt kontroli zasad. Można utworzyć zasady zapory platformy Azure i połączyć je z wirtualnym koncentratorem sieci WAN, aby zezwolić istniejącemu wirtualnemu koncentratorowi platformy Azure na działanie jako zabezpieczonego koncentratora wirtualnego, z uwzględnieniem wymaganych zasobów zapory.

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Uprość reguły zabezpieczeń sieci, wykorzystując Virtual Network Tagi usługi do definiowania kontroli dostępu do sieci w grupach zabezpieczeń sieci lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi w polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Zrozumienie i używanie grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups)

- [Dokumentacja zapory platformy Azure](/azure/firewall/)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: bezpieczna usługa nazw domen (DNS)

**Wskazówki**: bezpieczne funkcje systemu DNS są udostępniane wirtualnej sieci WAN przy użyciu zapory platformy Azure. Skonfiguruj zaporę platformy Azure, która będzie działać jako serwer proxy DNS, który jest pośrednikiem dla żądań DNS od maszyn wirtualnych klienta do serwera DNS. W przypadku niestandardowych konfiguracji serwera DNS Włącz serwer proxy DNS, aby uniknąć niezgodności rozpoznawania nazw DNS, i Włącz w pełni kwalifikowane filtrowanie nazw domen w regułach sieci. 

- [Dokumentacja zapory platformy Azure](/azure/firewall/)

- [Ustawienia usługi DNS zapory platformy Azure](/azure/firewall/dns-settings)

- [Używanie zapory platformy Azure jako usługi przesyłania dalej DNS z linkiem prywatnym](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Wiadomość błyskawiczna-1: standaryzacja Azure Active Directory jako centralny system tożsamości i uwierzytelniania

**Wskazówki**: Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem dla usług platformy Azure. łącznie z wirtualną siecią WAN. Normalizuj usługę Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machines (Linux i Windows), Azure Key Vault, platforma jako usługa (PaaS) i oprogramowanie jako usługa (SaaS).
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej

Zabezpiecz usługę Azure AD jako wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Oceń swoją tożsamość i stan zabezpieczeń dzięki funkcji oceny zabezpieczeń z Security Center, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań firmy Microsoft. W razie potrzeby Zaimplementuj zalecenia dotyczące najlepszych rozwiązań firmy Microsoft dotyczące ulepszeń stan zabezpieczeń.

Usługa Azure AD obsługuje również tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej. 

Zapoznaj się z informacjami dotyczącymi korzystania z usługi Azure AD w scenariuszach połączeń VPN typu punkt-lokacja w przywoływanych linkach.

- [Utwórz dzierżawę usługi Azure Active Directory (AD) dla połączeń protokołu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurowanie uwierzytelniania Azure Active Directory dla sieci VPN użytkownika](virtual-wan-point-to-site-azure-ad.md)

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>KOMUNIKATOR-4: Użyj kontrolek silnego uwierzytelniania dla wszystkich Azure Active Directory na podstawie dostępu

**Wskazówki**: obecnie uwierzytelnianie Azure Active Directory (Azure AD) jest zapewniane przez integrację z wirtualną siecią VPN typu punkt-lokacja.

Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem dla usług platformy Azure. Usługa Azure AD obsługuje kontrole silnego uwierzytelniania z uwierzytelnianiem wieloskładnikowym i silnymi metodami bez hasła.

W przypadku kontroli silnego uwierzytelniania usługa Azure AD zaleca następujące czynności:

- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w Azure Security Center z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń. Wymuś uwierzytelnianie wieloskładnikowe na wszystkich, wybierz opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka

- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła. Należą do nich:, Windows Hello dla firm, Microsoft Authenticator App i lokalne metody uwierzytelniania, takie jak karty inteligentne

Upewnij się, że najwyższego poziomu metody silnego uwierzytelniania są używane na potrzeby użytkowników z uprawnieniami administratora i uprzywilejowanego, a następnie przechodzące zasady silnego uwierzytelniania do innych użytkowników.

- [Jak włączyć usługę MFA w sieci VPN P2S dla wirtualnej sieci WAN](openvpn-azure-ad-mfa.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Wiadomości błyskawiczne — 6: Ogranicz dostęp do zasobów platformy Azure na podstawie warunków

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) dla użytkowników sieci VPN (punkt-lokacja) przy użyciu uwierzytelniania usługi Azure AD. Skonfiguruj uwierzytelnianie wieloskładnikowe na poszczególnych użytkownikach lub Skorzystaj z uwierzytelniania wieloskładnikowego z dostępem warunkowym. Dostęp warunkowy umożliwia dokładniejszą kontrolę nad sposobem promowania drugiego czynnika. Może zezwalać na przypisanie uwierzytelniania wieloskładnikowego tylko do sieci VPN i wykluczać inne aplikacje powiązane z dzierżawą usługi Azure AD. 

Należy pamiętać, że uwierzytelnianie usługi Azure AD jest dostępne tylko dla bram korzystających z protokołu OpenVPN i klientów z systemem Windows.

- [Co to jest dostęp warunkowy?](../active-directory/conditional-access/overview.md)

- [Konfigurowanie uwierzytelniania Azure Active Directory dla sieci VPN użytkownika](virtual-wan-point-to-site-azure-ad.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Wiadomości błyskawiczne — 7: eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki**: sieć VPN typu lokacja-lokacja w wirtualnej sieci WAN korzysta z kluczy wstępnych (PSK), które są wykrywane, tworzone i zarządzane przez klienta w Azure Key Vault. Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego klucza tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="privileged-access"></a>Uprzywilejowany dostęp

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: usługa Azure Virtual WAN korzysta z kontroli dostępu opartej na rolach (Azure RBAC), aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Należy również ograniczyć dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępu do firmy, takich jak kontrolery domena usługi Active Directory, narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfruj poufne informacje podczas przesyłania

**Wskazówki**: Użyj sieci VPN typu punkt-lokacja, sieci VPN typu lokacja-lokacja i zaszyfrowanej trasy Express z wirtualną siecią WAN w celu uzyskania wymagań dotyczących łączności. Szyfrowanie sieci VPN chroni dane przesyłane z ataków "poza pasmem" (takich jak przechwytywanie ruchu), aby upewnić się, że osoby atakujące nie mogą odczytywać ani modyfikować danych. 

- [Sieć VPN typu punkt-lokacja](virtual-wan-point-to-site-portal.md)

- [Sieć VPN typu lokacja-lokacja](virtual-wan-site-to-site-portal.md)

- [Zaszyfrowane ExpressRoute](vpn-over-expressroute.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Z tego względu informacje o zabezpieczeniach i ryzyka muszą zawsze zostać zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Uwaga: w celu uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie Grupy zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym. Wirtualna sieć WAN platformy Azure obsługuje również wdrożenia zasobów oparte na Azure Resource Managerach, za pomocą których można eksportować szablony zasobów. 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure monitor, aby utworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość korzystania z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: sieć VPN typu punkt-lokacja z wirtualną siecią WAN jest zintegrowana z usługą Azure Active Directory (Azure AD). Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, SIEM platformy Azure, narzędziami do monitorowania i monitorowaniem. Są to:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Użyj Azure Security Center, aby utworzyć alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, w tym przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń należy używać modułu ochrony przed zagrożeniami w programie Security Center, aby zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włącz usługę Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: sieć VPN typu punkt-lokacja z wirtualną siecią WAN jest zintegrowana z usługą Azure Active Directory (Azure AD). Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, SIEM platformy Azure, narzędziami do monitorowania i monitorowaniem. Są to:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Użyj Azure Security Center, aby utworzyć alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, w tym przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń należy używać modułu ochrony przed zagrożeniami w programie Security Center, aby zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włącz usługę Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

**Wskazówki**: monitorowanie wirtualnej sieci WAN platformy Azure przy użyciu Azure monitor. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. Wpisy dziennika aktywności są zbierane domyślnie i mogą być wyświetlane w Azure Portal. Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć dzienników aktywności platformy Azure (znanych wcześniej jako dzienników operacyjnych i dzienników inspekcji).

Wiele dzienników diagnostycznych jest również dostępnych dla wirtualnej sieci WAN i można je skonfigurować dla wirtualnego zasobu sieci WAN przy użyciu Azure Portal.  Możesz zdecydować się na wysyłanie do Log Analytics, przesyłanie strumieniowe do centrum zdarzeń lub po prostu archiwizowanie na koncie magazynu. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

- [Dzienniki i metryki usługi Azure Firewall](/azure/firewall/logs-and-metrics)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności platformy Azure, włączone automatycznie, zawierają wszystkie operacje zapisu (Put, post, Delete) dla wirtualnych zasobów sieci WAN platformy Azure z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz dzienniki zasobów platformy Azure dla wirtualnej sieci WAN. Za pomocą Azure Security Center i Azure Policy można włączyć zbieranie danych dzienników zasobów i dzienników. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: Włączanie rejestrowania zabezpieczeń dla wirtualnej sieci WAN przy użyciu Azure monitor. Wirtualna sieć WAN oferuje wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Bramy sieci VPN wirtualnej sieci WAN, bramy ExpressRoute i Zapora platformy Azure mają dostępne rejestrowanie i metryki za pośrednictwem Azure Monitor. Wpisy dziennika aktywności są zbierane domyślnie i mogą być wyświetlane w Azure Portal. Aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure, można użyć dzienników aktywności platformy Azure (znanych wcześniej jako dzienników operacyjnych i dzienników inspekcji). 

Wiele dzienników diagnostycznych jest również dostępnych dla wirtualnej sieci WAN i można je skonfigurować dla wirtualnego zasobu sieci WAN przy użyciu Azure Portal. Wysyłaj do Log Analytics, przesyłaj strumieniowo do centrum zdarzeń lub po prostu archiwizowanie na koncie magazynu. Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub informacji o zabezpieczeniach i rozwiązania do zarządzania zdarzeniami innych firm. 
 

- [Wirtualne dzienniki i metryki sieci WAN](logs-metrics.md)

- [Dzienniki i metryki usługi Azure Firewall](/azure/firewall/logs-and-metrics)

Zabezpieczenia wirtualnej sieci WAN platformy Azure są udostępniane za pomocą zapory platformy Azure. 

- [Dokumentacja zapory platformy Azure](/azure/firewall/overview)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Skonfiguruj przechowywanie dzienników zgodnie z wymaganiami dotyczącymi zgodności, regulacji i wymagań firmy. W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Zmień okres przechowywania danych w Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center alertów i zaleceń — eksport](../security-center/continuous-export.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

**Wskazówki**: Upewnij się, że organizacja ma procesy odpowiadające na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: przygotowanie — powiadomienie o zdarzeniu instalacji

**Wskazówki**: Konfigurowanie informacji kontaktowych dotyczących zdarzeń zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można uczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą one tracić czasu na fałszywie dodatnich. 

Alerty o wysokiej jakości mogą być tworzone na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów przez odmowę i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty o wysokiej jakości dla wielu zasobów platformy Azure. Łącznika danych ASC można użyć do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Wskaźnik oceny platformy Azure umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały sposób ciągły.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń, aby zbudować pełny wgląd w to, co się stało. Należy zebrać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Należy również upewnić się, że szczegółowe informacje i uczenia są przechwytywane dla innych analityków i w przyszłości.  

Źródła danych do zbadania obejmują scentralizowane źródła rejestrowania, które są już zbierane z usług w zakresie i działających systemów, ale mogą również obejmować:

- Dane sieci — używanie dzienników przepływów sieciowych grup zabezpieczeń, platformy Azure Network Watcher i Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj możliwości natywnego zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub własnej funkcji oprogramowania, aby utworzyć migawki uruchomionych systemów.

Platforma Azure — wskaźnik przedstawia szeroką analizę danych w praktycznie dowolnym źródle dziennika i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje o analizie podczas badania mogą być powiązane ze zdarzeniami w celu śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure obsługi informacji diagnostycznych i kolekcji zrzutów pamięci](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzenia mają być skoncentrowane na początku, na podstawie ważności alertów i poufności zasobów. 

Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć zasoby przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

**Wskazówki**: automatyzuje ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę incydentów, które może obsłużyć analityk. Zadania ręczne zwiększają również zmęczenie analityków, które zwiększają ryzyko wystąpienia błędu ludzkiego powodującego opóźnienia i obniżają możliwości skoncentrowania się na skomplikowanych zadaniach analityków. Korzystając z funkcji automatyzacji przepływu pracy w Azure Security Center i wskaźnik platformy Azure, można automatycznie wyzwalać akcje lub uruchamiać element PlayBook w celu reagowania na przychodzące alerty zabezpieczeń. Element PlayBook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia w Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="posture-and-vulnerability-management"></a>Stan i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: przeprowadzenie regularnej symulacji ataków

**Wskazówki**: w razie potrzeby przeprowadź testowanie penetracji lub czerwone działania zespołu na Twoich zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z Microsoft Cloud regułami testowania penetracji, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Testowanie penetracji na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="endpoint-security"></a>Zabezpieczenia punktu końcowego

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

**Wskazówki**: klienci nie mogą jawnie konfigurować ustawień wykrywania i reagowania punktów końcowych. Jednak Virtual Machines używany w ofercie wirtualnej sieci WAN platformy Azure używają tych funkcji. Więcej informacji na temat tych ogólnych funkcji znajduje się w łączach, do których istnieją odwołania. 

- [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Usługa Microsoft Defender ATP dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Usługa Microsoft Defender ATP dla serwerów spoza systemu Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security Tests: ładu and strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią ciągłego monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z funkcji ochrony danych natywnych i firmowych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także z kontrolkami dostępu i dostępem do aplikacji.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

**Wskazówki**: stałe mierzenie i łagodzenie ryzyka dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przenosząc wszystkie osoby na współużytkowany model odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówki**: ustanowienie podejścia zabezpieczeń sieci platformy Azure jako części ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

**Wskazówki**: ustanowienie podejścia do tożsamości platformy Azure i dostępu uprzywilejowanego w ramach ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami o wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

-   Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie usługi Azure Security test w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
