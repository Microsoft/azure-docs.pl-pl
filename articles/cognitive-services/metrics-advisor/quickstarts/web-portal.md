---
title: 'Szybki Start: Portal sieci Web doradcy metryk'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zacząć korzystać z portalu sieci Web usługi Advisor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 004685a50e2413c29528ad3aca08a0150843a8aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631387"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Szybki Start: monitorowanie pierwszej metryki przy użyciu portalu sieci Web

Po zainicjowaniu obsługi administracyjnej wystąpienia klasyfikatora metryk można korzystać z interfejsów API i obszaru roboczego opartego na sieci Web do pracy z usługą. Obszar roboczy oparty na sieci Web może służyć jako prosty sposób na szybkie rozpoczęcie pracy z usługą. Udostępnia również wizualizację, aby skonfigurować ustawienia, dostosować model i przeprowadzić analizę głównych przyczyn. 

* Dołączanie danych metryki
* Wyświetlanie metryk i wizualizacji
* Ustawienia wykrywania dostrajania
* Eksplorowanie szczegółowych informacji diagnostycznych
* Tworzenie i subskrybowanie alertów anomalii

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Gdy masz subskrypcję platformy Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Utwórz zasób klasyfikatora metryk "  target="_blank"> Utwórz zasób klasyfikatora metryk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby wdrożyć wystąpienie usługi Advisor metryk.  

    
> [!TIP]
> * Do wdrożenia zasobu usługi Advisor metryk może być od 10 do 30 minut. Po pomyślnym wdrożeniu kliknij pozycję **Przejdź do zasobu** .
> * Jeśli chcesz korzystać z interfejsu API REST w celu korzystania z usługi, będziesz potrzebować klucza i punktu końcowego z utworzonego zasobu. Można je znaleźć na karcie  **klucze i punkty końcowe** w utworzonym zasobie.

Ten dokument używa SQL Database jako przykładu do tworzenia pierwszego monitora.

## <a name="sign-in-to-your-workspace"></a>Zaloguj się do swojego obszaru roboczego

