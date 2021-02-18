---
title: Linia bazowa zabezpieczeń Azure dla pulpitu wirtualnego systemu Windows
description: Linia bazowa zabezpieczeń pulpitu wirtualnego systemu Windows zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42795e2dda6df24e656c9c06f6a9424bd9e4b5cb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092980"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linia bazowa zabezpieczeń Azure dla pulpitu wirtualnego systemu Windows

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 2,0 usługi Azure Security test](../security/benchmarks/overview.md) do pulpitu wirtualnego systemu Windows. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące pulpitu wirtualnego systemu Windows. **Formanty** nie mają zastosowania do pulpitu wirtualnego systemu Windows zostały wykluczone.

Usługa pulpitów wirtualnych systemu Windows obejmuje samą usługę, wirtualną jednostkę SKU z systemem Windows 10, a także FSLogix. Zalecenia dotyczące zabezpieczeń związane z FSLogixą znajdują się w [punkcie odniesienia zabezpieczeń usługi Storage](../storage/common/security-baseline.md). Usługa ma agenta uruchomionego na maszynach wirtualnych, ale ponieważ maszyny wirtualne są w pełni kontrolowane przez klienta, należy przestrzegać [zaleceń dotyczących zabezpieczeń dotyczących obliczeń](../virtual-machines/windows/security-baseline.md)

Aby dowiedzieć się, jak usługa pulpit wirtualny systemu Windows jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń pulpitu wirtualnego systemu Windows](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: należy utworzyć lub użyć istniejącej sieci wirtualnej podczas wdrażania maszyn wirtualnych do rejestracji na pulpicie wirtualnym systemu Windows. Upewnij się, że wszystkie sieci wirtualne platformy Azure są zgodne z zasadami segmentacji przedsiębiorstwa, które są dostosowane do ryzyka biznesowego. Każdy system, który może ponieść wyższy poziom ryzyka dla organizacji, powinien być odizolowany w ramach własnej sieci wirtualnej i dostatecznie zabezpieczony za pomocą sieciowej grupy zabezpieczeń lub zapory platformy Azure.

Korzystanie z funkcji adaptacyjnego ograniczania sieci w Azure Security Center, aby zalecać konfiguracje sieciowych grup zabezpieczeń, które ograniczają porty i źródłowe adresy IP z odniesieniem do zewnętrznych reguł ruchu sieciowego.

Na podstawie aplikacji i strategii segmentacji przedsiębiorstwa należy ograniczyć lub zezwolić na ruch między zasobami wewnętrznymi na podstawie reguł sieciowej grupy zabezpieczeń. W przypadku określonych dobrze zdefiniowanych aplikacji (np. aplikacji 3-warstwowej) może to być wysoce bezpieczne "odmowa domyślnie" Odmów przez wyjątek ". To może nie skalować się prawidłowo, jeśli masz wiele aplikacji i punktów końcowych, które współdziałają ze sobą. Możesz również użyć zapory platformy Azure w sytuacjach, gdy centralne zarządzanie jest wymagane dla dużej liczby segmentów przedsiębiorstwa lub szprych (w topologii gwiazdy).

Dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną (które są częścią podsieci pulpitu wirtualnego systemu Windows) należy zezwolić na ruch wychodzący do określonych punktów końcowych. 

- [Dowiedz się, jakie adresy URL są wymagane do uzyskania dostępu do pulpitu wirtualnego systemu Windows](safe-url-list.md)

- [Adaptacyjne Zabezpieczanie sieci w Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Zapora platformy Azure dla pulpitu wirtualnego systemu Windows ](../firewall/protect-windows-virtual-desktop.md)

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

**Wskazówki**: Użyj usługi Azure ExpressRoute lub wirtualnej sieci prywatnej platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet, oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. 

W przypadku wirtualnych sieci prywatnych typu punkt-lokacja i lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji opcji wirtualnej sieci prywatnej i usługi Azure ExpressRoute.

Używaj komunikacji równorzędnej sieci wirtualnej, aby połączyć dwie lub więcej sieci wirtualnych razem na platformie Azure. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i pozostaje w sieci szkieletowej platformy Azure.

- [Co to są modele łączności ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Omówienie usługi Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Komunikacja równorzędna sieci wirtualnych](/azure/virtual-network/virtual-network-peering-overview)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: aby chronić aplikacje i usługi przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych, należy użyć zapory platformy Azure. Ochrona zasobów klasycznych systemu Windows przed atakami z sieci zewnętrznych, w tym rozproszone ataki typu "odmowa usługi", ataki specyficzne dla aplikacji, niepożądane i potencjalnie złośliwy ruch internetowy. Ochrona zasobów przed rozproszonymi atakami typu "odmowa usługi" przez włączenie ochrony Standard DDoS w sieci wirtualnej platformy Azure. Użyj Azure Security Center do wykrywania niepowodowanych problemów z konfiguracją związanych z zasobami związanymi z siecią.

Pulpit wirtualny systemu Windows nie jest przeznaczony do uruchamiania aplikacji sieci Web i nie wymaga konfigurowania żadnych dodatkowych ustawień ani wdrażania dodatkowych usług sieciowych, aby chronić je przed atakami z sieci zewnętrznych ukierunkowanymi na aplikacje sieci Web.

- [Dokumentacja zapory platformy Azure](/azure/firewall)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: Użyj zapory platformy Azure z filtrowaniem opartym na analizie zagrożeń, aby ostrzec i opcjonalnie blokować ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. Gdy jest wymagana inspekcja ładunku, możesz wdrożyć rozwiązanie do wykrywania lub zapobiegania włamaniom innych firm z poziomu portalu Azure Marketplace. 

Jeśli masz przepisy prawne lub inne wymagania dotyczące wykrywania intruzów lub zapobiegania użyciu rozwiązania, upewnij się, że jest ono zawsze dostrojone, aby zapewnić wysoką jakość alertów dotyczących rozwiązania do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM).

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Portal Azure Marketplace obejmuje możliwości identyfikatorów innych firm](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Funkcja Microsoft Defender ATP EDR](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub zaporę platformy Azure skonfigurowaną dla zasobów pulpitu wirtualnego systemu Windows. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład: WindowsVirtualDesktop) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

