---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault
description: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7d116010d5e6fea5e1ba8a66431b874194506283
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843455"
---
# <a name="azure-security-baseline-for-key-vault"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi Key Vault

Podstawą zabezpieczeń platformy Azure dla Key Vault są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: integracja Azure Key Vault z prywatnym łączem platformy Azure. 

Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Jak zintegrować Key Vault z prywatnym łączem platformy Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby zabezpieczyć zasoby skonfigurowane Key Vault na platformie Azure. 

Aby uzyskać więcej informacji o zabezpieczeniach sieci dostarczonych przez Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu Azure DDoS Protection w sieciach wirtualnych platformy Azure skojarzonych z wystąpieniami Key Vault w celu ochrony przed rozproszonymi atakami typu "odmowa usługi". Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

 
Zarządzaj standardem Azure DDoS Protection przy użyciu Azure Portal: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: Azure Key Vault nie korzysta z sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i dzienników przepływów dla Azure Key Vault nie są przechwytywane. Zamiast tego należy użyć prywatnego linku platformy Azure w celu zabezpieczenia wystąpień Azure Key Vault i włączenia ustawień diagnostycznych w celu rejestrowania metryk i zdarzeń inspekcji.

Integruj Key Vault z prywatnym łączem platformy Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Rejestrowanie Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: to wymaganie można spełnić, konfigurując zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure Key Vault. ATP stanowi dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby dostępu do kont Azure Key Vault lub ich wykorzystania.

Gdy Azure Security Center wykrywa nietypowe działanie, wyświetla alerty. Również wysyła do administratora subskrypcji wiadomość e-mail z informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zidentyfikowanych zagrożeń.

Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do wystąpień Azure Key Vault, użyj tagów usługi platformy Azure dla Azure Key Vault, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapory platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Omówienie tagów usługi platformy Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z wystąpieniami Azure Key Vault przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft." i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Key Vault. Mogą również używać wbudowanych definicji zasad związanych z Azure Key Vault, takich jak:

Key Vault powinien używać punktu końcowego usługi sieci wirtualnej

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Szybki Start: Definiowanie i Przypisywanie planu w portalu:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień Azure Key Vault, aby zapewnić metadane i organizację logiczną.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

Użyj tagów do organizowania zasobów platformy Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Key Vault. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

Wyświetl i Pobierz zdarzenia dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi przy użyciu Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: nie dotyczy; Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Azure Key Vault, dla sygnatur czasowych w dziennikach.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Key Vault. W ramach Azure Monitor Użyj obszaru roboczego usługi Azure Log Analytics do wykonywania zapytań i przeprowadzania analiz, a następnie korzystaj z kont usługi Azure Storage w celu przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

Rejestrowanie Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Szybki Start: jak przejść do tablicy wskaźnikowej platformy Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych w wystąpieniach Azure Key Vault, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

Rejestrowanie Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor w obszarze roboczym log Analytics używanym do przechowywania dzienników Azure Key Vault należy ustawić okres przechowywania zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

Zmień okres przechowywania danych: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularnego przeglądania wyników dla zasobów chronionych przez Azure Key Vault. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

Szybki Start: tablica wskaźnikowa platformy Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Rozpocznij pracę z Log Analytics w Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Rozpocznij pracę z kwerendami dzienników w Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: w Azure Security Center Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Key Vault. Włącz ustawienia diagnostyczne w Azure Key Vault i Wyślij dzienniki do Log Analytics obszaru roboczego. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

Szybki Start: tablica wskaźnikowa platformy Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Odpowiadanie na zdarzenia przy użyciu alertów Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Azure Key Vault nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Key Vault nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu aplikacji zarejestrowanych Azure Active Directory, a także wszystkich kont użytkowników, które mają dostęp do kluczy Azure Key Vault, wpisów tajnych i certyfikatów. Do wykonywania zapytań i uzgadniania dostępu Key Vault można użyć Azure Portal lub programu PowerShell. Aby wyświetlić dostęp w programie PowerShell, użyj następującego polecenia:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Właściwości. AccessPolicies

