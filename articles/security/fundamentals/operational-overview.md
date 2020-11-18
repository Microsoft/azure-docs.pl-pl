---
title: Omówienie zabezpieczeń operacyjnych platformy Azure | Microsoft Docs
description: Informacje o zabezpieczeniach operacyjnych platformy Azure w tym omówieniu. Zabezpieczenia operacyjne odnoszą się do usług, kontroli i funkcji ochrony zasobów.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 9dcb1113bb37cb0f5f65c54ae9d6ca76a88330c1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698669"
---
# <a name="azure-operational-security-overview"></a>Omówienie zabezpieczeń operacyjnych platformy Azure

[Zabezpieczenia operacyjne platformy Azure](./operational-security.md) odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów w Microsoft Azure. Jest to platforma, która obejmuje wiedzę uzyskaną za pomocą różnych możliwości, które są unikatowe dla firmy Microsoft. Do tych funkcji należą Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center i szczegółowa świadomość zagrożeń cyberbezpieczeństwa.

## <a name="azure-management-services"></a>Usługi zarządzania platformy Azure

Zespół ds. operacyjnych jest odpowiedzialny za zarządzanie infrastrukturą, aplikacjami i danymi centrum danych, w tym stabilnością i bezpieczeństwem tych systemów. Jednak uzyskanie szczegółowych informacji o zabezpieczeniach w coraz bardziej złożonych środowiskach IT często wymaga, aby organizacje Cobble dane z wielu systemów zabezpieczeń i zarządzania.

[Dzienniki monitora Microsoft Azure](../../azure-monitor/overview.md) to oparte na chmurze rozwiązanie do zarządzania działem IT, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę. Podstawowe funkcje są udostępniane przez następujące usługi, które działają na platformie Azure. Platforma Azure obejmuje wiele usług, które ułatwiają zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę. Każda usługa zapewnia konkretną funkcję zarządzania. Możesz połączyć usługi, aby osiągnąć różne scenariusze zarządzania. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) zbiera dane z zarządzanych źródeł do centralnych magazynów danych. Te dane mogą obejmować zdarzenia, dane dotyczące wydajności lub dane niestandardowe udostępniane za pomocą interfejsu API. Po zebraniu danych są one dostępne dla alertów, analiz i eksportu.

Dane można skonsolidować z różnych źródeł i połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Dzienniki Azure Monitor również wyraźnie oddzielają kolekcję danych od akcji podjętej na tych danych, dzięki czemu wszystkie akcje są dostępne dla wszystkich rodzajów danych.

### <a name="automation"></a>Automation

[Azure Automation](../../automation/automation-intro.md) zapewnia sposób automatyzacji ręcznych, długotrwałych, podatnych na błędy i często powtarzanych zadań, które są często wykonywane w chmurze i środowisku przedsiębiorstwa. Oszczędza czas i zwiększa niezawodność zadań administracyjnych. Program planuje również automatyczne wykonywanie tych zadań w regularnych odstępach czasu. Procesy można zautomatyzować za pomocą elementów Runbook lub automatyzując zarządzanie konfiguracją przy użyciu konfiguracji żądanego stanu.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) jest usługą opartą na platformie Azure, za pomocą której można tworzyć kopie zapasowe (lub chronić) i przywracać dane w Microsoft Cloud. Azure Backup zastępuje istniejące rozwiązanie do tworzenia kopii zapasowych w środowisku lokalnym lub poza lokacją przy użyciu rozwiązania opartego na chmurze, które jest niezawodne, bezpieczne i konkurencyjne.

Azure Backup oferuje składniki pobierane i wdrażane na odpowiednim komputerze lub serwerze albo w chmurze. Wdrażany składnik lub agent zależy od tego, co ma być chronione. Wszystkie składniki Azure Backup (niezależnie od tego, czy dane są chronione lokalnie, czy w chmurze) mogą służyć do tworzenia kopii zapasowych danych w magazynie usługi Azure Recovery Services na platformie Azure.