- [Dowiedz się więcej na temat tego, co obejmuje tag usługi pulpitu wirtualnego systemu Windows ](safe-url-list.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: pulpit wirtualny systemu Windows używa programu Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.

- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia wskaźnik bezpieczeństwa tożsamości, który pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj ze wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń.

Usługa Azure AD obsługuje tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w usłudze Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Używanie zewnętrznych dostawców tożsamości w aplikacji](/azure/active-directory/b2b/identity-providers)

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Określone role, które muszą działać na pulpicie wirtualnym systemu Windows ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: pulpit wirtualny systemu Windows obsługuje tożsamości zarządzane przez platformę Azure dla kont nieludzkich, takich jak usługi lub Automatyzacja. Zalecane jest używanie funkcji tożsamości zarządzanej platformy Azure zamiast tworzenia bardziej zaawansowanego konta ludzkiego w celu uzyskania dostępu do zasobów lub ich wykonania. 

Pulpit wirtualny systemu Windows zaleca użycie Azure Active Directory (Azure AD) w celu utworzenia jednostki usługi z ograniczonymi uprawnieniami na poziomie zasobu w celu skonfigurowania jednostek usługi z poświadczeniami certyfikatów i powrotu do wpisów tajnych klienta. W obu przypadkach Azure Key Vault mogą być używane w połączeniu z tożsamościami zarządzanymi przez platformę Azure, dzięki czemu środowisko uruchomieniowe (na przykład funkcja platformy Azure) może pobrać poświadczenie z magazynu kluczy.

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Jednostka usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Tworzenie jednostki usługi przy użyciu certyfikatów](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Użyj Azure Key Vault do rejestracji podmiotu zabezpieczeń](../key-vault/general/authentication.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: pulpit wirtualny systemu Windows używa usługi Azure Active Directory (Azure AD), aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to logowanie jednokrotne w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Łączenie wszystkich użytkowników, aplikacji i urządzeń z usługą Azure AD w celu zapewnienia bezproblemowego bezpiecznego dostępu przy użyciu większej widoczności i kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory 

**Wskazówki**: pulpit wirtualny systemu Windows używa usługi Azure Active Directory (Azure AD), która obsługuje ścisłe mechanizmy kontroli uwierzytelniania przez uwierzytelnianie wieloskładnikowe i silne metody bezhasła.

- Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami związanymi z zarządzaniem tożsamościami i dostępem w Azure Security Center, aby uzyskać najlepsze rozwiązania w konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić na wszystkich, wybrać opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka.

- Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

Pulpit wirtualny systemu Windows obsługuje uwierzytelnianie oparte na hasłach, takie jak konta tylko w chmurze (konta użytkowników utworzone bezpośrednio na platformie Azure), które mają zasady haseł linii bazowej lub konta hybrydowe (konta użytkowników z lokalnej usługi Azure AD, które są zgodne z lokalnymi zasadami haseł). W przypadku korzystania z uwierzytelniania opartego na hasłach usługa Azure AD zapewnia funkcję ochrony hasłem, która uniemożliwia użytkownikom ustawianie haseł, które są łatwe do odgadnięcia. Firma Microsoft oferuje globalną listę zakazanych haseł, które są aktualizowane na podstawie danych telemetrycznych, a klienci mogą rozszerzyć listę na podstawie ich potrzeb (takich jak znakowanie, odwołania kulturowe itp.). Ta ochrona hasłem może być używana w przypadku kont tylko w chmurze i hybrydowych.

Uwierzytelnianie oparte na poświadczeniach hasła jest podatne na popularne metody ataków. Aby zapewnić wyższy poziom zabezpieczeń, należy użyć silnego uwierzytelniania, takiego jak uwierzytelnianie wieloskładnikowe i zasady silnego hasła. W przypadku aplikacji innych firm i usług portalu Marketplace, które mogą mieć domyślne hasła, należy je zmienić przy początkowej konfiguracji usługi.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że są używane najwyższego poziomu metod silnego uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem Azure Active Directory](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: pulpit wirtualny systemu Windows jest zintegrowany z usługą Azure Active Directory (Azure AD), która zapewnia następujące źródła danych:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitorem, wskaźnikiem produktów platformy Azure lub systemami zarządzania informacjami o zabezpieczeniach i zdarzeniach innych firm (SIEM). Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../security-center/alerts-reference.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: pulpit wirtualny systemu Windows obsługuje dostęp warunkowy z Azure Active Directory (Azure AD) w celu uzyskania szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika. Na przykład w celu uzyskania dostępu może być wymagane logowanie do użytkowników z określonych zakresów adresów IP. 

Ponadto szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md) 

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurowanie zarządzania sesją uwierzytelniania z użyciem dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Informacje o konfiguracji dostępu warunkowego dotyczące pulpitu wirtualnego systemu Windows można znaleźć tutaj](set-up-mfa.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: pulpit wirtualny systemu Windows korzysta z kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy. Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do dostępu o znaczeniu krytycznym dla firmy, takich jak kontrolery domena usługi Active Directory, narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach o krytycznym znaczeniu dla firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą potencjalnie natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access) 

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Minimalne uprawnienia administratora niezbędne do zarządzania pulpitem wirtualnym systemu Windows](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: pulpit wirtualny systemu Windows korzysta z kont usługi Azure Active Directory (Azure AD) do zarządzania zasobami, przeglądania kont użytkowników i przypisywania dostępu regularnie, aby zapewnić, że konta i ich dostęp są prawidłowe.

Za pomocą przeglądów dostępu w usłudze Azure AD można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji firmowych i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont.

Ponadto usługa Azure Privileged Identity Management może być również skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

Niektóre usługi platformy Azure obsługują lokalnych użytkowników i role, które nie są zarządzane za pomocą usługi Azure AD. Trzeba będzie osobno zarządzać tymi użytkownikami.

- [Wbudowane role dla pulpitu wirtualnego systemu Windows](rbac.md)

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: pulpit wirtualny systemu Windows używa usługi Azure Active Directory (Azure AD) do zarządzania swoimi zasobami. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: pulpit wirtualny systemu Windows jest zintegrowany z usługą Azure Active Directory (Azure AD) w celu zarządzania swoimi zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcia. W przypadku dodatkowych, obsługiwane są również podwójne lub wieloetapowe zatwierdzenia.

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówki**: zabezpieczone i izolowane stacje robocze są kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. 

Użyj Azure Active Directory (Azure AD), usługi Microsoft Defender Advanced Threat Protection (ATP) lub Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczona stacja robocza może być centralnie zarządzana, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: pulpit wirtualny systemu Windows jest zintegrowany z kontrolą dostępu opartą na rolach (Azure RBAC) na potrzeby zarządzania zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal. 

Uprawnienia przypisane do zasobów przy użyciu usługi Azure RBAC powinny być zawsze ograniczone do tych, które są wymagane przez role. Stanowi to uzupełnienie podejścia just in Time (JIT) Privileged Identity Management (PIM) z Azure Active Directory (Azure AD) i powinno być okresowo weryfikowane.

Ponadto przy użyciu wbudowanych ról można przydzielić uprawnienia i w razie potrzeby tworzyć tylko role niestandardowe.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wbudowane role dla pulpitu wirtualnego systemu Windows](rbac.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft  

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, pulpit wirtualny systemu Windows obsługuje skrytka klienta, aby zapewnić interfejs umożliwiający przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta.

- [Opis Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

**Wskazówki**: odkrywanie, klasyfikowanie i etykietowanie poufnych danych, dzięki czemu można zaprojektować odpowiednie kontrolki. Ma to na celu zapewnienie, że poufne informacje są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne w organizacji.

Użyj Azure Information Protection (i skojarzonego z nim narzędzia do skanowania), aby uzyskać informacje poufne w dokumentach pakietu Office na platformie Azure, lokalnie, Office 365 i innych lokalizacjach.

Usługa Azure SQL Information Protection może pomóc w klasyfikacji i etykietowaniu informacji przechowywanych w bazach danych Azure SQL Database.

- [Tagowanie informacji poufnych przy użyciu usługi Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Jak wdrożyć usługę Azure SQL Data Discovery](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: Ochrona poufnych danych przez ograniczenie dostępu przy użyciu Access Control opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w SQL i innych bazach danych).

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być dostosowane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa powinna być również oparta na lokalizacji poufnych lub krytycznych danych i systemów.

Firma Microsoft traktuje całą zawartość klienta jako poufną i chroni przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft zaimplementowała pewne domyślne mechanizmy kontroli i możliwości ochrony danych.

- [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>OD-3: Monitorowanie nieautoryzowanego transferu danych poufnych

**Wskazówki**: Monitoruj pod kątem nieautoryzowanego transferu danych do lokalizacji znajdujących się poza kontrolą i widocznością przedsiębiorstwa. Zazwyczaj obejmuje to monitorowanie pod kątem nietypowych działań (dużych lub nietypowych transferów), które mogą wskazywać na nieautoryzowaną eksfiltrację danych.

Funkcje zaawansowanej ochrony przed zagrożeniami (ATP) w usłudze Azure Storage i Azure SQL ATP mogą otrzymywać alerty dotyczące nietypowego transferu informacji, co oznacza, że mogą to być nieautoryzowane transfery informacji poufnych.

Usługa Azure Information Protection (AIP) oferuje funkcje monitorowania informacji, które zostały sklasyfikowane i oznaczone etykietami.

Używaj rozwiązań do ochrony przed utratą danych, takich jak oparte na hostach, aby wymusić środków wykrywających i/lub kontrolę prewencyjną, aby zapobiec eksfiltracji danych.

- [Włączanie usługi Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [Włączanie usługi Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Do wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Użyj spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o oprogramowaniu na Virtual Machines. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać dostęp do daty instalacji i innych informacji, Włącz diagnostykę na poziomie gościa i Przenieś dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md) 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy do inspekcji i ograniczania usług, które użytkownicy mogą inicjować w Twoim środowisku. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Za pomocą usługi Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>ZZ-4: zapewnienie bezpieczeństwa zarządzania cyklem życia zasobów

**Wskazówki**: nie dotyczy. Pulpitu wirtualnego systemu Windows nie można używać w celu zapewnienia bezpieczeństwa zasobów w procesie zarządzania cyklem życia. Klient jest odpowiedzialny za obsługę atrybutów i konfiguracji sieci zasobów, które są uważane za duże znaczenie. 

Zaleca się, aby klient utworzył proces przechwytywania atrybutów i zmian konfiguracji sieci, mierzyć wpływ zmian i tworzyć zadania korygowania, zgodnie z potrzebami.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Używaj tylko zatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Użyj spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na maszynach wirtualnych. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać dostęp do daty instalacji i innych informacji, Włącz diagnostykę na poziomie gościa i Przenieś dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanej funkcji wykrywania zagrożeń Azure Security Center i Włącz usługę Azure Defender (na przykład zaawansowanej ochrony przed zagrożeniami na platformie Azure) dla zasobów pulpitu wirtualnego systemu Windows. Usługa Azure Defender dla systemu Windows Virtual Desktop oferuje dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do zasobów pulpitu wirtualnego systemu Windows lub korzystania z nich.

Przekazuj wszystkie dzienniki z pulpitu wirtualnego systemu Windows do rozwiązania do zarządzania zdarzeniami zabezpieczeń (SIEM), których można użyć do skonfigurowania niestandardowych wykryć zagrożeń. Upewnij się, że monitorujesz różne typy zasobów platformy Azure pod kątem potencjalnych zagrożeń i anomalii. Skup się na uzyskiwaniu alertów o wysokiej jakości, aby zmniejszyć liczbę fałszywych dodatnich dla analityków do sortowania. Alerty mogą być źródłem danych dziennika, agentów lub innych danych.

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection) 

- [Przewodnik dotyczący podręcznika Azure Security Center alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](../sentinel/tutorial-detect-threats-custom.md) 

- [Analiza zagrożeń cybernetycznymi z użyciem platformy Azure](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Azure Active Directory (Azure AD) udostępniają następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, kontrolą platformy Azure, innymi informacjami o zabezpieczeniach i narzędziami do zarządzania zdarzeniami (Siem), a także do dalszych zaawansowanych przypadków użycia monitorowania i analizy:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia i przestarzałych kont w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami w Azure Security Center może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: pulpit wirtualny systemu Windows nie produkuje ani nie przetwarza dzienników zapytań usługi nazw domen (DNS). Jednak zasoby zarejestrowane w usłudze mogą generować dzienniki przepływów.

Włącz i zbierz dzienniki zasobów i przepływów sieciowych grup zabezpieczeń, Dzienniki zapory platformy Azure i Dzienniki zapory aplikacji sieci Web (WAF) na potrzeby analizy zabezpieczeń, aby obsługiwać badania zdarzeń, łowiectwo zagrożeń oraz generowanie alertów zabezpieczeń. Dzienniki przepływów można wysłać do obszaru roboczego Log Analytics Azure Monitor, a następnie użyć Analiza ruchu w celu uzyskania szczegółowych informacji.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Dzienniki i metryki usługi Azure Firewall](/azure/firewall/logs-and-metrics) 

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md) 

- [Rozwiązania do monitorowania sieci platformy Azure w Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie włączane, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów pulpitu wirtualnego systemu Windows z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisany właściciel danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia służące do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub zarządzania zdarzeniami zabezpieczeń innych firm (SIEM). Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Wykrywanie i analiza — tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówka**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można wyciągać wnioski z przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą oni tracić czasu na wyniki fałszywie dodatnie. 

Alerty o wysokiej jakości można tworzyć na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności oraz narzędzi przeznaczonych do generowania i czyszczenia alertów przez połączenie i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

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

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

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

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>WA-3: Ustanów bezpieczne konfiguracje dla zasobów obliczeniowych

**Wskazówki**: Użyj Azure Security Center i Azure Policy do ustanowienia bezpiecznych konfiguracji dla wszystkich zasobów obliczeniowych, w tym maszyn wirtualnych, kontenerów i innych.

Możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md) 

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md) 

- [Środowisko usługi Windows Virtual Desktop](environment-setup.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: utrzymywanie bezpiecznych konfiguracji dla zasobów obliczeniowych

**Wskazówki**: Użyj Azure Security Center i Azure Policy do regularnego oceniania i korygowania zagrożeń związanych z konfiguracją zasobów obliczeniowych platformy Azure, w tym maszyn wirtualnych, kontenerów i innych. Ponadto można używać szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację. Szablony maszyn wirtualnych firmy Microsoft połączone z konfiguracją stanu Azure Automation mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń.

Obrazy maszyn wirtualnych portalu Azure Marketplace opublikowane przez firmę Microsoft są zarządzane i obsługiwane przez firmę Microsoft.

Azure Security Center może również skanować luki w zabezpieczeniach w obrazie kontenera i przeprowadzać ciągłe monitorowanie konfiguracji platformy Docker w kontenerach względem testu porównawczego platformy Docker firmy. Na stronie rekomendacje Azure Security Center można przeglądać zalecenia i rozwiązywać problemy.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu ARM](../virtual-machines/windows/ps-template.md) 

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md) 

