---
title: Podstawa zabezpieczeń Azure dla usługi Azure Cloud Services
description: Podstawowa usługa Azure Cloud Services Security zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c389cfd2af6fe83ec232e5f205041676ba46bd2a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558197"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Podstawa zabezpieczeń Azure dla usługi Azure Cloud Services

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Microsoft Azure Cloud Services. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Cloud Services. **Kontrolki** nie mają zastosowania do Cloud Services zostały wykluczone.

 
Aby dowiedzieć się, jak Cloud Services całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Cloud Services pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: utwórz klasyczny Virtual Network platformy Azure z oddzielnymi podsieciami publicznymi i prywatnymi, aby wymusić izolację na podstawie zaufanych portów i zakresów adresów IP. Te sieci wirtualne i podsieci muszą być klasycznymi zasobami opartymi na Virtual Network (klasycznym wdrożeniu), a nie bieżącymi zasobami Azure Resource Manager.  

Zezwalaj lub Odmawiaj ruchu przy użyciu sieciowej grupy zabezpieczeń, która zawiera reguły kontroli dostępu na podstawie kierunku ruchu, protokołu, adresu źródłowego i portu oraz adresu i portu docelowego. Reguły sieciowej grupy zabezpieczeń można zmienić w dowolnym momencie, a zmiany są stosowane do wszystkich skojarzonych wystąpień.

Microsoft Azure Cloud Services (klasyczny) nie można umieścić w Azure Resource Manager sieci wirtualnych. W przypadku sieci wirtualnych opartych na Menedżer zasobów i klasycznych sieci wirtualnych opartych na wdrożeniach można łączyć się za pośrednictwem komunikacji równorzędnej. 

- [Omówienie sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md)

- [Komunikacja równorzędna sieci wirtualnych](./cloud-services-connectivity-and-networking-faq.md#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: udokumentowanie konfiguracji usługi Azure Cloud Services i monitorowanie jej pod kątem zmian. Plik konfiguracji usługi umożliwia określenie liczby wystąpień ról do wdrożenia dla każdej roli w usłudze, wartości ustawień konfiguracji oraz odcisków palców dla wszystkich certyfikatów skojarzonych z rolą. 

Jeśli usługa jest częścią sieci wirtualnej, informacje o konfiguracji sieci muszą być podane w pliku konfiguracji usługi, a także w pliku konfiguracji sieci wirtualnej. Domyślnym rozszerzeniem dla pliku konfiguracji usługi jest. cscfg. Należy pamiętać, że Azure Policy nie jest obsługiwany w przypadku wdrożeń klasycznych na potrzeby wymuszania konfiguracji.

Ustaw wartości konfiguracji usługi w chmurze w pliku konfiguracji usługi (. cscfg) i definicji w pliku definicji usługi (. csdef). Użyj pliku definicji usługi, aby zdefiniować model usługi dla aplikacji. Zdefiniuj role, które są dostępne dla usługi w chmurze, a także określ punkty końcowe usługi. Rejestruj konfigurację Cloud Services platformy Azure przy użyciu pliku konfiguracji usługi. Każdą ponowną konfigurację można wykonać za pomocą pliku ServiceConfig. cscfg. 

Monitoruj opcjonalną definicję usługi elementu NetworkTrafficRules, która ogranicza, które role mogą komunikować się z określonymi wewnętrznymi punktami końcowymi. Skonfiguruj węzeł NetworkTrafficRules, opcjonalny element w pliku definicji usługi, aby określić, w jaki sposób role mają komunikować się ze sobą. Umieszczaj limity, w których role mogą uzyskiwać dostęp do wewnętrznych punktów końcowych określonej roli.  Należy pamiętać, że nie można zmienić definicji usługi. 

Włącz dzienniki przepływu sieciowych grup zabezpieczeń i Wyślij dzienniki do konta usługi Azure Storage w celu przeprowadzenia inspekcji. Wyślij dzienniki przepływu do obszaru roboczego Log Analytics i użyj Analiza ruchu, aby uzyskać wgląd w wzorce ruchu w dzierżawie platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej, identyfikowania aktywnych plam i zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Wdrożenie Azure Resource Manager a klasyczne — Omówienie modeli wdrażania i stanu zasobów](../azure-resource-manager/management/deployment-models.md)

- [Plik konfiguracji Cloud Services](schema-cscfg-file.md)