Po wdrożeniu zasobu Zaloguj się do [portalu klasyfikatora metryk](https://go.microsoft.com/fwlink/?linkid=2143774). Wybierz swój obszar roboczy, aby rozpocząć monitorowanie metryk. 
 
Obecnie można utworzyć jeden zasób usługi Advisor metryk w każdym z dostępnych regionów. W dowolnym momencie możesz przełączyć obszary robocze w portalu usługi Advisor metryk.


## <a name="onboard-time-series-data"></a>Dołączanie danych szeregów czasowych

Usługa Advisor Metrics oferuje łączniki dla różnych źródeł danych, takich jak SQL Database, Azure Eksplorator danych i Azure Table Storage. Kroki dotyczące łączenia danych są podobne dla różnych łączników, chociaż niektóre parametry konfiguracji mogą się różnić. Zobacz [łączenie danych z różnych źródeł](../data-feeds-from-different-sources.md) dla wymaganych parametrów dla określonych źródeł danych.

Ten przewodnik Szybki Start używa SQL Database jako przykładu. Możesz również pozyskać własne dane, wykonując te same czynności.

Aby rozpocząć, zaloguj się do obszaru roboczego usługi Advisor metryk przy użyciu konta Active Directory. Na stronie docelowej wybierz swój **katalog**, **subskrypcję** i **obszar roboczy** , który właśnie został utworzony, a następnie kliknij pozycję **Rozpocznij**. Po rozpoczęciu ładowania obciążenia na stronie głównej wybierz pozycję **Dodaj strumieniowe źródło danych** z menu po lewej stronie.

### <a name="data-schema-requirements-and-configuration"></a>Wymagania i konfiguracja schematu danych

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Konfiguruj ustawienia połączenia

> [!TIP]
> Zobacz [jak dodać strumieniowe źródła danych](../how-tos/onboard-your-data.md) , aby uzyskać szczegółowe informacje na temat dostępnych parametrów.

Dodaj strumieniowe źródło danych, łącząc się ze źródłem danych szeregów czasowych. Zacznij od wybrania następujących parametrów:

* **Typ źródła**: typ źródła danych, w którym są przechowywane dane szeregów czasowych.
* **Stopień szczegółowości**: interwał między kolejnymi punktami danych w danych szeregów czasowych, na przykład co rok, co miesiąc, codziennie. Najniższe przedziały są obsługiwane przez 60 sekund.
* Pobieranie **danych od (UTC)**: godzina rozpoczęcia dla pierwszej sygnatury czasowej do pozyskania. 


Następnie określ **Parametry połączenia** z poświadczeniami dla źródła danych i **zapytanie**niestandardowe. Zapytanie służy do określania danych do pozyskania i przekonwertowania na wymagany schemat.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Ustawienia połączenia" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>Weryfikowanie połączenia i ładowanie schematu danych

Po utworzeniu parametrów połączenia i ciągu zapytania wybierz pozycję **Weryfikuj i Pobierz schemat** , aby zweryfikować połączenie i uruchomić zapytanie w celu pobrania schematu danych ze źródła danych. Zwykle trwa kilka sekund w zależności od połączenia ze źródłem danych. Jeśli w tym kroku wystąpi błąd, potwierdź, że:

1. Parametry połączenia i zapytanie są poprawne.
2. Wystąpienie usługi Advisor metryk jest w stanie połączyć się ze źródłem danych, jeśli istnieją ustawienia zapory.

### <a name="schema-configuration"></a>Konfiguracja schematu

Gdy schemat danych zostanie załadowany i przedstawiony poniżej, wybierz odpowiednie pola.


|Zaznaczenie  |Opis  |Uwagi  |
|---------|---------|---------|
|**Timestamp**     | Sygnatura czasowa punktu danych. W przypadku pominięcia klasyfikator metryk będzie używać sygnatury czasowej, gdy zostanie wprowadzony punkt danych. Dla każdego źródła danych można określić maksymalnie jedną kolumnę jako sygnaturę czasową.        | Opcjonalny. Należy określić z co najwyżej jedną kolumną.       |
|**Measure**     |  Wartości liczbowe w strumieniowym źródle danych. Dla każdego źródła danych można określić wiele miar, ale do miary należy wybrać co najmniej jedną kolumnę.        | Należy określić z co najmniej jedną kolumną.        |
|**Wymiar**     | Kategorii wartości. Kombinacja różnych wartości identyfikuje konkretną serię czasową pojedynczego wymiaru, na przykład: Country, language, dzierżawca. Jako wymiary można wybrać opcję Brak lub dowolną liczbę kolumn. Uwaga: Jeśli wybierasz kolumnę niebędącą ciągiem jako wymiar, należy zachować ostrożność z rozłożeniem wymiaru. | Opcjonalny.        |
|**Ignoruj**     | Ignoruj wybraną kolumnę.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Ustawienia połączenia" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Ustawienia automatycznego zestawiania

> [!IMPORTANT]
> Jeśli chcesz włączyć **analizę głównej przyczyny** i inne funkcje diagnostyczne, należy skonfigurować ustawienie "Automatyczne agregowanie". Po włączeniu nie można zmienić ustawień automatycznego wycofywania.

Usługa Advisor Metrics może automatycznie wykonywać agregację (SUM/MAX/MIN...) w każdym wymiarze podczas pozyskiwania, a następnie tworzy hierarchię, która będzie używana w głównej analizie przypadku i w innych funkcjach diagnostycznych. Aby uzyskać więcej informacji, zobacz sekcję [Automatyczne konfigurowanie ustawień](../how-tos/onboard-your-data.md#automatic-roll-up-settings) .

Nadaj nazwę niestandardową strumieniowego źródła danych, która będzie wyświetlana w obszarze roboczym. Kliknij pozycję **Prześlij**. 

## <a name="tune-detection-configuration"></a>Dostrajanie konfiguracji wykrywania

Po dodaniu strumieniowego źródła danych klasyfikator metryk podejmie próbę pozyskania danych metryk z określonej daty rozpoczęcia. Dane zostaną w pełni pozyskane i będzie można wyświetlić stan pozyskiwania, klikając pozycję **postęp** pozyskiwania w górnej części strony strumieniowego źródła danych. W przypadku pozyskiwania danych, Doradca metryk zastosuje wykrywanie i kontynuuje monitorowanie źródła pod kątem nowych danych.

Po zastosowaniu wykrywania kliknij jedno z metryk wymienionych w strumieniowym źródle danych, aby znaleźć **stronę szczegółów metryki** : 
- Wyświetl wizualizacje wszystkich wycinków szeregów czasowych w ramach tej metryki
- Aktualizowanie konfiguracji wykrywania w celu spełnienia oczekiwanych wyników
- Skonfiguruj powiadomienia dla wykrytych anomalii

:::image type="content" source="../media/metric-details.png" alt-text="Ustawienia połączenia" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Wyświetlanie szczegółowych informacji diagnostycznych

Po dostrojeniu konfiguracji wykrywania anomalie, które zostaną znalezione, powinny odzwierciedlać rzeczywiste anomalie w danych. Klasyfikator metryk wykonuje analizę na wielowymiarowych metrykach, takich jak klastrowanie anomalii, korelacja zdarzeń i analiza głównych przyczyn. Korzystając z tych funkcji, można analizować i diagnozować zdarzenia w danych.

Aby wyświetlić szczegółowe informacje diagnostyczne, kliknij czerwoną kropkę w wizualizacjach szeregów czasowych, które reprezentują wykryte anomalie. Zostanie wyświetlone okno z linkiem do strony analiza zdarzeń. 

:::image type="content" source="../media/incident-link.png" alt-text="Ustawienia połączenia" lightbox="../media/incident-link.png":::

Po kliknięciu linku nastąpi przestawienie na stronę analiza zdarzeń, która analizuje w odpowiedniej anomalii, za pomocą szeregu szczegółowych informacji diagnostycznych. W górnej części znajdują się statystyki dotyczące zdarzenia, takie jak **ważność**, **związane anomalie**oraz czas **rozpoczęcia** i **czas zakończenia**. 

Następnie zobaczysz nietypową anomalię zdarzenia oraz zautomatyzowane porady dotyczące głównych przyczyn. To zautomatyzowane porady dotyczące głównej przyczyny są generowane przez analizowanie drzewa incydentów wszystkich powiązanych anomalii, w tym: odchylenia, dystrybucji i udziału w przypadku nadrzędnych anomalii. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Ustawienia połączenia" lightbox="../media/incident-diagnostic.png":::

Na podstawie tych informacji można już uzyskać prosty widok tego, co się dzieje, i wpływ zdarzenia, jak również najbardziej potencjalną główną przyczynę. Dzięki temu można natychmiast podjąć działania w celu rozpoznania zdarzenia najszybciej, jak to możliwe. 

Możesz również przestawić więcej informacji diagnostycznych przy użyciu dodatkowych funkcji, aby przeanalizować anomalie według wymiarów, wyświetlić podobne anomalie i porównać je w ramach metryk. Więcej informacji można znaleźć w [tematach: diagnozowanie zdarzenia](../how-tos/diagnose-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Otrzymuj powiadomienia o znalezieniu nowych anomalii

Jeśli chcesz otrzymywać alerty w przypadku wykrycia anomalii w danych, możesz utworzyć subskrypcję dla co najmniej jednej metryki. Klasyfikator metryk używa punktów zaczepienia do wysyłania alertów. Obsługiwane są trzy typy punktów zaczepienia: hak poczty e-mail, element webhook i Azure DevOps. Jako przykładu będziemy używać elementu webhook. 

### <a name="create-a-web-hook"></a>Tworzenie elementu webhook

Element webhook to punkt wejścia, który pozwala na uzyskanie anomalii w sposób programistyczny od usługi Advisor metryk, która wywołuje interfejs API udostępniony przez użytkownika po wyzwoleniu alertu. Aby uzyskać szczegółowe informacje na temat sposobu tworzenia punktu zaczepienia, zapoznaj się z sekcją **Tworzenie punktu zaczepienia** w temacie [How to: Configure a Notifications and otrzymywanie powiadomień przy użyciu punktu zaczepienia](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Po utworzeniu punktu zaczepienia ustawienie alertu określa, jak i które powiadomienia o alertach powinny być wysyłane. Dla każdej metryki można ustawić wiele ustawień alertów. Istnieją dwa ważne ustawienia **alertu, dla** którego są uwzględniane anomalie, oraz **filtrowania opcji anomalii** , które definiują anomalie do uwzględnienia w alercie. Aby uzyskać więcej informacji, zobacz sekcję **Dodawanie lub edytowanie ustawień alertów** w temacie [How to: Configure Alerts and get Notifications using the hook](../how-tos/alerts.md#add-or-edit-alert-settings) .


## <a name="next-steps"></a>Następne kroki

- [Dołączanie źródeł danych](../how-tos/onboard-your-data.md)
    - [Zarządzanie strumieniowymi źródłami danych](../how-tos/manage-data-feeds.md)
    - [Konfiguracje dla różnych źródeł danych](../data-feeds-from-different-sources.md)
- [Użyj interfejsu API REST lub bibliotek klienckich](rest-api.md)
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](../how-tos/configure-metrics.md)
