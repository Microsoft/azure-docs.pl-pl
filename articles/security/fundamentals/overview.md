---
title: Wprowadzenie do zabezpieczeń platformy Azure | Microsoft Docs
description: Zapoznaj się z informacjami o zabezpieczeniach platformy Azure, jej różnych usługach i sposobach działania, odczytując ten przegląd.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: 1159736d59e15fa36c0feb7e5d5b5553953fb6b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564515"
---
# <a name="introduction-to-azure-security"></a>Wprowadzenie do zabezpieczeń platformy Azure

## <a name="overview"></a>Omówienie

Wiemy, że zabezpieczenia to zadanie jedno w chmurze i jak ważne jest, aby znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jednym z najlepszych powodów używania platformy Azure dla aplikacji i usług jest skorzystanie z zalet szerokiej gamy narzędzi i możliwości zabezpieczeń. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Microsoft Azure zapewnia poufność, integralność i dostępność danych klientów, a także umożliwia przejrzyste odpowiedzialności.

Ten artykuł zawiera szczegółowy opis zabezpieczeń dostępnych w systemie Azure.

### <a name="azure-platform"></a>Platforma Azure

Azure to platforma usług w chmurze publicznej, która obsługuje szeroką gamę systemów operacyjnych, języków programowania, struktur, narzędzi, baz danych i urządzeń. Może uruchamiać kontenery systemu Linux z integracją platformy Docker; Twórz aplikacje w językach JavaScript, Python, .NET, PHP, Java i Node.js; Twórz zaplecza dla urządzeń z systemem iOS, Android i Windows.

Usługi w chmurze publicznej platformy Azure obsługują te same technologie, które miliony deweloperów i specjalistów IT są już zależne i ufają. Podczas kompilowania lub migrowania zasobów IT do programu dostawca usług w chmurze publicznej korzysta z możliwości organizacji w zakresie ochrony Twoich aplikacji i danych za pomocą usług i kontroli, które zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana od funkcji do aplikacji do hostowania milionów klientów jednocześnie i zapewnia godną zaufania podstawę, w której firmy mogą spełniać wymagania dotyczące zabezpieczeń.

Ponadto platforma Azure oferuje szeroką gamę konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, aby można było dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania wdrożeń w organizacji. Ten dokument ułatwia zrozumienie sposobu, w jaki funkcje zabezpieczeń platformy Azure mogą pomóc spełnić te wymagania.

> [!Note]
> Głównym celem tego dokumentu jest użycie kontrolek ukierunkowanych na klienta, za pomocą których można dostosować i zwiększyć bezpieczeństwo aplikacji i usług.
>
> Aby uzyskać informacje na temat sposobu, w jaki firma Microsoft chroni platformę Azure, zobacz [zabezpieczenia infrastruktury platformy Azure](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Podsumowanie możliwości zabezpieczeń platformy Azure

W zależności od modelu usług w chmurze istnieje zmienna odpowiedzialność za zarządzanie zabezpieczeniami aplikacji lub usługi. Na platformie Azure dostępne są funkcje, które ułatwiają spełnienie tych obowiązków dzięki wbudowanym funkcjom i rozwiązaniom partnerskim, które można wdrożyć w ramach subskrypcji platformy Azure.

Wbudowane funkcje są zorganizowane w sześciu obszarach funkcjonalnych: operacje, aplikacje, magazyn, Sieć, obliczenia i tożsamość. Dodatkowe szczegóły dotyczące funkcji i możliwości dostępnych na platformie Azure w tych sześciu obszarach są udostępniane za pomocą informacji podsumowujących.

## <a name="operations"></a>Operacje

Ta sekcja zawiera dodatkowe informacje dotyczące najważniejszych funkcji zabezpieczeń i informacji podsumowujących dotyczących tych funkcji.

### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Ponadto Security Center ułatwia wykonywanie operacji związanych z zabezpieczeniami przez udostępnienie jednego pulpitu nawigacyjnego, na którym są wyświetlane alerty i zalecenia, które mogą być natychmiast przetwarzane. Często można skorygować problemy za pomocą jednego kliknięcia w konsoli Security Center.

### <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwia współpracę z zasobami w rozwiązaniu jako Grupa. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Do wdrożenia należy używać [szablonu Azure Resource Manager](../../azure-resource-manager/templates/overview.md) i ten szablon może współpracować z różnymi środowiskami, takimi jak testowanie, przemieszczanie i produkcja. Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające zarządzanie zasobami po wdrożeniu.

Azure Resource Manager wdrożenia oparte na szablonach pomagają ulepszyć zabezpieczenia rozwiązań wdrożonych na platformie Azure ze względu na standardowe ustawienia kontroli zabezpieczeń i można je zintegrować w ustandaryzowanych wdrożeniach opartych na szablonach. Zmniejsza to ryzyko błędów konfiguracji zabezpieczeń, które mogą wystąpić podczas wdrożeń ręcznych.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web. Za pomocą Application Insights można monitorować dynamiczne aplikacje sieci Web i automatycznie wykrywać anomalie wydajności. Zawiera ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie, jakie użytkownicy faktycznie robią z aplikacjami. Monitoruje aplikację przez cały czas jej działania, zarówno podczas testowania, jak i po jej opublikowaniu lub wdrożeniu.

Application Insights tworzy wykresy i tabele, które pokazują Ci, na przykład pory dnia, w jaki każdy dzień otrzymuje większość użytkowników, jak reaguje na aplikację i jak jest obsługiwany przez wszystkie zewnętrzne usługi, od których zależy.

W przypadku awarii, błędów lub problemów z wydajnością można wyszukać szczegółowe dane telemetryczne, aby zdiagnozować przyczynę. A usługa wysyła wiadomości e-mail, jeśli istnieją jakiekolwiek zmiany w zakresie dostępności i wydajności aplikacji. Usługa Application Insights w ten sposób będzie cennym narzędziem zabezpieczeń, ponieważ ułatwia dostęp do Triad zabezpieczeń, integralności i dostępności.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/monitoring-and-diagnostics/) oferuje wizualizacje, zapytania, routing, alerty, automatyczne skalowanie i automatyzację danych zarówno z subskrypcji platformy Azure ([Dziennik aktywności](../../azure-monitor/essentials/platform-logs-overview.md)), jak i poszczególnych zasobów platformy Azure ([dzienników zasobów](../../azure-monitor/essentials/platform-logs-overview.md)). Za pomocą Azure Monitor można ostrzec o zdarzeniach związanych z zabezpieczeniami, które są generowane w dziennikach platformy Azure.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](../../azure-monitor/logs/log-query-overview.md) — udostępnia rozwiązanie do zarządzania IT dla infrastruktury lokalnej i opartej na chmurze innej firmy (na przykład AWS) oprócz zasobów platformy Azure. Dane z Azure Monitor mogą być kierowane bezpośrednio do dzienników Azure Monitor, dzięki czemu można zobaczyć metryki i dzienniki dla całego środowiska w jednym miejscu.

