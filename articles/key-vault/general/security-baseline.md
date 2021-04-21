---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Key Vault
description: Punkt Key Vault zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753348"
---
# <a name="azure-security-baseline-for-key-vault"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Key Vault

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy Azure w wersji [1.0](../../security/benchmarks/overview-v1.md) do Key Vault. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Key Vault. **Wykluczono** mechanizmy kontroli Key Vault lub których odpowiedzialność za nie ponosi firma Microsoft.

Aby zobaczyć, Key Vault mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny [Key Vault mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Integracja Azure Key Vault z Azure Private Link. Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault) i usług klienta/partnera hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być przekierowyny przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz połączyć się z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

- [Jak zintegrować Key Vault z Azure Private Link](/azure/key-vault/private-link-service)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** używaj Azure Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić Key Vault zasobów na platformie Azure. 

- [Aby uzyskać więcej informacji na temat zabezpieczeń sieci zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz Azure DDoS Protection Standardowa w sieciach wirtualnych platformy Azure skojarzonych z wystąpieniami usługi Key Vault w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

 
- [Zarządzanie Azure DDoS Protection Standardowa przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Wykrywanie zagrożeń dla warstwy usługi platformy Azure w usłudze Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** To wymaganie można spełnić, konfigurując zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault. AtP zapewnia dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania Azure Key Vault kont.

Gdy Azure Security Center wykryje anomalie działania, wyświetlane są alerty. Wysyła również wiadomość e-mail do administratora subskrypcji ze szczegółami podejrzanych działań i zaleceniami na temat sposobu badania i korygowania zidentyfikowanych zagrożeń.

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do wystąpień usługi Azure Key Vault, użyj tagów usługi platformy Azure dla usługi Azure Key Vault, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródła lub miejsca docelowego reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Omówienie tagów usług platformy Azure](../../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z wystąpieniami Azure Key Vault z Azure Policy. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.KeyVault" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci w wystąpieniach Azure Key Vault sieci. Możesz również korzystać z wbudowanych definicji zasad związanych z Azure Key Vault, takich jak:
- Key Vault używać punktu końcowego usługi dla sieci wirtualnej

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Przykłady dla usługi Azure Policy](/azure/governance/policy/samples)

- [Definiowanie i przypisywanie strategii w portalu](../../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień Azure Key Vault, aby zapewnić metadane i organizację logiczną.

Użyj wbudowanych definicji Azure Policy związanych z tagami, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone z tagami, i powiadamiać o nieotagowanych zasobach.

Za pomocą interfejsu Azure PowerShell wiersza polecenia platformy Azure można wyszukiwania lub wykonywania akcji na zasobach na podstawie ich tagów.

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów sieciowych i wykrywać zmiany dotyczące zasobów sieciowych związanych Azure Key Vault wystąpieniami. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi przy użyciu Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki za pośrednictwem Azure Monitor, aby agregować dane zabezpieczeń generowane przez Azure Key Vault. W Azure Monitor obszaru roboczego usługi Azure Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego/archiwalnej. Alternatywnie można włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Azure Key Vault rejestrowania](/azure/key-vault/key-vault-logging)

- [Szybki start: jak rozpocząć Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** włącz ustawienia diagnostyczne w wystąpieniach Azure Key Vault, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostycznych. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, znacznik czasu, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Azure Key Vault rejestrowania](/azure/key-vault/key-vault-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor obszaru roboczego usługi Log Analytics używanego do przechowywania dzienników usługi Azure Key Vault ustaw okres przechowywania zgodnie z przepisami zgodności obowiązującymi w organizacji. Użyj kont usługi Azure Storage na potrzeby magazynu długoterminowego/archiwalnej.

- [Change the data retention period](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) (Zmienianie okresu przechowywania danych)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników Azure Key Vault chronionych zasobami. Użyj Azure Monitor usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Informacje Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Wprowadzenie do zapytań dotyczących dzienników w usłudze Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** w Azure Security Center zaawansowanej ochrony przed zagrożeniami (ATP) dla Key Vault. Włącz ustawienia diagnostyczne w Azure Key Vault i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics. Dołączanie obszaru roboczego usługi Log Analytics do Azure Sentinel zapewnia rozwiązanie do automatycznego reagowania na aranżację zabezpieczeń (SOAR). Dzięki temu podręczniki (zautomatyzowane rozwiązania) mogą być tworzone i używane do rozwiązywania problemów z zabezpieczeniami.

- [Szybki start: wdrażanie usługi Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Odpowiadanie na zdarzenia przy użyciu alertów platformy Azure Monitor](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** utrzymywanie spisu aplikacji zarejestrowanych w usłudze Azure Active Directory (Azure AD), a także kont użytkowników, które mają dostęp do kluczy Azure Key Vault, wpisów tajnych i certyfikatów. Do wykonywania zapytań i uzgadniania Azure Portal programu PowerShell można użyć Key Vault dostępu. Aby wyświetlić dostęp w programie PowerShell, użyj następującego polecenia:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

- [Rejestrowanie aplikacji w usłudze Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Bezpieczny dostęp do magazynu kluczy](security-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do Azure Key Vault wystąpień. Użyj Azure Security Center zarządzania tożsamościami i dostępem (obecnie w wersji zapoznawczej), aby monitorować liczbę aktywnych kont administracyjnych.

- [Monitorowanie tożsamości i dostępu (wersja zapoznawcza)](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** użyj jednostki usługi platformy Azure w połączeniu z elementami AppId, TenantID i ClientSecret, aby bezproblemowo uwierzytelnić aplikację i pobrać token, który będzie używany do uzyskiwania dostępu do Azure Key Vault tajnych.

- [Uwierzytelnianie typu usługa-usługa w celu Azure Key Vault przy użyciu programu .NET](/azure/key-vault/service-to-service-authentication)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami usługi Azure Security Center Identity and Access Management (obecnie w wersji zapoznawczej), aby chronić zasoby z obsługą centrum zdarzeń.

- [Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorowanie tożsamości i dostępu (wersja zapoznawcza)](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** używanie stacji roboczej z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym usługi Azure AD skonfigurowanym do logowania się i Key Vault zasobów.

- [Stacje robocze z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Planowanie wdrożenia uwierzytelniania wieloskładnikowego usługi Azure AD opartego na chmurze](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** Generowanie dzienników i alertów przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku. Wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników. Aby uzyskać dodatkowe rejestrowanie, Azure Security Center alerty wykrywania ryzyka do usługi Azure Monitor skonfigurować niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure Key Vault generowania alertów dotyczących podejrzanych działań.

- [Wdrażanie Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)](/azure/security-center/advanced-threat-protection-key-vault)

- [Alerty dotyczące Azure Key Vault (wersja zapoznawcza)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Wykrycia ryzyka w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal](/azure/azure-monitor/platform/action-groups)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Konfigurowanie warunku lokalizacji zasad dostępu warunkowego i zarządzanie nazwanych lokalizacji. Za pomocą nazwanych lokalizacji można tworzyć logiczne grupy zakresów adresów IP lub krajów i regionów. Możesz ograniczyć dostęp do poufnych zasobów, takich jak Key Vault tajne, do skonfigurowanych nazwanych lokalizacji.

- [Jaki jest warunek lokalizacji w dostępie warunkowym Azure Active Directory (Azure AD)?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Key Vault. Dzięki temu kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia administrowanie poufnymi zasobami.

- [Tworzenie nowej dzierżawy w usłudze Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Przejrzyj Azure Active Directory (Azure AD), aby ułatwić odnajdywanie nieaktywnego konta Azure Key Vault ról administracyjnych. Ponadto przeglądy dostępu usługi Azure AD służą do efektywnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw, które mogą być używane do uzyskiwania dostępu do Azure Key Vault i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko prawi użytkownicy nadal mają dostęp.

- [Dokumentacja dotycząca raportów i monitorowania usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Co to są przeglądy dostępu w usłudze Azure AD?](../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** włącz ustawienia diagnostyczne dla Azure Key Vault i Azure Active Directory (Azure AD), wysyłając wszystkie dzienniki do obszaru roboczego usługi Log Analytics. Skonfiguruj żądane alerty (takie jak próby uzyskania dostępu do wyłączonych wpisów tajnych) w u usługi Log Analytics.

- [Integrowanie dzienników usługi Azure AD z Azure Monitor dziennikami](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrowanie ze starego Key Vault rozwiązania](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) usługi Identity Protection i funkcji wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z Azure Key Vault chronionymi zasobami. Należy włączyć automatyczne odpowiedzi za pośrednictwem Azure Sentinel, aby zaimplementować odpowiedzi zabezpieczeń organizacji.

- [Raport ryzykownych logów w portalu usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady ryzyka](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje Azure Key Vault włączonych zasobów. 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Możesz zabezpieczyć dostęp do Azure Key Vault, korzystając z punktów końcowych usługi dla sieci wirtualnej skonfigurowanych w celu ograniczenia dostępu do określonych podsieci.

Gdy reguły zapory będą obowiązywać, można wykonywać operacje Azure Key Vault danych tylko wtedy, gdy żądanie pochodzi z dozwolonych podsieci lub zakresów adresów IP. Dotyczy to również Azure Key Vault dostępu w Azure Portal. Mimo że możesz przejść do magazynu kluczy z witryny Azure Portal, lista kluczy, wpisów tajnych lub certyfikatów może nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli komputer kliencki nie znajduje się na liście dozwolonych. Ma to również wpływ na Azure Key Vault wyboru i innych usług platformy Azure. Jeśli reguły zapory uniemożliwiają wykonanie tego działania na komputerze klienckim, mogą być możliwe listy magazynów kluczy, ale nie klucze listy.

- [Konfigurowanie Azure Key Vault sieci wirtualnych i zapory sieci wirtualnych](/azure/key-vault/key-vault-network-security)

- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** wszystkie dane przechowywane w Azure Key Vault są traktowane jako poufne. Użyj Azure Key Vault dostępu do płaszczyzny danych, aby kontrolować dostęp do Azure Key Vault tajnych. Możesz również użyć Key Vault zapory wbudowanej do kontrolowania dostępu w warstwie sieci. Aby monitorować dostęp do Azure Key Vault, włącz ustawienia diagnostyki Key Vault i wysyłaj dzienniki do konta usługi Azure Storage lub obszaru roboczego usługi Log Analytics.

- [Bezpieczny dostęp do magazynu kluczy](security-overview.md)

- [Konfigurowanie Azure Key Vault i sieci wirtualnych](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault rejestrowania](/azure/key-vault/key-vault-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** Bezpieczny dostęp do płaszczyzny zarządzania i danych Azure Key Vault wystąpień.

- [Bezpieczny dostęp do magazynu kluczy](security-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** Użyj rozwiązania Azure Key Vault Analytics w Azure Monitor, aby Azure Key Vault dzienniki zdarzeń inspekcji.

- [Azure Key Vault Analytics w Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** użyj domyślnych ocen ryzyka (Secure Score) dostarczonych przez Azure Security Center.

- [Zwiększ swój wynik bezpieczeństwa w Azure Security Center](/azure/security-center/security-center-secure-score)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym Azure Key Vault wystąpień) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

- [Uruchamianie pierwszego zapytania Resource Graph za pomocą Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Uzyskiwanie subskrypcji, do których bieżące konto może uzyskać dostęp](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure role-based access control (RBAC) (Kontrola dostępu oparta na rolach na platformie Azure)](../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do Azure Key Vault, które zawierają metadane w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli to konieczne, do organizowania i śledzenia Azure Key Vault i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [Tworzenie dodatkowej subskrypcji platformy Azure](/azure/billing/billing-create-subscription)

- [Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi](/azure/governance/management-groups/create)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj tej Azure Resource Graph, aby odpytywać/odnajdywać zasoby w ramach subskrypcji.

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Szybki start: uruchamianie pierwszego zapytania Resource Graph pomocą Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Przykłady dla usługi Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i zmiany zasobów w środowisku o wysokim poziomie zabezpieczeń, na przykład tych z Key Vault konfiguracji.

- [Zarządzanie dostępem do zarządzania platformą Azure przy użyciu dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.KeyVault", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji Azure Key Vault wystąpień. Możesz również użyć wbudowanych definicji Azure Policy dla Azure Key Vault takich jak:

- Key Vault obiektów powinno być można odzyskać

- Wdrażanie ustawień diagnostycznych dla Key Vault w obszarze roboczym usługi Log Analytics

- Dzienniki diagnostyczne w Key Vault powinny być włączone

- Key Vault używać punktu końcowego usługi dla sieci wirtualnej

- Wdrażanie ustawień diagnostycznych dla Key Vault centrum zdarzeń

- Użyj zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla Azure Key Vault wystąpień.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak wyświetlić dostępne Azure Policy aliasy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist] w celu wymuszenia bezpiecznych ustawień Azure Key Vault zasobów z włączoną obsługą zasad. 

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

  
- [Zrozumienie Azure Policy danych](../../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy dla zasobów z włączoną obsługą Azure Key Vault, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nimi.

- [Jak przechowywać kod w Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos dokumentacji](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.KeyVault", aby utworzyć zasady niestandardowe służące do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9. Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Security Center, aby przeprowadzić skanowanie bazowe dla Azure Key Vault chronionych przez zasoby.

- [Jak skorygować rekomendacje w Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** używaj tożsamości usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie wpisami tajnymi dla aplikacji w chmurze. Upewnij się, Azure Key Vault jest włączone usuwanie nie soft-delete.

- [Integracja z usługą tożsamości zarządzanych platformy Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Tworzenie Key Vault](quick-create-portal.md)

- [Uwierzytelnianie w usłudze Key Vault](authentication.md)

- [Przypisywanie zasad Key Vault dostępu](assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** użyj tożsamości usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

  

- [Jak zintegrować z tożsamościami zarządzanymi platformy Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Jak utworzyć Key Vault](quick-create-portal.md)    

- [Jak uwierzytelnić się w Key Vault](authentication.md)

- [Przypisywanie zasad Key Vault dostępu](assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.  
  
- [ Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na bazowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Key Vault), jednak nie działa w przypadku zawartości klienta.

Wstępnie przeskanuj zawartość przesyłaną lub wysyłaną do zasobów platformy Azure, które nie są obliczeniowe, takich jak Azure Key Vault. Firma Microsoft nie może uzyskać dostępu do twoich danych w tych wystąpieniach.

- [Opis Microsoft Antimalware dla Azure Cloud Services i Virtual Machines](../../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** regularne wykonywanie automatycznych kopii zapasowych certyfikatów Key Vault, kluczy, kont magazynu zarządzanego i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie możesz przechowywać kopie zapasowe Key Vault w Azure Backup.

- [Jak utworzyć kopię zapasową Key Vault certyfikatów](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault kluczy](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault tajnych](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Jak włączyć Azure Backup](/azure/backup)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** wykonywanie kopii zapasowych certyfikatów Key Vault, kluczy, kont magazynu zarządzanego i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie możesz przechowywać kopie zapasowe Key Vault w Azure Backup.

- [Jak utworzyć kopię zapasową Key Vault certyfikatów](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault kluczy](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault tajnych](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Jak włączyć Azure Backup](/azure/backup)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** okresowe przywracanie danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak przywrócić Key Vault certyfikatów](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak przywrócić Key Vault kluczy](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak przywrócić Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault tajne](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Upewnij się, że dla aplikacji włączono usuwanie Azure Key Vault. Usuwanie nie softowe umożliwia odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty. 

- [Jak używać usuwania Azure Key Vault usuwania nie soft](/azure/key-vault/key-vault-soft-delete-powershell)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.KeyVault:**

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu. Procesy te powinny koncentrować się na ochronie systemów poufnych, takich jak te, które Key Vault tajne.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub metryki użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, istnieje złośliwe intencje. Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane, takie jak Azure Key Vault tajne.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach, aby ułatwić ochronę Azure Key Vault i powiązanych zasobów. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i rekomendacji przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie ryzyka związanego Azure Key Vault z włączonymi zasobami. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób.  Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel. 

 

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md) 

  

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem funkcji "Logic Apps" w przypadku alertów zabezpieczeń i zaleceń dotyczących ochrony Azure Key Vault chronionych zasobami. 

 

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
