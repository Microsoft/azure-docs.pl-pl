---
title: Importowanie analizy zagrożeń do usługi Azure wskaźnikowej | Microsoft Docs
description: Używaj kanałów informacyjnych analizy zagrożeń w usłudze Azure wskaźnikowych, aby analizować dane, wykrywać zagrożenia i generować alerty i zdarzenia. Wizualizuj informacje o analizie zagrożeń przy użyciu skoroszytów.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: e04d7fa1f319ca3969d8acdc0235e2838bb3a88d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997609"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importowanie analizy zagrożeń do usługi Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Wprowadzenie do analizy zagrożeń

Cybernetycznymi Threat Intelligence (CTI) to informacje opisujące znane istniejące lub potencjalne zagrożenia dla systemów i użytkowników. Informacje tego typu zajmują wiele formularzy, od pisanych raportów zawierających szczegółowe informacje o konkretnych aktorach, infrastrukturze i technikach związanych z zagrożeniami, do określonych obserwacji adresów IP, domen i skrótów plików skojarzonych z zagrożeniami cybernetycznymi. CTI są używane przez organizacje w celu zapewnienia zasadniczego kontekstu dla nietypowego działania, dzięki czemu pracownicy działu bezpieczeństwa mogą szybko podejmować działania w celu ochrony swoich osób i zasobów. Elementy CTI mogą pochodzić z wielu miejsc, takich jak strumieniowe źródła danych typu "open source", społeczności udostępniania analizy zagrożeń, źródła analiz komercyjnych i lokalną inteligencję zebraną w toku badań zabezpieczeń w organizacji.

W ramach rozwiązania do zarządzania informacjami o zabezpieczeniach i zdarzeniach (SIEM), takich jak Azure, najbardziej wykorzystywane są wskaźniki zagrożeń, często nazywane wskaźnikami naruszenia lub IoCs. Wskaźniki zagrożeń to dane, które kojarzą takie uwagi, jak adresy URL, skróty plików lub adresy IP ze znanymi działaniami dotyczącymi zagrożeń, takimi jak phishing, botnetami lub złośliwe oprogramowanie. Ta forma analizy zagrożeń jest często nazywana taktyczną inteligencją, ponieważ może być stosowana do produktów i automatyzacji w dużej skali w celu ochrony i wykrywania potencjalnych zagrożeń dla organizacji. Na platformie Azure — Wskaźnik zagrożenia pozwala wykrywać złośliwe działania zaobserwowane w środowisku i zapewniać kontekst dla badaczy zabezpieczeń, aby pomóc w zapewnieniu decyzji dotyczących odpowiedzi.

Analiza zagrożeń (TI) można zintegrować z wskaźnikiem na platformie Azure za pomocą następujących działań:

- Użyj **łączników danych** do różnych platform TI, aby [zaimportować analizę zagrożeń](./connect-threat-intelligence.md) do platformy Azure.
- Wyświetlaj zaimportowaną analizę zagrożeń w **dziennikach** i zarządzaj nią w obszarze nowy obszar **analizy zagrożeń** platformy Azure — wskaźnik.
- Wbudowane szablony reguł **analizy** umożliwiają generowanie alertów zabezpieczeń i zdarzeń przy użyciu zaimportowanej analizy zagrożeń.
- Wizualizuj najważniejsze informacje o analizie zagrożeń na platformie Azure — za pomocą **skoroszytu analizy zagrożeń**.

Analiza zagrożeń zapewnia również przydatny kontekst w innych środowiskach usługi Azure wskaźnikowych, takich jak **łowiectwo** i **notesy**, a chociaż nie zostały omówione w tym artykule, te środowiska są rozwiązywane w [tym wspaniałym wpisie w blogu przez Ian Hellen w notesach Jupyter w wskaźniku](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), który obejmuje korzystanie z CTI w notesach.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Łączniki danych wskaźnikowych platformy Azure na potrzeby analizy zagrożeń

Podobnie jak w przypadku wszystkich innych danych zdarzeń w usłudze Azure — wskaźnik zagrożeń są importowane przy użyciu łączników danych. Dostępne są dwa łączniki danych na platformie Azure, które są przeznaczone dla wskaźników zagrożeń, **analizy zagrożeń — TAXII** i **analizy zagrożeń**. Możesz użyć samego łącznika danych lub obu łączników jednocześnie, w zależności od tego, gdzie są używane wskaźniki zagrożeń w organizacji. Porozmawiamy o każdym z łączników danych.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Dodawanie wskaźników zagrożeń do wskaźnika platformy Azure przy użyciu łącznika danych platform analizy zagrożeń

