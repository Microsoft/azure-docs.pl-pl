---
title: Zabezpieczanie aplikacji sieci Web dla PCI DSS | Microsoft Docs
description: Ta przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które ulepszają aplikację i stan bezpieczeństwa organizacji podczas opracowywania na platformie Azure.
keywords: nie dotyczy
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187583"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Opracowywanie bezpiecznej infrastruktury dla aplikacji PCI

## <a name="overview"></a>Omówienie

Ta Bezpieczna infrastruktura dla aplikacji branżowych dla kart płatniczych (PCI) zawiera wskazówki dotyczące wdrożenia środowiska branżowego w branży jako usługi (PaaS) na potrzeby zbierania, przechowywania i pobierania danych posiadaczy kart kredytowych. To rozwiązanie automatyzuje wdrażanie i Konfigurowanie zasobów platformy Azure na potrzeby wspólnej architektury referencyjnej, pokazując sposoby, w których klienci mogą spełniać określone wymagania dotyczące zabezpieczeń i zgodności, a także służy jako podstawa dla klientów do tworzenia i konfigurowania własnych rozwiązań na platformie Azure. Rozwiązanie implementuje podzbiór wymagań podobny do standardowych standardów zabezpieczeń danych branżowych (PCI DSS 3,2).

Ten przykład automatycznie wdraża architekturę referencyjną aplikacji sieci Web PaaS ze wstępnie skonfigurowanymi kontrolami zabezpieczeń, aby pomóc klientom w osiągnięciu zgodności podobnej do wymagań PCI DSS 3,2. Rozwiązanie składa się z szablonów Azure Resource Manager i skryptów programu PowerShell, które ułatwiają wdrażanie i Konfigurowanie zasobów.

Należy postępować zgodnie z krokami opisanymi w tym artykule, aby upewnić się, że składniki aplikacji są prawidłowo skonfigurowane. Baza danych, Azure App Service, wystąpienie Azure Key Vault i wystąpienie Application Gateway platformy Azure są od siebie zależne.

Skrypty wdrażania skonfigurują infrastrukturę. Po uruchomieniu skryptów wdrażania należy wykonać ręczną konfigurację w Azure Portal, aby połączyć składniki i usługi ze sobą.

Ten przykład jest przeznaczony dla doświadczonych deweloperów na platformie Azure, którzy pracują w branży detalicznej i chcą utworzyć aplikację handlu detalicznego za pomocą bezpiecznej infrastruktury platformy Azure.

> [!NOTE]
> Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.

Wdrożenie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania PCI DSS 3,2. Pamiętaj o następujących kwestiach:

- Ta architektura udostępnia linię bazową, która ułatwia klientom korzystanie z platformy Azure w PCI DSS 3,2.
- Klienci są odpowiedzialni za przeprowadzenie odpowiedniej oceny zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

W opracowywaniu i wdrażaniu tej aplikacji dowiesz się, jak:

