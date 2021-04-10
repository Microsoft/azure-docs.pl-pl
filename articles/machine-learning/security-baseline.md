---
title: Podstawa zabezpieczeń platformy Azure dla Azure Machine Learning
description: Linia bazowa zabezpieczeń Azure Machine Learning zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f8bde45cfdf9cf1c9d50faee76161461d8b0aa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607833"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Podstawa zabezpieczeń platformy Azure dla Azure Machine Learning

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Microsoft Azure Machine Learning. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Machine Learning. **Kontrolki** nie mają zastosowania do Azure Machine Learning zostały wykluczone.

 
Aby dowiedzieć się, jak Azure Machine Learning całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Machine Learning pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Azure Machine Learning opierają się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (cele obliczeniowe) są używane do uczenia i wdrażania modeli. Te obiekty docelowe obliczeń można utworzyć w sieci wirtualnej. Na przykład można użyć wystąpienia obliczeniowego usługi Azure Virtual Machine Learning do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). Możesz zabezpieczyć cykle uczenia maszynowego, izolując Azure Machine Learning szkolenia i zadania wnioskowania w ramach sieci wirtualnej platformy Azure.

Za pomocą zapory platformy Azure można kontrolować dostęp do obszaru roboczego Azure Machine Learning i publicznego Internetu.

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

