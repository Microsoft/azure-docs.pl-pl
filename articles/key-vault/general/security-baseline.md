---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault
description: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6e660c1244dd5566fbfb45a6da37d39294354ccb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756991"
---
# <a name="azure-security-baseline-for-key-vault"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault

Usługa Azure Security Baseline for Key Vault zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia.

Punkt odniesienia dla tej usługi jest pobierany z [usługi Azure Security Benchmark w wersji 1.0,](https://docs.microsoft.com/azure/security/benchmarks/overview)która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure za pomocą naszych najlepszych wskazówek dotyczących rozwiązań.

Aby uzyskać więcej informacji, zobacz [omówienie planów bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure w sieci wirtualnej

**Wskazówki:** Integracja usługi Azure Key Vault z łączem prywatnym platformy Azure. 

Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Key Vault) i usługi Azure hostowane przez klienta/partnera za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

Jak zintegrować usługę Key Vault z usługą Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki:** Skorzystaj z usługi Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby skonfigurowane przez usługę Key Vault na platformie Azure. 

Aby uzyskać więcej informacji na temat zabezpieczeń sieciowych dostarczonych przez usługę Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Włącz standard ochrony przed atakami DDoS w usłudze Azure W sieciach wirtualnych skojarzonych z wystąpieniami usługi Key Vault w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Usługa Azure Security Center Integrated Threat Intelligence umożliwia odmawianie komunikacji ze znanymi złośliwymi lub nieużywanym internetowymi adresami IP.

 
Zarządzanie standardem ochrony przed atakami DDoS platformy Azure przy użyciu portalu Azure:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Wykrywanie zagrożeń dla warstwy usługi platformy Azure w usłudze Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Rejestrowanie pakietów sieciowych i dzienników przepływu

**Wskazówki:** Usługa Azure Key Vault nie używa sieciowych grup zabezpieczeń (NSG) i dzienniki przepływu dla usługi Azure Key Vault nie są przechwytywane. Zamiast tego użyj łącza prywatnego platformy Azure, aby zabezpieczyć wystąpienia usługi Azure Key Vault i włączyć ustawienia diagnostyczne do rejestrowania metryk i zdarzeń inspekcji.

Zintegruj magazyn kluczy z łączem prywatnym platformy Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Rejestrowanie usługi Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Wdrażanie opartych na sieci systemów wykrywania włamań/włamań (IDS/IPS)

**Wskazówki:** To wymaganie można spełnić, konfigurując zaawansowaną ochronę przed zagrożeniami (ATP) dla usługi Azure Key Vault. Atp zapewnia dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Azure Key Vault lub ich wykorzystania.

Gdy usługa Azure Security Center wykryje nietypową aktywność, wyświetla alerty. Wysyła również wiadomość e-mail do administratora subskrypcji ze szczegółami podejrzanej aktywności i zaleceniami dotyczącymi sposobu badania i korygować zidentyfikowane zagrożenia.

Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i administracyjnych narzutów administracyjnych dotyczących zasad bezpieczeństwa sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do wystąpień usługi Azure Key Vault, użyj tagów usługi Azure dla usługi Azure Key Vault do definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń lub zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

Omówienie tagów usługi platformy Azure:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z wystąpieniami usługi Azure Key Vault za pomocą usługi Azure Policy. Użyj aliasów zasad platformy Azure w obszarach nazw "Microsoft.KeyVault" i "Microsoft.Network", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji sieciowej wystąpień usługi Azure Key Vault. Można również korzystać z wbudowanych definicji zasad związanych z usługą Azure Key Vault, takich jak:

Usługa Key Vault powinna używać punktu końcowego usługi sieci wirtualnej

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Szybki start: zdefiniuj i przypisz plan w portalu:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1.10: Przepisy konfiguracji ruchu dokumentów

**Wskazówki:** Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień usługi Azure Key Vault, aby zapewnić metadane i organizację logiczną.

Użyj dowolnej z wbudowanych definicji zasad platformy Azure związanych z tagowaniem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone za pomocą tagów i powiadamiać o istniejących nieoznakowanych zasobach.

Za pomocą narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można wyszukać lub wykonać akcje na podstawie ich tagów.

Użyj tagów do organizowania zasobów platformy Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami usługi Azure Key Vault. Tworzenie alertów w usłudze Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności przy użyciu usługi Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Korzystanie z zatwierdzonych źródeł synchronizacji czasu

**Wskazówki**: Nie dotyczy; Firma Microsoft przechowuje źródło czasu używane dla zasobów platformy Azure, takich jak Usługa Azure Key Vault, dla sygnatur czasowych w dziennikach.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Pozyskiwania dzienników za pośrednictwem usługi Azure Monitor do agregowania danych zabezpieczeń generowanych przez usługę Azure Key Vault. W usłudze Azure Monitor użyj obszaru roboczego usługi Azure Log Analytics do wykonywania zapytań i wykonywania analiz oraz użyj kont usługi Azure Storage do magazynu długoterminowego/archiwizacji. Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM. 

Rejestrowanie usługi Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Szybki start: jak na pokładzie usługi Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Włączanie rejestrowania inspekcji zasobów platformy Azure

**Wskazówki:** Włącz ustawienia diagnostyczne w wystąpieniach usługi Azure Key Vault, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

Rejestrowanie usługi Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** W usłudze Azure Monitor obszar roboczy usługi Log Analytics używany do przechowywania dzienników usługi Azure Key Vault ustaw okres przechowywania zgodnie z przepisami dotyczącymi zgodności obowiązującymi w organizacji. Użyj kont usługi Azure Storage dla magazynu długoterminowego/archiwizacji.

Zmień okres przechowywania danych:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorowanie i przeglądanie dzienników

**Wskazówki:** Analizuj i monitoruj dzienniki pod kątem nietypowego zachowania i regularnie przeglądaj wyniki zasobów chronionych przez usługę Azure Key Vault. Użyj obszaru roboczego usługi Azure Monitor log analytics do przeglądania dzienników i wykonywania zapytań na dane dziennika. Alternatywnie można włączyć i na pokładzie danych do usługi Azure Sentinel lub innej firmy SIEM. 

Szybki start: wbudowany program Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Wprowadzenie do zapytań dziennika w usłudze Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Włączanie alertów o nietypowej aktywności

**Wskazówki:** W usłudze Azure Security Center włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla usługi Key Vault. Włącz ustawienia diagnostyczne w usłudze Azure Key Vault i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics. Dołączanie obszaru roboczego usługi Log Analytics do usługi Azure Sentinel, ponieważ zapewnia rozwiązanie automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR). Pozwala to na tworzenie podręczników (zautomatyzowanych rozwiązań) i ich wykorzystanie do rozwiązywania problemów z zabezpieczeniami.

