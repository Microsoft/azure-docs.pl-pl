---
title: Podstawa zabezpieczeń platformy Azure dla Key Vault
description: Linia bazowa zabezpieczeń Key Vault zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968709"
---
# <a name="azure-security-baseline-for-key-vault"></a>Podstawa zabezpieczeń platformy Azure dla Key Vault

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../../security/benchmarks/overview-v1.md) do Key Vault. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Key Vault. Nie zostały wykluczone żadne **kontrolki** , które mają zastosowanie do Key Vault lub dla których odpowiedzialnością jest firma Microsoft.

Aby dowiedzieć się, jak Key Vault całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Key Vault pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: integracja Azure Key Vault z prywatnym łączem platformy Azure. Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

- [Jak zintegrować Key Vault z prywatnym łączem platformy Azure](/azure/key-vault/private-link-service)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. kluczy — magazyn**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby skonfigurowane Key Vault na platformie Azure. 

- [Więcej informacji o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włączanie standardu Azure DDoS Protection w sieciach wirtualnych platformy Azure skojarzonych z wystąpieniami Key Vault w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

 
- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: to wymaganie można spełnić, konfigurując zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure Key Vault. ATP stanowi dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby dostępu do kont Azure Key Vault lub ich wykorzystania.

Gdy Azure Security Center wykrywa nietypowe działanie, wyświetla alerty. Również wysyła do administratora subskrypcji wiadomość e-mail z informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zidentyfikowanych zagrożeń.

- [Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do wystąpień Azure Key Vault, użyj tagów usługi platformy Azure dla Azure Key Vault, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Omówienie tagów usługi platformy Azure](../../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z wystąpieniami Azure Key Vault przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft." i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Key Vault. Mogą również używać wbudowanych definicji zasad związanych z Azure Key Vault, takich jak:
- Key Vault powinien używać punktu końcowego usługi sieci wirtualnej

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Przykłady dla usługi Azure Policy](/azure/governance/policy/samples)

- [Definiowanie i Przypisywanie planu w portalu](../../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień Azure Key Vault, aby zapewnić metadane i organizację logiczną.

Użyj wbudowanych definicji Azure Policy związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Key Vault. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Key Vault. W ramach Azure Monitor Użyj obszaru roboczego usługi Azure Log Analytics do wykonywania zapytań i przeprowadzania analiz, a następnie korzystaj z kont usługi Azure Storage w celu przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Funkcja rejestrowania usługi Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Szybki Start: jak przejść do tablicy wskaźnikowej platformy Azure](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych w wystąpieniach Azure Key Vault, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Funkcja rejestrowania usługi Azure Key Vault](/azure/key-vault/key-vault-logging)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. kluczy — magazyn**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor w obszarze roboczym log Analytics używanym do przechowywania dzienników Azure Key Vault należy ustawić okres przechowywania zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

- [Change the data retention period](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) (Zmienianie okresu przechowywania danych)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularnego przeglądania wyników dla zasobów chronionych przez Azure Key Vault. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

- [Tablica wskaźnikowa platformy Azure](../../sentinel/quickstart-onboard.md)

- [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Wprowadzenie do zapytań dotyczących dzienników w usłudze Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: w Azure Security Center Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Key Vault. Włącz ustawienia diagnostyczne w Azure Key Vault i Wyślij dzienniki do Log Analytics obszaru roboczego. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

- [Szybki start: wdrażanie usługi Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Odpowiadanie na zdarzenia przy użyciu alertów platformy Azure Monitor](/azure/azure-monitor/learn/tutorial-response)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu aplikacji zarejestrowanych w Azure Active Directory (Azure AD), a także wszystkich kont użytkowników, które mają dostęp do kluczy Azure Key Vault, wpisów tajnych i certyfikatów. Do wykonywania zapytań i uzgadniania dostępu Key Vault można użyć Azure Portal lub programu PowerShell. Aby wyświetlić dostęp w programie PowerShell, użyj następującego polecenia:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Właściwości. AccessPolicies

- [Rejestrowanie aplikacji w usłudze Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do wystąpień Azure Key Vault. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem (obecnie w wersji zapoznawczej), aby monitorować liczbę aktywnych kont administracyjnych.

- [Monitoruj tożsamość i dostęp (wersja zapoznawcza)](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: Użyj jednostki usługi platformy Azure w połączeniu z identyfikatorami AppID, TenantID i ClientSecret, aby bezproblemowo uwierzytelnić aplikację i pobrać token, który zostanie użyty w celu uzyskania dostępu do Azure Key Vault wpisów tajnych.

- [Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET](/azure/key-vault/service-to-service-authentication)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami Azure Security Center zarządzania dostępem i tożsamościami (obecnie w wersji zapoznawczej), aby pomóc w ochronie zasobów z obsługą centrum zdarzeń.

- [Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitoruj tożsamość i dostęp (wersja zapoznawcza)](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym usługi Azure AD skonfigurowanym do logowania się i konfigurowania Key Vault włączonych zasobów.

- [Stacje robocze z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Planowanie wdrożenia wieloskładnikowego uwierzytelniania opartego na chmurze Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników. Aby uzyskać dodatkowe rejestrowanie, Wyślij Azure Security Center alerty wykrywania ryzyka do Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure Key Vault, aby generować alerty dla podejrzanych działań.

- [Wdróż Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)](/azure/security-center/advanced-threat-protection-key-vault)

- [Alerty dla Azure Key Vault (wersja zapoznawcza)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Wykrywanie ryzyka w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal](/azure/azure-monitor/platform/action-groups)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Skonfiguruj warunek lokalizacji zasad dostępu warunkowego i Zarządzaj nazwanymi lokalizacjami. Przy użyciu nazwanych lokalizacji można tworzyć logiczne grupowania zakresów adresów IP lub krajów i regionów. Można ograniczyć dostęp do poufnych zasobów, takich jak wpisy tajne Key Vault, do skonfigurowanych lokalizacji nazwanych.

- [Jaki jest warunek lokalizacji w usłudze Azure Active Directory (Azure AD) dostęp warunkowy?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Key Vault. Umożliwia to kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby administrowania poufnymi zasobami.

- [Tworzenie nowej dzierżawy w usłudze Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: przegląd dzienników usługi Azure Active Directory (Azure AD) w celu ułatwienia wykrywania starych kont przy użyciu Azure Key Vault ról administracyjnych. Ponadto za pomocą przeglądów dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do Azure Key Vault i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Dokumentacja dotycząca monitorowania i raportów usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Co to są przeglądy dostępu do usługi Azure AD?](../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Key Vault i Azure Active Directory (Azure AD), wysyłanie wszystkich dzienników do obszaru roboczego log Analytics. Skonfiguruj żądane alerty (takie jak próby uzyskania dostępu do wyłączonych wpisów tajnych) w ramach Log Analytics.

- [Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrowanie ze starego rozwiązania Key Vault](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania dotyczące Azure Key Vault chronionych zasobów. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

- [Raport dotyczący ryzykownych logowań w portalu usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Instrukcje: Konfigurowanie i Włączanie zasad ryzyka](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne dotyczące Azure Key Vault włączonych zasobów. 

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: można zabezpieczyć dostęp do Azure Key Vault, korzystając z punktów końcowych usługi sieci wirtualnej skonfigurowanych w celu ograniczenia dostępu do określonych podsieci.

Po zastosowaniu reguł zapory można wykonywać Azure Key Vault operacje płaszczyzny danych tylko wtedy, gdy żądanie pochodzi z dozwolonych podsieci lub zakresów adresów IP. Dotyczy to również dostępu Azure Key Vault w Azure Portal. Mimo że możesz przejść do magazynu kluczy z Azure Portal, możesz nie być w stanie wyświetlić kluczy, wpisów tajnych lub certyfikatów, jeśli komputer kliencki nie znajduje się na liście dozwolonych. Ma to również wpływ na selektor Azure Key Vault i inne usługi platformy Azure. Mogą być widoczne listy magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają wykonanie tej czynności na komputerze klienckim.

- [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](/azure/key-vault/key-vault-network-security)

- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: wszystkie dane przechowywane w Azure Key Vault są uważane za poufne. Użyj Azure Key Vault kontroli dostępu płaszczyzny danych, aby kontrolować dostęp do Azure Key Vault wpisów tajnych. Możesz również użyć wbudowanej zapory Key Vault, aby kontrolować dostęp do warstwy sieciowej. Aby monitorować dostęp do Azure Key Vault, Włącz Key Vault ustawienia diagnostyczne i Wyślij dzienniki do konta usługi Azure Storage lub obszaru roboczego Log Analytics.

- [Bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md)

- [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](/azure/key-vault/key-vault-network-security)

- [Funkcja rejestrowania usługi Azure Key Vault](/azure/key-vault/key-vault-logging)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: bezpieczny dostęp do zarządzania i płaszczyzny danych wystąpień Azure Key Vault.

- [Bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: użyj rozwiązania Azure Key Vault Analytics w Azure monitor, aby przejrzeć dzienniki zdarzeń inspekcji Azure Key Vault.

- [Rozwiązanie Azure Key Vault Analytics w Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

- [Popraw swój Bezpieczny wynik w Azure Security Center](/azure/security-center/security-center-secure-score)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień Azure Key Vault) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Pobierz subskrypcje, do których bieżące konto może uzyskać dostęp](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Kontrola dostępu oparta na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do Azure Key Vault zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia wystąpień Azure Key Vault i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Tworzenie dodatkowej subskrypcji platformy Azure](/azure/billing/billing-create-subscription)

- [Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi](/azure/governance/management-groups/create)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md)

- [Przykłady dla usługi Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak te z konfiguracją Key Vault.

- [Zarządzanie dostępem do zarządzania platformą Azure przy użyciu dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datamagazyn", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Key Vault. Możesz również użyć wbudowanych Azure Policy definicji dla Azure Key Vault takich jak:

- Key Vault obiektów powinno być możliwe do odzyskania

- Wdróż ustawienia diagnostyczne dla Key Vault do Log Analytics obszaru roboczego

- Dzienniki diagnostyczne w Key Vault powinny być włączone

- Key Vault powinien używać punktu końcowego usługi sieci wirtualnej

- Wdróż ustawienia diagnostyczne dla Key Vault w centrum zdarzeń

- Użyj zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla wystąpień Azure Key Vault.

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w ramach zasobów z włączoną obsługą Azure Key Vault. 

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../../governance/policy/tutorials/create-and-manage.md)

  
- [Zrozumienie efektów Azure Policy](../../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla zasobów Azure Key Vault włączonych, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datamagazyn", aby utworzyć zasady niestandardowe do alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów chronionych Azure Key Vault.

- [Jak skorygować zalecenia w Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze. Upewnij się, że jest włączone Azure Key Vault usuwania nietrwałego.

- [Integracja z usługą tożsamości zarządzanych platformy Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Tworzenie Key Vault](quick-create-portal.md)

- [Uwierzytelnianie w usłudze Key Vault](authentication.md)

- [Przypisywanie zasad dostępu Key Vault](assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. kluczy — magazyn**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze.

  

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Jak utworzyć Key Vault](quick-create-portal.md)    

- [Jak przeprowadzić uwierzytelnianie w Key Vault](authentication.md)

- [Przypisywanie zasad dostępu Key Vault](assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.  
  
- [ Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Key Vault), ale nie jest uruchamiane w treści klienta.

Wstępnie Skanuj zawartość przekazywaną lub wysłaną do zasobów platformy Azure nieobliczeniowych, takich jak Azure Key Vault. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: zapewnianie zwykłych zautomatyzowanych kopii zapasowych certyfikatów Key Vault, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe Key Vault w Azure Backup.

- [Jak utworzyć kopię zapasową certyfikatów Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową kluczy Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault Secret](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Jak włączyć Azure Backup](/azure/backup)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: wykonywanie kopii zapasowych certyfikatów Key Vault, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe Key Vault w Azure Backup.

- [Jak utworzyć kopię zapasową certyfikatów Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową kluczy Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak utworzyć kopię zapasową Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Jak utworzyć kopię zapasową Key Vault Secret](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Jak włączyć Azure Backup](/azure/backup)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak przywrócić Key Vault certyfikaty](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Jak przywrócić klucze Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Jak przywrócić Key Vault zarządzanych kont magazynu](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault wpisy tajne](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że dla Azure Key Vault jest włączona funkcja usuwania nietrwałego. Usuwanie nietrwałe umożliwia Odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty. 

- [Jak używać usuwania nietrwałego Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. kluczy — magazyn**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu. Te procesy powinny mieć fokus na ochronie poufnych systemów, takich jak te, które używają Key Vault Secret.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub metryki używanej do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które doprowadziło do alertu. Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne, takie jak Azure Key Vault Secret.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w ramach regularnego erze, aby chronić wystąpienia Azure Key Vault i powiązane zasoby. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować ryzyko związane z Azure Key Vault zasobami. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły.  Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

 

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md) 

  

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi przez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony chronionych przez Azure Key Vault zasobów. 

 

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
