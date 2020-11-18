---
title: Podstawa zabezpieczeń platformy Azure dla App Service
description: Linia bazowa zabezpieczeń App Service zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 640d51de6dac1d175f9b46f327b72f293cf025cb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849192"
---
# <a name="azure-security-baseline-for-app-service"></a>Podstawa zabezpieczeń platformy Azure dla App Service

Podstawą zabezpieczeń platformy Azure dla App Service są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia. Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące App Service. **Kontrolki** nie mają zastosowania do App Service zostały wykluczone.

Aby dowiedzieć się, jak App Service całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny App Service pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: w przypadku korzystania z App Service w warstwie cenowej izolowanej nazywanej także App Service Environment (ASE) można wdrożyć bezpośrednio w podsieci w ramach Virtual Network platformy Azure. Używaj sieciowych grup zabezpieczeń, aby zabezpieczyć Azure App Service Environment przez zablokowanie ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej lub ograniczenie dostępu do aplikacji w App Service Environment.

Domyślnie sieciowe grupy zabezpieczeń zawierają niejawną regułę odmowy o najniższym priorytecie, która wymaga jawnego dodawania reguł zezwalania. Dodaj reguły zezwalania dla sieciowej grupy zabezpieczeń na podstawie najmniej uprzywilejowanego podejścia do sieci. Źródłowe maszyny wirtualne, które są używane do hostowania App Service Environment nie są bezpośrednio dostępne, ponieważ znajdują się w subskrypcji zarządzanej przez firmę Microsoft.

Ochrona App Service Environment przez kierowanie ruchu przez zaporę aplikacji sieci Web (WAF) z włączoną usługą Azure Application Gateway. Używaj punktów końcowych usługi w połączeniu z Application Gateway, aby zabezpieczyć ruch przychodzący do aplikacji.  

W App Service z wieloma dzierżawcami (aplikacja spoza warstwy izolowanej) Użyj sieciowych grup zabezpieczeń, aby zablokować ruch wychodzący z aplikacji. Umożliwia aplikacjom dostęp do zasobów w programie lub za pomocą Virtual Network za pomocą funkcji integracji Virtual Network. Ta funkcja może również służyć do blokowania ruchu wychodzącego do publicznych adresów z aplikacji. Nie można użyć integracji Virtual Network, aby zapewnić dostęp przychodzący do aplikacji.  

Zabezpieczanie ruchu przychodzącego do aplikacji przy użyciu:
- Ograniczenia dostępu — seria reguł zezwalania lub odmowy kontrolujących dostęp przychodzący
- Punkty końcowe usługi — mogą odrzucać ruch przychodzący spoza określonych sieci wirtualnych lub podsieci
- Prywatne punkty końcowe — umożliwia udostępnienie aplikacji Virtual Network przy użyciu prywatnego adresu IP. Gdy prywatne punkty końcowe są włączone w aplikacji, nie są już dostępne do Internetu

W przypadku korzystania z funkcji integracji Virtual Network z sieciami wirtualnymi w tym samym regionie należy używać grup zabezpieczeń sieci i tabel tras ze zdefiniowanymi przez użytkownika trasami. Trasy zdefiniowane przez użytkownika mogą być umieszczane w podsieci integracji w celu wysyłania ruchu wychodzącego zgodnie z oczekiwaniami.  

Rozważ zaimplementowanie zapory platformy Azure w celu scentralizowanego tworzenia, wymuszania i rejestrowania zasad łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Zapora platformy Azure używa statycznego publicznego adresu IP dla zasobów sieci wirtualnej, dzięki czemu zapory zewnętrzne mogą identyfikować ruch pochodzący z sieci wirtualnej. 

- [Blokowanie App Service Environment](environment/firewall-integration.md)

- [Otwórz aplikację Web Application Security Project (OWASP) 10 najważniejszych luk w zabezpieczeniach](https://owasp.org/www-project-top-ten/)

- [Sieciowe grupy zabezpieczeń](/azure/virtual-network/security-overview)

- [Integrowanie aplikacji z usługą Azure Virtual Network](web-sites-integrate-with-vnet.md)

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Jak utworzyć zewnętrzny środowisko ASE](environment/create-external-ase.md)

