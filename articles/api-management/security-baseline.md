---
title: Podstawa zabezpieczeń platformy Azure dla API Management
description: Podstawa zabezpieczeń platformy Azure dla API Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4db7727558d0b7f4c26aba3ffa823bebb07862f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713777"
---
# <a name="azure-security-baseline-for-api-management"></a>Podstawa zabezpieczeń platformy Azure dla API Management

Podstawą zabezpieczeń platformy Azure dla API Management są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network

**Wskazówki**: usługa Azure API Management może zostać wdrożona w ramach usługi Azure Virtual Network (VNET), dzięki czemu można uzyskać dostęp do usług zaplecza w sieci. Portal dla deweloperów i bramę API Management można skonfigurować tak, aby były dostępne z Internetu (zewnętrzna) lub tylko w obrębie sieci wirtualnej (wewnętrznej).
- Zewnętrzny: Brama API Management i Portal dla deweloperów są dostępni z publicznej sieci Internet za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.
- Wewnętrzne: Brama API Management i Portal dla deweloperów są dostępni tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

Ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management można kontrolować przy użyciu sieciowej grupy zabezpieczeń.

* [Używanie usługi Azure API Management z sieciami wirtualnymi](./api-management-using-with-vnet.md)

* [Korzystanie z usługi Azure API Management z wewnętrzną siecią wirtualną](./api-management-using-with-internal-vnet.md)