Aby uzyskać więcej informacji, zobacz [tabelę składników Azure Backup](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) zapewnia ciągłość działania przez organizowanie replikacji lokalnych maszyn wirtualnych i fizycznych na platformę Azure lub do lokacji dodatkowej. Jeśli lokacja główna jest niedostępna, przełączenie w tryb failover do lokalizacji dodatkowej spowoduje, że użytkownicy będą mogli kontynuować pracę. Powrót po awarii, gdy system wróci do kolejności roboczej. Użyj Azure Security Center, aby wykonywać bardziej inteligentne i efektywne wykrywanie zagrożeń.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) to kompleksowa usługa tożsamości:

-   Umożliwia zarządzanie tożsamościami i dostępem (IAM) jako usługą w chmurze.
-   Zapewnia centralne zarządzanie dostępem, logowanie jednokrotne (SSO) i raportowanie.
-   Obsługa zintegrowanego zarządzania dostępem dla [tysięcy aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) w portalu Azure Marketplace, w tym usług Salesforce, Google Apps, Box i Concur.

Usługa Azure AD zawiera również pełen pakiet [funkcji zarządzania tożsamościami](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports), w tym:

- [Uwierzytelnianie wieloskładnikowe](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Samoobsługowe zarządzanie hasłami](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobsługowe zarządzanie grupami](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Zarządzanie kontami uprzywilejowanymi](../../active-directory/privileged-identity-management/pim-configure.md)
- [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md)
- [Monitorowanie użycia aplikacji](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Zaawansowana Inspekcja](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Monitorowanie zabezpieczeń i alerty](../../security-center/security-center-managing-and-responding-alerts.md)

Dzięki Azure Active Directory wszystkie aplikacje publikowane dla partnerów i klientów (firma lub konsument) mają takie same możliwości zarządzania tożsamościami i dostępem. Pozwala to znacznie zmniejszyć koszty operacyjne.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby być niezauważalne, i współpracuje z szeroką ekosystemem rozwiązań w zakresie zabezpieczeń.

[Ochrona danych maszyny wirtualnej](../../security-center/security-center-introduction.md) na platformie Azure, zapewniając wgląd w ustawienia zabezpieczeń maszyny wirtualnej i monitorowanie zagrożeń. Usługa Security Center może monitorować maszyny wirtualne pod kątem następujących elementów:

- Ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji.
- Brak zabezpieczeń systemu i krytycznych aktualizacji.
- Zalecenia dotyczące programu Endpoint Protection.
- Walidacja szyfrowania dysku.
- Ataki sieciowe.

Security Center używa [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md). Usługa Azure RBAC udostępnia [wbudowane role](../../role-based-access-control/built-in-roles.md) , które można przypisać do użytkowników, grup i usług na platformie Azure.

Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów i luk w zabezpieczeniach. W Security Center informacje dotyczące zasobu są wyświetlane tylko wtedy, gdy przypiszesz rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

>[!Note]
>Aby dowiedzieć się więcej o rolach i dozwolonych akcjach w Security Center, zobacz [uprawnienia w Azure Security Center](../../security-center/security-center-permissions.md).

Security Center używa Microsoft Monitoring Agent. Jest to ten sam Agent, którego używa usługa Azure Monitor. Dane zbierane z tego agenta są przechowywane w istniejącym [obszarze roboczym](../../azure-monitor/platform/manage-access.md) log Analytics skojarzonym z subskrypcją platformy Azure lub nowym obszarem roboczym, biorąc pod uwagę GEOLOKALIZACJĘ maszyny wirtualnej.

## <a name="azure-monitor"></a>Azure Monitor

Problemy z wydajnością w aplikacji w chmurze mogą mieć wpływ na Twoją firmę. Z wieloma połączonymi składnikami i częstymi wersjami w dowolnym momencie mogą wystąpić obniżenie wydajności. Jeśli tworzysz aplikację, użytkownicy zazwyczaj odkrywają problemy, które nie zostały odnalezione w testowaniu. Informacje o tych problemach powinny być natychmiast znane i należy dysponować narzędziami do diagnozowania i rozwiązywania problemów.

