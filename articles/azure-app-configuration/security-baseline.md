---
title: Azure Security Baseline dla konfiguracji aplikacji platformy Azure
description: Linia bazowa zabezpieczeń konfiguracji aplikacji platformy Azure zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79a99ad37c526103fc3068562d62ed40defc983b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532384"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure Security Baseline dla konfiguracji aplikacji platformy Azure

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do konfiguracji aplikacji platformy Azure. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące konfiguracji aplikacji platformy Azure. Wykluczenia **nie mają** zastosowania do konfiguracji aplikacji platformy Azure.

Aby dowiedzieć się, jak usługa Azure App Configuration jest całkowicie mapowana na test porównawczy zabezpieczeń Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń konfiguracji aplikacji platformy Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: usługa Azure App Configuration nie wdraża żadnych zasobów bezpośrednio w sieci wirtualnej. Ponieważ usługa nie jest wdrażana w sieci wirtualnej, nie można wykorzystać niektórych funkcji sieciowych do zabezpieczenia wewnętrznego ruchu usługi, takiego jak: sieciowe grupy zabezpieczeń, tabele tras lub inne urządzenia sieciowe, takie jak Zapora platformy Azure. Jednak konfiguracja aplikacji umożliwia używanie prywatnych punktów końcowych w celu bezpiecznego nawiązywania połączenia z konfiguracją aplikacji platformy Azure z sieci wirtualnej.

Za pomocą wskaźnikowego platformy Azure można wykrywać użycie starszych, niezabezpieczonych protokołów, takich jak SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niepodpisane powiązania LDAP oraz słabe szyfry w protokole Kerberos.

- [Używanie prywatnych punktów końcowych dla konfiguracji aplikacji platformy Azure](concept-private-endpoint.md)