Rejestrowanie aplikacji z Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Bezpieczny dostęp do magazynu kluczy:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: nie dotyczy; Azure Key Vault nie ma koncepcji domyślnych haseł, ponieważ uwierzytelnianie jest zapewniane przez Active Directory i zabezpieczone przy użyciu kontroli dostępu opartej na rolach (Azure RBAC).


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do wystąpień Azure Key Vault. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem (obecnie w wersji zapoznawczej), aby monitorować liczbę aktywnych kont administracyjnych.

Monitoruj tożsamość i dostęp (wersja zapoznawcza):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Użyj jednostki usługi platformy Azure w połączeniu z identyfikatorami AppID, TenantID i ClientSecret, aby bezproblemowo uwierzytelnić aplikację i pobrać token, który zostanie użyty w celu uzyskania dostępu do Azure Key Vault wpisów tajnych.

Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: włącz Azure Active Directory Multi-Factor Authentication i postępuj zgodnie z zaleceniami Azure Security Center Zarządzanie tożsamościami i dostępem (obecnie dostępne w wersji zapoznawczej), aby pomóc w ochronie zasobów z obsługą centrum zdarzeń.

Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartej na chmurze:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitoruj tożsamość i dostęp (wersja zapoznawcza):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Azure AD Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania Key Vault włączonych zasobów. 

Stacje robocze uprzywilejowanego dostępu: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartej na chmurze: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników. Aby uzyskać dodatkowe rejestrowanie, Wyślij Azure Security Center alerty wykrywania ryzyka do Azure Monitor i skonfiguruj niestandardowe alerty/powiadomienia przy użyciu grup akcji.

Włącz zaawansowaną ochronę przed zagrożeniami (ATP) dla Azure Key Vault, aby generować alerty dla podejrzanych działań.

Wdróż Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alerty dla Azure Key Vault (wersja zapoznawcza): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory wykrywania ryzyka: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Tworzenie grup akcji i zarządzanie nimi w Azure Portal: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Skonfiguruj warunek lokalizacji zasad dostępu warunkowego i Zarządzaj nazwanymi lokalizacjami. Przy użyciu nazwanych lokalizacji można tworzyć logiczne grupowania zakresów adresów IP lub krajów i regionów. Można ograniczyć dostęp do poufnych zasobów, takich jak wpisy tajne Key Vault, do skonfigurowanych lokalizacji nazwanych.

Jaki jest warunek lokalizacji w Azure Active Directory dostępu warunkowego?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla zasobów platformy Azure, takich jak Key Vault. Umożliwia to kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby administrowania poufnymi zasobami.

 

Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: przegląd dzienników usługi Azure Active Directory (Azure AD) w celu ułatwienia wykrywania starych kont przy użyciu Azure Key Vault ról administracyjnych. Ponadto za pomocą przeglądów dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do Azure Key Vault i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

Azure Active Directory raportów i dokumentacji monitorowania:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Co to są przeglądy dostępu do usługi Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Key Vault i Azure Active Directory, wysyłanie wszystkich dzienników do obszaru roboczego log Analytics. Skonfiguruj żądane alerty (takie jak próby uzyskania dostępu do wyłączonych wpisów tajnych) w ramach Log Analytics.

Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrowanie ze starego rozwiązania Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania dotyczące Azure Key Vault chronionych zasobów. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure. 

Raport dotyczący ryzykownych logowań w portalu Azure Active Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Instrukcje: Konfigurowanie i Włączanie zasad ryzyka: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak dołączyć wskaźnik na platformie Azure:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Azure Key Vault nie Skrytka klienta obsługiwane.

Obsługiwane usługi i scenariusze ogólnie dostępne: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne dotyczące Azure Key Vault włączonych zasobów. 

Użyj tagów do organizowania zasobów platformy Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: można zabezpieczyć dostęp do Azure Key Vault, korzystając z punktów końcowych usługi sieci wirtualnej skonfigurowanych w celu ograniczenia dostępu do określonych podsieci.