- [Zabezpieczenia kontenerów w usłudze Security Center](../security-center/container-security.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: bezpieczne przechowywanie niestandardowych obrazów systemów operacyjnych i kontenerów

**Wskazówki**: pulpit wirtualny systemu Windows umożliwia klientom zarządzanie obrazami systemu operacyjnego. Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby mieć pewność, że dostęp do obrazów niestandardowych mają tylko autoryzowani użytkownicy. Użyj galerii obrazów udostępnionych platformy Azure, możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usługi lub grupom Active Directory w organizacji. Przechowuj obrazy kontenerów w Azure Container Registry i używaj RBAC, aby mieć pewność, że dostęp mają tylko autoryzowani użytkownicy.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Omówienie galerii obrazów udostępnionych](/azure/virtual-machines/windows/shared-image-galleries)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: wykonywanie ocen luk w zabezpieczeniach oprogramowania

**Wskazówki**: pulpit wirtualny systemu Windows umożliwia wdrażanie własnych maszyn wirtualnych i rejestrowanie ich w usłudze oraz korzystanie z usługi SQL Database w środowisku.

Pulpit wirtualny systemu Windows może korzystać z rozwiązań innych firm w celu przeprowadzania ocen luk w zabezpieczeniach na urządzeniach sieciowych i aplikacjach sieci Web. Podczas przeprowadzania skanowania zdalnego nie należy używać pojedynczego, bezterminowego konta administratora. Rozważ zaimplementowanie metody inicjowania obsługi JIT dla konta skanowania. Poświadczenia dla konta skanowania powinny być chronione, monitorowane i używane tylko w celu skanowania w poszukiwaniu luk w zabezpieczeniach.