Szybki start: wbudowany program Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Odpowiadanie na zdarzenia za pomocą alertów usługi Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizuj rejestrowanie przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; Usługa Azure Key Vault nie przetwarza ani nie generuje dzienników związanych ze złośliwym oprogramowaniem.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Nie dotyczy; Usługa Azure Key Vault nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włącz rejestrowanie inspekcji wiersza polecenia

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Kontrola tożsamości i dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Prowadzenie spisu rachunków administracyjnych

**Wskazówki:** Obsługa spisu aplikacji zarejestrowanych w usłudze Azure Active Directory, a także wszystkich kont użytkowników, które mają dostęp do kluczy, wpisów tajnych i certyfikatów usługi Azure Key Vault. Możesz użyć witryny Azure portal lub programu PowerShell do wykonywania zapytań i uzgadniania dostępu do usługi Key Vault. Aby wyświetlić dostęp w programie PowerShell, użyj następującego polecenia:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Właściwości.AccessPolicies

Rejestrowanie aplikacji w usłudze Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Bezpieczny dostęp do magazynu kluczy:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3.2: W stosownych przypadkach zmieniaj domyślne hasła

**Wskazówki**: Nie dotyczy; Usługa Azure Key Vault nie ma pojęcia haseł domyślnych, ponieważ uwierzytelnianie jest dostarczane przez usługę Active Directory i zabezpieczone za pomocą kontroli dostępu opartej na rolach.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Korzystanie z dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych, które mają dostęp do wystąpień usługi Azure Key Vault. Użyj usługi Azure Security Center Identity and Access Management (obecnie w wersji zapoznawczej), aby monitorować liczbę aktywnych kont administracyjnych.

Monitorowanie tożsamości i dostępu (wersja zapoznawcza):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Używanie logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure Active Directory

**Wskazówki:** Użyj jednostki usługi platformy Azure w połączeniu z Identyfikatorem AppId, Identyfikatorem Dzierżawy i ClientSecret, aby bezproblemowo uwierzytelnić aplikację i pobrać token, który będzie używany do uzyskiwania dostępu do wpisów tajnych usługi Azure Key Vault.

