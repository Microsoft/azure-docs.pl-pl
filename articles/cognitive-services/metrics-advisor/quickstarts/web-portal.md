---
title: 'Szybki start: Doradca w zakresie metryk portal internetowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpocząć korzystanie z portalu Doradca w zakresie metryk sieci Web.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531803"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Szybki start: monitorowanie pierwszej metryki przy użyciu portalu internetowego

Podczas aprowizowania Doradca w zakresie metryk można używać interfejsów API i internetowego obszaru roboczego do pracy z usługą. Internetowy obszar roboczy może służyć jako prosty sposób na szybkie rozpoczynanie pracy z usługą. Udostępnia również wizualny sposób konfigurowania ustawień, dostosowywania modelu i przeprowadzania analizy głównej przyczyny. 

* Dołączanie danych metryk
* Wyświetlanie metryk i wizualizacji
* Dostrajanie konfiguracji wykrywania
* Eksplorowanie szczegółowych informacji diagnostycznych
* Tworzenie i subskrybowanie alertów dotyczących anomalii

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Doradca w zakresie metryk zasobów Doradca w zakresie metryk w witrynie Azure Portal, aby <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" "  target="_blank"> </a> wdrożyć Doradca w zakresie metryk zasobów.  

    
> [!TIP]
> * Wdrożenie zasobu aplikacji może Doradca w zakresie metryk 10–30 minut. Po **pomyślnym wdrożeniu kliknij** pozycję Przejdź do zasobu.
> * Jeśli chcesz używać interfejsu API REST do interakcji z usługą, potrzebujesz klucza i punktu końcowego z zasobu, który utworzysz. Można je znaleźć na karcie  **Klucze i punkty końcowe** we utworzonym zasobie.

W tym dokumencie użyto SQL Database jako przykładu tworzenia pierwszego monitora.

## <a name="sign-in-to-your-workspace"></a>Logowanie do obszaru roboczego