Wiele organizacji używa rozwiązań platformy analizy zagrożeń (TIP) do agregowania kanałów informacyjnych wskaźnika zagrożeń z różnych źródeł, do nadzoru nad danymi na platformie, a następnie do wybierania wskaźników zagrożeń, które mają być stosowane do różnych rozwiązań zabezpieczeń, takich jak urządzenia sieciowe, zaawansowane rozwiązania ochrony przed zagrożeniami lub rozwiązań Siem, takie jak platformy Azure. Jeśli Twoja organizacja korzysta z zintegrowanego rozwiązania TIP, takiego jak MISP, anomali ThreatStream, ThreatConnect lub Palo Alto Networks "MineMeld, **Łącznik danych platformy analizy zagrożeń** umożliwia użycie porady do importowania wskaźników zagrożeń do usługi Azure wskaźnikowej. Ponieważ łącznik współpracuje z [interfejsem API usługi Microsoft Graph Security tiIndicators](https://docs.microsoft.com/graph/api/resources/tiindicator) w ten sposób, łącznik może być również używany przez dowolną platformę do analizy zagrożeń, aby korzystać z interfejsu API tiIndicators w celu wysyłania wskaźników do usługi Azure wskaźnikowego (oraz do innych rozwiązań zabezpieczeń firmy Microsoft, takich jak usługa Defender ATP).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Ścieżka importu analizy zagrożeń":::

Wykonaj następujące kroki, aby zaimportować wskaźniki zagrożeń do platformy Azure wskaźnikowego z zintegrowanej porady lub niestandardowego rozwiązania do analizy zagrożeń:

1. Uzyskaj identyfikator aplikacji i klucz tajny klienta z Azure Active Directory

1. Wprowadź te informacje w rozwiązaniu TIP lub aplikacji niestandardowej

1. Włącz Łącznik danych platformy analizy zagrożeń na platformie Azure — wskaźnik

Poniżej przedstawiono szczegółowy opis każdego z tych kroków.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Uzyskaj identyfikator aplikacji i klucz tajny klienta z Azure Active Directory

Niezależnie od tego, czy pracujesz z PORADą, czy z niestandardowym rozwiązaniem, interfejs API tiIndicators wymaga pewnych podstawowych informacji do łączenia i wysyłania wskaźników zagrożeń. Trzy informacje, które należy uzyskać:
- Identyfikator aplikacji (klienta)
- Identyfikator katalogu (dzierżawcy)
- Klucz tajny klienta

Te informacje zawsze pochodzą z Azure Active Directory przez proces o nazwie **Rejestracja aplikacji** , który obejmuje następujące trzy kroki:
- Rejestrowanie aplikacji w usłudze Azure Active Directory
- Określ uprawnienia wymagane przez aplikację do nawiązania Microsoft Graph połączenia z interfejsem API tiIndicators i wysyłaniem wskaźników zagrożeń
- Uzyskaj zgodę od organizacji, aby przyznać te uprawnienia do tej aplikacji.  

**Rejestrowanie aplikacji przy użyciu Azure Active Directory**

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure Active Directory** .

1. Z menu wybierz pozycję **rejestracje aplikacji** , a następnie wybierz pozycję **Nowa rejestracja**.

1. Wybierz nazwę rejestracji aplikacji, wybierz przycisk radiowy **pojedynczej dzierżawy** i wybierz pozycję **zarejestruj**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Ścieżka importu analizy zagrożeń":::

1. Na wyświetlonym ekranie Skopiuj wartości identyfikatora **aplikacji (klienta)** i **identyfikatora katalogu (dzierżawy)** . Poniżej znajdują się dwie pierwsze informacje potrzebne do skonfigurowania wskazówki lub niestandardowego rozwiązania do wysyłania wskaźników zagrożeń do platformy Azure.

**Określ uprawnienia wymagane przez aplikację**

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure Active Directory** .

1. Wybierz pozycję **rejestracje aplikacji** z menu i wybierz nowo zarejestrowanej aplikacji.