[Azure monitor](../../azure-monitor/overview.md) to podstawowe narzędzie do monitorowania usług działających na platformie Azure. Zapewnia ona dane na poziomie infrastruktury dotyczące przepływności usługi i otoczenia. Jeśli zarządzasz aplikacjami na platformie Azure i zdecydujesz, czy chcesz skalować zasoby w górę czy w dół, Azure Monitor to miejsce do rozpoczęcia.

Dane monitorowania można także wykorzystać do uzyskania szczegółowych informacji o aplikacji. Ta wiedza może pomóc w zwiększeniu wydajności aplikacji lub utrzymaniu lub zautomatyzowaniu działań, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor obejmuje następujące składniki.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennik aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) zawiera szczegółowe informacje o operacjach wykonywanych na zasobach w ramach subskrypcji. Wcześniej była znana jako "dziennik inspekcji" lub "dziennik operacyjny", ponieważ zgłasza zdarzenia płaszczyzny kontroli dla subskrypcji.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

[Dzienniki diagnostyczne platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Zawartość tych dzienników różni się w zależności od typu zasobu.

Dzienniki systemu zdarzeń systemu Windows to jedna kategoria dzienników diagnostycznych dla maszyn wirtualnych. Dzienniki obiektów blob, tabel i kolejek są kategoriami dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności](../../azure-monitor/platform/platform-logs-overview.md). Dziennik aktywności zawiera szczegółowe informacje o operacjach wykonywanych na zasobach w ramach subskrypcji. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane przez zasób.

### <a name="metrics"></a>Metryki

