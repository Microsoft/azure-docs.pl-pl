---
title: Punkt odniesienia zabezpieczeń platformy Azure dla Azure IoT Hub
description: Punkt Azure IoT Hub zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99243dbfac7fddb8a4fe9d64ed64ab706245ec3c
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587633"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Punkt odniesienia zabezpieczeń platformy Azure dla Azure IoT Hub

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy Azure w wersji [1.0](../security/benchmarks/overview-v1.md) do Microsoft Azure IoT Hub. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń** zdefiniowanych przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących Azure IoT Hub. **Kontrolki,** które nie Azure IoT Hub zostały wykluczone.

 
Aby zobaczyć, Azure IoT Hub mapuje całkowicie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny [Azure IoT Hub mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** IoT Hub jest wielodostępną platformą jako usługą (PaaS), różni klienci współdzielą tę samą pulę zasobów sprzętowych obliczeniowych, sieciowych i magazynowych. IoT Hub hostów są mapowane na publiczny punkt końcowy z publicznie routowalnym adresem IP za pośrednictwem Internetu. Różni klienci współdzielą ten IoT Hub punkt końcowy, a urządzenia IoT w sieciach szerokich i lokalnych mogą uzyskać do niego dostęp. Firma Microsoft zaprojektowała usługę pod kątem pełnej izolacji danych poszczególnych dzierżaw i stale pracuje nad zapewnieniem takiego wyniku.

IoT Hub takie jak routing komunikatów, przekazywanie plików i zbiorcze importowanie/eksportowanie urządzeń również wymagają łączności z usługi IoT Hub do zasobu platformy Azure należącego do klienta za pośrednictwem jego publicznego punktu końcowego. Te ścieżki łączności tworzą zbiorczo ruch wychodzący z IoT Hub do zasobów klienta.

Zaleca się ograniczenie łączności z zasobami platformy Azure (w tym z usługą Azure IoT Hub) za pośrednictwem posiadanej i opernej sieci wirtualnej, aby zmniejszyć narażenie na łączność w izolowanej sieci i włączyć lokalną łączność sieciową bezpośrednio z siecią szkieletową platformy Azure. Użyj Azure Private Link i prywatnego punktu końcowego platformy Azure, jeśli jest to możliwe, aby umożliwić prywatny dostęp do usług z innych sieci wirtualnych. 

Po nawiązycej dostęp prywatny należy wyłączyć dostęp do sieci publicznej dla IoT Hub w celu uzyskania dodatkowych zabezpieczeń. Ta kontrola poziomu sieci jest wymuszana dla określonego zasobu centrum IoT, zapewniając izolację. Aby zachować aktywna usługę dla innych zasobów klienta przy użyciu ścieżki publicznej, jej publiczny punkt końcowy pozostaje rozpoznawalny, adresy IP można odnajdywać i porty pozostają otwarte. Nie jest to powód do obaw, ponieważ firma Microsoft integruje wiele warstw zabezpieczeń w celu zapewnienia pełnej izolacji między dzierżawami.

Aby uniknąć niechcianego dostępu, otwórz porty sprzętowe na urządzeniach na minimalnym poziomie. Ponadto należy tworzyć mechanizmy zapobiegające fizycznemu manipulowaniu urządzeniem lub wykrywane przez nie.

- [Obsługa sieci wirtualnych IoT](virtual-network-support.md)

- [Zarządzanie dostępem do sieci publicznej dla centrum IoT](iot-hub-public-network-access.md)

- [Izolacja dzierżawy na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [loT networking best practice (Najlepsze rozwiązanie dotyczące sieci loT)](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Azure Private Link omówienie](../private-link/private-link-overview.md)

- [Sieciowa grupa zabezpieczeń platformy Azure](../virtual-network/network-security-groups-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i karty sieciowe

**Wskazówki:** użyj Azure Security Center i postępuj zgodnie z zaleceniami ochrony sieci, aby ułatwić zabezpieczanie zasobów sieciowych platformy Azure. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i wyślij dzienniki do konta usługi Azure Storage w celu inspekcji. Możesz również wysłać dzienniki przepływu do obszaru roboczego usługi Log Analytics, a następnie użyć Analiza ruchu, aby zapewnić wgląd w wzorce ruchu w chmurze platformy Azure. Niektóre zalety usługi Analiza ruchu to możliwość wizualizacji aktywności sieciowej, identyfikowania punktów aktywności i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i identyfikowania błędnych konfiguracji sieci.
 
- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)
 
- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3. Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** Blokuj znane złośliwe adresy IP przy IoT Hub reguł filtrowania adresów IP. Złośliwe próby są również rejestrowane i powiadamiane za pośrednictwem Azure Security Center dla IoT.

Azure DDoS Protection Podstawowa jest już włączona i jest dostępna bez dodatkowych kosztów w ramach IoT Hub. Zawsze wł. monitorowanie ruchu i ograniczanie ryzyka typowych ataków na poziomie sieci w czasie rzeczywistym zapewniają taką samą ochronę, która jest wykorzystywana przez rozwiązania firmy Microsoft Usługi online. Cała skala globalnej sieci platformy Azure może służyć do dystrybucji i ograniczania ataku we wszystkich regionach.

- [IoT Hub ip](iot-hub-ip-filtering.md)

- [Azure Security Center komunikacji z podejrzanymi adresami IP IoT](/azure/asc-for-iot/concept-security-alerts)

- [Zarządzanie Azure DDoS Protection podstawowym](../ddos-protection/ddos-protection-overview.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla ofert, które produkują pakiety sieciowe, które mogą być rejestrowane i przeglądane przez klientów. IoT Hub nie powoduje tworzenia pakietów sieciowych przeznaczonych dla klientów i nie jest przeznaczony do wdrażania bezpośrednio w sieciach wirtualnych platformy Azure.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie opartych na sieci systemów wykrywania włamań/zapobiegania włamań (IDS/IPS)

**Wskazówki:** wybierz ofertę z Azure Marketplace która obsługuje funkcje ids/IPS z możliwościami inspekcji ładunku.  Jeśli inspekcja ładunku nie jest wymagana, można Azure Firewall analizy zagrożeń. Azure Firewall filtrowania opartego na analizie zagrożeń służy do alertów i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdrożenie wybranego rozwiązania zapory na poszczególnych granicach sieci organizacji w celu wykrywania i/lub blokowania złośliwego ruchu. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą Azure Firewall](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** W przypadku zasobów, które wymagają dostępu do Azure IoT Hub, użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład AzureIoTHub) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Jak używać tagów usługi dla usługi Azure IoT](iot-hub-understand-ip-address.md)
- [Aby uzyskać więcej informacji na temat korzystania z tagów usługi](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami Azure IoT Hub z Azure Policy. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.Devices" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci w przestrzeniach nazw Machine Learning nazw. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem Azure IoT Hub, aby logicznie zorganizować je w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure, aby monitorować konfiguracje zasobów sieciowych i wykrywać zmiany zasobów sieciowych związanych z Azure IoT Hub. Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki za pośrednictwem Azure Monitor, aby agregować dane zabezpieczeń wygenerowane przez Azure IoT Hub. W Azure Monitor obszarów roboczych usługi Log Analytics można wykonywać zapytania i przeprowadzać analizy, a także używać kont magazynu do przechowywania długoterminowego/archiwalnej. Alternatywnie możesz włączyć i dołączyć dane do rozwiązania Azure Sentinel lub rozwiązania SIEM (Security Incident and Event Management) innej firmy.

- [Konfigurowanie dzienników usługi Azure IoT](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** włącz ustawienia diagnostyczne usługi Azure IoT dla zasobów platformy Azure, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i zasobów. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, znacznik czasu, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Konfigurowanie dzienników Azure IoT Hub danych](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Devices:**

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4. Zbieranie dzienników zabezpieczeń z systemów operacyjnych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z wystąpieniami usługi Azure IoT Hub zgodnie z przepisami dotyczącymi zgodności w organizacji.

- [Jak ustawić parametry przechowywania dzienników](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** analizowanie i monitorowanie dzienników pod celu zachowania anomalii oraz regularne przeglądanie wyników Azure IoT Hub. Użyj Azure Monitor i obszaru roboczego usługi Log Analytics, aby przeglądać dzienniki i wykonywać zapytania dotyczące danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do Azure Sentinel lub rozwiązania SIEM innej firmy. 

- [Monitorowanie kondycji usługi Azure IoT](monitor-iot-hub.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Wprowadzenie do zapytań usługi Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Jak wykonywać zapytania niestandardowe w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włącz alerty dla działań anomalii

**Wskazówki:** używanie Azure Security Center dla IoT z obszarem roboczym usługi Log Analytics w celu monitorowania i alertów dotyczących anomalii w dziennikach zabezpieczeń i zdarzeniach. Alternatywnie możesz włączyć i włączyć dane, aby Azure Sentinel. Alerty operacyjne można również definiować za pomocą Azure Monitor, które mogą mieć wpływ na bezpieczeństwo, na przykład w przypadku nieoczekiwanego spadku ruchu.

- [Monitorowanie Azure IoT Hub danych](monitor-iot-hub.md)

- [Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Azure Security Center alertów IoT](/azure/asc-for-iot/concept-security-alerts)

- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8. Scentralizowanie rejestrowania w poszukiwaniu złośliwego oprogramowania

**Wskazówki:** Nie dotyczy; Azure IoT Hub nie przetwarza ani nie tworzyć dzienników związanych ze złośliwym oprogramowaniem.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Nie dotyczy; Azure IoT Hub nie przetwarza ani nie tworzyć dzienników związanych z systemem DNS.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="210-enable-command-line-audit-logging"></a>2.10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia zarządzanie dostępem do centrum Azure IoT hub za pomocą przypisań ról. Te role można przypisywać do użytkowników, grupować jednostki usługi i tożsamości zarządzane. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role, które można dodać do spisu lub zapytania za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Zarządzanie dostępem do Azure IoT Hub jest kontrolowane za pośrednictwem Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji haseł domyślnych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych.

Dostęp just in time do kont administracyjnych można również włączyć przy użyciu Azure Active Directory (Azure AD) Privileged Identity Management i Azure Resource Manager.

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Korzystanie z logowania jednokrotnego (SSO) z Azure Active Directory

**Wskazówki:** W przypadku użytkowników, którzy IoT Hub dostęp do aplikacji, użyj logowania jednokrotnego Azure Active Directory (Azure AD). Używaj Azure Security Center dotyczących tożsamości i dostępu.

- [Understand SSO with Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na systemie

**Wskazówki:** Włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD), aby chronić ogólną dzierżawę platformy Azure i korzystać ze wszystkich usług. IoT Hub usługa nie obsługuje uwierzytelniania wieloskładnikowego.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w ramach Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Użyj dedykowanych maszyn (stacji roboczych z dostępem uprzywilejowanym) do wykonywania wszystkich zadań administracyjnych

**Wskazówki:** Używanie stacji roboczej z bezpiecznym dostępem uprzywilejowanym (PAW) do wykonywania zadań administracyjnych wymagających podniesionych uprawnień.

- [Opis bezpiecznych stacji roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7. Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** użyj Azure Active Directory zabezpieczeń i monitorowania usługi Azure AD, aby wykryć, kiedy w środowisku wystąpi podejrzane lub niebezpieczne działanie. Użyj Azure Security Center, aby monitorować działania tożsamości i dostępu.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** w przypadku użytkowników IoT Hub dostęp warunkowy nie jest obsługiwany. Aby temu uniknąć, użyj nazwanych lokalizacji usługi Azure Active Directory (Azure AD), aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów dla całej dzierżawy platformy Azure, co zapewnia korzyści wszystkim usługom, w tym IoT Hub.

- [Jak skonfigurować nazwane lokalizacje w usłudze Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Aby uzyskać dostęp użytkowników do IoT Hub, użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowany i podczas przesyłania. Usługa Azure AD dodaje również instyty, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

W przypadku dostępu do urządzeń i usług IoT Hub tokeny zabezpieczające i tokeny sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług w celu uniknięcia wysyłania kluczy w sieci. 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Azure Active Directory (Azure AD) zawiera dzienniki, które ułatwiają odnajdywanie nieaktualnych kont. Ponadto za pomocą przeglądów dostępu i tożsamości usługi Azure AD można wydajnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkowników można regularnie przeglądać, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp.

Użyj Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanej lub niebezpiecznej aktywności w środowisku.

- [Opis raportowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wdrażanie Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Masz dostęp do źródeł dzienników Azure Active Directory (Azure AD), inspekcji i dzienników zdarzeń ryzyka, które umożliwiają integrację z dowolnym narzędziem SIEM/monitorowaniem.

Ten proces można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

Użytkownik Azure Monitor zasobów do monitorowania nieautoryzowanych prób połączenia w kategorii Połączenia.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Konfigurowanie dzienników zasobów dla usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alert w przypadku odchylenia zachowania logowania na koncie

**Wskazówki:** używanie Azure Active Directory usługi Identity Protection (Azure AD) do konfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników. Możesz również pozysać dane do Azure Sentinel w celu dalszego badania.

- [ Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [ Jak skonfigurować i włączyć zasady ryzyka usługi Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ Jak do dołączać Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnianie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, będzie ona żądana bezpośrednio od klienta.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.
 
- [ Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie izolacji przy użyciu oddzielnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska przedsiębiorstwa. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem kontroli dostępu na RBAC platformy Azure.
  
- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [ Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [ Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Użyj rozwiązania innej firmy od firmy Azure Marketplace w obwodach sieciowych do monitorowania nieautoryzowanego transferu poufnych informacji i blokowania takich transferów podczas powiadamiania specjalistów ds. zabezpieczeń informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i chroni przed utratą i utratą danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** IoT Hub używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1.0, 1.1 i 1.2. Zdecydowanie zaleca się używanie wersji TLS 1.2 jako preferowanej wersji TLS podczas nawiązywania połączenia z IoT Hub.

Postępuj Azure Security Center dotyczących szyfrowania danych w spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Obsługa TLS w u IoT Hub](iot-hub-tls-support.md)
- [Opis szyfrowania podczas przesyłania za pomocą platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure IoT Hub. W razie potrzeby zaim implementuj rozwiązanie innej firmy na potrzeby zgodności.

W przypadku podstawowej platformy Azure zarządzanej przez firmę Microsoft firma Microsoft traktuje całą zawartość klientów jako poufne i przechodzi przez długi czas, aby chronić się przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6. Zarządzanie dostępem do zasobów przy użyciu kontroli RBAC platformy Azure

**Wskazówki:** Aby uzyskać dostęp użytkownika płaszczyzny sterowania do IoT Hub, użyj kontroli dostępu na IoT Hub Azure RBAC. Aby uzyskać dostęp do płaszczyzny danych IoT Hub, użyj zasad dostępu współdzielonych dla IoT Hub.

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

- [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące sytuacji, w których zmiany mają miejsce w wystąpieniach produkcyjnych Azure IoT Hub i innych krytycznych lub powiązanych zasobów.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3. Wdrażanie zautomatyzowanego rozwiązania do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu wstecz

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure (nie wszystkie zasoby obsługują tagi, ale większość z nich) w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** do organizowania i śledzenia zasobów używaj tagowania, grup zarządzania i oddzielnych subskrypcji tam, gdzie jest to konieczne. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób.
  
- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak tworzyć grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z potrzebami organizacji.

Każdy IoT Hub ma rejestr tożsamości, który może służyć do tworzenia zasobów dla poszczególnych urządzeń w usłudze. Tożsamości użytkowników lub grupy urządzeń można dodać do listy zezwalania lub listy zablokowanych, co zapewnia pełną kontrolę nad dostępem do urządzeń.

- [IoT Hub rejestru tożsamości](iot-hub-devguide-identity-registry.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach. 

Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto użyj tego Azure Resource Graph do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego usługi Azure Active Directory (Azure AD), aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [ Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Ogranicz użytkownikom możliwość wykonywania skryptów w zasobach obliczeniowych

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Nie dotyczy; To zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure IoT Hub przy użyciu Azure Policy. Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Devices", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji Azure IoT Hub usług.

Azure Resource Manager ma możliwość eksportowania szablonu w pliku JavaScript Object Notation (JSON), który należy przejrzeć, aby upewnić się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń w organizacji.

Możesz również użyć zaleceń z witryny Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksportowanie pojedynczego i wielu zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="72-establish-secure-operating-system-configurations"></a>7.2. Ustanawianie bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach platformy Azure. Ponadto można użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganą przez organizację.  
 
- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
- [omówienie Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemu operacyjnego

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** Jeśli używasz niestandardowych Azure Policy dla zasobów Azure IoT Hub lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Azure Repos dokumentacji](/azure/devops/repos)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7. Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** użyj Azure Policy w przestrzeni nazw "Microsoft.Devices", aby tworzyć niestandardowe zasady dotyczące alertów, inspekcji i wymuszania konfiguracji systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8. Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** użyj Azure Security Center, aby przeprowadzić skanowanie bazowe zasobów platformy Azure. Ponadto użyj Azure Policy do alertów i inspekcji konfiguracji zasobów platformy Azure. 
 
- [ Jak korygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementowanie zautomatyzowanego monitorowania konfiguracji dla systemów operacyjnych

**Wskazówki:** Nie dotyczy; Ta wy przewodnik jest przeznaczona dla zasobów obliczeniowych.

**Odpowiedzialność:** Nie dotyczy

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** IoT Hub tokenów zabezpieczających i tokenów sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług w celu uniknięcia wysyłania kluczy w sieci. 

Używaj tożsamości zarządzanych w połączeniu z Azure Key Vault, aby uprościć zarządzanie kluczami tajnymi dla aplikacji w chmurze.

- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Jak używać tożsamości zarządzanych dla IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Jak utworzyć magazyn kluczy](../key-vault/general/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** IoT Hub tokenów zabezpieczających i tokenów sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług w celu uniknięcia wysyłania kluczy w sieci.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Jak skonfigurować tożsamości zarządzane dla IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 
 
- [  Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** ochrona przed złośliwym oprogramowaniem firmy Microsoft jest włączona na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure IoT Hub), ale nie działa w przypadku zawartości klienta.

To Ty odpowiadasz za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzać skanowania zawartości klienta w poszukiwaniu złośliwego oprogramowania w Jego imieniu.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Azure IoT Hub udostępnia metody i struktury, dzięki IoT Hub usługom o wysokiej IoT Hub i możliwości ich odzyskiwania po awarii w oparciu o konkretne cele biznesowe. 

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](iot-hub-ha-dr.md)

- [Jak sklonować IoT Hub](iot-hub-how-to-clone.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Azure IoT Hub, że pomocnicze centrum IoT musi zawierać wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie powinno przechowywać kopie zapasowe tożsamości urządzeń z replikacją geograficzną i przekazywać je do pomocniczego centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. W tym kontekście przydatna jest funkcja eksportowania tożsamości IoT Hub urządzenia.

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub eksportowania tożsamości urządzenia](iot-hub-bulk-identity-mgmt.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** Azure IoT Hub, że pomocnicze centrum IoT musi zawierać wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie powinno przechowywać kopie zapasowe tożsamości urządzeń z replikacją geograficzną i przekazywać je do pomocniczego centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. W tym kontekście przydatna jest funkcja eksportowania tożsamości IoT Hub urządzenia.

Okresowe przywracanie danych zawartości w kopii zapasowej. Upewnij się, że możesz przywrócić kopię zapasową kluczy zarządzanych przez klienta.

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [IoT Hub eksportowania tożsamości urządzenia](iot-hub-bulk-identity-mgmt.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** włącz ochronę przed usuwaniem nieumyślnego i przeczyszczaniem w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, włącz usuwanie nie softowe, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów blob.

 
- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

- [Usuwanie nie softne dla usługi Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówki:** Opracowywanie przewodnika reagowania na zdarzenia dla organizacji. Upewnij się, że istnieją zapisane plany reagowania na zdarzenia, które definiują wszystkie role personelu, a także fazy obsługi zdarzeń i zarządzania od wykrywania do przeglądu po zdarzeniu. 
  
- [ Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Użyj przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Azure Security Center przypisuje ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znalezieniu lub jest analityczna używana do wystawienia alertu, a także od poziomu pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

  
 Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa do identyfikowania i kategoryzowania zasobów platformy Azure, zwłaszcza tych przetwarzających poufne dane. To Ty odpowiadasz za ustalanie priorytetów korygowania alertów na podstawie krytyczności zasobów i środowiska platformy Azure, w których wystąpiło zdarzenie.
  
- [ Alerty zabezpieczeń w Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia w celu testowania możliwości reagowania na zdarzenia w systemach w regularnych odstępach czasu, aby ułatwić ochronę zasobów platformy Azure. Zidentyfikuj słabe punkty i luki, a następnie popraw plan reagowania zgodnie z potrzebami.
  
- [ Publikacja NIST — przewodnik dotyczący programów testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
  
- [ Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Azure Security Center i zaleceń przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń dla zasobów platformy Azure. Eksport ciągły umożliwia eksportowanie alertów i rekomendacji ręcznie lub w ciągły sposób. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.
  
- [ Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)
 
- [ Jak przesyłać strumieniowo alerty do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu Azure Security Center, aby automatycznie wyzwalać odpowiedzi na alerty zabezpieczeń i zalecenia w celu ochrony zasobów platformy Azure.
  
- [ Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu czerwonego.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1. Przeprowadzanie regularnych testów penetracyjnych zasobów platformy Azure i zapewnianie korygowania wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z regułami testowania penetracyjne firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