1. Wybierz z menu pozycję **uprawnienia interfejsu API** , a następnie kliknij przycisk **Dodaj uprawnienie** .

1. Na stronie **Wybierz interfejs API** wybierz pozycję **Microsoft Graph** do wybrania z listy uprawnień Microsoft Graph.

1. Po wyświetleniu pytania **, jakiego typu uprawnienia wymagają aplikacja?** wybierz **uprawnienia aplikacji**. Jest to typ uprawnień używanych przez aplikacje uwierzytelniane przy użyciu identyfikatora aplikacji i wpisów tajnych aplikacji (klucze interfejsu API).

1. Wybierz pozycję **ThreatIndicators. ReadWrite. OwnedBy** i wybierz pozycję **Dodaj uprawnienia** , aby dodać to uprawnienie do listy uprawnień aplikacji.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Ścieżka importu analizy zagrożeń":::

**Uzyskaj zgodę od organizacji, aby przyznać te uprawnienia**

1. Aby udzielić zgody, musisz mieć Azure Active Directory administratora globalnego, aby wybrać przycisk **Udziel zgody administratora dla dzierżawy** na stronie uprawnień interfejsu API aplikacji. Jeśli nie masz roli administratora globalnego na Twoim koncie, ten przycisk nie będzie dostępny i musisz polecić administratora globalnego z Twojej organizacji, aby wykonać ten krok.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Ścieżka importu analizy zagrożeń":::

1. Po udzieleniu zgody na swoją aplikację w obszarze **stan**powinien zostać wyświetlony zielony znacznik wyboru.
 
Teraz, gdy Twoja aplikacja została zarejestrowana i przyznano uprawnienia, możesz uzyskać ostatnią rzecz swojej listy — klucz tajny klienta dla aplikacji.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure Active Directory** .

1. Wybierz pozycję **rejestracje aplikacji** z menu i wybierz nowo zarejestrowanej aplikacji.

1. Wybierz pozycję **certyfikaty & wpisy tajne** z menu, a następnie kliknij przycisk **Nowy klient** , aby uzyskać klucz tajny (klucz interfejsu API) dla aplikacji.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Ścieżka importu analizy zagrożeń":::

1. Kliknij przycisk **Dodaj** i **Pamiętaj, aby skopiować klucz tajny klienta**, ponieważ nie możesz ponownie pobrać tego klucza tajnego, Jeśli opuścisz Tę stronę. Ta wartość będzie potrzebna podczas konfigurowania wskazówki lub niestandardowego rozwiązania.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Wprowadź te informacje w rozwiązaniu TIP lub aplikacji niestandardowej

Masz teraz wszystkie trzy informacje potrzebne do skonfigurowania wskazówki lub niestandardowego rozwiązania do wysyłania wskaźników zagrożeń do usługi Azure wskaźnikowej. 
- Identyfikator aplikacji (klienta)
- Identyfikator katalogu (dzierżawcy)
- Klucz tajny klienta

Wprowadź te wartości w konfiguracji zintegrowanej porady lub niestandardowego rozwiązania, gdy jest to wymagane, i wskaźniki zagrożeń będą wysyłane za pośrednictwem interfejsu API Microsoft Graph tiIndicators, który jest przeznaczony dla platformy Azure.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Włącz Łącznik danych platformy analizy zagrożeń na platformie Azure — wskaźnik

Ostatnim krokiem w procesie integracji jest włączenie łącznika danych **platformy analizy zagrożeń** na platformie Azure. Jest to krok polegający na zaimportowaniu wskaźników zagrożeń wysyłanych z porady lub niestandardowego rozwiązania za pośrednictwem interfejsu API Microsoft Graph tiIndicators do platformy Azure. Te wskaźniki będą dostępne dla wszystkich obszarów roboczych usługi Azure wskaźnikowych dla Twojej organizacji. Wykonaj następujące kroki, aby włączyć Łącznik danych platformy analizy zagrożeń dla każdego obszaru roboczego:

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego chcesz zaimportować wskaźniki zagrożeń wysyłane z Twojej porady lub niestandardowego rozwiązania.

1. Wybierz z menu pozycję **Łączniki danych** , wybierz pozycję **platformy analizy zagrożeń** z galerii łączników, a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. Po zakończeniu rejestracji aplikacji i skonfigurowaniu wskazówki lub niestandardowego rozwiązania do wysyłania wskaźników zagrożeń Jedynym krokiem jest kliknięcie przycisku **Połącz** .