Uwierzytelnianie usługi do usługi w usłudze Azure Key Vault przy użyciu platformy .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem usługi Azure Security Center (obecnie w wersji zapoznawczej), aby chronić zasoby obsługujące centrum zdarzeń.

Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure w chmurze:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorowanie tożsamości i dostępu (wersja zapoznawcza):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Używanie dedykowanych maszyn (stacje robocze z uprzywilejowanym dostępem) do wszystkich zadań administracyjnych

**Wskazówki:** Użyj stacji roboczej dostępu uprzywilejowanego (PAW) z uwierzytelnianiem wieloskładnikowym platformy Azure skonfigurowanym do logowania się i konfigurowania zasobów włączonych usługi Key Vault. 

Stacje robocze dostępu uprzywilejowanego:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure w chmurze:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Rejestrowanie i ostrzeganie o podejrzanych działaniach z kont administracyjnych

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) Uprzywilejowane zarządzanie tożsamościami (PIM) do generowania dzienników i alertów, gdy podejrzane lub niebezpieczne działania występuje w środowisku. Wykrywanie ryzyka AAD umożliwia wyświetlanie alertów i raportów o ryzykownych zachowaniach użytkowników. Aby uzyskać dodatkowe rejestrowanie, wyślij alerty wykrywania ryzyka usługi Azure Security Center do usługi Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla usługi Azure Key Vault do generowania alertów dla podejrzanych działań.

