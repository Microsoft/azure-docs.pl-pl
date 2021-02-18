---
title: Zabezpieczenia operacyjne platformy Azure | Microsoft Docs
description: Zapoznaj się z Microsoft Azure monitorowaniem dzienników, ich usług i sposobu działania, odczytując ten przegląd.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 7380c0d9679fe74bc91eea269f53fea750922b79
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575011"
---
# <a name="azure-operational-security"></a>Zabezpieczenia operacyjne platformy Azure
## <a name="introduction"></a>Wprowadzenie

### <a name="overview"></a>Omówienie
Wiemy, że zabezpieczenia to zadanie jedno w chmurze i jak ważne jest, aby znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jednym z najlepszych powodów używania platformy Azure dla aplikacji i usług jest skorzystanie z zalet szerokiej gamy dostępnych narzędzi i funkcji zabezpieczeń. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. System Windows Azure musi zapewnić poufność, integralność i dostępność danych klientów, a także włączyć przejrzystą odpowiedzialność.

Aby pomóc klientom w lepszym zrozumieniu macierzy kontroli zabezpieczeń wdrożonych w ramach Microsoft Azure z perspektywy klienta i firmy Microsoft, ten oficjalny dokument "zabezpieczenia operacyjne platformy Azure" jest zapisywana, która zapewnia kompleksowy wgląd w zabezpieczenia operacyjne dostępne w systemie Windows Azure.

### <a name="azure-platform"></a>Platforma Azure
Azure to platforma usług w chmurze publicznej, która obsługuje szeroką gamę systemów operacyjnych, języków programowania, struktur, narzędzi, baz danych i urządzeń. Może uruchamiać kontenery systemu Linux z integracją platformy Docker; Twórz aplikacje w językach JavaScript, Python, .NET, PHP, Java i Node.js; Twórz zaplecza dla urządzeń z systemem iOS, Android i Windows. Usługa w chmurze platformy Azure obsługuje takie same technologie jak miliony deweloperów i specjalistów IT.

Podczas kompilowania lub migrowania zasobów IT do programu dostawca usług w chmurze publicznej korzysta z możliwości organizacji w zakresie ochrony Twoich aplikacji i danych za pomocą usług i kontroli, które zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana od funkcji do aplikacji do hostowania milionów klientów jednocześnie i zapewnia godną zaufania podstawę, w której firmy mogą spełniać wymagania dotyczące zabezpieczeń. Ponadto platforma Azure oferuje szeroką gamę konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, aby można było dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania wdrożeń w organizacji. Ten dokument pomoże Ci zrozumieć, jak funkcje zabezpieczeń platformy Azure mogą pomóc spełnić te wymagania.

### <a name="abstract"></a>Abstract
Zabezpieczenia operacyjne platformy Azure odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów w Microsoft Azure. Zabezpieczenia operacyjne platformy Azure są oparte na strukturze, która obejmuje wiedzę uzyskaną na podstawie różnych możliwości, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center, oraz głębokiej świadomości dotyczącej zagrożeń cyberbezpieczeństwa.

Ten oficjalny dokument przedstawia podejście firmy Microsoft do zabezpieczeń operacyjnych platformy Azure w ramach platformy Microsoft Azure w chmurze i obejmuje następujące usługi:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-introduction.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Obserwator sieci platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Analiza usługi Azure Storage](/rest/api/storageservices/fileservices/storage-analytics)