- [Skoroszyt niezabezpieczonych protokołów na platformie Azure](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

**Wskazówki**: usługa Azure App Configuration obsługuje używanie prywatnych punktów końcowych do bezpiecznego wysyłania danych za pośrednictwem prywatnego linku. Użyj usługi Azure ExpressRoute lub wirtualnej sieci prywatnej (VPN) platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i usługi Azure ExpressRoute.

Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj komunikacji równorzędnej sieci wirtualnej. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure.

- [Co to są modele łączności ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Omówienie usługi Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp w sieci prywatnej do usług platformy Azure

**Wskazówki**: Użyj prywatnego linku platformy Azure, aby umożliwić prywatny dostęp do konfiguracji aplikacji platformy Azure z sieci wirtualnych bez przekraczania Internetu.

Dostęp prywatny to dodatkowe środki ochrony, które oprócz uwierzytelniania i zabezpieczeń ruchu oferowane przez usługi platformy Azure.

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Jak skonfigurować link prywatny w konfiguracji aplikacji platformy Azure](concept-private-endpoint.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: podczas uzyskiwania dostępu do wartości konfiguracyjnych za pośrednictwem sieci wirtualnej Chroń swoje zasoby przed atakami z sieci zewnętrznych, w tym atakami typu "odmowa usługi" (DDoS), atakami specyficznymi dla aplikacji i potencjalnie złośliwym ruchem internetowym. Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. Chroń zasoby przed atakami DDoS, włączając ochronę standardową DDoS w sieciach wirtualnych platformy Azure. Użyj Azure Security Center do wykrywania niepowodowanych problemów z konfiguracją związanych z zasobami związanymi z siecią.

Konfiguracja aplikacji platformy Azure nie jest przeznaczona do uruchamiania aplikacji sieci Web, ale zawiera konfigurację dla tych aplikacji sieci Web. Nie trzeba konfigurować żadnych dodatkowych ustawień ani wdrażać dodatkowych usług sieciowych, aby chronić je przed atakami z sieci zewnętrznych ukierunkowanymi na aplikacje sieci Web.

- [Dokumentacja zapory platformy Azure](../firewall/index.yml)

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: Używanie zapory platformy Azure z filtrowaniem opartym na analizie zagrożeń w celu wysyłania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. Gdy jest wymagana inspekcja ładunku, można wdrożyć rozwiązanie identyfikatorów/adresów IP innych firm z witryny Azure Marketplace z możliwościami inspekcji ładunku. Alternatywnie możesz wybrać użycie identyfikatorów/adresów IP opartych na hoście lub rozwiązania do wykrywania punktów końcowych (EDR) opartych na hoście w połączeniu z lub zamiast identyfikatorów/adresów IP opartych na sieci.

Uwaga: Jeśli posiadasz przepisy prawne lub inne wymagania dotyczące używania identyfikatorów/adresów IP, upewnij się, że jest zawsze dostrojona, aby zapewnić wysoką jakość alertów w rozwiązaniu SIEM.

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Portal Azure Marketplace obejmuje możliwości identyfikatorów innych firm](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Funkcja Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Azure Virtual Network, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure skonfigurowanej dla zasobów konfiguracji aplikacji. Podczas tworzenia reguł zabezpieczeń dla ruchu wychodzącego w sieci aplikacji można użyć znacznika usługi "AppConfiguration" zamiast konkretnych adresów IP. Określając nazwę tagu usługi w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: Konfiguracja aplikacji platformy Azure jest zintegrowana z Azure Active Directory (Azure AD), która jest domyślną usługą zarządzania tożsamościami i dostępem platformy Azure. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:
- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.
- Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia wskaźnik bezpieczeństwa tożsamości, który pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj ze wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń.

Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- Właściciel danych konfiguracji aplikacji: Użyj tej roli, aby przyznać dostęp do odczytu/zapisu/usuwania do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.

- Czytnik danych konfiguracji aplikacji: Użyj tej roli, aby zapewnić dostęp do odczytu do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.

- Współautor: Ta rola służy do zarządzania zasobem konfiguracji aplikacji. Podczas gdy dane konfiguracji aplikacji są dostępne przy użyciu kluczy dostępu, ta rola nie udziela bezpośredniego dostępu do danych za pomocą usługi Azure AD.

- Czytelnik: Ta rola umożliwia dostęp do odczytu do zasobu konfiguracji aplikacji. Nie powoduje to przyznania dostępu do kluczy dostępu zasobu ani do danych przechowywanych w konfiguracji aplikacji.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure AD](concept-enable-rbac.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: Użyj tożsamości zarządzanych przez platformę Azure, aby uzyskać dostęp do konfiguracji aplikacji platformy Azure z kont nieludzkich, takich jak inne usługi platformy Azure. Zalecane jest korzystanie z funkcji tożsamości zarządzanej platformy Azure zamiast tworzenia bardziej zaawansowanych kont ludzkich w celu uzyskania dostępu do zasobów lub ich wykonywania w celu ograniczenia potrzeb zarządzania dodatkowymi poświadczeniami. Do konfiguracji aplikacji platformy Azure można także przypisać tożsamość zarządzaną w celu natywnego uwierzytelnienia w innych usługach/zasobach platformy Azure, które obsługują uwierzytelnianie usługi Azure AD. Może to być przydatne, aby zapewnić łatwy dostęp z konfiguracji aplikacji do Azure Key Vault podczas pobierania wpisów tajnych. W przypadku korzystania z tożsamości zarządzanych tożsamość jest zarządzana przez platformę Azure i nie wymaga podawania ani rotacji kluczy tajnych.

Usługa Azure App Configuration obsługuje aplikacje mające dwa typy tożsamości:
- Tożsamość przypisana do systemu jest powiązana z zasobem konfiguracji. Jest ona usuwana, jeśli zasób konfiguracji został usunięty. Zasób konfiguracji może mieć tylko jedną tożsamość przypisaną do systemu.
- Tożsamość przypisana przez użytkownika to autonomiczny zasób platformy Azure, który można przypisać do tego zasobu konfiguracji. Zasób konfiguracji może mieć wiele tożsamości przypisanych do użytkownika.

Gdy nie można wykorzystać tożsamości zarządzanych, Utwórz jednostkę usługi z ograniczonymi uprawnieniami na poziomie zasobu konfiguracji aplikacji platformy Azure. Skonfiguruj te jednostki usługi z poświadczeniami certyfikatów i wróć tylko do wpisów tajnych klienta. W obu przypadkach Azure Key Vault mogą być używane w połączeniu z tożsamościami zarządzanymi przez platformę Azure, dzięki czemu środowisko uruchomieniowe (np. funkcja platformy Azure) może pobrać poświadczenie z magazynu kluczy.

- [Jak używać tożsamości zarządzanych do konfiguracji aplikacji platformy Azure](overview-managed-identity.md)

- [Tożsamości zarządzane przez platformę Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych](howto-integrate-azure-managed-service-identity.md)

- [Jednostka usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Tworzenie jednostki usługi przy użyciu certyfikatów](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Użyj Azure Key Vault do rejestracji podmiotu zabezpieczeń](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: usługa Azure App Configuration używa usługi Azure Active Directory (Azure AD), aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Usługa Azure AD umożliwia logowanie jednokrotne (SSO) w celu zarządzania usługą konfiguracji aplikacji za pośrednictwem Azure Portal przy użyciu wszelkich synchronizowanych tożsamości Active Directory firmowych. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i większej kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: usługa Azure App Configuration używa Azure Active Directory, które obsługuje kontrolę silnego uwierzytelniania za pośrednictwem uwierzytelniania wieloskładnikowego (MFA) i silnych metod bezhaseł.
- Uwierzytelnianie wieloskładnikowe — włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w usłudze Azure Security Center, aby zapoznać się z najlepszymi rozwiązaniami w konfiguracji usługi MFA. Usługę MFA można wymusić na wszystkich lub wybranych użytkownikach albo na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że jest używana najwyższa Metoda silnego uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

Uwaga: uwierzytelnianie wieloskładnikowe można wymusić na kontach użytkowników, które uzyskują dostęp do konfiguracji aplikacji i zarządzają nimi, ale nie na kontach usług programistycznych. W miarę możliwości używaj uwierzytelniania bez hasła, takiego jak zarządzane tożsamości, i Wymuszaj uwierzytelnianie wieloskładnikowe na wszystkich kontach użytkowników.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Wprowadzenie do opcji uwierzytelniania bez hasła dla usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Konfiguracja aplikacji platformy Azure jest zintegrowana z Azure Active Directory, w którym znajdują się następujące źródła danych:

-   Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

-   Dzienniki inspekcji — umożliwia śledzenie przy użyciu dzienników dla wszystkich zmian wprowadzonych za pomocą różnych funkcji w usłudze Azure AD. Przykłady rejestrowanych zmian dzienniki inspekcji obejmują dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

-   Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

-   Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia i przestarzałe konta w subskrypcji. 

Azure Advanced Threat Protection (ATP) to rozwiązanie zabezpieczeń, które umożliwia używanie lokalnych sygnałów Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, złamanych tożsamości i złośliwych działań niejawnych.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../security-center/alerts-reference.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Łączenie danych z Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: usługa Azure App Configuration obsługuje dostęp warunkowy Azure Active Directory (Azure AD) w celu uzyskania bardziej szczegółowej kontroli dostępu opartej na warunkach zdefiniowanych przez użytkownika, takich jak nazwy logowania użytkowników z określonych zakresów adresów IP, będą musieli używać usługi MFA do logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia. Te zasady dostępu warunkowego będą stosowane tylko do kont użytkowników, które są uwierzytelniane w usłudze Azure AD w celu uzyskania dostępu do usługi konfiguracji aplikacji i zarządzania nią, ale nie będą miały zastosowania do jednostek usługi ani parametrów połączenia łączących się z zasobem konfiguracji.

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md)

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurowanie zarządzania sesją uwierzytelniania z użyciem dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

**Wskazówki**: usługa Azure App Configuration umożliwia klientom przechowywanie konfiguracji, które mogą potencjalnie zawierać tożsamości lub wpisy tajne. Zaleca się implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w ramach konfiguracji. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

Użyj usługi konfiguracji aplikacji platformy Azure razem z Azure Key Vault. Zapisz dowolne poświadczenia w Key Vault, a następnie połącz się z tymi poświadczeniami, tworząc odwołanie Key Vault do zasobu konfiguracji aplikacji. Gdy konfiguracja aplikacji tworzy te odwołania, przechowuje ona identyfikatory URI wartości Key Vault, a nie same wartości. Aplikacje mogą łączyć się z konfiguracją aplikacji w celu pobrania poświadczeń z Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego wpisu tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Samouczek dotyczący używania odwołań Key Vault w aplikacji ASP.NET Core](use-key-vault-references-dotnet-core.md)

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrona i ograniczanie użytkowników z wysokim poziomem uprawnień

**Wskazówki**: Ogranicz liczbę wysoce uprzywilejowanych kont lub ról i Chroń te konta na poziomie podwyższonego poziomu, ponieważ użytkownicy z tym uprawnieniem mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

Klucze dostępu są wysoce uprzywilejowane i powinny być regularnie obracane jako najlepsze rozwiązanie w zakresie zabezpieczeń. Klucze dostępu zawierają parametry połączenia, które zawierają informacje o poświadczeniach i są uważane za wpisy tajne. Te klucze tajne muszą być przechowywane w Azure Key Vault, a kod musi być uwierzytelniany do Key Vault, aby je pobrać. Klucze dostępu mogą zapewnić dostęp do odczytu i zapisu lub tylko do odczytu do aplikacji. Upewnij się, że wydano prawidłowy typ klucza dostępu, aby zapobiec nieautoryzowanemu dostępowi. Aby zwiększyć bezpieczeństwo, użyj funkcji tożsamości zarządzane w usłudze Azure AD. Jest to wymagane tylko w przypadku, gdy aplikacje mają adres URL punktu końcowego konfiguracji w celu uzyskania dostępu do wartości konfiguracyjnych.

- [Najlepsze rozwiązania dotyczące konfiguracji aplikacji](howto-best-practices.md#app-configuration-bootstrap)

- [Uzyskiwanie dostępu do usługi App Configuration przy użyciu tożsamości zarządzanych](howto-integrate-azure-managed-service-identity.md)
- [Uprawnienia ról administratora w usłudze Azure AD](../active-directory/roles/permissions-reference.md)

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../active-directory/roles/security-planning.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: usługa Azure App Configuration korzysta z usługi Azure RBAC, aby izolować dostęp do systemów o krytycznym znaczeniu dla firmy przez ograniczenie kont, które mają przyznane dostęp uprzywilejowany. Usługa Azure RBAC jest obsługiwana przez konfigurację aplikacji na poziomie zasobu. Aby bezpiecznie silosować konfiguracje krytyczne dla działalności firmy, przechowuj te informacje w swoim własnym zasobie konfiguracji aplikacji. W ramach zasobu szczegółowy dostęp jest również dostępny za pomocą kont lub kluczy dostępu tylko do odczytu, a także etykietowania i tagowania.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Aby zintegrować RBAC przy użyciu usługi Azure AD z konfiguracją aplikacji](concept-enable-rbac.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: usługa Azure App Configuration używa kont usługi Azure Active Directory (Azure AD) do zarządzania swoimi zasobami, przeglądania kont użytkowników i przypisywania dostępu regularnie, aby zapewnić, że konta i ich dostęp są prawidłowe. 

Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- Właściciel danych konfiguracji aplikacji: Użyj tej roli, aby przyznać dostęp do odczytu/zapisu/usuwania do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.

- Czytnik danych konfiguracji aplikacji: Użyj tej roli, aby zapewnić dostęp do odczytu do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji

Za pomocą przeglądów dostępu w usłudze Azure AD można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw oraz przypisań ról, takich jak powyższe role konfiguracji aplikacji. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

Uwaga: tożsamości zarządzane są sugerowane w przypadku, gdy jest to możliwe do uwierzytelniania w konfiguracji aplikacji z innej usługi lub aplikacji. Należy zarządzać wszystkimi jednostkami usług lub parametrami połączenia skonfigurowanymi z dostępem do konfiguracji aplikacji oddzielnie, gdy zostanie użyta.

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](/azure/active-directory/governance/access-reviews-overview)

- [Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure AD](concept-enable-rbac.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: Konfiguracja aplikacji platformy Azure jest zintegrowana z Azure Active Directory, aby zarządzać swoimi zasobami. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: Konfiguracja aplikacji platformy Azure jest zintegrowana z Azure Active Directory, aby zarządzać swoimi zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówka**: Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak administratorzy, deweloperzy i operatorzy usług o kluczowym znaczeniu. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych związanych z konfiguracją aplikacji. Użyj usługi Azure Active Directory, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i/lub usługi Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe punkty odniesienia, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: Konfiguracja aplikacji platformy Azure jest zintegrowana z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla konfiguracji aplikacji platformy Azure. te role można spisować lub odszukiwać za pomocą takich narzędzi, jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. Uprawnienia przypisywane do zasobów z użyciem kontroli dostępu opartej na rolach platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez te role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:
- Właściciel danych konfiguracji aplikacji: Użyj tej roli, aby przyznać dostęp do odczytu/zapisu/usuwania do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.
- Czytnik danych konfiguracji aplikacji: Użyj tej roli, aby zapewnić dostęp do odczytu do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.

Przy użyciu wbudowanych ról można przydzielić uprawnienia i tworzyć role niestandardowe tylko wtedy, gdy jest to wymagane. 

Konfiguracja aplikacji obsługuje przechowywanie konfiguracji wielu aplikacji w jednym zasobie konfiguracyjnym aplikacji. Aby ograniczyć dostęp do informacji między aplikacjami, należy utworzyć zasób konfiguracji aplikacji na aplikację i odpowiednio skonfigurować usługę Azure RBAC.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure AD](concept-enable-rbac.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft  

**Wskazówki**: implementowanie procesu zatwierdzania w organizacji dla scenariuszy pomocy technicznej, w których firma Microsoft może potrzebować dostępu do danych konfiguracji aplikacji. Skrytka klienta nie jest obecnie dostępna w scenariuszach obsługi konfiguracji aplikacji.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: odnajdywanie, klasyfikowanie i etykietowanie danych poufnych

**Wskazówki**: odkrywanie, klasyfikowanie i etykietowanie poufnych danych, dzięki czemu można zaprojektować odpowiednie kontrolki, aby zapewnić, że poufne informacje są przechowywane, przetwarzane i przesyłane bezpiecznie przez systemy technologiczne organizacji. Etykieta dla informacji poufnych w formie tagowania jest obsługiwana w przypadku zasobów konfiguracji aplikacji, ale nie dla wartości konfiguracji zawartych w nich. Gdy aplikacja ma dostęp do odczytu lub odczytu/zapisu w magazynie konfiguracji, ma pełny dostęp do dowolnej konfiguracji w tym magazynie.

- [Tagowanie informacji poufnych przy użyciu usługi Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Tagowanie klasyfikacji danych na platformie Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: dla podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft zaimplementowała pewne domyślne mechanizmy kontroli i możliwości ochrony danych. Upewnij się, że klucze dostępu są regularnie obracane do zasobów konfiguracji aplikacji. Informacje o poświadczeniach z parametrów połączenia mogą być przechowywane w Azure Key Vault, a Twój kod musi być uwierzytelniany do Key Vault, aby je pobrać. Klucze dostępu mogą zapewnić dostęp do odczytu i zapisu lub tylko do odczytu do aplikacji. Upewnij się, że wydano prawidłowy typ klucza dostępu, aby zapobiec nieautoryzowanemu dostępowi. Aby zwiększyć bezpieczeństwo, użyj funkcji tożsamości zarządzane w usłudze Azure AD. Jest to wymagane tylko w przypadku, gdy aplikacje mają adres URL punktu końcowego konfiguracji w celu uzyskania dostępu do wartości konfiguracyjnych.

Ograniczanie dostępu przy użyciu kontroli dostępu opartej na rolach (Azure RBAC):

- Oddziel dane poufne do własnego zasobu konfiguracji aplikacji, a następnie przydziel odpowiednio zasady RBAC, aby włączyć tylko autoryzowany dostęp 

- Korzystanie z kontroli dostępu opartej na sieci

- Określone kontrolki usług platformy Azure (takie jak szyfrowanie w SQL i inne bazy danych) i zapewniają spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa.

- Strategię segmentacji przedsiębiorstwa powinna być również oparta na lokalizacji poufnych lub krytycznych danych i systemów.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory](concept-enable-rbac.md)

- [Szyfrowanie danych konfiguracji aplikacji](faq.md#does-app-configuration-encrypt-my-data)

- [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

**Wskazówki**: aby uzupełnić kontrolę dostępu, przesyłane dane powinny być chronione przed atakami "poza pasmem" przy użyciu szyfrowania. Pozwala to zagwarantować, że osoby atakujące nie mogą łatwo odczytywać ani modyfikować danych.

Konfiguracja aplikacji platformy Azure używa szyfrowania TLS dla wszystkich żądań HTTP. Infrastruktura platformy Azure zapewnia dodaną warstwę szyfrowania na poziomie sieci dla wszystkich żądań między centrami danych platformy Azure. Upewnij się, że ruch HTTP, który każdy Klient nawiązujący połączenie z zasobami konfiguracji aplikacji, może negocjować protokół TLS w wersji 1.2 lub nowszej.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Szyfrowanie poufnych danych nieużywanych

**Wskazówki**: aby uzupełnić kontrolę dostępu, dane przechowywane w spoczynku powinny być chronione przed atakami "poza pasmem" (takimi jak dostęp do magazynu bazowego) przy użyciu szyfrowania. Pozwala to zagwarantować, że osoby atakujące nie mogą łatwo odczytywać ani modyfikować danych.

Domyślnie platforma Azure zapewnia szyfrowanie danych. W przypadku wysoce poufnych danych masz możliwość zaimplementowania dodatkowego szyfrowania dla wszystkich zasobów platformy Azure, jeśli jest to możliwe. Platforma Azure domyślnie zarządza kluczami szyfrowania, ale platforma Azure udostępnia opcję zarządzania własnymi kluczami (klucze zarządzane przez klienta) na potrzeby konfiguracji aplikacji platformy Azure.

- [Szyfrowanie danych w konfiguracji aplikacji platformy Azure przy użyciu kluczy zarządzanych przez klienta](concept-customer-managed-keys.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Model szyfrowania i tabela zarządzania kluczami](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Szyfrowanie danych przy podwójnej obspoczynku na platformie Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Uwaga: Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają dostęp do stale aktualizowanego spisu zasobów na platformie Azure, takich jak konfiguracja aplikacji platformy Azure. Zespoły ds. zabezpieczeń często potrzebują tego spisu, aby oszacować potencjalne zagrożenie w organizacji do pojawiających się zagrożeń i jako dane wejściowe w celu ciągłego ulepszania zabezpieczeń. Utwórz grupę Azure Active Directory, aby zawierała autoryzowanego zespołu zabezpieczeń organizacji i przypisać im dostęp do odczytu do wszystkich zasobów konfiguracji aplikacji platformy Azure. może to być uproszczone przez pojedyncze przypisanie roli wysokiego poziomu w ramach subskrypcji.

Funkcja spisu Azure Security Center i wykres zasobów platformy Azure mogą wykonywać zapytania dotyczące i odnajdywania wszystkich zasobów w subskrypcjach, w tym usług platformy Azure, aplikacji i zasobów sieciowych.

Zastosuj Tagi do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: usługa Azure App Configuration obsługuje wdrożenia oparte na Azure Resource Manager i wymuszanie konfiguracji przy użyciu Azure Policy. Usługa Azure Policy pozwala przeprowadzić inspekcję i ograniczyć liczbę usług, które użytkownicy mogą aprowizować w danym środowisku. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Za pomocą usługi Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>ZZ-4: zapewnienie bezpieczeństwa zarządzania cyklem życia zasobów

**Wskazówki**: Utwórz lub zaktualizuj zasady zabezpieczeń, które dotyczą procesów zarządzania cyklem życia zasobów dla modyfikacji o dużym wpływie. Te modyfikacje obejmują zmiany, ale nie są ograniczone do: dostawcy tożsamości i dostępu, czułość danych, konfiguracja sieci i przypisanie uprawnień administracyjnych.

Usuń zasoby platformy Azure, gdy nie są już potrzebne. Upewnij się, że administratorzy regularnie przenoszą klucze dostępu, aby upewnić się, że tylko uwierzytelnieni użytkownicy mają dostęp do ich zasobów konfiguracyjnych.

- [Obróć klucze szyfrowania używane do konfiguracji aplikacji](concept-customer-managed-keys.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Konfiguracja aplikacji integruje się z Azure Active Directory (Azure AD). Zapewnia to następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformie Azure lub innymi narzędziami SIEM/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy:
- Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.
- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń Azure Security Center moduł ochrony przed zagrożeniami może również zbierać bardziej szczegółowe alerty zabezpieczeń warstw usług platformy Azure. Ta funkcja zapewnia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

Inną metodą uzyskiwania dostępu do zasobu konfiguracji konfiguracji aplikacji jest użycie kluczy dostępu. Muszą one być regularnie obrócone w celu zapewnienia, że żaden nieautoryzowany agent nie uzyska dostępu do zasobu konfiguracji. Obracanie ich można wykonać bezpośrednio w portalu w obszarze "klucze dostępu".

- [Aby umożliwić usłudze Azure App Configuration dostęp do innych chronionych zasobów usługi Azure AD przy użyciu tożsamości zarządzanej](overview-managed-identity.md)

- [Używanie tożsamości zarządzanych z konfiguracją aplikacji platformy Azure](howto-integrate-azure-managed-service-identity.md)

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Autoryzowanie dostępu do konfiguracji aplikacji platformy Azure przy użyciu usługi Azure AD](concept-enable-rbac.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: usługa Azure App Configuration nie wdraża żadnych zasobów bezpośrednio w sieci wirtualnej. Jednak konfiguracja aplikacji umożliwia używanie prywatnych punktów końcowych w celu bezpiecznego nawiązywania połączenia z konfiguracją aplikacji platformy Azure z sieci wirtualnej. Usługa Azure App Configuration nie produkuje ani nie przetwarza dzienników zapytań DNS, które trzeba włączyć.

Włącz rejestrowanie dla skonfigurowanych prywatnych punktów końcowych konfiguracji aplikacji, aby przechwycić:
- Dane przetworzone przez prywatny punkt końcowy (WE/OUT)
- Dane przetwarzane przez usługę linku prywatnego (WE/wychodzącym)
- Dostępność portów NAT

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Monitorowanie prywatnych linków platformy Azure](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie dostępne, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów konfiguracji aplikacji z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób. W przypadku konfiguracji aplikacji dzienniki aktywności są dostępne tylko na płaszczyźnie kontroli i są rozdzielone Azure Resource Manager (ARM). Rejestrowanie płaszczyzny danych po stronie klienta dla konfiguracji aplikacji nie jest obecnie obsługiwane. Dzienniki zasobów platformy Azure są również niedostępne do skonfigurowania.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: Upewnij się, że wszystkie konta magazynów lub log Analytics obszary robocze używane do przechowywania dzienników konfiguracji aplikacji mają ustawiony okres przechowywania dziennika zgodnie z przepisami obowiązującymi w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji.

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

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

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Ustanów bezpieczne konfiguracje dla usług platformy Azure 

**Wskazówki**: Konfiguracja aplikacji platformy Azure obsługuje poniższe zasady specyficzne dla usługi, które są dostępne w Azure Security Center do inspekcji i wymuszania konfiguracji zasobów platformy Azure. Tę konfigurację można skonfigurować w ramach inicjatyw Azure Security Center lub Azure Policy.
- Konfiguracja aplikacji powinna używać klucza zarządzanego przez klienta: klucze zarządzane przez klienta zapewniają ulepszoną ochronę danych, umożliwiając zarządzanie kluczami szyfrowania. Jest to często wymagane w celu spełnienia wymagań dotyczących zgodności.
- W przypadku konfiguracji aplikacji należy użyć prywatnego linku: połączenia prywatnego punktu końcowego umożliwiają klientom w sieci wirtualnej bezpieczny dostęp do konfiguracji aplikacji platformy Azure za pośrednictwem prywatnego linku.

Plany platformy Azure umożliwiają automatyzację wdrażania i konfigurowania usług i środowisk aplikacji, w tym szablonów usługi Azure Resources, kontrolek kontroli RBAC platformy Azure oraz zasad w ramach jednej definicji planu.

- [Więcej informacji na temat zasad konfiguracji aplikacji](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Praca z zasadami zabezpieczeń w Azure Security Center](../security-center/tutorial-security-policy.md)

- [Ilustracja implementacji Guardrails w strefie docelowej skalowania w przedsiębiorstwie](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Utrzymaj bezpieczne konfiguracje dla usług platformy Azure

**Wskazówki**: Użyj Azure Security Center do monitorowania linii bazowej konfiguracji i wymuszania przy użyciu Azure Policy. Azure Policy do konfiguracji aplikacji obejmuje: 
- Konfiguracja aplikacji powinna używać klucza zarządzanego przez klienta: klucze zarządzane przez klienta zapewniają ulepszoną ochronę danych, umożliwiając zarządzanie kluczami szyfrowania. Jest to często wymagane w celu spełnienia wymagań dotyczących zgodności.
- W przypadku konfiguracji aplikacji należy użyć prywatnego linku: połączenia prywatnego punktu końcowego umożliwiają klientom w sieci wirtualnej bezpieczny dostęp do konfiguracji aplikacji platformy Azure za pośrednictwem prywatnego linku.

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md) 

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: tworzenie i przywracanie kopii zapasowych](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmniejszanie ryzyka utraty kluczy

**Wskazówki**: Upewnij się, że masz środki, aby zapobiec utracie kluczy i je odzyskać. Włącz usuwanie nietrwałe i przeczyść ochronę w usłudze Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

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

-   Korzystanie z natywnych i zewnętrznych funkcji ochrony danych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie przesyłania i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

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

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)