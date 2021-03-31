---
title: Podstawa zabezpieczeń Azure dla usługi Azure IoT Hub
description: Podstawowa usługa Azure IoT Hub Security zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a21e0ae235d5b5c514f3d82b76b4d17394035872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576899"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Podstawa zabezpieczeń Azure dla usługi Azure IoT Hub

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) w celu Microsoft Azure IoT Hub. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez usługę Azure Security test i powiązane wskazówki dotyczące usługi Azure IoT Hub. Nie zostały wykluczone **kontrolki** , które mają zastosowanie do IoT Hub platformy Azure.

 
Aby dowiedzieć się, jak platforma Azure IoT Hub całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z [pełnym plikiem mapowania podstawy zabezpieczeń usługi azure IoT Hub](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: IoT Hub to wielodostępna platforma jako usługa (PaaS), różni klienci korzystają z tej samej puli zasobów obliczeniowych, sieciowych i magazynowych. Nazwy hostów IoT Hub są mapowane na publiczny punkt końcowy z publicznie rutowanym adresem IP przez Internet. Różni klienci korzystają z tego IoT Hub publicznego punktu końcowego, a urządzenia IoT w sieci rozległej i sieci lokalne mogą uzyskać do niego dostęp. Firma Microsoft zaprojektował usługę pod kątem pełnej izolacji między danymi poszczególnych dzierżawców i działa w sposób ciągły, aby zapewnić ten wynik.

IoT Hub funkcje, w tym Routing komunikatów, przekazywanie plików i zbiorcze importowanie/eksportowanie urządzeń, wymagają także łączności od IoT Hub do zasobu platformy Azure należącego do klienta za pośrednictwem publicznego punktu końcowego. Te ścieżki łączności zbiorowo tworzą ruch wychodzący z IoT Hub do zasobów klientów.

Zaleca się ograniczenie łączności z zasobami platformy Azure (w tym Azure IoT Hub) za pośrednictwem sieci wirtualnej, która jest posiadana, i działa w celu zmniejszenia narażenia na łączność w sieci izolowanej i bezpośredniego połączenia z siecią w sieci szkieletowej platformy Azure. Użyj prywatnego linku platformy Azure i prywatnego punktu końcowego platformy Azure, gdzie to możliwe, aby włączyć prywatny dostęp do usług z innych sieci wirtualnych. 

Po ustanowieniu dostępu prywatnego należy wyłączyć dostęp do sieci publicznej dla IoT Hub, aby uzyskać dodatkowe zabezpieczenia. Ta kontrolka poziomu sieci jest wymuszana w określonym zasobie Centrum IoT, zapewniając izolację. Aby zachować aktywną usługę dla innych zasobów klienta przy użyciu ścieżki publicznej, jego publiczny punkt końcowy pozostaje rozpoznawalny, adresy IP są wykrywalne i porty pozostają otwarte. Nie jest to powód dla obaw, ponieważ firma Microsoft integruje wiele warstw zabezpieczeń w celu zapewnienia pełnej izolacji między dzierżawcami.

Pozostaw otwarte porty sprzętowe na urządzeniach, aby uniknąć niepożądanego dostępu. Ponadto mechanizmy kompilacji, aby zapobiec lub wykryć fizyczne manipulowanie urządzeniem.

- [Obsługa sieci wirtualnych IoT](virtual-network-support.md)

- [Zarządzanie dostępem do sieci publicznej dla usługi IoT Hub](iot-hub-public-network-access.md)

- [Izolacja dzierżawy na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [najlepsze rozwiązanie w zakresie sieci](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Omówienie linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Grupa zabezpieczeń sieci platformy Azure](../virtual-network/network-security-groups-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Użyj Azure Security Center i postępuj zgodnie z zaleceniami dotyczącymi ochrony sieci, aby pomóc w zabezpieczeniu zasobów sieciowych platformy Azure. Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji. Możesz również wysłać dzienniki przepływów do obszaru roboczego Log Analytics, a następnie użyć Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej, identyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.
 
- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)
 
- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Blokuj znane złośliwe adresy IP przy użyciu reguł filtru IP IoT Hub. Złośliwe próby są również rejestrowane i powiadamiane za pośrednictwem Azure Security Center dla IoT.

Azure DDoS Protection podstawowa jest już włączona i dostępna bez dodatkowych kosztów w ramach IoT Hub. Zawsze włączone monitorowanie ruchu oraz łagodzenie typowych ataków na poziomie sieci w czasie rzeczywistym zapewnia te same zabezpieczenia wykorzystywane przez Usługi online firmy Microsoft. Cała Skala sieci globalnej platformy Azure może służyć do dystrybucji i ograniczania ruchu ataków między regionami.

- [IoT Hub filtr IP](iot-hub-ip-filtering.md)

- [Azure Security Center do komunikacji z podejrzanym adresem IP IoT](/azure/asc-for-iot/concept-security-alerts)

- [Zarządzanie Azure DDoS Protection podstawowa](../ddos-protection/ddos-protection-overview.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla ofert, które tworzą pakiety sieciowe, które mogą być rejestrowane i wyświetlane przez klientów. IoT Hub nie produkuje pakietów sieciowych, które są dostępne dla klientów i nie są przeznaczone do wdrażania bezpośrednio w sieciach wirtualnych platformy Azure.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: wybierz ofertę z witryny Azure Marketplace, która obsługuje funkcje identyfikatorów/adresów IP z możliwościami inspekcji ładunku.  Gdy inspekcja ładunku nie jest wymagana, można użyć analizy zagrożeń zapory platformy Azure. Filtrowanie oparte na analizie zagrożeń dla zapory platformy Azure służy do wyzwalania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

W celu wykrycia i/lub zablokowania złośliwego ruchu należy wdrożyć wybrane rozwiązanie zapory na wszystkich granicach sieci w organizacji. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak skonfigurować alerty za pomocą zapory platformy Azure](../firewall/threat-intel.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do IoT Hub platformy Azure, użyj tagów usługi Virtual Network, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład AzureIoTHub) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Jak używać tagów usługi dla usługi Azure IoT](iot-hub-understand-ip-address.md)
- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z przestrzeniami nazw platformy Azure IoT Hub przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. Devices" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieciowej Machine Learning przestrzeni nazw. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów sieciowych skojarzonych z wdrożeniem IoT Hub platformy Azure, aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z usługą Azure IoT Hub. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników za pośrednictwem Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez usługę Azure IoT Hub. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub zdarzenia związane z zabezpieczeniami innych firm (SIEM).

- [Konfigurowanie dzienników usługi Azure IoT](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych usługi Azure IoT w zasobach platformy Azure w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i zasobów. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Konfigurowanie dzienników usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. Devices**:

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z wystąpieniami IoT Hub platformy Azure zgodnie z regulacjami zgodności w organizacji.

- [Jak ustawić parametry przechowywania dziennika](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularne przeglądanie wyników IoT Hub platformy Azure. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy. 

- [Monitorowanie kondycji usługi Azure IoT](monitor-iot-hub.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)
  
- [Wprowadzenie do Log Analytics zapytań](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center dla IoT z obszarem roboczym log Analytics do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej. Istnieje również możliwość zdefiniowania alertów operacyjnych z Azure Monitor, które mogą mieć wpływ na bezpieczeństwo, na przykład w przypadku nieoczekiwanego spadku ruchu.

- [Monitorowanie kondycji usługi Azure IoT Hub](monitor-iot-hub.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Azure Security Center alertów IoT](/azure/asc-for-iot/concept-security-alerts)

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Usługa Azure IoT Hub nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Usługa Azure IoT Hub nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do usługi Azure IoT Hub za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i tożsamości zarządzanych. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure lub Azure PowerShell lub Azure Portal.

- [Jak uzyskać rolę katalogu w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: zarządzanie dostępem do zasobów platformy Azure IoT Hub jest kontrolowane za pomocą Azure Active Directory (Azure AD). Usługa Azure AD nie ma koncepcji domyślnych haseł.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych.

Możesz również włączyć dostęp just in Time do kont administracyjnych przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management i Azure Resource Manager.

- [Dowiedz się więcej o Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: w przypadku użytkowników uzyskujących dostęp do IoT Hub Użyj usługi logowania jednokrotnego w usłudze Azure Active Directory (Azure AD). Użyj Azure Security Center zaleceń dotyczących tożsamości i dostępu.

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego usługi Azure Active Directory (Azure AD) w celu ochrony całej dzierżawy platformy Azure, korzyści płynącej ze wszystkich usług. Usługa IoT Hub nie ma obsługi uwierzytelniania wieloskładnikowego.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj bezpiecznej stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) na potrzeby zadań administracyjnych wymagających podwyższonego poziomu uprawnień.

- [Informacje o bezpiecznych, uprzywilejowanych stacjach roboczych dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

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

**Wskazówki**: dla użytkowników mających dostęp do IoT Hub, dostęp warunkowy nie jest obsługiwany. Aby wyeliminować ten problem, należy użyć nazw lokalizacji Azure Active Directory (Azure AD), aby zezwolić na dostęp tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów dla całej dzierżawy platformy Azure, korzystając wszystkie usługi, w tym IoT Hub.

- [Jak skonfigurować nazwane lokalizacje usługi Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Aby uzyskać dostęp użytkowników do IoT Hub, użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

W przypadku dostępu do urządzeń i usług IoT Hub używa tokenów zabezpieczających i tokenów sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (Azure AD) zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

Użyj Azure AD Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Wdróż Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem w usłudze Azure Active Directory (Azure AD), inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

Użytkownik Azure Monitor dzienników zasobów do monitorowania nieautoryzowanych prób połączenia w kategorii połączenia.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Konfigurowanie dzienników zasobów dla usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony tożsamości Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [ Jak wyświetlić ryzykowne logowania usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [ Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [ Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: w scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klientów, będzie ona żądała bezpośrednio od klienta.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.
 
- [ Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska korporacyjne. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem usługi Azure RBAC.
  
- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [ Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [ Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Użyj rozwiązania innej firmy z witryny Azure Marketplace w obszarze sieci, aby monitorować nieautoryzowany transfer informacji poufnych i blokować takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroni przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: IoT Hub używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1,0, 1,1 i 1,2. Zdecydowanie zaleca się użycie protokołu TLS 1,2 jako preferowanej wersji protokołu TLS podczas nawiązywania połączenia z usługą IoT Hub.

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Obsługa protokołu TLS w IoT Hub](iot-hub-tls-support.md)
- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla IoT Hub platformy Azure. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy Azure zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby zapewnić ochronę przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: w celu uzyskania dostępu do IoT Hub przez użytkownika płaszczyzny kontroli Użyj usługi Azure RBAC, aby kontrolować dostęp. Aby uzyskać dostęp do IoT Hub płaszczyzny danych, użyj zasad dostępu współdzielonego dla IoT Hub.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych platformy Azure IoT Hub i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure (nie wszystkie zasoby obsługują Tagi, ale większość z nich), aby logicznie zorganizować je w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.
  
- [ Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

Każdy IoT Hub zawiera rejestr tożsamości, którego można użyć do tworzenia zasobów dla urządzeń w usłudze. Osoby lub grupy tożsamości urządzeń można dodawać do dozwolonych lub listy blokowania, umożliwiając pełną kontrolę nad dostępem urządzeń.

- [Rejestr tożsamości IoT Hub](iot-hub-devguide-identity-registry.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji. 

Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

* Niedozwolone typy zasobów
* Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: korzystanie z dostępu warunkowego Azure Active Directory (Azure AD) w celu ograniczenia możliwości korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [ Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure IoT Hub przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Devices", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji usług Azure IoT Hub.

Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony w celu upewnienia się, że konfiguracje spełniają wymagania dotyczące zabezpieczeń Twojej organizacji.

Możesz również użyć zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure. Ponadto za pomocą szablonów Azure Resource Manager można zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację.  
 
- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)
- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)
- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla IoT Hub platformy Azure lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Devices", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)
- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure. Ponadto należy używać Azure Policy do powiadamiania i inspekcji konfiguracji zasobów platformy Azure. 
 
- [ Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Odpowiedzialność**: nie dotyczy

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: IoT Hub używa tokenów zabezpieczających i tokenów sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. 

Korzystaj z tożsamości zarządzanych w połączeniu z Azure Key Vault, aby uprościć zarządzanie tajnymi aplikacjami w chmurze.

- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Jak używać tożsamości zarządzanych do IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Jak utworzyć magazyn kluczy](../key-vault/general/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: IoT Hub używa tokenów zabezpieczających i tokenów sygnatury dostępu współdzielonego (SAS) do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [IoT Hub tokeny zabezpieczające](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Jak skonfigurować tożsamości zarządzane dla IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 
 
- [  Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure IoT Hub), ale nie jest ono uruchamiane w treści klienta.

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: usługa Azure IoT Hub Service oferuje metody i struktury, dzięki którym usługi IoT Hub mają wysoką dostępność i możliwość odzyskania danych z awarii na podstawie określonych celów firmy. 

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](iot-hub-ha-dr.md)

- [Jak klonować IoT Hub](iot-hub-how-to-clone.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: usługa Azure IoT Hub zaleca, aby pomocnicze Centrum IoT Hub zawierało wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie powinno zachować kopie zapasowe replikowanych tożsamości urządzeń i przekazać je do pomocniczego Centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. Funkcja eksportowania tożsamości urządzeń IoT Hub jest przydatna w tym kontekście.

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Eksport tożsamości urządzenia IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: usługa Azure IoT Hub zaleca, aby pomocnicze Centrum IoT Hub zawierało wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie powinno zachować kopie zapasowe replikowanych tożsamości urządzeń i przekazać je do pomocniczego Centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. Funkcja eksportowania tożsamości urządzeń IoT Hub jest przydatna w tym kontekście.

Okresowe wykonywanie przywracania danych w kopii zapasowej. Upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Eksport tożsamości urządzenia IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Włącz trwałe usuwanie i przeczyszczanie ochrony w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem. Jeśli usługa Azure Storage jest używana do przechowywania kopii zapasowych, należy włączyć funkcję usuwania nietrwałego w celu zapisywania i odzyskiwania danych po usunięciu obiektów blob lub migawek obiektów BLOB.

 
- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

- [Usuwanie nietrwałe dla magazynu obiektów blob platformy Azure](../storage/blobs/soft-delete-blob-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją jednokierunkowe plany reagowania na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń i zarządzania z wykrywania na potrzeby przeglądu po zdarzeniu. 
  
- [ Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Skorzystaj z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

  
 Ponadto należy oznaczyć subskrypcje przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają poufne dane. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.
  
- [ Alerty zabezpieczeń w Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy, a następnie Popraw plan odpowiedzi zgodnie z potrzebami.
  
- [ Publikacja NIST — Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.
  
- [ Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.
  
- [ Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)
 
- [ Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy Azure Security Center do automatycznego wyzwalania odpowiedzi na alerty zabezpieczeń i zalecenia dotyczące ochrony zasobów platformy Azure.
  
- [ Jak skonfigurować automatyzację przepływu pracy w Security Center](../security-center/workflow-automation.md)

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