Po zastosowaniu reguł zapory można wykonywać Azure Key Vault operacje płaszczyzny danych tylko wtedy, gdy żądanie pochodzi z dozwolonych podsieci lub zakresów adresów IP. Dotyczy to również dostępu Azure Key Vault w Azure Portal. Mimo że możesz przejść do magazynu kluczy z Azure Portal, możesz nie być w stanie wyświetlić kluczy, wpisów tajnych lub certyfikatów, jeśli komputer kliencki nie znajduje się na liście dozwolonych. Ma to również wpływ na selektor Azure Key Vault i inne usługi platformy Azure. Mogą być widoczne listy magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają wykonanie tej czynności na komputerze klienckim.

Skonfiguruj Azure Key Vault zapory i sieci wirtualne: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: wszystkie dane przechowywane w Azure Key Vault są uważane za poufne. Użyj Azure Key Vault kontroli dostępu płaszczyzny danych, aby kontrolować dostęp do Azure Key Vault wpisów tajnych. Możesz również użyć wbudowanej zapory Key Vault, aby kontrolować dostęp do warstwy sieciowej. Aby monitorować dostęp do Azure Key Vault, Włącz Key Vault ustawienia diagnostyczne i Wyślij dzienniki do konta usługi Azure Storage lub obszaru roboczego Log Analytics.

Bezpieczny dostęp do magazynu kluczy: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Skonfiguruj Azure Key Vault zapory i sieci wirtualne: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Rejestrowanie Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: cały ruch do Azure Key Vault na potrzeby uwierzytelniania, zarządzania i dostępu do płaszczyzny danych, jest szyfrowany i przechodzi za pośrednictwem protokołu https: port 443. (Jednak czasami będzie to ruch HTTP [port 80] dla listy CRL). Azure Key Vault nadal zezwala na pozyskiwanie danych TLS 1,1 i TLS 1,0. Dane mogą być ograniczone do protokołu TLS 1,2 za pomocą konfiguracji po stronie klienta.



Dostęp Azure Key Vault za zaporą: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: nie dotyczy; wszystkie dane w Azure Key Vault (wpisy tajne, klucze i certyfikaty) są uznawane za poufne.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: bezpieczny dostęp do zarządzania i płaszczyzny danych wystąpień Azure Key Vault.

Bezpieczny dostęp do magazynu kluczy:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: Firma Microsoft zarządza podstawową infrastrukturą dla Azure Key Vault i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

Co to jest usługa Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Ochrona danych klienta platformy Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: wszystkie obiekty zarządzane (klucze, certyfikaty i wpisy tajne) są szyfrowane w stanie spoczynku w Azure Key Vault.

Dokumentacja pomocnicza:

- [Model szyfrowania i tabela zarządzania kluczami](../../security/fundamentals/encryption-atrest.md)


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: użyj rozwiązania Azure Key Vault Analytics w Azure monitor, aby przejrzeć dzienniki zdarzeń inspekcji Azure Key Vault.

Rozwiązanie Azure Key Vault Analytics w Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Key Vault.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: N/A; Firma Microsoft wykonuje zarządzanie poprawkami w systemach podstawowych, które obsługują Key Vault.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Key Vault.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

Popraw swój Bezpieczny wynik w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień Azure Key Vault) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uzyskaj subskrypcje, do których bieżące konto ma dostęp.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do Azure Key Vault zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia wystąpień Azure Key Vault i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Utwórz dodatkową subskrypcję platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Tworzenie grup zarządzania dla organizacji zasobów i zarządzania nimi:

https://docs.microsoft.com/azure/governance/management-groups/create

Użyj tagów do organizowania zasobów platformy Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla platformy Azure jako całości oraz zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Przykłady Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z programu AzureResources Manager za pośrednictwem skryptów

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager (ARM) przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak te z konfiguracją Key Vault.

Zarządzanie dostępem do zarządzania platformą Azure przy użyciu dostępu warunkowego:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datamagazyn", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień Azure Key Vault. Możesz również użyć wbudowanych Azure Policy definicji dla Azure Key Vault takich jak:

Key Vault obiektów powinno być możliwe do odzyskania

Wdróż ustawienia diagnostyczne dla Key Vault do Log Analytics obszaru roboczego

Dzienniki diagnostyczne w Key Vault powinny być włączone

Key Vault powinien używać punktu końcowego usługi sieci wirtualnej

Wdróż ustawienia diagnostyczne dla Key Vault w centrum zdarzeń