Azure Monitor udostępnia dane telemetryczne, które zapewniają wgląd w wydajność i kondycję obciążeń na platformie Azure. Najważniejszym typem danych telemetrii platformy Azure jest [metryki](../../azure-monitor/platform/data-platform.md) (nazywane również licznikami wydajności) emitowane przez większość zasobów platformy Azure. Azure Monitor oferuje kilka sposobów konfigurowania i używania tych metryk do monitorowania i rozwiązywania problemów.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Diagnostyka Azure umożliwia zbieranie danych diagnostycznych we wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych źródeł. Obecnie obsługiwane są [role usługi w chmurze platformy Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyny wirtualne platformy Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) z systemem Microsoft Windows i [Service Fabric platformy Azure](../../azure-monitor/platform/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Klienci tworzą kompleksową sieć na platformie Azure dzięki aranżacji i tworzeniu poszczególnych zasobów sieciowych, takich jak sieci wirtualne, Azure ExpressRoute, Azure Application Gateway i równoważenia obciążenia. Monitorowanie jest dostępne w każdym z zasobów sieciowych.

Sieć kompleksowa może mieć złożone konfiguracje i interakcje między zasobami. W wyniku tego powstaje skomplikowane scenariusze, które wymagają monitorowania opartego na scenariuszu za pośrednictwem [usługi Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

Network Watcher upraszcza monitorowanie i diagnozowanie sieci platformy Azure. Narzędzi diagnostycznych i wizualizacji programu można użyć w Network Watcher do:

- Utwórz Zdalne przechwytywanie pakietów na maszynie wirtualnej platformy Azure.
- Uzyskaj wgląd w ruch sieciowy przy użyciu dzienników przepływu.
- Diagnozuj VPN Gateway i połączenia platformy Azure.

Network Watcher obecnie ma następujące możliwości:

- [Topologia](../../network-watcher/view-network-topology.md): zawiera widok różnych połączeń i skojarzeń między zasobami sieci w grupie zasobów.
- [Przechwytywanie pakietów zmiennych](../../network-watcher/network-watcher-packet-capture-overview.md): przechwytuje dane pakietów do i z maszyny wirtualnej. Bardzo przydatne są zaawansowane opcje filtrowania i dostosowywania ustawień, takie jak określanie czasu i limitów rozmiaru. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie. Cap.
- [Weryfikowanie przepływu adresów IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md): sprawdza, czy pakiet jest dozwolony, czy odrzucany na podstawie parametrów pakietu 5-spoiny dla informacji o przepływie (docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli grupa zabezpieczeń nie zezwala na pakiet, zwracane są reguły i grupy, które odrzuciły pakiet.
- [Następny przeskok](../../network-watcher/network-watcher-next-hop-overview.md): określa następny przeskok dla pakietów przesyłanych w sieci szkieletowej platformy Azure, dzięki czemu można zdiagnozować wszelkie nieprawidłowo skonfigurowane trasy zdefiniowane przez użytkownika.
- [Widok grupy zabezpieczeń](../../network-watcher/network-watcher-security-group-view-overview.md): Pobiera obowiązujące i stosowane reguły zabezpieczeń stosowane na maszynie wirtualnej.
- [Dzienniki przepływu sieciowej grupy zabezpieczeń dla sieciowych grup zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): umożliwiają przechwytywanie dzienników związanych z ruchem, które są dozwolone lub odrzucane przez reguły zabezpieczeń w grupie. Przepływ jest definiowany przy użyciu 5-informacje o spójnej kolekcji: źródłowy adres IP, docelowy adres IP, port źródłowy, port docelowy i protokół.
- [Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniem](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): umożliwia rozwiązywanie problemów z bramami i połączeniami sieci wirtualnej.
- [Limity dotyczące subskrypcji sieci](../../network-watcher/network-watcher-monitoring-overview.md): umożliwiają wyświetlanie użycia zasobów sieciowych względem limitów.
- [Dzienniki diagnostyczne](../../network-watcher/network-watcher-monitoring-overview.md): zawiera pojedyncze okienko do włączania lub wyłączania dzienników diagnostycznych dla zasobów sieciowych w grupie zasobów.

Aby uzyskać więcej informacji, zobacz [konfigurowanie Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Przezroczystość dostępu dostawcy usług w chmurze

[Skrytka klienta dla Microsoft Azure](customer-lockbox-overview.md) to usługa zintegrowana z Azure Portal, która zapewnia jawną kontrolę w rzadkim wystąpieniu, gdy inżynier pomoc techniczna firmy Microsoft może potrzebować dostępu do danych w celu rozwiązania problemu.
Istnieje kilka wystąpień, takich jak debugowanie problemu dostępu zdalnego, gdzie inżynier pomoc techniczna firmy Microsoft wymaga podniesionych uprawnień, aby rozwiązać ten problem. W takich przypadkach inżynierowie firmy Microsoft używają usługi dostępu just in Time, która zapewnia ograniczoną, powiązaną z czasem autoryzację z dostępem ograniczonym do usługi.  
Firma Microsoft zawsze uzyskała zgodę klienta na dostęp, Skrytka klienta teraz umożliwia przeglądanie i zatwierdzanie lub odrzucanie takich żądań w witrynie Azure Portal. Inżynierowie pomocy technicznej firmy Microsoft nie otrzymają dostępu do momentu zatwierdzenia żądania.

## <a name="standardized-and-compliant-deployments"></a>Standardowe i zgodne wdrożenia

[Plany platformy Azure](../../governance/blueprints/overview.md) umożliwiają architektom chmury i centralnym grupom technologii informatycznych Definiowanie powtarzalnych zestawów zasobów platformy Azure, które implementują i stosują się do standardów, wzorców i wymagań organizacji.  
Dzięki temu zespoły DevOps mogą szybko tworzyć i wdrażać nowe środowiska oraz ufać, że kompilują je z infrastrukturą, która utrzymuje zgodność organizacji.
Plany zapewniają deklaratywny sposób organizowania wdrożenia różnych szablonów zasobów i innych artefaktów, takich jak:

- Przypisania ról
- Przypisania zasad
- Szablony usługi Azure Resource Manager
- Grupy zasobów

## <a name="devops"></a>DevOps

Przed rozpoczęciem tworzenia aplikacji dla [deweloperów (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) zespoły były odpowiedzialne za gromadzenie wymagań firmy dla programu i pisanie kodu. Następnie oddzielny zespół kontroli jakości przetestował program w izolowanym środowisku programistycznym. Jeśli zostały spełnione wymagania, zespół usługi pytań i odpowiedzi wydał kod dla operacji do wdrożenia. Zespoły wdrożeniowe zostały przedzielone na grupy, takie jak sieć i baza danych. Za każdym razem, gdy program został zgłoszony przez ściany do niezależnego zespołu, dodaliśmy wąskie gardła.

DevOps umożliwia zespołom szybsze i wydajniejsze dostarczanie bardziej bezpiecznych rozwiązań o wyższej jakości. Klienci oczekują dynamicznego i niezawodnego środowiska podczas korzystania z oprogramowania i usług. Zespoły muszą szybko wykonać iterację aktualizacji oprogramowania i zmierzyć wpływ aktualizacji. Muszą szybko reagować na nowe iteracje programistyczne w celu rozwiązania problemów lub zapewnienia większej wartości.  

Platformy w chmurze, takie jak Microsoft Azure, zostały usunięte tradycyjne wąskie gardła i pomogły postrzeganie infrastrukturę. Oprogramowanie Reigns w każdej firmie jako odróżniający klucz i współczynnik w wyników działalności biznesowej. Żadna organizacja, deweloper lub pracownik IT nie może ani nie powinien unikać przesunięć DevOps.

Praktykujący lekarze DevOps przyjmuje kilka z poniższych praktyk. Te praktyki [obejmują](https://www.visualstudio.com/learn/what-is-devops-culture/) metody tworzenia strategii na podstawie scenariuszy firmy. Narzędzia mogą pomóc zautomatyzować różne praktyki.

- [Planowanie Agile i techniki zarządzania projektami](https://www.visualstudio.com/learn/what-is-agile/) są używane do planowania i izolowania pracy w przebiegach, zarządzania pojemnością zespołu i ułatwienia zespołom szybkiej adaptacji do zmieniających się potrzeb firmy.
- [Kontrola wersji, zwykle z usługą git](https://www.visualstudio.com/learn/what-is-git/), umożliwia zespołom znajdującym się w dowolnym miejscu na świecie udostępnianie źródła i integrację z narzędziami do tworzenia oprogramowania w celu zautomatyzowania potoku wydania.
- [Ciągła integracja](https://www.visualstudio.com/learn/what-is-continuous-integration/) pozwala na ciągłe scalanie i testowanie kodu, który prowadzi do wczesnego znajdowania wad.  Inne korzyści obejmują mniej czasu na problemy z scalaniem i błyskawiczną opinię dla zespołów programistycznych.
- [Ciągłe dostarczanie](https://www.visualstudio.com/learn/what-is-continuous-delivery/) rozwiązań programowych do środowisk produkcyjnych i testowych ułatwia organizacjom szybkie rozwiązywanie usterek i reagowanie na zmieniające się wymagania biznesowe.
- [Monitorowanie](https://www.visualstudio.com/learn/what-is-monitoring/) uruchomionych aplikacji — w tym środowisk produkcyjnych w celu zapewnienia kondycji aplikacji, a także użycia klienta — pomaga organizacjom tworzyć hipotezy i szybko weryfikować lub odrzucanie strategie.  Bogate dane są przechwytywane i przechowywane w różnych formatach rejestrowania.
- [Infrastruktura jako kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) to rozwiązanie, które umożliwia automatyzację i sprawdzanie poprawności tworzenia i usuwania sieci oraz maszyn wirtualnych w celu ułatwienia dostarczania bezpiecznych, stabilnych platform hostingu aplikacji.
- Architektura [mikrousług](https://www.visualstudio.com/learn/what-are-microservices/) służy do izolowania przypadków użycia biznesowego w małych usługach wielokrotnego użytku.  Ta architektura umożliwia skalowalność i wydajność.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozwiązania Security and Audit, zobacz następujące artykuły:

- [Zabezpieczenia i zgodność](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)