Dzienniki Azure Monitor mogą być użytecznym narzędziem w śledczej i inne analizy zabezpieczeń, ponieważ narzędzie umożliwia szybkie przeszukiwanie dużych ilości wpisów związanych z zabezpieczeniami przy użyciu elastycznego podejścia zapytania. Ponadto na [platformie Azure można eksportować lokalne Dzienniki zapory i serwera proxy i udostępnić je do analizy przy użyciu dzienników Azure monitor.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) to spersonalizowany konsultant ds. chmury, który pomaga zoptymalizować wdrożenia platformy Azure. Analizuje on konfigurację zasobów i dane telemetryczne użycia. Następnie zaleca rozwiązania pomagające ulepszyć [wydajność](../../advisor/advisor-performance-recommendations.md), [bezpieczeństwo](../../advisor/advisor-security-recommendations.md)i [niezawodność](../../advisor/advisor-high-availability-recommendations.md) zasobów, a jednocześnie poszukuje możliwości [zredukowania ogólnych wydatków na platformę Azure](../../advisor/advisor-cost-recommendations.md). Azure Advisor zawiera zalecenia dotyczące zabezpieczeń, co może znacząco poprawić ogólne stan zabezpieczeń dla rozwiązań wdrażanych na platformie Azure. Te zalecenia są rysowane na podstawie analizy zabezpieczeń wykonywanej przez [Azure Security Center.](../../security-center/security-center-introduction.md)

## <a name="applications"></a>Aplikacje

Sekcja zawiera dodatkowe informacje dotyczące najważniejszych funkcji zabezpieczeń aplikacji oraz informacje podsumowujące dotyczące tych możliwości.

### <a name="web-application-vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach aplikacji sieci Web

Jednym z najprostszych sposobów rozpoczęcia testowania pod kątem luk w zabezpieczeniach [aplikacji App Service](../../app-service/overview.md) jest użycie [integracji z zabezpieczeniami usługa TINFOIL](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) w celu wykonania skanowania w aplikacji za pomocą jednego kliknięcia. Możesz wyświetlić wyniki testów w łatwym w zrozumieniu raporcie i dowiedzieć się, jak rozwiązać każdą lukę w instrukcje krok po kroku.

### <a name="penetration-testing"></a>Testy penetracyjne