Użyj zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla wystąpień Azure Key Vault.

Jak wyświetlić dostępne aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w ramach zasobów z włączoną obsługą Azure Key Vault. 

Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Opis efektów Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla zasobów Azure Key Vault włączonych, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Dokumentacja Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. datamagazyn", aby utworzyć zasady niestandardowe do alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów chronionych Azure Key Vault 

  

Jak skorygować zalecenia w Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; Ten test porównawczy jest przeznaczony dla zasobów obliczeniowych.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze. Upewnij się, że jest włączone Azure Key Vault usuwania nietrwałego.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Jak przeprowadzić uwierzytelnianie w Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Jak przypisać zasady dostępu Key Vault:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć poufne Zarządzanie aplikacjami w chmurze. 

  

* [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Jak utworzyć Key Vault](quick-create-portal.md)

* [Jak przeprowadzić uwierzytelnianie w Key Vault](authentication.md)

* [Jak przypisać zasady dostępu Key Vault](assign-access-policy-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.  
  
 Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Key Vault), ale nie jest uruchamiane w treści klienta.

Wstępnie Skanuj zawartość przekazywaną lub wysłaną do zasobów platformy Azure nieobliczeniowych, takich jak Azure Key Vault. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft obsługuje oprogramowanie chroniące przed złośliwym kodem dla podstawowej platformy.


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: zapewnianie zwykłych zautomatyzowanych kopii zapasowych certyfikatów Key Vault, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe Key Vault w Azure Backup.

Jak utworzyć kopię zapasową Key Vault certyfikatów: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak utworzyć kopię zapasową kluczy Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Jak utworzyć kopię zapasową Key Vault zarządzanych kont magazynu: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak utworzyć kopię zapasową Key Vault Secret: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak włączyć Azure Backup: https://docs.microsoft.com/azure/backup



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: wykonywanie kopii zapasowych certyfikatów Key Vault, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcjonalnie można przechowywać kopie zapasowe Key Vault w Azure Backup.

Jak utworzyć kopię zapasową Key Vault certyfikatów: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak utworzyć kopię zapasową kluczy Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Jak utworzyć kopię zapasową Key Vault zarządzanych kont magazynu: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak utworzyć kopię zapasową Key Vault Secret: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak włączyć Azure Backup: https://docs.microsoft.com/azure/backup



**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Jak przywrócić Key Vault certyfikaty:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Jak przywrócić klucze Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Jak przywrócić Key Vault zarządzanych kont magazynu: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Jak przywrócić Key Vault wpisy tajne: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że funkcja usuwania nietrwałego jest włączona dla Azure Key Vault. Usuwanie nietrwałe umożliwia Odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty. 

Jak używać usuwania nietrwałego Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu. Te procesy powinny mieć fokus na ochronie poufnych systemów, takich jak te, które używają Key Vault Secret.

Jak skonfigurować automatyzację przepływu pracy w ramach Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia w centrum Microsoft Security Response:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Klient może także skorzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu. Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne, takie jak Azure Key Vault Secret.


**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w ramach regularnego erze, aby chronić wystąpienia Azure Key Vault i powiązane zasoby. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

Jak ustawić Azure Security Center kontaktu zabezpieczeń:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować ryzyko związane z Azure Key Vault zasobami. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły.  Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

 

Jak skonfigurować eksport ciągły: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowej: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi przez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony chronionych przez Azure Key Vault zasobów. 

 

Jak skonfigurować automatyzację przepływu pracy i Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń w ciągu 60 dni

**Wskazówki**: nie przeprowadzaj bezpośredniego testowania piórem w usłudze Azure Key Vault, jednak zaleca się przetestowanie zasobów platformy Azure korzystających z Key Vault w celu zapewnienia bezpieczeństwa wpisów tajnych.

Aby testy penetracji nie miały naruszenia zasad firmy Microsoft, należy przestrzegać reguł firmy Microsoft dotyczących zaangażowania:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w oparciu o infrastrukturę, usługi i aplikacje chmurowe zarządzane przez firmę Microsoft, należy tutaj: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../security/benchmarks/security-baselines-overview.md)