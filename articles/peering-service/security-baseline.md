---
title: Podstawa zabezpieczeń platformy Azure dla Microsoft Azure usługi komunikacji równorzędnej
description: Linia bazowa zabezpieczeń usługi Microsoft Azure komunikacji równorzędnej zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315607"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Podstawa zabezpieczeń platformy Azure dla Microsoft Azure usługi komunikacji równorzędnej

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure usługi komunikacji równorzędnej. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Microsoft Azure usługi komunikacji równorzędnej.

> [!NOTE]
> **Kontrolki** nie mają zastosowania do Microsoft Azure usługi komunikacji równorzędnej lub dla których odpowiedzialności są wykluczane przez firmę Microsoft. Aby dowiedzieć się, jak Usługa Komunikacja równorzędna Microsoft Azure całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z **[plikiem mapowanie linii bazowej zabezpieczeń usługi peer Microsoft Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)**.

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: Usługa Komunikacja równorzędna nie oferuje natywnych usług wykrywania intruzów ani zapobiegania. Klienci powinni przestrzegać najlepszych rozwiązań w zakresie zabezpieczeń, takich jak używanie funkcji filtrowania opartego na analizie zagrożeń w zaporze platformy Azure, aby wysyłać alerty i blokować ruch z i do znanych, złośliwych adresów IP i domen, zgodnie z ich wymaganiami biznesowymi. Te adresy IP i domeny pochodzą ze źródła danych Threat-Intelligence firmy Microsoft. 

W przypadkach, gdy wymagana jest inspekcja ładunku, należy wdrożyć systemy wykrywania lub zapobiegania włamaniom innych firm (identyfikatory/adresy IP) z możliwościami inspekcji ładunku w portalu Azure Marketplace.  
Alternatywnie można użyć rozwiązania wykrywania i reagowania punktu końcowego opartego na hoście (EDR) w połączeniu z systemami wykrywania lub zapobiegania intruzom opartym na sieci.  

Jeśli istnieje wymóg prawny do korzystania z wykrywania wtargnięcia lub zapobiegania włamaniom, należy upewnić się, że jest on zawsze dostrojony do użycia lub zapewnienia wysokiej jakości alertów. 

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Portal Azure Marketplace obejmuje możliwości identyfikatorów innych firm](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Funkcja Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: przechowywanie spisu kont użytkowników z dostępem administracyjnym do płaszczyzny kontroli (na przykład Azure Portal) zasobów usługi Komunikacja równorzędna Microsoft Azure.

Użyj okienka tożsamość i kontrola dostępu (IAM) w Azure Portal subskrypcji, aby skonfigurować kontrolę dostępu opartą na rolach (Azure RBAC). Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w Azure Active Directory (Azure AD).

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: tożsamości zarządzane nie są obsługiwane w przypadku usługi komunikacji równorzędnej. W przypadku usług, które nie obsługują tożsamości zarządzanych, takich jak usługa komunikacji równorzędnej, użyj Azure Active Directory (Azure AD), aby utworzyć jednostkę usługi z ograniczonymi uprawnieniami na poziomie zasobu. 

- [Tożsamości zarządzane przez platformę Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Jednostka usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Tworzenie jednostki usługi przy użyciu certyfikatów](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: usługa komunikacji równorzędnej Microsoft Azure używa usługi Azure Active Directory (Azure AD), aby zapewnić zarządzanie tożsamościami i dostępem do zasobów platformy Azure. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. 

Logowanie jednokrotne służy do zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i większej kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: Włącz uwierzytelnianie wieloskładnikowe przy użyciu usługi Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem z Azure Security Center.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Użyj PRIVILEGED Identity Management (PIM) z usługą Azure Active Directory (Azure AD) w celu wygenerowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: korzystanie z dostępu warunkowego z Azure Active Directory (Azure AD) w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak wymaganie logowania użytkowników z określonych zakresów adresów IP w celu używania uwierzytelniania wieloskładnikowego. Szczegółowe zarządzanie sesjami uwierzytelniania może być również używane za pomocą zasad dostępu warunkowego usługi Azure AD dla różnych przypadków użycia. 

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md)

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Skonfiguruj zarządzanie sesją uwierzytelniania z użyciem dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

**Wskazówki**: Implementuj skaner poświadczeń w usłudze Azure DevOps, aby identyfikować poświadczenia używane w kodzie. Skaner poświadczeń zachęca również do przechodzenia odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

W usłudze GitHub można używać funkcji skanowania natywnego wpisu tajnego do identyfikowania poświadczeń lub innej formy wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrona i ograniczanie użytkowników z wysokim poziomem uprawnień

**Wskazówki**: Ogranicz liczbę kont użytkowników z wysokim poziomem uprawnień i Chroń te konta na poziomie podwyższonego poziomu. 

Najbardziej krytyczne role wbudowane w Azure Active Directory (Azure AD) to Administrator globalny i administrator ról uprzywilejowanych, ponieważ użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów. 

Dzięki tym przywilejom użytkownicy mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure:

- Administrator globalny/administrator firmy: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.

- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure AD, a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

W przypadku korzystania z ról niestandardowych z przypisanymi do nich uprawnieniami niestandardowymi mogą istnieć inne krytyczne role. Zastosuj podobne kontrolki do konta administratora o kluczowych zasobach firmy.  

Włącz dostęp uprzywilejowany just-in-Time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Uprawnienia ról administratora w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: Izoluj dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, którym udzielono uprzywilejowanego dostępu do subskrypcji i grup zarządzania, do których należą. 

Ogranicz dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do najważniejszych zasobów firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem, z agentami zainstalowanymi w systemach krytycznych dla firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy. 

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu. 

Należy przypisać oddzielne konta uprzywilejowane, które różnią się od kont użytkowników standardowych używanych do obsługi poczty e-mail, przeglądania i produktywności.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Dostęp do grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Znajdź raporty aktywności usługi Azure AD](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: Skonfiguruj konto awaryjne w celu uzyskania dostępu, gdy regularne konta administracyjne są niedostępne, aby zapobiec przypadkowemu zablokowaniu w organizacji usługi Azure Active Directory (Azure AD). Konta dostępu awaryjnego mogą zawierać uprawnienia wyższego poziomu i nie powinny być przypisane do konkretnych osób. Ogranicz konta dostępu awaryjnego do scenariuszy awaryjnych lub "Break Glass".

Upewnij się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom upoważnionym do używania ich w sytuacjach nagłych.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: korzystanie z funkcji zarządzania prawami Azure Active Directory (Azure AD) do automatyzowania przepływów pracy żądania dostępu, w tym przypisań, przeglądów i ważności. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu Azure Active Directory (Azure AD)](../active-directory/governance/access-reviews-overview.md)

