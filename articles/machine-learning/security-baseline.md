---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure Machine Learning
description: Punkt odniesienia Azure Machine Learning zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817003"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure Machine Learning

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0](../security/benchmarks/overview-v1.md) do Microsoft Azure Machine Learning. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure Machine Learning. **Kontrolki** nie dotyczy Azure Machine Learning zostały wykluczone.

 
Aby zobaczyć, Azure Machine Learning mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny Azure Machine Learning [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Azure Machine Learning się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (docelowe obiekty obliczeniowe) są używane do trenowania i wdrażania modeli. Te docelowe obiekty obliczeniowe można utworzyć w sieci wirtualnej. Możesz na przykład użyć usługi Azure Virtual Machine Learning wystąpienia obliczeniowego do trenowania modelu, a następnie wdrożyć model w usłudze Azure Kubernetes Service (AKS). Cykle życia uczenia maszynowego można zabezpieczyć, izolowając Azure Machine Learning trenowania i wnioskowania w ramach sieci wirtualnej platformy Azure.

Azure Firewall służy do kontrolowania dostępu do obszaru roboczego Azure Machine Learning i publicznego Internetu.

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

- [Używanie obszaru roboczego Azure Firewall na Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i karty sieciowe

**Wskazówki:** Azure Machine Learning się na innych usługach platformy Azure dla zasobów obliczeniowych. Przypisz sieciowe grupy zabezpieczeń do sieci, które są tworzone jako Machine Learning wdrożenia. 

Włącz dzienniki przepływu sieciowych grup zabezpieczeń i wyślij je do konta usługi Azure Storage w celu inspekcji. Możesz również wysłać dzienniki przepływu do obszaru roboczego usługi Log Analytics, a następnie użyć usługi Analiza ruchu, aby zapewnić wgląd we wzorce ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej, identyfikowania punktów aktywności i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i określenia błędnych konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3. Ochrona krytycznych aplikacji internetowych

**Wskazówki:** możesz włączyć protokół HTTPS, aby zabezpieczyć komunikację z usługami internetowymi wdrożonmi przez Azure Machine Learning. Usługi sieci Web są wdrażane w usługach Azure Kubernetes Services (AKS) lub Azure Container Instances (ACI) i zabezpieczają dane przesyłane przez klientów. Możesz również użyć prywatnego adresu IP z usługą AKS, aby ograniczyć ocenianie, dzięki czemu tylko klienci za siecią wirtualną będą mieć dostęp do usługi internetowej.

- [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włącz DDoS Protection Standardową w sieciach wirtualnych skojarzonych z wystąpieniem usługi Machine Learning, aby chronić się przed atakami DDoS (distributed denial-of-service). Użyj Azure Security Center zintegrowanego wykrywania zagrożeń do wykrywania komunikacji ze znanymi złośliwymi lub nieużywanymi internetowymi adresami IP.

Wdrażanie Azure Firewall na poszczególnych granicach sieci organizacji z włączonym filtrowaniem opartym na analizie zagrożeń i skonfigurowanym na "Alert i odmowa" dla złośliwego ruchu sieciowego.

- [Jak skonfigurować ochronę przed DDoS](../ddos-protection/manage-ddos-protection.md)

- [Używanie obszaru roboczego Azure Firewall na Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Aby uzyskać więcej informacji na temat wykrywania Azure Security Center zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** W przypadku maszyn wirtualnych z odpowiednim rozszerzeniem zainstalowanym w usługach Azure Machine Learning można włączyć przechwytywanie pakietów Network Watcher w celu zbadania anomalii działań. 

- [Jak utworzyć wystąpienie Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Wdrożenie wybranego rozwiązania zapory na poszczególnych granicach sieci organizacji w celu wykrywania i/lub blokowania złośliwego ruchu.

Wybierz ofertę z Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IPS z możliwościami inspekcji ładunku.  Jeśli inspekcja ładunku nie jest wymagana, można Azure Firewall analizy zagrożeń. Azure Firewall filtrowania opartego na analizie zagrożeń służy do alertów i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do konta Azure Machine Learning, użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład AzureMachineLearning) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odmówić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

Azure Machine Learning service dokumentuje listę tagów usługi dla jej docelowych obiektów obliczeniowych w sieci wirtualnej, która pomaga zminimalizować złożoność, można użyć jej jako wytycznych w zarządzaniu siecią.

- [Aby uzyskać więcej informacji na temat korzystania z tagów usług](../virtual-network/service-tags-overview.md)

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw Azure Machine Learning z Azure Policy. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.MachineLearning" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci Machine Learning nazw. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10. Udokumentuj reguły konfiguracji ruchu

**Wskazówki:** Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem Azure Machine Learning, aby logicznie zorganizować je zgodnie z taksonomią.

W przypadku zasobu w Azure Machine Learning wirtualnej, która obsługuje pole Opis, użyj go do udokumentowania reguł, które zezwalają na ruch do/z sieci.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów sieciowych i wykrywać zmiany zasobów sieciowych związanych z Azure Machine Learning. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki za pośrednictwem Azure Monitor, aby agregować dane zabezpieczeń generowane przez Azure Machine Learning. W Azure Monitor obszarów roboczych usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont usługi Azure Storage do przechowywania długoterminowego i archiwalnej. Alternatywnie można włączyć i dołączyć dane do usług Azure Sentinel lub zarządzania zdarzeniami zabezpieczeń i zdarzeniami innych firm (SIEM).

- [Jak skonfigurować dzienniki diagnostyczne dla Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** włączanie ustawień diagnostycznych zasobów platformy Azure w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i diagnostycznych. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, znacznik czasu, adresy źródłowe, adresy docelowe i inne przydatne elementy.

Można również skorelować Machine Learning service operacji na potrzeby zabezpieczeń i zgodności.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Włączanie rejestrowania w Azure Machine Learning](how-to-log-view-metrics.md)

- [Monitorowanie Azure Machine Learning](monitor-azure-machine-learning.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za zbieranie i monitorowanie go. 

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku wszystkich zasobów obliczeniowych należących do organizacji użyj Azure Security Center do monitorowania systemu operacyjnego. 

- [Jak zbierać dzienniki wewnętrznych hostów maszyny wirtualnej platformy Azure za pomocą Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Informacje Azure Security Center zbierania danych](../security-center/security-center-enable-data-collection.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z wystąpieniami usługi Azure Machine Learning zgodnie z przepisami zgodności obowiązującymi w organizacji.

- [Jak ustawić parametry przechowywania dzienników](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników Azure Machine Learning. Użyj Azure Monitor i obszaru roboczego usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Jak wykonywać zapytania dotyczące Azure Machine Learning w obszarach roboczych usługi Log Analytics](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Włączanie rejestrowania w Azure Machine Learning](how-to-log-view-metrics.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do zapytań usługi Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać zapytania niestandardowe w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** w programie Azure Monitor skonfiguruj dzienniki związane z usługą Azure Machine Learning w dzienniku aktywności oraz ustawienia diagnostyczne usługi Machine Learning w celu wysyłania dzienników do obszaru roboczego usługi Log Analytics, które mają być wyszukiwane, lub na konto magazynu w celu przechowywania długoterminowego archiwizacji. Obszar roboczy usługi Log Analytics umożliwia tworzenie alertów dotyczących anomalii działań znalezionych w dziennikach zabezpieczeń i zdarzeniach.

Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel.

- [Aby uzyskać więcej informacji na temat Azure Machine Learning alertów](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Jak rejestrować alerty dotyczące danych dziennika obszaru roboczego usługi Log Analytics](/azure/azure-monitor/learn/tutorial-response)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizowanie rejestrowania ochrony przed złośliwym oprogramowaniem

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za wdrożenie oprogramowania Azure Machine Learning service.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji włącz zbieranie zdarzeń ochrony przed złośliwym oprogramowaniem na Microsoft Antimalware na Azure Cloud Services i Virtual Machines.

- [Jak skonfigurować rozszerzenie Microsoft Antimalware dla usług w chmurze](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Opis Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Nie dotyczy; Azure Machine Learning nie przetwarza ani nie tworzyć dzienników związanych z systemem DNS.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj Azure Security Center, aby włączyć monitorowanie dziennika zdarzeń zabezpieczeń dla maszyn wirtualnych platformy Azure. Azure Security Center aprowizuje agenta usługi Log Analytics na wszystkich obsługiwanych maszyn wirtualnych platformy Azure oraz wszystkich nowych, które są tworzone, jeśli włączono automatyczną aprowizowanie. Możesz też zainstalować agenta ręcznie. Agent włącza zdarzenie tworzenia procesu 4688 i pole wiersza polecenia wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane Security Center usług wykrywania danych.

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** możesz użyć karty Zarządzanie tożsamościami i dostępem dla zasobu w usłudze Azure Portal, aby skonfigurować kontrolę dostępu na podstawie ról (RBAC) platformy Azure i zachować spis zasobów Azure Machine Learning zasobów. Role są stosowane do użytkowników, grup, jednostki usługi i tożsamości zarządzanych w usłudze Active Directory. Możesz użyć ról wbudowanych lub ról niestandardowych dla użytkowników indywidualnych i grup.

Azure Machine Learning wbudowane role dla typowych scenariuszy zarządzania w Azure Machine Learning. Osoba mająca profil w usłudze Azure Active Directory (Azure AD) może przypisać te role użytkownikom, grupom, jednostkom usługi lub tożsamościom zarządzanym, aby udzielić lub odmówić dostępu do zasobów i operacji Azure Machine Learning zasobów.

Moduł Azure AD PowerShell umożliwia również wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Opis kontroli dostępu opartej na rolach na platformie Azure w Azure Machine Learning](how-to-assign-roles.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Zarządzanie dostępem do Machine Learning jest kontrolowane za pośrednictwem Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji haseł domyślnych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Używanie dedykowanych kont administracyjnych

**Wskazówki:** Azure Machine Learning się z trzema domyślnymi rolami podczas tworzenia nowego obszaru roboczego, tworząc standardowe procedury operacyjne dotyczące korzystania z kont właściciela.

Dostęp just in time do kont administracyjnych można również włączyć przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management i Azure Resource Manager.

- [Aby dowiedzieć się więcej Machine Learning domyślnych ról](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (SSO) z Azure Active Directory

**Wskazówki:** Machine Learning jest zintegrowana z usługą Azure Active Directory (Azure AD), użyj logowania jednokrotnego usługi Azure AD zamiast konfigurowania indywidualnych poświadczeń autonomicznych dla usługi. Używaj Azure Security Center dotyczących tożsamości i dostępu.

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na danych

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie Azure Security Center zaleceniami dotyczącymi tożsamości i dostępu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Użyj bezpiecznej stacji roboczej zarządzanej przez platformę Azure (nazywanej również stacją roboczą z dostępem uprzywilejowanym lub stacją roboczą z dostępem uprzywilejowanym) do wykonywania zadań administracyjnych wymagających podniesionych uprawnień.

- [Opis bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** użyj Azure Active Directory zabezpieczeń i monitorowania usługi Azure AD, aby wykryć, kiedy w środowisku występuje podejrzane lub niebezpieczne działanie. Użyj Azure Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** użyj Azure Active Directory (Azure AD), aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje usługi Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Używanie Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowych i podczas przesyłania. Usługa Azure AD dodaje również iniekty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.
 
Zakres dostępu do ról może być ograniczony do wielu poziomów na platformie Azure. Na Machine Learning można zarządzać rolami na poziomie obszaru roboczego, na przykład masz dostęp właściciela do obszaru roboczego, który może nie mieć dostępu właściciela do grupy zasobów, która zawiera obszar roboczy. Zapewnia to bardziej szczegółową kontrolę dostępu do oddzielnych ról w tej samej grupie zasobów. 

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md) 
 
- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto za pomocą przeglądów dostępu i tożsamości usługi Azure AD można wydajnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

Użyj Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów, gdy w środowisku wystąpi podejrzane lub niebezpieczne działanie.

- [Informacje o raportowaniu w usłudze Azure AD](/azure/active-directory/reports-monitoring)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wdrażanie Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Masz dostęp do Azure Active Directory logowania, inspekcji i dzienników zdarzeń o ryzyku, które umożliwiają integrację z dowolnym narzędziem SIEM/monitorowaniem.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania do konta

**Wskazówki:** użyj Azure Active Directory (Azure AD) Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel celu dalszego badania.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** Nie dotyczy; Azure Machine Learning service nie obsługuje skrytki klienta.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.
 
- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Odizoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie izolacji przy użyciu oddzielnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska przedsiębiorstwa. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem kontroli dostępu na kontach platformy Azure.
 
- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

 
- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Użyj rozwiązania innej firmy firmy Azure Marketplace w obwodach sieci w celu monitorowania nieautoryzowanego transferu poufnych informacji i blokowania takich transferów podczas powiadamiania specjalistów ds. zabezpieczeń informacji. 

W odniesieniu do platformy podstawowej zarządzanej przez firmę Microsoft, firma Microsoft traktuje całą zawartość kliencką jako poufną i zapewnia ochronę przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** Usługi sieci Web wdrożone za pośrednictwem usługi Azure Machine Learning obsługują tylko szyfrowanie TLS w wersji 1.2, które wymusza szyfrowanie danych podczas przesyłania.

- [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Machine Learning. W razie potrzeby zaim implementuj rozwiązanie innej firmy w celu zapewnienia zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i bardzo długo chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** usługa Azure Machine Learning obsługuje Azure Active Directory (Azure AD) w celu autoryzowania żądań do Machine Learning zasobów. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby udzielić uprawnień do podmiotu zabezpieczeń, który może być użytkownikiem lub jednostką usługi aplikacji.

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md)

- [Używanie kontroli RBAC platformy Azure na użytek autoryzacji platformy Kubernetes](../aks/manage-azure-rbac.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Azure Machine Learning migawki, dane wyjściowe i dzienniki są przechowywane na koncie usługi Azure Blob Storage, które jest powiązane z obszarem roboczym Azure Machine Learning subskrypcją. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft. Możesz również szyfrować dane przechowywane w usłudze Azure Blob Storage przy użyciu własnych kluczy w Machine Learning service. 

- [Azure Machine Learning danych w spoczynku](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Jak skonfigurować klucze szyfrowania zarządzane przez klienta](../storage/common/customer-managed-keys-configure-key-vault.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają miejsce w wystąpieniach produkcyjnych Azure Machine Learning i innych krytycznych lub powiązanych zasobach.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za zarządzanie lukami w zabezpieczeniach Azure Machine Learning service. 

Azure Machine Learning ma różną obsługę różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami firmy Azure Security Center przeprowadzania ocen luk w zabezpieczeniach na maszynach wirtualnych platformy Azure, obrazach kontenerów i serwerach SQL.

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami systemu operacyjnego

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za zarządzanie poprawkami Azure Machine Learning service. 

Azure Machine Learning ma różną obsługę różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku wszystkich zasobów obliczeniowych należących do organizacji użyj usługi Azure Automation Update Management, aby upewnić się, że najnowsze aktualizacje zabezpieczeń są zainstalowane na twoich komputerach wirtualnych z systemami Windows i Linux. W przypadku maszyn wirtualnych z systemem Windows upewnij się, Windows Update została włączona i ustawiona na automatyczną aktualizację.

- [Jak skonfigurować konfigurację Update Management maszyn wirtualnych na platformie Azure](../automation/update-management/overview.md)

- [Opis zasad zabezpieczeń platformy Azure monitorowanych przez Security Center](../security-center/policy-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji należy użyć rozwiązania do zarządzania poprawkami innej firmy. Klienci, którzy już Menedżer konfiguracji w swoim środowisku, mogą również używać usługi System Center Updates Publisher, co umożliwia im publikowanie aktualizacji niestandardowych w usłudze Windows Server Update Service. Dzięki temu Update Management poprawki maszyn, które Menedżer konfiguracji jako repozytorium aktualizacji za pomocą oprogramowania innych firm.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu wstecz

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami firmy Azure Security Center w zakresie przeprowadzania ocen luk w zabezpieczeniach na maszynach wirtualnych platformy Azure, obrazach kontenerów i serwerach SQL. Wyeksportuj wyniki skanowania w spójnych odstępach czasu i porównaj je z poprzednimi skanowaniami, aby sprawdzić, czy luki w zabezpieczeniach zostały skorygowane. Korzystając z zaleceń dotyczących zarządzania lukami w zabezpieczeniach sugerowanych przez Azure Security Center, można przesunić się do portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** użyj Azure Resource Graph do wykonywania zapytań o zasoby (takie jak obliczenia, magazyn, sieć, porty i protokoły itp.) w subskrypcjach i odnajdowanie ich. Upewnij się, że w dzierżawie są odpowiednie uprawnienia (odczyt) i wyliczenie wszystkich subskrypcji platformy Azure, a także zasobów w twoich subskrypcjach.

Mimo że klasyczne zasoby platformy Azure można znaleźć za Azure Resource Graph Explorer, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlać subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure, dodawanie metadanych w celu logicznego organizowania zgodnie z taksonomią.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** do organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji tam, gdzie jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.

- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [ Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)
 
- [ Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto użyj tego Azure Resource Graph do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj Azure Automation Śledzenie zmian i spis, aby zautomatyzować zbieranie informacji o spisie z maszyn wirtualnych z systemami Windows i Linux. Nazwa oprogramowania, wersja, wydawca i czas odświeżania są dostępne w Azure Portal. Aby uzyskać datę instalacji oprogramowania i inne informacje, włącz diagnostykę na poziomie gościa i przekieruj dzienniki zdarzeń systemu Windows do obszaru roboczego usługi Log Analytics.

- [Jak włączyć zbieranie Azure Automation spisu dla maszyny wirtualnej](../automation/automation-tutorial-installed-software.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj programu Azure Security Center File Integrity Monitoring (FIM), aby zidentyfikować całe oprogramowanie zainstalowane na maszynach wirtualnych. Inną opcją, która może być używana zamiast lub w połączeniu z programem FIM, jest Azure Automation Śledzenie zmian i spis zbierania spisu z maszyn wirtualnych z systemami Linux i Windows. 

Możesz zaimplementować własny proces usuwania nieautoryzowanego oprogramowania. Możesz również użyć rozwiązania innej firmy, aby zidentyfikować niezatwierdzone oprogramowanie.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Jak używać monitorowania integralności plików](../security-center/security-center-file-integrity-monitoring.md)

- [Opis Azure Automation Śledzenie zmian i spis](../automation/change-tracking/overview.md)

- [Jak włączyć spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Usuwanie grupy zasobów i zasobów platformy Azure](../azure-resource-manager/management/delete-resource-group.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj funkcji adaptacyjnego sterowania aplikacją usługi Azure Security Center, aby zagwarantować, że w usłudze Azure Virtual Machines będzie można wykonywać tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane Virtual Machines.

- [Jak używać funkcji adaptacyjnego sterowania Azure Security Center aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9. Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto użyj funkcji Azure Resource Graph do wykonywania zapytań o zasoby i odnajdywania ich w subskrypcjach.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku wszystkich zasobów obliczeniowych należących do organizacji użyj funkcji adaptacyjnego sterowania Azure Security Center, aby określić typy plików, których może dotyczyć reguła.

Jeśli funkcje adaptacyjnego sterowania aplikacją nie spełniają wymagań, zaim implementuj rozwiązanie innej firmy.

- [Jak używać funkcji adaptacyjnego sterowania Azure Security Center aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** użyj Azure Active Directory warunkowego usługi Azure AD, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resources Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do usługi Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Ograniczanie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji, w zależności od typu skryptów, można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć użytkownikom możliwość wykonywania skryptów w zasobach obliczeniowych platformy Azure. Za pomocą funkcji adaptacyjnego sterowania Azure Security Center można również zagwarantować, że w usłudze Azure Virtual Machines będzie można wykonywać tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane Virtual Machines.

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Jak używać funkcji adaptacyjnego sterowania Azure Security Center aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla Azure Machine Learning service za pomocą Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.MachineLearning", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji Azure Machine Learning usług.

Azure Resource Manager szablon można wyeksportować w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji.

Możesz również użyć zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

Azure Machine Learning w pełni obsługuje repozytoria Git do śledzenia pracy; Repozytoria można klonować bezpośrednio do systemu plików udostępnionego obszaru roboczego, używać narzędzia Git na lokalnej stacji roboczej i upewnić się, że bezpieczne konfiguracje są stosowane do zasobów kodu w Machine Learning roboczym.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksportowanie pojedynczego zasobu i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za bezpieczne konfiguracje zasobów Azure Machine Learning service.

Azure Machine Learning ma różną obsługę różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji użyj zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów obliczeniowych.  Ponadto można użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation stanu w celu ustalenia konfiguracji zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować Azure Security Center zalecenia](../security-center/security-center-recommendations.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration omówienie](../automation/automation-dsc-overview.md)

- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Tworzenie maszyny wirtualnej z systemem Linux na użyciu dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/upload-vhd.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure. Ponadto można użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganą przez organizację. 
 
- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)
 
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
 
- [ omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za bezpieczne konfiguracje Azure Machine Learning service.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami Azure Security Center oceny luk w zabezpieczeniach zasobów obliczeniowych platformy Azure.  Ponadto można użyć szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub Azure Automation State Configuration, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganą przez organizację.   Szablony maszyn wirtualnych firmy Microsoft w połączeniu z Azure Automation State Configuration mogą pomóc w spełnianiu i spełnianiu wymagań dotyczących zabezpieczeń. 

Należy pamiętać, Azure Marketplace maszyny wirtualnej Obrazy opublikowane przez firmę Microsoft są zarządzane i utrzymywane przez firmę Microsoft. 

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu usługi ARM](../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration omówienie](../automation/automation-dsc-overview.md)

- [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

- [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy dla swojego Machine Learning lub powiązanych zasobów, użyj Azure Repos do bezpiecznego przechowywania kodu i zarządzania nim.

Azure Machine Learning w pełni obsługuje repozytoria Git do śledzenia pracy; Repozytoria można klonować bezpośrednio do systemu plików udostępnionego obszaru roboczego, używać narzędzia Git na lokalnej stacji roboczej i upewnić się, że bezpieczne konfiguracje są stosowane do zasobów kodu w Machine Learning roboczym.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj kontroli dostępu na podstawie ról (RBAC) platformy Azure, aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskać dostęp do obrazów niestandardowych. Za pomocą usługi Azure Shared Image Gallery można udostępniać obrazy różnym użytkownikom, jednostkom usługi lub grupom Azure Active Directory (Azure AD) w organizacji. Przechowuj obrazy kontenerów w Azure Container Registry i korzystaj z kontroli RBAC platformy Azure, aby mieć pewność, że dostęp mają tylko autoryzowani użytkownicy.

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Understand Azure RBAC for Container Registry](../container-registry/container-registry-roles.md)

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Shared Image Gallery omówienie](../virtual-machines/shared-image-galleries.md)

- [Używanie kontroli RBAC platformy Azure na użytek autoryzacji platformy Kubernetes](../aks/manage-azure-rbac.md)

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy w przestrzeni nazw "Microsoft.MachineLearning", aby utworzyć zasady niestandardowe służące do alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za bezpieczne wdrażanie konfiguracji Azure Machine Learning service.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj Azure Automation State Configuration dla węzłów Desired State Configuration (DSC) w dowolnej chmurze lub lokalnym centrum danych. Maszyny można łatwo dołączać, przypisywać do nich konfiguracje deklaratywne i wyświetlać raporty przedstawiające zgodność poszczególnych maszyn z określonym żądanym stanem. 

- [Jak włączyć Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Security Center, aby przeprowadzić skanowanie bazowe zasobów platformy Azure. Ponadto użyj Azure Policy do alertów i inspekcji konfiguracji zasobów platformy Azure.
 
 
 
- [ Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** Jeśli zasób obliczeniowy jest własnością firmy Microsoft, firma Microsoft jest odpowiedzialna za automatyczne bezpieczne monitorowanie konfiguracji Azure Machine Learning service.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj usługi Azure Security Center Compute Apps i postępuj zgodnie z zaleceniami dla maszyn wirtualnych, serwerów &amp; i kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Używanie tożsamości zarządzanych w połączeniu z Azure Key Vault w celu uproszczenia zarządzania kluczami tajnymi dla aplikacji w chmurze.

Azure Machine Learning obsługuje szyfrowanie magazynu danych za pomocą kluczy zarządzanych przez klienta, należy zarządzać rotacją i odwoływaniem kluczy zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. 

Użyj Azure Key Vault do bezpiecznego przekazania wpisów tajnych do przebiegów zdalnych zamiast zwykłego tekstu w skryptach trenowania.

- [Azure Machine Learning kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Używanie wpisów tajnych poświadczeń uwierzytelniania Azure Machine Learning przebiegach trenowania](how-to-use-secrets-in-runs.md)

- [Jak używać tożsamości zarządzanych dla zasobów platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak uwierzytelnić się w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady Key Vault dostępu](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Azure Machine Learning obsługuje zarówno role wbudowane, jak i możliwość tworzenia ról niestandardowych. Tożsamości zarządzane zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md)

- [Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Korzystanie z centralnie zarządzanego oprogramowania do ochrony przed złośliwym oprogramowaniem

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na bazowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Machine Learning), jednak nie działa w przypadku zawartości klienta.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku zasobów obliczeniowych należących do organizacji użyj Microsoft Antimalware platformy Azure, aby stale monitorować i chronić zasoby. W przypadku systemu Linux użyj rozwiązania do ochrony przed złośliwym oprogramowaniem innej firmy. Ponadto należy Azure Security Center wykrywania zagrożeń dla usług danych w celu wykrywania złośliwego oprogramowania przekazywanego na konta magazynu.

- [Jak skonfigurować usługę Microsoft Antimalware platformy Azure](../security/fundamentals/antimalware.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Wstępne skanowanie plików do przesłania do zasobów platformy Azure bez obliczeń

**Wskazówki:** Ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure Machine Learning), ale nie jest uruchamiane na zawartości klienta.

To Ty odpowiadasz za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzać skanowania zawartości klienta w jego imieniu przez oprogramowanie chroniące przed złośliwym kodem.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Upewnij się, że oprogramowanie i podpisy ochrony przed złośliwym oprogramowaniem zostały zaktualizowane

**Wskazówki:** ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona i utrzymywana dla bazowego hosta, który obsługuje usługi platformy Azure (na przykład Azure Machine Learning), jednak nie działa na zawartości klienta.

Azure Machine Learning obsługuje różne zasoby obliczeniowe, a nawet własne zasoby obliczeniowe. W przypadku wszystkich zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami w te Azure Security Center, Compute Apps (Aplikacje obliczeniowe), aby upewnić się, że wszystkie punkty końcowe są aktualne i mają &amp; najnowsze podpisy. W przypadku systemu Linux użyj rozwiązania do ochrony przed złośliwym oprogramowaniem innej firmy.

- [Jak wdrożyć aplikację Microsoft Antimalware platformie Azure](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Zarządzanie odzyskiwaniem danych w Machine Learning service jest za pośrednictwem zarządzania danymi w połączonych magazynach danych. Upewnij się, że przestrzegasz wytycznych dotyczących odzyskiwania danych dla połączonych magazynów w celu przechowywania kopii zapasowej danych zgodnie z zasadami organizacji klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Tworzenie kopii zapasowej Machine Learning service jest za pośrednictwem zarządzania danymi w połączonych magazynach danych. Włącz Azure Backup dla maszyn wirtualnych i skonfiguruj żądaną częstotliwość i okresy przechowywania. Kopii zapasowej kluczy zarządzanych przez klienta w Azure Key Vault.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Walidacja kopii zapasowej danych w Machine Learning service jest za pośrednictwem zarządzania danymi w połączonych magazynach danych. Okresowe przywracanie danych zawartości w Azure Backup. Upewnij się, że możesz przywrócić kopię zapasową kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** W przypadku lokalnej kopii zapasowej szyfrowanie danych magazynowych jest zapewniane przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby chronić kopie zapasowe i klucze zarządzane przez klienta. 

Włącz usuwanie nieumyślne i ochronę przed przeczyszczaniem w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, włącz usuwanie nie softowe, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów blob.

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Usuwanie nie softne dla usługi Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Opracowywanie przewodnika reagowania na zdarzenia dla organizacji. Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują wszystkie role personelu, a także fazy obsługi zdarzeń i zarządzania od wykrywania do przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Azure Security Center przypisuje ważność do każdego alertu, aby ułatwić określanie priorytetów alertów, które powinny być badane w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizach używanych do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar.

Ponadto oznacz subskrypcje przy użyciu tagów i utwórz system nazewnictwa w celu identyfikowania i kategoryzowania zasobów platformy Azure, szczególnie tych przetwarzających poufne dane. To Ty odpowiadasz za ustalanie priorytetów korygowania alertów na podstawie krytyczności zasobów i środowiska platformy Azure, w których wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia w celu testowania możliwości reagowania na zdarzenia w systemach w regularnych odstępach czasu, aby ułatwić ochronę zasobów platformy Azure. Zidentyfikuj słabe punkty i luki, a następnie popraw plan reagowania zgodnie z potrzebami.

- [Publikacja NIST — przewodnik dotyczący programów testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli użytkownik Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że do Twoich danych dostęp uzyskał nieupoważniony lub niedozwolony podmiot. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportuj alerty Azure Security Center i zalecenia przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Eksport ciągły umożliwia ręczne lub ciągłe eksportowanie alertów i zaleceń. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu Azure Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Przeprowadzaj regularne testy penetrowania zasobów platformy Azure i zapewniaj korygowanie wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
