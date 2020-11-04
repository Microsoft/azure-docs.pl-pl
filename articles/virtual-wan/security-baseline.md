---
title: Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN
description: Podstawowa linia bazowa sieci WAN zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328724"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Podstawa zabezpieczeń platformy Azure dla wirtualnej sieci WAN

Ta linia bazowa zabezpieczeń stosuje wskazówki z [usługi Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure wirtualnej sieci WAN. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące wirtualnej sieci WAN. **Formanty** nie mają zastosowania do wirtualnej sieci WAN zostały wykluczone.

Aby sprawdzić, jak wirtualne sieci WAN są całkowicie mapowane na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń sieci WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki** : usługa Azure Virtual WAN nie obsługuje wdrażania bezpośrednio w sieci wirtualnej. Jednak nadal można stosować reguły sieciowej grupy zabezpieczeń w zasobach szprych, używać ochrony zapory platformy Azure lub tworzyć niestandardowe tabele tras w celu zapobiegania lub zezwalania na ruch prywatny.

- [Niestandardowe scenariusze routingu](scenario-any-to-any.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem 

**Wskazówki** : Użyj usługi Azure ExpressRoute lub wirtualnej sieci prywatnej (VPN) platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet, oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. 

W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja Połącz lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i ExpressRoute. Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj Virtual Network komunikacji równorzędnej. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure.

- [ExpressRoute w wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)

- [Omówienie sieci VPN między lokacjami](virtual-wan-site-to-site-portal.md)

- [Omówienie połączeń sieci VPN między lokacjami](virtual-wan-point-to-site-portal.md)

- [Link prywatny](howto-private-link.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : udostępnione

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp do sieci prywatnej do usług platformy Azure

**Wskazówki** : Użyj prywatnego linku platformy Azure, aby umożliwić prywatnym dostęp do wirtualnej sieci WAN platformy Azure z sieci wirtualnych bez przekraczania Internetu. Dostęp prywatny jest kolejną metodą obrony szczegółowej oprócz uwierzytelniania i zabezpieczeń ruchu oferowanych przez usługi platformy Azure.

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Połączenie prywatne wirtualnej sieci WAN](howto-private-link.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki** : Ochrona wirtualnych zasobów sieci WAN platformy Azure przed atakami z sieci zewnętrznych, w tym ataków typu "odmowa usługi" (DDoS), ataków specyficznych dla aplikacji oraz nieproszonym i potencjalnie złośliwym ruchem internetowym. 

Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. Wybierz Azure DDoS Protection zasobów przed atakami w sieci wirtualnych platformy Azure. Użyj Azure Security Center do wykrywania niepowodowanych problemów z konfiguracją związanych z zasobami związanymi z siecią.

- [Dokumentacja zapory platformy Azure](/azure/firewall)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki** : najlepszym sposobem wdrażania systemów wykrywania lub zapobiegania włamaniu w wirtualnej sieci WAN platformy Azure jest użycie sieciowego urządzenia wirtualnego w sieciach szprych podłączonych do koncentratora wirtualnego.  Więcej informacji można znaleźć w scenariuszach routingu na linkach, do których istnieją odwołania. 

- [Scenariusz — kierowanie ruchem za pośrednictwem urządzenia WUS](scenario-route-through-nva.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki** : Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci platformy Azure lub w zaporze platformy Azure skonfigurowanych dla wirtualnych zasobów sieci WAN. 

Podczas tworzenia reguł zabezpieczeń użyj tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład: {VirtualWAN: Virtual Network}) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: bezpieczna usługa nazw domen (DNS)

**Wskazówki** : usługa Azure Virtual WAN oferuje niestandardowe serwery DNS dla bram sieci VPN typu punkt-lokacja. 

Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń DNS, aby wyeliminować typowe ataki, takie jak zawieszonego DNS, DNS wzmocnienie ataków, zatrucie i fałszowanie DNS itd.

Gdy Azure DNS jest używany jako autorytatywna usługa DNS, upewnij się, że strefy i rekordy DNS są chronione przed przypadkową lub złośliwą modyfikacją przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) i blokad zasobów.

- [Przegląd Azure DNS](../dns/dns-overview.md) 

- [Przewodnik wdrażania bezpiecznego systemu nazw domen (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych](../security/fundamentals/subdomain-takeover.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Wiadomość błyskawiczna-1: standaryzacja Azure Active Directory jako centralny system tożsamości i uwierzytelniania

**Wskazówki** : sieć VPN typu punkt-lokacja usługi Azure Virtual WAN jest zintegrowana z usługą Azure Active Directory (Azure AD), która to domyślna usługa zarządzania tożsamościami i dostępem platformy Azure. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machines (Linux i Windows), Azure Key Vault, PaaS i SaaS.
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej.

Zabezpiecz usługę Azure AD przy użyciu wysokiego priorytetu w zakresie zabezpieczeń chmury w organizacji. Oceń swoją tożsamość i stan zabezpieczeń, korzystając z funkcji oceny zabezpieczeń Azure Security Center, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań. Zaimplementuj zalecenia dotyczące najlepszych rozwiązań firmy Microsoft dotyczące ulepszeń stan zabezpieczeń.

Usługa Azure AD obsługuje również tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej. Zapoznaj się z informacjami dotyczącymi korzystania z usługi Azure AD w scenariuszach połączeń VPN typu punkt-lokacja w przywoływanych linkach.

- [Utwórz dzierżawę usługi Azure Active Directory (AD) dla połączeń protokołu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurowanie uwierzytelniania Azure Active Directory dla sieci VPN użytkownika](virtual-wan-point-to-site-azure-ad.md)

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Użyj zewnętrznych dostawców tożsamości dla aplikacji](/azure/active-directory/b2b/identity-providers)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>KOMUNIKATOR-4: Użyj kontrolek silnego uwierzytelniania dla wszystkich Azure Active Directory na podstawie dostępu

**Wskazówki** : sieć VPN typu punkt-lokacja usługi Azure Virtual WAN jest zintegrowana z usługą Azure Active Directory (Azure AD), która obsługuje silne funkcje kontroli uwierzytelniania z uwierzytelnianiem wieloskładnikowym i silnymi metodami bez hasła.

- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem, aby uzyskać najlepsze rozwiązania dotyczące konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić na wszystkich, wybrać opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka.

- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła. Należą do nich:, Windows Hello dla firm, Microsoft Authenticator App i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i administratorów należy upewnić się, że są używane najwyższego poziomu metody silnego uwierzytelniania, a następnie przeprowadzenie przez niego wdrożenia odpowiednich zasad silnego uwierzytelniania.

- [Jak włączyć usługę MFA w sieci VPN P2S dla wirtualnej sieci WAN](openvpn-azure-ad-mfa.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Wiadomości błyskawiczne — 6: Ogranicz dostęp do zasobów platformy Azure na podstawie warunków

**Wskazówki** : sieć VPN typu punkt-lokacja usługi Azure Virtual WAN obsługuje dostęp warunkowy usługi Azure AD w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika. Na przykład logowania użytkowników z określonych zakresów adresów IP muszą używać uwierzytelniania wieloskładnikowego podczas logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Dostęp warunkowy do wirtualnej sieci WAN P2S VPN](openvpn-azure-ad-mfa.md#conditional)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Wiadomości błyskawiczne — 7: eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki** : Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego klucza tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

## <a name="privileged-access"></a>Uprzywilejowany dostęp

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki** : usługa Azure Virtual WAN korzysta z kontroli dostępu opartej na rolach (Azure RBAC), aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, w których się znajdują.

Należy również ograniczyć dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do krytycznych dostępu do firmy, takich jak kontrolery domena usługi Active Directory, narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla działalności firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: używanie uprzywilejowanych stacji roboczych dostępu

**Wskazówki** : zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce zabezpieczonych stacji roboczych użytkowników lub usługi Azure bastionu na potrzeby zadań administracyjnych. Użyj Azure Active Directory (Azure AD), usługi Microsoft Defender Advanced Threat Protection (ATP) lub Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](../active-directory/devices/concept-azure-managed-workstation.md)

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfruj poufne informacje podczas przesyłania

**Wskazówki** : szyfrowanie ma kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych.

- Przy użyciu funkcji kontroli dostępu dane przesyłane w trakcie przesyłania danych powinny być chronione przed atakami typu "poza pasmem" (na przykład przechwytywanie ruchu) za pomocą szyfrowania, aby upewnić się, że osoby atakujące nie mogą łatwo odczytać ani zmodyfikować danych.

- Upewnij się, że dla ruchu HTTP wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej.-

- W przypadku zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows) zamiast z nieszyfrowanym protokołem. Przestarzałe wersje protokołu SSL/TLS/SSH, protokoły i słabe szyfry powinny być wyłączone.-

- W podstawowej infrastrukturze platforma Azure zapewnia domyślnie dane dotyczące ruchu danych w ramach usługi Azure datacenters.

Ogólnie rzecz biorąc, zapewniamy szyfrowanie bezpiecznej sieci szkieletowej firmy Microsoft i szanse na szyfrowanie ruchu sieciowego, sieci VPN typu lokacja-lokacja za pośrednictwem usługi Azure ExpressRoute i sieci VPN typu punkt-lokacja.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem)

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

**Wskazówki** : Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Wirtualna sieć WAN platformy Azure obsługuje również wdrożenia zasobów na podstawie Azure Resource Manager i zapytania dotyczące wykresów zasobów platformy Azure. 

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki** : Użyj Azure Policy, aby ograniczyć usługi, które mogą być obsługiwane w danym środowisku. Twórz zapytania i odnajduj zasoby przy użyciu grafu zasobów platformy Azure w ramach subskrypcji i użyj Azure Monitor do tworzenia reguł wyzwalania alertów w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki** : Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość korzystania z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki** : Azure Active Directory (Azure AD) udostępniają następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, platformą Azure, Siem lub narzędziami do monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy. Są to:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Użyj Azure Security Center, aby utworzyć alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, w tym przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń należy używać Security Center modułu ochrony przed zagrożeniami, aby zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włącz usługę Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

**Wskazówki** : Użyj narzędzi przechwytywania pakietów sieci VPN, aby zarejestrować pakiety sieciowe przesyłane między zasobami wirtualnej sieci WAN platformy Azure. Może to ułatwić proces debugowania związany z siecią hybrydową. Nie można wdrożyć sieciowej grupy zabezpieczeń w wirtualnej sieci WAN, można wdrożyć ją w zasobach sieci wirtualnej szprych.

Włącz dzienniki przepływu sieciowych grup zabezpieczeń w sieciowych grupach zabezpieczeń na potrzeby inspekcji ruchu.

Włącz funkcję Analiza ruchu platformy Azure, aby przetwarzać dzienniki przepływów przechowywane na koncie magazynu, a następnie wysyłać je do Log Analytics obszaru roboczego. Analiza ruchu zapewnia dodatkowe informacje o przepływie ruchu dla sieci platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md) 