6.  [Usługa Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Dzienniki monitora Microsoft Azure

Dzienniki monitora Microsoft Azure to rozwiązanie do zarządzania IT w chmurze hybrydowej. Dzienniki Azure Monitor używane samodzielnie lub w celu rozbudowania istniejącego wdrożenia programu System Center oferują maksymalną elastyczność i kontrolę w zakresie zarządzania infrastrukturą opartą na chmurze.

![Dzienniki usługi Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Za pomocą dzienników Azure Monitor można zarządzać dowolnym wystąpieniem w dowolnej chmurze, w tym lokalnym, platformą Azure, AWS, Windows Server, Linux, VMware i OpenStack, z niższym kosztem niż rozwiązania konkurencyjne. Na świecie, w którym zawarto chmurę w chmurze, Azure Monitor dzienniki oferują nowe podejście do zarządzania przedsiębiorstwem, które jest najszybszym i ekonomicznym sposobem sprostania nowym wyzwaniom biznesowym i uwzględnieniu nowych obciążeń, aplikacji i środowisk chmury.

### <a name="azure-monitor-services"></a>Usługi Azure Monitor

Podstawowe funkcje dzienników Azure Monitor są udostępniane przez zestaw usług uruchomionych na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

| Usługa  | Opis|
| :------------- | :-------------|
| Dzienniki usługi Azure Monitor | Monitorowanie i analizowanie dostępności oraz wydajności różnych zasobów, łącznie z maszynami fizycznymi i wirtualnymi. |
|Automation | Automatyzowanie procesów ręcznych oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych. |
| Backup | Tworzenie kopii zapasowych i przywracanie kluczowych danych. |
| Site Recovery | Zapewnianie wysokiej dostępności kluczowych aplikacji. |

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) udostępniają usługi monitorowania przez zbieranie danych z zarządzanych zasobów do centralnego repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.


Ta metoda umożliwia konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.


![Diagram przedstawiający konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym.](./media/operational-security/azure-operational-security-fig2.png)

Usługa Azure Monitor umożliwia bezpieczne zarządzanie danymi opartymi na chmurze przy użyciu następujących metod:
-   podział danych
-   przechowywanie danych
-   zabezpieczenia fizyczne
-   zarządzanie zdarzeniami
-   przepisów
-   Certyfikaty standardów zabezpieczeń

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) zapewnia usługi tworzenia kopii zapasowych i przywracania danych oraz jest częścią Azure monitor pakietu produktów i usług.
Chroni ona dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych. Umożliwia tworzenie kopii zapasowych danych z serwerów fizycznych i wirtualnych systemu Windows, a także obciążeń aplikacji, takich jak SQL Server i SharePoint. Może być również używany przez [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) do replikowania chronionych danych na platformę Azure w celu zapewnienia nadmiarowości i długoterminowego przechowywania.


Chronione dane w usłudze Azure Backup są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym. Dane są replikowane w tym samym regionie i, w zależności od typu magazynu, mogą być również replikowane do innego regionu w celu uzyskania dalszej odporności.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Azure monitor](../../security-center/security-center-introduction.md) to oparte na chmurze rozwiązanie do zarządzania działem IT firmy Microsoft, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.


[Rozwiązania do zarządzania](../../azure-monitor/insights/solutions.md) to zestawy logiki, które implementują konkretny scenariusz zarządzania przy użyciu co najmniej jednej Azure monitor usług. Dostępne są różne rozwiązania firmy Microsoft i partnerów, które można łatwo dodać do subskrypcji platformy Azure, aby zwiększyć wartość inwestycji w Azure Monitor. Jako partner możesz tworzyć własne rozwiązania do obsługi aplikacji i usług oraz udostępniać je użytkownikom za pomocą witryny Azure Marketplace lub szablonów szybkiego startu.


![Rozwiązania do zarządzania](./media/operational-security/azure-operational-security-fig4.png)

Dobrym przykładem rozwiązania korzystającego z wielu usług w celu zapewnienia dodatkowej funkcjonalności jest [rozwiązanie Update Management](../../automation/update-management/overview.md). To rozwiązanie używa agenta [dzienników Azure monitor](../../azure-monitor/logs/log-query-overview.md) dla systemów Windows i Linux w celu zbierania informacji na temat wymaganych aktualizacji na każdym agencie. Zapisuje te dane w repozytorium dzienników Azure Monitor, w którym można je analizować za pomocą dołączonego pulpitu nawigacyjnego.