Wdrażanie zarządzania tożsamościami uprzywilejowanymi usługi Azure AD (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Key Vault (wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alerty dotyczące usługi Azure Key Vault (wersja zapoznawcza):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Wykrywanie ryzyka usługi Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Skonfiguruj stan lokalizacji zasad dostępu warunkowego i zarządzaj nazwanymi lokalizacjami. W nazwanych lokalizacjach można tworzyć logiczne grupy zakresów adresów IP lub krajów i regionów. Dostęp do poufnych zasobów, takich jak wpisy tajne usługi Key Vault, można ograniczyć do skonfigurowanych nazwanych lokalizacji.

Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3.9: Korzystanie z usługi Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (AAD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Usługa Key Vault. Dzięki temu kontrola dostępu oparta na rolach (RBAC) do administrowania poufnych zasobów.

 

Szybki start: tworzenie nowej dzierżawy w usłudze Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Przejrzyj dzienniki usługi Azure Active Directory (AAD), aby ułatwić odnajdywanie starych kont za pomocą ról administracyjnych usługi Azure Key Vault. Ponadto użyj przeglądów dostępu AAD, aby skutecznie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw, które mogą być używane do uzyskiwania dostępu do usługi Azure Key Vault, oraz przydziałami ról. Dostęp użytkowników powinien być regularnie sprawdzany, na przykład co 90 dni, aby upewnić się, że tylko właściwi użytkownicy mają stały dostęp.

Raporty usługi Azure Active Directory i dokumentacja monitorowania:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Co to są recenzje dostępu usługi Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych kont

**Wskazówki:** Włącz ustawienia diagnostyczne usługi Azure Key Vault i usługi Azure Active Directory, wysyłając wszystkie dzienniki do obszaru roboczego usługi Log Analytics. Skonfiguruj żądane alerty (takie jak próby uzyskania dostępu do wyłączonych wpisów tajnych) w usłudze Log Analytics.

Integruj dzienniki usługi Azure AD z dziennikami usługi Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migracja ze starego rozwiązania usługi Key Vault:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert o odchyleniu zachowania logowania do konta

**Wskazówki:** Użyj funkcji ochrony tożsamości usługi Azure Active Directory i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z zasobami chronionymi usługi Azure Key Vault. Należy włączyć automatyczne odpowiedzi za pośrednictwem usługi Azure Sentinel do zaimplementowania odpowiedzi zabezpieczeń organizacji. 

Raport ryzykownych logów w portalu usługi Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Jak: Konfigurowanie i włączanie zasad ryzyka:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak ować wobec pokład Azure Wartownik:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki**: Nie dotyczy; Skrytka klienta nie jest obsługiwana w usłudze Azure Key Vault.

Obsługiwane usługi i scenariusze w ogólnej dostępności:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

**Wskazówki:** Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje w zasobach włączonych usługi Azure Key Vault. 

Użyj tagów do organizowania zasobów platformy Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Możesz zabezpieczyć dostęp do usługi Azure Key Vault, korzystając z punktów końcowych usługi sieci wirtualnej skonfigurowanych do ograniczania dostępu do określonych podsieci.

Po wprowadzeniu reguł zapory można wykonywać operacje na płaszczyźnie danych usługi Azure Key Vault tylko wtedy, gdy żądanie pochodzi z dozwolonych podsieci lub zakresów adresów IP. Dotyczy to również dostępu usługi Azure Key Vault w witrynie Azure portal. Chociaż można przejść do magazynu kluczy z witryny Azure portal, może nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli komputer kliencki nie znajduje się na liście dozwolonych. Dotyczy to również selektora usługi Azure Key Vault i innych usług platformy Azure. Może być w stanie zobaczyć listy magazynów kluczy, ale nie klucze listy, jeśli reguły zapory uniemożliwiają komputer klienckiemu.

Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

**Wskazówki:** Wszystkie dane przechowywane w usłudze Azure Key Vault są uważane za poufne. Użyj kontroli dostępu do płaszczyzny danych usługi Azure Key Vault, aby kontrolować dostęp do wpisów tajnych usługi Azure Key Vault. Można również użyć wbudowanej zapory usługi Key Vault do kontrolowania dostępu w warstwie sieciowej. Aby monitorować dostęp do usługi Azure Key Vault, włącz ustawienia diagnostyczne usługi Key Vault i wyślij dzienniki do obszaru roboczego usługi Azure Storage account lub Log Analytics.

Bezpieczny dostęp do magazynu kluczy:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Rejestrowanie usługi Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

**Wskazówki:** Cały ruch do usługi Azure Key Vault w celu uwierzytelniania, zarządzania i dostępu do płaszczyzny danych jest szyfrowany i przechodzi przez https: port 443. (Jednak od czasu do czasu będzie http [port 80] ruch dla CRL.) 

Dostęp do usługi Azure Key Vault za zaporą:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

**Wskazówki**: Nie dotyczy; wszystkie dane w usłudze Azure Key Vault (wpisy tajne, klucze i certyfikaty) są uważane za poufne.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

**Wskazówki:** Bezpieczny dostęp do płaszczyzny zarządzania i danych wystąpień usługi Azure Key Vault.

Bezpieczny dostęp do magazynu kluczy:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

**Wskazówki:** Firma Microsoft zarządza podstawową infrastrukturą usługi Azure Key Vault i wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub ekspozycji danych klientów.

Co to jest usługa Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Ochrona danych klientów platformy Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Wszystkie obiekty zarządzane (klucz, certyfikaty i wpisy tajne) są szyfrowane w spoczynku w usłudze Azure Key Vault.

Dokumentacją:

- [Tabela zarządzania modelem szyfrowania i kluczami](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

**Wskazówki:** Użyj rozwiązania usługi Azure Key Vault Analytics w usłudze Azure Monitor, aby przejrzeć dzienniki zdarzeń inspekcji usługi Azure Key Vault.

Rozwiązanie usługi Azure Key Vault Analytics w usłudze Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w podstawowych systemach obsługujących usługę Azure Key Vault.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Nie dotyczy; Firma Microsoft wykonuje zarządzanie poprawkami w systemach bazowych obsługujących usługę Key Vault.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu back-to-back

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w systemach bazowych obsługujących usługę Key Vault.

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Użyj procesu oceny ryzyka, aby nadać priorytet usuwaniu wykrytych luk w zabezpieczeniach

**Wskazówki:** Użyj domyślnych ocen ryzyka (Secure Score) dostarczonych przez usługę Azure Security Center.

Popraw swój bezpieczny wynik w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Korzystanie z odnajdowania zasobów platformy Azure

**Wskazówki:** Użyj usługi Azure Resource Graph do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień usługi Azure Key Vault) w ramach subskrypcji. Upewnij się, że masz odpowiednie (odczytu) uprawnienia w dzierżawie i są w stanie wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Szybki start: uruchom pierwszą kwerendę wykresu zasobów przy użyciu Eksploratora wykresu zasobów platformy Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uzyskaj subskrypcje, do których może uzyskać dostęp bieżące konto.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów usługi Azure Key Vault, podając metadane logicznie zorganizować je w taksonomię.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Użyj tagowania, grup zarządzania i oddzielnych subskrypcji, w stosownych przypadkach, do organizowania i śledzenia wystąpień usługi Azure Key Vault i powiązanych zasobów. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Utwórz dodatkową subskrypcję platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Tworzenie grup zarządzania dla organizacji zasobów i zarządzania zasobami:

https://docs.microsoft.com/azure/governance/management-groups/create

Użyj tagów do organizowania zasobów platformy Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Obsługa spisu zatwierdzonych zasobów i tytułów oprogramowania platformy Azure

**Wskazówki:** Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto użyj programu Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach subskrypcji.

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Szybki start: uruchom pierwszą kwerendę wykresu zasobów przy użyciu Eksploratora wykresu zasobów platformy Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla platformy Azure jako całości, a także zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady zasad platformy Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="610-implement-approved-application-list"></a>6.10: Wdrożenie listy zatwierdzonych wniosków

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Ogranicz możliwość interakcji użytkowników z usługą AzureResources Manager za pomocą skryptów

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager (ARM) przez skonfigurowanie "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i zmiany zasobów w środowisku wysokiego bezpieczeństwa, takich jak te z konfiguracją usługi Key Vault.

Zarządzanie dostępem do zarządzania platformą Azure za pomocą dostępu warunkowego:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci web działających w usłudze Azure App Service lub zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.KeyVault", aby utworzyć niestandardowe zasady do inspekcji lub wymuszenia konfiguracji wystąpień usługi Azure Key Vault. Można również użyć wbudowanych definicji zasad platformy Azure dla usługi Azure Key Vault, takich jak:

Obiekty magazynu kluczy powinny być odzyskiwalne

Wdrażanie ustawień diagnostycznych dla obszaru roboczego usługi Log Analytics w usłudze Hostowanie magazynu kluczy

Dzienniki diagnostyczne w magazynie kluczy powinny być włączone

Usługa Key Vault powinna używać punktu końcowego usługi sieci wirtualnej

Wdrażanie ustawień diagnostycznych dla usługi Key Vault w Centrum zdarzeń

Użyj zaleceń z usługi Azure Security Center jako linii bazowej bezpiecznej konfiguracji dla wystąpień usługi Azure Key Vault.

Jak wyświetlić dostępne aliasy zasad platformy Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach obsługujących usługę Azure Key Vault. 

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Poznaj efekty zasad platformy Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych definicji zasad platformy Azure dla zasobów obsługujących usługę Azure Key Vault, użyj aplikacji Repozytorium platformy Azure do bezpiecznego przechowywania kodu i zarządzania nim.

Jak przechowywać kod w usłudze Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Dokumentacja repozytorium platformy Azure: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki:** Użyj aliasów zasad platformy Azure w obszarze nazw "Microsoft.KeyVault", aby utworzyć niestandardowe zasady do ostrzegania, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

**Wskazówki:** Korzystanie z usługi Azure Security Center w celu wykonywania skanowania według planu bazowego zasobów chronionych przez usługę Azure Key Vault 

  

Jak skorygować zalecenia w usłudze Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

**Wskazówki**: Nie dotyczy; ten wskaźnik jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze. Upewnij się, że usługa Azure Key Vault jest włączona.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie kluczami tajnymi dla aplikacji w chmurze. 

  

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Jak utworzyć przechowalnię kluczy: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

**Wskazówki:** Implementowanie skanera poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault.  
  
 Jak skonfigurować Skaner poświadczeń:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym oprogramowaniem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Przed skanowaniem plików, które mają zostać przesłane do zasobów platformy Azure nieoliczonych

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na głównym hoście, który obsługuje usługi platformy Azure (na przykład usługa Azure Key Vault), jednak nie działa na zawartości klienta.

Wstępnie zeskanuj dowolną zawartość przekazywanych lub wysyłanych do nie obliczających zasobów platformy Azure, takich jak Usługa Azure Key Vault. Firma Microsoft nie może uzyskać dostępu do danych użytkownika w takich przypadkach.

Poznaj oprogramowanie antywirusowe firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy chroniące przed złośliwym oprogramowaniem są aktualizowane

**Wskazówki**: Nie dotyczy; to zalecenie jest przeznaczone dla zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym oprogramowaniem dla podstawowej platformy.


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

**Wskazówki:** Zapewnij regularne automatyczne tworzenie kopii zapasowych certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Kopia zapasowa-Certyfikat AzKeyVaultCertificate

- Kopia zapasowa-AzKeyVaultKey

- Kopia zapasowa —AzKeyVaultManagedStorageKonta

- Kopia zapasowa-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe usługi Key Vault w ramach usługi Azure Backup.

Jak wykonać kopię zapasową certyfikatów magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak wykonać kopię zapasową kluczy magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Jak wykonać kopię zapasową zarządzanych kont magazynu usługi Key Vault:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak wykonać kopię zapasową key vault secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak włączyć usługę Azure Backup:https://docs.microsoft.com/azure/backup



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Wykonywanie kopii zapasowych certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Kopia zapasowa-Certyfikat AzKeyVaultCertificate

- Kopia zapasowa-AzKeyVaultKey

- Kopia zapasowa —AzKeyVaultManagedStorageKonta

- Kopia zapasowa-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe usługi Key Vault w ramach usługi Azure Backup.

Jak wykonać kopię zapasową certyfikatów magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak wykonać kopię zapasową kluczy magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Jak wykonać kopię zapasową zarządzanych kont magazynu usługi Key Vault:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak wykonać kopię zapasową key vault secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak włączyć usługę Azure Backup:https://docs.microsoft.com/azure/backup



**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

**Wskazówki:** Okresowo przeprowadzaj przywracanie danych certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Certyfikat Przywracania-AzKeyVaultCertificate

- Przywracanie-AzKeyVaultKey

- Przywróć konto —AzKeyVaultManagedStorageAccount

- Przywróć—AzKeyVaultSecret

Jak przywrócić certyfikaty magazynu kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Jak przywrócić klucze przechowalni kluczy:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Jak przywrócić konta magazynu zarządzanego w przechowalni kluczy:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Jak przywrócić klucze vault secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Upewnij się, że usługa usuwania nietrwałego jest włączona dla usługi Azure Key Vault. Usuwanie programowe umożliwia odzyskiwanie usuniętych magazynów kluczy i obiektów przechowalni, takich jak klucze, wpisy tajne i certyfikaty. 

Jak korzystać z nietrwałego usuwania usługi Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Reagowanie na incydenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

**Wskazówki:** Tworzenie przewodnika po zdarzeniach dla twojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie. Procesy te powinny koncentrować się na ochronie poufnych systemów, takich jak te przy użyciu wpisów tajnych usługi Key Vault.

Jak skonfigurować automatyzacje przepływu pracy w usłudze Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Klient może również wykorzystać przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

**Wskazówki:** Centrum zabezpieczeń przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje. Dodatkowo wyraźnie zaznacz subskrypcje (np. produkcji, non-prod) i utworzyć system nazewnictwa, aby wyraźnie zidentyfikować i kategoryzować zasoby platformy Azure, zwłaszcza tych, które przetwarzają poufne dane, takie jak wpisy tajne usługi Azure Key Vault.


**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki: Przeprowadzaj**ćwiczenia, aby przetestować funkcje reagowania na incydenty w systemach w regularnym rytmie, aby chronić wystąpienia usługi Azure Key Vault i powiązane zasoby. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

Zapoznaj się z publikacją NIST: Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

**Wskazówki:** Informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dane użytkownika zostały uzyskiane przez osobę niezgodną z prawem lub nieautoryzowaną.  Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić kontakt zabezpieczeń usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie usługi Azure Security Center**: Tak

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

**Wskazówki:** Eksportuj alerty i zalecenia usługi Azure Security Center za pomocą funkcji ciągłego eksportowania, aby ułatwić identyfikowanie zagrożeń związanych z zasobami obsługującymi usługę Azure Key Vault. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły.  Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Azure Sentinel. 

 

Jak skonfigurować eksport ciągły: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Jak przesyłać strumieniowo alerty do usługi Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń w celu ochrony zasobów chronionych przez usługę Azure Key Vault. 

 

Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola bezpieczeństwa: Testy penetracyjne i Ćwiczenia zespołu Czerwonego](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Przeprowadzaj regularne testy penetracyjne zasobów platformy Azure i zapewnij korygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki:** Nie należy przeprowadzać testów pióra bezpośrednio w usłudze Azure Key Vault, jednak zaleca się przetestowanie zasobów platformy Azure, które są przy użyciu usługi Key Vault w celu zapewnienia bezpieczeństwa wpisów tajnych.

Aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft, należy przestrzegać zasad zaangażowania firmy Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Więcej informacji na temat strategii firmy Microsoft i wykonywania testów red teaming i testów penetracji witryn na żywo w infrastrukturze, usługach i aplikacjach zarządzanych przez firmę Microsoft można znaleźć tutaj: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie usługi Azure Security Center:** nie dotyczy

**Odpowiedzialność**: Współdzielone

## <a name="next-steps"></a>Następne kroki

- Zobacz [wzorzec zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [planach bazowych zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
