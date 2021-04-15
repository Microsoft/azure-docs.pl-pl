---
title: Co nowego w programie Azure Sentinel
description: W tym artykule opisano nowe funkcje Azure Sentinel w ciągu ostatnich kilku miesięcy.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376039"
---
# <a name="whats-new-in-azure-sentinel"></a>Co nowego w programie Azure Sentinel

W tym artykule wymieniono najnowsze funkcje dodane Azure Sentinel i nowe funkcje w powiązanych usługach, które zapewniają ulepszone środowisko użytkownika w Azure Sentinel.

Aby uzyskać informacje o poprzednich dostarczonych funkcjach, zobacz [blogi społeczności technicznej](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Zauważyć funkcje są obecnie w wersji zapoznawczej. Postanowienia [uzupełniające dotyczące wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) platformy Azure obejmują dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.


> [!TIP]
> Nasze zespoły do wyszukiwania zagrożeń w firmie Microsoft współtwoują zapytania, podręczniki, skoroszyty i notesy w społeczności [Azure Sentinel,](https://github.com/Azure/Azure-Sentinel)w tym konkretne zapytania dotyczące wyszukiwania zagrożeń, które zespoły mogą dostosować i których mogą używać. [](https://github.com/Azure/Azure-Sentinel)
>
> Możesz również współtłać! Dołącz do nas w [społeczności Azure Sentinel Threat GitHub.](https://github.com/Azure/Azure-Sentinel/wiki)
>

## <a name="april-2021"></a>Kwiecień 2021 r.

- [Oś czasu zdarzenia (publiczna wersja zapoznawcza)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Oś czasu zdarzenia (publiczna wersja zapoznawcza)

Pierwszą kartą na stronie szczegółów zdarzenia jest teraz **Oś czasu**, która pokazuje oś czasu alertów i zakładek w zdarzeniu. Oś czasu zdarzenia może pomóc w lepszym zrozumieniu zdarzenia i odtworzenie osi czasu działań osoby atakującej w powiązanych alertach i zakładkach.

- Wybierz element na osi czasu, aby wyświetlić jego szczegóły bez opuszczania kontekstu zdarzenia
- Filtruj zawartość osi czasu, aby wyświetlić tylko alerty lub zakładki albo elementy o określonej ważności lub taktyki MITRE.
- Możesz wybrać link **Identyfikator alertu systemowego,** aby wyświetlić cały rekord, lub link **Zdarzenia,** aby wyświetlić powiązane zdarzenia w **obszarze** Dzienniki.

Na przykład:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Karta Oś czasu zdarzenia":::

Aby uzyskać więcej informacji, zobacz [Samouczek: badanie zdarzeń za pomocą Azure Sentinel](tutorial-investigate-cases.md).

## <a name="march-2021"></a>Marzec 2021 r.

- [Ustawianie automatycznego odświeżania skoroszytów w trybie wyświetlania](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nowe wykrycia dla Azure Firewall](#new-detections-for-azure-firewall)
- [Reguły automatyzacji i podręczniki wyzwalane przez zdarzenia](#automation-rules-and-incident-triggered-playbooks) (w tym całą nową dokumentację podręcznika)
- [Nowe wzbogacenia alertów: ulepszone mapowanie jednostek i niestandardowe szczegóły](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Drukowanie skoroszytów Azure Sentinel lub zapisywanie w formacie PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtry zdarzeń i preferencje sortowania są teraz zapisywane w sesji (publiczna wersja zapoznawcza)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 zdarzenia usługi Defender (publiczna wersja zapoznawcza)](#microsoft-365-defender-incident-integration-public-preview)
- [Nowe łączniki usług firmy Microsoft przy użyciu Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Ustawianie automatycznego odświeżania skoroszytów w trybie wyświetlania

Azure Sentinel użytkownicy mogą teraz używać nowej [Azure Monitor automatycznego](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) odświeżania danych skoroszytu podczas sesji widoku.

W każdym skoroszycie lub szablonie skoroszytu wybierz :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **pozycję Odśwież automatycznie,** aby wyświetlić opcje interwału. Wybierz opcję, której chcesz użyć dla bieżącej sesji widoku, a następnie wybierz pozycję **Zastosuj.**

- Obsługiwane interwały odświeżania to **od 5 minut** **do 1 dnia.**
- Domyślnie automatyczne odświeżanie jest wyłączone. Aby zoptymalizować wydajność, automatyczne odświeżanie jest również wyłączone przy każdym zamknięciu skoroszytu i nie działa w tle. Włącz automatyczne odświeżanie ponownie w razie potrzeby przy następnym otwarciu skoroszytu.
- Automatyczne odświeżanie jest wstrzymane podczas edytowania skoroszytu, a interwały automatycznego odświeżania są ponownie uruchamiany po każdym przełączeniu z powrotem do trybu wyświetlania z trybu edycji.

    Interwały są również ponownie uruchomione, jeśli ręcznie odświeżysz skoroszyt, wybierając :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **przycisk Odśwież.**

Aby uzyskać więcej informacji, zobacz [Samouczek: wizualizowanie i monitorowanie danych](tutorial-monitor-your-data.md) oraz Azure Monitor [dokumentacji.](../azure-monitor/visualize/workbooks-overview.md)

### <a name="new-detections-for-azure-firewall"></a>Nowe wykrycia dla Azure Firewall

Do obszaru Analiza w obszarze analizy w Azure Sentinel dodano [](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) kilka funkcji wykrywania Azure Firewall od Azure Sentinel. Te nowe wykrycia umożliwiają zespołom ds. zabezpieczeń uzyskiwanie alertów, jeśli maszyny w sieci wewnętrznej spróbują odpytują lub połączą się z nazwami domen internetowych lub adresami IP skojarzonymi ze znanymi we/wy zgodnie z definicją w zapytaniu reguły wykrywania.

Nowe wykrycia obejmują:

- [Solorigate Network Sygnał nawigacyjny](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Znane domeny i skróty GALIUM](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Znany adres IP IRIDIUM](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Znane domeny grupy Worus/adres IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domeny FIRMY THALLIUM uwzględnione w odjęciu dcu](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Znany skrót maldoc związany z algorytmem HASH](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Znane domeny grup STRONTIUM](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM — Kontrolery we/wy domeny i adresów IP — marzec 2021 r.](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Wykrywanie za pomocą usługi Azure Firewall jest stale dodawane do wbudowanej galerii szablonów. Aby uzyskać najnowsze wykrycia dla Azure Firewall, w obszarze Szablony reguł odfiltruj źródła danych, **Azure Firewall** **:**

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nowe wykrycia w skoroszycie wydajności analizy":::

Aby uzyskać więcej informacji, zobacz [New detections for Azure Firewall in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Reguły automatyzacji i podręczniki wyzwalane przez zdarzenia

Reguły automatyzacji to nowe pojęcie w Azure Sentinel, które umożliwia centralne zarządzanie automatyzacją obsługi zdarzeń. Oprócz umożliwienia przypisywania elementów playbook do zdarzeń (nie tylko do alertów, jak wcześniej), reguły automatyzacji umożliwiają również automatyzację odpowiedzi dla wielu reguł analizy jednocześnie, automatyczne tagowanie, przypisywanie lub zamykanie zdarzeń bez konieczności użycia elementów playbook oraz kontrolowanie kolejności wykonywanych akcji. Reguły automatyzacji usprawniają automatyzację Azure Sentinel i umożliwiają uproszczenie złożonych przepływów pracy dla procesów orkiestracji zdarzeń.

Dowiedz się więcej z tego [pełnego wyjaśnienia reguł automatyzacji.](automate-incident-handling-with-automation-rules.md)

Jak wspomniano powyżej, podręczniki oprócz wyzwalacza alertu można teraz aktywować za pomocą wyzwalacza zdarzenia. Wyzwalacz zdarzenia udostępnia podręcznikom większy zestaw danych wejściowych do pracy (ponieważ zdarzenie obejmuje również wszystkie dane alertów i jednostek), co zapewnia jeszcze większą moc i elastyczność przepływów pracy odpowiedzi. Podręczniki wyzwalane przez zdarzenia są aktywowane przez wywoływanie z reguł automatyzacji.

Dowiedz się więcej o [ulepszonych](automate-responses-with-playbooks.md)możliwościach podręczników i [dziel](tutorial-respond-threats-playbook.md) się przepływem pracy odpowiedzi przy użyciu podręczników wraz z regułami automatyzacji.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nowe wzbogacenia alertów: ulepszone mapowanie jednostek i niestandardowe szczegóły

Wzbogać alerty na dwa nowe sposoby, aby były bardziej użyteczne i bardziej informacyjne.

Zacznij od ustawienia mapowania jednostki na następny poziom. Teraz można mapować niemal 20 rodzajów jednostek, od użytkowników, hostów i adresów IP, do plików i procesów, do skrzynek pocztowych, zasobów platformy Azure i urządzeń IoT. Można również użyć wielu identyfikatorów dla każdej jednostki, aby zwiększyć ich unikatową identyfikację. Zapewnia to znacznie bogatszy zestaw danych w zdarzeniach, zapewniając szerszą korelację i bardziej zaawansowane badanie. [Poznaj nowy sposób mapowania jednostek w](map-data-fields-to-entities.md) alertach.

[Przeczytaj więcej na temat jednostek](entities-in-azure-sentinel.md) i zobacz pełną [listę dostępnych jednostek i ich identyfikatorów.](entities-reference.md)

Zwiększ możliwości badania i reagowania dzięki dostosowywaniu alertów w celu nakierowania szczegółów na nieprzetworzone zdarzenia. Zapewnianie wglądu w zawartość zdarzeń w zdarzeniach, co zapewnia jeszcze większą moc i elastyczność reagowania na zagrożenia bezpieczeństwa i badania ich. [Dowiedz się, jak na powierzchni niestandardowych szczegółów](surface-custom-details-in-alerts.md) alertów.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Drukowanie skoroszytów Azure Sentinel lub zapisywanie w formacie PDF

Teraz możesz drukować Azure Sentinel, co umożliwia również eksportowanie do nich plików PDF i zapisywanie lokalnie lub udostępnianie.

W skoroszycie wybierz menu opcji > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Drukuj zawartość.** Następnie wybierz drukarkę lub wybierz pozycję **Zapisz jako plik PDF** zgodnie z potrzebami.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Wydrukuj skoroszyt lub zapisz go jako plik PDF.":::

Aby uzyskać więcej informacji, zobacz [Samouczek: wizualizowanie i monitorowanie danych.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtry zdarzeń i preferencje sortowania są teraz zapisane w sesji (publiczna wersja zapoznawcza)

Teraz filtry zdarzeń i sortowanie są zapisywane w Azure Sentinel, nawet podczas przechodzenia do innych obszarów produktu.
Tak długo, jak nadal jesteś w tej samej [](tutorial-investigate-cases.md) sesji, przejście z powrotem do obszaru Zdarzenia w programie Azure Sentinel pokazuje filtry i sortowanie tak samo, jak zostało to pozostawione.

> [!NOTE]
> Filtry zdarzeń i sortowanie nie są zapisywane po opuszczeniu Azure Sentinel odświeżeniu przeglądarki.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 zdarzenia usługi Defender (publiczna wersja zapoznawcza)

Azure Sentinel zdarzeń usługi [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) umożliwia przesyłanie strumieniowe wszystkich zdarzeń M365D do usługi Azure Sentinel i synchronizowanie ich między dwoma portalami. Zdarzenia z usługi M365D (wcześniej znane jako Microsoft Threat Protection lub MTP) obejmują wszystkie skojarzone alerty, jednostki i istotne informacje, zapewniając kontekst wystarczający do przeprowadzenia triage i wstępnego badania w Azure Sentinel. W przypadku korzystania z usługi Sentinel zdarzenia pozostaną zsynchronizowane dwukierunkowo z usługą M365D, co pozwoli na skorzystanie z zalet obu portali podczas badania zdarzeń.

Korzystanie zarówno Azure Sentinel, jak i Microsoft 365 Defender zapewnia najlepsze rozwiązania z obu tych świata. Możesz uzyskać zakres szczegółowych informacji zapewnianych przez rozwiązania SIEM w całym zakresie zasobów informacyjnych w organizacji, a także głębię dostosowanych i dostosowanych możliwości badania zapewnianych przez proces XDR w celu ochrony zasobów usługi Microsoft 365, zarówno tych skoordynowanych, jak i zsynchronizowanych w celu bezproblemowej operacji SOC.

Aby uzyskać więcej informacji, zobacz [Microsoft 365 Defender integration with Azure Sentinel (Integracja usługi Defender z Azure Sentinel](microsoft-365-defender-sentinel-integration.md)).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nowe łączniki usług firmy Microsoft przy użyciu Azure Policy

[Azure Policy](../governance/policy/overview.md) to usługa platformy Azure, która umożliwia stosowanie zasad do wymuszania i kontrolowania właściwości zasobu. Korzystanie z zasad gwarantuje, że zasoby pozostają zgodne ze standardami ładu IT.

Do właściwości zasobów, które mogą być kontrolowane przez zasady, należą tworzenie i obsługa dzienników diagnostycznych i inspekcji. Azure Sentinel teraz używa narzędzia Azure Policy, aby umożliwić zastosowanie wspólnego zestawu ustawień dzienników diagnostycznych do wszystkich (bieżących i przyszłych) zasobów określonego typu, których dzienniki chcesz pozysać do Azure Sentinel. Dzięki Azure Policy nie trzeba już ustawiać zasobów ustawień dzienników diagnostycznych według zasobu.

Azure Policy oparte na języku są teraz dostępne dla następujących usług platformy Azure:
- [Azure Key Vault (publiczna](connect-azure-key-vault.md) wersja zapoznawcza)
- [Azure Kubernetes Service (publiczna](connect-azure-kubernetes-service.md) wersja zapoznawcza)
- Azure SQL baz danych/serwerów (GA)

Klienci nadal będą mogli wysyłać dzienniki ręcznie dla konkretnych wystąpień i nie muszą używać aparatu zasad.

## <a name="february-2021"></a>Luty 2021 r.

- [Skoroszyt certyfikatu CMMC (Cybersecurity Maturity Model Certification)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Łączniki danych innych firm](#third-party-data-connectors)
- [Szczegółowe informacje o interfejsie UEBA na stronie jednostki (publiczna wersja zapoznawcza)](#ueba-insights-in-the-entity-page-public-preview)
- [Ulepszone wyszukiwanie zdarzeń (publiczna wersja zapoznawcza)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Skoroszyt z certyfikatem CMMC (Cybersecurity Maturity Model Certification)

Skoroszyt cmmc Azure Sentinel zapewnia mechanizm wyświetlania zapytań dziennika dostosowanych do kontrolek CMMC w portfolio firmy Microsoft, w tym ofert zabezpieczeń firmy Microsoft, usługi Office 365, Teams, Intune, Windows Virtual Desktop i wielu innych.

Skoroszyt cmmc umożliwia architektom zabezpieczeń, inżynierom, analitykom operacji zabezpieczeń, menedżerom i specjalistom IT uzyskanie wglądu w świadomość sycycyjną poziomu bezpieczeństwa obciążeń w chmurze. Istnieją również zalecenia dotyczące wybierania, projektowania, wdrażania i konfigurowania ofert firmy Microsoft w celu dostosowania ich do odpowiednich wymagań i praktyk dotyczących kontrolera CMMC.

Nawet jeśli nie jest wymagane zapewnienie zgodności z kontrolerem CMMC, skoroszyt kontrolera CMMC jest przydatny podczas tworzenia centrów operacji zabezpieczeń, opracowywania alertów, wizualizowania zagrożeń i zapewniania świadomości sytacyjnej obciążeń.

Uzyskaj dostęp do skoroszytu kontrolera CMMC w Azure Sentinel **Skoroszyty.** Wybierz **pozycję Szablon**, a następnie wyszukaj pozycję **CMMC.**

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Przełączanie i wyłączanie przewodnika po skoroszycie kontrolera CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Aby uzyskać więcej informacji, zobacz:

- [Azure Sentinel certyfikat modelu dojrzałości cyberbezpieczeństwa (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Samouczek: wizualizowanie i monitorowanie danych](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Łączniki danych innych firm

Nasza kolekcja integracji innych firm wciąż rośnie, a w ciągu ostatnich dwóch miesięcy dodano 30 łączników. Oto lista:

- [Agari Phishing Defense and Brand Protection](connect-agari-phishing-defense.md)
- [Zdarzenia zabezpieczeń akamai](connect-akamai-security-events.md)
- [Alsid dla usługi Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [Centrum zarządzania zabezpieczeniami programu ESET](connect-data-sources.md)
- [Obszar roboczy Google (dawniej G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [Dzienniki DNS NXLog](connect-nxlog-dns.md)
- [Inspekcja systemu Linux NXLog](connect-nxlog-linuxaudit.md)
- [Platforma Onapsis](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Baza wiedzy na temat zarządzania lukami w zabezpieczeniach firmy Qualys](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Zajmij się usługą](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Szczegółowe informacje o interfejsie UEBA na stronie jednostki (publiczna wersja zapoznawcza)

Strony Azure Sentinel jednostki zawierają okienko Szczegółowe [informacje,](identify-threats-with-entity-behavior-analytics.md#entity-insights)które wyświetla szczegółowe informacje dotyczące zachowania jednostki i pomaga szybko identyfikować anomalie i zagrożenia bezpieczeństwa.

Jeśli masz [włączoną](ueba-enrichments.md)funkcję UEBA i wybrano ramy czasowe co najmniej czterech dni, to okienko szczegółowych informacji będzie teraz zawierać również następujące nowe sekcje dotyczące szczegółowych informacji o analizie ueba:

|Sekcja  |Opis  |
|---------|---------|
|**UEBA Insights**     | Podsumowuje anomalie działań użytkownika: <br>— W różnych lokalizacjach geograficznych, urządzeniach i środowiskach<br>— Horyzonty czasu i częstotliwości w porównaniu z historią użytkownika <br>— W porównaniu z zachowaniem elementów równorzędnych <br>— W porównaniu z zachowaniem organizacji     |
|**Komunikacja równorzędna użytkowników oparta na członkostwie w grupie zabezpieczeń**     |   Wyświetla listę elementów równorzędnych użytkownika opartych na członkostwie w grupach zabezpieczeń usługi Azure AD, udostępniając zespołom ds. operacji zabezpieczeń listę innych użytkowników, którzy mają podobne uprawnienia.  |
|**Uprawnienia dostępu użytkowników do subskrypcji platformy Azure**     |     Przedstawia uprawnienia dostępu użytkownika do subskrypcji platformy Azure dostępnych bezpośrednio lub za pośrednictwem grup/podmiotów zabezpieczeń usługi Azure AD.   |
|**Wskaźniki zagrożeń związane z użytkownikiem**     |  Wyświetla kolekcję znanych zagrożeń związanych z adresami IP reprezentowanymi w działaniach użytkownika. Zagrożenia są wymienione według typu i rodziny zagrożeń oraz są wzbogacane przez usługę analizy zagrożeń firmy Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Ulepszone wyszukiwanie zdarzeń (publiczna wersja zapoznawcza)

Ulepszyliśmy środowisko wyszukiwania Azure Sentinel zdarzeń, co umożliwia szybsze nawigowanie po zdarzeniach podczas badania określonego zagrożenia.

Podczas wyszukiwania zdarzeń w Azure Sentinel można teraz wyszukiwać według następujących szczegółów zdarzenia:

- ID (Identyfikator)
- Tytuł
- Produkt
- Właściciel
- Tag

## <a name="january-2021"></a>Styczeń 2021 r.

- [Kreator reguł analizy: Ulepszone środowisko edytowania zapytań (publiczna wersja zapoznawcza)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Moduł Az.SecurityInsights programu PowerShell (publiczna wersja zapoznawcza)](#azsecurityinsights-powershell-module-public-preview)
- [Łącznik bazy danych SQL](#sql-database-connector)
- [Łącznik usługi Dynamics 365 (publiczna wersja zapoznawcza)](#dynamics-365-connector-public-preview)
- [Ulepszone komentarze do zdarzeń](#improved-incident-comments)
- [Dedykowane klastry usługi Log Analytics](#dedicated-log-analytics-clusters)
- [Tożsamości zarządzane aplikacji logiki](#logic-apps-managed-identities)
- [Ulepszone dostrajanie reguł za pomocą wykresów w wersji zapoznawczej reguł analizy](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Kreator reguł analizy: Ulepszone środowisko edytowania zapytań (publiczna wersja zapoznawcza)

Kreator Azure Sentinel reguły zaplanowanej analizy zawiera teraz następujące ulepszenia dotyczące pisania i edytowania zapytań:

-   Okno do edycji z możliwością rozwijania, które zapewnia więcej miejsca na ekranie do wyświetlania zapytania.
-   Wyróżnianie kluczowych słów w kodzie zapytania.
-   Rozszerzona obsługa autouzupełniania.
-   Walidacje zapytań w czasie rzeczywistym. Błędy w zapytaniu są teraz wyświetlane jako czerwony blok na pasku przewijania i jako czerwona kropka w nazwie karty **Ustaw regułę** logiki. Ponadto nie można zapisać zapytania z błędami.

Aby uzyskać więcej informacji, [zobacz Samouczek: tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Moduł Az.SecurityInsights programu PowerShell (publiczna wersja zapoznawcza)

Azure Sentinel obsługuje teraz nowy [moduł Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) programu PowerShell.

Moduł **Az.SecurityInsights** obsługuje typowe przypadki użycia systemu Azure Sentinel, takie jak interakcja ze zdarzeniami w celu zmiany ważności, ważności, właściciela itp., dodawanie komentarzy i etykiet do zdarzeń oraz tworzenie zakładek.

Mimo że zalecamy używanie [szablonów Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) dla potoku ci/CD, moduł **Az.SecurityInsights** jest przydatny w przypadku zadań po wdrożeniu i jest ukierunkowany na automatyzację SOC.  Na przykład automatyzacja SOC może obejmować kroki konfigurowania łączników danych, tworzenia reguł analizy lub dodawania akcji automatyzacji do reguł analizy.

Aby uzyskać więcej informacji, w tym pełną listę i opis dostępnych poleceń cmdlet, opisy parametrów i przykłady, zobacz dokumentację programu [PowerShell Az.SecurityInsights.](/powershell/module/az.securityinsights/)

### <a name="sql-database-connector"></a>Łącznik bazy danych SQL

Azure Sentinel teraz udostępnia łącznik bazy danych Azure SQL, który umożliwia przesyłanie strumieniowe dzienników inspekcji i diagnostycznych baz danych do usługi Azure Sentinel i ciągłe monitorowanie aktywności we wszystkich wystąpieniach.

Azure SQL to w pełni zarządzany aparat bazy danych typu platforma jako usługa (PaaS), który obsługuje większość funkcji zarządzania bazami danych, takich jak uaktualnianie, stosowanie poprawek, tworzenie kopii zapasowych i monitorowanie, bez udziału użytkownika.

Aby uzyskać więcej informacji, zobacz Connect Azure SQL database diagnostics and auditing logs (Łączenie z [Azure SQL danych diagnostycznych i dzienników inspekcji).](connect-azure-sql-logs.md)

### <a name="dynamics-365-connector-public-preview"></a>Łącznik usługi Dynamics 365 (publiczna wersja zapoznawcza)

Azure Sentinel udostępnia teraz łącznik dla usługi Microsoft Dynamics 365, który umożliwia zbieranie dzienników aktywności użytkowników, administratorów i pomocy technicznej aplikacji usługi Dynamics 365 w Azure Sentinel. Te dane mogą pomóc w inspekcji całego procesu przetwarzania danych i przeanalizowaniu ich pod uwagę pod uwagę możliwych naruszeń zabezpieczeń.

Aby uzyskać więcej informacji, zobacz [Connect Dynamics 365 activity logs to Azure Sentinel](connect-dynamics-365.md)(Łączenie dzienników aktywności usługi Dynamics 365 z Azure Sentinel ).

### <a name="improved-incident-comments"></a>Ulepszone komentarze do zdarzeń

Analitycy używają komentarzy do zdarzeń, aby współpracować nad zdarzeniami, dokumentować procesy i kroki ręcznie lub w ramach podręcznika. 

Nasze ulepszone środowisko komentowania zdarzeń umożliwia formatowanie komentarzy oraz edytowanie lub usuwanie istniejących komentarzy.

Aby uzyskać więcej informacji, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Dedykowane klastry usługi Log Analytics

Azure Sentinel obsługuje teraz dedykowane klastry usługi Log Analytics jako opcję wdrażania. Zalecamy rozważenie dedykowanego klastra, jeśli:

- **Pozysuj ponad 1 Tb dziennie** do swojego Azure Sentinel roboczego
- **Wiele obszarów Azure Sentinel w** rejestracji platformy Azure

Klastry dedykowane umożliwiają korzystanie z funkcji, takich jak klucze zarządzane przez klienta, skrytka, podwójne szyfrowanie i szybsze zapytania między obszarami roboczymi, gdy w tym samym klastrze znajduje się wiele obszarów roboczych.

Aby uzyskać więcej informacji, zobacz [Azure Monitor klastrów dedykowanych.](../azure-monitor/logs/logs-dedicated-clusters.md)

### <a name="logic-apps-managed-identities"></a>Tożsamości zarządzane aplikacji logiki

Azure Sentinel obsługuje teraz tożsamości zarządzane dla łącznika usługi Azure Sentinel Logic Apps, umożliwiając przyznawanie uprawnień bezpośrednio do określonego podręcznika w celu działania na Azure Sentinel zamiast tworzenia dodatkowych tożsamości.

- **Bez tożsamości zarządzanej** łącznik Logic Apps wymaga oddzielnej tożsamości z Azure Sentinel RBAC w celu uruchomienia na Azure Sentinel. Oddzielną tożsamością może być użytkownik usługi Azure AD lub nazwa główna usługi, taka jak aplikacja zarejestrowana w usłudze Azure AD.

- **Włączenie obsługi tożsamości zarządzanej w aplikacji logiki** rejestruje aplikację logiki w usłudze Azure AD i udostępnia identyfikator obiektu. Użyj identyfikatora obiektu w Azure Sentinel, aby przypisać aplikację logiki z rolą RBAC platformy Azure w twoim Azure Sentinel roboczym. 

Aby uzyskać więcej informacji, zobacz:

- [Uwierzytelnianie za pomocą tożsamości zarządzanej w Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel Logic Apps dokumentacji łącznika](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Ulepszone dostrajanie reguł za pomocą wykresów w wersji zapoznawczej reguł analizy (publiczna wersja zapoznawcza)

Azure Sentinel teraz ułatwia lepsze dostrojenie reguł analizy, co pomaga zwiększyć ich dokładność i zmniejszyć szum.

Po edytowaniu reguły analizy na karcie **Ustawianie logiki reguły** znajdź obszar **Symulacja** wyników po prawej stronie. 

Wybierz **pozycję Testuj z bieżącymi** Azure Sentinel, aby uruchomić symulację ostatnich 50 przebiegów reguły analizy. Na podstawie ocenionych nieprzetworzonych danych zdarzenia generowany jest wykres pokazujący średnią liczbę alertów wygenerowanych przez regułę. 

Aby uzyskać więcej informacji, zobacz [Definiowanie logiki zapytania reguły i konfigurowanie ustawień](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Grudzień 2020 r.

- [80 nowych wbudowanych zapytań wyszukiwania](#80-new-built-in-hunting-queries)
- [Ulepszenia agenta usługi Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nowych wbudowanych zapytań wyszukiwania
 
Azure Sentinel wbudowanych zapytań dotyczących wyszukiwania zagrożeń umożliwia analitykom SOC zmniejszenie luk w bieżącym zasięgu wykrywania i zapalenie nowych potencjalnych klientów.

Ta aktualizacja programu Azure Sentinel obejmuje nowe zapytania wyszukiwania danych, które zapewniają pokrycie w macierzy struktury MITRE ATT&CK:

- **Kolekcja**
- **Polecenie i kontrolka**
- **Dostęp poświadczeń**
- **Odnajdywanie**
- **Wykonanie**
- **Eksfiltracja**
- **Wpływ**
- **Dostęp początkowy**
- **Trwałości**
- **Eskalacja uprawnień**

Dodane zapytania dotyczące wyszukiwania danych mają na celu pomoc w odnalezieniu podejrzanych działań w środowisku. Chociaż mogą one zwracać legalne działania i potencjalnie złośliwe działania, mogą być przydatne w kierowaniu łowami. 

Jeśli po uruchomieniu tych zapytań masz pewność co do wyników, możesz przekonwertować je na reguły analizy lub dodać wyniki wyszukiwania do istniejących lub nowych zdarzeń.

Wszystkie dodane zapytania są dostępne za pośrednictwem strony wyszukiwania Azure Sentinel wyszukiwania. Aby uzyskać więcej informacji, zobacz [Hunt for threats with Azure Sentinel (Wyszukanie](hunting.md)zagrożeń za pomocą Azure Sentinel ).

### <a name="log-analytics-agent-improvements"></a>Ulepszenia agenta usługi Log Analytics

Azure Sentinel korzyści z następujących ulepszeń agenta usługi Log Analytics:

- **Obsługa większej liczby systemów operacyjnych,** w tym CentOS 8, RedHat 8 i SUSE Linux 15.
- **Obsługa języka Python 3** oprócz języka Python 2

Azure Sentinel używa agenta usługi Log Analytics do wysłania zdarzeń do obszaru roboczego, w tym zdarzeń Zabezpieczenia Windows, zdarzeń dziennika systemowego, dzienników CEF i innych.

> [!NOTE]
> Agent usługi Log Analytics jest czasami określany jako agent OMS lub agent usługi Microsoft Monitoring Agent (MMA). 
> 

Aby uzyskać więcej informacji, zobacz [dokumentację usługi Log Analytics](../azure-monitor/agents/log-analytics-agent.md) i informacje o wersji agenta [usługi Log Analytics.](https://github.com/microsoft/OMS-Agent-for-Linux/releases)
## <a name="november-2020"></a>Listopad 2020 r.

- [Monitorowanie kondycji podręczników w Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender (publiczna wersja zapoznawcza)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitorowanie kondycji podręczników w Azure Sentinel

Azure Sentinel są oparte na przepływach pracy wbudowanych w usługę [Azure Log Apps](../logic-apps/index.yml), usługę w chmurze, która ułatwia planowanie, automatyzowanie i organizowanie zadań, procesów biznesowych i przepływów pracy. Podręczniki mogą być wywoływane automatycznie podczas tworzenia zdarzenia lub podczas ujednoliwiania i pracy ze zdarzeniami. 

Aby zapewnić wgląd w kondycję, wydajność i użycie podręczników, [](../azure-monitor/visualize/workbooks-overview.md) dodaliśmy skoroszyt o nazwie Monitorowanie kondycji **podręczników.** 

Użyj **skoroszytu monitorowania kondycji** podręczników, aby monitorować kondycję podręczników lub poszukać anomalii w ilości przebiegów, które zakończyły się powodzeniem lub niepowodzeniem. 

Skoroszyt **monitorowania kondycji podręczników** jest teraz dostępny w galerii szablonów Azure Sentinel Playbook:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Przykładowy skoroszyt monitorowania kondycji podręczników":::

Aby uzyskać więcej informacji, zobacz:

- [Logic Apps dokumentacji](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Dokumentacja usługi Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender (publiczna wersja zapoznawcza)
 
Łącznik Microsoft 365 Defender dla usługi Azure Sentinel umożliwia przesyłanie strumieniowe zaawansowanych dzienników wyszukiwania informacji (typu nieprzetworzonych danych zdarzeń) z usługi Microsoft 365 Defender do Azure Sentinel. 

Dzięki integracji usługi Microsoft Defender dla punktu końcowego [(MDATP)](/windows/security/threat-protection/) z pakietem zabezpieczeń usługi [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) można teraz zbierać zdarzenia zaawansowanego wyszukiwania punktów końcowych usługi Microsoft Defender za pomocą łącznika usługi Microsoft 365 Defender i przesyłać je bezpośrednio do nowych tabel wbudowanych w obszarze roboczym usługi Azure Sentinel. 

Tabele Azure Sentinel są zbudowane na tym samym schemacie, który jest używany w portalu usługi Microsoft 365 Defender, i zapewniają pełny dostęp do pełnego zestawu zaawansowanych dzienników wyszukiwania. 

Aby uzyskać więcej informacji, zobacz [Connect data from Microsoft 365 Defender to Azure Sentinel](connect-microsoft-365-defender.md)(Łączenie danych z usługi Microsoft 365 Defender z Azure Sentinel ).

> [!NOTE]
> Microsoft 365 Defender była wcześniej znana jako Microsoft Threat Protection lub MTP. Usługa Microsoft Defender dla punktu końcowego była wcześniej znana jako Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender lub MDATP.
> 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Informacje Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Uzyskiwanie wglądu w alerty](quickstart-get-visibility.md)
