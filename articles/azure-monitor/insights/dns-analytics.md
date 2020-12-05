---
title: DNS Analytics rozwiązanie w Azure Monitor | Microsoft Docs
description: Skonfiguruj rozwiązanie DNS Analytics w Azure Monitor, aby zebrać szczegółowe informacje dotyczące infrastruktury DNS w zakresie zabezpieczeń, wydajności i operacji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 7bdea9239faa4ec66fffa236bea40afd5e628e62
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607147"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Zbierz informacje o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics w wersji zapoznawczej

![Symbol DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania Azure DNS Analytics w Azure Monitor w celu zbierania szczegółowych informacji o infrastrukturze DNS w zakresie zabezpieczeń, wydajności i operacji.

DNS Analytics ułatwia:

- Zidentyfikuj klientów próbujących rozpoznać złośliwe nazwy domen.
- Identyfikowanie starych rekordów zasobów.
- Zidentyfikuj często badane nazwy domen i próg klientów DNS.
- Wyświetl obciążenie żądań na serwerach DNS.
- Wyświetl błędy rejestracji dynamicznej usługi DNS.

Rozwiązanie gromadzi, analizuje i skorelowanie dzienników analitycznych i inspekcji systemu Windows DNS oraz innych powiązanych danych z serwerów DNS.

## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| **Połączone źródło** | **Pomoc techniczna** | **Opis** |
| --- | --- | --- |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Tak | Rozwiązanie zbiera informacje DNS z agentów systemu Windows. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji DNS z bezpośrednich agentów systemu Linux. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Tak | Rozwiązanie zbiera informacje DNS z agentów w połączonej grupie zarządzania Operations Manager. Bezpośrednie połączenie z agenta Operations Manager do Azure Monitor nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego Log Analytics. |
| [Konto usługi Azure Storage](../platform/resource-logs.md#send-to-log-analytics-workspace) | Nie | Usługa Azure Storage nie jest używana przez rozwiązanie. |

### <a name="data-collection-details"></a>Szczegóły zbierania danych

Rozwiązanie zbiera dane dotyczące spisu DNS i zdarzeń DNS z serwerów DNS, na których zainstalowano agenta Log Analytics. Te dane są następnie przekazywane do Azure Monitor i wyświetlane na pulpicie nawigacyjnym rozwiązania. Dane dotyczące spisu, takie jak liczba serwerów DNS, stref i rekordów zasobów, są zbierane przez uruchomienie poleceń cmdlet programu PowerShell dla usługi DNS. Dane są aktualizowane co dwa dni. Dane związane ze zdarzeniami są zbierane niemal w czasie rzeczywistym z [dzienników analitycznych i inspekcji](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) dostarczonych przez ulepszone rejestrowanie i diagnostykę DNS w systemie Windows Server 2012 R2.

## <a name="configuration"></a>Konfiguracja

Skorzystaj z poniższych informacji, aby skonfigurować rozwiązanie:

- Na każdym serwerze DNS, który ma być monitorowany, musi znajdować się Agent [systemu Windows](../platform/agent-windows.md) lub [Operations Manager](../platform/om-agents.md) .
- Rozwiązanie DNS Analytics można dodać do obszaru roboczego Log Analytics z [witryny Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Można również użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](solutions.md).

Rozwiązanie uruchamia zbieranie danych bez konieczności dalszej konfiguracji. Można jednak użyć poniższej konfiguracji, aby dostosować zbieranie danych.

### <a name="configure-the-solution"></a>Skonfiguruj rozwiązanie

Na pulpicie nawigacyjnym rozwiązania kliknij pozycję **Konfiguracja** , aby otworzyć stronę Konfiguracja DNS Analytics. Istnieją dwa typy zmian konfiguracji, które można wprowadzić:

- **Allowlisted nazwy domen**. Rozwiązanie nie przetwarza wszystkich zapytań wyszukiwania. Obsługuje ona dozwolonych sufiksów nazw domen. Zapytania wyszukiwania, które są rozpoznawane jako nazwy domen pasujące do sufiksów nazw domen w tym dozwolonych, nie są przetwarzane przez rozwiązanie. Nie przetwarzanie nazw domen allowlisted pomaga zoptymalizować dane wysyłane do Azure Monitor. Domyślne dozwolonych obejmują Popularne nazwy domen publicznych, takie jak www.google.com i www.facebook.com. Możesz wyświetlić kompletną listę domyślną, przewijając.

  Możesz zmodyfikować listę, aby dodać dowolny sufiks nazwy domeny, dla którego chcesz wyświetlić szczegółowe informacje dotyczące wyszukiwania. Możesz również usunąć dowolny sufiks nazwy domeny, dla którego chcesz wyświetlić szczegółowe informacje dotyczące wyszukiwania.

- **Próg klienta próg**. Klienci DNS, którzy przekroczyli wartość progową liczby żądań wyszukiwania, są wyróżnione w bloku **klienci DNS** . Domyślny próg to 1 000. Próg można edytować.

    ![Allowlisted nazwy domen](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli używasz Microsoft Monitoring Agent do nawiązywania połączenia z obszarem roboczym Log Analytics, zostanie zainstalowany następujący pakiet administracyjny:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Jeśli grupa zarządzania Operations Manager jest połączona z obszarem roboczym Log Analytics, następujące pakiety administracyjne są instalowane w Operations Manager po dodaniu tego rozwiązania. Nie istnieje wymagana konfiguracja ani konserwacja tych pakietów administracyjnych:

- Microsoft DNS Data Collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Konfiguracja DNS Analytics programu Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configwersja)

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../platform/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="use-the-dns-analytics-solution"></a>Użyj rozwiązania DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Kafelek DNS zawiera liczbę serwerów DNS, na których są zbierane dane. Zawiera również liczbę żądań podejmowanych przez klientów w celu rozpoznania złośliwych domen w ciągu ostatnich 24 godzin. Po kliknięciu kafelka zostanie otwarty pulpit nawigacyjny rozwiązania.

![Kafelek DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Pulpit nawigacyjny rozwiązania

Pulpit nawigacyjny rozwiązania zawiera podsumowanie informacji o różnych funkcjach rozwiązania. Zawiera również linki do widoku szczegółowego na potrzeby analizy i diagnostyki śledczej. Domyślnie dane są wyświetlane w ciągu ostatnich siedmiu dni. Można zmienić zakres dat i godzin przy użyciu **kontrolki wyboru daty i godziny**, jak pokazano na poniższej ilustracji:

![Kontrola wyboru czasu](./media/dns-analytics/dns-time.png)

Pulpit nawigacyjny rozwiązania pokazuje następujące bloki:

**Zabezpieczenia systemu DNS**. Raportuje klientów DNS próbujących komunikować się ze złośliwymi domenami. Korzystając z kanałów informacyjnych analizy zagrożeń firmy Microsoft, DNS Analytics mogą wykrywać adresy IP klientów próbujących uzyskać dostęp do złośliwych domen. W wielu przypadkach urządzenia Zainfekowane złośliwym oprogramowaniem są "wybierane" w centrum "polecenie i kontrola" złośliwej domeny przez rozpoznanie nazwy domeny złośliwego oprogramowania.

![Blok zabezpieczenia DNS](./media/dns-analytics/dns-security-blade.png)

Po kliknięciu adresu IP klienta na liście zostanie otwarte wyszukiwanie w dzienniku i zostanie wyświetlone szczegóły odnośnika odpowiedniego zapytania. W poniższym przykładzie DNS Analytics wykryła, że komunikacja została wykonana z [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Wyniki wyszukiwania w dzienniku pokazujące IRCBot](./media/dns-analytics/ircbot.png)

Informacje ułatwiają zidentyfikowanie:

- Adres IP klienta, który zainicjował komunikację.
- Nazwa domeny, która jest rozpoznawana jako złośliwy adres IP.
- Adresy IP, do których jest rozpoznawana nazwa domeny.
- Złośliwy adres IP.
- Ważność problemu.
- Powód blocklisting złośliwego adresu IP.
- Czas wykrycia.

**Domeny z zapytaniem**. Zapewnia najczęstsze nazwy domen, które są badane przez klientów DNS w danym środowisku. Można wyświetlić listę wszystkich nazw domen, których dotyczy kwerenda. Możesz również przejść do szczegółów żądania wyszukiwania dla konkretnej nazwy domeny w przeszukiwaniu dzienników.

![Blok zapytań o domeny](./media/dns-analytics/domains-queried-blade.png)

**Klienci DNS**. Zgłasza klientom *naruszenie progu* liczby zapytań w wybranym okresie. Można wyświetlić listę wszystkich klientów DNS i szczegóły zapytań wykonywanych przez nich w przeszukiwaniu dzienników.

![Blok klientów DNS](./media/dns-analytics/dns-clients-blade.png)

**Dynamiczne rejestracje DNS**. Zgłasza błędy rejestracji nazw. Wszystkie błędy rejestracji dla [rekordów zasobów](https://en.wikipedia.org/wiki/List_of_DNS_record_types) adresu (typu a i AAAA) są wyróżnione wraz z adresami IP klientów, którzy wykonali żądania rejestracji. Korzystając z tych informacji, można znaleźć główną przyczynę niepowodzenia rejestracji, wykonując następujące czynności:

1. Znajdź strefę autorytatywną dla nazwy, którą klient próbuje zaktualizować.

1. Użyj rozwiązania, aby sprawdzić informacje o spisie tej strefy.

1. Sprawdź, czy aktualizacja dynamiczna strefy jest włączona.

1. Sprawdź, czy strefa jest skonfigurowana pod kątem zabezpieczonych aktualizacji dynamicznych.

    ![Blok dynamiczne rejestracje DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Żądania rejestracji nazw**. Górny kafelek przedstawia linię trendu żądań aktualizacji dynamicznych DNS zakończonych powodzeniem i niepowodzeniem. Dolny kafelek zawiera listę 10 klientów, którzy wysyłają Nieudane żądania aktualizacji DNS do serwerów DNS, posortowane według liczby błędów.

![Blok żądania rejestracji nazw](./media/dns-analytics/name-reg-req-blade.png)

**Przykładowe zapytania analizy interfejsu DDI**. Zawiera listę najczęściej używanych zapytań wyszukiwania, które bezpośrednio pobierają dane z nieprzetworzonej analizy.


![Przykładowe zapytania](./media/dns-analytics/queries.png)

Tych zapytań można użyć jako punktu wyjścia do tworzenia własnych zapytań na potrzeby niestandardowego raportowania. Zapytanie prowadzi do strony przeszukiwania dzienników DNS Analytics, w której są wyświetlane wyniki:

- **Lista serwerów DNS**. Przedstawia listę wszystkich serwerów DNS z skojarzoną nazwą FQDN, domeną, nazwą lasu i adresami IP serwerów.
- **Lista strefy DNS**. Przedstawia listę wszystkich stref DNS ze skojarzoną nazwą strefy, stanem aktualizacji dynamicznej, serwerami nazw i stanem podpisywania DNSSEC.
- **Nieużywane rekordy zasobów**. Przedstawia listę wszystkich nieużywanych/starych rekordów zasobów. Ta lista zawiera nazwę rekordu zasobu, typ rekordu zasobu, skojarzony serwer DNS, czas generowania rekordu i nazwę strefy. Za pomocą tej listy można identyfikować rekordy zasobów DNS, które nie są już używane. Na podstawie tych informacji można następnie usunąć te wpisy z serwerów DNS.
- **Obciążenie zapytania serwerów DNS**. Przedstawia informacje, dzięki którym można uzyskać perspektywę obciążenia DNS na serwerach DNS. Te informacje mogą pomóc w planowaniu pojemności serwerów. Możesz przejść do karty **metryki** , aby zmienić widok na graficzną wizualizację. Ten widok ułatwia zrozumienie sposobu dystrybuowania obciążenia DNS na serwerach DNS. Pokazuje trendy szybkości zapytań DNS dla każdego serwera.

    ![Wyniki wyszukiwania dzienników przez serwery DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Strefy DNS obciążenie zapytania**. Przedstawia statystykę strefy DNS zapytania na sekundę wszystkich stref na serwerach DNS zarządzanych przez rozwiązanie. Kliknij kartę **metryki** , aby zmienić widok ze szczegółowych rekordów na graficzną wizualizację wyników.
- **Zdarzenia konfiguracji**. Pokazuje wszystkie zdarzenia zmiany konfiguracji DNS i powiązane komunikaty. Następnie można filtrować te zdarzenia na podstawie czasu zdarzenia, identyfikatora zdarzenia, serwera DNS lub kategorii zadań. Dane mogą ułatwić inspekcję zmian wprowadzonych w określonych serwerach DNS w określonych godzinach.
- **Dziennik analityczny DNS**. Przedstawia wszystkie zdarzenia analityczne na wszystkich serwerach DNS zarządzanych przez rozwiązanie. Następnie można filtrować te zdarzenia na podstawie czasu zdarzenia, identyfikatora zdarzenia, serwera DNS, adresu IP klienta, który dokonał kwerendy wyszukiwania, oraz kategorii zadań typ zapytania. Zdarzenia analityczne serwera DNS umożliwiają śledzenie działań na serwerze DNS. Zdarzenie analityczne jest rejestrowane za każdym razem, gdy serwer wysyła lub odbiera informacje DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Wyszukiwanie przy użyciu przeszukiwania dzienników DNS Analytics

Na stronie przeszukiwanie dzienników można utworzyć zapytanie. Wyniki wyszukiwania można filtrować przy użyciu formantów aspektów. Możesz również tworzyć zaawansowane zapytania do przekształcania, filtrowania i raportowania wyników. Zacznij od następującej kwerendy:

1. W **polu zapytania wyszukiwania** wpisz, `DnsEvents` Aby wyświetlić wszystkie zdarzenia DNS wygenerowane przez serwery DNS zarządzane przez rozwiązanie. Wyniki wyświetlają dane dziennika dla wszystkich zdarzeń związanych z zapytaniami wyszukiwania, rejestracjami dynamicznymi i zmianami konfiguracji.

    ![Przeszukiwanie dzienników DnsEvents](./media/dns-analytics/log-search-dnsevents.png)  

    a. Aby wyświetlić dane dziennika dla zapytań wyszukiwania, wybierz pozycję **LookUpQuery** jako filtr **podtypu** z kontrolki aspekt po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń zapytania wyszukiwania dla wybranego okresu.

    b. Aby wyświetlić dane dziennika dla rejestracji dynamicznych, wybierz pozycję **DynamicRegistration** jako filtr **podtypu** z kontrolki aspekt po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń rejestracji dynamicznej dla wybranego okresu.

    c. Aby wyświetlić dane dziennika dla zmian konfiguracji, wybierz pozycję **zmianakonfiguracji** jako filtr **podtypu** z kontrolki aspekt po lewej stronie. Zostanie wyświetlona tabela zawierająca listę wszystkich zdarzeń zmiany konfiguracji w wybranym okresie.

1. W **polu zapytania wyszukiwania** wpisz, `DnsInventory` Aby wyświetlić wszystkie dane dotyczące spisu DNS dla serwerów DNS zarządzanych przez rozwiązanie. Wyniki wyświetlają dane dziennika dla serwerów DNS, stref DNS i rekordów zasobów.

    ![Przeszukiwanie dzienników DnsInventory](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Typowe kroki rozwiązywania problemów:

1. Brakujące dane wyszukiwania DNS — aby rozwiązać ten problem, spróbuj zresetować konfigurację lub po prostu Załaduj stronę konfiguracji raz w portalu. Na potrzeby resetowania po prostu zmień ustawienie na inną wartość, a następnie zmień je z powrotem na oryginalną wartość i Zapisz konfigurację.

## <a name="suggestions"></a>Sugestie

Aby przekazać opinię, odwiedź [stronę log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) , aby opublikować pomysły dotyczące DNS Analyticsch funkcji. 

## <a name="next-steps"></a>Następne kroki

[Dzienniki zapytań](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe rekordy dziennika DNS.