- Utwórz wystąpienie Azure Key Vault i Zapisz je i Pobierz z niego wpisy tajne.
- Wdróż usługę Azure Database for Azure SQL, skonfiguruj bezpieczne dane i Autoryzuj dostęp do niej.
- Wdróż aplikację internetową platformy Azure przy użyciu środowiska App Service, które jest dedykowane izolowane od dostępu do zapory frontonu.
- Utwórz i skonfiguruj wystąpienie usługi Azure Application Gateway przy użyciu zapory korzystającej z [OWASP 10 najważniejszych zestawów reguł](https://coreruleset.org/).
- Włącz szyfrowanie danych podczas przesyłania i w spoczynku przy użyciu usług platformy Azure.
- Skonfiguruj Azure Policy i plany platformy Azure w celu oceny zgodności.

Po utworzeniu i wdrożeniu tej aplikacji skonfigurujesz następującą przykładową aplikację internetową wraz z opisanymi przez siebie środkami konfiguracji i zabezpieczeń.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Warstwy frontonu, zaplecza i warstwa bazy danych ze zintegrowanymi składnikami monitorowania i zarządzania kluczami tajnymi przedstawiono tutaj:

![Architektura aplikacji](./media/secure-pci-web-app/architecture.png)

Architektura składa się z następujących składników:

- [App Service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Zapewnia App Service Environment i moduł równoważenia obciążenia dla naszej architektury aplikacji.
- [Application Gateway platformy Azure](https://docs.microsoft.com/azure/application-gateway/). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Zapewnia rozszerzalną usługę zarządzania wydajnością aplikacji (APM) na wielu platformach.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Przechowuje i szyfruje wpisy tajne aplikacji i zarządza tworzeniem zasad dostępu.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Zapewnia usługi zarządzania tożsamościami i dostępem w chmurze, logowania i uzyskiwania dostępu do zasobów.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Udostępnia usługę do hostowania domeny.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Skalowanie aplikacji i tworzenie wysokiej dostępności dla usług
- [Usługa Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Zapewnia rozwiązanie dla nowoczesnych scenariuszy magazynowania danych.
- [Aplikacja internetowa platformy Azure](https://docs.microsoft.com/azure/app-service/overview/).  Zapewnia usługę opartą na protokole HTTP do hostowania aplikacji sieci Web.
- [Azure Database for AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Bezpieczne przechowywanie danych aplikacji.
- [Plany platformy Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Zapewnia specyfikacje i zgodność z pewnymi standardami i wymaganiami.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Szacuje zasoby pod kątem braku zgodności z przypisanymi zasadami.

## <a name="threat-model"></a>Model zagrożeń
Modelowanie zagrożeń polega na zidentyfikowaniu potencjalnych zagrożeń bezpieczeństwa dla Twojej firmy i aplikacji, a następnie zapewnieniu prawidłowego planu zaradczego.

W tym przykładzie użyto [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Dzięki utworzeniu diagramu składników i przepływów danych można wcześnie identyfikować problemy i zagrożenia w procesie tworzenia oprogramowania. Pozwala to zaoszczędzić czas i pieniądze później.

Jest to model zagrożeń dla przykładowej aplikacji:

![Model zagrożeń](./media/secure-pci-web-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń przedstawiono na poniższym zrzucie ekranu. Model zagrożeń zawiera przegląd narażonych obszarów ataków i umożliwia deweloperom zaprezentowanie informacji o sposobach łagodzenia problemów.

![Dane wyjściowe modelu zagrożeń](./media/secure-web-app/threat-model-output.png)

Na przykład iniekcja kodu SQL w poprzednim wyjściu modelu zagrożeń jest zmniejszana przez oczyszczanie danych wejściowych użytkownika i używanie funkcji przechowywanych w Azure Database for PostgreSQL. To ograniczenie uniemożliwia wykonywanie zapytań podczas odczytu i zapisu danych.

Deweloperzy zwiększają ogólne zabezpieczenia systemu przez łagodzenie poszczególnych zagrożeń w danych wyjściowych modelu zagrożeń.

## <a name="deployment"></a>Wdrożenie
### <a name="prerequisites"></a>Wymagania wstępne
Aby można było uruchomić aplikację, należy zainstalować następujące narzędzia:

- Edytor kodu służący do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) to opcja "open source".
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) na komputerze deweloperskim.
- System [git](https://git-scm.com/) w systemie. Git służy do lokalnego klonowania kodu źródłowego.
- [JQ](https://stedolan.github.io/jq/), narzędzie systemu UNIX do wykonywania zapytań w formacie JSON w sposób przyjazny dla użytkownika.

Ten przykład składa się z plików konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API Azure Resource Manager do wdrażania zasobów na platformie Azure. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Szybki start

1.  Sklonuj lub Pobierz [to](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repozytorium GitHub do lokalnej stacji roboczej.
2.  Przejrzyj 0-Setup-AdministrativeAccountAndPermission.md i uruchom podane polecenia.
3.  Wdróż rozwiązanie testowe za pomocą przykładowych danych firmy Contoso lub pilotażowe środowisko produkcyjne.

    Ten skrypt wdraża zasoby platformy Azure w celu pokazania sklepu internetowego przy użyciu przykładowych danych firmy Contoso.

W tym przykładzie zostanie uruchomiony skrypt wdrażania, który wdraża aplikację internetową na App Service i tworzy zasoby.

Istnieje wiele sposobów wdrażania aplikacji na platformie Azure, w tym:

- Szablony usługi Azure Resource Manager
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="network"></a>Sieć
Architektura definiuje prywatny Virtual Network z przestrzenią adresową 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy Access Control (ACL), które zezwalają na ruch w sieci wirtualnej lub go odmawiają. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:

- 1 sieciowa Grupa zabezpieczeń dla Application Gateway
- 1 sieciowa Grupa zabezpieczeń dla App Service Environment
- 1 sieciowa Grupa zabezpieczeń dla Azure SQL Database
- 1 sieciowa Grupa zabezpieczeń dla hosta bastionu

Każda z grup zabezpieczeń sieci ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

- Dzienniki i zdarzenia diagnostyczne (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu)
- Dzienniki Azure Monitor są połączone z diagnostyką sieciowej grupy zabezpieczeń (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Konfiguracja sieciowej grupy zabezpieczeń
Konfiguracja sieciowej grupy zabezpieczeń dla App Service Environment powinna być skonfigurowana jak pokazano na poniższej ilustracji.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

### <a name="config"></a>Config
Podsieci są konfigurowane, jak pokazano na poniższej ilustracji.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>System DNS platformy Azure
System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozpoznanie) nazwy witryny sieci Web lub usługi na adres IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure umożliwia użytkownikom Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure. Azure DNS obsługuje również prywatne domeny DNS.

### <a name="protect-data"></a>Ochrona danych
Aby chronić dane w chmurze, należy uwzględnić możliwe stany, w których mogą wystąpić dane, oraz informacje o kontrolkach dostępnych dla tego stanu. Najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania danych platformy Azure odnoszą się do następujących stanów danych:

- W spoczynku: obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które istnieją statycznie na nośniku fizycznym, niezależnie od tego, czy dysk magnetyczny czy optyczny.
- Podczas przesyłania: gdy dane są przesyłane między składnikami, lokalizacjami lub programami, jest w trakcie przesyłania. Przykłady są przesyłane przez sieć, przez magistralę usług (od lokalnego do chmury i na odwrót, łącznie z połączeniami hybrydowymi, takimi jak ExpressRoute), lub w procesie wejścia/wyjścia.

### <a name="azure-storage"></a>Azure Storage
Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących spoczynku, cała [usługa Azure Storage](https://azure.microsoft.com/services/storage/) używa Azure Key Vault, aby zachować kontrolę nad kluczami, które mają dostęp do danych i je szyfrować. Pomaga to w ochronie i ochronie danych posiadaczy kart w celu wspierania zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez PCI DSS 3,2.

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
Azure Disk Encryption korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

### <a name="azure-sql-database"></a>Azure SQL Database
W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
- Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych w taki sposób, aby nie zamykał bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Poniższe technologie zapewniają możliwości zarządzania dostępem do danych posiadaczy kart w środowisku platformy Azure:

- Azure Active Directory to oparta na chmurze usługa zarządzania tożsamościami i tożsamość firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz [jak chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Kontrola dostępu oparta na rolach na platformie Azure umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych posiadaczy tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- Azure Active Directory Privileged Identity Management umożliwia klientom zminimalizowanie liczby użytkowników, którzy mają dostęp do pewnych informacji, takich jak dane posiadaczy kart. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- Azure Active Directory Identity Protection wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="secrets-management"></a>Zarządzanie wpisami tajnymi
Rozwiązanie używa Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza to chroniony przez moduł HSM 2048-bitowy klucz RSA.
- Za pomocą Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń)
- Funkcja RBAC umożliwia przypisywanie uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie.
- Użyj Key Vault do zarządzania certyfikatami TLS przy użyciu autoodnawiania.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

### <a name="azure-security-center"></a>Azure Security Center
Dzięki Azure Security Center klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw wstępnie zdefiniowanych alertów zabezpieczeń, które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. Niestandardowe reguły alertów w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. Raport analizy zagrożeń jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Architektura zmniejsza ryzyko wystąpienia luk w zabezpieczeniach przy użyciu Application Gateway platformy Azure z skonfigurowaną zaporą aplikacji sieci Web, a zestaw reguł OWASP jest włączony. Dodatkowe możliwości obejmują:

- Kompleksowa — SSL
- Wyłącz protokół TLS w wersji 1.0 i 1.1
- Włącz TLSv 1.2
- Zapora aplikacji sieci Web (tryb zapobiegania)
- Tryb zapobiegania z zestawem reguł OWASP 3,0
- Włączanie rejestrowania diagnostycznego
- Niestandardowe sondy kondycji
- Azure Security Center i Azure Advisor zapewniają dodatkową ochronę i powiadomienia. Azure Security Center udostępnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji
Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:

- [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są zorganizowane w oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) platformy Azure są dostępne w ramach tej architektury:

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): rozwiązanie Active Directory Health Check ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązanie sprawdzania kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i oferuje klientom z priorytetową listą zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów oraz ich dystrybucję geograficzną, a także liczbę agentów, którzy nie odpowiadają, i liczby agenci, którzy przesyłają dane operacyjne.
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci Web na wielu platformach. Application Insights wykrywa anomalie wydajności i klienci mogą używać jej do monitorowania działającej aplikacji sieci Web. Zawiera ona zaawansowane narzędzia analityczne, które ułatwiają klientom diagnozowanie problemów i zrozumienie, jakie użytkownicy faktycznie robią z ich aplikacjami. Zaprojektowano, aby pomóc klientom w ciągłym ulepszaniu wydajności i użyteczności.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Utwórz magazyn dla organizacji, w której mają być przechowywane klucze, i utrzymaj odpowiedzialność za zadania operacyjne podobne do poniższych:

- Dane przechowywane w Key Vault obejmują:

   - Klucz usługi Application Insight
   - Klucz dostępu do magazynu danych
   - Parametry połączenia
   - Nazwa tabeli danych
   - Poświadczenia użytkownika

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia
- Wszystkie klucze w Key Vault są chronione przez moduł HSM [typ klucza = chronione przez moduł HSM 2048-bitowy klucz RSA]
- Wszyscy użytkownicy/tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu Access Control opartego na rolach (RBAC)
- Aplikacje nie udostępniają Key Vault, chyba że ufają sobie nawzajem i potrzebują dostępu do tych samych wpisów tajnych w czasie wykonywania
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych wymaganych

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute
Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. Tryb tunelowania IPsec jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatny. Przejdź do [strony bezpłatnego konta](https://azure.microsoft.com/free/) , aby rozpocząć pracę, zobacz co możesz zrobić za pomocą bezpłatnego konta platformy Azure i Dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, musisz uregulować pewne funkcje w warstwie Premium. Gdy aplikacja jest skalowana i warstwy bezpłatne i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą ulec zwiększeniu. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure, aby oszacować swoje koszty.

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły mogą pomóc w projektowaniu, projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie](secure-design.md)
- [Programowanie](secure-develop.md)
- [Wdróż](secure-deploy.md)