- [Używanie obszaru roboczego za zaporą platformy Azure w celu Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Azure Machine Learning opierają się na innych usługach platformy Azure dla zasobów obliczeniowych. Przypisz sieciowe grupy zabezpieczeń do sieci utworzonych jako wdrożenie Machine Learning. 

Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji. Możesz również wysłać dzienniki przepływów do obszaru roboczego Log Analytics, a następnie użyć Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej, identyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: można włączyć protokół https, aby zabezpieczyć komunikację z usługami sieci Web wdrożonymi przez Azure Machine Learning. Usługi sieci Web są wdrażane w usłudze Azure Kubernetes Services (AKS) lub Azure Container Instances (ACI) i zabezpieczają dane przesyłane przez klientów. Możesz również użyć prywatnego adresu IP z AKS, aby ograniczyć ocenianie, tak aby tylko klienci znajdujący się za siecią wirtualną mogli uzyskiwać dostęp do usługi sieci Web.

- [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Włączanie standardu DDoS Protection w sieciach wirtualnych skojarzonych z wystąpieniem Machine Learning w celu ochrony przed rozproszonymi atakami typu "odmowa usługi" (DDoS). Użyj Azure Security Center zintegrowane wykrywanie zagrożeń, aby wykrywać komunikację ze znanymi złośliwymi lub nieużywanymi adresami IP.

Wdróż zaporę platformy Azure w każdej z granic sieci organizacji przy włączonej filtrowaniu opartym na analizie zagrożeń i skonfigurowanym jako "Alert i Odmów" w przypadku złośliwego ruchu sieciowego.

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

- [Używanie obszaru roboczego za zaporą platformy Azure w celu Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Aby uzyskać więcej informacji na temat Azure Security Center wykrywania zagrożeń](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: dla dowolnych maszyn wirtualnych z odpowiednim rozszerzeniem zainstalowanym w usługach Azure Machine Learning można włączyć funkcję przechwytywania pakietów Network Watcher w celu zbadania nietypowych działań. 

- [Jak utworzyć wystąpienie Network Watcher](../network-watcher/network-watcher-create.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: należy wdrożyć wybrane rozwiązanie zapory na wszystkich granicach sieci organizacji w celu wykrycia i/lub zablokowania złośliwego ruchu.

Wybierz ofertę z witryny Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Gdy inspekcja ładunku nie jest wymagana, można użyć analizy zagrożeń zapory platformy Azure. Filtrowanie oparte na analizie zagrożeń dla zapory platformy Azure służy do wyzwalania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do konta Azure Machine Learning, użyj Virtual Network tagów usługi, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład AzureMachineLearning) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Azure Machine Learning usługa dokumentuje listę tagów usługi dla swoich celów obliczeniowych w sieci wirtualnej, która pomaga zminimalizować złożoność, można użyć jej jako wytycznych w zarządzaniu siecią.

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

- [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw Azure Machine Learning przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. MachineLearning" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieciowej Machine Learning przestrzeni nazw. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem Azure Machine Learning, aby logicznie zorganizować je na podstawie taksonomii.

W przypadku zasobu w Azure Machine Learning sieci wirtualnej, który obsługuje pole Opis, użyj go do dokumentowania reguł zezwalających na ruch do/z sieci.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z Azure Machine Learning. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników przy użyciu Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez Azure Machine Learning. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM).

- [Jak skonfigurować dzienniki diagnostyczne dla Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych w zasobach platformy Azure w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

Można również skorelować dzienniki operacji usługi Machine Learning w celu zapewnienia bezpieczeństwa i zgodności.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Włącz rejestrowanie w Azure Machine Learning](how-to-track-experiments.md)

- [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za gromadzenie i monitorowanie. 

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku wszystkich zasobów obliczeniowych należących do organizacji Użyj Azure Security Center do monitorowania systemu operacyjnego. 

- [Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z wystąpieniami Azure Machine Learning zgodnie z regulacjami zgodności w organizacji.

- [Jak ustawić parametry przechowywania dziennika](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularne przeglądanie wyników Azure Machine Learning. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy. 

- [Jak wykonywać zapytania dotyczące Azure Machine Learning w Log Analytics obszarach roboczych](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Włącz rejestrowanie w Azure Machine Learning](how-to-track-experiments.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Wprowadzenie do Log Analytics zapytań](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: w Azure monitor Skonfiguruj dzienniki dotyczące Azure Machine Learning w dzienniku aktywności oraz Machine Learning ustawienia diagnostyczne, aby wysyłać dzienniki do obszaru roboczego log Analytics, aby można było wykonać zapytanie lub do konta magazynu w celu zapewnienia długoterminowego przechowywania danych. Użyj Log Analytics obszaru roboczego, aby utworzyć alerty dla nietypowej aktywności Znalezione w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Aby uzyskać więcej informacji na temat alertów Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Jak otrzymywać alerty dotyczące Log Analytics danych dziennika obszaru roboczego](/azure/azure-monitor/learn/tutorial-response)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za wdrożenie złośliwego oprogramowania Azure Machine Learning usługi.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji Włącz zbieranie zdarzeń chroniących przed złośliwym kodem dla programu Microsoft chroniącego przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines.

- [Jak skonfigurować rozszerzenie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla usług Cloud Services](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Machine Learning nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należy użyć Azure Security Center, aby włączyć monitorowanie dzienników zdarzeń zabezpieczeń dla usługi Azure Virtual Machines. Azure Security Center inicjuje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia automatycznej aprowizacji. Lub ręcznie zainstalować agenta. Agent włącza zdarzenie tworzenia procesu 4688 i pole wiersza polecenia wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center.

- [Zbieranie danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: możesz użyć karty Zarządzanie tożsamościami i dostępem dla zasobu w Azure Portal, aby skonfigurować kontrolę dostępu opartą na rolach (Azure RBAC) i zachować spis zasobów Azure Machine Learning. Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w Active Directory. Można używać wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup.

Azure Machine Learning udostępnia wbudowane role dla typowych scenariuszy zarządzania w programie Azure Machine Learning. Osoba mająca profil w usłudze Azure Active Directory (Azure AD) może przypisywać te role do użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych w celu udzielania lub odmawiania dostępu do zasobów i operacji na zasobach Azure Machine Learning.

Przy użyciu modułu Azure AD PowerShell można również wykonywać zapytania ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Informacje na temat kontroli dostępu opartej na rolach na platformie Azure w Azure Machine Learning](how-to-assign-roles.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: zarządzanie dostępem do zasobów Machine Learning jest kontrolowane za pomocą Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Azure Machine Learning zawiera trzy role domyślne podczas tworzenia nowego obszaru roboczego, tworząc standardowe procedury operacyjne dotyczące korzystania z kont właścicieli.

Możesz również włączyć dostęp just in Time do kont administracyjnych przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management i Azure Resource Manager.

- [Aby dowiedzieć się więcej Machine Learning ról domyślnych](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: usługa Machine Learning jest zintegrowana z usługą Azure Active Directory (Azure AD), zamiast konfigurować pojedyncze poświadczenia autonomiczne na usługę przy użyciu usługi Azure AD SSO. Użyj Azure Security Center zaleceń dotyczących tożsamości i dostępu.

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zaleceń dotyczących tożsamości i dostępu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego lub dostępem uprzywilejowanym) dla zadań administracyjnych, które wymagają podwyższonego poziomu uprawnień.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj raportów i monitorowania zabezpieczeń Azure Active Directory (Azure AD), aby wykryć, kiedy w środowisku występuje podejrzane lub niebezpieczne działania. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Zarządzaj zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj nazw lokalizacji Azure Active Directory (Azure AD), aby zezwolić na dostęp tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje usługi Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.
 
Dostęp do roli można ograniczyć do wielu poziomów na platformie Azure. W przypadku Machine Learning role mogą być zarządzane na poziomie obszaru roboczego, na przykład dostęp właściciela do obszaru roboczego może nie mieć dostępu właściciela do grupy zasobów, która zawiera obszar roboczy. Zapewnia to bardziej szczegółowe kontrole dostępu w celu oddzielenia ról w ramach tej samej grupy zasobów. 

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md) 
 
- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Użyj Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wdróż Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem w usłudze Azure Active Directory (Azure AD), inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Usługa Azure Machine Learning nie obsługuje skrytki klienta.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.
 
- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska korporacyjne. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem usługi Azure RBAC.
 
- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

 
- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Użyj rozwiązania innej firmy z witryny Azure Marketplace w obszarze sieci, aby monitorować nieautoryzowany transfer informacji poufnych i blokować takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji. 

W odniesieniu do platformy podstawowej zarządzanej przez firmę Microsoft, firma Microsoft traktuje całą zawartość kliencką jako poufną i zapewnia ochronę przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: usługi sieci Web wdrożone za pomocą Azure Machine Learning obsługują tylko protokół TLS w wersji 1,2, który wymusza szyfrowanie danych podczas przesyłania.

- [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Machine Learning. Zaimplementuj rozwiązanie innych firm, jeśli jest to niezbędne do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Azure Machine Learning obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań Machine Learning zasobów. Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji.

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md)

- [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](../aks/manage-azure-rbac.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie usługi Azure Blob Storage, które jest powiązane z obszarem roboczym Azure Machine Learning i subskrypcją. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft. Można także szyfrować dane przechowywane w usłudze Azure Blob Storage za pomocą własnych kluczy w usłudze Machine Learning. 

- [Azure Machine Learning szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](../storage/common/customer-managed-keys-configure-key-vault.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Azure Machine Learning i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za zarządzanie lukami w zabezpieczeniach usługi Azure Machine Learning Service. 

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami z Azure Security Center, aby przeprowadzać oceny luk w zabezpieczeniach na maszynach wirtualnych platformy Azure, na obrazach kontenerów i serwerach SQL.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za zarządzanie poprawkami usługi Azure Machine Learning. 

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku wszelkich zasobów obliczeniowych, których właścicielem jest organizacja, użyj Update Management Azure Automation, aby upewnić się, że najnowsze aktualizacje zabezpieczeń są zainstalowane na maszynach wirtualnych z systemami Windows i Linux. W przypadku maszyn wirtualnych z systemem Windows upewnij się, że Windows Update została włączona i ustawiona na automatyczne aktualizowanie.

- [Jak skonfigurować Update Management dla maszyn wirtualnych na platformie Azure](../automation/update-management/overview.md)

- [Informacje o zasadach zabezpieczeń platformy Azure monitorowanych przez Security Center](../security-center/policy-reference.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji Użyj rozwiązania do zarządzania poprawkami innej firmy. Klienci używający już Configuration Manager w ich środowisku mogą również używać System Center Updates Publisher, co umożliwia im publikowanie aktualizacji niestandardowych w usłudze Windows Server Update Service. Pozwala to Update Management na poprawianie maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami z Azure Security Center, aby przeprowadzać oceny luk w zabezpieczeniach na maszynach wirtualnych platformy Azure, na obrazach kontenerów i serwerach SQL. Eksportuj wyniki skanowania w regularnych odstępach czasu i Porównaj wyniki z poprzednimi skanami, aby sprawdzić, czy luki zostały skorygowane. W przypadku korzystania z zaleceń dotyczących zarządzania lukami zalecanymi przez Azure Security Center można przestawić w portalu wybranego rozwiązania, aby wyświetlić historyczne dane skanowania.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów (np. obliczeń, magazynu, sieci, portów i protokołów itp.) w Twoich subskrypcjach. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w Twoich subskrypcjach.

Mimo że klasyczne zasoby platformy Azure można odnaleźć za pośrednictwem Eksploratora Azure Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dodawanie metadanych do logicznie zorganizowanych w oparciu o taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [ Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)
 
- [ Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji Użyj Azure Automation Change Tracking i spisu, aby zautomatyzować zbieranie informacji o spisie z maszyn wirtualnych z systemami Windows i Linux. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać datę instalacji oprogramowania i inne informacje, Włącz diagnostykę na poziomie gościa i Przekieruj dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

- [Jak włączyć zbieranie spisu Azure Automation dla maszyny wirtualnej](../automation/automation-tutorial-installed-software.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych, których właścicielem jest organizacja, użyj Azure Security Center monitorowania integralności plików (FIM) w celu zidentyfikowania wszystkich programów zainstalowanych na maszynach wirtualnych. Kolejną opcją, która może być używana zamiast lub w połączeniu z programem FIM, jest Azure Automation Change Tracking i spisu do zbierania spisu z maszyn wirtualnych z systemem Linux i Windows. 

Możesz zaimplementować własny proces usuwania nieautoryzowanego oprogramowania. W celu zidentyfikowania niezatwierdzonego oprogramowania można również użyć rozwiązania innej firmy.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Jak używać monitorowania integralności plików](../security-center/security-center-file-integrity-monitoring.md)

- [Informacje o Azure Automation Change Tracking i spisie](../automation/change-tracking/overview.md)

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

- [Usuwanie zasobów i grup zasobów platformy Azure](../azure-resource-manager/management/delete-resource-group.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych, których właścicielem jest organizacja, użyj Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że wykonywane jest tylko autoryzowane oprogramowanie i że wszystkie nieautoryzowane oprogramowanie zostanie zablokowane na platformie Azure Virtual Machines.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku wszelkich zasobów obliczeniowych, które należą do organizacji, użyj Azure Security Center adaptacyjnych kontrolek aplikacji, aby określić typy plików, których może dotyczyć reguła lub które nie mogą być stosowane.

Zaimplementuj rozwiązanie innych firm, jeśli adaptacyjne kontrole aplikacji nie spełniają wymagań.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego Azure Active Directory (Azure AD) w celu ograniczenia możliwości korzystania przez użytkowników z usługi Azure zasobów Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji, w zależności od typu skryptów, można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w zasobach obliczeniowych platformy Azure. Możesz również użyć Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure Machine Learning przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. MachineLearning", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji usług Azure Machine Learning.

Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

Azure Machine Learning w pełni obsługuje repozytoria Git na potrzeby śledzenia pracy; repozytoria można klonować bezpośrednio do udostępnionego systemu plików obszaru roboczego, korzystać z narzędzia Git na lokalnej stacji roboczej i upewnić się, że bezpieczne konfiguracje dotyczą zasobów kodu w ramach środowiska Machine Learning.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za bezpieczne konfiguracje systemów operacyjnych Azure Machine Learning usługi.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji należy użyć zaleceń Azure Security Center, aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.  Ponadto możesz użyć niestandardowych obrazów systemu operacyjnego lub Azure Automation konfiguracji stanu, aby określić konfigurację zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

- [Przekazywanie wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/upload-vhd.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure. Ponadto za pomocą szablonów Azure Resource Manager można zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 
 
- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)
 
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
 
- [ Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za bezpieczne konfiguracje systemów operacyjnych Azure Machine Learning usługi.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami Azure Security Center na temat przeprowadzania ocen luk w zabezpieczeniach zasobów obliczeniowych platformy Azure.  Ponadto można używać szablonów Azure Resource Manager, niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu Azure Automation do obsługi konfiguracji zabezpieczeń systemu operacyjnego wymaganego przez organizację.   Szablony maszyn wirtualnych firmy Microsoft połączone z konfiguracją stanu Azure Automation mogą pomóc w spotkaniu i utrzymaniu wymagań w zakresie zabezpieczeń. 

Należy pamiętać, że obrazy maszyn wirtualnych portalu Azure Marketplace opublikowane przez firmę Microsoft są zarządzane i obsługiwane przez firmę Microsoft. 

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu ARM](../virtual-machines/windows/ps-template.md)

- [Przegląd konfiguracji stanu Azure Automation](../automation/automation-dsc-overview.md)

- [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

- [Informacje na temat pobierania szablonu maszyny wirtualnej](/previous-versions/azure/virtual-machines/windows/download-template)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla Machine Learning lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Azure Machine Learning w pełni obsługuje repozytoria Git na potrzeby śledzenia pracy; repozytoria można klonować bezpośrednio do udostępnionego systemu plików obszaru roboczego, korzystać z narzędzia Git na lokalnej stacji roboczej i upewnić się, że bezpieczne konfiguracje dotyczą zasobów kodu w ramach środowiska Machine Learning.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby umożliwić dostęp do obrazów niestandardowych tylko autoryzowanym użytkownikom. Użyj galerii obrazów udostępnionych platformy Azure, możesz udostępnić swoje obrazy innym użytkownikom, podmiotom usługi lub grupom Azure Active Directory (Azure AD) w organizacji. Przechowuj obrazy kontenerów w Azure Container Registry i korzystaj z usługi Azure RBAC, aby upewnić się, że tylko autoryzowani użytkownicy mają dostęp.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Informacje na temat usługi Azure RBAC dla Container Registry](../container-registry/container-registry-roles.md)

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Omówienie galerii obrazów udostępnionych](../virtual-machines/shared-image-galleries.md)

- [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](../aks/manage-azure-rbac.md)

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. MachineLearning", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za wdrożenie bezpiecznego konfigurowania usługi Azure Machine Learning.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych, które są własnością Twojej organizacji, należy użyć konfiguracji stanu Azure Automation w przypadku węzłów konfiguracji żądanego stanu (DSC) w dowolnym lokalnym centrum danych. Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem. 

- [Jak włączyć konfigurację stanu Azure Automation](../automation/automation-dsc-onboarding.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure. Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure.
 
 
 
- [ Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za automatyczne monitorowanie konfiguracji bezpiecznego Azure Machine Learning usługi.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych należących do organizacji Użyj Azure Security Center aplikacji obliczeniowych &amp; i postępuj zgodnie z zaleceniami dotyczącymi maszyn wirtualnych i serwerów oraz kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj zarządzanych tożsamości w połączeniu z Azure Key Vault, aby uprościć zarządzanie tajnymi aplikacjami w chmurze.

Azure Machine Learning obsługuje szyfrowanie magazynów danych z kluczami zarządzanymi przez klienta, należy zarządzać kluczowymi rotacjami i odwołaniami zgodnie z wymaganiami dotyczącymi zabezpieczeń i zgodności w organizacji. 

Użyj Azure Key Vault, aby bezpiecznie przekazywać klucze tajne do zdalnego uruchomienia zamiast zwykłego tekstu w skryptach szkoleniowych.

- [Azure Machine Learning klucze zarządzane przez klienta](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Użyj wpisów tajnych poświadczeń uwierzytelniania w Azure Machine Learningych przebiegach szkoleniowych](how-to-use-secrets-in-runs.md)

- [Jak używać tożsamości zarządzanych dla zasobów platformy Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)

- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Azure Machine Learning obsługuje zarówno role wbudowane, jak i możliwość tworzenia ról niestandardowych. Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning](how-to-assign-roles.md)

- [Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Machine Learning), ale nie jest uruchamiane w treści klienta.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku zasobów obliczeniowych, których właścicielem jest organizacja, użyj programu Microsoft chroniącego przed złośliwym kodem dla platformy Azure w celu ciągłego monitorowania i obrony zasobów. W przypadku systemu Linux należy użyć rozwiązania chroniącego przed złośliwym kodem innej firmy. Ponadto należy użyć wykrywania zagrożeń Azure Security Center dla usług danych w celu wykrywania złośliwego oprogramowania przekazanego do kont magazynu.

- [Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure](../security/fundamentals/antimalware.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Machine Learning), ale nie jest ono uruchamiane w treści klienta.

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone i utrzymywane dla podstawowego hosta, który obsługuje usługi platformy Azure (na przykład Azure Machine Learning), ale nie jest uruchamiane na zawartość klienta.

Azure Machine Learning ma różne wsparcie dla różnych zasobów obliczeniowych, a nawet własnych zasobów obliczeniowych. W przypadku wszystkich zasobów obliczeniowych należących do organizacji postępuj zgodnie z zaleceniami w Azure Security Center, aplikacje obliczeniowe, &amp; Aby upewnić się, że wszystkie punkty końcowe są aktualne przy użyciu najnowszych sygnatur. W przypadku systemu Linux należy użyć rozwiązania chroniącego przed złośliwym kodem innej firmy.

- [Jak wdrożyć oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure](../security/fundamentals/antimalware.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Zarządzanie odzyskiwaniem danych w usłudze Machine Learning odbywa się za poorednictwem zarządzania danymi w połączonych magazynach danych. Należy postępować zgodnie z instrukcjami dotyczącymi odzyskiwania danych dla magazynów połączonych, aby utworzyć kopię zapasową danych na zasadach organizacji klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Tworzenie kopii zapasowych danych w usłudze Machine Learning odbywa się za poorednictwem zarządzania danymi w połączonych magazynach danych. Włącz Azure Backup dla maszyn wirtualnych i skonfiguruj żądaną częstotliwość oraz okresy przechowywania. Wykonaj kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: sprawdzanie kopii zapasowych danych w usłudze Machine Learning odbywa się za poorednictwem zarządzania danymi w połączonych magazynach danych. Okresowe wykonywanie przywracania danych w Azure Backup. Upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](../backup/backup-azure-restore-files-from-vm.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: dla lokalnej kopii zapasowej szyfrowanie w spoczynku jest dostarczane przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można chronić kopie zapasowe i klucze zarządzane przez klienta. 

Włącz trwałe usuwanie i przeczyszczanie ochrony w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, należy włączyć funkcję usuwania nietrwałego w celu zapisywania i odzyskiwania danych po usunięciu obiektów blob lub migawek obiektów BLOB.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

- [Jak włączyć ochronę w formie usuwania nietrwałego i przeczyszczania w usłudze Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Usuwanie nietrwałe dla magazynu obiektów blob platformy Azure](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy, a następnie Popraw plan odpowiedzi zgodnie z potrzebami.

- [Publikacja NIST — Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy Azure Security Center do automatycznego wyzwalania odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
