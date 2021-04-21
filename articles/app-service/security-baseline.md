---
title: Punkt odniesienia zabezpieczeń platformy Azure dla App Service
description: Punkt App Service zabezpieczeń zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: d3e9d7d8cd8e5ffad74b02179994e299a59c8894
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833944"
---
# <a name="azure-security-baseline-for-app-service"></a>Punkt odniesienia zabezpieczeń platformy Azure dla App Service

Punkt odniesienia zabezpieczeń platformy Azure App Service zawiera zalecenia, które pomogą Ci poprawić poziom zabezpieczeń wdrożenia. Punkt odniesienia dla tej usługi został opracowany na podstawie testu porównawczego zabezpieczeń platformy Azure w wersji [1.0,](../security/benchmarks/overview-v1.md)który zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure przy użyciu naszych wskazówek dotyczących najlepszych rozwiązań. Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących App Service. **Kontrolki** nie dotyczy App Service zostały wykluczone.

Aby zobaczyć, App Service mapowanie na test porównawczy zabezpieczeń platformy Azure, zobacz pełny App Service [mapowania punktów odniesienia zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** W przypadku korzystania App Service w warstwie cenowej Izolowana, nazywanej również środowiskiem App Service Environment (ASE), można wdrożyć bezpośrednio w podsieci w ramach usługi Azure Virtual Network. Użyj sieciowych grup zabezpieczeń, aby zabezpieczyć Azure App Service Environment przez zablokowanie ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej lub ograniczenie dostępu do aplikacji w App Service Environment.

Domyślnie sieciowe grupy zabezpieczeń zawierają regułę niejawnego odmowy o najniższym priorytecie i wymagają jawnego dodania reguł zezwalania. Dodaj reguły zezwalania dla sieciowej grupy zabezpieczeń na podstawie najmniej uprzywilejowanego podejścia sieciowego. Bazowe maszyny wirtualne używane do hostowania App Service Environment nie są bezpośrednio dostępne, ponieważ znajdują się w subskrypcji zarządzanej przez firmę Microsoft.

Chroń dane App Service Environment, przekieruj ruch za pośrednictwem Web Application Firewall (WAF) Azure Application Gateway. Używaj punktów końcowych usługi w połączeniu z Application Gateway, aby zabezpieczyć przychodzący ruch publikowania do aplikacji.  

W środowisku wielodostępnym App Service (aplikacja nie w warstwie Izolowana) użyj sieciowych grup zabezpieczeń, aby zablokować ruch wychodzący z aplikacji. Umożliwianie aplikacjom dostępu do zasobów w chmurze Virtual Network za pomocą funkcji Virtual Network Integracji urządzeń. Ta funkcja może również służyć do blokowania ruchu wychodzącego do publicznych adresów z aplikacji. Virtual Network Integracji nie można używać do zapewnienia dostępu przychodzącego do aplikacji.  

Zabezpieczanie ruchu przychodzącego do aplikacji za pomocą:
- Ograniczenia dostępu — seria reguł zezwalania lub odmowy, które kontrolują dostęp przychodzący
- Punkty końcowe usługi — mogą odrzucać ruch przychodzący spoza określonych sieci wirtualnych lub podsieci
- Prywatne punkty końcowe — uwidocznij aplikację dla Virtual Network przy użyciu prywatnego adresu IP. Po włączeniu prywatnych punktów końcowych w aplikacji nie jest ona już dostępna z Internetu

W przypadku Virtual Network integracji z sieciami wirtualnymi w tym samym regionie należy używać sieciowych grup zabezpieczeń i tabel tras z trasami zdefiniowanymi przez użytkownika. Trasy zdefiniowane przez użytkownika można umieścić w podsieci integracji, aby wysyłać ruch wychodzący zgodnie z zamierzeniami.  

Rozważ zaimplementowanie zasad Azure Firewall, aby centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej. 

- [Blokowanie App Service Environment](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) 10 najochlejsza ochrona przed lukami w zabezpieczeniach](https://owasp.org/www-project-top-ten/)

- [Sieciowe grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md)

- [Integrowanie aplikacji z usługą Azure Virtual Network](web-sites-integrate-with-vnet.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Jak utworzyć zewnętrznego ase](environment/create-external-ase.md)

- [Jak utworzyć wewnętrzny system ASE](environment/create-ilb-ase.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** implementowanie zaleceń dotyczących ochrony sieci Azure Security Center sieci w celu zabezpieczenia zasobów sieciowych i konfiguracji związanych z App Service i interfejsami API.

Użyj Azure Firewall, aby wysyłać ruch oraz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci między subskrypcjami i sieciami wirtualnymi. Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z Virtual Network. Usługa Azure Firewall jest również w pełni zintegrowana z Azure Monitor rejestrowania i analizy.

- [Azure Firewall omówienie](../firewall/overview.md)

- [Informacje o zabezpieczeniach sieciowych zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć monitorowanie i ochronę App Service](../security-center/defender-for-app-service-introduction.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Zabezpieczanie aplikacji dostępnej w Internecie w środowisku App Service Environment (ASE) przez:
- Wdrażanie aplikacji Web Application Firewall (WAF) z Azure Application Gateway przed aplikacją dostępną z Internetu
- Użyj ograniczeń dostępu, aby zabezpieczyć ruch przychodzący do Application Gateway
- Zabezpieczanie aplikacji przy użyciu usługi Azure Active Directory (Azure AD) w celu zapewnienia uwierzytelniania
- Ustaw minimalną wersję TLS na 1.2
- Ustaw aplikację na wartość Tylko HTTPS

Przekieruj cały ruch aplikacji wychodzący przez Azure Firewall i monitoruj dzienniki. 

Aby zabezpieczyć aplikację dostępną z Internetu w wielodostępnych App Service (np. nie w warstwie izolowanej)
- Wdrażanie Web Application Firewall z obsługą aplikacji przed aplikacją
- Użyj ograniczeń dostępu lub punktów końcowych usługi, aby zabezpieczyć ruch przychodzący do Web Application Firewall (WAF)
- Zabezpieczanie aplikacji przy użyciu usługi Azure AD w celu zapewnienia uwierzytelniania
- Ustaw minimalną wersję TLS na 1.2
- Ustaw aplikację na wartość Tylko HTTPS
- Użyj opcji Integracja z siecią wirtualną i ustawień aplikacji WEBSITE_VIRTUAL NETWORK_ROUTE_ALL, aby cały ruch wychodzący podlegał sieciowym grupom zabezpieczeń i trasom zdefiniowanym przez użytkownika w podsieci integracji.

Podobnie jak w przypadku aplikacji Application Service Environment, przekieruj cały ruch aplikacji wychodzący przez urządzenie Azure Firewall i monitoruj dzienniki w aplikacji.

Ponadto przejrzyj zalecenia i postępuj zgodnie z zaleceniami w dokumencie Blokowanie App Service Environment danych.

- [Blokowanie App Service Environment](environment/firewall-integration.md)

- [Zapora aplikacji internetowej platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service dostępu](app-service-ip-restrictions.md)

- [Śledzenie alertów WAF i łatwe monitorowanie trendów za pomocą Azure Monitor ](../azure-monitor/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** zabezpieczanie App Service Environment zgodnie z opisem w dokumentacji blokowania App Service Environment danych. Zastosuj funkcję zintegrowanej analizy zagrożeń w programie Azure Security Center, aby uniemożliwić komunikację ze znanymi złośliwymi lub nieużywanymi publicznymi adresami IP. Użyj ograniczeń dostępu, aby zabezpieczyć ruch przychodzący do Application Gateway. 

Zabezpieczanie aplikacji wielodostępnej App Service (aplikacja nie w warstwie Izolowana) za pomocą publicznego punktu końcowego dostępnego z Internetu. Zezwala na ruch tylko z określonej podsieci w obrębie Virtual Network i blokuje wszystkie inne. Użyj ograniczeń dostępu, aby skonfigurować listy Access Control sieci (ograniczenia adresów IP) w celu blokowania dozwolonego ruchu przychodzącego.

Zdefiniuj priorytet na uporządkowanej liście zezwalania lub odmowy, aby zarządzać dostępem sieciowym do aplikacji. Ta lista może zawierać adresy IP lub Virtual Network podsieci. Niejawna reguła "odmów wszystkim" istnieje na końcu listy, gdy zawiera co najmniej jeden wpis. Ta funkcja działa ze wszystkimi hostowanych obciążeń App Service, w tym Web Apps, API Apps, aplikacjami systemu Linux, aplikacjami kontenerów systemu Linux i usługami Functions. 

Użyj punktów końcowych usługi, aby ograniczyć dostęp do aplikacji internetowej z usługi Azure Virtual Network. Ogranicz dostęp do aplikacji wielodostępnej App Service (aplikacja nie w warstwie Izolowana) z wybranych podsieci z punktami końcowymi usługi. 

- [Azure App Service Static IP Restrictions](app-service-ip-restrictions.md) (Ograniczenia statycznych adresów IP usługi Azure App Service)

- [Zapora aplikacji internetowej platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Jak skonfigurować aplikację Web Application Firewall (WAF) dla App Service Environment](environment/app-service-app-service-environment-web-application-firewall.md)

- [Zabezpieczanie środowisk ASE zgodnie z opisem w te tematu Blokowanie App Service Environment](environment/firewall-integration.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** monitoruje żądania i odpowiedzi wysyłane do i z App Service za pomocą Security Center. Ataki na aplikację internetową można monitorować przy użyciu usługi Application Gateway w czasie rzeczywistym, która ma usługę Web Application Firewall, włączaną za pomocą zintegrowanego rejestrowania z usługi Azure Monitor w celu śledzenia alertów Web Application Firewall i łatwego monitorowania trendów.

- [Azure Web Application Firewall na Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Zarządzanie ruchem dla aplikacji w App Service Environment:

- Zabezpiecz App Service Environment zgodnie z opisem w tece Blokowanie App Service Environment
- Wdrażanie Application Gateway, która ma Azure Web Application Firewall przed aplikacjami dostępnymi z Internetu
- Ustaw aplikację tak, aby był dostępna tylko za pośrednictwem protokołu HTTPS

Zarządzanie ruchem aplikacji dostępnej z Internetu w wielodostępnej aplikacji App Service (nie w warstwie izolowanej): 

- Wdrażanie Application Gateway, która Azure Web Application Firewall włączona przed aplikacjami dostępnymi z Internetu
- Użyj ograniczeń dostępu lub punktów końcowych usługi, aby zabezpieczyć ruch przychodzący do Web Application Firewall. Możliwość ograniczeń dostępu działa ze wszystkimi hostowanych obciążeń App Service, w tym Web Apps, API Apps, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje.

- Ustawianie dostępu do aplikacji tylko za pośrednictwem protokołu HTTPS
- Ogranicz dostęp do aplikacji App Service z ograniczeniami statycznych adresów IP, tak aby odbierała ruch z adresu VIP w bramie aplikacji jako jedyny adres z dostępem.

Zapoznaj się z linkami, do których się odwołujesz, aby uzyskać dodatkowe informacje.

- [Azure App Service Static IP Restrictions](app-service-ip-restrictions.md) (Ograniczenia statycznych adresów IP usługi Azure App Service)

- [Zapora aplikacji internetowej platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Jak skonfigurować end-to-end TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpieczanie środowisk ASE zgodnie z opisem w tece Blokowanie App Service](/azure/app-service/environment/firewall-integration)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizowanie złożoności i obciążeń administracyjnych reguł zabezpieczeń sieci

**Wskazówki:** App Service ma wiele punktów końcowych, które są używane do zarządzania usługą. Te adresy punktu końcowego są również uwzględnione w tagu usługi adresu IP AppServiceManagement. Tag AppServiceManagement jest używany tylko z App Service Environment, aby zezwolić na taki ruch. 

Możesz zezwolić na ruch dla odpowiedniej usługi lub go odrzucić, określając nazwę tagu usługi w odpowiednim polu źródła lub miejsca docelowego reguły. App Service przychodzące są śledzone w tagu usługi AppService IP. Nie ma tagu usługi IP, który zawiera adresy wychodzące używane przez App Service.

Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów.

- [Tagi usługi dla sieci wirtualnej](../virtual-network/service-tags-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych App Service aplikacji. 

Zachowaj konfiguracje zabezpieczeń przy Azure Policy aliasów w przestrzeniach nazw "Microsoft.Web" i "Microsoft.Network". Utwórz zasady niestandardowe w celu inspekcji lub wymuszenia konfiguracji sieci App Service aplikacji. 

Użyj wbudowanych definicji zasad dla App Service, takich jak:
- Aplikacja powinna używać punktu końcowego usługi dla sieci wirtualnej
- Aplikacja powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Ustaw minimalną wersję TLS na bieżącą wersję

Zapoznaj się z przywoływami linków, aby uzyskać dodatkowe informacje.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak skonfigurować end-to-end TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpieczanie środowisk ASE zgodnie z opisem w te tematu Blokowanie App Service](/azure/app-service/environment/firewall-integration)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Udokumentowanie reguł konfiguracji ruchu

**Wskazówki:** Użyj tagów dla sieciowych grup zabezpieczeń i innych powiązanych zasobów, w tym przepływu ruchu App Service.

Określ potrzebę biznesową, czas trwania i tak dalej, w polu "Opis" dla wszystkich reguł, które zezwalają na ruch do lub z sieci dla poszczególnych reguł sieciowych grup zabezpieczeń.

Zastosuj dowolną wbudowaną definicję usługi Azure Policy związaną z efektami tagowania, taką jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów, i powiadamiać o wszelkich istniejących nieotagowanych zasobach. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby szukać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service dostępu](/azure/app-service/app-service-ip-restrictions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Używanie zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki:** Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych oraz wszelkich zasobów związanych z App Service. 

Zastosuj jedną z Azure Policy wbudowanych definicji usługi App Service, takich jak zasady, które przeprowadza inspekcję aplikacji pod celu korzystania z usługi punktu końcowego sieci wirtualnej. Utwórz alerty w Azure Monitor wyzwalane w przypadku zmiany krytycznych ustawień sieciowych lub zasobów sieciowych. 

Przejrzyj szczegółowe alerty zabezpieczeń i zalecenia w Security Center, w portalu lub za pomocą narzędzi programowych. Wyeksportuj te informacje lub wyślij je do innych narzędzi do monitorowania w twoim środowisku. Dostępne są narzędzia do eksportowania alertów i zaleceń ręcznie lub w sposób ciągły i ciągły. Za pomocą tych narzędzi można:
 
- Ciągłe eksportowanie do obszaru roboczego usługi Log Analytics
- Ciągłe eksportowanie do Azure Event Hubs (w przypadku integracji z systemami SIEM innych firm)
- Eksportowanie do pliku CSV (jeden raz)

Zaleca się utworzenie procesu za pomocą zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i szybkiego wykrywania zmian.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak tworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Eksportowanie alertów zabezpieczeń i zaleceń](../security-center/continuous-export.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** Zintegruj App Service Environment (ASE) z usługą Azure Monitor, aby wysyłać dzienniki do usługi Azure Storage, Azure Event Hubs lub Log Analytics. Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure dla rejestrowania inspekcji płaszczyzny sterowania. Alerty zabezpieczeń z Security Center są publikowane w dzienniku aktywności platformy Azure. Przeszukaj dane dziennika aktywności platformy Azure, które pozwalają określić "co, kto i kiedy" dla wszystkich operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania dla Azure App Service i innych zasobów platformy Azure. Zapisz zapytania do użycia w przyszłości, przypnij wyniki zapytania do pulpitów nawigacyjnych platformy Azure i utwórz alerty dzienników. Ponadto użyj interfejsu API REST dostępu do danych w Application Insights, aby programowo uzyskać dostęp do telemetrii.

Użyj Microsoft Azure Sentinel, skalowalnego, natywnego dla chmury rozwiązania do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM) dostępnego do łączenia się z różnymi źródłami danych i łącznikami w zależności od wymagań biznesowych. Dane można również włączyć i dołączyć do systemu zarządzania zdarzeniami zabezpieczeń (SIEM) innej firmy, takiego jak Barracuda w Azure Marketplace.

- [Rejestrowanie aktywności ase](./environment/using-an-ase.md#logging)

- [Jak włączyć ustawienia diagnostyczne dla Azure App Service](troubleshoot-diagnostic-logs.md)

- [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Eksportowanie telemetrii z usługi Application Insights](../azure-monitor/app/export-telemetry.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure dla rejestrowania inspekcji płaszczyzny sterowania App Service. Wysyłanie dzienników do obszaru roboczego usługi Log Analytics, usługi Azure Event Hub lub konta usługi Azure Storage.

"Co, kto i kiedy" dla dowolnych operacji zapisu (PUT, POST, DELETE) wykonywanych na poziomie płaszczyzny sterowania można określić przy użyciu danych dziennika aktywności platformy Azure dla App Service i innych zasobów platformy Azure.

Ponadto usługa Azure Key Vault scentralizowane zarządzanie kluczami tajnymi z zasadami dostępu i historią inspekcji. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć ustawienia diagnostyczne dla Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager operacyjne](../role-based-access-control/resource-provider-operations.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** w Azure Monitor ustaw okres przechowywania dzienników dla obszarów roboczych usługi Log Analytics skojarzonych z zasobami usługi App Service zgodnie z przepisami dotyczącymi zgodności w organizacji.
- [Jak ustawić parametry przechowywania dzienników](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Przejrzyj ustawienia diagnostyczne dziennika aktywności platformy Azure App Service zasobów przy użyciu dzienników wysyłanych do obszaru roboczego usługi Log Analytics. Wykonywanie zapytań w u usługi Log Analytics w celu wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i zapewnienia wielu innych szczegółowych informacji na podstawie zebranych danych.

Użyj Application Insights dla aplikacji App Service, aby zbierać dane dzienników, wydajności i błędów. Wyświetlanie danych telemetrycznych zebranych przez Application Insights w Azure Portal.

Jeśli wdrożono aplikację Web Application Firewall (WAF), możesz monitorować ataki na aplikacje App Service przy użyciu dziennika Web Application Firewall w czasie rzeczywistym. Dziennik jest zintegrowany z Azure Monitor, aby śledzić alerty Web Application Firewall i łatwo monitorować trendy.

Użyj Azure Sentinel, skalowalnego i natywnego dla chmury rozwiązania do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM) do integracji z różnymi źródłami danych i łącznikami zgodnie z wymaganiami. Opcjonalnie możesz włączyć i dołączyć dane do rozwiązania do zarządzania zdarzeniami informacji zabezpieczających innej firmy w Azure Marketplace.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Jak zintegrować App Service Environment z Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Jak Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** konfigurowanie Security Center subskrypcji platformy Azure i przeglądanie wygenerowanych alertów. Użyj Azure Monitor, aby pobrać dane dziennika aktywności do centrum zdarzeń, gdzie mogą być odczytywane przez rozwiązanie do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM), takie jak Azure Sentinel. 

Monitoruj ataki na aplikacje App Service za pomocą dziennika Web Application Firewall w czasie rzeczywistym z wdrożonym Azure Web Application Firewall (WAF). Dziennik jest zintegrowany z Azure Monitor, aby śledzić alerty Web Application Firewall (WAF) i łatwo monitorować trendy.

- [Jak zintegrować swoją App Service Environment z Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Eksportowanie alertów zabezpieczeń i zaleceń](../security-center/continuous-export.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: tożsamość i Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i mieć możliwość wykonywania zapytań. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Jak używać tożsamości zarządzanych na App Service i Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Azure Active Directory (Azure AD) nie ma koncepcji haseł domyślnych. Zapewnia dostęp do płaszczyzny sterowania App Service.

Ogólnie rzecz biorąc, unikaj implementowania domyślnych haseł dostępu użytkowników podczas tworzenia własnych aplikacji. Użyj jednego z dostawców tożsamości dostępnych domyślnie dla App Service, takich jak usługa Azure AD, konto Microsoft, Facebook, Google lub Twitter.

Wyłącz dostęp anonimowy, chyba że musisz go obsługiwać. 

- [Dostawcy tożsamości domyślnie dostępni w Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Uwierzytelnianie i autoryzacja w Azure App Service i Azure Functions](overview-authentication-authorization.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Funkcje zarządzania tożsamościami i dostępem w programie Security Center do monitorowania i śledzenia liczby kont administracyjnych. 

Użyj zaleceń Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel. 
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Utwórz proces monitorowania konfiguracji zasobów sieciowych i wykrywania zmian na kontach administracyjnych.

- [Jak używać usługi Azure Security Center do monitorowania tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dowiedz się więcej o udzielaniu użytkownikom dostępu do aplikacji](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** uwierzytelnianie App Service za Azure Active Directory (Azure AD). Zapewnia ona usługę OAuth 2.0 dla dostawcy tożsamości i umożliwia autoryzowany dostęp do aplikacji mobilnych i internetowych. 

App Service używają tożsamości federowej, w której dostawca tożsamości innej firmy zarządza tożsamościami użytkowników i przepływem uwierzytelniania. Ci dostawcy tożsamości są domyślnie dostępni:

- Azure AD
- Konto Microsoft

- Facebook

- Google

- Twitter

Po włączeniu uwierzytelniania i autoryzacji u jednego z tych dostawców punkt końcowy logowania jest dostępny do uwierzytelniania użytkownika i weryfikacji tokenów uwierzytelniania od dostawcy.

- [Opis uwierzytelniania i autoryzacji w Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Dowiedz się więcej o uwierzytelnianiu i autoryzacji w Azure App Service](overview-authentication-authorization.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na Azure Active Directory danych

**Wskazówki:** Włącz funkcję uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w Security Center.

Implementowanie uwierzytelniania wieloskładnikowego dla usługi Azure AD. Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi zasobami. 

- [Uwierzytelnianie wieloskładnikowe zabezpieczeń platformy Azure](/previous-versions/azure/security/develop/secure-aad-app)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w ramach Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym (PAW) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure. 

- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** generowanie dzienników i alertów Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD) w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto wykrywanie ryzyka w usłudze Azure AD umożliwia wyświetlanie alertów i raportów dotyczących ryzykownych zachowań użytkowników.

Ochrona przed zagrożeniami w usłudze Security Center zapewnia kompleksową ochronę środowiska, w tym ochronę przed zagrożeniami dla zasobów obliczeniowych platformy Azure, takich jak maszyny z systemem Windows, maszyny z systemem Linux App Service i kontenery platformy Azure.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Opis wykrywania ryzyka w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami dla zasobów obliczeniowych platformy Azure](../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp do Azure Portal tylko z określonych grup logicznych zakresów adresów IP, krajów lub regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9: Użyj Azure Active Directory

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla App Service aplikacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowanego i podczas ich przesyłania, a także insygnia, skróty i bezpiecznie przechowuje poświadczenia użytkownika.

- [Jak skonfigurować aplikacje Azure App Service do korzystania z logowania usługi Azure AD](configure-authentication-provider-aad.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** odnajdywanie nieaktywnego konta za pomocą dzienników dostarczonych przez Azure Active Directory (Azure AD). Użyj przeglądów dostępu tożsamości platformy Azure, aby efektywnie zarządzać członkostwem w grupach i dostępem do aplikacji dla przedsiębiorstw, a także przypisań ról. Okresowo sprawdzaj dostęp użytkowników, aby upewnić się, że tylko zamierzony użytkownik ma dalszy dostęp. 

- [Informacje o raportowaniu w usłudze Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak używać przeglądów dostępu tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** Użyj usługi Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla App Service aplikacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych magazynowanego i podczas przesyłania, instyt, skrótów i bezpiecznie przechowuje poświadczenia użytkownika.

Dostęp do źródeł dzienników zdarzeń, inspekcji i inspekcji usługi Azure AD umożliwia integrację z usługą Azure Sentinel lub rozwiązaniem do zarządzania zdarzeniami zabezpieczeń (SIEM) innej firmy. Usprawnij proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD oraz wysyłając dzienniki inspekcji i logowania do obszaru roboczego usługi Log Analytics. Żądane alerty dzienników można skonfigurować w ramach usługi Log Analytics.

- [Jak skonfigurować aplikacje Azure App Service do korzystania z logowania usługi Azure AD](configure-authentication-provider-aad.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla App Service aplikacji. 

Użyj Azure AD Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami użytkowników, takie jak odchylenie zachowania logowania do konta na płaszczyźnie sterowania za pomocą Azure Portal. Możesz również pozysać dane do Azure Sentinel w celu dalszego badania. 

- [Jak skonfigurować aplikację Azure App Service do korzystania z logowania usługi Azure AD](configure-authentication-provider-aad.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** Niedostępne; Skrytka klienta nie jest obsługiwane w Azure App Service.

- [Lista Skrytka klienta obsługiwanych przez program](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia App Service zasobów, które przechowują lub przetwarzają informacje poufne.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** W przypadku App Service Environment należy zaimplementować oddzielne subskrypcje, grupy zarządzania lub oba te rozwiązania dla środowisk deweloperskich, testowych i produkcyjnych. Aplikacje, które przetwarzają poufne informacje z innych aplikacji, można izolować w taki sam sposób. Wdrażanie App Service aplikacji w Virtual Network. Użyj sieciowych grup zabezpieczeń i podsieci w celu dalszej izolacji aplikacji. 

Istnieją dwa typy wdrożeń dla App Service (ASE). Oba umożliwiają odizolowanie ruchu na podstawie wymagań biznesowych.

- Zewnętrzne środowisko usługi aplikacji — uwidacznia App Service Environment hostowanych na adresie IP dostępnym z Internetu.

-  Środowisko application Service Environment wewnętrznego równoważenia obciążenia (ILB) — udostępnia aplikacje hostowane App Service Environment na adres IP wewnątrz Virtual Network. Wewnętrzny punkt końcowy jest wewnętrznym równoważeniem obciążenia ( ILB), dlatego jest nazywany asE wewnętrznego równoważenia obciążenia. 

W przypadku aplikacji wielodostępnych App Service (aplikacja nie w warstwie Izolowana) użyj usługi Virtual Network Integration, aby uzyskać dostęp aplikacji do zasobów w sieci wirtualnej.  Użyj dostępu do witryny prywatnej, aby udostępnić aplikację tylko z sieci prywatnej, takiej jak jedna z sieci wirtualnej platformy Azure. Virtual Network integracja jest używana tylko do wywołania wychodzącego z aplikacji do Virtual Network. Funkcja Virtual Network Integration działa inaczej, gdy jest używana z siecią wirtualną w tym samym regionie i z sieciami wirtualnymi w innych regionach. 
 
- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Jak utworzyć zewnętrznego ase](environment/create-external-ase.md)

- [Jak utworzyć wewnętrzny system ASE](environment/create-ilb-ase.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** Chociaż funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi App Service, można zmniejszyć ryzyko eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły, w których miejsce docelowe używa "tagu" dla usług internetowych lub platformy Azure. 

Firma Microsoft zarządza podstawową infrastrukturą App Service wdrożyła ścisłe mechanizmy kontroli, aby zapobiec utracie lub narażeniu danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki:** użyj domyślnej minimalnej wersji protokołu TLS 1.2 skonfigurowanej w ustawieniach protokołu TLS/SSL do szyfrowania wszystkich informacji podczas przesyłania. Upewnij się również, że wszystkie żądania połączenia HTTP są przekierowywany do protokołu HTTPS.

- [Understand encryption in transit for Azure App Service web apps (Opis szyfrowania podczas przesyłania Azure App Service internetowych)](security-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** obecnie niedostępne. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla App Service. 

Tagowanie App Service, które mogą przetwarzać poufne informacje. W razie potrzeby zaim implementuj rozwiązanie innej firmy na potrzeby zgodności.

Firma Microsoft zarządza podstawową platformą i traktuje wszystkie dane klientów jako poufne oraz przechodzi do bardzo długich okresów, aby chronić się przed utratą i narażeniem danych klientów. Aby zapewnić bezpieczeństwo danych klientów na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych funkcji i kontroli ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6. Kontrola dostępu do zasobów przy użyciu kontroli dostępu na platformie Azure 

**Wskazówki:** Kontrola dostępu oparta na rolach (RBAC) na platformie Azure w usłudze Azure Active Directory (Azure AD) umożliwia kontrolowanie dostępu do płaszczyzny kontroli App Service na Azure Portal.

- [Jak skonfigurować RBAC platformy Azure](../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** zawartość witryny internetowej w aplikacji App Service, takiej jak pliki, jest przechowywana w usłudze Azure Storage, która automatycznie szyfruje zawartość magazynowanych danych. Wybierz przechowywanie wpisów tajnych aplikacji w Key Vault i pobieranie ich w czasie wykonywania.

Wpisy tajne dostarczane przez klienta są szyfrowane podczas przechowywania w App Service danych konfiguracji.

Należy pamiętać, że dyski dołączone lokalnie mogą być używane opcjonalnie przez witryny internetowe jako magazyn tymczasowy (na przykład D:\local i %TMP"), ale nie są szyfrowane w spoczynku.

- [Opis kontroli ochrony danych dla Azure App Service](./security-recommendations.md#data-protection)

- [Opis szyfrowania magazynów w usłudze Azure Storage](../storage/common/storage-service-encryption.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące wszelkich zmian w aplikacjach produkcyjnych App Service i innych krytycznych lub powiązanych zasobach.

- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** Zastosuj praktykę DevSecOps, aby zagwarantować, że aplikacje App Service są bezpieczne i pozostaną zabezpieczone przez cały czas ich cyklu życia. Metodyka DevSecOps dołącza zespół ds. zabezpieczeń organizacji i jej możliwości do praktyk metodyki DevOps, co oznacza, że za bezpieczeństwo odpowiadają wszyscy członkowie zespołu.

Przejrzyj i postępuj zgodnie z zaleceniami Security Center w zakresie zabezpieczania App Service aplikacji.

- [Jak dodać ciągłą weryfikację zabezpieczeń do potoku ciągłej integracji/ciągłego integracji](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Jak zaimplementować Azure Security Center oceny luk w zabezpieczeniach](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Ustalanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w systemach bazowych, które obsługują App Service. Do mierzenia ryzyka w środowisku można jednak użyć ważności zaleceń w ramach Security Center oraz wskaźników bezpieczeństwa. Ocena bezpieczeństwa jest oparta na tym, ile Security Center rekomendacji, które zostały skorygowane. Aby ustalić priorytety zaleceń do rozwiązania w pierwszej kolejności, należy wziąć pod uwagę ważność każdego z nich.

- [Przewodnik po zaleceniach dotyczących zabezpieczeń](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph do wykonywania zapytań lub odnajdywania wszystkich zasobów (takich jak zasoby obliczeniowe, magazynowe, sieciowe, porty, protokoły itp.) w ramach subskrypcji. Upewnij się, że do dzierżawy zastosowano odpowiednie uprawnienia i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach swoich subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą być Resource Graph pośrednictwem usługi , zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów w przyszłości.

- [Jak tworzyć zapytania za pomocą Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Opis kontroli RBAC na platformie Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów platformy Azure przy użyciu metadanych w celu logicznego zorganizowania ich w taksonomię.

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji zgodnie z potrzebami, aby organizować i śledzić zasoby platformy Azure. Uzgadniaj spis w regularnych odstępach czasu i upewnij się, że w ramach tego procesu nieautoryzowane zasoby zostaną usunięte z subskrypcji.

Wybierz Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Zapoznaj się z przywoływami linków, aby uzyskać dodatkowe informacje.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych w zależności od potrzeb organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach.

Użyj Azure Resource Graph do wykonywania zapytań lub odnajdywania zasobów w ramach ich subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6. Monitorowanie niezatwierdzonych aplikacji w ramach zasobów obliczeniowych

**Wskazówki:** Użyj Azure Resource Graph, aby odpytywać lub odnajdywać zasoby w ramach subskrypcji i upewnić się, że odnalezione zasoby platformy Azure są zatwierdzane na podstawie zasad organizacji.

Używaj usługi WebJobs w App Service, aby monitorować niezatwierdzone aplikacje, które są wdrażane w ramach zasobów obliczeniowych. Użyj usługi WebJobs, aby uruchomić program lub skrypt w tym samym wystąpieniu co aplikacja internetowa, aplikacja interfejsu API lub aplikacja mobilna. Definiowanie konfiguracji i monitorowania usługi WebJob za pomocą dzienników. Na stronie Szczegóły uruchamiania usługi WebJob wybierz pozycję Przełącz dane wyjściowe, aby wyświetlić tekst zawartości dziennika. Należy pamiętać, że usługi WebJob nie są jeszcze obsługiwane w App Service dla systemu Linux.

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

- [Samouczek — tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki start — uruchamianie pierwszego zapytania Resource Graph pomocą Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone. Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach. Usuń wszystkie wdrożone aplikacje, które nie zostały zatwierdzone zgodnie z zasadami organizacji.

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

- [Samouczek — tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki start — uruchamianie pierwszego zapytania Resource Graph pomocą Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="68-use-only-approved-applications"></a>6.8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki:** Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone. Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w twoich subskrypcjach. Usuń wszystkie wdrożone aplikacje, które nie zostały zatwierdzone zgodnie z zasadami organizacji. 

- [Uruchamianie zadań w tle za pomocą zadań WebJob na Azure App Service](webjobs-create.md)

- [Samouczek — tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki start — uruchamianie pierwszego zapytania Resource Graph pomocą Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** Utwórz proces okresowego przeglądania nieautoryzowanych usług platformy Azure, aby upewnić się, że w Twoich subskrypcjach są używane tylko autoryzowane usługi platformy Azure.

Użyj Azure Resource Graph, w ramach tego procesu, aby tworzyć zapytania lub odnajdywać zasoby w ramach ich subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku zostały zatwierdzone.

Skonfiguruj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Używaj usługi WebJobs w App Service do monitorowania niezatwierdzonych aplikacji oprogramowania wdrożonych w zasobach komputera. Użyj usługi WebJobs, aby uruchomić program lub skrypt w tym samym wystąpieniu co aplikacja internetowa, aplikacja interfejsu API lub aplikacja mobilna. Definiowanie konfiguracji i monitorowania usługi WebJob za pomocą dzienników. Na stronie Szczegóły uruchamiania usługi WebJob wybierz pozycję Przełącz dane wyjściowe, aby wyświetlić tekst zawartości dziennika. Należy pamiętać, że usługi WebJob nie są jeszcze obsługiwane w App Service dla systemu Linux.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10. Utrzymywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki:** regularnie wdrażaj proces tworzenia spisu i przeglądania tytułów oprogramowania w subskrypcjach, aby upewnić się, że w Twoich subskrypcjach są używane tylko autoryzowane usługi platformy Azure.

Użyj Azure Resource Graph w ramach tego procesu, aby tworzyć zapytania lub odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure odnalezione w środowisku zostały zatwierdzone.

Skonfiguruj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Podobnie należy użyć usługi WebJobs w App Service do spisu niezatwierdzonych aplikacji oprogramowania wdrożonych w zasobach komputera. Zdefiniuj ich konfigurację i monitorowanie za pomocą dzienników. Na stronie Szczegóły uruchamiania usługi WebJob wybierz pozycję Przełącz dane wyjściowe, aby wyświetlić tekst zawartości dziennika. Należy pamiętać, że usługi WebJob nie są jeszcze obsługiwane w App Service dla systemu Linux.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ogranicz użytkownikom możliwość wykonywania skryptów w ramach zasobów obliczeniowych

**Wskazówki:** Usługa WebJobs w App Service umożliwia klientom uruchamianie programu lub skryptu w tym samym wystąpieniu co aplikacja internetowa, aplikacja interfejsu API lub aplikacja mobilna. Użytkownik jest odpowiedzialny za definiowanie konfiguracji w celu ograniczenia lub ograniczenia wszelkich skryptów, które są niedozwolone przez organizację. App Service nie zapewnia mechanizmu natywnego ograniczania wykonywania skryptu. Należy pamiętać, że usługi WebJob nie są jeszcze obsługiwane w App Service dla systemu Linux.

- [Uruchamianie zadań w tle za pomocą zadań WebJob na Azure App Service](webjobs-create.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki:** Implementowanie oddzielnych subskrypcji lub grup zarządzania w celu zapewnienia izolacji dla aplikacji App Service ryzyka. Wdrażanie aplikacji o wyższym ryzyku we własnym Virtual Network, ponieważ zabezpieczenia obwodowe w App Service są osiągane przez użycie sieci wirtualnych. Ten App Service Environment to wdrożenie App Service podsieci w usłudze Azure Virtual Network. 

Istnieją dwa typy środowiska application service environment, środowiska usług aplikacji zewnętrznych i środowiska usług aplikacji wewnętrznego Load Balancer wewnętrznego. Wybierz najlepszą architekturę w zależności od wymagań.

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Tworzenie środowiska App Service zewnętrznego](environment/create-external-ase.md)

- [Tworzenie i używanie wewnętrznego Load Balancer App Service Environment](environment/create-ilb-ase.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla App Service wdrożonych aplikacji za pomocą Azure Policy.

Użyj Azure Policy aliasów w przestrzeni nazw "Microsoft.Web", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji App Service Web Apps.

Stosowanie wbudowanych definicji zasad, takich jak:

- App Service używać punktu końcowego usługi dla sieci wirtualnej

- Aplikacje internetowe powinny być dostępne tylko za pośrednictwem protokołu HTTPS

- Używanie najnowszej wersji TLS w aplikacjach

Zaleca się udokumentowanie procesu stosowania wbudowanych definicji zasad do standardowego użycia.   

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w Azure App Service aplikacji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Opis Azure Policy danych](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** wybierz Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać jego kodem podczas korzystania z niestandardowych Azure Policy definicji.

Użyj istniejącego potoku ciągłej integracji (CI) i ciągłego dostarczania (CD), aby wdrożyć znaną bezpieczną konfigurację.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Azure Repos dokumentacji](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Web", aby utworzyć niestandardowe zasady do alertów, inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku do zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** Użyj wbudowanych definicji Azure Policy, a także aliasów usługi Azure Policy w przestrzeni nazw "Microsoft.Web", aby utworzyć niestandardowe zasady do alertów, inspekcji i wymuszania konfiguracji systemu. 

Zastosuj Azure Policy [audit], [deny] i [deploy if not exist], aby automatycznie wymusić konfiguracje zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Użyj tożsamości zarządzanych, aby zapewnić aplikacjom zarządzanym App Service automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają aplikacjom uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w Key Vault, bez żadnych poświadczeń w kodzie. Upewnij się, że na Azure Key Vault włączono usuwanie Azure Key Vault.

- [Jak włączyć usuwanie nie Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Jak używać tożsamości zarządzanych na App Service](overview-managed-identity.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Użyj tożsamości zarządzanych, aby udostępnić App Service wdrożonym aplikacjom automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tożsamości zarządzane umożliwiają aplikacjom uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak używać tożsamości zarządzanych na App Service](overview-managed-identity.md)

- [Jak zapewnić uwierzytelnianie Key Vault tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1. Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** Funkcja tworzenia kopii zapasowych i przywracania w App Service umożliwia łatwe tworzenie kopii zapasowych aplikacji ręcznie lub zgodnie z harmonogramem. Kopie zapasowe można skonfigurować do przechowywania przez czas nieokreślony. Możesz przywrócić aplikację do migawki poprzedniego stanu, nadpisując istniejącą aplikację lub przywracając ją do innej aplikacji.

App Service utworzyć kopię zapasową następujących informacji na koncie magazynu i kontenerze usługi Azure Storage, które zostały skonfigurowane do użycia przez aplikację:
- Konfiguracja aplikacji
- Zawartość pliku
- Baza danych połączona z aplikacją

Upewnij się, że regularne i automatyczne kopię zapasową są występujące z częstotliwością zgodnie z zasadami organizacji.

- [Opis Azure App Service tworzenia kopii zapasowych](manage-backup.md)

- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](../storage/common/customer-managed-keys-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** Użyj funkcji tworzenia kopii zapasowych i przywracania App Service do tworzenia kopii zapasowych aplikacji. Funkcje tworzenia kopii zapasowej wymagają konta usługi Azure Storage do przechowywania informacji o kopii zapasowej aplikacji.

- Usługa Azure Storage zapewnia szyfrowanie danych magazynowych — użyj kluczy dostarczonych przez system lub własnych kluczy zarządzanych przez klienta. Jest to miejsce, w którym dane aplikacji są przechowywane, gdy nie są uruchomione w aplikacji internetowej na platformie Azure.
- Uruchamianie z pakietu wdrożeniowego jest funkcją wdrażania App Service. Umożliwia wdrażanie zawartości witryny z konta usługi Azure Storage przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).

- Key Vault odwołania są funkcją zabezpieczeń App Service. Umożliwia importowanie wpisów tajnych w czasie wykonywania jako ustawień aplikacji. Użyj tej funkcji, aby zaszyfrować adres URL sygnatury dostępu współdzielonego konta usługi Azure Storage.

Więcej informacji można znaleźć w linkach, do których się odwoływuje.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)

- [Przywracanie aplikacji uruchomionej w programie Azure App Service](web-sites-restore.md)

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Model szyfrowania i tabela zarządzania kluczami](../security/fundamentals/encryption-atrest.md)

- [Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta](configure-encrypt-at-rest-using-cmk.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** okresowe testowanie procesu przywracania dla wszystkich kopii zapasowych App Service aplikacji.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)

- [Jak przywrócić aplikację Azure App Service internetową](web-sites-restore.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** App Service kopii zapasowych są przechowywane na koncie usługi Azure Storage. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywać w sposób przezroczysty przy użyciu 256-bitowego szyfrowania AES, jednego z najmocniejszych dostępnych szyfrów blokowych i są zgodne ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym Resource Manager i klasycznych kont magazynu. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są domyślnie zabezpieczone, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Domyślnie dane na koncie magazynu są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Do szyfrowania danych możesz polegać na kluczach zarządzanych przez firmę Microsoft lub zarządzać szyfrowaniem przy użyciu własnych kluczy. Upewnij się, że na Azure Key Vault włączono usuwanie Azure Key Vault.

- [Informacje o szyfrowaniu usługi Azure Storage dla danych magazynowych](../storage/common/storage-service-encryption.md)

- [Jak włączyć usuwanie nie Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzacje przepływu pracy w Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również skorzystać z przewodnika obsługi zdarzeń zabezpieczeń komputera NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisać ważność do każdego alertu, aby ułatwić priorytetyzowanie alertów, które należy zbadać w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie ufności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwe intencje.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład produkcyjne lub nieprodukcyjne) i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemie. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST — Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z Tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych klienta został uzyskany przez niedozwoloną lub niedozwoloną stronę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportowanie alertów Security Center i zaleceń przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w ciągły sposób. Użyj łącznika Security Center danych, aby przesyłać strumieniowo alerty do Azure Sentinel zgodnie z potrzebami biznesowymi.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