W ciągu kilku minut wskaźniki zagrożeń powinny zacząć przepływać do tego obszaru roboczego wskaźnikowego platformy Azure.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Dodawanie wskaźników zagrożeń do wskaźnika platformy Azure przy użyciu łącznika danych analizy zagrożeń — TAXII

Najbardziej powszechnie przyjętym standardem branżowym na potrzeby przesyłania analizy zagrożeń jest [kombinacja formatu danych Stix i protokołu TAXII](https://oasis-open.github.io/cti-documentation/). Jeśli Twoja organizacja uzyskuje wskaźniki zagrożeń z rozwiązań, które obsługują bieżącą wersję STIX/TAXII (2,0 lub 2,1), możesz użyć łącznika danych **analizy zagrożeń-TAXII** , aby przenieść wskaźniki zagrożeń na platformę Azure. Łącznik Threat Intelligence-TAXII Data Connector umożliwia wbudowanemu klientowi TAXII na platformie Azure wskaźnik do importowania analizy zagrożeń z serwerów TAXII 2. x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Ścieżka importu analizy zagrożeń":::
 
Wykonaj następujące kroki, aby zaimportować STIX sformatowane wskaźniki zagrożeń do wskaźnika platformy Azure z serwera TAXII:

1. Uzyskanie identyfikatora głównego i kolekcji interfejsu API serwera TAXII

1. Włącz łącznik TAXII Data Intelligence (analiza zagrożeń) na platformie Azure — wskaźnik

Teraz przyjrzyjmy się szczegółowym poszczególnym czynnościom.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Uzyskanie identyfikatora głównego i kolekcji interfejsu API serwera TAXII

Serwery TAXII 2. x anonsują katalogi główne interfejsów API, które są adresami URL, które obsługują kolekcje dla analizy zagrożeń. Najczęściej dostęp do katalogu głównego interfejsu API można uzyskać za pośrednictwem strony z dokumentacją dostawcy analizy zagrożeń hostującym serwer TAXII. Czasami jednak jedyne anonsowane informacje są adresami URL znanymi jako punkt końcowy odnajdywania. W takim przypadku można łatwo znaleźć katalog główny interfejsu API za pomocą punktu końcowego odnajdywania. Jeśli znasz już identyfikatory katalogów głównych i kolekcji interfejsu API serwera TAXII, z którymi chcesz współpracować, możesz przejść do następnej sekcji, **włączając Łącznik danych TAXII analizy zagrożeń na platformie Azure**.

Spójrzmy na rzeczywisty przykład użycia prostego narzędzia wiersza polecenia o nazwie [zwinięcie](https://en.wikipedia.org/wiki/CURL), które jest dostępne w systemie Windows i większości dystrybucji systemu Linux, aby odnaleźć katalog główny interfejsu API i przeglądać kolekcje serwera TAXII, rozpoczynając tylko od punktu końcowego odnajdywania. W tym przykładzie użyjemy punktu końcowego odnajdywania serwera [anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2,0.

1.  W przeglądarce przejdź do [punktu końcowego odnajdywania serwera THREATSTREAM TAXII 2,0](https://limo.anomali.com/taxii) , aby pobrać Katalog główny interfejsu API. Uwierzytelnianie przy użyciu użytkownika i hasła `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Użyj narzędzia zwinięcie i katalogu głównego interfejsu API ( https://limo.anomali.com/api/v1/taxii2/feeds/) z poprzedniego kroku, aby przejrzeć listę identyfikatorów kolekcji hostowanych w katalogu głównym interfejsu API

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Masz teraz wszystkie informacje potrzebne do nawiązania połączenia z platformą Azure wskaźnikowego do co najmniej jednej kolekcji serwerów TAXII dostarczonych przez anomali limo.

| **Katalog główny interfejsu API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Identyfikator kolekcji |
| ------------------------------------------------------------ | ------------: |
| **Zbiornik phishingu**                                               | 107           |
| **Abuse.ch adresy IP oprogramowania wymuszającego okup**                                  | 135           |
| **Abuse.ch domeny oprogramowania wymuszającego okup**                              | 136           |
| **DShield skanowania adresów IP**                                     | 150           |
| **Lista domen złośliwego oprogramowania — Hotlist**                            | 200           |
| **Blutmagie TOR — węzły**                                      | 209           |
| **Nowe zagrożenia C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Nowe zagrożenia — naruszone**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Włącz łącznik TAXII Data Intelligence (analiza zagrożeń) na platformie Azure — wskaźnik

Aby zaimportować wskaźniki zagrożeń do wskaźnika wskaźnikowego platformy Azure z serwera TAXII, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego chcesz zaimportować wskaźniki zagrożeń z serwera TAXII.

1. Wybierz z menu pozycję **Łączniki danych** , wybierz pozycję **Analiza zagrożeń — TAXII** z galerii łączników, a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. Wpisz **nazwę** tej kolekcji serwera TAXII, **główny adres URL interfejsu API**, **identyfikator kolekcji**, **nazwę użytkownika** (jeśli jest to wymagane) i **hasło** (jeśli jest wymagane), a następnie kliknij przycisk **Dodaj** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Ścieżka importu analizy zagrożeń":::
 
Powinno zostać wyświetlone potwierdzenie, że połączenie z serwerem TAXII zostało pomyślnie ustanowione i można powtórzyć krok (4) powyżej tyle razy, ile jest to konieczne, aby połączyć się z wieloma kolekcjami z tego samego lub różnych serwerów TAXII.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Wyświetlanie wskaźników zagrożeń na platformie Azure — Wskaźnik zagrożenia

Po pomyślnym zaimportowaniu wskaźników zagrożeń do funkcji wskaźnikowej platformy Azure za pomocą **platform analizy zagrożeń** i/lub łącznika danych **analizy zagrożeń TAXII** można je wyświetlić w tabeli **ThreatIntelligenceIndicator** w **dziennikach** , w których są przechowywane wszystkie dane zdarzeń wskaźnika platformy Azure. Ta tabela jest podstawą dla zapytań wykonywanych przez inne funkcje wskaźnikowe platformy Azure, takich jak analiza i skoroszyt. Poniżej przedstawiono sposób znajdowania i wyświetlania wskaźników zagrożeń w tabeli **ThreatIntelligenceIndicator** .

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego zaimportowano wskaźniki zagrożeń przy użyciu dowolnego łącznika danych analizy zagrożeń.

1. Wybierz pozycję **dzienniki** z sekcji **Ogólne** w menu wskaźnikowym platformy Azure.

1. Tabela **ThreatIntelligenceIndicator** znajduje się w grupie **wskaźnikowej platformy Azure** .

1. Wybierz ikonę **Podgląd danych** (oczami) obok nazwy tabeli i wybierz przycisk **Zobacz w edytorze zapytań** , aby wykonać zapytanie, w którym będą wyświetlane rekordy z tej tabeli.

Wyniki powinny wyglądać podobnie do wskaźnika przykładowego zagrożenia pokazanego poniżej:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Ścieżka importu analizy zagrożeń":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Zarządzaj wskaźnikami zagrożeń w nowym obszarze analizy zagrożeń platformy Azure — wskaźnikiem

Dzięki nowemu obszarowi **analizy zagrożeń** dostępnym w menu wskaźnikowym platformy Azure można również wyświetlać, sortować, filtrować i przeszukiwać zaimportowane wskaźniki zagrożeń bez konieczności pisania zapytania **dzienników** . Ten nowy obszar umożliwia również tworzenie wskaźników zagrożeń bezpośrednio w interfejsie badania platformy Azure, a także wykonywanie typowych zadań administracyjnych analizy zagrożeń, takich jak tagowanie wskaźników i tworzenie nowych wskaźników związanych z badaniem zabezpieczeń.
Przyjrzyjmy się dwóm z najczęstszych zadań, tworząc nowe wskaźniki zagrożeń i wskaźniki tagowania do łatwego grupowania i odniesienia.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego zaimportowano wskaźniki zagrożeń przy użyciu dowolnego łącznika danych analizy zagrożeń.

1. Wybierz pozycję **Analiza zagrożeń** z sekcji Zarządzanie zagrożeniami w menu wskaźnikowym platformy Azure.

1. Wybierz przycisk **Dodaj nowy** w górnym menu strony.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Ścieżka importu analizy zagrożeń" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Wybierz typ wskaźnika, a następnie Wypełnij wymagane pola oznaczone czerwoną gwiazdką (*) w panelu **Nowy wskaźnik** .

1. Wybierz przycisk **Zastosuj**. Wskaźnik zostanie dodany do siatki wskaźników i jest również wysyłany do tabeli ThreatIntelligenceIndicator w **dziennikach**.

Tagowanie wskaźników zagrożeń jest łatwym sposobem grupowania ich ze sobą, aby ułatwić ich znalezienie. Zazwyczaj można zastosować tag do wskaźników związanych z konkretnym zdarzeniem lub do wskaźników reprezentujących zagrożenia od konkretnego znanego aktora lub dobrze znanej kampanii atakującej. Możesz oznakować wskaźniki zagrożeń indywidualnie lub wskaźniki wielokrotnego wyboru i oznaczyć je wszystkie jednocześnie. Pokazano poniżej przykład tagowania wielu wskaźników z IDENTYFIKATORem zdarzenia. Ze względu na to, że znakowanie jest bezpłatne, zaleca się utworzenie standardowych konwencji nazewnictwa dla tagów wskaźnika zagrożeń. Do każdego wskaźnika można zastosować wiele tagów.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Ścieżka importu analizy zagrożeń" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analiza sprawia, że wskaźniki zagrożeń umożliwiają wykrywanie potencjalnych zagrożeń

Twoje wskaźniki zagrożeń są przekazywane do wskaźnika wskaźnikowego platformy Azure; zobaczysz, jak wyświetlać i zarządzać nimi. teraz Zobacz, co można zrobić. Najważniejszym przypadkiem użycia dla wskaźników zagrożeń w rozwiązaniach SIEM, takich jak platforma Azure, to zasady analizy.  Te reguły oparte na wskaźnikach porównują zdarzenia pierwotne ze źródeł danych ze wskaźnikami zagrożeń w celu wykrywania zagrożeń bezpieczeństwa w organizacji. W **analizie**wskaźnikowej platformy Azure można tworzyć reguły analityczne, które są uruchamiane zgodnie z harmonogramem i generować alerty zabezpieczeń. Reguły są określane przez zapytania, a także konfiguracje, które określają częstotliwość uruchamiania reguły, jakiego rodzaju wyniki zapytania powinny generować alerty zabezpieczeń i wszystkie automatyczne odpowiedzi wyzwalane podczas generowania alertów.

Mimo że zawsze możesz tworzyć nowe reguły analizy od podstaw, wskaźnik na platformie Azure obejmuje zestaw wbudowanych szablonów reguł utworzonych przez inżynierów zabezpieczeń firmy Microsoft, których można użyć jako-lub zmodyfikować, aby zaspokoić Twoje potrzeby. Można łatwo identyfikować szablony reguł korzystające ze wskaźników zagrożeń, ponieważ są one zatytułowane zaczynające się od "**TI map**...". Wszystkie te szablony reguł działają podobnie, a jedyna różnica polega na tym, że używany jest typ wskaźników zagrożeń (domena, adres e-mail, skrót pliku, adres IP lub adres URL) i typ zdarzenia, względem którego ma być zgodny. Każdy szablon zawiera listę wymaganych źródeł danych, które są potrzebne do działania reguły, aby można było szybko zobaczyć, jeśli masz wymagane zdarzenia już zaimportowane na platformie Azure.

Przyjrzyjmy się jednemu z tych szablonów reguł i dowiesz się, jak włączyć i skonfigurować regułę do generowania alertów zabezpieczeń przy użyciu wskaźników zagrożeń zaimportowanych do platformy Azure. W tym przykładzie użyjemy szablonu reguły o nazwie " **Mapowanie TI" jednostki IP na platformie Azure**. Ta reguła będzie pasować do dowolnego wskaźnika zagrożeń typu adresu IP ze wszystkimi zdarzeniami aktywności platformy Azure. Po znalezieniu dopasowania zostanie wygenerowany **alert** , a także odpowiednie **zdarzenie** dotyczące badania przez zespół operacji zabezpieczających. Ta reguła analizy będzie działać pomyślnie tylko w przypadku włączenia jednego lub obu łączników danych **analizy zagrożeń** (w celu zaimportowania wskaźników zagrożeń) oraz łącznika danych **aktywności platformy Azure** (w celu zaimportowania zdarzeń na poziomie subskrypcji platformy Azure).

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego zaimportowano wskaźniki zagrożeń przy użyciu łączników danych **analizy zagrożeń** i danych aktywności platformy Azure przy użyciu łącznika danych **działania platformy Azure** .

1. Wybierz pozycję **Analiza** w sekcji **Konfiguracja** menu wskaźnikowego platformy Azure.

1. Wybierz kartę **Szablony reguł** , aby wyświetlić listę dostępnych szablonów reguł analizy.

1. Przejdź do reguły zatytułowanej **Mapowanie TI jednostki IP do platformy Azure** i upewnij się, że zostały połączone wszystkie wymagane źródła danych, jak pokazano poniżej.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Ścieżka importu analizy zagrożeń":::

1. Wybierz tę regułę, a następnie wybierz przycisk **Utwórz regułę** . Spowoduje to otwarcie Kreatora w celu skonfigurowania reguły. Wypełnij ustawienia tutaj i wybierz przycisk **Dalej: Ustaw logikę reguł >** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Ścieżka importu analizy zagrożeń":::

1. Część logiki reguły w Kreatorze zawiera:
    - Zapytanie, które będzie używane w regule.

    - Mapowania jednostek, które informują platformę Azure, jak rozpoznawać jednostki, takie jak konta, adresy IP i adresy URL, dzięki czemu **zdarzenia** i **badania** opisują sposób pracy z danymi w alertach zabezpieczeń wygenerowanych przez tę regułę.

    - Harmonogram uruchamiania tej reguły.

    - Liczba wyników zapytania wymagana przed wygenerowaniem alertu zabezpieczeń.

    Domyślne ustawienia szablonu są następujące:
    - Uruchom raz na godzinę.

    - Dopasuj wszystkie wskaźniki zagrożeń adresów IP z tabeli **ThreatIntelligenceIndicator** z dowolnym adresem IP znalezionym w ciągu ostatniej godziny zdarzenia z tabeli **usługi Azure** .

    - Generuj alert zabezpieczeń, jeśli wyniki zapytania są większe niż zero, co oznacza, że zostaną znalezione dopasowań.

Możesz pozostawić ustawienia domyślne lub zmienić dowolne z nich w celu spełnienia wymagań. Gdy skończysz, wybierz przycisk **Dalej: automatyczna odpowiedź >**

1. Ten krok kreatora umożliwia skonfigurowanie dowolnej automatyzacji, która ma być wyzwalana po wygenerowaniu alertu zabezpieczeń na podstawie tej reguły analizy. Automatyzacja na platformie Azure — Wskaźnikowanie odbywa się przy użyciu **elementy PlayBook**, obsługiwanego przez Azure Logic Apps. Aby dowiedzieć się więcej, zobacz ten [Samouczek: Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](./tutorial-respond-threats-playbook.md). W tym przykładzie po prostu wybierzemy przycisk **Dalej: przejrzyj >** , aby kontynuować.

1. Ten ostatni krok sprawdza poprawność ustawień w regule. Gdy wszystko będzie gotowe do włączenia reguły, wybierz przycisk **Utwórz** , a wszystko jest gotowe.

Teraz, po włączeniu reguły analizy, można znaleźć regułę włączenia na karcie **aktywne reguły** w sekcji **Analiza** w obszarze wskaźnik platformy Azure. Można edytować, włączać, wyłączać, powielać lub usuwać aktywną regułę z tego miejsca. Nowa reguła zostanie uruchomiona natychmiast po aktywacji, a następnie zostanie uruchomiona zgodnie ze zdefiniowanym harmonogramem.

Zgodnie z ustawieniami domyślnymi, za każdym razem, gdy reguła jest uruchamiana według harmonogramu, Znalezione wyniki spowodują wygenerowanie alertu zabezpieczeń. Alerty zabezpieczeń w usłudze Azure — wskaźnik kontrolny można wyświetlić w sekcji **dzienniki** na platformie Azure wskaźnikowej w tabeli **SecurityAlert** w grupie **wskaźnikowej platformy Azure** .

W przypadku platformy Azure — alerty wygenerowane na podstawie reguł analizy również generują zdarzenia zabezpieczeń, które można znaleźć w **zdarzeniach** w obszarze **Zarządzanie zagrożeniami** w menu wskaźnikowym platformy Azure. Zdarzenia to elementy, które będą Klasyfikacja przez zespoły operacji zabezpieczeń i zbadają, aby określić odpowiednie działania odpowiedzi. Szczegółowe informacje można znaleźć w tym [samouczku: Zbadaj zdarzenia za pomocą usługi Azure wskaźnikowej](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Skoroszyty zawierają szczegółowe informacje o analizie zagrożeń

Na koniec możesz użyć skoroszytu wskaźnikowego platformy Azure do wizualizacji najważniejszych informacji o analizie zagrożeń w usłudze Azure wskaźnikowej i można łatwo dostosować skoroszyty zgodnie z potrzebami biznesowymi.
Zapoznaj się z tematem jak znaleźć skoroszyt analizy zagrożeń dostarczony na platformie Azure wskaźnikowej. Ponadto pokażemy, jak wprowadzać zmiany w skoroszycie, aby je dostosować.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do usługi **Azure wskaźnikowej** .

1. Wybierz **obszar roboczy** , do którego zaimportowano wskaźniki zagrożeń przy użyciu dowolnego łącznika danych analizy zagrożeń.

1. Wybierz pozycję **skoroszyty** w sekcji **Zarządzanie zagrożeniami** w menu wskaźnikowym platformy Azure.

1. Przejdź do skoroszytu zatytułowanego **Analiza zagrożeń** i sprawdź, czy w tabeli **ThreatIntelligenceIndicator** znajdują się dane, jak pokazano poniżej.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Ścieżka importu analizy zagrożeń":::
 
1. Wybierz przycisk **Zapisz** i wybierz lokalizację platformy Azure, w której ma zostać zapisany skoroszyt. Ten krok jest wymagany, jeśli zamierzasz zmodyfikować skoroszyt w dowolny sposób i zapisać zmiany.

1. Teraz wybierz przycisk **Wyświetl zapisany skoroszyt** , aby otworzyć skoroszyt do wyświetlania i edytowania.

1. Powinny teraz być widoczne domyślne wykresy dostarczone przez szablon. Teraz Wprowadźmy zmiany w jednym z wykresów. Wybierz przycisk **Edytuj** w górnej części strony, aby przejść do trybu edycji skoroszytu.

1. Dodajmy nowy wykres wskaźników zagrożeń według typu zagrożenia. Przewiń w dół strony i wybierz polecenie Dodaj zapytanie.

1. Dodaj następujący tekst do pola tekstowego **kwerendy dziennika obszaru roboczego log Analytics** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Na liście rozwijanej **Wizualizacja** wybierz pozycję **Wykres słupkowy**.

1. Wybierz przycisk **gotowe do edycji** . Utworzono nowy wykres dla skoroszytu.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Ścieżka importu analizy zagrożeń":::

Skoroszyty zapewniają zaawansowane interaktywne pulpity nawigacyjne, które zapewniają wgląd we wszystkie aspekty platformy Azure. Istnieje cała partia, którą można wykonać ze skoroszytami, a w przypadku, gdy udostępnione szablony są doskonałym punktem wyjścia, prawdopodobnie zechcesz szczegółowe w programie i dostosować te szablony lub utworzyć nowe pulpity nawigacyjne łączące wiele różnych źródeł danych, dzięki czemu można wizualizować dane na różne sposoby. Ze względu na to, że skoroszyty na platformie Azure są oparte na Azure Monitor skoroszytach, dostępna jest już Obszerna dokumentacja i wiele innych szablonów. W tym artykule zawarto informacje na temat [tworzenia interaktywnych raportów przy użyciu skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md). 

Istnieje również obszerna społeczność [Azure monitor skoroszytów w usłudze GitHub, w](https://github.com/microsoft/Application-Insights-Workbooks) której można pobrać dodatkowe szablony i współtworzyć własne szablony.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono informacje o możliwościach analizy zagrożeń w usłudze Azure wskaźnikowych i w nowym bloku analizy zagrożeń. Aby uzyskać praktyczne wskazówki dotyczące korzystania z funkcji analizy zagrożeń na platformie Azure, zobacz następujące artykuły:

- [Podłącz dane analizy zagrożeń](./connect-threat-intelligence.md) do usługi Azure wskaźnikowej.
- Twórz [wbudowane](./tutorial-detect-threats-built-in.md) lub [niestandardowe](./tutorial-detect-threats-custom.md) alerty i [Badaj](./tutorial-investigate-cases.md) zdarzenia na platformie Azure — wskaźnik.