Po utworzeniu zasobu zaloguj się do witryny [Doradca w zakresie metryk Portal.](https://go.microsoft.com/fwlink/?linkid=2143774) Wybierz obszar roboczy, aby rozpocząć monitorowanie metryk. 
 
Obecnie można utworzyć jeden zasób Doradca w zakresie metryk w każdym dostępnym regionie. Obszary robocze można przełączać w Doradca w zakresie metryk portalu w dowolnym momencie.


## <a name="onboard-time-series-data"></a>Dołączanie danych szeregów czasowych

Doradca w zakresie metryk łączniki dla różnych źródeł danych, takich jak SQL Database, Azure Data Explorer i Azure Table Storage. Kroki łączenia danych są podobne dla różnych łączników, chociaż niektóre parametry konfiguracji mogą się różnić. Zobacz [Connect your data from different sources (Łączenie](../data-feeds-from-different-sources.md) danych z różnych źródeł), aby uzyskać informacje o wymaganych parametrach dla określonych źródeł danych.

W tym przewodniku Szybki start SQL Database przykład. Możesz również pozysać własne dane, wykonaj te same kroki.

Aby rozpocząć, zaloguj się do swojego Doradca w zakresie metryk roboczego przy użyciu konta usługi Active Directory. Na stronie docelowej wybierz właśnie **utworzony**  **katalog,** subskrypcję i obszar roboczy, a następnie kliknij pozycję **Wprowadzenie.** Po stronie głównej ładowania obciążenia wybierz pozycję **Dodaj źródło danych** z menu po lewej stronie.

### <a name="data-schema-requirements-and-configuration"></a>Wymagania i konfiguracja schematu danych

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfigurowanie ustawień połączenia

> [!TIP]
> Zobacz, [jak dodać źródła danych,](../how-tos/onboard-your-data.md) aby uzyskać szczegółowe informacje na temat dostępnych parametrów.

Dodaj źródło danych, łącząc się ze źródłem danych szeregów czasu. Zacznij od wybrania następujących parametrów:

* **Typ źródła:** typ źródła danych, w którym są przechowywane dane szeregów czasu.
* **Poziom szczegółowości:** interwał między kolejnymi punktami danych w danych szeregów czasowych, na przykład Co rok, Co miesiąc, Codziennie. Dostosowywanie o najniższym interwale obsługuje wartość 60 sekund.
* **Pozysuj** dane od (UTC): godzina rozpoczęcia pierwszego znacznika czasu, który ma zostać pozystywowany. 


Następnie określ parametry **połączenia** przy użyciu poświadczeń dla źródła danych i niestandardowego **zapytania**. Zapytanie służy do określania danych do pozysłania i konwertowania na wymagany schemat.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Ustawienia połączenia" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Weryfikowanie połączenia i ładowanie schematu danych

Po utworzeniu parametrów połączenia i  parametrów zapytania wybierz pozycję Weryfikuj i pobierz schemat, aby zweryfikować połączenie, i uruchom zapytanie, aby pobrać schemat danych ze źródła danych. Zwykle trwa to kilka sekund w zależności od połączenia ze źródłem danych. Jeśli w tym kroku wystąpi błąd, upewnij się, że:

1. Parametry połączenia i zapytanie są poprawne.
2. Wystąpienie Doradca w zakresie metryk może nawiązać połączenie ze źródłem danych, jeśli istnieją ustawienia zapory.

### <a name="schema-configuration"></a>Konfiguracja schematu

Po załadowaniu schematu danych i pokazanym poniżej wybierz odpowiednie pola.


|Zaznaczenie  |Opis  |Uwagi  |
|---------|---------|---------|
|**Znacznik czasu**     | Sygnatura czasowa punktu danych. W przypadku pominięcia Doradca w zakresie metryk użyje znacznika czasu, gdy punkt danych jest zamiast tego pozytywem. Dla każdego źródła danych można określić co najwyżej jedną kolumnę jako znacznik czasu.        | Opcjonalny. Należy określić co najwyżej jedną kolumnę.       |
|**Measure**     |  Wartości liczbowe w kanale informacyjnym danych. Dla każdego źródła danych można określić wiele miar, ale jako miarę należy wybrać co najmniej jedną kolumnę.        | Należy określić z co najmniej jedną kolumną.        |
|**Wymiar**     | Wartości kategorii. Kombinacja różnych wartości identyfikuje konkretny szereg czasowy pojedynczego wymiaru, na przykład: kraj, język, dzierżawa. Jako wymiary możesz wybrać żadną lub dowolną liczbę kolumn. Uwaga: jeśli jako wymiar wybierasz kolumnę bez ciągu, należy zachować ostrożność w przypadku niewydajnych wymiarów. | Opcjonalny.        |
|**Ignoruj**     | Zignoruj wybraną kolumnę.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Konfiguracja schematu" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Ustawienia automatycznego zbiorczych danych

> [!IMPORTANT]
> Jeśli chcesz włączyć analizę głównej **przyczyny i** inne możliwości diagnostyczne, należy skonfigurować "automatyczne ustawienie zbiorczo". Po włączeniu tej opcji nie można zmienić ustawień automatycznego zsuwu.

Doradca w zakresie metryk automatycznie wykonywać agregację (SUM/MAX/MIN...) w każdym wymiarze podczas pozyskiwania, a następnie tworzy hierarchię, która będzie używana w analizie przypadków głównych i innych funkcjach diagnostycznych. Aby uzyskać [więcej informacji, zobacz Ustawienia automatycznego](../how-tos/onboard-your-data.md#automatic-roll-up-settings) zsuwu.

Nadaj źródłom danych niestandardową nazwę, która będzie wyświetlana w obszarze roboczym. Kliknij pozycję **Prześlij**. 

## <a name="tune-detection-configuration"></a>Konfiguracja wykrywania dostrajania

Po dodaniu źródła danych program Doradca w zakresie metryk pozysł dane metryk od określonej daty rozpoczęcia. Pełne pozyskiwanie danych może zająć trochę czasu, a stan pozyskiwania można  wyświetlić, klikając pozycję Postęp pozyskiwania w górnej części strony źródła danych. Jeśli dane są pozysłane, Doradca w zakresie metryk zastosuje wykrywanie i będzie nadal monitorować źródło dla nowych danych.

Po zastosowaniu wykrywania kliknij jedną z metryk na liście danych, aby znaleźć stronę **Szczegóły metryki,** aby: 
- Wyświetlanie wizualizacji wszystkich wycinków szeregów czasu w ramach tej metryki
- Aktualizowanie konfiguracji wykrywania w celu spełnienia oczekiwanych wyników
- Konfigurowanie powiadomień dla wykrytych anomalii

:::image type="content" source="../media/metric-details.png" alt-text="Szczegóły metryki" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Wyświetlanie szczegółowych informacji diagnostycznych

Po dostrojeniu konfiguracji wykrywania znalezione anomalie powinny odzwierciedlać rzeczywiste anomalie w danych. Doradca w zakresie metryk przeprowadza analizę metryk wielowymiarowych, takich jak klastrowanie anomalii, korelacja zdarzeń i analiza głównej przyczyny. Za pomocą tych funkcji można analizować i diagnozować zdarzenia w danych.

Aby wyświetlić szczegółowe informacje diagnostyczne, kliknij czerwone kropki w wizualizacjach szeregów czasu, które reprezentują wykryte anomalie. Zostanie wyświetlone okno z linkiem do strony analizy zdarzeń. 

:::image type="content" source="../media/incident-link.png" alt-text="Link zdarzenia" lightbox="../media/incident-link.png":::

Po kliknięciu linku zostanie przenoszana do strony analizy zdarzeń, która analizuje odpowiadającą anomalię i zawiera szereg szczegółowych informacji diagnostycznych. W górnej części będą dostępne statystyki dotyczące zdarzenia, takie jak ważność,  **anomalie,** których dotyczy błąd , oraz czas rozpoczęcia i **godzina zakończenia.**  

Następnie zobaczysz anomalię elementu nadrzędnego zdarzenia i zautomatyzowane porady dotyczące głównej przyczyny zdarzenia. Ta automatyczna porada głównej przyczyny jest generowana przez analizę drzewa zdarzeń wszystkich powiązanych anomalii, w tym: odchylenia, rozkładu i udziału w anomaliach nadrzędnych. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnostyka zdarzeń" lightbox="../media/incident-diagnostic.png":::

Na ich podstawie można już uzyskać prosty widok tego, co się dzieje i jaki ma wpływ na zdarzenie, a także najbardziej potencjalną główną przyczynę. Dzięki czemu można podjąć natychmiastowe działania w celu rozwiązania zdarzenia tak szybko, jak to możliwe. 

Można jednak również przejść do kolejnych szczegółowych informacji diagnostycznych, korzystając z dodatkowych funkcji w celu przechodzenia do szczegółów anomalii według wymiarów, wyświetlania podobnych anomalii i porównywania metryk. Więcej informacji można znaleźć na [stronie How to: diagnose an incident (Jak zdiagnozować zdarzenie).](../how-tos/diagnose-incident.md) 

## <a name="get-notified-when-new-anomalies-are-found"></a>Otrzymuj powiadomienia o nowych anomaliach

Jeśli chcesz otrzymać alert po wykryciu anomalii w danych, możesz utworzyć subskrypcję dla co najmniej jednej metryki. Doradca w zakresie metryk używa wpięć do wysyłania alertów. Obsługiwane są trzy typy elementów zaczepienia: wpięcie poczty e-mail, web hook i Azure DevOps. Jako przykładu użyjemy web hook. 

### <a name="create-a-web-hook"></a>Tworzenie wpięcie internetowego

Element web hook to punkt wejścia, który umożliwia zauważenie anomalii w sposób programowy z usługi Doradca w zakresie metryk, która wywołuje interfejs API dostarczony przez użytkownika po wyzwoleniu alertu. Aby uzyskać szczegółowe informacje na temat tworzenia  punkt zaczepienia, zapoznaj się z sekcją Tworzenie punkt zaczepienia w tece Jak skonfigurować alerty i otrzymywać powiadomienia [przy użyciu wpięcie](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Po utworzeniu wpięcie ustawienie alertu określa sposób i powiadomienia o alertach, które mają być wysyłane. Dla każdej metryki można ustawić wiele ustawień alertów. Dwa ważne ustawienia to **Alert,** który określa anomalie, które mają zostać uwzględnione, oraz opcje **filtrowania anomalii,** które definiują anomalie, które mają zostać uwzględnione w alercie. Aby uzyskać **więcej informacji,** zobacz sekcję Dodawanie lub edytowanie ustawień alertów w te [tematu How-to: Configure alerts and get notifications using a hook](../how-tos/alerts.md#add-or-edit-alert-settings) (Jak skonfigurować alerty i otrzymywać powiadomienia przy użyciu punkt zaczepienia).


## <a name="next-steps"></a>Następne kroki

- [Dołączanie źródeł danych](../how-tos/onboard-your-data.md)
    - [Zarządzanie strumieniowymi źródłami danych](../how-tos/manage-data-feeds.md)
    - [Konfiguracje dla różnych źródeł danych](../data-feeds-from-different-sources.md)
- [Korzystanie z bibliotek interfejsu API REST lub klienta](./rest-api-and-client-library.md)
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](../how-tos/configure-metrics.md)
