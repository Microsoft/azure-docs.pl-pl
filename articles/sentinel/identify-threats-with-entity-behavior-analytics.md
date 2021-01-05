---
title: Identyfikowanie zaawansowanych zagrożeń przy użyciu funkcji analizy zachowań użytkowników i jednostek (UEBA) na platformie Azure Microsoft Docs
description: Tworzenie linii bazowych zachowań dla jednostek (użytkowników, nazw hostów, adresów IP) i ich używanie do wykrywania nietypowego zachowania i identyfikowania zaawansowanych zagrożeń trwałych (APT).
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
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: 2326746d274c68225cd4c8569df6a20d6050ec1a
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900896"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identyfikowanie zaawansowanych zagrożeń przy użyciu funkcji analizy zachowań użytkowników i jednostek (UEBA) na platformie Azure — wskaźnik

> [!IMPORTANT]
>
> - Funkcje UEBA i strony jednostki są teraz **ogólnie dostępne** w następujących regionach i obszarach lokalizacje geograficzne platformy Azure:
>    - Stany Zjednoczone Geografia
>    - Region Zachodni Europa
>    - Geografia Australia
>
> - We wszystkich innych lokalizacje geograficzne i regionach te funkcje pozostają na czas w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [**warunkami użytkowania Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Co to jest Analiza zachowań użytkowników i jednostek (UEBA)?

### <a name="the-concept"></a>Pojęcie koncepcji

Identyfikowanie zagrożeń w organizacji i ich potencjalny wpływ — czy zagrożona jednostka lub złośliwy Tester — zawsze był czasochłonny i intensywnie korzystający z pracy. Przechodzenie przez alerty, połączenie kropek i aktywne łowiectwo wszystko Dodaj do ogromnych nakładów czasowych i wysiłku z minimalnymi wynikami, a możliwość wyrafinowanych zagrożeń po prostu uchyla odnajdywanie. Szczególnie zagrożenia nieuchwytnych, takie jak zero dni, celem i zaawansowanym trwałym zagrożeniem, mogą być najbardziej niebezpieczne dla organizacji, co sprawia, że ich wykrywanie jest bardziej krytyczne.

Funkcja UEBA na platformie Azure wskaźnikowa eliminuje drudgery z obciążeń analityków oraz niepewność z ich wysiłków i zapewnia wysoką wierność, z możliwością podejmowania działań, dzięki czemu mogą skupić się na badaniu i korygowaniu.

Ponieważ wskaźnik na platformie Azure zbiera dzienniki i alerty ze wszystkich połączonych źródeł danych, analizuje je i tworzy podstawowe profile zachowań organizacji (użytkowników, hostów, adresów IP, aplikacji itp.) w zakresie grup czasu i elementów równorzędnych. Korzystając z różnych technik i możliwości uczenia maszynowego, wskaźnik kontrolny może następnie identyfikować nietypowe działanie i pomóc w ustaleniu, czy zasób został naruszony. Nie tylko jest to możliwe, ale może również ustalić względną czułość poszczególnych zasobów, zidentyfikować grupy elementów równorzędnych i oszacować potencjalny wpływ danego środka trwałego (jego "serwer"). Dzięki tym informacjom można skutecznie określić priorytety badania i obsługi zdarzeń. 

### <a name="architecture-overview"></a>Omówienie architektury

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architektura analizy zachowań jednostek":::

### <a name="security-driven-analytics"></a>Analiza oparta na zabezpieczeniach

Inspirowany modelem firmy Gartner dla rozwiązań UEBA, wskaźnik na platformie Azure zawiera podejście "zewnętrzne", w oparciu o trzy ramki odniesienia:

- **Przypadki użycia:** Dzięki wykorzystaniu priorytetów dla odpowiednich wektorów ataków i scenariuszy opartych na badaniach bezpieczeństwa, które są wyrównane z MITRE ATT&taktykę, technikami i technikami podrzędnymi, które umieszczają różne jednostki jako ofiary, samodziałania lub punkty obrotu w łańcuchu kasowania; Platforma Azure wskaźnikowego koncentruje się w szczególności na najbardziej cennych dziennikach, które mogą być obsługiwane przez źródło danych.

- **Źródła danych:** Podczas pierwszej i najwyższego poziomu obsługi źródeł danych platformy Azure wskaźnik Thoughtfully na platformie Azure wybiera źródła danych innych firm, aby zapewnić dane zgodne z naszymi scenariuszami zagrożeń.

- **Analiza:** Korzystając z różnych algorytmów uczenia maszynowego (ML), wskaźnik Azure wskazują nietypowe działania i prezentuje dowody jasno i zwięzłe w postaci wzbogacania kontekstowego, niektóre przykłady, które są wyświetlane poniżej.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Analiza zachowań — poza podejściem":::

Wskaźnik na platformie Azure przedstawia artefakty ułatwiające analitykom zabezpieczeń dokładne zrozumienie nietypowych działań w kontekście oraz w porównaniu z profilem linii bazowej użytkownika. Akcje wykonywane przez użytkownika (lub hosta lub adres) są oceniane kontekstowo, gdzie wynik "prawda" wskazuje zidentyfikowaną anomalię:
- między lokalizacjami geograficznymi, urządzeniami i środowiskami.
- w Horizons czasu i częstotliwości (w porównaniu do własnej historii użytkownika).
- w porównaniu z zachowaniem elementów równorzędnych.
- w porównaniu z zachowaniem organizacji.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Kontekst jednostki":::


### <a name="scoring"></a>Scoring (Ocenianie)

Każde działanie jest oceniane z uwzględnieniem "oceny priorytetu badania", które określa prawdopodobieństwo określonego użytkownika wykonującego określone działanie, na podstawie uczenia zachowań użytkownika i ich elementów równorzędnych. Działania zidentyfikowane jako najbardziej nietypowe otrzymują największe wyniki (w skali 0-10).

Zapoznaj się z tematem jak działa Analiza zachowań w [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) .

## <a name="entities-in-azure-sentinel"></a>Jednostki na platformie Azure — wskaźnik

### <a name="entity-identifiers"></a>Identyfikatory jednostek

Gdy alerty są wysyłane do badania wskaźnikowego platformy Azure, obejmują elementy danych identyfikowane przez platformę Azure i klasyfikuje je jako jednostki, takie jak konta użytkowników, hosty, adresy IP i inne. W tej sytuacji ten identyfikator może być wyzwaniem, jeśli alert nie zawiera wystarczających informacji o jednostce.

Na przykład konta użytkowników mogą być identyfikowane w więcej niż jeden sposób: przy użyciu identyfikatora liczbowego (GUID) konta usługi Azure AD lub jego wartości głównej nazwy użytkownika (UPN) lub alternatywnie, przy użyciu kombinacji nazwy użytkownika i nazwy domeny NT. Różne źródła danych mogą identyfikować tego samego użytkownika na różne sposoby. W związku z tym, jeśli jest to możliwe, wskaźnik na platformie Azure będzie scalał te identyfikatory w pojedynczą jednostkę, dzięki czemu może być prawidłowo zidentyfikowany.

Może to być spowodowane tym, że jeden z dostawców zasobów tworzy alert, w którym jednostka nie jest wystarczająco identyfikowana — na przykład nazwę użytkownika bez kontekstu nazwy domeny. W takim przypadku jednostka użytkownika nie może być scalona z innymi wystąpieniami tego samego konta użytkownika, które będą identyfikowane jako osobne jednostki, a te dwie jednostki byłyby osobne, a nie ujednolicone.

Aby zminimalizować ryzyko wystąpienia tego problemu, należy sprawdzić, czy wszyscy dostawcy alertów prawidłowo identyfikują jednostki w wygenerowanej przez nich alertach. Ponadto synchronizowanie jednostek kont użytkowników z Azure Active Directory może utworzyć katalog ujednolicania, który będzie mógł scalać jednostki konta użytkownika.

Następujące typy jednostek są obecnie identyfikowane na platformie Azure — wskaźnik:

- Konto użytkownika (konto)
- Host
- Adres IP (IP)
- Złośliwe oprogramowanie
- Plik
- Proces
- Aplikacja w chmurze (CloudApplication)
- Nazwa domeny (DNS)
- Zasób platformy Azure
- Plik (FileHash)
- Klucz rejestru
- Wartość rejestru
- Grupa zabezpieczeń
- Adres URL
- Urządzenie IoT
- Mailbox
- Klaster poczty
- Wiadomość e-mail
- Przesyłanie poczty

### <a name="entity-pages"></a>Strony jednostki

W przypadku wystąpienia dowolnej jednostki (obecnie ograniczonej do użytkowników i hostów) w wyszukiwaniu, alercie lub zbadaniu można wybrać jednostkę i przetworzyć ją na **stronie jednostki**, a arkusz danych zapełnił przydatne informacje o tej jednostce. Typy informacji, które można znaleźć na tej stronie, obejmują podstawowe fakty dotyczące jednostki, oś czasu istotnych zdarzeń związanych z tą jednostką i szczegółowe informacje o zachowaniu działania jednostki.
 
Strony jednostki składają się z trzech części:
- Panel po lewej stronie zawiera informacje identyfikacyjne jednostki, zebrane ze źródeł danych, takich jak Azure Active Directory, Azure Monitor, Azure Security Center i Microsoft Defender.

- Panel centrum pokazuje graficzną i tekstową oś czasu dla istotnych zdarzeń związanych z jednostką, takich jak alerty, zakładki i działania. Działania są agregacją istotnych zdarzeń z Log Analytics. Zapytania, które wykrywają te działania, są opracowywane przez zespoły badawcze zabezpieczeń firmy Microsoft.

- Panel po prawej stronie przedstawia informacje o zachowaniu działania w jednostce. Te informacje ułatwiają szybkie identyfikowanie anomalii i zagrożeń bezpieczeństwa. Szczegółowe informacje są opracowywane przez zespoły badawcze zabezpieczeń firmy Microsoft i są oparte na modelach wykrywania anomalii.

### <a name="the-timeline"></a>Oś czasu

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Oś czasu stron jednostki":::

Oś czasu jest główną częścią udziału strony jednostki na potrzeby analiz zachowań na platformie Azure. Przedstawia historię zdarzeń związanych z jednostkami, ułatwiając zrozumienie aktywności jednostki w określonym przedziale czasowym.

Możesz wybrać **zakres czasu** spośród kilku opcji predefiniowanych (takich jak *ostatnie 24 godziny*) lub ustawić go na dowolny niestandardowy zakres czasu. Ponadto można ustawić filtry, które ograniczają informacje na osi czasu do określonych typów zdarzeń lub alertów.

Na osi czasu są uwzględniane następujące typy elementów:

- Alerty — wszystkie alerty, w których jednostka jest zdefiniowana jako **zamapowana jednostka**. Należy pamiętać, że jeśli organizacja utworzyła [niestandardowe alerty przy użyciu reguł analizy](./tutorial-detect-threats-custom.md), należy upewnić się, że mapowanie jednostek reguł zostało wykonane prawidłowo.

- Zakładki — wszystkie zakładki, które zawierają konkretną jednostkę widoczną na stronie.

- Działania — agregacja istotnych zdarzeń odnoszących się do jednostki. 
 
### <a name="entity-insights"></a>Informacje o jednostce
 
Usługi Entity Insights to zapytania zdefiniowane przez badaczy zabezpieczeń firmy Microsoft, aby ułatwić analitykom badanie bardziej wydajne i efektywne. Szczegółowe informacje są prezentowane jako część strony jednostki i dostarczają cennych informacji o zabezpieczeniach hostów i użytkowników w postaci danych tabelarycznych i wykresów. Informacje o tym, że nie trzeba przeznaczyć na Log Analytics. Szczegółowe informacje obejmują dane dotyczące logowania, dodawania grup, zdarzeń nietypowych i innych, a także zawierają zaawansowane algorytmy ML do wykrywania nietypowego zachowania. Szczegółowe informacje są oparte na następujących typach danych:
- Dziennik systemu
- SecurityEvent
- Dzienniki inspekcji
- Dzienniki logowania
- Aktywność pakietu Office
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Jak używać stron jednostki

Strony jednostki zostały zaprojektowane jako części wielu scenariuszy użycia i można uzyskać do nich dostęp z zarządzania zdarzeniami, wykresu badawczego, zakładek lub bezpośrednio ze strony wyszukiwania jednostek w obszarze **Analiza zachowań jednostek** w menu głównym wskaźnikiem na platformie Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Przypadki użycia stron jednostki":::


## <a name="data-schema"></a>Schemat danych

### <a name="behavior-analytics-table"></a>Tabela analizy zachowań

| Pole                     | Opis                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | unikatowy numer IDENTYFIKACYJNy dzierżawy                                      |
| SourceRecordId            | unikatowy numer IDENTYFIKACYJNy zdarzenia EBA                                   |
| TimeGenerated             | Sygnatura czasowa wystąpienia działania                              |
| TimeProcessed             | Sygnatura czasowa przetwarzania działania przez aparat EBA            |
| ActivityType              | Kategoria wysokiego poziomu działania                                 |
| ActionType                | znormalizowana nazwa działania                                     |
| Nazwa użytkownika                  | Nazwa użytkownika, który zainicjował działanie                    |
| UserPrincipalName         | Pełna nazwa użytkownika, która zainicjowała działanie               |
| EventSource               | Źródło danych, które dostarczyło oryginalne zdarzenie                        |
| SourceIPAddress           | Adres IP, z którego zainicjowano działanie                        |
| SourceIPLocation          | kraj, z którego zainicjowano działanie, wzbogacony od adresu IP |
| SourceDevice              | Nazwa hosta urządzenia, na którym zainicjowano działanie                  |
| DestinationIPAddress      | Adres IP elementu docelowego działania                            |
| DestinationIPLocation     | kraj docelowy działania, wzbogacony od adresu IP     |
| DestinationDevice         | Nazwa urządzenia docelowego                                           |
| **UsersInsights**         | kontekstowe wzbogacanie powiązanych użytkowników                            |
| **DevicesInsights**       | kontekstowe wzbogacanie powiązanych urządzeń                          |
| **ActivityInsights**      | Analiza kontekstowa działania oparta na naszym profilowania              |
| **InvestigationPriority** | wynik anomalii, między 0-10 (0 = niegroźne, 10 = wysoce anomalia)         |
|

Można zobaczyć pełny zestaw wzbogacania kontekstowego, do których odwołuje się element **UsersInsights**, **DevicesInsights** i **ActivityInsights** w [dokumencie referencyjnym wzbogacania UEBA](ueba-enrichments.md).

### <a name="querying-behavior-analytics-data"></a>Wykonywanie zapytania dotyczącego danych analizy zachowań

Za pomocą [KQL](/azure/data-explorer/kusto/query/)możemy zbadać tabelę analizy behawioralnej.

Na przykład — jeśli chcemy znaleźć wszystkie przypadki użytkownika, który nie mógł zalogować się do zasobu platformy Azure, w którym była pierwszą próbą nawiązania połączenia z danego kraju, a połączenia z tego kraju są nietypowe nawet w przypadku elementów równorzędnych użytkownika, można użyć następującego zapytania:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadane elementów równorzędnych użytkownika — tabela i Notes

Metadane elementów równorzędnych użytkownika udostępniają ważne konteksty wykrywania zagrożeń, w trakcie badania incydentu i w łowiectwie dla potencjalnego zagrożenia. Analitycy zabezpieczeń mogą obserwować normalne działania elementów równorzędnych użytkownika, aby określić, czy działania użytkownika są nietypowe w porównaniu do tych elementów równorzędnych.

Usługa Azure — wskaźnik oblicza i ustala rangę elementów równorzędnych użytkownika, na podstawie przynależności do grupy zabezpieczeń usługi Azure AD użytkownika, listy adresowej i zadanie, a następnie przechowuje elementy równorzędne 1-20 w tabeli **UserPeerAnalytics** . Zrzut ekranu poniżej przedstawia schemat tabeli UserPeerAnalytics i wyświetla górne osiem rang elementów równorzędnych użytkownika Kendall Collins. W przypadku korzystania z platformy Azure wskaźnikowego jest używany algorytm *częstotliwości bezpowrotnego dokumentu* (TF-IDF) do normalizacji ważenia do obliczenia rangi: mniejsza grupa, im wyższa waga. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Zrzut ekranu przedstawiający tabelę metadanych elementów równorzędnych użytkownika":::

Do wizualizacji metadanych elementów równorzędnych użytkownika można użyć [notesu Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) dostępnego w repozytorium Azure wskaźnikowego usługi GitHub. Szczegółowe instrukcje dotyczące korzystania z notesu znajdują się w artykule [Analiza z przewodnikiem — metadane zabezpieczeń użytkownika](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="permission-analytics---table-and-notebook"></a>Analiza uprawnień — tabela i Notes

Analiza uprawnień pomaga określić potencjalny wpływ naruszenia zasobów organizacyjnych przez osobę atakującą. Ten wpływ jest również znany jako "wzmacniający promień zasobu". Analitycy zabezpieczeń mogą używać tych informacji do określania priorytetów kontroli i obsługi zdarzeń.

Wskaźnik oceny platformy Azure określa bezpośrednie i przechodnie prawa dostępu przechowywane przez danego użytkownika do zasobów platformy Azure, oceniając subskrypcje platformy Azure, do których użytkownik może uzyskiwać dostęp bezpośrednio lub za pośrednictwem grup lub jednostek usługi. Te informacje, a także pełna lista przynależności do grupy zabezpieczeń użytkownika usługi Azure AD, są następnie przechowywane w tabeli **UserAccessAnalytics** . Zrzut ekranu poniżej przedstawia przykładowy wiersz w tabeli UserAccessAnalytics, dla którego użytkownik Alexuje Johnsonem. **Jednostka źródłowa** jest kontem użytkownika lub usługi, a **jednostką docelową** jest zasób, do którego jednostka źródłowa ma dostęp. Wartości **poziomu dostępu** i **typu dostępu** są zależne od modelu kontroli dostępu jednostki docelowej. Zobaczysz, że Alex ma dostęp współautora do *dzierżawy* usługi Azure Subscription contoso. Model kontroli dostępu subskrypcji to Azure RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Zrzut ekranu przedstawiający tabelę analizy dostępu użytkowników":::

Aby wizualizować dane analizy uprawnień, można użyć [notesu Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (opisanego powyżej) w repozytorium GitHub na platformie Azure. Szczegółowe instrukcje dotyczące korzystania z notesu znajdują się w artykule [Analiza z przewodnikiem — metadane zabezpieczeń użytkownika](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="hunting-queries-and-exploration-queries"></a>Zapytania polowania i zapytania eksploracji

Wskaźnik "Azure" obejmuje wbudowane zestawy zapytań polowających, zapytania eksploracji i skoroszyt, na podstawie tabeli BehaviorAnalytics. Narzędzia te zawierają wzbogacone dane, które koncentrują się na określonych przypadkach użycia, które wskazują na nietypowe zachowanie. 

Dowiedz się więcej na temat [polowania i grafu badania](./hunting.md) na platformie Azure — wskaźnik.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono informacje o możliwościach analizy zachowań jednostek na platformie Azure. Aby uzyskać praktyczne wskazówki dotyczące implementacji i korzystać z szczegółowych informacji, zobacz następujące artykuły:

- [Włącz analizę zachowań jednostek](./enable-entity-behavior-analytics.md) na platformie Azure.
- [W przypadku zagrożeń bezpieczeństwa](./hunting.md).