Podczas tworzenia wdrożenia elementy Runbook w [Azure Automation](../../automation/automation-intro.md) są używane do instalowania wymaganych aktualizacji. Możesz zarządzać całym procesem w portalu, dzięki czemu nie musisz przejmować się szczegółami.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomaga chronić zasoby platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. W ramach usługi można definiować zasady nie tylko dla subskrypcji platformy Azure, ale również dla [grup zasobów](../../azure-resource-manager/management/overview.md#resource-groups), dzięki czemu można być bardziej szczegółowym.

### <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia w tym zakresie

Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w określonej subskrypcji lub grupie zasobów.

W usłudze Security Center możesz zdefiniować zasady zgodnie z wymaganiami w zakresie zabezpieczeń firmy oraz typem aplikacji lub stopniem poufności danych.

![Zasady zabezpieczeń i zalecenia w tym zakresie](./media/operational-security/azure-operational-security-fig5.png)


Zasady włączone na poziomie subskrypcji są automatycznie propagowane do wszystkich grup zasobów w ramach subskrypcji, jak pokazano na diagramie po prawej stronie:


### <a name="data-collection"></a>Zbieranie danych

Usługa Security Center zbiera dane z maszyn wirtualnych w celu oceny ich stanu zabezpieczeń, przekazywania zaleceń dotyczących zabezpieczeń oraz alertów dotyczących zagrożeń. Gdy pierwszy Security Center dostępu, zbieranie danych jest włączone na wszystkich maszynach wirtualnych w ramach subskrypcji. Zaleca się zbieranie danych, ale możesz zrezygnować z niego, wyłączając zbieranie danych w zasadach usługi Security Center.

### <a name="data-sources"></a>Źródła danych

- Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

-   Usługi platformy Azure: używa informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.

- Ruch sieciowy: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy i docelowy adres IP, źródłowy i docelowy port, rozmiar pakietu i protokół sieciowy.

-   Rozwiązania partnerów: używa alertów zabezpieczeń z rozwiązań zintegrowanych partnerów, takich jak zapory i rozwiązania do ochrony przed złośliwym oprogramowaniem.

-   Maszyny wirtualne: używa informacji dotyczących konfiguracji oraz zdarzeń związanych z zabezpieczeniami, takich jak zdarzenia systemu Windows i dzienniki inspekcji, dzienniki programu IIS, komunikaty dziennika systemu i pliki zrzutu awaryjnego, z maszyn wirtualnych.

### <a name="data-protection"></a>Ochrona danych

Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, usługa Azure Security Center zbiera i przetwarza dane dotyczące zabezpieczeń, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń, pliki zrzutu awaryjnego i inne. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

-   **Podział danych**: dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

-   **Dostęp do danych**: aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym plików zrzutu awaryjnego, zdarzeń tworzenia procesów, migawek dysków maszyny wirtualnej i artefaktów, co może przypadkowo obejmować dane klienta lub dane osobowe z maszyn wirtualnych. Stosujemy się do [warunków i zasad zachowania poufności informacji w usługach online firmy Microsoft](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), co oznacza, że firma Microsoft nie korzysta z danych klientów ani nie udostępnia informacji z nich na potrzeby reklamy lub podobnych celów komercyjnych.

-   **Użycie danych**: firma Microsoft używa wzorców i analizy zagrożeń obecnych w wielu dzierżawach, aby zwiększyć możliwości wykrywania zagrożeń i zapobiegania im — robimy to zgodnie ze zobowiązaniami co do prywatności opisanymi w [zasadach zachowania poufności informacji](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Lokalizacja danych

Usługa Azure Security Center zbiera efemeryczne kopie plików zrzutu awaryjnego i analizuje je pod kątem dowodów na próby ich naruszenia i pomyślnie przeprowadzonych ataków. Usługa Azure Security Center dokonuje tej analizy w ramach tego samego obszaru geograficznego, co obszar roboczy, i usuwa efemeryczne kopie po zakończeniu analizy. Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.

-   **Konta magazynu**: konto magazynu jest określone dla każdego regionu, w którym działają maszyny wirtualne. Dzięki temu można przechowywać dane w tym samym regionie co maszyna wirtualna, z której zbierane są dane.

-   **Magazyn usługi Azure Security Center Storage**: informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, zalecenia oraz stan kondycji zabezpieczeń, są przechowywane centralnie, obecnie na terenie Stanów Zjednoczonych. Do informacji zebranych z maszyn wirtualnych mogą należeć informacje dotyczące konfiguracji oraz zdarzeń związanych z zabezpieczeniami, dzięki którym możliwe jest przekazanie użytkownikowi danych na temat alertu zabezpieczeń, ewentualnych zaleceń postępowania oraz stanu kondycji zabezpieczeń.


## <a name="azure-monitor"></a>Azure Monitor

[Azure monitor rejestruje rozwiązanie zabezpieczenia](../../security-center/security-center-monitoring.md) i Inspekcja umożliwia działowi IT aktywne monitorowanie wszystkich zasobów, co może pomóc zminimalizować wpływ zdarzeń zabezpieczeń. Dzienniki Azure Monitor Security and Audit mają domeny zabezpieczeń, których można używać do monitorowania zasobów. Domena zabezpieczeń zapewnia szybki dostęp do opcji, aby monitorować zabezpieczenia następujące domeny zostały omówione bardziej szczegółowo:

-   Ocena złośliwego oprogramowania
-   Ocena aktualizacji
-   Tożsamość i dostęp.

Azure Monitor zawiera wskaźniki do informacji o określonych typach zasobów. Oferuje wizualizacje, zapytania, routing, alerty, automatyczne skalowanie i automatyzację danych zarówno z infrastruktury platformy Azure (Dziennik aktywności), jak i poszczególnych zasobów platformy Azure (dzienników diagnostycznych).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja będzie działać w dobrej kondycji. Pomaga również w staveniu potencjalnych problemów lub rozwiązywaniu problemów z poprzednimi.

Ponadto możesz użyć monitorowania danych, aby uzyskać szczegółowe informacje o aplikacji. Ta wiedza może pomóc w zwiększeniu wydajności aplikacji lub utrzymaniu lub zautomatyzowaniu działań, które w przeciwnym razie wymagają ręcznej interwencji.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure


Jest to dziennik zawierający szczegółowe informacje o operacjach wykonywanych na zasobach w ramach subskrypcji. Dziennik aktywności był wcześniej znany jako "dzienniki inspekcji" lub "dzienniki operacyjne", ponieważ zgłasza zdarzenia płaszczyzny kontroli dla subskrypcji.

![Dziennik aktywności platformy Azure](./media/operational-security/azure-operational-security-fig7.png)

Za pomocą dziennika aktywności można określić, kto i kiedy "w przypadku operacji zapisu (PUT, POST, DELETE) wykonanych dla zasobów w subskrypcji. Można także zrozumieć stan operacji i inne istotne właściwości. Dziennik aktywności nie obejmuje operacji odczytu (GET) ani operacji dla zasobów, które korzystają z modelu klasycznego.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Te dzienniki są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Zawartość tych dzienników różni się w zależności od typu zasobu.

Na przykład Dzienniki systemu zdarzeń systemu Windows są jedną kategorią dziennika diagnostycznego dla maszyn wirtualnych i dzienników obiektów blob, tabel i kolejek są kategorie dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności (dawniej zwanego dziennikiem inspekcji lub dziennikiem operacji)](../../azure-monitor/essentials/platform-logs-overview.md). Dziennik aktywności zawiera szczegółowe informacje o operacjach wykonywanych na zasobach w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez sam zasób.

### <a name="metrics"></a>Metryki

Azure Monitor umożliwia korzystanie z telemetrii w celu uzyskania wglądu w wydajność i kondycję obciążeń na platformie Azure. Najważniejszym typem danych telemetrii platformy Azure jest metryki (nazywane również licznikami wydajności) emitowane przez większość zasobów platformy Azure. Azure Monitor oferuje kilka sposobów konfigurowania i używania tych [metryk](../../azure-monitor/data-platform.md) do monitorowania i rozwiązywania problemów. Metryki są cennym źródłem danych telemetrycznych i umożliwiają wykonywanie następujących zadań:

-   **Śledź wydajność** zasobów (takich jak maszyna wirtualna, witryna sieci Web lub aplikacja logiki) przez wykreślenie jej metryk na wykresie portalu i Przypinanie tego wykresu do pulpitu nawigacyjnego.

-   **Otrzymuj powiadomienia o problemie** , który ma wpływ na wydajność zasobu, gdy Metryka przekroczy określony próg.

-   **Skonfiguruj akcje automatyczne**, takie jak automatyczne skalowanie zasobu lub wyzwalanie elementu Runbook, gdy Metryka przekroczy określony próg.

-   **Wykonaj zaawansowaną analizę** lub Raportowanie dotyczące trendów wydajności i użycia zasobów.

-   **Archiwizuj** historię wydajności lub kondycji zasobu w celu zapewnienia zgodności lub inspekcji.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Jest to możliwość na platformie Azure, która umożliwia zbieranie danych diagnostycznych we wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych różnych źródeł. Obecnie obsługiwane są [role sieci Web i procesu roboczego usługi w chmurze platformy Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [platforma Azure Virtual Machines](../../virtual-machines/windows/overview.md) z systemem Microsoft Windows i [Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md). Inne usługi platformy Azure mają własną osobną diagnostykę.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Inspekcja zabezpieczeń sieci jest konieczna do wykrywania luk w zabezpieczeniach sieci i zapewnienia zgodności z modelem nadzoru obowiązującym w zakresie bezpieczeństwa i zarządzania. Widok grupy zabezpieczeń umożliwia pobranie skonfigurowanej sieciowej grupy zabezpieczeń i reguł zabezpieczeń oraz obowiązujących reguł zabezpieczeń. Mając listę zastosowanych reguł, możesz określić porty, które są otwarte i oceniają lukę w zabezpieczeniach sieci.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci w, do i z platformy Azure. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. Ta usługa obejmuje funkcję przechwytywania pakietów, następny przeskok, sprawdzenie przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie poziomu scenariusza zapewnia kompleksowy wgląd w zasoby sieciowe w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher obecnie ma następujące możliwości:

-   **<a href="/azure/network-watcher/network-watcher-monitoring-overview">Dzienniki inspekcji</a>**— operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki można wyświetlać w Azure Portal lub pobrać przy użyciu narzędzi firmy Microsoft, takich jak Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia i API REST. Aby uzyskać więcej informacji na temat dzienników inspekcji, zobacz operacje inspekcji z Menedżer zasobów. Dzienniki inspekcji są dostępne dla operacji wykonywanych na wszystkich zasobach sieciowych.


-   **<a href="/azure/network-watcher/network-watcher-ip-flow-verify-overview">Sprawdzanie przepływów IP</a>** — sprawdza, czy pakiet jest dozwolony, czy odrzucany na podstawie informacji o przepływie 5-parametry pakietu (docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń sieci, zwracana jest reguła i sieciowa Grupa zabezpieczeń, która odrzuciła pakiet.

-   **<a href="/azure/network-watcher/network-watcher-next-hop-overview">Następny przeskok</a>** — określa następny przeskok dla pakietów przesyłanych w sieci szkieletowej platformy Azure, dzięki czemu można zdiagnozować wszelkie nieprawidłowo skonfigurowane trasy zdefiniowane przez użytkownika.

-   **<a href="/azure/network-watcher/network-watcher-security-group-view-overview">Widok grupy zabezpieczeń</a>** — pobiera obowiązujące i stosowane reguły zabezpieczeń stosowane na maszynie wirtualnej.

-   **<a href="/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Rejestrowanie przepływu sieciowej grupy zabezpieczeń</a>** — dzienniki przepływu dla sieciowych grup zabezpieczeń umożliwiają przechwytywanie dzienników związanych z ruchem, które są dozwolone lub odrzucane przez reguły zabezpieczeń w grupie. Przepływ jest definiowany przy użyciu 5-informacje o spójnej kolekcji — źródłowy adres IP, docelowy adres IP, port źródłowy, port docelowy i protokół.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Analityka magazynu](/rest/api/storageservices/fileservices/storage-analytics) mogą przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryk umożliwiają analizowanie użycia usługi magazynu, diagnozowanie problemów z żądaniami dotyczącymi usługi Storage oraz Poprawianie wydajności aplikacji korzystających z usługi.

[Azure Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia dane metryk dla konta magazynu. Za pomocą tych danych można śledzić żądania, analizować trendy użycia i diagnozować problemy z kontem magazynu. Rejestrowanie analityka magazynu jest dostępne dla [usług obiektów blob, kolejek i tabel](../../storage/common/storage-introduction.md). Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem.

Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku. Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w swoim punkcie końcowym obiektu BLOB, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki dotyczące Blob service.

Aby użyć analityka magazynu, należy włączyć ją indywidualnie dla każdej usługi, którą chcesz monitorować. Można ją włączyć w [Azure Portal](https://portal.azure.com/); Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w Azure Portal](../../storage/common/storage-monitor-storage-account.md). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji ustaw właściwości usługi, aby włączyć analityka magazynu indywidualnie dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanym obiekcie BLOB (na potrzeby rejestrowania) i w dobrze znanych tabelach (dla metryk), do których można uzyskać dostęp za pomocą interfejsów API Blob service i Table service.

Analityka magazynu ma limit 20 TB ilości przechowywanych danych, które są niezależne od łącznego limitu dla konta magazynu. Wszystkie dzienniki są przechowywane w [blokowych](../../storage/common/storage-analytics.md) obiektach Blob w kontenerze o nazwie $logs, które są tworzone automatycznie po włączeniu analityka magazynu dla konta magazynu.

Następujące akcje wykonywane przez analityka magazynu są rozliczane:

-   Żądania tworzenia obiektów BLOB do rejestrowania
-   Żądania utworzenia jednostek tabeli dla metryk.

> [!Note]
> Aby uzyskać więcej informacji na temat zasad dotyczących rozliczeń i przechowywania danych, zobacz [analityka magazynu i rozliczanie](/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> W celu uzyskania optymalnej wydajności należy ograniczyć liczbę dysków o wysokiej dostępności podłączonych do maszyny wirtualnej, aby uniknąć możliwego ograniczenia przepustowości. Jeśli wszystkie dyski nie są w wysokim stopniu wykorzystywane, konto magazynu może obsługiwać większy dysk.

> [!Note]
> Aby uzyskać więcej informacji na temat limitów kont magazynu, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md).


Rejestrowane są następujące typy żądań uwierzytelnionych i anonimowych.

| Uwierzytelnione  | Anonimowe|
| :------------- | :-------------|
| Żądania zakończone powodzeniem | Żądania zakończone powodzeniem |
|Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy | Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądań zakończonych niepowodzeniem i zakończonych pomyślnie |
| Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądań zakończonych niepowodzeniem i zakończonych pomyślnie |Błędy przekroczenia limitu czasu dla klienta i serwera |
|   Żądania dotyczące danych analityki |    Żądania GET zakończone niepowodzeniem z kodem błędu 304 (Nie zmodyfikowano) |
| Żądania wykonywane przez analityka magazynu samego siebie, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w temacie [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [formacie dziennika analityka magazynu](/rest/api/storageservices/fileservices/storage-analytics-log-format) . | Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w [analityka magazynu zarejestrowanych operacji i komunikatów o stanie](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) oraz w [formacie dziennika analityka magazynu](/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Usługa Azure AD zawiera również pełen zestaw funkcji zarządzania tożsamościami, w tym uwierzytelnianie wieloskładnikowe, rejestrację urządzeń, Samoobsługowe zarządzanie hasłami, Samoobsługowe zarządzanie grupami, zarządzanie kontami uprzywilejowanymi, kontrola dostępu oparta na rolach, monitorowanie użycia aplikacji, zaawansowaną inspekcję oraz monitorowanie zabezpieczeń i alerty.

-   Zwiększ bezpieczeństwo aplikacji za pomocą uwierzytelniania wieloskładnikowego usługi Azure AD i dostępu warunkowego.

-   Monitoruj użycie aplikacji i Chroń swoją firmę przed zaawansowanymi zagrożeniami Dzięki raportowaniu i monitorowaniu zabezpieczeń.

W usłudze Azure Active Directory (Azure AD) uwzględniono raporty dotyczące zabezpieczeń, działań i inspekcji dla katalogu. [Raport inspekcji Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) pomaga klientom identyfikować uprzywilejowane akcje, które wystąpiły w ich Azure Active Directory. Akcje uprzywilejowane obejmują zmiany podniesienia uprawnień (na przykład tworzenie ról lub resetowanie haseł), Zmienianie konfiguracji zasad (na przykład zasad haseł) lub zmiany konfiguracji katalogu (na przykład zmiany w ustawieniach federacyjnych domen).

Raporty zawierają rekord inspekcji dla nazwy zdarzenia, aktora, który wykonał akcję, zasób docelowy, na który ma wpływ zmiana, oraz datę i godzinę (w formacie UTC). Klienci mogą pobrać listę zdarzeń inspekcji dla ich Azure Active Directory za pośrednictwem [Azure Portal](https://portal.azure.com/), zgodnie z opisem w temacie [Wyświetlanie dzienników inspekcji](../../active-directory/reports-monitoring/overview-reports.md). Oto lista uwzględnionych raportów:

| Raporty dotyczące zabezpieczeń  | Raporty dotyczące działań| Raporty dotyczące inspekcji |
| :------------- | :-------------| :-------------|
|Logowania z nieznanych źródeł | Podsumowanie użycia aplikacji | Raport dotyczący inspekcji katalogu |
|Logowania po wielokrotnych niepowodzeniach | Szczegóły użycia aplikacji |   |
|Logowania z wielu lokalizacji geograficznych | Pulpit nawigacyjny aplikacji |  |
|Logowania z adresów IP związanych z podejrzanymi działaniami |Błędy aprowizacji kont |  |
|Nieregularne działania związane z logowaniem |Urządzenia indywidualnych użytkowników |  |
|Logowania z urządzeń, które mogą być zainfekowane |Działania indywidualnych użytkowników |   |
|Nietypowe działania użytkowników związane z logowaniem |Raport dotyczący działań grup |   |
| |Raport dotyczący działań związanych z rejestracją resetowania haseł |   |
| |Działania związane z resetowaniem haseł |   |



Dane tych raportów mogą być przydatne dla aplikacji, takich jak systemy SIEM, Inspekcja i narzędzia analizy biznesowej. [Interfejsy API](../../active-directory/reports-monitoring/concept-reporting-api.md) raportowania usługi Azure AD zapewniają programistyczny dostęp do danych za pośrednictwem zestawu interfejsów API opartych na interfejsie REST. Te interfejsy API można wywoływać za pomocą różnych języków programowania i narzędzi.

Zdarzenia w raporcie inspekcji usługi Azure AD są przechowywane przez 180 dni.

> [!Note]
> Aby uzyskać więcej informacji o przechowywaniu w raportach, zobacz [Azure Active Directory zasad przechowywania raportów](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

W przypadku klientów zainteresowanych przechowywaniem [zdarzeń inspekcji](../../active-directory/reports-monitoring/concept-audit-logs.md) dla dłuższych okresów przechowywania można użyć interfejsu API raportowania do regularnego ściągania zdarzeń inspekcji do oddzielnego magazynu danych.

## <a name="summary"></a>Podsumowanie

Ten artykuł podsumowuje ochronę prywatności i zabezpieczanie danych przy jednoczesnym dostarczaniu oprogramowania i usług, które ułatwiają zarządzanie infrastrukturą IT w organizacji. Firma Microsoft uznaje, że w przypadku powierzenia ich danych innym osobom zaufanie wymaga rygorystycznych zabezpieczeń. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi. Zabezpieczanie i ochrona danych to najwyższy priorytet w firmie Microsoft.

W tym artykule opisano

-   Sposób zbierania, przetwarzania i zabezpieczania danych w zestawie Azure Monitor.

-   Szybko Analizuj zdarzenia w wielu źródłach danych. Zidentyfikuj zagrożenia bezpieczeństwa i poznanie zakresu i wpływu zagrożeń i ataków, aby wyeliminować szkody związane z naruszeniem zabezpieczeń.

-   Identyfikuj wzorce ataków dzięki wizualizacji wychodzącego złośliwego ruchu IP i typów złośliwych zagrożeń. Zapoznaj się z stan zabezpieczeń całego środowiska, niezależnie od platformy.

-   Przechwyć wszystkie dane dziennika i zdarzenia wymagane do inspekcji zabezpieczeń lub zgodności. Umożliwia przekroczenie czasu i zasobów wymaganych do zapewnienia inspekcji zabezpieczeń z pełnym, możliwym do przeszukiwania i eksportowanym zestawem danych.

<ul>
<li>Zbierz zdarzenia związane z zabezpieczeniami, inspekcję i analizę naruszeń, aby zachować bliskie oczy zasobów:</li>
<ul>
<li>Stan zabezpieczeń</li>
<li>Istotny problem</li>
<li>Podsumowuje zagrożenia</li>
</ul>
</ul>

## <a name="next-steps"></a>Następne kroki

- [Bezpieczeństwo projektowania i działania](https://www.microsoft.com/trustcenter/security/designopsecurity)

Firma Microsoft projektuje swoje usługi i oprogramowanie z uwzględnieniem zabezpieczeń, aby zapewnić odporność i ochronę infrastruktury chmurowej przed atakami.

- [Dzienniki Azure Monitor | Zabezpieczenia & zgodności](https://www.microsoft.com/cloud-platform/security-and-compliance)

Użyj danych i analizy zabezpieczeń firmy Microsoft, aby przeprowadzić bardziej inteligentne i efektywne wykrywanie zagrożeń.

- [Azure Security Center planowanie i operacje](../../security-center/security-center-planning-and-operations-guide.md) Zestaw kroków i zadań, które można wykonać, aby zoptymalizować korzystanie z Security Center na podstawie wymagań dotyczących zabezpieczeń i modelu zarządzania chmurą w organizacji.