Wirtualna sieć WAN nie produkuje ani nie przetwarza dzienników zapytań DNS, które trzeba włączyć.

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki** : dzienniki aktywności platformy Azure, włączone automatycznie, zawierają wszystkie operacje zapisu (Put, post, Delete) dla wirtualnych zasobów sieci WAN platformy Azure z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób. Jednak wirtualna sieć WAN nie produkuje dzienników zasobów platformy Azure.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki** : scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych. Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnymi systemami rejestrowania. 

Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania. Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : udostępnione

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

**Wskazówki** : Upewnij się, że organizacja ma procesy odpowiadające na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości. Upewnij się, że oferta obsługi jest uwzględniona w procesie reagowania na zdarzenia, zgodnie z wymaganiami.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](https://aka.ms/AzSec4) 
- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](https://aka.ms/IRRG)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: przygotowanie — powiadomienie o zdarzeniu instalacji

**Wskazówki** : Konfigurowanie informacji kontaktowych dotyczących zdarzeń zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia.

Firma Microsoft będzie używać informacji kontaktowych o zdarzeniach dotyczących zabezpieczeń, aby skontaktować się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy są w pełni rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki** : Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

**Wskazówki** : Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Przeprowadzaj ćwiczenia, aby testować możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki** : Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Ponadto wyraźnie oznaczaj subskrypcje (takie jak produkcja, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md) 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

**Wskazówki** : Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Azure Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : klient

## <a name="posture-and-vulnerability-management"></a>Stan i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: przeprowadzenie regularnej symulacji ataków

**Wskazówki** : zgodnie z wymaganiami, przeprowadzaj testowanie penetracji lub czerwone działania zespołu na Twoich zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.

Postępuj zgodnie z Microsoft Cloud regułami testowania penetracji, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft, wykonywania testów trójwymiarowych i testowania penetracji witryn na żywo względem infrastruktury, usług i aplikacji chmurowych zarządzanych przez firmę Microsoft.

- [Testowanie penetracji na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center** : nie dotyczy

**Odpowiedzialność** : klient

## <a name="endpoint-security"></a>Zabezpieczenia punktu końcowego

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

**Wskazówki** : klienci nie mogą jawnie konfigurować ustawień wykrywania i reagowania punktów końcowych. Jednak Virtual Machines używany w ofercie wirtualnej sieci WAN platformy Azure używają tych funkcji. Więcej informacji na temat tych ogólnych funkcji znajduje się w łączach, do których istnieją odwołania. 

- [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Usługa Microsoft Defender ATP dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Usługa Microsoft Defender ATP dla serwerów spoza systemu Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorowanie Azure Security Center** : tak

**Odpowiedzialność** : udostępnione

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security Tests: ładu and strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki** : Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią ciągłego monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Standard klasyfikacji danych zgodny z ryzykiem biznesowym
- Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 
- Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 
- Bezpieczeństwo zasobów w ramach cyklu życia
- Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji
- Korzystanie z funkcji ochrony danych natywnych i firmowych platformy Azure
- Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku
- Odpowiednie standardy kryptograficzne

Zapoznaj się z dodatkowymi informacjami dostępnymi na linkach przywoływanych.

- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki** : Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek. Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także uprawnieniami aplikacji lub modelami dostępu oraz kontrolkami procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

**Wskazówki** : stałe mierzenie i łagodzenie ryzyka dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących, punkty końcowe użytkowników i administratorów itd.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

**Wskazówki** : Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety działań w oparciu o wyraźną odpowiedzialność za decyzje dotyczące zabezpieczeń, przenosząc się do wszystkich wspólnych modeli odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](https://aka.ms/AzSec1)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](https://aka.ms/AzSec2)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](https://aka.ms/AzSec3)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówki** : ustanowienie podejścia zabezpieczeń sieci platformy Azure jako części ogólnej strategii kontroli dostępu zabezpieczeń w organizacji. Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń
- Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa
- Strategia korygowania w różnych scenariuszach zagrożeń i ataków
- Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego
- Strategia międzyłączności w chmurze hybrydowej i lokalnej
- Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Zapoznaj się z dodatkowymi informacjami dostępnymi na linkach przywoływanych.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

**Wskazówki** : ustalanie tożsamości platformy Azure i dostępu uprzywilejowanego w ramach ogólnej strategii kontroli dostępu zabezpieczeń w organizacji. Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości
- Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach
- Ochrona użytkowników z wysokim poziomem uprawnień
- Monitorowanie i obsługa nietypowych działań użytkowników  
- Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Zapoznaj się z dodatkowymi informacjami dostępnymi na linkach przywoływanych.

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki** : Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami o wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Rola i obowiązki organizacji operacji zabezpieczeń (SecOP)
- Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową
- Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności
- Scentralizowana widoczność i korelacja informacji o zagrożeniach, korzystanie z SIEM, natywnych możliwości platformy Azure i innych źródeł — z klientami, dostawcami i zainteresowanymi podmiotami.
- Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków
- Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Zapoznaj się z dodatkowymi informacjami dostępnymi na linkach przywoływanych.
- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](https://aka.ms/AzSec4)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitorowanie Azure Security Center** : obecnie niedostępna

**Odpowiedzialność** : klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie usługi Azure Security test w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