Nie przeprowadzimy [testów](./pen-testing.md) dla aplikacji, ale rozumiemy, że chcesz, aby przetestować swoje aplikacje. Jest to dobry efekt, ponieważ w przypadku zwiększenia bezpieczeństwa aplikacji pomaga zwiększyć bezpieczeństwo całego ekosystemu platformy Azure. Podczas powiadamiania firmy Microsoft o działaniach związanych z testowaniem pióra nie jest już wymagane, aby klienci musieli przestrzegać [Microsoft Cloud reguł testowania penetracji](https://www.microsoft.com/msrc/pentest-rules-of-engagement).

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web

Zapora aplikacji sieci Web (WAF) na [platformie Azure Application Gateway](../../application-gateway/features.md#web-application-firewall) pomaga chronić aplikacje sieci Web przed typowymi atakami opartymi na sieci Web, takimi jak iniekcja SQL, ataki skryptów między lokacjami i przejmowanie sesji. Jest on wstępnie skonfigurowany z ochroną przed zagrożeniami określonymi przez [projekt Open Web Application Security Project (OWASP) jako 10 najczęściej spotykanych luk w](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)zabezpieczeniach.

### <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service

[App Service uwierzytelnianie/autoryzacja](../../app-service/overview-authentication-authorization.md) to funkcja, która umożliwia aplikacji Logowanie użytkowników w taki sposób, aby nie trzeba było zmieniać kodu w zapleczu aplikacji. Zapewnia łatwy sposób ochrony aplikacji i pracy z danymi poszczególnych użytkowników.

### <a name="layered-security-architecture"></a>Architektura zabezpieczeń warstwowych

Ponieważ [środowiska App Service](../../app-service/environment/app-service-app-service-environment-intro.md) udostępniają izolowane środowisko uruchomieniowe wdrożone w usłudze [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md), deweloperzy mogą tworzyć warstwową architekturę zabezpieczeń zapewniającą różne poziomy dostępu do sieci dla każdej warstwy aplikacji. Typowym pragnieniem jest ukrycie zaplecza interfejsu API z ogólnego dostępu do Internetu i Zezwalanie na wywoływanie interfejsów API przez nadrzędne aplikacje sieci Web. [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) mogą być używane w podsieciach platformy Azure Virtual Network zawierającej środowiska App Service, aby ograniczyć dostęp publiczny do aplikacji interfejsu API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostyka serwera sieci Web i Diagnostyka aplikacji
[App Service Web Apps](../../app-service/troubleshoot-diagnostic-logs.md) udostępnia funkcje diagnostyczne do rejestrowania informacji z serwera sieci Web i aplikacji sieci Web. Są one logicznie rozdzielone na diagnostykę serwera sieci Web i diagnostykę aplikacji. Serwer sieci Web zawiera dwa główne postępy w diagnozowaniu i rozwiązywaniu problemów z witrynami i aplikacjami.

Pierwsza Nowa funkcja to informacje o stanie w czasie rzeczywistym o pulach aplikacji, procesach roboczych, lokacjach, domenach aplikacji i uruchomionych żądaniach. Drugie nowe zalety to szczegółowe zdarzenia śledzenia, które śledzą żądanie w trakcie całego procesu żądania i odpowiedzi.

Aby włączyć zbieranie tych zdarzeń śledzenia, można skonfigurować usługi IIS 7 do automatycznego przechwytywania pełnych dzienników śledzenia w formacie XML dla każdego określonego żądania na podstawie kodów czasu, który upłynął lub odpowiedzi na błędy.

## <a name="storage"></a>Storage
Sekcja zawiera dodatkowe informacje dotyczące najważniejszych funkcji zabezpieczeń usługi Azure Storage oraz informacje podsumowujące dotyczące tych możliwości.

### <a name="azure-role-based-access-control-azure-rbac"></a>Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)
Konto magazynu można zabezpieczyć za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Ograniczanie dostępu na podstawie [konieczności znajomości](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zasad zabezpieczeń jest niezbędne dla organizacji, które chcą wymuszać zasady zabezpieczeń dostępu do danych. Te prawa dostępu są przyznawane przez przypisanie odpowiedniej roli platformy Azure do grup i aplikacji w określonym zakresie. Do przypisywania uprawnień użytkownikom można używać [wbudowanych ról platformy Azure](../../role-based-access-control/built-in-roles.md), takich jak współautor konta magazynu. Dostęp do kluczy magazynu dla konta magazynu przy użyciu modelu [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) można kontrolować za pomocą funkcji RBAC platformy Azure.

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](../../storage/common/storage-sas-overview.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Sygnatura dostępu współdzielonego oznacza, że można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Można przyznać te ograniczone uprawnienia bez konieczności udostępniania kluczy dostępu do konta.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Szyfrowanie podczas przesyłania jest mechanizmem ochrony danych przesyłanych między sieciami. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:
- [Szyfrowanie na poziomie transportu](../../storage/blobs/security-recommendations.md), takie jak https, podczas transferu danych do usługi Azure Storage lub z niej.

- [Szyfrowanie przewodowe](../../storage/blobs/security-recommendations.md), takie jak [Szyfrowanie SMB 3,0](../../storage/blobs/security-recommendations.md) dla [udziałów plików platformy Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

- Szyfrowanie po stronie klienta umożliwia szyfrowanie danych przed ich przesłaniem do magazynu oraz odszyfrowanie danych po ich przeniesieniu poza magazyn.

### <a name="encryption-at-rest"></a>Szyfrowanie danych magazynowanych
W przypadku wielu organizacji szyfrowanie danych w spoczynku stanowi obowiązkowy krok w kierunku prywatności, zgodności i suwerenności danych. Istnieją trzy funkcje zabezpieczeń usługi Azure Storage, które zapewniają szyfrowanie danych "w spoczynku":

- [Szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md) pozwala na zażądanie, aby usługa magazynu automatycznie szyfruje dane podczas ich zapisywania do usługi Azure Storage.

- [Szyfrowanie po stronie klienta](../../storage/common/storage-client-side-encryption.md) udostępnia również funkcję szyfrowania w stanie spoczynku.

- [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

### <a name="storage-analytics"></a>Analityka magazynu

[Azure Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia dane metryk dla konta magazynu. Za pomocą tych danych można śledzić żądania, analizować trendy użycia i diagnozować problemy z kontem magazynu. Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku. Rejestrowane są następujące typy żądań uwierzytelnionych:

- Pomyślne żądania.
- Żądania zakończone niepowodzeniem, takie jak limit czasu, ograniczanie przepustowości, Sieć, autoryzacja i inne błędy.
- Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądań zakończonych niepowodzeniem i zakończonych pomyślnie.
- Żądania danych analitycznych.

### <a name="enabling-browser-based-clients-using-cors"></a>Włączanie klientów Browser-Based przy użyciu mechanizmu CORS

[Współużytkowanie zasobów między źródłami (CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) to mechanizm, który umożliwia domenom nadawanie każdemu innemu użytkownikowi uprawnień do uzyskiwania dostępu do wszystkich zasobów. Agent użytkownika wysyła dodatkowe nagłówki, aby upewnić się, że kod JavaScript załadowany z określonej domeny może uzyskać dostęp do zasobów znajdujących się w innej domenie. Druga domena następnie odpowiada za pomocą dodatkowych nagłówków, umożliwiając lub odmówienie dostępu do jego zasobów przez pierwotną domenę.

Usługi Azure Storage obsługują teraz mechanizm CORS, dzięki czemu po ustawieniu reguł CORS dla usługi jest oceniane prawidłowo uwierzytelnione żądanie dotyczące usługi z innej domeny, aby określić, czy jest dozwolone zgodnie z określonymi regułami.

## <a name="networking"></a>Sieć

Sekcja zawiera dodatkowe informacje dotyczące najważniejszych funkcji zabezpieczeń sieci platformy Azure oraz informacje podsumowujące dotyczące tych możliwości.

### <a name="network-layer-controls"></a>Kontrolki warstwy sieciowej

Kontrola dostępu do sieci to czynność ograniczająca łączność do i z określonych urządzeń lub podsieci oraz reprezentuje podstawowe zabezpieczenia sieci. Celem kontroli dostępu do sieci jest upewnienie się, że maszyny wirtualne i usługi są dostępne tylko dla użytkowników i urządzeń, do których mają dostęp.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

[Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) to podstawowa Zapora filtrowania pakietów stanowych, która umożliwia kontrolowanie dostępu na podstawie 5-spójnej kolekcji. Sieciowych grup zabezpieczeń nie zapewniają kontroli warstwy aplikacji ani kontroli dostępu uwierzytelnionego. Mogą one służyć do kontrolowania ruchu przechodzącego między podsieciami w ramach Virtual Network platformy Azure i ruchu między usługą Azure Virtual Network i Internetem.

#### <a name="route-control-and-forced-tunneling"></a>Kontrola trasy i Wymuszone tunelowanie

Możliwość sterowania zachowaniem routingu w sieciach wirtualnych platformy Azure to krytyczna funkcja zabezpieczeń sieci i kontroli dostępu. Na przykład jeśli chcesz upewnić się, że cały ruch do i z Virtual Network platformy Azure odbywa się za pośrednictwem tego wirtualnego urządzenia zabezpieczeń, musisz mieć możliwość kontrolowania i dostosowywania zachowania routingu. Można to zrobić przez skonfigurowanie tras User-Defined na platformie Azure.

[Trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) umożliwiają dostosowanie ścieżek ruchu przychodzącego i wychodzącego dla ruchu przemieszczanego do i z poszczególnych maszyn wirtualnych lub podsieci w celu zapewnienia najbezpieczniejszej możliwej trasy. [Wymuszone tunelowanie](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) jest mechanizmem, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie.

Jest to inne możliwości akceptowania połączeń przychodzących, a następnie reagowanie na nie. Serwery frontonu sieci Web muszą odpowiadać na żądania z hostów internetowych, a więc ruch internetowy jest dozwolony dla ruchu przychodzącego do tych serwerów sieci Web, a serwery sieci Web mogą reagować.

Wymuszone tunelowanie jest często używane do wymuszania ruchu wychodzącego do Internetu w celu przechodzenia przez lokalne serwery proxy zabezpieczeń i zapory.

#### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń Virtual Network

Grupy zabezpieczeń sieci, trasy User-Defined i Wymuszone tunelowanie zapewniają poziom zabezpieczeń w warstwach sieciowych i transportowych [modelu osi](https://en.wikipedia.org/wiki/OSI_model), jednak mogą wystąpić sytuacje, w których chcesz włączyć zabezpieczenia na wyższych poziomach stosu. Dostęp do tych zaawansowanych funkcji zabezpieczeń sieci można uzyskać przy użyciu rozwiązania do ochrony sieci partnerów platformy Azure. Najbardziej aktualne rozwiązania zabezpieczeń sieci partnerów platformy Azure można znaleźć, odwiedzając [witrynę Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukując frazę "zabezpieczenia" i "zabezpieczenia sieci".

### <a name="azure-virtual-network"></a>Azure Virtual Network

Sieć wirtualna Azure odzwierciedla w chmurze Twoją sieć. Jest to logiczna izolacja sieci szkieletowej platformy Azure dedykowanej dla Twojej subskrypcji. W ramach tej sieci można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras. Sieć wirtualną można podzielić na podsieci i umieścić maszyny wirtualne (VM) IaaS platformy Azure i/lub [usługi Cloud Services (PaaS role Instances)](../../cloud-services/cloud-services-choose-me.md) w sieciach wirtualnych platformy Azure.

Dodatkowo można połączyć sieć wirtualną z siecią lokalną przy użyciu jednej z [opcji łączności](../../vpn-gateway/index.yml) dostępnej na platformie Azure. W zasadzie można rozbudować swoją sieć do sieci Azure, zachowując pełną kontrolę nad blokami adresów IP i wykorzystując zapewnianą przez platformę Azure skalowalność klasy korporacyjnej.

Sieć platformy Azure obsługuje różne scenariusze bezpiecznego dostępu zdalnego. Oto niektóre poprawki:

- [Łączenie poszczególnych stacji roboczych z usługą Azure Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [Łączenie sieci lokalnej z usługą Azure Virtual Network przy użyciu sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Łączenie sieci lokalnej z usługą Azure Virtual Network przy użyciu dedykowanego linku sieci WAN](../../expressroute/expressroute-introduction.md)

- [Łączenie sieci wirtualnych platformy Azure ze sobą](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="azure-private-link"></a>Link prywatny platformy Azure

[Link prywatny platformy Azure](https://azure.microsoft.com/services/private-link/) umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerskich i firmowych platformy Azure w sieci wirtualnej za pośrednictwem [prywatnego punktu końcowego](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). Konfiguracja i użycie przy użyciu prywatnego linku platformy Azure jest spójne w ramach usług Azure PaaS, należących do klienta i współużytkowanych partnerów. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej Microsoft Azure.

[Prywatne punkty końcowe](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) umożliwiają zabezpieczenie najważniejszych zasobów usługi platformy Azure tylko do sieci wirtualnych. Prywatny punkt końcowy platformy Azure używa prywatnego adresu IP z sieci wirtualnej, aby połączyć się prywatnie i bezpiecznie do usługi obsługiwanej przez link prywatny platformy Azure, efektywnie udostępniając usługę w sieci wirtualnej. Udostępnienie sieci wirtualnej do publicznego Internetu nie jest już konieczne do korzystania z usług na platformie Azure. 

Możesz również utworzyć własną prywatną usługę linku w sieci wirtualnej. [Usługa link prywatny platformy Azure](https://docs.microsoft.com/azure/private-link/private-link-service-overview) to odwołanie do własnej usługi, która jest obsługiwana przez link prywatny platformy Azure. Usługa, która działa za usługa Load Balancer w warstwie Standardowa platformy Azure, może być włączona do prywatnego dostępu do łączy, dzięki czemu konsumenci usługi mogą uzyskiwać do nich dostęp prywatnie z własnych sieci wirtualnych. Klienci mogą utworzyć prywatny punkt końcowy wewnątrz sieci wirtualnej i zmapować ją na tę usługę. Ujawnienie usługi do publicznej sieci Internet nie jest już konieczne do renderowania usług na platformie Azure. 

### <a name="vpn-gateway"></a>VPN Gateway

Aby wysyłać ruch sieciowy między usługą Azure Virtual Network i lokacją lokalną, musisz utworzyć bramę sieci VPN dla Virtual Network platformy Azure. [Brama VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) to typ bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy przez połączenie publiczne. Za pomocą bram sieci VPN można także wysyłać ruch sieciowy między sieciami wirtualnymi platformy Azure za pośrednictwem sieci szkieletowej platformy Azure.

### <a name="express-route"></a>Express Route

Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) to dedykowany link sieci WAN, który umożliwia poszerzanie sieci lokalnych w chmurze firmy Microsoft przez dedykowane połączenie prywatne obsługiwane przez dostawcę połączenia.

![Express Route](./media/overview/azure-security-figure-1.png)

Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure, Microsoft 365 i CRM Online. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji.

Połączenia ExpressRoute nie przechodzą do publicznej sieci Internet i w ten sposób mogą być uważane za bezpieczniejsze od rozwiązań opartych na sieci VPN. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

### <a name="application-gateway"></a>Application Gateway

[Platforma Microsoft Azure Application Gateway](../../application-gateway/overview.md) udostępnia [kontroler dostarczania aplikacji (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako usługę, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji.

![Application Gateway](./media/overview/azure-security-figure-2.png)

Pozwala to zoptymalizować produktywność farmy sieci Web przez odciążenie zakończenia procesora TLS intensywnie korzystające z Application Gateway (zwanego także "odciążeniem TLS" lub "mostkowanie TLS"). Zapewnia także inne możliwości routingu warstwy 7, w tym rozkład działania ruchu przychodzącego, koligację sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczym Application Gateway. Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7.

Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi.

Aplikacja oferuje wiele funkcji kontrolera dostarczania aplikacji (ADC), w tym Równoważenie obciążenia HTTP, koligację sesji na podstawie plików cookie, [odciążanie protokołu TLS](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), niestandardowe sondy kondycji, obsługę wielu witryn i wiele innych.

### <a name="web-application-firewall"></a>Web Application Firewall

Zapora aplikacji sieci Web to funkcja [platformy Azure Application Gateway](../../application-gateway/overview.md) , która zapewnia ochronę aplikacji sieci Web, które używają usługi Application Gateway do standardowych funkcji kontroli dostarczania aplikacji (ADC). Zapora aplikacji internetowej realizuje ten cel, chroniąc je przed większością z 10 najpopularniejszych luk w zabezpieczeniach w Internecie OWASP.

![Web Application Firewall](./media/overview/azure-security-figure-3.png)

- Ochrona przed atakami polegającymi na iniekcji SQL

- Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

- Ochrona przed naruszeniami protokołu HTTP

- Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

- Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

- Wykrywanie typowych niespotykanych konfiguracji aplikacji (czyli Apache, IIS itp.)

Scentralizowana zapora aplikacji internetowej chroniąca przed atakami z Internetu sprawia, że zarządzanie zabezpieczeniami jest znacznie prostsze oraz zapewnia lepszą ochronę aplikacji przed intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji z zaporą aplikacji internetowej.

### <a name="traffic-manager"></a>Traffic Manager

[Platforma Microsoft Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) umożliwia kontrolowanie dystrybucji ruchu użytkowników dla punktów końcowych usługi w różnych centrach danych. Punkty końcowe usługi obsługiwane przez Traffic Manager obejmują maszyny wirtualne platformy Azure, Web Apps i usługi w chmurze. Usługi Traffic Manager można też użyć z zewnętrznymi punktami końcowymi nienależącymi do platformy Azure. Traffic Manager używa systemu nazw domen (DNS) do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego na podstawie [metody routingu ruchu](../../traffic-manager/traffic-manager-routing-methods.md) i kondycji punktów końcowych.

Traffic Manager zapewnia szereg metod routingu ruchu w celu dopasowania do różnych potrzeb aplikacji, [monitorowania](../../traffic-manager/traffic-manager-monitoring.md)kondycji punktu końcowego i automatycznej pracy awaryjnej. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer

Usługa [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) zapewnia aplikacjom wysoką dostępność i wydajność sieci. Jest to moduł równoważenia obciążenia warstwy 4 (TCP, UDP), który dystrybuuje ruch przychodzący między prawidłowymi wystąpieniami usług zdefiniowanymi w zestawie o zrównoważonym obciążeniu. Azure Load Balancer można skonfigurować w taki sposób, aby:

- Równoważenie obciążenia przychodzącego ruchu internetowego do maszyn wirtualnych. Ta konfiguracja jest znana jako [publiczne Równoważenie obciążenia](../../load-balancer/components.md#frontend-ip-configurations).

- Równoważyć obciążenie ruchu między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługach w chmurze lub między komputerami lokalnymi i maszynami wirtualnymi w sieci wirtualnej obejmującej wiele lokalizacji. Ta konfiguracja jest znana jako [wewnętrzna usługa równoważenia obciążenia](../../load-balancer/components.md#frontend-ip-configurations).

- Przekazywanie ruchu zewnętrznego do określonej maszyny wirtualnej

### <a name="internal-dns"></a>Wewnętrzny serwer DNS

Można zarządzać listą serwerów DNS używanych w sieci wirtualnej w portal zarządzania lub w pliku konfiguracji sieci. Klient może dodać do 12 serwerów DNS dla każdej sieci wirtualnej. Podczas określania serwerów DNS ważne jest, aby sprawdzić, czy serwery DNS klienta są w odpowiedniej kolejności dla środowiska klienta. Listy serwerów DNS nie działają w sposób okrężny. Są one używane w kolejności, w jakiej zostały określone. Jeśli pierwszy serwer DNS na liście jest w stanie dotrzeć, klient korzysta z tego serwera DNS bez względu na to, czy serwer DNS działa prawidłowo, czy nie. Aby zmienić kolejność serwera DNS dla sieci wirtualnej klienta, Usuń serwery DNS z listy i Dodaj je ponownie w kolejności, w jakiej klient chce. System DNS obsługuje aspekt dostępności "CIA" zabezpieczeń Triad.

### <a name="azure-dns"></a>Azure DNS

System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozwiązanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](../../dns/dns-overview.md) to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. System DNS obsługuje aspekt dostępności "CIA" zabezpieczeń Triad.

### <a name="azure-monitor-logs-nsgs"></a>Sieciowych grup zabezpieczeń dzienników Azure Monitor

Następujące kategorie dzienników diagnostycznych można włączyć dla sieciowych grup zabezpieczeń:

- Zdarzenie: zawiera wpisy, dla których reguły sieciowej grupy zabezpieczeń są stosowane do maszyn wirtualnych i ról wystąpień opartych na adresie MAC. Stan tych reguł jest zbierany co 60 sekund.

- Licznik reguł: zawiera wpisy dla tego, ile razy została zastosowana Każda reguła sieciowej grupy zabezpieczeń, aby odmówić lub zezwolić na ruch.

### <a name="security-center"></a>Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) stale analizuje stan zabezpieczeń zasobów platformy Azure pod kątem najlepszych rozwiązań z zakresu zabezpieczeń sieci. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy [zalecenia](../../security-center/security-center-recommendations.md) , które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

## <a name="compute"></a>Compute
Sekcja zawiera dodatkowe informacje dotyczące najważniejszych funkcji w tym obszarze oraz informacje podsumowujące dotyczące tych możliwości.

### <a name="antimalware--antivirus"></a>Złośliwe oprogramowanie & antywirusowe
Za pomocą usługi Azure IaaS można korzystać z oprogramowania chroniącego przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky pomogą, aby chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami. [Program Microsoft chroniący przed złośliwym kodem](antimalware.md) dla platformy Azure Cloud Services i Virtual Machines to funkcja ochrony, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft zapewnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje się zainstalować lub uruchomić w swoich systemach platformy Azure. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft można również wdrożyć przy użyciu Azure Security Center

### <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń
Szyfrowanie i uwierzytelnianie nie zwiększa zabezpieczeń, chyba że same klucze są chronione. Można uprościć zarządzanie i zabezpieczanie kluczowych kluczy tajnych i kluczy, przechowując je w [Azure Key Vault](../../key-vault/general/overview.md). Key Vault udostępnia opcję przechowywania kluczy w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) certyfikowanych do standardów standardu FIPS 140-2 Level 2. Klucze szyfrowania SQL Server na potrzeby tworzenia kopii zapasowej lub [przezroczystego szyfrowania danych](/sql/relational-databases/security/encryption/transparent-data-encryption) mogą być przechowywane w Key Vault z użyciem dowolnych kluczy lub wpisów tajnych aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za poorednictwem [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej
[Azure Backup](../../backup/backup-overview.md) to rozwiązanie chroniące dane aplikacji bez inwestycji kapitałowych i minimalnych kosztów operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą wprowadzać usterki do aplikacji, które mogą prowadzić do problemów z zabezpieczeniami. W przypadku Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Ważnym elementem strategii zachowania ciągłości działania i [odzyskiwania po awarii (BCDR)](../../best-practices-availability-paired-regions.md) w organizacji jest ustalenie, jak zachować obciążenia i aplikacje firmowe w przypadku wystąpienia planowanych i nieplanowanych przestojów. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomaga organizować replikację, pracę w trybie failover oraz odzyskiwanie obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja główna ulegnie awarii.

### <a name="sql-vm-tde"></a>TDE MASZYNY WIRTUALNEJ SQL
Szyfrowanie danych przezroczystych (TDE) i szyfrowanie na poziomie kolumny (CLE) są funkcjami szyfrowania programu SQL Server. Ta forma szyfrowania wymaga od klientów zarządzania i przechowywania kluczy kryptograficznych używanych do szyfrowania.

Usługa Azure Key Vault (AKV) została zaprojektowana w celu poprawy bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. SQL Server Connector umożliwia SQL Server Korzystanie z tych kluczy z Azure Key Vault.

W przypadku uruchamiania SQL Server z maszynami lokalnymi istnieją kroki, które można wykonać, aby uzyskać dostęp do Azure Key Vault z lokalnego wystąpienia SQL Server. Jednak w przypadku SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas, korzystając z funkcji integracji Azure Key Vault. Aby włączyć tę funkcję za pomocą kilku poleceń cmdlet Azure PowerShell, można zautomatyzować konfigurację niezbędną do uzyskania dostępu do magazynu kluczy w maszynie wirtualnej SQL.

### <a name="vm-disk-encryption"></a>Szyfrowanie dysku maszyny wirtualnej
[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) to nowa funkcja, która ułatwia szyfrowanie dysków maszyn wirtualnych z systemem Windows i Linux IaaS. Stosuje ona standardową funkcję funkcji BitLocker systemu Windows i funkcję DM-Crypt w systemie Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych. Rozwiązanie jest zintegrowane z Azure Key Vault, które ułatwiają kontrolowanie kluczy szyfrowania dysków i wpisów tajnych w subskrypcji Key Vault oraz zarządzanie nimi. Rozwiązanie gwarantuje również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.

### <a name="virtual-networking"></a>Sieć wirtualna
Maszyny wirtualne muszą mieć łączność sieciową. Aby zapewnić obsługę tego wymagania, platforma Azure wymaga, aby maszyny wirtualne były połączone z usługą Azure Virtual Network. Virtual Network platformy Azure to konstrukcja logiczna oparta na fizycznej sieci szkieletowej platformy Azure. Każdy logiczny [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md) jest odizolowany od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępny dla innych klientów Microsoft Azure.

### <a name="patch-updates"></a>Aktualizacje poprawek
Aktualizacje poprawek stanowią podstawę do znajdowania i rozwiązywania potencjalnych problemów oraz upraszczają proces zarządzania aktualizacjami oprogramowania, jednocześnie zmniejszając liczbę aktualizacji oprogramowania, które należy wdrożyć w przedsiębiorstwie i zwiększając możliwości monitorowania zgodności.

### <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie
[Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie oraz zapewnia lepszą widoczność i kontrolę nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby być niezauważalne, i współpracować z szeroką ekosystemem rozwiązań zabezpieczających.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
Zabezpieczanie systemów, aplikacji i danych rozpoczyna się od kontroli dostępu opartej na tożsamościach. Funkcje zarządzania tożsamościami i dostępem, które są wbudowane w produkty i usługi firmy Microsoft, ułatwiają ochronę informacji o organizacji i danych osobowych przed nieautoryzowanym dostępem, zapewniając dostęp dla uprawnionych użytkowników za każdym razem, gdy ich potrzebują.

### <a name="secure-identity"></a>Bezpieczna tożsamość
Firma Microsoft używa wielu rozwiązań w zakresie zabezpieczeń i usług w celu zarządzania tożsamościami i dostępem.

- [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) wymaga od użytkowników korzystania z wielu metod w celu uzyskania dostępu, lokalnego i w chmurze. Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji łatwej weryfikacji, jednocześnie udostępniając użytkownikom prosty proces logowania.

- [Microsoft Authenticator](https://aka.ms/authenticator) zapewnia przyjazne dla użytkownika środowisko Multi-Factor Authentication, które działa zarówno w Microsoft Azure Active Directory, jak i na kontach Microsoft, i obejmuje obsługę noszenia i zatwierdzeń opartych na odcisku palca.

- [Wymuszanie zasad haseł](../../active-directory/authentication/concept-sspr-policy.md) zwiększa bezpieczeństwo tradycyjnych haseł, nakładając długość i wymagania dotyczące złożoności, wymuszone rotacje okresowe i blokadę konta po nieudanej próbie uwierzytelnienia.

- [Uwierzytelnianie oparte na tokenach](../../active-directory/develop/authentication-vs-authorization.md) umożliwia uwierzytelnianie za pośrednictwem Azure Active Directory.

- [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/built-in-roles.md) umożliwia udzielanie dostępu na podstawie przypisanej roli użytkownika, dzięki czemu użytkownicy mogą łatwo przydzielać użytkownikom tylko te dane, których potrzebują do wykonywania swoich zadań. Kontrolę RBAC platformy Azure można dostosować na model biznesowy organizacji i tolerancję ryzyka.

- [Zintegrowana usługa zarządzania tożsamościami (tożsamość hybrydowa)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) pozwala zachować kontrolę dostępu użytkowników w wewnętrznych centrach danych i na platformach w chmurze, tworząc pojedynczą tożsamość użytkownika w celu uwierzytelniania i autoryzacji do wszystkich zasobów.

### <a name="secure-apps-and-data"></a>Zabezpieczanie aplikacji i danych
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), kompleksowe rozwiązanie do zarządzania tożsamościami i dostępem w chmurze, pomaga w zabezpieczeniu dostępu do danych w aplikacjach w lokacjach i w chmurze oraz upraszcza zarządzanie użytkownikami i grupami. Łączy ona podstawowe usługi katalogowe, zaawansowane zarządzanie tożsamościami, zabezpieczenia i zarządzania dostępem do aplikacji oraz ułatwia deweloperom tworzenie zarządzania tożsamościami opartymi na zasadach w aplikacjach. Aby zwiększyć możliwości usługi Azure Active Directory i uzyskać dostęp do płatnych funkcji, możesz zdecydować się na wykupienie usługi Azure Active Directory w wersji Podstawowej, Premium P1 lub Premium P2.

| Funkcje bezpłatne/wspólne     | Podstawowe funkcje    |Funkcje w warstwie Premium P1 |Funkcje Premium P2 | Azure Active Directory Join — powiązane funkcje systemu Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Obiekty katalogu](../../active-directory/fundamentals/active-directory-whatis.md),    [Zarządzanie użytkownikami/grupami (Dodawanie/aktualizowanie/usuwanie)/oparta na użytkownikach Inicjowanie obsługi, rejestracja urządzeń](../../active-directory/fundamentals/active-directory-whatis.md),  [pojedyncze Sign-On (Logowanie jednokrotne)](../../active-directory/fundamentals/active-directory-whatis.md), samoobsługowe     [Zmienianie haseł dla użytkowników w chmurze](../../active-directory/fundamentals/active-directory-whatis.md),     [łączenie (aparat synchronizacji rozszerzający katalogi lokalne do Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md),     [raporty dotyczące zabezpieczeń/użycia](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Zarządzanie dostępem oparte na grupach/Inicjowanie obsługi administracyjnej](../../active-directory/fundamentals/active-directory-whatis.md), samoobsługowe [resetowanie haseł dla użytkowników w chmurze](../../active-directory/fundamentals/active-directory-whatis.md),  [znakowanie firmowe (strona logowania/dostosowywanie panelu dostępu)](../../active-directory/fundamentals/active-directory-whatis.md),    [serwer proxy aplikacji](../../active-directory/fundamentals/active-directory-whatis.md),    [Umowa SLA 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  [Samoobsługowe zarządzanie grupami i aplikacjami/samoobsługowe Dodawanie aplikacji/grupy dynamiczne](../../active-directory/fundamentals/active-directory-whatis.md), samoobsługowe   [resetowanie haseł/zmiana/Odblokowywanie przy użyciu lokalnych funkcji zapisywania zwrotnego](../../active-directory/fundamentals/active-directory-whatis.md),    [Multi-Factor Authentication (w chmurze i lokalnych (serwer MFA))](../../active-directory/fundamentals/active-directory-whatis.md), program [MIM cal + serwer MIM](../../active-directory/fundamentals/active-directory-whatis.md),     [Cloud App Discovery](../../active-directory/fundamentals/active-directory-whatis.md),  [Connect Health](../../active-directory/fundamentals/active-directory-whatis.md),   [Automatyczne Przerzucanie haseł dla kont grup](../../active-directory/fundamentals/active-directory-whatis.md)|   [Ochrona tożsamości](../../active-directory/identity-protection/overview-identity-protection.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Przyłączanie urządzenia do usługi Azure AD, logowanie JEDNOkrotne na komputerze stacjonarnym, Microsoft Passport usługi Azure AD, odzyskiwanie funkcji BitLocker przez administratora](../../active-directory/fundamentals/active-directory-whatis.md),    [Automatyczne rejestrowanie w usłudze MDM, Self-Service odzyskiwania funkcji BitLocker, dodatkowi Administratorzy lokalni do urządzeń z systemem Windows 10 za pośrednictwem](../../active-directory/fundamentals/active-directory-whatis.md)|

- [Cloud App Discovery](/cloud-app-security/set-up-cloud-discovery) to funkcja Premium Azure Active Directory, która pozwala identyfikować aplikacje w chmurze, które są używane przez pracowników w organizacji.

- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) to usługa zabezpieczeń, która używa Azure Active Directory funkcji wykrywania anomalii w celu zapewnienia skonsolidowanego widoku na potrzeby wykrywania ryzyka i potencjalnych luk w zabezpieczeniach, które mogą mieć wpływ na tożsamości w organizacji.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umożliwia dołączanie maszyn wirtualnych platformy Azure do domeny bez konieczności wdrażania kontrolerów domeny. Użytkownicy logują się do tych maszyn wirtualnych przy użyciu poświadczeń firmowych Active Directory i mogą bezproblemowo uzyskiwać dostęp do zasobów.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami o wysokiej dostępności dla aplikacji przeznaczonych dla klientów, która może być skalowana do setek milionów tożsamości i integruje się między platformami mobilnymi i sieci Web. Klienci mogą zalogować się do wszystkich aplikacji za pomocą dostosowywalnych środowisk, które korzystają z istniejących kont mediów społecznościowych, lub można utworzyć nowe poświadczenia autonomiczne.

- [Azure Active Directory współpracy B2B](../../active-directory/external-identities/what-is-b2b.md) to rozwiązanie bezpiecznego partnera, które obsługuje relacje między firmami przez umożliwienie partnerom dostępu do aplikacji i danych firmowych w sposób selektywny przy użyciu własnych tożsamości.

- [Azure Active Directory dołączone](../../active-directory/devices/overview.md) umożliwia zwiększenie możliwości chmury do urządzeń z systemem Windows 10 w celu scentralizowanego zarządzania. Umożliwia użytkownikom łączenie się z chmurą firmową lub organizacyjną przez Azure Active Directory i upraszcza dostęp do aplikacji i zasobów.

- [Serwer proxy aplikacji usługi Azure Active Directory](../../active-directory/manage-apps/application-proxy.md) zapewnia Logowanie jednokrotne i bezpieczny dostęp zdalny do aplikacji sieci Web hostowanych lokalnie.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z Twoją [wspólną odpowiedzialnością w chmurze](shared-responsibility.md).

- Dowiedz się, jak [Azure Security Center](../../security-center/security-center-introduction.md) może pomóc zapobiec zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów platformy Azure.