* [Integrowanie API Management w wewnętrznej sieci wirtualnej z Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management można kontrolować za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Wdróż sieciowej grupy zabezpieczeń w podsieci API Management i Włącz dzienniki przepływów sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Przestroga: podczas konfigurowania sieciowej grupy zabezpieczeń w podsieci API Management istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management może nie działać prawidłowo i może stać się niedostępna.

* [Poznaj konfiguracje sieciowej grupy zabezpieczeń dla platformy Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: aby chronić krytyczne interfejsy API sieci Web/http, skonfiguruj API Management w ramach Virtual Network (VNET) w trybie wewnętrznym i skonfiguruj Application Gateway platformy Azure. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i udostępnia Równoważenie obciążenia, routing, zaporę aplikacji sieci Web (WAF) i inne usługi.

Łączenie API Management obsługiwane w wewnętrznej sieci wirtualnej przy użyciu Application Gateway frontonu umożliwia realizację następujących scenariuszy:
- Użyj pojedynczego zasobu API Management, aby uwidocznić wszystkie interfejsy API zarówno dla użytkowników wewnętrznych, jak i dla użytkowników zewnętrznych.
- Użyj pojedynczego zasobu API Management, aby uwidocznić podzestaw interfejsów API dla użytkowników zewnętrznych.
- Możliwość przełączenia dostępu do API Management z publicznej sieci Internet.

Uwaga: Ta funkcja jest dostępna w warstwach Premium i Developer API Management.

* [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Omówienie usługi Azure Application Gateway](../application-gateway/index.yml)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Konfigurowanie API Management w ramach Virtual Network (VNET) w trybie wewnętrznym i Konfigurowanie Application Gateway platformy Azure. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i udostępnia Równoważenie obciążenia, routing, zaporę aplikacji sieci Web (WAF) i inne usługi.

Łączenie API Management obsługiwane w wewnętrznej sieci wirtualnej przy użyciu Application Gateway frontonu umożliwia realizację następujących scenariuszy:
- Użyj pojedynczego zasobu API Management, aby uwidocznić wszystkie interfejsy API zarówno dla użytkowników wewnętrznych, jak i dla użytkowników zewnętrznych.
- Użyj pojedynczego zasobu API Management, aby uwidocznić podzestaw interfejsów API dla użytkowników zewnętrznych.
- Możliwość przełączenia dostępu do API Management z publicznej sieci Internet.

Uwaga: Ta funkcja jest dostępna w warstwach Premium i Developer API Management.

Włącz Standard Azure DDoS Protection w sieci wirtualnej skojarzonej z wdrożeniem API Management, aby chronić przed atakami rozproszonymi typu "odmowa usługi" (DDoS).

Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

* [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Omówienie usługi Azure Application Gateway](../application-gateway/index.yml)

* [Jak skonfigurować Azure DDoS Protection Standard](../virtual-network/manage-ddos-protection.md)

* [Opis Azure Security Center zintegrowanej analizy zagrożeń](../security-center/threat-protection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: rejestrowanie pakietów sieciowych i dzienników przepływów

**Wskazówki**: ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management można kontrolować za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Wdróż sieciowej grupy zabezpieczeń w podsieci API Management i Włącz dzienniki przepływów sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

Przestroga: podczas konfigurowania sieciowej grupy zabezpieczeń w podsieci API Management istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management może nie działać prawidłowo i może stać się niedostępna.

* [Poznaj konfiguracje sieciowej grupy zabezpieczeń dla platformy Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Wdróż systemy zapobiegania wykrywaniu dostępu do sieci/dostępu intruzów (identyfikatory/adresy IP)

**Wskazówki**: Konfigurowanie API Management w ramach Virtual Network (VNET) w trybie wewnętrznym i Konfigurowanie Application Gateway platformy Azure. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i udostępnia Równoważenie obciążenia, routing, zaporę aplikacji sieci Web (WAF) i inne usługi. Application Gateway WAF zapewnia ochronę przed typowymi atakami i lukami w zabezpieczeniach i może działać w następujących dwóch trybach:
- Tryb wykrywania: służy do monitorowania i rejestrowania wszystkich alertów dotyczących zagrożeń. Można włączyć diagnostykę rejestrowania dla Application Gateway w sekcji Diagnostyka. Musisz się upewnić, że dziennik WAF jest wybrany i włączony. Zapora aplikacji sieci Web nie blokuje żądań przychodzących, gdy działa w trybie wykrywania.
- Tryb zapobiegania: blokuje wtargnięcie i ataki wykrywane przez zasady. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie jest zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

Łączenie API Management obsługiwane w wewnętrznej sieci wirtualnej przy użyciu Application Gateway frontonu umożliwia realizację następujących scenariuszy:
- Użyj pojedynczego zasobu API Management, aby uwidocznić wszystkie interfejsy API zarówno dla użytkowników wewnętrznych, jak i dla użytkowników zewnętrznych.
- Użyj pojedynczego zasobu API Management, aby uwidocznić podzestaw interfejsów API dla użytkowników zewnętrznych.
- Możliwość przełączenia dostępu do API Management z publicznej sieci Internet.

Uwaga: Ta funkcja jest dostępna w warstwach Premium i Developer API Management.

* [Jak zintegrować API Management w wewnętrznej sieci wirtualnej z Application Gateway](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Omówienie usługi Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: Aby zarządzać ruchem skierowanym do interfejsów API sieci Web/HTTP Wdróż API Management do Virtual Network (VNET) skojarzonych z App Service Environment w trybie zewnętrznym lub wewnętrznym.

W trybie wewnętrznym Skonfiguruj Application Gateway platformy Azure przed API Management. Application Gateway to usługa PaaS. Działa jako zwrotny serwer proxy i udostępnia Równoważenie obciążenia, routing, zaporę aplikacji sieci Web (WAF) i inne usługi. Application Gateway WAF zapewnia ochronę przed typowymi atakami i lukami w zabezpieczeniach.

Łączenie API Management obsługiwane w wewnętrznej sieci wirtualnej przy użyciu Application Gateway frontonu umożliwia realizację następujących scenariuszy:
- Użyj pojedynczego zasobu API Management, aby uwidocznić wszystkie interfejsy API zarówno dla użytkowników wewnętrznych, jak i dla użytkowników zewnętrznych.
- Użyj pojedynczego zasobu API Management, aby uwidocznić podzestaw interfejsów API dla użytkowników zewnętrznych.
- Możliwość przełączenia dostępu do API Management z publicznej sieci Internet.

Uwaga: Ta funkcja jest dostępna w warstwach Premium i Developer API Management.

* [Jak uwidaczniać prywatne interfejsy API klientom zewnętrznym](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Jak używać API Management w sieci wirtualnej](./api-management-using-with-vnet.md)

* [Zapora aplikacji sieci Web platformy Azure na platformie Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

* [Omówienie usługi Azure Application Gateway](../application-gateway/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi Virtual Network (VNET), aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci (sieciowych grup zabezpieczeń) używanych w API Management podsieciach. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Przestroga: podczas konfigurowania sieciowej grupy zabezpieczeń w podsieci API Management istnieje zestaw portów, które muszą być otwarte. Jeśli którykolwiek z tych portów jest niedostępny, API Management może nie działać prawidłowo i może stać się niedostępna.

* [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

* [Porty wymagane dla API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych związanych z wdrożeniami API Management platformy Azure. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. ApiManagement" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci dla wdrożeń API Management platformy Azure i powiązanych zasobów. Możesz również używać wbudowanych definicji zasad dla sieci wirtualnych platformy Azure, takich jak:
- Należy włączyć Standard DDoS Protection

Możesz również użyć planów platformy Azure, aby uprościć wdrożenia platformy Azure na dużą skalę przez pakowanie kluczowych artefaktów środowiska, takich jak szablony Azure Resource Manager, kontrola dostępu oparta na rolach (RBAC) na platformie Azure i zasady w ramach jednej definicji planu. Możesz łatwo zastosować plan do nowych subskrypcji, środowisk i dostrajania kontroli i zarządzania przy użyciu wersji.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń można użyć pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Jak utworzyć Virtual Network](../virtual-network/quick-create-portal.md)

* [Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych skojarzonych z wdrożeniami API Management platformy Azure. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla API Management platformy Azure.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: w Azure Monitor używać log Analytics obszarów roboczych do wysyłania zapytań i wykonywania analiz, wysyłania dzienników do usługi Azure Storage w celu przechowywania długoterminowego/archiwizowania oraz analizy w trybie offline lub eksportowania dzienników do innych rozwiązań analitycznych na platformie Azure i w innym miejscu przy użyciu usługi Azure Event Hubs. Usługa Azure API Management domyślnie rejestruje dzienniki i Azure Monitor metryki. Poziom szczegółowości rejestrowania można skonfigurować na podstawie usługi i poszczególnych interfejsów API.

Oprócz Azure Monitor można zintegrować platformę Azure API Management z jedną lub kilkoma usługami Application Insights platformy Azure. Ustawienia rejestrowania dla Application Insights można skonfigurować dla każdej usługi lub dla poszczególnych interfejsów API.

Opcjonalne, włączaj i dołączaj dane do usługi Azure wskaźnikowej lub zdarzenia zabezpieczeń innych firm (SIEM).

* [Jak skonfigurować ustawienia diagnostyczne](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

* [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Jak utworzyć niestandardowe rejestrowanie i potok analizy](./api-management-howto-log-event-hubs.md)

* [Jak przeprowadzić integrację z usługą Azure Application Insights](./api-management-howto-app-insights.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, włączania ustawień diagnostycznych dziennika aktywności platformy Azure i wysyłania dzienników aktywności do log Analyticsego obszaru roboczego na potrzeby raportowania i analizy do usługi Azure Storage w celu zapewnienia długoterminowego przechowywania w usłudze Azure Event Hubs do eksportowania w innych rozwiązaniach analitycznych na platformie Azure i w innym miejscu. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla usługi Azure API Management.

W przypadku rejestrowania inspekcji płaszczyzny danych dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji, a także do rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla API Management platformy Azure](./api-management-howto-use-azure-monitor.md#activity-logs)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w obszarze Azure monitor Ustaw okres przechowywania log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

* [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Jak zarchiwizować dzienniki na koncie usługi Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: usługa Azure API Management ciągle emituje dzienniki i metryki do Azure monitor, co pozwala na wgląd w informacje o stanie i kondycji interfejsów API. Za pomocą Azure Monitor i Log Analytics obszarów roboczych można przeglądać, wykonywać zapytania, wizualizować, kierować, archiwizować, konfigurować alerty i podejmować działania dotyczące metryk i dzienników pochodzących z API Management i powiązanych zasobów. Analizuj i monitoruj dzienniki pod kątem nietypowych zachowań i regularnie Przeglądaj wyniki.

Opcjonalnie możesz zintegrować API Management z platformą Azure Application Insights i używać jej jako podstawowego lub pomocniczego narzędzia do monitorowania, śledzenia, raportowania i alertów.

* [Jak monitorować i przeglądać dzienniki dla usługi Azure API Management](./api-management-howto-use-azure-monitor.md)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Informacje o obszarze roboczym Log Analytics](../azure-monitor/log-query/get-started-portal.md)

* [Jak przeprowadzić integrację z usługą Azure Application Insights](./api-management-howto-app-insights.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

**Wskazówki**: Włączanie ustawień diagnostycznych dziennika aktywności platformy Azure oraz ustawień diagnostycznych wystąpień API Management platformy Azure i wysyłanie dzienników do log Analytics obszaru roboczego. Wykonuj zapytania w Log Analytics, aby wyszukiwać terminy, identyfikować trendy, analizować wzorce i udostępniać wiele innych szczegółowych informacji na podstawie zebranych danych. Możesz tworzyć alerty oparte na zapytaniach obszaru roboczego Log Analytics.

Utwórz alerty metryk, aby poinformować Cię, gdy wystąpi coś nieoczekiwanego. Na przykład Otrzymuj powiadomienia, gdy wystąpienie usługi Azure API Management przekroczy oczekiwaną szczytową pojemność w określonym czasie, lub jeśli w wstępnie zdefiniowanym okresie wystąpi pewna liczba nieautoryzowanych żądań lub błędów bramy.

Opcjonalnie możesz zintegrować API Management z platformą Azure Application Insights i używać jej jako podstawowego lub pomocniczego narzędzia do monitorowania, śledzenia, raportowania i alertów.

Opcjonalnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

* [Jak włączyć ustawienia diagnostyczne dla API Management platformy Azure](./api-management-howto-use-azure-monitor.md#activity-logs)

* [Jak skonfigurować regułę alertu dla nieautoryzowanego żądania](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule-for-unauthorized-request)

* [Jak wyświetlić metryki pojemności wystąpienia usługi Azure API Management](./api-management-capacity.md)

* [Jak przeprowadzić integrację z usługą Azure Application Insights](./api-management-howto-app-insights.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Usługa Azure API Management nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Usługa Azure API Management nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: obsługa spisu kont, które mają dostęp administracyjny do płaszczyzny kontroli API Management platformy Azure (Azure Portal).

Azure Active Directory (AD) ma wbudowane role, które muszą być jawnie przypisane i queryable. API Management opiera się na tych rolach i Role-Based Access Control w celu umożliwienia szczegółowego zarządzania dostępem dla usług i usługi API Management.

Ponadto API Management zawiera wbudowaną grupę Administratorzy w systemie API Management użytkownika. Grupy w API Management kontrolują widoczność interfejsów API w portalu dla deweloperów, a członkowie grupy Administratorzy mogą zobaczyć wszystkie interfejsy API.

Postępuj zgodnie z zaleceniami Azure Security Center, aby zarządzać i konserwacją kont administracyjnych.

* [Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management](./api-management-role-based-access-control.md)

* [Jak uzyskać listę użytkowników w ramach wystąpienia usługi Azure API Management](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Jak uzyskać listę użytkowników przypisanych do roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [Jak uzyskać definicję roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Informacje o zaleceniach dotyczących tożsamości i dostępu Azure Security Center](../security-center/recommendations-reference.md#recs-identity)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: usługa Azure API Management nie ma koncepcji domyślnych haseł/kluczy.

Subskrypcje usługi Azure API Management, które są jednym ze sposobów zabezpieczania dostępu do interfejsów API, są jednak dołączone do pary wygenerowanych kluczy subskrypcji. Klienci mogą ponownie generować te klucze subskrypcji w dowolnym momencie.

* [Informacje o subskrypcjach usługi Azure API Management](./api-management-subscriptions.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

* [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

* [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: usługa Azure API Management może zostać skonfigurowana w taki sposób, aby korzystała z Azure Active Directory jako dostawcy tożsamości w celu uwierzytelniania użytkowników w portalu dla deweloperów w celu skorzystania z możliwości rejestracji jednokrotnej oferowanych przez usługę Azure AD. Po skonfigurowaniu nowi użytkownicy portalu deweloperów mogą wybrać opcję skorzystania z nieaktualnego procesu tworzenia konta, najpierw uwierzytelniającego się za pomocą usługi Azure AD, a następnie kończąc proces rejestracji w portalu po uwierzytelnieniu.

* [Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory w usłudze Azure API Management](./api-management-howto-aad.md)

Alternatywnie proces logowania/tworzenia konta może być dodatkowo dostosowywany za pomocą delegowania. Delegowanie umożliwia korzystanie z istniejącej witryny sieci Web do obsługi logowania/tworzenia konta i subskrypcji dla deweloperów, w przeciwieństwie do korzystania z wbudowanych funkcji w portalu dla deweloperów. Umożliwia ona korzystanie z danych użytkownika przez witrynę internetową i wykonywanie walidacji tych kroków w niestandardowy sposób.

* [Jak delegować rejestrację użytkownika i subskrypcję produktu](./api-management-howto-setup-delegation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włączanie Azure Active Directory (AD) Multi-Factor Authentication (MFA) i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem Azure Security Center.

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z usługą Multi-Factor Authentication (MFA) skonfigurowaną w celu logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: dziennik i alert dotyczący podejrzanego działania z kont administracyjnych

**Wskazówki**: Użyj Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

* [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów lub krajów/regionów adresów IP.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Jeśli to możliwe, Użyj usługi Azure AD jako centralnego systemu uwierzytelniania i autoryzacji. W usłudze AAD dane są chronione przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Skonfiguruj Portal deweloperów API Management platformy Azure, aby uwierzytelniać konta dewelopera przy użyciu Azure Active Directory.

Skonfiguruj wystąpienie usługi Azure API Management, aby chronić interfejsy API przy użyciu protokołu OAuth 2,0 z usługą Azure Active Directory (AD).

* [Jak autoryzować konta dewelopera przy użyciu Azure Active Directory na platformie Azure API Management](./api-management-howto-aad.md)

* [Jak chronić interfejs API przy użyciu protokołu OAuth 2,0 z Azure Active Directory i API Management](./api-management-howto-protect-backend-with-aad.md)

* [Jak utworzyć i skonfigurować wystąpienie usługi AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory zawiera dzienniki ułatwiające wykrywanie starych kont. Klienci mogą korzystać z przeglądów dostępu do tożsamości platformy Azure w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy nadal mają odpowiedni dostęp.

Klienci mogą zachować spis API Management kont użytkowników i uzgodnić dostęp zgodnie z wymaganiami. W API Management deweloperzy są użytkownikami interfejsów API, które współdziałają z API Management. Nowo utworzone konta dewelopera są domyślnie aktywne i skojarzone z grupą deweloperzy. Konta deweloperów, które znajdują się w stanie aktywnym, mogą być używane do uzyskiwania dostępu do wszystkich interfejsów API, dla których mają subskrypcje.

Administratorzy mogą tworzyć grupy niestandardowe lub korzystać z grup zewnętrznych w skojarzonych dzierżawach Azure Active Directory. Niestandardowe i zewnętrzne grupy mogą być używane razem z grupami systemowymi, zapewniając deweloperom widoczność produktów interfejsu API i dostęp do nich.

* [Zarządzanie kontami użytkowników w usłudze Azure API Management](./api-management-howto-create-or-invite-developers.md)

* [Jak uzyskać listę API Management użytkowników](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Jak tworzyć grupy do zarządzania kontami deweloperów w usłudze Azure API Management i jak z nich korzystać](./api-management-howto-create-groups.md)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych kont

**Wskazówki**: Skonfiguruj wystąpienie usługi Azure API Management, aby uwierzytelniać konta dewelopera przy użyciu Azure Active Directory jako dostawcy tożsamości w usłudze Azure API Management.

Skonfiguruj wystąpienie usługi Azure API Management, aby chronić interfejsy API przy użyciu protokołu OAuth 2,0 z usługą Azure Active Directory (AD).

Skonfiguruj zasady sprawdzania poprawności tokenu JWT dla przychodzących żądań interfejsu API, aby ułatwić wymuszanie istnienia i ważności prawidłowego tokenu.

Tworzenie ustawień diagnostycznych dla kont użytkowników usługi Azure AD i wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego Log Analytics. Skonfiguruj żądane alerty w Log Analytics. Ponadto możesz dołączać obszar roboczy Log Analytics do usługi Azure wskaźnikowej lub SIEM innej firmy.

Skonfiguruj zaawansowane monitorowanie za pomocą API Management przy użyciu `log-to-eventhub` zasad, Przechwyć wszelkie dodatkowe informacje kontekstowe wymagane do analizy zabezpieczeń i Wyślij do usługi Azure wskaźnikowej lub innej firmy Siem.

* [Jak autoryzować konta dewelopera przy użyciu Azure Active Directory na platformie Azure API Management](./api-management-howto-aad.md)

* [Jak chronić interfejs API przy użyciu protokołu OAuth 2,0 z Azure Active Directory i API Management](./api-management-howto-protect-backend-with-aad.md)

* [Zasady ograniczeń dostępu usługi API Management](./api-management-access-restriction-policies.md)

* [Jak zintegrować dzienniki usługi Azure AD z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

* [Zaawansowane monitorowanie interfejsów API](./api-management-log-to-eventhub-sample.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: w celu odróżnienia zachowania logowania do konta na płaszczyźnie kontroli (Azure Portal) użyj funkcji Azure Active Directory (AD) Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

* [Jak wyświetlić ryzykowne logowania usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest obecnie obsługiwana w przypadku usługi Azure API Management.

* [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Wystąpienia usługi Azure API Management muszą być rozdzielone przez sieć wirtualną (VNet)/Subnet i odpowiednio oznakowane.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

* [Używanie usługi Azure API Management z sieciami wirtualnymi](./api-management-using-with-vnet.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla API Management platformy Azure.

Firma Microsoft zarządza podstawową infrastrukturą dla systemu Azure API Management i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: wywołania płaszczyzny zarządzania są nawiązywane za pośrednictwem Azure Resource Manager za pośrednictwem protokołu TLS. Wymagany jest prawidłowy token sieci Web JSON (JWT). Wywołania płaszczyzny danych mogą być zabezpieczone przy użyciu protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub tokenu JWT).

* [Informacje o ochronie danych w usłudze Azure API Management](./api-management-security-controls.md#data-protection)

* [Zarządzanie ustawieniami protokołu TLS w usłudze Azure API Management](./api-management-howto-manage-protocols-ciphers.md)

* [Ochrona interfejsów API w usłudze Azure API Management przy użyciu Azure Active Directory](./api-management-howto-protect-backend-with-aad.md)

* [Ochrona interfejsów API w usłudze Azure API Management przy użyciu Azure Active Directory B2C](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są obecnie dostępne dla API Management platformy Azure. Oznacz usługi Azure API Management Services, które mogą przetwarzać informacje poufne jako takie i wdrażać rozwiązania innych firm, jeśli jest to wymagane do celów zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: korzystanie z kontroli dostępu opartej na rolach w celu kontrolowania dostępu do usługi Azure API Management. Usługa Azure API Management opiera się na kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure w celu umożliwienia szczegółowego zarządzania dostępem do API Management usług i jednostek (na przykład interfejsów API i zasad).

* [Jak używać kontroli dostępu opartej na rolach w usłudze Azure API Management](./api-management-role-based-access-control.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Firma Microsoft zarządza podstawową infrastrukturą dla systemu Azure API Management i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: dane poufne, takie jak certyfikaty, klucze i wartości nazwanych wpisów tajnych, są szyfrowane przy użyciu zarządzanych przez usługę usług według kluczy wystąpienia usługi. Wszystkie klucze szyfrowania są dla każdego wystąpienia usługi i są zarządzane przez usługę.

* [Informacje o ochronie i szyfrowaniu danych przechowywanych w usłudze Azure API Management](./api-management-security-controls.md#data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty, kiedy zmiany mają miejsce w środowisku produkcyjnym Azure Functions aplikacje, a także inne krytyczne lub pokrewne zasoby.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Jak używać Azure Monitor i dziennika aktywności platformy Azure na platformie Azure API Management](./api-management-howto-use-azure-monitor.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: obecnie niedostępne; Ocena luk w zabezpieczeniach w Azure Security Center nie jest obecnie dostępna dla API Management platformy Azure.

Platforma bazowa przeskanowana i poprawiona przez firmę Microsoft. Przejrzyj kontrolki zabezpieczeń dostępne w celu zmniejszenia luk w zabezpieczeniach związanych z konfiguracją usługi.

* [Informacje o kontrolkach zabezpieczeń dostępnych dla usługi Azure API Management](./api-management-security-controls.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Wdróż zautomatyzowane rozwiązanie do zarządzania poprawkami oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: obecnie niedostępne; Ocena luk w zabezpieczeniach w Azure Security Center nie jest obecnie dostępna dla API Management platformy Azure.

Platforma bazowa przeskanowana i poprawiona przez firmę Microsoft. Klient do przeglądania dostępnych dla nich kontroli zabezpieczeń w celu zmniejszenia luk w zabezpieczeniach związanych z konfiguracją usługi.

* [Informacje o kontrolkach zabezpieczeń dostępnych dla usługi Azure API Management](./api-management-security-controls.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

* [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Jak tworzyć Tagi i korzystać z nich](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów platformy Azure. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

* [Jak utworzyć Grupy zarządzania](../governance/management-groups/create.md)

* [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby określić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z usługi Azure Resources za pośrednictwem skryptów

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

* [Kontrola dostępu oparta na rolach na platformie Azure API Management](./api-management-role-based-access-control.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usługi Azure API Management przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ApiManagement", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji usług Azure API Management.

* [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usług API Management platformy Azure z Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ApiManagement", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień usługi Azure API Management. Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji zasad platformy Azure, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać konfigurację usługi Azure API Management i zarządzać nią.

* [Jak przechowywać pliki na platformie Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentacja Azure Repos](/azure/devops/repos/index?view=azure-devops)

* [Omówienie zestawu Resource Kit usługi Azure API Management DevOps](./api-management-security-controls.md#configuration-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla usług API Management platformy Azure z Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ApiManagement", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień usługi Azure API Management. Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

**Wskazówki**: Użyj zestawu Resource Kit usługi Azure API Management DevOps, aby przeprowadzić zarządzanie konfiguracją dla API Management platformy Azure.

Ponadto należy definiować i implementować standardowe konfiguracje zabezpieczeń dla usług API Management platformy Azure z Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. ApiManagement", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpień usługi Azure API Management. Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Omówienie zestawu Resource Kit usługi Azure API Management DevOps](./api-management-security-controls.md#configuration-management)

* [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md)

* [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Użyj Key Vault do zarządzania certyfikatami i ustaw je na automatyczne obracanie. Jeśli używasz Azure Key Vault do zarządzania certyfikatem SSL domeny niestandardowej, upewnij się, że certyfikat został wstawiony do Key Vault jako certyfikat, a nie wpis tajny.

* [Jak ustawić niestandardowe nazwy domen ze wskazówkami dotyczącymi rotacji kluczy Key Vault](./configure-custom-domain.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Użyj tożsamość usługi zarządzanej generowanych przez Azure Active Directory (AD), aby umożliwić usłudze API Management łatwe i bezpieczne uzyskiwanie dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault.

* [Jak utworzyć tożsamość zarządzaną dla wystąpienia API Management](./api-management-howto-use-managed-service-identity.md)

* [Zasady do uwierzytelniania przy użyciu tożsamości zarządzanej](./api-management-authentication-policies.md#ManagedIdentity)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartość klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla zasobów nieobliczeniowych zaprojektowanych do przechowywania danych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartość klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla zasobów nieobliczeniowych zaprojektowanych do przechowywania danych.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure API Management), ale nie jest uruchamiane na zawartość klienta.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: publikowanie interfejsów API i zarządzanie nimi za pomocą usługi Azure API Management, korzystasz z zalet odporności na uszkodzenia i infrastruktury, które w inny sposób projektujesz, implementują i zarządzają ręcznie. API Management obsługuje wdrożenie obejmujące wiele regionów, co sprawia, że płaszczyzna danych jest odporna na awarie regionalne bez dodawania jakichkolwiek nakładów operacyjnych.

Funkcje tworzenia kopii zapasowych i przywracania usługi API Management zapewniają niezbędne bloki konstrukcyjne do wdrażania strategii odzyskiwania po awarii. Operacje tworzenia kopii zapasowej i przywracania można wykonać ręcznie lub automatycznie.

* [Jak wdrożyć API Management płaszczyznę danych w wielu regionach](./api-management-howto-deploy-multi-region.md)

* [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Jak wywołać operację tworzenia kopii zapasowej API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [Jak wywołać operację przywracania API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: operacje tworzenia kopii zapasowych i przywracania danych przez platformę Azure API Management wykonywania pełnych kopii zapasowych i przywracania systemu.

Tożsamości zarządzane mogą służyć do uzyskiwania certyfikatów z Azure Key Vault API Management niestandardowych nazw domen. Utwórz kopię zapasową wszystkich certyfikatów przechowywanych w Azure Key Vault.

* [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Jak utworzyć kopię zapasową certyfikatów Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Weryfikowanie kopii zapasowych przez wykonywanie testów przywracania usługi i certyfikatów z kopii zapasowych.

* [Jak wywołać operację przywracania API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Jak przywrócić Azure Key Vault certyfikaty](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: usługa Azure API Management zapisuje kopie zapasowe na kontach usługi Azure Storage należących do klienta. Wykonaj zalecenia dotyczące zabezpieczeń usługi Azure Storage, aby chronić kopię zapasową.

* [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Zalecenie dotyczące zabezpieczeń usługi BLOB Storage](../storage/blobs/security-recommendations.md)

Włącz Soft-Delete w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

* [Jak włączyć Soft-Delete w Key Vault](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

* [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

* [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

* [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: korzystanie z funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów i zaleceń dotyczących zabezpieczeń.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i upewnij się, że korygujesz wszystkie krytyczne wnioski o zabezpieczeniach w ciągu 60 dni

**Wskazówki**: * [postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w ramach infrastruktury, usług i aplikacji chmurowych firmy Microsoft, zobacz](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
