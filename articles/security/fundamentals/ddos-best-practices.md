---
title: Projektowanie odpornych rozwiązań za pomocą Azure DDoS Protection
description: Dowiedz się, jak korzystać z danych rejestrowania w celu uzyskania szczegółowych informacji o aplikacji.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: c817608a9e015c69b252f69000d78437e918b8f8
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631515"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection — projektowanie odpornych rozwiązań

Ten artykuł dotyczy osób podejmujących decyzje IT i personelu zabezpieczeń. Oczekujemy, że znasz platformę Azure, sieci i zabezpieczenia.
DDoS jest typem ataku próbującego obsłużyć zasoby aplikacji. Celem jest wpływ na dostępność aplikacji i jej zdolność do obsługi uzasadnionych żądań. Ataki stają się bardziej zaawansowane i większe. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu. Projektowanie dla odporności rozproszonej odmowy usługi (DDoS) wymaga planowania i projektowania dla różnych trybów awarii. Platforma Azure zapewnia ciągłą ochronę przed atakami DDoS. Ta ochrona jest domyślnie zintegrowana z platformą Azure i bez dodatkowych kosztów.

Oprócz podstawowej ochrony DDoS na platformie, [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) zapewnia zaawansowane możliwości ograniczenia DDoS przed atakami sieciowymi. Jest on automatycznie dostrojony do ochrony określonych zasobów platformy Azure. Ochrona jest prosta do włączenia podczas tworzenia nowych sieci wirtualnych. Można to również zrobić po utworzeniu i nie wymaga żadnych zmian aplikacji ani zasobów.