W razie potrzeby należy wyeksportować wyniki skanowania w regularnych odstępach czasu i porównać wyniki z poprzednimi skanami, aby sprawdzić, czy luki zostały skorygowane.

Postępuj zgodnie z zaleceniami Azure Security Center, aby przeprowadzać oceny luk w zabezpieczeniach na maszynach wirtualnych platformy Azure (i serwerach SQL). Azure Security Center ma wbudowany skaner luk w zabezpieczeniach dla maszyn wirtualnych, obrazów kontenerów i bazy danych SQL.

W razie potrzeby Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki z poprzednimi skanami, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z zaleceń dotyczących zarządzania lukami zalecanymi przez Azure Security Center można przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Zintegrowany skaner luk w zabezpieczeniach dla maszyn wirtualnych](/azure/security-center/built-in-vulnerability-assessment) 
- [Ocena luk w zabezpieczeniach SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>SL-7: szybkie i automatyczne korygowanie luk w zabezpieczeniach oprogramowania

**Wskazówki**: pulpit wirtualny systemu Windows nie jest używany ani nie wymaga żadnego oprogramowania innych firm. Jednak pulpit wirtualny systemu Windows umożliwia wdrażanie własnych maszyn wirtualnych i rejestrowanie ich w usłudze.

Użyj Update Management Azure Automation lub rozwiązania innej firmy, aby upewnić się, że najnowsze aktualizacje zabezpieczeń zostaną zainstalowane na maszynach wirtualnych z systemem Windows Server. W przypadku maszyn wirtualnych z systemem Windows upewnij się, że Windows Update została włączona i ustawiona na automatyczne aktualizowanie.

Użyj rozwiązania do zarządzania poprawkami innych firm dla oprogramowania innej firmy lub System Center Updates Publisher dla Configuration Manager.

- [Jak skonfigurować Update Management dla maszyn wirtualnych na platformie Azure](/azure/automation/update-management/overview) 

- [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](/azure/automation/update-management/manage-updates-for-vm)

- [Konfigurowanie usługi Microsoft Endpoint Configuration Manager dla pulpitu wirtualnego systemu Windows](configure-automatic-updates.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówki**: pulpit wirtualny systemu Windows nie pozwala klientom na wykonywanie własnych testów penetracji w swoich zasobach pulpitu wirtualnego systemu Windows.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="endpoint-security"></a>Zabezpieczenia punktu końcowego

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: zabezpieczenia punktu końcowego](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: korzystanie z wykrywania i odpowiedzi punktów końcowych (EDR)

**Wskazówki**: pulpit wirtualny systemu Windows nie zapewnia żadnych określonych funkcji procesów wykrywania i reagowania punktów końcowych (EDR). Jednak zasoby zarejestrowane w usłudze mogą korzystać z możliwości wykrywania i reagowania punktów końcowych. 

Włącz możliwości wykrywania i reagowania punktów końcowych dla serwerów i klientów, a następnie Zintegruj je z rozwiązaniami do zarządzania informacjami o zabezpieczeniach i procesami operacji zabezpieczeń (SIEM).

Zaawansowana ochrona przed zagrożeniami z usługi Microsoft Defender zapewnia możliwości wykrywania i reagowania punktów końcowych w ramach platformy zabezpieczeń punktu końcowego przedsiębiorstwa, aby zapobiegać zaawansowanym zagrożeniom, wykrywać je i reagować na nie.

- [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Usługa Microsoft Defender ATP dla serwerów z systemem Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Usługa Microsoft Defender ATP dla serwerów spoza systemu Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Usługa Microsoft Defender ATP dla nietrwałej infrastruktury pulpitu wirtualnego](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Użyj centralnie zarządzanych nowoczesnych programów chroniących przed złośliwym oprogramowaniem

**Wskazówki**: Ochrona zasobów pulpitu wirtualnego systemu Windows przy użyciu centralnie zarządzanego i nowoczesnego rozwiązania chroniącego przed złośliwym oprogramowaniem w czasie rzeczywistym i okresowego skanowania.

Azure Security Center może automatycznie identyfikować użycie wielu popularnych rozwiązań chroniących przed złośliwym oprogramowaniem dla maszyn wirtualnych i zgłaszać stan działania programu Endpoint Protection i podejmować zalecenia.

Program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services to domyślne oprogramowanie chroniące przed złośliwym oprogramowaniem dla maszyn wirtualnych z systemem Windows. Ponadto w celu wykrywania złośliwego oprogramowania przekazanego do kont usługi Azure Storage można użyć wykrywania zagrożeń z Azure Security Centerami dla usług danych.

- [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md) 

- [Obsługiwane rozwiązania programu Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Upewnij się, że podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane szybko i spójnie.

Postępuj zgodnie z zaleceniami w Azure Security Center: "COMPUTE &amp; Apps", aby upewnić się, że wszystkie maszyny wirtualne i/lub kontenery są aktualne przy użyciu najnowszych sygnatur.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft automatycznie zainstaluje najnowsze aktualizacje sygnatur i aparatu.

- [Jak wdrożyć program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md) 

- [Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: tworzenie i przywracanie kopii zapasowych](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

**Wskazówki**: Upewnij się, że tworzysz kopię zapasową systemów i danych, aby zachować ciągłość działania po nieoczekiwanym zdarzeniu. Powinno to być wskazówkami dla celów punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO).

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowych (np. maszyny wirtualne platformy Azure, SQL Server, bazy danych HANA lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

W przypadku wyższego poziomu nadmiarowości można włączyć opcję magazynu geograficznie nadmiarowego, aby replikować dane kopii zapasowej do regionu pomocniczego i odzyskać przy użyciu operacji przywracania między regionami.

- [Ciągłość działalności biznesowej i odzyskiwanie po awarii w skali przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Jak włączyć Azure Backup](/azure/backup/) 

- [Jak włączyć przywracanie między regionami](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Jak skonfigurować plan ciągłości działania i odzyskiwania po awarii w programie Virtual Desktop systemu Windows](disaster-recovery.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

**Wskazówki**: Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.

W przypadku zwykłej kopii zapasowej usługi Azure dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure. Możesz zaszyfrować kopię zapasową przy użyciu klucza zarządzanego przez klienta. W takim przypadku upewnij się, że ten klucz zarządzany przez klienta w magazynie kluczy należy również do zakresu kopii zapasowych.

Użyj kontroli dostępu opartej na rolach w Azure Backup, Azure Key Vault lub innych zasobów, aby chronić kopie zapasowe i klucze zarządzane przez klienta. Dodatkowo można włączyć zaawansowane funkcje zabezpieczeń, aby wymagać uwierzytelniania wieloskładnikowego przed zmianą lub usunięciem kopii zapasowych.

Omówienie funkcji zabezpieczeń w programie Azure Backup/Azure/Backup/Security-Overview 

- [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta](/azure/backup/encryption-at-rest-with-cmk) 

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe przed atakami](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikowanie wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki**: zaleca się regularne Weryfikowanie integralności danych na nośniku kopii zapasowych przez wykonanie procesu przywracania danych w celu zapewnienia prawidłowego działania kopii zapasowej.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Implementacja zabezpieczeń](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](/azure/automation/update-management/overview)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówka**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i eliminować zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analitykom alerty wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na integracji i ręcznych działaniach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji w zakresie operacji zabezpieczeń (SecOps) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

-   Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