- [Lista usług obsługiwanych przez Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: Firma Microsoft korzysta z protokołu Transport Layer Security (TLS) w celu ochrony danych podczas podróży między usługą Azure Cloud Services i klientami. Centra danych firmy Microsoft negocjują połączenie TLS z systemami klienckimi, które łączą się z usługami platformy Azure. Protokół TLS zapewnia silne uwierzytelnianie, prywatność komunikatów i integralność (umożliwiając wykrywanie manipulowania komunikatami, przechwycenie i fałszowanie), współdziałanie, elastyczność algorytmu oraz łatwość wdrażania i używania.

- [Podstawy szyfrowania](../security/fundamentals/encryption-overview.md)

- [Konfigurowanie certyfikatów TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: usługa Azure Cloud implementuje zabezpieczenia sieci Multilayer, aby chronić usługi platformy przed atakami typu "odmowa usługi" (DDoS). Azure DDoS Protection jest częścią procesu ciągłego monitorowania w chmurze platformy Azure, który jest ciągle ulepszany przez testowanie penetracji. Ta DDoS Protection została zaprojektowana tak, aby wytrzymać nie tylko ataki z zewnątrz, ale również od innych dzierżawców platformy Azure. 

Istnieje kilka różnych sposobów blokowania lub odmawiania komunikacji poza ochroną na poziomie platformy w ramach usługi Azure Cloud Services. Są to: 

-  Utwórz zadanie uruchamiania, aby selektywnie blokować niektóre określone adresy IP
-  Ogranicz dostęp roli sieci Web platformy Azure do zestawu określonych adresów IP, modyfikując plik web.config IIS

Zapobiegaj przychodzącemu ruchowi do domyślnego adresu URL lub nazwy Cloud Services, na przykład *. cloudapp.NET. Dla nagłówka hosta Ustaw niestandardową nazwę DNS, w obszarze Konfiguracja powiązania witryny w pliku definicji Cloud Services (* csdef).

Skonfiguruj regułę odmowy zastosowania do przypisań administratora klasycznej subskrypcji. Domyślnie po zdefiniowaniu wewnętrznego punktu końcowego komunikacja może przepływać z dowolnej roli do wewnętrznego punktu końcowego roli bez żadnych ograniczeń. Aby ograniczyć komunikację, należy dodać element NetworkTrafficRules do elementu ServiceDefinition w pliku definicji usługi.

- [Jak zablokować/wyłączyć ruch przychodzący do domyślnego adresu URL mojej usługi w chmurze](./cloud-services-connectivity-and-networking-faq.md#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Ochrona za pomocą usługi Azure DDOS](./cloud-services-connectivity-and-networking-faq.md#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Blokuj określony adres IP](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: korzystanie z platformy Azure Network Watcher, monitorowania wydajności sieci, diagnostyki i usługi analizy, która umożliwia monitorowanie sieci platformy Azure. Rozszerzenie maszyny wirtualnej agenta Network Watcher jest wymaganiem do przechwytywania ruchu sieciowego na żądanie oraz innych zaawansowanych funkcji w usłudze Azure Virtual Machines. Zainstaluj rozszerzenie maszyny wirtualnej agenta Network Watcher i Włącz dzienniki przepływu sieciowych grup zabezpieczeń.

Skonfiguruj rejestrowanie przepływu w sieciowej grupie zabezpieczeń. Zapoznaj się ze szczegółowymi informacjami na temat wdrażania rozszerzenia maszyny wirtualnej Network Watcher na istniejącej maszynie wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania.

- [Konfigurowanie rejestrowania przepływu w sieciowej grupie zabezpieczeń](../virtual-machines/extensions/network-watcher-linux.md)

- [Aby uzyskać więcej informacji na temat konfigurowania dzienników przepływów, odwiedź stronę](/cli/azure/azure-services-the-azure-cli-can-manage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: usługa Azure Cloud Services nie ma wbudowanych identyfikatorów ani adresów IP. Klienci mogą wybrać i wdrożyć dodatkowe rozwiązanie sieciowe lub adresy IP w witrynie Azure Marketplace w oparciu o ich wymagania organizacyjne. W przypadku korzystania z rozwiązań innych firm Pamiętaj o gruntownym przetestowaniu wybranych identyfikatorów lub adresów IP za pomocą usługi Azure Cloud Services, aby zapewnić prawidłowe działanie i funkcjonalność.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: certyfikaty usług, które są dołączone do usługi Azure Cloud Services, umożliwiają bezpieczną komunikację z usługą i z niej. Te certyfikaty są zdefiniowane w definicji usług i są automatycznie wdrażane na maszynie wirtualnej, na której działa wystąpienie roli sieci Web. Przykładowo dla roli sieci Web można użyć certyfikatu usługi, który może uwierzytelniać uwidoczniony punkt końcowy HTTPS. 

Aby zaktualizować certyfikat, należy przekazać nowy certyfikat i zmienić wartość odcisku palca w pliku konfiguracji usługi.

Użyj protokołu TLS 1,2, najczęściej stosowanej metody zabezpieczania danych w celu zapewnienia poufności i ochrony integralności. 

Ogólnie rzecz biorąc, w celu ochrony aplikacji sieci Web i zabezpieczania ich przed atakami, takimi jak OWASP 10 pierwszych, można wdrożyć usługę Azure Web Application z obsługą zapory Application Gateway platformy Azure na potrzeby ochrony aplikacji sieci Web. 

- [Certyfikaty usługi](cloud-services-certs-create.md)

- [Konfigurowanie protokołu TLS dla aplikacji na platformie Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Jak wdrożyć Application Gateway](../application-gateway/quick-create-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Zwiększanie funkcjonalności konfiguracji usługi Azure Cloud Services i monitorowanie jej pod kątem zmian. Plik konfiguracji usługi określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości wszelkich ustawień konfiguracji i odcisków palców dla wszystkich certyfikatów skojarzonych z rolą. 

Jeśli usługa jest częścią sieci wirtualnej, informacje o konfiguracji sieci muszą być podane w pliku konfiguracji usługi, a także w pliku konfiguracji sieci wirtualnej. Domyślnym rozszerzeniem dla pliku konfiguracji usługi jest. cscfg.

Należy pamiętać, że Azure Policy nie jest obsługiwana w usłudze Azure Cloud Services w celu wymuszenia konfiguracji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: grupy zabezpieczeń sieci platformy Azure mogą służyć do filtrowania ruchu sieciowego do i z zasobów platformy Azure w ramach Virtual Network platformy Azure. Sieciowa Grupa zabezpieczeń zawiera reguły zabezpieczeń zezwalające lub odmawiające przychodzącego ruchu sieciowego do lub wychodzącego ruchu sieciowego z programu z kilku typów zasobów platformy Azure. Dla każdej reguły można określić źródło i obiekt docelowy, port i protokół.

Użyj pola "Description" dla poszczególnych reguł sieciowych grup zabezpieczeń w ramach platformy Azure Cloud Services, aby udokumentować reguły, które zezwalają na ruch do lub z sieci.

- [Jak filtrować ruch sieciowy przy użyciu reguł sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj wbudowanego monitorowania punktu końcowego Traffic Manager platformy Azure oraz funkcji automatycznego przełączania do trybu failover punktu końcowego. Ułatwiają one dostarczanie aplikacji o wysokiej dostępności, które są odporne na awarie i regiony platformy Azure. Aby skonfigurować monitorowanie punktów końcowych, należy określić niektóre ustawienia w profilu Traffic Manager.

Zbierz informacje z dziennika aktywności, a następnie zaloguj się na platformie Azure do zdarzeń na poziomie subskrypcji. Zawiera takie informacje, jak w przypadku zmodyfikowania zasobu lub uruchomienia maszyny wirtualnej. Wyświetlanie dziennika aktywności w Azure Portal lub pobieranie wpisów przy użyciu programu PowerShell i interfejsu wiersza polecenia. 

Utwórz ustawienie diagnostyczne, aby wysłać dziennik aktywności do Azure Monitor, Event Hubs platformy Azure do przodu poza platformą Azure lub do usługi Azure Storage w celu archiwizacji. Skonfiguruj Azure Monitor alertów powiadomień, gdy krytyczne zasoby w usłudze Azure Cloud Services zostały zmienione. 

- [Dziennik aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Monitorowanie Traffic Manager](../traffic-manager/traffic-manager-monitoring.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure dla usługi Azure Cloud Services. Klienci mogą potrzebować utworzyć regułę sieci, aby zezwolić na dostęp do serwera czasu używanego w środowisku, przez port 123 z protokołem UDP.

- [Dostęp do serwera NTP](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: programowe korzystanie z danych strumieniowych usługi w chmurze za pomocą usługi Azure Event Hubs. Integruj i wysyłaj wszystkie te dane do usługi Azure wskaźnikowej, aby monitorować i przeglądać dzienniki, lub korzystać z SIEM innych firm. Aby zarządzać centralnym dziennikiem zabezpieczeń, należy skonfigurować ciągły Eksport wybranych danych Azure Security Center na platformie Azure Event Hubs i skonfigurować odpowiedni łącznik dla SIEM. Poniżej przedstawiono niektóre opcje dotyczące platformy Azure, w tym narzędzi innych firm:

- Wskaźnik platformy Azure — korzystanie z łącznika danych Security Center natywnych alertów
- Splunk — Użyj dodatku Azure Monitor dla Splunk
- IBM QRadar — Użyj ręcznie skonfigurowanego źródła dziennika
- ArcSight — Użyj SmartConnector

Zapoznaj się z dokumentacją platformy Azure, aby uzyskać dodatkowe szczegóły dotyczące dostępnych łączników na platformie Azure. 

- [Łączenie ze źródłami danych](../sentinel/connect-data-sources.md)

- [Integracja z usługą SIEM](../security-center/continuous-export.md)

- [Przechowywanie danych diagnostycznych](diagnostics-extension-to-storage.md)

- [Konfigurowanie integracji SIEM za pomocą usługi Azure Event Hubs](../security-center/continuous-export.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Skonfiguruj program Visual Studio, aby skonfigurować Diagnostyka Azure do rozwiązywania problemów z usługą Azure Cloud Services, która przechwytuje dane systemowe i rejestrowania na maszynach wirtualnych, w tym wystąpieniach maszyn wirtualnych, na których działa Cloud Services platformy Azure Dane diagnostyczne są przesyłane do wybranego konta magazynu. Włącz diagnostykę w projektach Cloud Services platformy Azure przed ich wdrożeniem.

 
Wyświetl historię zmian niektórych zdarzeń w dzienniku aktywności w Azure Monitor. Inspekcja zmian, które wystąpiły w okresie zdarzenia. Wybierz zdarzenie z dziennika aktywności w celu uzyskania dokładniejszej inspekcji na karcie Historia zmian (wersja zapoznawcza). Wyślij dane diagnostyczne do Application Insights podczas publikowania Cloud Services platformy Azure z poziomu programu Visual Studio. Utwórz zasób usługi Application Insights Azure w tym momencie lub Wyślij dane do istniejącego zasobu platformy Azure. 

Cloud Services platformy Azure mogą być monitorowane przez Application Insights w celu zapewnienia dostępności, wydajności, błędów i użycia. Niestandardowe wykresy można dodać do Application Insights, aby zobaczyć dane, które są najbardziej istotne. Dane wystąpienia roli można zbierać przy użyciu zestawu SDK Application Insights w projekcie Cloud Services platformy Azure. 

- [Włącz diagnostykę w programie Visual Studio przed wdrożeniem](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Wyświetl historię zmian](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Application Insights dla usługi w chmurze systemu Azure (wersja klasyczna)](../azure-monitor/app/cloudservices.md)

- [Konfigurowanie diagnostyki dla usługi w chmurze platformy Azure (klasycznej) i maszyn wirtualnych](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: możesz użyć zaawansowanego monitorowania z usługą Azure Cloud Services, co umożliwia próbkowanie i zbieranie dodatkowych metryk w odstępach wynoszących 5 minut, 1 godzinę i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu w tabelach i usuwane po upływie 10 dni. Jednak używane konto magazynu jest konfigurowane przez rolę i można użyć różnych kont magazynu dla różnych ról. Jest to konfiguracja z parametrami połączenia w plikach. csdef i. cscfg.

Należy zauważyć, że zaawansowane monitorowanie obejmuje użycie rozszerzenia Diagnostyka Azure (Application Insights SDK jest opcjonalne) na roli, która ma być monitorowana. Rozszerzenie diagnostyki używa pliku konfiguracji (na rolę) o nazwie Diagnostics. wadcfgx, aby skonfigurować monitorowane metryki diagnostyki. Rozszerzenie diagnostyki platformy Azure gromadzi i zapisuje dane na koncie usługi Azure Storage. Te ustawienia są konfigurowane w plikach. wadcfgx,. csdef i. cscfg.

- [Wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: dostępne są podstawowe lub zaawansowane tryby monitorowania dla Cloud Services platformy Azure. Usługa Azure Cloud Services automatycznie zbiera podstawowe dane monitorowania (procent użycia procesora, w sieci i do odczytu i zapisu na dysku) z maszyny wirtualnej hosta. Zebrane dane monitorowania są wyświetlane na stronach przegląd i metryki usługi w chmurze w Azure Portal. 

Włącz diagnostykę na platformie Azure Cloud Services w celu zbierania danych diagnostycznych, takich jak Dzienniki aplikacji, liczniki wydajności i inne, przy użyciu rozszerzenia Diagnostyka Azure. Włącz lub zaktualizuj konfigurację diagnostyki w usłudze w chmurze, która jest już uruchomiona przy użyciu polecenia cmdlet Set-AzureServiceDiagnosticsExtension lub automatycznie Wdróż usługę w chmurze z rozszerzeniem diagnostyki. Opcjonalnie Zainstaluj zestaw SDK Application Insights. Wyślij liczniki wydajności do Azure Monitor.

Rozszerzenie diagnostyki platformy Azure gromadzi i zapisuje dane na koncie usługi Azure Storage. Przenieś dane diagnostyczne do emulator magazynu Microsoft Azure lub do usługi Azure Storage, ponieważ nie są one trwale przechowywane. Jeden z kilku dostępnych narzędzi, takich jak Eksplorator serwera w programie Visual Studio, Eksplorator usługi Microsoft Azure Storage Management Studio platformy Azure, można wyświetlić w magazynie. Skonfiguruj metryki diagnostyki do monitorowania za pomocą pliku konfiguracji (na rolę) o nazwie Diagnostics. wadcfgx w rozszerzeniu diagnostyki. 

- [Wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md)

- [Jak włączyć diagnostykę w roli procesu roboczego — Integruj z SIEM](../security-center/continuous-export.md)

- [Włączanie diagnostyki na platformie Azure Cloud Services przy użyciu programu PowerShell](cloud-services-diagnostics-powershell.md)

- [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](./diagnostics-extension-to-storage.md?preserve-view=)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: można monitorować dane dziennika Cloud Services platformy Azure przez integrację z platformą Azure, lub z Siem innej firmy, włączając alerty dla nietypowych działań.

- [Integracja z usługą SIEM](../security-center/continuous-export.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Firma Microsoft chroniąca przed złośliwym kodem dla platformy Azure, chroniące Cloud Services i maszyny wirtualne platformy Azure. Istnieje możliwość wdrażania rozwiązań zabezpieczeń innych firm, takich jak ściany aplikacji sieci Web, zapory sieci, ochrona przed złośliwym oprogramowaniem, systemy wykrywania i zapobiegania dostępem intruzów (identyfikatory lub adresy IP) itd.

- [Jakie są funkcje i możliwości, które zapewnia usługa Azure Basic adresy IP/identyfikatory i DDOS](./cloud-services-configuration-and-management-faq.md#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: Firma Microsoft zaleca zarządzanie dostępem do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Usługa Azure Cloud Services nie obsługuje jednak modelu RBAC platformy Azure, ponieważ nie jest ona usługą opartą na Azure Resource Manager i musisz używać klasycznej subskrypcji

Domyślnie administrator konta, administrator usługi i Co-Administrator są trzema klasycznymi rolami administratora subskrypcji na platformie Azure. 

Klasyczni administratorzy subskrypcji mają pełny dostęp do subskrypcji platformy Azure. Mogą zarządzać zasobami przy użyciu witryny Azure Portal, interfejsów API usługi Azure Resource Manager i interfejsów API klasycznego modelu wdrożenia. Konto używane do rejestracji na platformie Azure zostanie automatycznie przypisane do administratora konta i administratora usługi. Dodatkowe Co-Administrators można dodać później. 

Administrator usługi i Co-Administrators mają odpowiedni dostęp do użytkowników, którym przypisano rolę właściciela (rolę platformy Azure) w zakresie subskrypcji. Zarządzanie Co-Administrators lub wyświetlanie administratora usługi przy użyciu karty administratorów klasycznych na Azure Portal. 

Wyświetl listę przypisań ról dla administratora klasycznej usługi i współadministratorów przy użyciu programu PowerShell z poleceniem:

Get-AzRoleAssignment — IncludeClassicAdministrators

Zapoznaj się z różnicami między rolami administracyjnymi klasycznych subskrypcji. 

- [Różnice między trzema klasycznymi rolami administracyjnymi subskrypcji](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: zalecane jest utworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych na podstawie dostępnych ról i uprawnień wymaganych do obsługi zasobów usługi Azure Cloud Services i zarządzania nimi.

- [Różnice między rolami administracyjnymi klasycznych subskrypcji](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: Unikaj zarządzania osobnymi tożsamościami dla aplikacji działających w usłudze Azure Cloud Services. Zaimplementuj Logowanie jednokrotne, aby uniknąć konieczności, aby użytkownicy mogli zarządzać wieloma tożsamościami i poświadczeniami.

- [Co to jest logowanie jednokrotne (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: zaleca się korzystanie z bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego) do zadań administracyjnych, które wymagają podwyższonego poziomu uprawnień.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe w usłudze Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj interfejsów API REST usługi Azure Cloud Service, aby przeprowadzić spis zasobów usługi w chmurze platformy Azure w celu uzyskania informacji poufnych. Przesondowaj wdrożone zasoby usługi w chmurze, aby uzyskać konfigurację i zasoby pkg.

 Na przykład poniżej przedstawiono kilka interfejsów API:

- Pobieranie wdrożenia — operacja pobrania wdrożenia zwraca informacje o konfiguracji, stan i właściwości systemu dla wdrożenia.
- Pobierz pakiet — operacja Pobierz pakiet pobiera pakiet usługi w chmurze dla wdrożenia i zapisuje pliki pakietu w Microsoft Azure Storage BLOB
- Pobierz właściwości usługi w chmurze — operacja pobrania właściwości usługi w chmurze pobiera właściwości dla określonej usługi w chmurze

Przejrzyj dokumentację interfejsów API REST usługi Azure Cloud Service i Utwórz proces ochrony danych poufnych informacji zgodnie z wymaganiami organizacji.

- [Uzyskiwanie wdrożenia](/rest/api/compute/cloudservices/rest-get-deployment)

- [Pobierz właściwości usługi w chmurze](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Pobierz pakiet](/rest/api/compute/cloudservices/rest-get-package)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych dla Cloud Services platformy Azure.

Możesz również edytować "permissionLevel" w elemencie certyfikatu usługi w chmurze platformy Azure, aby określić uprawnienia dostępu nadawane procesom roli. Jeśli chcesz, aby dostęp do klucza prywatnego był możliwy tylko z podniesionymi procesami, określ uprawnienie podwyższone uprawnienia. uprawnienie limitedOrElevated umożliwia wszystkim procesom roli dostęp do klucza prywatnego. Możliwe wartości to limitedOrElevated lub Podwyższono poziom uprawnień. Wartość domyślna to limitedOrElevated.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Schemat WebRole](./schema-csdef-webrole.md#Certificate)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: zaleca się korzystanie z rozwiązania innej firmy z witryny Azure Marketplace w sieci obwodowej do monitorowania nieautoryzowanego transferu poufnych informacji i blokowania takich transferów podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Konfigurowanie protokołu TLS v2 dla Cloud Services platformy Azure. Użyj Azure Portal, aby dodać certyfikat do wdrożenia przygotowanych Cloud Services platformy Azure i dodać informacje o certyfikacie do plików CSDEF i CSCFG usług. Ponownie spakować aplikację i zaktualizuj wdrożenie przemieszczane, aby użyć nowego pakietu. 

Użyj certyfikatów usług na platformie Azure, które są dołączone do usługi Azure Cloud Services, aby umożliwić bezpieczną komunikację z usługą i z niej. Podaj certyfikat, który może uwierzytelniać uwidoczniony punkt końcowy HTTPS. Zdefiniuj certyfikaty usługi w definicji usługi w chmurze i automatycznie wdróż je na maszynie wirtualnej, uruchamiając wystąpienie roli.

Uwierzytelnianie za pomocą interfejsu API zarządzania z certyfikatami zarządzania programu pozwala na uwierzytelnianie przy użyciu klasycznego modelu wdrażania. Wiele programów i narzędzi (takich jak program Visual Studio lub zestaw Azure SDK) używa tych certyfikatów do zautomatyzowania konfigurowania i wdrażania różnych usług platformy Azure. 

Aby uzyskać dodatkowe informacje, interfejs API klasyczny model wdrażania zapewnia programistyczny dostęp do funkcji klasycznego modelu wdrażania dostępnego za pomocą Azure Portal. Zestaw Azure SDK dla języka Python może służyć do zarządzania kontami usługi Azure Cloud Services i Azure Storage. Zestaw Azure SDK dla języka Python otacza klasyczny interfejs API modelu wdrażania, interfejs API REST. Wszystkie operacje interfejsu API są wykonywane za pośrednictwem protokołu TLS i uwierzytelniane obustronnie przy użyciu certyfikatów X. 509 v3. Dostęp do usługi zarządzania można uzyskać z poziomu usługi działającej na platformie Azure. Dostęp do niego można uzyskać również bezpośrednio przez Internet z dowolnej aplikacji, która może wysyłać żądanie HTTPS i odbierać odpowiedź HTTPS.

- [Konfigurowanie protokołu TLS dla aplikacji na platformie Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Korzystanie z klasycznego modelu wdrażania z poziomu języka Python](cloud-services-python-how-to-use-service-management.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: zalecane jest użycie narzędzia do odnajdywania aktywnego innej firmy w celu zidentyfikowania wszystkich informacji poufnych przechowywanych, przetwarzanych lub przesyłanych przez systemy technologiczne organizacji, w tym znajdujących się w lokacji lub u dostawcy usług zdalnych, a następnie zaktualizować spis informacji poufnych organizacji.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy usługi w chmurze (klasycznej). Nie wymusza to ochrony przed utratą danych.

Zalecane jest zaimplementowanie narzędzi innych firm, takich jak automatyczne rozwiązanie do ochrony przed utratą danych opartych na hoście, aby wymusić kontrolę dostępu dla danych nawet wtedy, gdy dane są kopiowane poza system.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: usługa Azure Cloud Services nie obsługuje szyfrowania w czasie spoczynku. Wynika to z faktu, że usługa Azure Cloud Services została zaprojektowana jako bezstanowa. Usługa Azure Cloud Services obsługuje magazyn zewnętrzny, na przykład usługę Azure Storage, która jest domyślnie szyfrowana w stanie spoczynku.  

Dane aplikacji przechowywane na dyskach tymczasowych nie są szyfrowane. Klient jest odpowiedzialny za zarządzanie i szyfrowanie tych danych zgodnie z potrzebami.  

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../security/fundamentals/encryption-atrest.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: możesz użyć klasycznych alertów metryk w Azure monitor, aby otrzymywać powiadomienia, gdy jedna z metryk zastosowana do zasobów krytycznych przekracza wartość progową. Klasyczne alerty metryk są starszą funkcją, która umożliwia generowanie alertów tylko w przypadku metryk niewymiarowych. Istnieje nowa funkcja, nazywana alertami metryk, która oferuje ulepszone funkcje w porównaniu z klasycznymi alertami metryk. 

Ponadto Application Insights mogą monitorować aplikacje Cloud Services platformy Azure pod kątem dostępności, wydajności, błędów i użycia. Używa on połączonych danych z zestawów SDK Application Insights z danymi Diagnostyka Azure z Cloud Services platformy Azure.

- [Twórz i wyświetlaj klasyczne alerty metryk i zarządzaj nimi za pomocą Azure Monitor](../azure-monitor/alerts/alerts-classic-portal.md)

- [Przegląd alertów dotyczących metryk](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Application Insights dla usługi w chmurze systemu Azure (wersja klasyczna)](../azure-monitor/app/cloudservices.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Zwróć uwagę na to, że te informacje dotyczą systemu operacyjnego gościa platformy Azure dla usługi Azure Cloud Services Worker i ról sieci Web z platformą jako usługą (PaaS). Nie dotyczy to jednak Virtual Machines z infrastrukturą jako usługą (IaaS).

Domyślnie platforma Azure okresowo aktualizuje system operacyjny gościa do najnowszej wersji obrazu obsługiwanej przez rodzinę systemów operacyjnych, która została określona w konfiguracji usługi (. cscfg), np. w systemie Windows Server 2016.

Jeśli klient wybierze określoną wersję systemu operacyjnego dla wdrożenia usługi Azure Cloud Services, program wyłączy automatyczne aktualizacje systemu operacyjnego i podejmuje odpowiednie poprawki. Klient musi upewnić się, że ich wystąpienia roli otrzymują aktualizacje lub mogą uwidocznić swoją aplikację w przypadku luk w zabezpieczeniach.

- [System operacyjny gościa platformy Azure](cloud-services-guestos-msrc-releases.md)

- [Zasady dotyczące obsługi i wycofania systemu operacyjnego gościa platformy Azure](cloud-services-guestos-retirement-policy.md)

- [Jak skonfigurować usługę w chmurze (klasyczną)](cloud-services-how-to-configure-portal.md)

- [Zarządzaj wersją systemu operacyjnego gościa](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż automatyczne rozwiązanie do zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Użyj rozwiązania do zarządzania poprawkami innej firmy. Klienci używający już Configuration Manager w ich środowisku mogą również używać System Center Updates Publisher, co umożliwia im publikowanie aktualizacji niestandardowych w usłudze Windows Server Update Service. 

Pozwala to Update Management na poprawianie maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: zalecane jest, aby klient mógł zrozumieć zakres ryzyka z ataku DDoS na bieżąco. 

Sugerujemy następujące scenariusze:

- Jakie nowe publicznie dostępne zasoby platformy Azure wymagają ochrony?
- Czy istnieje single point of failure w usłudze?
- Jak usługi mogą być izolowane w celu ograniczenia wpływu ataku, a w dalszym ciągu udostępniają usługi prawidłowym klientom?
- Czy istnieją sieci wirtualne, w których należy włączyć DDoS Protection Standard, ale nie są?
- Czy moje usługi są aktywne/aktywne z trybem failover w wielu regionach?

Dokumentacja pomocnicza:

- [Ocena ryzyka zasobów platformy Azure](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: nie dotyczy Cloud Services platformy Azure. To zalecenie dotyczy IaaS zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: zalecane jest regularne uzgadnianie spisu i zapewnienie, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w odpowiednim czasie.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: klient powinien zdefiniować zatwierdzone zasoby platformy Azure oraz zatwierdzone oprogramowanie do zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:
- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:

- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.

- Unikanie używania w danym środowisku niechcianego oprogramowania.

- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.

- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.

- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:

- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.

- Unikanie używania w danym środowisku niechcianego oprogramowania.

- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.

- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.

- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:

- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.

- Unikanie używania w danym środowisku niechcianego oprogramowania.

- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.

- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.

- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:
- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: korzystanie z funkcji adaptacyjnej kontroli aplikacji dostępnej w Azure Security Center. Jest to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z systemami Windows i Linux, Azure i poza platformą Azure. Pomaga również w zabezpieczaniu komputerów przed złośliwym oprogramowaniem. 

Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux.

Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:

- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.

- Unikanie używania w danym środowisku niechcianego oprogramowania.

- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.

- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.

- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: dla wrażliwych lub wysoce narażonych aplikacji z platformą Azure Cloud Services, implementować osobne subskrypcje lub grupy zarządzania w celu zapewnienia izolacji.

Użyj sieciowej grupy zabezpieczeń, Utwórz regułę zabezpieczeń dla ruchu przychodzącego, wybierz usługę, taką jak http, wybierz również port niestandardowy, nadaj mu priorytet i nazwę. Priorytet ma wpływ na kolejność, w której są stosowane reguły, im niższa wartość liczbowa, tym przed zastosowana reguła. Należy skojarzyć sieciową grupę zabezpieczeń z podsiecią lub określonym interfejsem sieciowym w celu wyizolowania lub segmentacji ruchu sieciowego w zależności od potrzeb firmy.

Więcej szczegółów znajduje się w łączach, do których istnieją odwołania.

- [Samouczek — Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu Azure Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj zaleceń z Azure Security Center jako bezpiecznej linii bazowej konfiguracji dla zasobów Cloud Services platformy Azure. 

Na Azure Portal wybierz Security Center, a następnie pozycję aplikacje obliczeniowe &amp; i usługę Azure Cloud Services, aby zobaczyć zalecenia dotyczące zasobów usług.

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: nie dotyczy Cloud Services platformy Azure. Jest on oparty na klasycznym modelu wdrażania. Zalecane jest korzystanie z rozwiązania innej firmy w celu utrzymania bezpiecznych konfiguracji zasobów platformy Azure

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: plik konfiguracji usługi w chmurze platformy Azure przechowuje atrybuty operacyjne dla zasobu. Kopię plików konfiguracji można przechowywać na bezpiecznym koncie magazynu.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: nie dotyczy Cloud Services platformy Azure. Jest on oparty na klasycznym modelu wdrażania i nie może być zarządzany przez Azure Resource Manager narzędzi konfiguracyjnych opartych na wdrożeniu.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy Cloud Services platformy Azure. To zalecenie dotyczy zasobów obliczeniowych opartych na infrastrukturze jako usługi (IaaS).

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure.  

- [Jak skorygować zalecenia w Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: w Azure Security Center wybierz funkcję COMPUTE &amp; Apps i postępuj zgodnie z zaleceniami dotyczącymi maszyn wirtualnych, serwerów i kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](../security-center/container-security.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: usługa Azure Cloud Services opiera się na klasycznym modelu wdrażania i nie integruje się z Azure Key Vault.

Możesz zabezpieczyć wpisy tajne, takie jak poświadczenia, które są używane w usłudze Azure Cloud Services, aby nie trzeba było wpisywać hasła za każdym razem. Aby rozpocząć od, określ hasło w postaci zwykłego tekstu, przekonwertuj je na bezpieczny ciąg za pomocą polecenia ConvertTo-SecureString, programu PowerShell. Następnie przekonwertuj ten bezpieczny ciąg na zaszyfrowany ciąg standardowy przy użyciu ConvertFrom-SecureString.  Teraz można zapisać zaszyfrowany ciąg standardowy do pliku przy użyciu polecenia Set-Content.

Ponadto zaleca się przechowywanie kluczy prywatnych dla certyfikatów używanych w usłudze Azure Cloud Services do bezpiecznego magazynu.

- [Konfigurowanie Pulpit zdalny przy użyciu programu PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Zabezpiecz wpisy tajne, takie jak poświadczenia używane w usłudze Azure Cloud Services, aby nie trzeba było wpisywać hasła za każdym razem. 
 

Aby rozpocząć, określ hasło w postaci zwykłego tekstu, zmień je na bezpieczny ciąg za pomocą polecenia ConvertTo-SecureString i programu PowerShell. Następnie przekonwertuj ten bezpieczny ciąg na zaszyfrowany ciąg standardowy przy użyciu ConvertFrom-SecureString. Teraz Zapisz zaszyfrowany ciąg standardowy do pliku przy użyciu polecenia Set-Content.

Przechowuj klucze prywatne dla certyfikatów używanych w usłudze Azure Cloud Services w bezpiecznej lokalizacji magazynu.

- [Konfigurowanie Pulpit zdalny przy użyciu programu PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym kodem

**Wskazówki**: program Microsoft chroniący przed złośliwym kodem dla platformy Azure jest dostępny dla Cloud Services i Virtual Machines platformy Azure. Jest to bezpłatna ochrona w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Generuje alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w swoich systemach platformy Azure. 

Użyj polecenia cmdlet chroniącego przed złośliwym kodem programu PowerShell, aby uzyskać konfigurację ochrony przed złośliwym kodem z opcją "Get-AzureServiceAntimalwareConfig".

Włącz rozszerzenie ochrony przed złośliwym kodem za pomocą skryptu programu PowerShell w zadaniu uruchamiania w usłudze Azure Cloud Services.

Wybierz funkcję adaptacyjnego sterowania aplikacjami w Azure Security Center — inteligentne, zautomatyzowane i kompleksowe rozwiązanie. Pomaga chronić komputery przed złośliwym oprogramowaniem i umożliwia blokowanie lub Alertowanie prób uruchomienia złośliwych aplikacji, w tym tych, które mogłyby zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.

- [Jak dodać rozszerzenie ochrony przed złośliwym kodem dla mojego Cloud Services platformy Azure w sposób zautomatyzowany](./cloud-services-configuration-and-management-faq.md#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Scenariusze wdrażania oprogramowania chroniącego przed złośliwym kodem](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Funkcje adaptacyjnego sterowania aplikacjami](../security-center/security-center-adaptive-application.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu. 

Wyraźnie oznaczaj subskrypcje (na przykład produkcyjne, nieprodukcyjne) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami. 

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

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

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