- [Jak utworzyć wewnętrzny ASE](environment/create-ilb-ase.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Zaimplementuj zalecenia dotyczące ochrony sieci w Azure Security Center, aby zabezpieczyć zasoby sieciowe i konfiguracje związane z aplikacjami App Service i interfejsami API.

Używaj zapory platformy Azure do wysyłania ruchu sieciowego i centralnego tworzenia, wymuszania i rejestrowania zasad łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Zapora platformy Azure używa statycznego publicznego adresu IP dla zasobów sieci wirtualnej, dzięki czemu zapory zewnętrzne mogą identyfikować ruch pochodzący z Virtual Network. Usługa Zapora platformy Azure jest również w pełni zintegrowana z Azure Monitor na potrzeby rejestrowania i analizy.

- [Omówienie Zapory platformy Azure](../firewall/overview.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak włączyć monitorowanie i ochronę App Service](/azure/security-center/security-center-app-services)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Zabezpieczanie aplikacji dostępnej do internetu w App Service Environment (ASE) przez:
- Wdrażanie zapory aplikacji sieci Web (WAF) przy użyciu platformy Azure Application Gateway przed aplikacją internetową
- Używanie ograniczeń dostępu w celu zabezpieczania ruchu przychodzącego do Application Gateway
- Zabezpieczanie aplikacji za pomocą Azure Active Directory (Azure AD) w celu zapewnienia uwierzytelniania
- Ustaw minimalną wersję protokołu TLS na 1,2
- Ustaw tylko aplikację na HTTPS

Wszystkie ruch wychodzący z aplikacji przez urządzenie zapory platformy Azure i monitorowanie dzienników. 

Aby zabezpieczyć aplikację z dostępem do Internetu w App Service z wieloma dzierżawcami, (na przykład nie w warstwie izolowanej)
- Wdrażanie urządzenia z obsługą zapory aplikacji sieci Web przed aplikacją
- Używanie ograniczeń dostępu lub punktów końcowych usługi do zabezpieczania ruchu przychodzącego na urządzeniu zapory aplikacji sieci Web (WAF)
- Zabezpieczanie aplikacji za pomocą usługi Azure AD w celu zapewnienia uwierzytelniania
- Ustaw minimalną wersję protokołu TLS na 1,2
- Ustaw tylko aplikację na HTTPS
- Użyj integracji z siecią wirtualną i ustawienia aplikacji WEBSITE_VIRTUAL NETWORK_ROUTE_ALL, aby cały ruch wychodzący podlegał grupom zabezpieczeń sieci i trasom zdefiniowanym przez użytkownika w podsieci integracji.

Podobnie jak w przypadku aplikacji środowisko usługi aplikacji, należy zwiększyć ruch wychodzący przez urządzenie zapory platformy Azure i monitorować dzienniki w aplikacji.

Ponadto Przejrzyj i postępuj zgodnie z zaleceniami dotyczącymi blokowania App Service Environment dokumencie.

- [Blokowanie App Service Environment](environment/firewall-integration.md)

- [Zapora aplikacji sieci Web platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Ograniczenia dostępu Azure App Service](app-service-ip-restrictions.md)

- [Śledź alerty WAF i łatwo Monitoruj trendy dzięki Azure Monitor ](../azure-monitor/overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Zabezpiecz App Service Environment zgodnie z opisem w temacie blokowanie App Service Environment dokumentacji. Zastosuj zintegrowane funkcje analizy zagrożeń w Azure Security Center, aby odmówić komunikacji ze znanymi lub nieużywanymi publicznymi adresami IP. Użyj ograniczeń dostępu, aby zabezpieczyć ruch przychodzący do Application Gateway. 

Zabezpiecz App Service z wieloma dzierżawcami (aplikację, która nie znajduje się w warstwie izolowanej), z publicznym punktem końcowym z dostępem do Internetu. Zezwala na ruch tylko z określonej podsieci w ramach Virtual Network i blokuje wszystko inne. Użyj ograniczeń dostępu, aby skonfigurować listy Access Control sieci (ograniczenia adresów IP), aby zablokować dozwolony ruch przychodzący.

Zdefiniuj priorytet wśród uporządkowanej listy dozwolonych lub zablokowanych, aby zarządzać dostępem do sieci do aplikacji. Ta lista może zawierać adresy IP lub podsieci Virtual Network. Niejawna reguła "Odmów wszystkim" istnieje na końcu listy, jeśli zawiera co najmniej jeden wpis. Ta funkcja współpracuje ze wszystkimi App Service hostowanymi obciążeniami roboczymi, takimi jak, Web Apps, API Apps, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje. 

Za pomocą punktów końcowych usługi Ogranicz dostęp do aplikacji sieci Web z poziomu usługi Azure Virtual Network. Ogranicz dostęp do App Service z wieloma dzierżawcami (aplikacja nie znajduje się w warstwie izolowanej) z wybranych podsieci z punktami końcowymi usługi. 

- [Azure App Service Static IP Restrictions](app-service-ip-restrictions.md) (Ograniczenia statycznych adresów IP usługi Azure App Service)

- [Zapora aplikacji sieci Web platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Jak skonfigurować zaporę aplikacji sieci Web (WAF) dla App Service Environment](environment/app-service-app-service-environment-web-application-firewall.md)

- [Zabezpiecz środowisko ASE zgodnie z opisem w temacie blokowanie App Service Environment](environment/firewall-integration.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: służy do monitorowania żądań i odpowiedzi wysyłanych do i z aplikacji App Service z Security Center. Ataki na aplikacje sieci Web mogą być monitorowane przy użyciu Application Gateway w czasie rzeczywistym, który ma zaporę aplikacji sieci Web, z obsługą rejestrowania zintegrowanego z Azure Monitor do śledzenia alertów zapory aplikacji sieci Web i łatwego monitorowania trendów.

- [Zapora aplikacji sieci Web platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: zarządzanie ruchem dla aplikacji w App Service Environment:

- Zabezpiecz App Service Environment zgodnie z opisem w temacie blokowanie App Service Environment
- Wdróż Application Gateway, która ma zaporę aplikacji sieci Web platformy Azure przed aplikacjami internetowymi
- Ustaw aplikację tak, aby była dostępna tylko za pośrednictwem protokołu HTTPS

Zarządzanie ruchem dla aplikacji z dostępem do Internetu w App Service z wieloma dzierżawcami (nie w warstwie izolowanej): 

- Wdróż Application Gateway, która ma włączoną zaporę aplikacji sieci Web platformy Azure przed aplikacjami internetowymi
- Użyj ograniczeń dostępu lub punktów końcowych usługi, aby zabezpieczyć ruch przychodzący do zapory aplikacji sieci Web. Funkcja ograniczeń dostępu współpracuje ze wszystkimi App Service hostowanymi obciążeniami roboczymi, takimi jak Web Apps, API Apps, aplikacje dla systemu Linux, aplikacje kontenera systemu Linux i funkcje.

- Ustaw aplikację jako dostępną tylko przy użyciu protokołu HTTPS
- Ogranicz dostęp do aplikacji App Service ze statycznymi ograniczeniami adresów IP, aby tylko odbierał ruch z adresu VIP w bramie aplikacji jako jedyny adres z dostępem.

Przejrzyj linki, do których istnieją odwołania, aby uzyskać dodatkowe informacje.

- [Azure App Service Static IP Restrictions](app-service-ip-restrictions.md) (Ograniczenia statycznych adresów IP usługi Azure App Service)

- [Zapora aplikacji sieci Web platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Jak skonfigurować kompleksową metodę TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpiecz środowisko ASE zgodnie z opisem w temacie blokowanie App Service](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: App Service ma wiele punktów końcowych, które są używane do zarządzania usługą. Te adresy punktów końcowych są również zawarte w tagu usługi AppServiceManagement IP. Tag AppServiceManagement jest używany tylko z App Service Environment, aby zezwalać na ten ruch. 

Możesz zezwolić na ruch dla odpowiedniej usługi lub go odmówić, określając nazwę tagu usługi w odpowiednim polu źródłowym lub docelowym reguły. Adresy przychodzące App Service są śledzone w tagu usługi IP AppService. Nie istnieje tag usługi IP zawierający adresy wychodzące używane przez App Service.

Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z App Service aplikacjami. 

Należy zachować konfiguracje zabezpieczeń, używając aliasów Azure Policy w przestrzeniach nazw "Microsoft. Web" i "Microsoft. Network". Utwórz zasady niestandardowe w celu inspekcji lub wymuszania konfiguracji sieciowej aplikacji App Service. 

Użyj wbudowanych definicji zasad dla App Service, takich jak:
- Aplikacja powinna używać punktu końcowego usługi sieci wirtualnej
- Aplikacja powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Ustawianie minimalnej wersji protokołu TLS na bieżącą wersję

Przejrzyj linki, do których istnieją odwołania, aby uzyskać dodatkowe informacje.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak skonfigurować kompleksową metodę TLS przy użyciu Application Gateway z portalem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpiecz środowisko ASE zgodnie z opisem w temacie blokowanie App Service](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla sieciowych grup zabezpieczeń i innych powiązanych zasobów, w tym przepływu ruchu w App Service.

Określ potrzebę biznesową, czas trwania itd., z uwzględnieniem pola "opis" dla reguł, które zezwalają na ruch do lub z sieci dla indywidualnych reguł grup zabezpieczeń sieci.

Zastosuj dowolne wbudowane definicje Azure Policy związane ze skutkami tagowania, na przykład "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o wszelkich istniejących nieoznakowanych zasobach. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

- [Ograniczenia dostępu Azure App Service](/azure/app-service/app-service-ip-restriction)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian ustawień sieciowych oraz wszelkich zasobów związanych z App Service. 

Zastosuj jedną z kilku Azure Policy wbudowanych definicji dla App Service, takich jak zasady, które przeprowadzają inspekcję aplikacji do używania usługi punktu końcowego sieci wirtualnej. Utwórz alerty w Azure Monitor, aby wyzwolić zmiany w krytycznych ustawieniach sieciowych lub zasobach. 

Przejrzyj szczegółowe alerty zabezpieczeń i zalecenia w Security Center w portalu lub za pomocą narzędzi programistycznych. Wyeksportuj te informacje lub wyślij je do innych narzędzi do monitorowania w danym środowisku. Narzędzia są dostępne do eksportowania alertów i zaleceń ręcznie lub w stały i ciągły sposób. Za pomocą tych narzędzi możesz:
 
- Ciągle Eksportuj do obszaru roboczego Log Analytics
- Ciągle Eksportuj do Event Hubs platformy Azure (w przypadku integracji z usługą rozwiązań Siem innej firmy)
- Eksportuj do pliku CSV (jednorazowo)

Zaleca się utworzenie procesu z zautomatyzowanymi narzędziami do monitorowania konfiguracji zasobów sieciowych i szybkiego wykrywania zmian.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Eksportowanie alertów zabezpieczeń i zaleceń](../security-center/continuous-export.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Zintegruj App Service Environment (ASE) z Azure monitor, aby wysyłać dzienniki do usługi Azure Storage, Azure Event Hubs lub log Analytics. Włącz ustawienia diagnostyczne dziennika aktywności platformy Azure dla rejestrowania inspekcji płaszczyzny kontroli. Alerty zabezpieczeń z Security Center są publikowane w dzienniku aktywności platformy Azure. Inspekcja danych dziennika aktywności platformy Azure, które umożliwiają określenie "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla Azure App Service i innych zasobów platformy Azure. Zapisz zapytania do użycia w przyszłości, Przypnij wyniki zapytania do pulpitów nawigacyjnych platformy Azure i Utwórz alerty dzienników. Należy również użyć interfejsu API REST dostępu do danych w Application Insights, aby programowo uzyskać dostęp do telemetrii.

Użyj Microsoft Azure wskaźnikiem, skalowalnej, natywnej w chmurze usługi zarządzania zdarzeniami zabezpieczeń (SIEM) dostępnego do łączenia się z różnymi źródłami danych i łącznikami, zgodnie z wymaganiami biznesowymi. Możesz również włączyć i dołączyć dane do systemu zarządzania zdarzeniami zabezpieczeń (SIEM) innych firm, takich jak Barracuda w portalu Azure Marketplace.

- [Rejestrowanie aktywności ASE](environment/using-an-ase.md#logging)

- [Jak włączyć ustawienia diagnostyczne dla Azure App Service](troubleshoot-diagnostic-logs.md)

- [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Eksportowanie telemetrii z usługi Application Insights](../azure-monitor/app/export-telemetry.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure dla inspekcji płaszczyzny kontroli rejestrowanie App Service. Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage.
"Co, kto i kiedy" dla każdej operacji zapisu (PUT, POST, DELETE) wykonanej na poziomie płaszczyzny kontroli można określić przy użyciu danych dziennika aktywności platformy Azure dla App Service i innych zasobów platformy Azure.

Ponadto Azure Key Vault zapewnia scentralizowane zarządzanie kluczami tajnymi przy użyciu zasad dostępu i historii inspekcji. 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Jak włączyć ustawienia diagnostyczne dla Azure App Service](troubleshoot-diagnostic-logs.md)

- [Operacje Menedżer zasobów](../role-based-access-control/resource-provider-operations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor ustawić okres przechowywania dziennika dla log Analytics obszarów roboczych skojarzonych z zasobami App Service zgodnie z regulacjami zgodności w organizacji.
- [Jak ustawić parametry przechowywania dziennika](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: Przegląd ustawień diagnostycznych dzienników aktywności platformy Azure w swoich zasobach App Service z użyciem dzienników wysyłanych do log Analytics obszaru roboczego. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych.

Używaj Application Insights aplikacji App Service i zbierać dane dotyczące dzienników, wydajności i błędów. Wyświetlanie danych telemetrycznych zebranych przez Application Insights w Azure Portal.

Jeśli wdrożono zaporę aplikacji sieci Web (WAF), można monitorować ataki do aplikacji App Service przy użyciu dziennika zapory aplikacji sieci Web w czasie rzeczywistym. Dziennik jest zintegrowany z Azure Monitor do śledzenia alertów zapory aplikacji sieci Web i łatwego monitorowania trendów.

Skorzystaj z platformy Azure, skalowalnej i natywnej w chmurze zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM), aby zintegrować z różnymi źródłami danych i łącznikami zgodnie z wymaganiami. Opcjonalnie możesz włączyć i dołączyć dane do rozwiązania do zarządzania zdarzeniami dotyczącymi zabezpieczeń innych firm w witrynie Azure Marketplace.

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Jak włączyć Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Jak zintegrować App Service Environment z usługą Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Konfigurowanie Security Center w ramach subskrypcji platformy Azure i przeglądanie wygenerowanych alertów. Użyj Azure Monitor, aby uzyskać dane dziennika aktywności do centrum zdarzeń, które można odczytać za pomocą rozwiązania do zarządzania zdarzeniami zabezpieczeń (SIEM), na przykład platformy Azure. 

Monitoruj ataki na App Service aplikacje, używając dziennika zapory aplikacji sieci Web w czasie rzeczywistym za pomocą wdrożonej zapory aplikacji sieci Web platformy Azure (WAF). Dziennik jest zintegrowany z Azure Monitor do śledzenia alertów zapory aplikacji sieci Web (WAF) i łatwego monitorowania trendów.

- [Jak zintegrować App Service Environment z usługą Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Eksportowanie alertów zabezpieczeń i zaleceń](../security-center/continuous-export.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Azure Active Directory (Azure AD) ma wbudowane role, które muszą zostać jawnie przypisane i mogą być dostępne zapytania. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak używać tożsamości zarządzanych do App Service i Azure Functions](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Zapewnia płaszczyznę kontroli dostęp do App Service.

Ogólnie rzecz biorąc, unikaj implementowania domyślnych haseł dla dostępu użytkowników podczas tworzenia własnych aplikacji. Użyj jednego z dostawców tożsamości dostępnych domyślnie dla App Service, takich jak Azure AD, konto Microsoft, Facebook, Google lub Twitter.

Wyłącz dostęp anonimowy, chyba że potrzebujesz go obsługiwać. 

- [Dostawcy tożsamości są dostępni domyślnie w Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Uwierzytelnianie i autoryzacja w Azure App Service i Azure Functions](overview-authentication-authorization.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Korzystając z funkcji zarządzania tożsamościami i dostępem w Security Center, można monitorować i śledzić liczbę kont administracyjnych. 

Użyj zaleceń z Security Center lub wbudowanych zasad platformy Azure, takich jak:

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel. 
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

Utwórz proces monitorowania konfiguracji zasobów sieciowych i Wykryj zmiany na kontach administracyjnych.

- [Jak używać Azure Security Center do monitorowania tożsamości i dostępu](../security-center/security-center-identity-access.md)

- [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dowiedz się więcej o udzielaniu użytkownikom dostępu do aplikacji](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: uwierzytelnianie App Service za pomocą Azure Active Directory (Azure AD). Zapewnia ona usługę OAuth 2,0 dla dostawcy tożsamości i umożliwia autoryzowany dostęp do aplikacji mobilnych i sieci Web. 

Aplikacje App Service używają tożsamości federacyjnej, w której dostawca tożsamości innej firmy zarządza tożsamościami użytkowników i przepływem uwierzytelniania. Te dostawcy tożsamości są domyślnie dostępne:

- Azure AD
- Konto Microsoft

- Facebook

- Google

- Twitter

Po włączeniu uwierzytelniania i autoryzacji jednego z tych dostawców, jego punkt końcowy logowania jest dostępny do uwierzytelnienia użytkownika i weryfikacji tokenów uwierzytelniania od dostawcy.

- [Informacje o uwierzytelnianiu i autoryzacji w Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Informacje o uwierzytelnianiu i autoryzacji w Azure App Service](overview-authentication-authorization.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włącz funkcję uwierzytelniania wieloskładnikowego w Azure Active Directory (Azure AD) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem w programie Security Center.

Zaimplementuj uwierzytelnianie wieloskładnikowe dla usługi Azure AD. Administratorzy muszą upewnić się, że konta subskrypcji w portalu są chronione. Subskrypcja jest narażona na ataki, ponieważ zarządza utworzonymi przez siebie zasobami. 

- [Azure Security MFA](/azure/security/develop/secure-aad-app)

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Użyj PRIVILEGED Identity Management (PIM) w usłudze Azure Active Directory (Azure AD) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

Ochrona przed zagrożeniami w programie Security Center zapewnia kompleksową ochronę środowiska, która obejmuje ochronę przed zagrożeniami dla zasobów obliczeniowych platformy Azure, takich jak maszyny z systemem Windows, maszyny z systemami Linux, App Service i kontenery platformy Azure.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Ochrona przed zagrożeniami dla zasobów obliczeniowych platformy Azure](/azure/security-center/threat-protection)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów, krajów lub regionów adresów IP.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji App Service. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, a także Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak skonfigurować aplikacje Azure App Service do korzystania z usługi Azure AD login](configure-authentication-provider-aad.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: wykrywanie starych kont przy użyciu dzienników dostarczonych przez Azure Active Directory (Azure AD). Za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach i dostępem do aplikacji dla przedsiębiorstw, a także przypisywania ról. Przejrzyj okresy dostępu użytkowników, aby upewnić się, że tylko zaznaczeni użytkownicy mają stały dostęp. 

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji App Service. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych, sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Dostęp do źródeł danych dotyczących działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem związanym z dziennikami zdarzeń można zintegrować z platformą Azure — rozwiązanie do zarządzania zdarzeniami (SIEM). Usprawnij proces, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając inspekcję i zaloguj dzienniki do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

- [Jak skonfigurować aplikacje Azure App Service do korzystania z usługi Azure AD login](configure-authentication-provider-aad.md)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji dla aplikacji App Service. 

Użyj Azure AD Identity Protection, aby skonfigurować automatyczne reagowanie na wykryte podejrzane działania związane z tożsamościami użytkowników, takie jak odróżnienie zachowania logowania do konta na płaszczyźnie kontroli z Azure Portal. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy. 

- [Jak skonfigurować aplikację Azure App Service do korzystania z logowania za pomocą usługi Azure AD](configure-authentication-provider-aad.md)

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: niedostępne dla App Service. Skrytka klienta nie jest obsługiwana w przypadku Azure App Service.

- [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów App Service, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: dla App Service Environment, zaimplementować osobne subskrypcje, grupy zarządzania lub oba te elementy, dla środowisk programistycznych, testowych i produkcyjnych. Można izolować aplikacje, które przetwarzają informacje poufne z innych aplikacji w taki sam sposób. Wdróż aplikację App Service w Virtual Network. Aby zapewnić lepszą izolację aplikacji, należy użyć sieciowych grup zabezpieczeń i podsieci. 

Istnieją dwa typy wdrożeń dla środowiska App Serviceowego (ASE). Oba umożliwiają izolację ruchu na podstawie wymagań firmy.

- Środowisko usługi aplikacji zewnętrznych — udostępnia App Service Environment aplikacje hostowane na adresie IP dostępnym z Internetu.

-  Środowisko usługi aplikacji wewnętrznego modułu równoważenia obciążenia (ILB) — udostępnia App Service Environment aplikacje hostowane na adresie IP w Virtual Network. Wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB), dlatego jest on nazywany ILB ASE. 

W przypadku App Service wielodostępnego (aplikacji spoza warstwy izolowanej) Użyj integracji Virtual Network, aby uzyskać dostęp do zasobów w sieci wirtualnej.  Użyj dostępu do lokacji prywatnej, aby udostępnić aplikację tylko z sieci prywatnej, na przykład z jednej z sieci wirtualnej platformy Azure. Integracja Virtual Network służy tylko do wykonywania wywołań wychodzących z aplikacji do Virtual Network. Funkcja integracji Virtual Network działa inaczej, gdy jest używana z siecią wirtualną w tym samym regionie i w sieciach wirtualnych w innych regionach. 
 
- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Jak utworzyć zewnętrzny środowisko ASE](environment/create-external-ase.md)

- [Jak utworzyć wewnętrzny ASE](environment/create-ilb-ase.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku, gdy funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla App Service, można zmniejszyć ryzyko związane z eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły, w których miejsce docelowe używa znacznika "tag" w przypadku usług internetowych lub Azure. 

Firma Microsoft zarządza podstawową infrastrukturą dla App Service i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Użyj domyślnej minimalnej wersji protokołu TLS 1,2 skonfigurowanej w ustawieniach protokołu TLS/SSL, aby szyfrować wszystkie przesyłane informacje. Upewnij się również, że wszystkie żądania połączenia HTTP są przekierowywane do protokołu HTTPS.

- [Informacje o szyfrowaniu podczas przesyłania aplikacji internetowych Azure App Service](security-recommendations.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: aktualnie niedostępne. Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla App Service. 

Tagi App Service aplikacje, które mogą przetwarzać informacje poufne. Zaimplementuj rozwiązanie innych firm, jeśli jest to niezbędne do celów zgodności.

Firma Microsoft zarządza podstawową platformą i traktuje wszystkie dane klientów jako poufne i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: udostępnione

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Funkcja kontroli dostępu opartej na ROLACH (RBAC) w Azure Active Directory (Azure AD) umożliwia kontrolowanie dostępu do App Service płaszczyzny kontroli w Azure Portal.

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: zawartość witryny sieci Web w aplikacji App Service, na przykład pliki, są przechowywane w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku. Wybierz opcję przechowywania wpisów tajnych aplikacji w Key Vault i Pobierz je w czasie wykonywania.

Klucze tajne dostarczone przez klienta są szyfrowane w stanie spoczynku, natomiast są przechowywane w App Service bazach danych konfiguracji.

Należy pamiętać, że w przypadku, gdy lokalnie dołączone dyski mogą być używane opcjonalnie przez witryny sieci Web jako magazyn tymczasowy (na przykład D:\Local i% TMP%), nie są szyfrowane w stanie spoczynku.

- [Informacje o kontrolkach ochrony danych Azure App Service](app-service-security-controls.md)

- [Informacje o szyfrowaniu usługi Azure Storage w spoczynku](../storage/common/storage-service-encryption.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące wszelkich zmian w środowisku produkcyjnym App Service aplikacje i inne krytyczne lub pokrewne zasoby.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: należy wdrożyć DevSecOpsą, aby upewnić się, że aplikacje App Service są bezpieczne i pozostają zabezpieczone przez cały czas trwania cyklu życia. Firma DevSecOps jest członkiem zespołu ds. zabezpieczeń w organizacji i ich możliwościami w praktyce DevOps, co pozwala na zapewnienie bezpieczeństwa wszystkim członkom zespołu.

Przejrzyj i postępuj zgodnie z zaleceniami Security Center na potrzeby zabezpieczania aplikacji App Service.

- [Jak dodać ciągłą weryfikację zabezpieczeń do potoku CI/CD](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&amp;preserve-view=true)

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują App Service. Można jednak użyć ważności zaleceń w ramach Security Center, a także bezpiecznego wyniku mierzenia ryzyka w danym środowisku. Twój bezpieczny wynik jest oparty na liczbie Security Center zaleceń, które zostały przez Ciebie skorygowane. Aby określić priorytety rekomendacji do rozwiązania, należy wziąć pod uwagę ważność każdego z nich.

- [Przewodnik referencyjny dotyczący zaleceń dotyczących zabezpieczeń](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty, protokoły itd.) w ramach subskrypcji. Upewnij się, że w dzierżawie są stosowane odpowiednie uprawnienia i możesz wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure przy użyciu metadanych do logicznego organizowania ich w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: w razie potrzeby można organizować i śledzić zasoby platformy Azure za pomocą tagowania, grup zarządzania i oddzielnych subskrypcji. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w ramach tego procesu.

Wybierz Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Przejrzyj linki, do których istnieją odwołania, aby uzyskać dodatkowe informacje.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania dla zasobów obliczeniowych na podstawie potrzeb organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania zasobów w ramach ich subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone. 

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań lub odnajdywania zasobów w ramach subskrypcji oraz upewnienia się, że odnalezione zasoby platformy Azure są zatwierdzone na podstawie zasad organizacji.

Używanie zadań WebJob w App Service do monitorowania niezatwierdzonych aplikacji, które są wdrażane w ramach zasobów obliczeniowych. Używanie zadań WebJob do uruchamiania programu lub skryptu w tym samym wystąpieniu co aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna. Zdefiniuj konfiguracje zadań WebJob i monitoruj je za pomocą dzienników. Na stronie Szczegóły przebiegu Zadania WebJob wybierz pozycję Przełącz dane wyjściowe, aby zobaczyć tekst zawartości dziennika. Należy pamiętać, że Zadania WebJob nie są jeszcze obsługiwane dla App Service w systemie Linux.

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

- [Samouczek — Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki Start — uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone. Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji. Usuń wszystkie wdrożone aplikacje, które nie zostały zatwierdzone zgodnie z zasadami organizacji.

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

- [Samouczek — Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki Start — uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone. Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji. Usuń wszystkie wdrożone aplikacje, które nie zostały zatwierdzone zgodnie z zasadami organizacji. 

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

- [Samouczek — Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Szybki Start — uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Utwórz proces, aby okresowo przeglądać nieautoryzowane usługi platformy Azure w celu zapewnienia, że tylko autoryzowane usługi platformy Azure są używane w Twoich subskrypcjach.

Użyj grafu zasobów platformy Azure w ramach tego procesu, aby badać lub odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Skonfiguruj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Użyj zadań WebJob w App Service, aby monitorować niezatwierdzone aplikacje wdrożone w ramach zasobów komputerowych. Używanie zadań WebJob do uruchamiania programu lub skryptu w tym samym wystąpieniu co aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna. Zdefiniuj konfiguracje zadań WebJob i monitoruj je za pomocą dzienników. Na stronie Szczegóły przebiegu Zadania WebJob wybierz pozycję Przełącz dane wyjściowe, aby zobaczyć tekst zawartości dziennika. Należy pamiętać, że Zadania WebJob nie są jeszcze obsługiwane dla App Service w systemie Linux.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: Zaimplementuj proces do spisu i przejrzyj tytuły oprogramowania w Twoich subskrypcjach okresowo, aby upewnić się, że tylko autoryzowane usługi platformy Azure są używane w Twoich subskrypcjach.

Użyj grafu zasobów platformy Azure w ramach tego procesu, aby wykonywać zapytania lub odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure wykryte w środowisku zostały zatwierdzone.

Skonfiguruj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji klienta przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Podobnie należy używać zadań WebJob w App Service do spisu niezatwierdzonych aplikacji oprogramowania wdrożonych w ramach zasobów komputerowych. Zdefiniuj ich konfigurację i monitorowanie za pomocą dzienników. Na stronie Szczegóły przebiegu Zadania WebJob wybierz pozycję Przełącz dane wyjściowe, aby zobaczyć tekst zawartości dziennika. Należy pamiętać, że Zadania WebJob nie są jeszcze obsługiwane dla App Service w systemie Linux.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Konfigurowanie dostępu warunkowego platformy Azure w celu ograniczenia możliwości współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: zadania webjob w App Service umożliwiają klientom Uruchamianie programu lub skryptu w tym samym wystąpieniu, w którym znajduje się aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna. Użytkownik jest odpowiedzialny za Definiowanie konfiguracji w celu ograniczenia lub ograniczenia skryptów, które nie są dozwolone przez organizację. App Service nie zapewnia mechanizmu, aby ograniczyć wykonywanie skryptu natywnie. Należy pamiętać, że Zadania WebJob nie są jeszcze obsługiwane dla App Service w systemie Linux.

- [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: Zaimplementuj osobne subskrypcje lub grupy zarządzania, aby zapewnić izolację App Service aplikacji o wysokim ryzyku. Wdróż aplikację o podwyższonym ryzyku we własnym Virtual Network, ponieważ zabezpieczenia obwodów w App Service są realizowane za pomocą sieci wirtualnych. App Service Environment to wdrożenie App Service w podsieci w Virtual Network platformy Azure. 

Istnieją dwa typy środowiska usługi aplikacji, zewnętrzne środowisko usługi aplikacji i ILB (wewnętrzne Load Balancer) środowisko usługi aplikacji. Wybierz najlepszą architekturę opartą na Twoich wymaganiach.

- [Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment)](environment/network-info.md)

- [Tworzenie zewnętrznego środowiska App Service](environment/create-external-ase.md)

- [Tworzenie i używanie wewnętrznego Load Balancer App Service Environment](environment/create-ilb-ase.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla App Service wdrożonych aplikacji z Azure Policy.

Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji App Service Web Apps.

Zastosuj wbudowane definicje zasad, takie jak:
- App Service powinien używać punktu końcowego usługi sieci wirtualnej
- Aplikacje sieci Web powinny być dostępne tylko za pośrednictwem protokołu HTTPS

- Korzystanie z najnowszej wersji protokołu TLS w aplikacjach

Zaleca się udokumentowanie procesu, aby zastosować wbudowane definicje zasad dla standardowego użycia.   

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje] efektów, aby wymusić bezpieczne ustawienia dla aplikacji Azure App Service.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: wybierz pozycję Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim przy użyciu definicji Azure Policy niestandardowych.

Użyj istniejącego potoku ciągłej integracji i ciągłego dostarczania, aby wdrożyć znaną bezpieczną konfigurację.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Opracowywanie procesu i potoku w celu zarządzania wyjątkami zasad.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy, a także aliasów Azure Policy w przestrzeni nazw "Microsoft. Web", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. 

Zastosuj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], powoduje automatyczne wymuszanie konfiguracji zasobów platformy Azure.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić aplikacjom App Service automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają aplikacjom uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie. Upewnij się, że w Azure Key Vault jest włączona funkcja usuwania nietrwałego.

- [Jak włączyć usuwanie nietrwałe w Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

- [Jak używać tożsamości zarządzanych do App Service](overview-managed-identity.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/managed-identity)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamości zarządzanych, aby zapewnić aplikacjom wdrożonym w App Service aplikacje z automatyczną tożsamością zarządzaną w usłudze Azure Active Directory (Azure AD). Zarządzane tożsamości umożliwiają aplikacjom uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak używać tożsamości zarządzanych do App Service](overview-managed-identity.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](/azure/key-vault/managed-identity)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: funkcja tworzenia kopii zapasowych i przywracania w App Service umożliwia łatwe tworzenie kopii zapasowych aplikacji ręcznie lub zgodnie z harmonogramem. Kopie zapasowe można skonfigurować tak, aby były przechowywane przez czas nieokreślony. Możesz przywrócić aplikację do migawki poprzedniego stanu, zastępując istniejącą aplikację lub przywracając ją do innej aplikacji.

App Service może utworzyć kopię zapasową następujących informacji na koncie usługi Azure Storage i kontenerze, dla którego skonfigurowano aplikację do użycia:
- Konfiguracja aplikacji
- Zawartość pliku
- Baza danych połączona z aplikacją

Upewnij się, że regularne i zautomatyzowane kopie zapasowe są wykonywane z częstotliwością zdefiniowaną przez zasady organizacyjne.

- [Informacje o możliwościach tworzenia kopii zapasowych Azure App Service](manage-backup.md)

- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-overview?toc=/azure/storage/blobs/toc.json)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Użyj funkcji tworzenia kopii zapasowych i przywracania App Service, aby utworzyć kopię zapasową aplikacji. Funkcja tworzenia kopii zapasowych wymaga konta usługi Azure Storage do przechowywania informacji o kopii zapasowej aplikacji.

- Usługa Azure Storage zapewnia szyfrowanie przy użyciu kluczy dostarczonych przez system lub własnych kluczy zarządzanych przez klienta. Jest to miejsce, w którym dane aplikacji są przechowywane, gdy nie są uruchomione w aplikacji sieci Web na platformie Azure.
- Uruchamianie z pakietu wdrożeniowego jest funkcją wdrażania App Service. Dzięki temu można wdrożyć zawartość witryny na koncie usługi Azure Storage przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).

- Odwołania Key Vault są funkcją zabezpieczeń App Service. Umożliwia importowanie wpisów tajnych w środowisku uruchomieniowym jako ustawienia aplikacji. Służy do szyfrowania adresu URL sygnatury dostępu współdzielonego konta usługi Azure Storage.

Więcej informacji można znaleźć w łączach, do których istnieją odwołania.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)

- [Przywracanie aplikacji uruchomionej w Azure App Service](web-sites-restore.md)

- [Informacje o szyfrowaniu na platformie Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Model szyfrowania i tabela zarządzania kluczami](../security/fundamentals/encryption-atrest.md)

- [Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta](configure-encrypt-at-rest-using-cmk.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe testowanie procesu przywracania dla wszystkich kopii zapasowych aplikacji App Service.

- [Tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)

- [Jak przywrócić aplikację sieci Web Azure App Service](web-sites-restore.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: kopie zapasowe App Service są przechowywane w ramach konta usługi Azure Storage. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym kont magazynu Menedżer zasobów i klasycznych. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Domyślnie dane na koncie magazynu są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania danych. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy. Upewnij się, że w Azure Key Vault jest włączona funkcja usuwania nietrwałego.

- [Informacje o szyfrowaniu usługi Azure Storage w przypadku przechowywania danych](../storage/common/storage-service-encryption.md)

- [Jak włączyć usuwanie nietrwałe w Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Ponadto wyraźnie oznaczaj subskrypcje (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń do testowania możliwości odpowiedzi na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z przewodnikiem po publikacji NIST, aby testować, uczenie i ćwiczenie programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Użyj łącznika danych Security Center do przesyłania strumieniowego z alertów jako dla potrzeb firmy.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Aby uzyskać więcej informacji na temat strategii i sposobu wykonywania czerwonych zespołów i testowania platformy Microsoft na żywo w witrynie, usług i aplikacjach chmurowych zarządzanych przez firmę Microsoft.

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