- [Co to jest usługa zarządzania prawami Azure Active Directory (Azure AD)](../active-directory/governance/entitlement-management-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym z usługi Azure Active Directory (Azure AD), która umożliwia logowanie się i Konfigurowanie zasobów związanych z platformą Azure.

- [Stacje robocze z dostępem uprzywilejowanym](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planowanie wdrożenia wieloskładnikowego uwierzytelniania opartego na chmurze Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, podmiotów usługi grupy i tożsamości zarządzanych. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell i Azure Portal.

Uprawnienia przypisane do zasobów za pomocą usługi Azure RBAC powinny być zawsze ograniczone do tego, co jest wymagane przez role. Ograniczone uprawnienia uzupełniają podejście just in Time (JIT) Azure Active Directory (Azure AD) Privileged Identity Management (PIM), a te uprawnienia powinny być okresowo weryfikowane.

Używaj wbudowanych ról do przydzielenia uprawnień i utwórz własną rolę tylko wtedy, gdy jest to wymagane.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają dostęp do stale aktualizowanego spisu zasobów na platformie Azure. Zespoły ds. zabezpieczeń często potrzebują tego spisu, aby oszacować potencjalne zagrożenie w organizacji do pojawiających się zagrożeń i jako dane wejściowe w celu ciągłego ulepszania zabezpieczeń. 

Funkcja spisu Azure Security Center i wykres zasobów platformy Azure mogą wykonywać zapytania dotyczące i odnajdywania wszystkich zasobów w subskrypcjach, w tym usług platformy Azure, aplikacji i zasobów sieciowych.  

Logicznie organizuj elementy zawartości zgodnie z taksonomią organizacji przy użyciu tagów, a także innych metadanych na platformie Azure (nazwa, opis i kategoria).  

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md)

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: usługa komunikacji równorzędnej Microsoft Azure obsługuje wdrożenia oparte na Azure Resource Manager i wymuszanie konfiguracji przy użyciu Azure Policy w przestrzeni nazw "Microsoft. peering/Peers". Usługa Azure Policy pozwala przeprowadzić inspekcję i ograniczyć liczbę usług, które użytkownicy mogą aprowizować w danym środowisku. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Za pomocą usługi Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Upewnij się, że monitorujesz różne typy zasobów platformy Azure pod kątem potencjalnych zagrożeń i anomalii. Skup się na uzyskiwaniu alertów o wysokiej jakości, aby zmniejszyć liczbę fałszywych dodatnich dla analityków do sortowania. Alerty mogą być źródłem danych dziennika, agentów lub innych danych.

Użyj Azure Security Center wbudowanej funkcji wykrywania zagrożeń, która jest oparta na monitorowaniu danych telemetrycznych usługi platformy Azure i analizowaniu dzienników usług. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z systemu i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. 

Ponadto przy użyciu wskaźnikowego platformy Azure można tworzyć reguły analityczne, które wskazują na zagrożenia, które pasują do określonych kryteriów w danym środowisku. Reguły generują zdarzenia w przypadku dopasowania kryteriów, aby można było zbadać każde zdarzenie. Na platformie Azure można także zaimportować analizę zagrożeń innych firm, aby zwiększyć możliwości wykrywania zagrożeń. 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

- [Przewodnik dotyczący podręcznika Azure Security Center alertów zabezpieczeń](../security-center/alerts-reference.md)

- [Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](../sentinel/tutorial-detect-threats-custom.md)

- [Analiza zagrożeń cybernetycznymi z użyciem platformy Azure](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włącz wykrywanie zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki użytkowników, które można przeglądać za pomocą usługi Azure AD Reporting lub zintegrowane z Azure monitor. Te dzienniki mogą być również zintegrowane z platformą Azure, innymi rozwiązaniami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) lub narzędziami do monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy:

- Logowanie — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników

- Dzienniki inspekcji — umożliwia śledzenie przy użyciu dzienników dla wszystkich zmian wprowadzonych przez różne funkcje w ramach usługi Azure AD. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad

- Ryzykowne logowanie — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika

- Użytkownicy oflagowani w celu ryzyka — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, które mogło zostać naruszone

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia przez przestarzałe konta w ramach subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami w Security Center może również zbierać dodatkowe szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (takich jak maszyny wirtualne, kontenery, usługa App Service), zasoby danych (np. SQL DB i magazyn) oraz warstwy usług platformy Azure. Ta funkcja umożliwia wyświetlanie anomalii kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: Konfigurowanie telemetrii połączenia w celu uzyskania wglądu w łączność między lokalizacją połączenia a

Sieć firmy Microsoft za pośrednictwem usługi Komunikacja równorzędna Microsoft Azure.

- [Konfigurowanie telemetrii połączenia](measure-connection-telemetry.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włącz rejestrowanie zasobów platformy Azure

**Wskazówki**: Włączanie dziennika aktywności platformy Azure i wysyłanie dzienników do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji 

Dzienniki aktywności zapewniają wgląd w operacje wykonywane na zasobach usługi Azure ExpressRoute na poziomie płaszczyzny kontroli. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów.

- [Dziennik aktywności platformy Azure](/azure/azure-monitor/platform/activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z zasobami platformy Azure zgodnie z regulacjami zgodności w organizacji.

- [Jak ustawić parametry przechowywania dziennika](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówka**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, zaktualizował te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości 

**Wskazówka**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można wyciągać wnioski z przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą oni tracić czasu na wyniki fałszywie dodatnie. 

Alerty o wysokiej jakości można tworzyć na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności oraz narzędzi przeznaczonych do generowania i czyszczenia alertów przez połączenie i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Wykrywanie i analiza — badanie zdarzenia

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i korzystać z różnych źródeł danych, ponieważ badają potencjalne zdarzenia w celu utworzenia pełnego widoku tego, co się stało. Należy zbierać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w ramach całego łańcucha zagrożeń, aby uniknąć efektu martwego pola.  Należy również upewnić się, że szczegółowe informacje i wnioski są rejestrowane dla innych analityków i na potrzeby przyszłych badań.  

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

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Ustanów bezpieczne konfiguracje dla usług platformy Azure 

**Wskazówki**: Definiowanie guardrails zabezpieczeń dla zespołów infrastruktury i DevOps, dzięki czemu można łatwo bezpiecznie konfigurować wykorzystywane przez nich usługi platformy Azure. 

Rozpocznij konfigurację zabezpieczeń usług platformy Azure, korzystając z linii bazowych usługi w ramach testu porównawczego zabezpieczeń platformy Azure, a następnie dostosuj ją do potrzeb organizacji. 

Użyj Azure Security Center, aby skonfigurować Azure Policy do inspekcji i wymuszania konfiguracji zasobów platformy Azure. 

Plany platformy Azure umożliwiają automatyzację wdrażania i konfigurowania usług i środowisk aplikacji, w tym Azure Resource Manager szablonów, kontrolek RBAC platformy Azure i zasad, w ramach jednej definicji planu.

- [Ilustracja implementacji guardrails w strefie docelowej skali przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Praca z zasadami zabezpieczeń w Azure Security Center](../security-center/tutorial-security-policy.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Utrzymaj bezpieczne konfiguracje dla usług platformy Azure

**Wskazówki**: nie dotyczy;  to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

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

-   Korzystanie z natywnych i zewnętrznych funkcji ochrony danych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie przesyłania i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

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

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../security/fundamentals/identity-management-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

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

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