![Rola Azure DDoS Protection w ochronie klientów i sieci wirtualnej od osoby atakującej](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Podstawowe najlepsze rozwiązania

Poniższe sekcje zawierają wskazówki dotyczące tworzenia DDoSych odpornych na błędy usług na platformie Azure.

### <a name="design-for-security"></a>Projektowanie pod kątem zabezpieczeń

Upewnij się, że zabezpieczenia są priorytetem całego cyklu życia aplikacji, od projektowania i implementacji wdrożenia i operacji. Aplikacje mogą mieć usterkę, która pozwala na stosunkowo małą liczbę żądań w celu użycia nadmiernej ilości zasobów, co spowodowało awarię usługi. 

Aby pomóc w ochronie usługi działającej na Microsoft Azure, należy dobrze zrozumieć swoją architekturę aplikacji i skupić się na [pięciu filarach jakości oprogramowania](/azure/architecture/guide/pillars).
Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są dostępne dla publicznego Internetu.

Upewnienie się, że aplikacja jest wystarczająco odporna na obsługę odmowy usługi, która jest przeznaczona dla samej aplikacji, jest najważniejsza. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [cyklu projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL eliminuje bezpieczeństwo w każdej fazie tworzenia i gwarantuje, że platforma Azure jest stale aktualizowana w celu zapewnienia jeszcze większego bezpieczeństwa.

### <a name="design-for-scalability"></a>Projektowanie pod kątem skalowalności

Skalowalność to szybkość, z jaką system może obsłużyć większe obciążenie. Zaprojektuj aplikacje w celu [skalowania w poziomie](/azure/architecture/guide/design-principles/scale-out) , aby spełnić zapotrzebowanie na rozbudowane obciążenie, w zależności od przypadku ataku DDoS. Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy single point of failure. Inicjowanie obsługi wielu wystąpień systemu sprawia, że system jest bardziej odporny i bardziej skalowalny.

W obszarze [Azure App Service](/azure/app-service/app-service-value-prop-what-is)wybierz [Plan App Service](/azure/app-service/overview-hosting-plans) , który oferuje wiele wystąpień. W przypadku usługi Azure Cloud Services skonfiguruj każdą rolę, aby użyć [wielu wystąpień](/azure/cloud-services/cloud-services-choose-me). W przypadku [usługi Azure Virtual Machines](../../virtual-machines/index.yml)upewnij się, że architektura maszyny wirtualnej (VM) zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w [zestawie dostępności](../../virtual-machines/windows/tutorial-availability-sets.md). Zalecamy używanie [zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/overview.md) do obsługi funkcji skalowania automatycznego.

### <a name="defense-in-depth"></a>Ochrona w głębi systemu

Pomysłem związanym z obroną jest zarządzanie ryzykiem przy użyciu różnorodnych strategii obronnych. Zabezpieczenia warstw zabezpieczeń w aplikacji zmniejszają prawdopodobieństwo pomyślnego ataku. Zalecamy wdrożenie bezpiecznych projektów dla aplikacji przy użyciu wbudowanych funkcji platformy Azure.

Na przykład ryzyko ataku zwiększa się wraz z rozmiarem (*obszar powierzchni*) aplikacji. Obszar powierzchniowy można zmniejszyć przy użyciu listy zatwierdzania, aby zamknąć uwidocznioną przestrzeń adresów IP i porty nasłuchujące, które nie są potrzebne w przypadku modułów równoważenia obciążenia ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) i [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)). [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](/azure/virtual-network/security-overview) to inny sposób zmniejszania podatności na ataki.
Możesz użyć [tagów usługi](/azure/virtual-network/security-overview#service-tags) i [grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups) , aby zminimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieci, jako naturalnego rozszerzenia struktury aplikacji.

W miarę możliwości należy wdrożyć usługi platformy Azure w [sieci wirtualnej](/azure/virtual-network/virtual-networks-overview) . Dzięki temu zasoby usług mogą komunikować się za poorednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP. Korzystanie z [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) spowoduje przełączenie ruchu usługi w taki sposób, aby używały prywatnych adresów sieci wirtualnej jako źródłowych adresów IP podczas uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów atakują razem ze swoimi zasobami na platformie Azure. W przypadku łączenia środowiska lokalnego z platformą Azure zalecamy zminimalizowanie zagrożeń zasobów lokalnych do publicznej sieci Internet. Możesz użyć funkcji skalowania i zaawansowanej ochrony DDoS na platformie Azure, wdrażając dobrze znane jednostki publiczne na platformie Azure. Ponieważ te publicznie dostępne jednostki są często celem ataków DDoS, umieszczenie ich na platformie Azure zmniejsza wpływ zasobów lokalnych.

## <a name="azure-offerings-for-ddos-protection"></a>Oferty platformy Azure dla ochrony DDoS

Platforma Azure ma dwie oferty usługi DDoS, które zapewniają ochronę przed atakami sieciowymi (warstwa 3 i 4): DDoS Protection Basic i DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection podstawowa

Ochrona podstawowa jest domyślnie zintegrowana z platformą Azure bez dodatkowych kosztów. Skalowanie i pojemność sieci platformy Azure wdrożonej globalnie zapewnia ochronę przed typowymi atakami warstwy sieci przez zawsze włączone monitorowanie ruchu i środki zaradcze w czasie rzeczywistym. W DDoS Protection Basic nie są wymagane żadne zmiany w konfiguracji użytkownika ani aplikacji. DDoS Protection podstawowa pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak Azure DNS.

![Reprezentacja mapy sieci platformy Azure z tekstem "Global DDoSe ograniczenia obecności" i "Wiodąca wydajność DDoS"](./media/ddos-best-practices/image3.png)

Podstawowa ochrona DDoS na platformie Azure obejmuje składniki oprogramowania i sprzętu. Płaszczyzna kontroli oprogramowania decyduje o tym, gdzie i jakiego typu ruch powinien być kierowany przez urządzenia sprzętowe, które analizują i usuwają ruch związany z atakiem. Płaszczyzna kontroli podejmuje niniejszą decyzję na podstawie *zasad*DDoS Protection w całej infrastrukturze. Te zasady są statycznie ustawiane i uniwersalne dla wszystkich klientów platformy Azure.

Na przykład zasady DDoS Protection określają, w jaki sposób ilość ruchu powinna być *wyzwalana.* (Oznacza to, że ruch dzierżawy powinien być kierowany przez urządzenia do szybkiej kontroli). Następnie zasady określają, jak urządzenia do przemywania powinny *ograniczać* ataki.

Podstawowa usługa Azure DDoS Protection jest przeznaczona do ochrony infrastruktury i ochrony platformy Azure. Zmniejsza to ruch, gdy przekracza on stawkę, która jest tak znacząca, że może mieć wpływ na wielu klientów w środowisku wielodostępnym. Nie zapewnia to alertów ani zasad niestandardowych dla klienta.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Ochrona standardowa zapewnia ulepszone funkcje ograniczenia DDoS. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure w sieci wirtualnej. Ochrona jest prosta do włączenia w przypadku każdej nowej lub istniejącej sieci wirtualnej i nie wymaga żadnych zmian aplikacji ani zasobów. Ma kilka korzyści w porównaniu z usługą podstawową, w tym rejestrowanie, alerty i dane telemetryczne. W poniższych sekcjach przedstawiono najważniejsze funkcje usługi Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptacyjne dostosowanie czasu rzeczywistego

Usługa Azure DDoS Protection Basic pomaga chronić klientów i zapobiega wpływowi innych klientów. Jeśli na przykład usługa została zainicjowana w przypadku typowej ilości legalnego ruchu przychodzącego, który jest mniejszy niż *częstotliwość wyzwalania* zasad DDoS Protection całej infrastruktury, atak DDoS na zasoby tego klienta może być niezauważalny. Ogólnie rzecz biorąc, złożoność ostatnich ataków (na przykład wielowektorowe DDoS) i zachowanie specyficzne dla aplikacji dla dzierżawców wywołują dla poszczególnych klientów, dostosowane zasady ochrony. Usługa wykonuje to dostosowanie przy użyciu dwóch szczegółowych informacji:

- Automatyczna nauka wzorców ruchu dla poszczególnych klientów (na adres IP) dla warstwy 3 i 4.

- Minimalizacja fałszywych wartości dodatnich, biorąc pod uwagę, że skalowanie systemu Azure pozwala na pochłanianie znacznego natężenia ruchu.

![Diagram przedstawiający sposób działania standardu DDoS Protection, z zakreślonym wygenerowaniem zasad](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection dane telemetryczne, monitorowanie i alerty

DDoS Protection Standard uwidacznia rozbudowane dane telemetryczne za pośrednictwem [Azure monitor](/azure/azure-monitor/overview) na czas trwania ataku DDoS. Można skonfigurować alerty dla dowolnych metryk Azure Monitor używanych przez DDoS Protection. Możesz zintegrować rejestrowanie z usługą Splunk (Azure Event Hubs), dziennikami Azure Monitor i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Zasady ograniczania DDoS

W Azure Portal wybierz pozycję **Monitoruj**  >  **metryki**. W okienku **metryk** wybierz grupę zasobów, wybierz typ zasobu **publiczny adres IP**, a następnie wybierz publiczny adres IP platformy Azure. Metryki DDoS są widoczne w okienku **Dostępne metryki** .

DDoS Protection standard stosuje trzy zasady ograniczania ryzyka (TCP SYN, TCP i UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej z włączoną funkcją DDoS. Progi zasad można wyświetlić, wybierając pakiety przychodzące metryczne, **Aby wyzwolić DDoS ograniczenia**.

![Wykres dostępnych metryk i metryk](./media/ddos-best-practices/image7.png)

Progi zasad są automatycznie konfigurowane za pośrednictwem profilowania sieci opartych na uczeniu maszynowym. W przypadku przekroczenia progu zasad środki zaradcze DDoS występują dla adresu IP w obszarze atak.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metryka adresu IP w obszarze atak DDoS

Jeśli publiczny adres IP jest w trakcie ataku, wartość metryki **w obszarze atak DDoS lub nie** zostanie zmieniona na 1, ponieważ DDoS Protection wykonuje środki zaradcze dla ruchu ataków.

!["W przypadku ataku DDoS lub nie" metryki i wykresu](./media/ddos-best-practices/image8.png)

Zalecamy skonfigurowanie alertu dla tej metryki. Następnie otrzymasz powiadomienie, gdy na publicznym adresie IP zostanie wykonane aktywne ograniczenie DDoS.

Aby uzyskać więcej informacji, zobacz [zarządzanie Azure DDoS Protection standard przy użyciu Azure Portal](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Zapora aplikacji sieci Web dla ataków zasobów

Specyficzne dla ataków zasobów w warstwie aplikacji należy skonfigurować zaporę aplikacji sieci Web (WAF), aby ułatwić Zabezpieczanie aplikacji sieci Web. WAF sprawdza przychodzący ruch internetowy, aby blokować iniekcje SQL, skrypty między lokacjami, DDoS i inne ataki warstwy 7. Platforma Azure udostępnia [WAF jako funkcję Application Gateway](/azure/application-gateway/application-gateway-web-application-firewall-overview) w celu scentralizowanej ochrony aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. Dostępne są inne oferty WAF od partnerów platformy Azure, które mogą być bardziej odpowiednie dla Twoich potrzeb za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Nawet zapory aplikacji sieci Web są podatne na ataki na mocy i wyczerpania stanu. Zdecydowanie zalecamy włączenie standardu DDoS Protection w sieci wirtualnej WAF, aby ułatwić ochronę przed atakami typu "i". Aby uzyskać więcej informacji, zobacz sekcję [architektury referencyjne DDoS Protection](#ddos-protection-reference-architectures) .

### <a name="protection-planning"></a>Planowanie ochrony

Planowanie i przygotowywanie ma kluczowe znaczenie dla zrozumienia, jak system będzie wykonywany podczas ataku DDoS. Projektowanie planu odpowiedzi zarządzania zdarzeniami jest częścią tego nakładu pracy.

Jeśli masz DDoS Protection Standard, upewnij się, że jest on włączony w sieci wirtualnej punktów końcowych dostępnych z Internetu. Konfigurowanie alertów DDoS pomaga stale oglądać potencjalne ataki na infrastrukturę. 

Monitoruj aplikacje niezależnie od siebie. Zapoznaj się z normalnym zachowaniem aplikacji. Przygotuj się do działania, jeśli aplikacja nie zachowuje się zgodnie z oczekiwaniami podczas ataku DDoS.

#### <a name="testing-through-simulations"></a>Testowanie poprzez symulacje

Dobrym sposobem jest przetestowanie założeń dotyczących sposobu reagowania usług na ataki przez przeprowadzenie okresowych symulacji. Podczas testowania Sprawdź, czy usługi lub aplikacje nadal działają zgodnie z oczekiwaniami, i nie przerywaj pracy użytkownika. Zidentyfikuj luki zarówno z technologii, jak i procesu punktu widzenia i Uwzględnij je w strategii odpowiedzi DDoS. Zalecamy przeprowadzenie takich testów w środowiskach przejściowych lub poza godzinami szczytu, aby zminimalizować wpływ na środowisko produkcyjne.

Współpracujemy z [chmurą BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) , aby utworzyć interfejs, w którym klienci platformy Azure mogą generować ruch do publicznych punktów końcowych z włączonymi DDoS Protectionami dla symulacji. Możesz użyć symulacji [chmury BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) , aby:

- Sprawdź, jak Azure DDoS Protection pomaga chronić zasoby platformy Azure przed atakami DDoS.

- Zoptymalizuj proces reagowania na zdarzenia w trakcie ataku DDoS.

- Zgodność dokumentu DDoS.

- Uczenie zespołów zabezpieczeń sieci.

Cyberbezpieczeństwa wymaga stałej innowacji w ramach obrony. Ochrona Standardowa usługi Azure DDoS to najnowocześniejsza oferta z skutecznym rozwiązaniem, które pozwala wyeliminować coraz bardziej złożone ataki DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii odpowiedzi DDoS

Atak DDoS, który jest przeznaczony dla zasobów platformy Azure, zazwyczaj wymaga minimalnej interwencji z punktu widzenia użytkownika. Nadal, uwzględnianie środków zaradczych DDoS w ramach strategii reagowania na zdarzenia pomaga zminimalizować wpływ na ciągłość działania.

### <a name="microsoft-threat-intelligence"></a>Analiza zagrożeń firmy Microsoft

Firma Microsoft ma rozbudowaną sieć analizy zagrożeń. Ta sieć korzysta z zbiorczej wiedzy o rozszerzonej społeczności zabezpieczeń, która obsługuje firmę Microsoft Usługi online, partnerów firmy Microsoft i relacje w społeczności Internet Security. 

Jako dostawca infrastruktury krytycznej firma Microsoft otrzymuje wczesne ostrzeżenia o zagrożeniach. Firma Microsoft zbiera informacje o analizie zagrożeń z jej Usługi online i od jej globalnej bazy klientów. Firma Microsoft obejmuje wszystkie te analizy zagrożeń z powrotem w Azure DDoS Protection produkty.

Ponadto firma Microsoft Digital Zbrodnis (DCU) wykonuje obraźliwe strategie przeciwko botnetami. Botnetami są typowym źródłem poleceń i kontroli dla ataków DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Ocena ryzyka zasobów platformy Azure

Konieczna jest znajomość zakresu ryzyka od ataku DDoS na bieżąco. Okresowo zadawaj pytania:

- Jakie nowe publicznie dostępne zasoby platformy Azure wymagają ochrony?

- Czy istnieje single point of failure w usłudze? 

- Jak usługi mogą być izolowane w celu ograniczenia wpływu ataku, a w dalszym ciągu udostępniają usługi prawidłowym klientom?

- Czy istnieją sieci wirtualne, w których należy włączyć DDoS Protection Standard, ale nie są? 

- Czy moje usługi są aktywne/aktywne z trybem failover w wielu regionach?

### <a name="customer-ddos-response-team"></a>Zespół odpowiedzi DDoS klienta

Tworzenie zespołu odpowiedzi DDoS to kluczowy krok w celu szybkiego i efektywnego ataku. Zidentyfikuj kontakty w organizacji, które będą nadzorowały planowanie i wykonywanie. Ten zespół odpowiedzi DDoS powinien starannie zrozumieć usługę Azure DDoS Protection Standard. Upewnij się, że zespół może identyfikować i ograniczać ataki przez koordynowanie z klientami wewnętrznymi i zewnętrznymi, w tym z zespołem pomocy technicznej firmy Microsoft.

W przypadku zespołu odpowiedzi DDoS zalecamy korzystanie z ćwiczeń symulacji jako zwykłych części planowania dostępności i ciągłości usług. Ćwiczenia te powinny obejmować testowanie skali.

### <a name="alerts-during-an-attack"></a>Alerty w trakcie ataku

Azure DDoS Protection Standard identyfikuje i zmniejsza ataki DDoS bez żadnej interwencji użytkownika. Aby otrzymywać powiadomienia, gdy istnieje aktywne środki zaradcze dla chronionego publicznego adresu IP, można [skonfigurować alert](/azure/virtual-network/ddos-protection-manage-portal) dotyczący metryki w **obszarze atak DDoS**. Można utworzyć alerty dla innych metryk DDoS, aby zrozumieć skalę ataku, pozostały ruch oraz inne szczegóły.

#### <a name="when-to-contact-microsoft-support"></a>Kiedy należy skontaktować się z pomocą techniczną firmy Microsoft

- Podczas ataku DDoS można stwierdzić, że wydajność chronionego zasobu jest poważnie obniżona lub zasób nie jest dostępny.

- Uważasz, że usługa DDoS Protection nie zachowuje się zgodnie z oczekiwaniami. 

  Usługa DDoS Protection uruchamia środki zaradcze tylko wtedy, gdy zasady wartości metryk **wyzwalają ograniczenia DDoS (TCP/TCP syn/UDP)** są mniejsze niż ruch odebrany w chronionym publicznym adresie IP.

- Planujesz zdarzenie wirusowe, które znacząco zwiększy ruch sieciowy.

- Aktor został narażony na uruchomienie ataku DDoS w odniesieniu do zasobów.

- Jeśli musisz zezwolić na wyświetlanie listy adresów IP lub adresów IP z Azure DDoS Protection Standard. Typowym scenariuszem jest zezwolenie na listę adresów IP, jeśli ruch jest kierowany z zewnętrznej chmury WAF do platformy Azure. 

W przypadku ataków mających krytyczny wpływ na firmę Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroki po ataku

Jest to zawsze dobrą strategią wykonywania postmortem po ataku i dostosowania strategii odpowiedzi DDoS zgodnie z wymaganiami. Zagadnienia, które należy wziąć pod uwagę:

- Czy w przypadku braku skalowalnej architektury występuje zakłócenie w działaniu usługi lub użytkownika?

- Które aplikacje lub usługi były największe?

- Jak skutecznie była strategia odpowiedzi DDoS i jak można ją ulepszyć?

Jeśli podejrzewasz, że jesteś w trakcie ataku DDoS, przeprowadzenie eskalacji zwykłych kanałów pomocy technicznej platformy Azure.

## <a name="ddos-protection-reference-architectures"></a>Architektury referencyjne DDoS Protection

Standard DDoS Protection jest przeznaczony [dla usług, które są wdrażane w sieci wirtualnej](/azure/virtual-network/virtual-network-for-azure-services). W przypadku innych usług stosowana jest domyślna usługa DDoS Protection podstawowa. Poniższe architektury referencyjne są uporządkowane według scenariuszy, a wzorce architektury pogrupowane razem.

### <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyn wirtualnych (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikacja działająca na maszynach wirtualnych z równoważeniem obciążenia

Ta architektura referencyjna przedstawia zestaw sprawdzonych rozwiązań dotyczących uruchamiania wielu maszyn wirtualnych z systemem Windows w zestawie skalowania za modułem równoważenia obciążenia, aby zwiększyć dostępność i skalowalność. Ta architektura może być używana w przypadku każdego bezstanowego obciążenia, takiego jak serwer sieci Web.

![Diagram architektury referencyjnej dla aplikacji uruchomionej na maszynach wirtualnych z równoważeniem obciążenia](./media/ddos-best-practices/image9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowany do maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Standard DDoS Protection jest włączony w sieci wirtualnej modułu równoważenia obciążenia platformy Azure (Internet), z którym jest skojarzony publiczny adres IP.

Moduł równoważenia obciążenia dystrybuuje przychodzące żądania internetowe do wystąpień maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych umożliwiają ręczne skalowanie lub wyewidencjonowywanie liczby maszyn wirtualnych lub automatyczne na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób jest w trakcie ataku DDoS. Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikacja działająca w systemie Windows N-warstwowa

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na poniższym diagramie przedstawiono typową aplikację sieci Web z trzema warstwami. Ta architektura jest oparta na artykule [Uruchamianie maszyn wirtualnych ze zrównoważonym obciążeniem w celu zapewnienia skalowalności i dostępności](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![Diagram architektury referencyjnej dla aplikacji działającej w systemie Windows N-warstwowej](./media/ddos-best-practices/image10.png)

W tej architekturze w sieci wirtualnej jest włączony DDoS Protection Standard. Wszystkie publiczne adresy IP w sieci wirtualnej uzyskają ochronę DDoSą dla warstwy 3 i 4. W przypadku ochrony warstwy 7 Wdróż Application Gateway w jednostce SKU WAF. Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplikacja internetowa PaaS

Ta architektura referencyjna pokazuje, jak działa aplikacja Azure App Service w jednym regionie. Ta architektura pokazuje zestaw sprawdzonych rozwiązań dla aplikacji sieci Web, które używają [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/)   i [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
W przypadku scenariuszy trybu failover jest ustawiany region w stanie wstrzymania.

![Diagram architektury referencyjnej dla aplikacji sieci Web PaaS](./media/ddos-best-practices/image11.png)

Usługa Azure Traffic Manager kieruje żądania przychodzące do Application Gateway w jednym z regionów. Podczas normalnych operacji usługa kieruje żądania do Application Gateway w aktywnym regionie. W przypadku niedostępności tego regionu Traffic Manager przełączenia w tryb failover w celu Application Gateway w regionie gotowości.

Cały ruch z Internetu przeznaczony do aplikacji sieci Web jest kierowany do [Application Gateway publicznego adresu IP](/azure/application-gateway/application-gateway-web-app-overview) za pośrednictwem Traffic Manager. W tym scenariuszu sama usługa App Service (aplikacja sieci Web) nie jest bezpośrednio na zewnątrz i jest chroniona przez Application Gateway. 

Zalecamy skonfigurowanie Application Gateway jednostki SKU WAF (Zapobiegaj trybowi), aby chronić przed atakami warstwy 7 (HTTP/HTTPS/WebSocket). Ponadto aplikacje sieci Web są skonfigurowane tak, aby [akceptowały tylko ruch z Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adres IP.

Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Środki zaradcze dla usług PaaS innych niż sieci Web

#### <a name="hdinsight-on-azure"></a>HDInsight na platformie Azure

W tej architekturze referencyjnej przedstawiono Konfigurowanie DDoS Protection Standard dla [klastra usługi Azure HDInsight](/azure/hdinsight/). Upewnij się, że klaster usługi HDInsight jest połączony z siecią wirtualną i że DDoS Protection jest włączona w sieci wirtualnej.

![Okienka "HDInsight" i "Ustawienia zaawansowane" z ustawieniami sieci wirtualnej](./media/ddos-best-practices/image12.png)

![Wybór na potrzeby włączania DDoS Protection](./media/ddos-best-practices/image13.png)

W tej architekturze ruch kierowany do klastra usługi HDInsight z Internetu jest kierowany do publicznego adresu IP skojarzonego z usługą równoważenia obciążenia bramy usługi HDInsight. Moduł równoważenia obciążenia bramy wysyła następnie bezpośrednio ruch do węzłów głównych lub węzłów procesu roboczego. Ponieważ w sieci wirtualnej usługi HDInsight włączono Standard DDoS Protection, wszystkie publiczne adresy IP w sieci wirtualnej uzyskają ochronę DDoSą dla warstwy 3 i 4. Ta architektura referencyjna może być łączona z architekturą referencyjną N-warstwową i wieloregionową.

Więcej informacji na temat tej architektury referencyjnej można znaleźć w dokumentacji usługi [Azure HDInsight przy użyciu usługi azure Virtual Network](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment usługi PowerApps lub API Management w sieci wirtualnej z publicznym adresem IP nie są obsługiwane natywnie.

## <a name="next-steps"></a>Następne kroki

* [Wspólna odpowiedzialność w chmurze](shared-responsibility.md)
* [Strona produktu Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
* [Dokumentacja Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)
