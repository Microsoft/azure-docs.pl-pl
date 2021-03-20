---
title: Dołącz strumieniowe źródło danych do klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Jak zacząć dołączać strumieniowe źródła danych do usługi Metric Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043215"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Instrukcje: Dołączanie danych metryki do klasyfikatora metryk

Skorzystaj z tego artykułu, aby dowiedzieć się więcej na temat dołączania danych do usługi Metric Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Wymagania i konfiguracja schematu danych

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Unikanie ładowania danych częściowych

Częściowe dane wynikają z niespójności między danymi przechowywanymi w usłudze Metrics Advisor a źródłem danych. Taka sytuacja może wystąpić, gdy źródło danych zostanie zaktualizowane po zakończeniu przez klasyfikatora metryk danych ściągania. Klasyfikator metryk pobiera tylko dane z danego źródła danych.

Na przykład jeśli Metryka została dołączona do klasyfikatora metryk na potrzeby monitorowania. Doradca metryk pomyślnie połączy dane metryk w sygnaturze czasowej A i wykonuje na niej wykrywanie anomalii. Jednakże jeśli dane metryki określonego znacznika czasu A zostały odświeżone po pobraniu danych. Nie zostanie pobrana nowa wartość danych.

Można spróbować [wypełniać](manage-data-feeds.md#backfill-your-data-feed) dane historyczne (opisane później) w celu ograniczenia niespójności, ale nie spowoduje to wyzwolenia nowych alertów anomalii, jeśli alerty dla tych punktów czasu zostały już wyzwolone. Ten proces może spowodować dodanie dodatkowego obciążenia do systemu i nie jest automatyczny.

Aby uniknąć ładowania częściowych danych, zalecamy stosowanie dwóch metod:

* Generuj dane w jednej transakcji:

    Upewnij się, że wartości metryk dla wszystkich kombinacji wymiarów w tej samej sygnaturze czasowej są przechowywane w źródle danych w jednej transakcji. W powyższym przykładzie poczekaj, aż dane ze wszystkich źródeł danych będą gotowe, a następnie załaduj je do klasyfikatora metryk w jednej transakcji. Klasyfikator metryk może regularnie sondować strumieniowe źródło danych, dopóki dane nie zostaną pomyślnie pobrane (lub częściowo).

* Opóźnij pozyskiwanie danych przez ustawienie prawidłowej wartości parametru **przesunięcia czasu** pozyskiwania:

    Ustaw parametr **przesunięcia czasu** pozyskiwania dla źródła danych, aby opóźnić pozyskiwanie do momentu pełnego przygotowania danych. Może to być przydatne w przypadku niektórych źródeł danych, które nie obsługują transakcji, takich jak Azure Table Storage. Aby uzyskać szczegółowe informacje, zobacz [Ustawienia zaawansowane](manage-data-feeds.md#advanced-settings) .

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Dodawanie strumieniowego źródła danych przy użyciu obszaru roboczego opartego na sieci Web

Po zalogowaniu się do portalu klasyfikatora metryk i wybraniu obszaru roboczego kliknij pozycję **Rozpocznij pracę**. Następnie na stronie głównej obszaru roboczego kliknij pozycję **Dodaj strumieniowe źródło danych** z menu po lewej stronie.

### <a name="add-connection-settings"></a>Dodaj ustawienia połączenia

Następnie będziesz wprowadzać zestaw parametrów w celu połączenia ze źródłem danych szeregów czasowych. 
* **Typ źródła**: typ źródła danych, w którym są przechowywane dane szeregów czasowych.
* **Stopień szczegółowości**: interwał między kolejnymi punktami danych w danych szeregów czasowych. Obecnie klasyfikator metryk obsługuje: rocznie, co miesiąc, co tydzień, codziennie, co godzinę i niestandardowe. Najniższy interwał, który obsługuje opcja dostosowywania to 60 sekund.
  * **Sekundy**: liczba sekund, po których *granularityName* jest ustawiony na *Dostosowywanie*.
* Pozyskanie **danych od (UTC)**: czasu rozpoczęcia okresu bazowego do pozyskiwania danych. *startOffsetInSeconds* jest często używany do dodawania przesunięcia w celu zapewnienia spójności danych.

Następnie należy określić informacje o połączeniu dla źródła danych, a następnie niestandardowe zapytania używane do konwersji danych do wymaganego schematu. Aby uzyskać szczegółowe informacje na temat innych pól i łączenia różnych typów źródeł danych, zobacz [Dodawanie źródeł danych z różnych źródeł danych](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Weryfikowanie i pobieranie schematu

Po ustawieniu parametrów połączenia i ciągu zapytania wybierz pozycję **Weryfikuj i Pobierz schemat** , aby zweryfikować połączenie i uruchomić zapytanie w celu pobrania schematu danych ze źródła danych. Zwykle trwa kilka sekund w zależności od połączenia ze źródłem danych. Jeśli w tym kroku wystąpi błąd, potwierdź, że:

* Parametry połączenia i zapytanie są poprawne.
* Wystąpienie usługi Advisor metryk jest w stanie połączyć się ze źródłem danych, jeśli istnieją ustawienia zapory.

### <a name="schema-configuration"></a>Konfiguracja schematu

Po załadowaniu schematu danych wybierz odpowiednie pola.

Jeśli sygnatura czasowa punktu danych zostanie pominięta, Doradca metryk będzie używać sygnatury czasowej, gdy w zamian zostanie wprowadzony punkt danych. Dla każdego źródła danych można określić maksymalnie jedną kolumnę jako sygnaturę czasową. Jeśli zostanie wyświetlony komunikat informujący o tym, że nie można określić kolumny jako sygnatury czasowej, należy sprawdzić zapytanie lub źródło danych oraz czy w wyniku zapytania występuje wiele sygnatur czasowych — nie tylko w podglądzie danych. Podczas pozyskiwania danych, klasyfikator metryk może zużywać tylko jeden fragment (na przykład jeden dzień, godzinę — zgodnie z stopnia szczegółowości) danych szeregów czasowych z danego źródła za każdym razem.

|Zaznaczenie  |Opis  |Uwagi  |
|---------|---------|---------|
| **Nazwa wyświetlana** | Nazwa, która ma być wyświetlana w obszarze roboczym, a nie oryginalna nazwa kolumny. | |
|**Znacznik czasu**     | Sygnatura czasowa punktu danych. W przypadku pominięcia klasyfikator metryk będzie używać sygnatury czasowej, gdy zostanie wprowadzony punkt danych. Dla każdego źródła danych można określić maksymalnie jedną kolumnę jako sygnaturę czasową.        | Opcjonalny. Należy określić z co najwyżej jedną kolumną. Jeśli **nie można określić kolumny jako błąd znacznika czasu** , sprawdź zapytanie lub źródło danych dla zduplikowanych sygnatur czasowych.      |
|**Measure**     |  Wartości liczbowe w strumieniowym źródle danych. Dla każdego źródła danych można określić wiele miar, ale do miary należy wybrać co najmniej jedną kolumnę.        | Należy określić z co najmniej jedną kolumną.        |
|**Wymiar**     | Kategorii wartości. Kombinacja różnych wartości identyfikuje konkretną serię czasową pojedynczego wymiaru, na przykład: Country, language, dzierżawca. Możesz wybrać zero lub więcej kolumn jako wymiary. Uwaga: należy zachować ostrożność podczas wybierania kolumny niebędącej ciągiem jako wymiaru. | Opcjonalny.        |
|**Ignoruj**     | Ignoruj wybraną kolumnę.        | Opcjonalny. Zobacz poniższy tekst.       |

Jeśli chcesz zignorować kolumny, Zalecamy zaktualizowanie zapytania lub źródła danych w celu wykluczenia tych kolumn. Możesz również zignorować kolumny, używając opcji **Ignoruj kolumny** , a następnie **ignorując** je w określonych kolumnach. Jeśli kolumna powinna być wymiarem i jest traktowana jako *ignorowana*, klasyfikator metryk może zakończyć pozyskiwanie danych częściowych. Załóżmy na przykład, że dane z zapytania są następujące:

| Identyfikator wiersza | Znacznik czasu | Kraj | Język | Income |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Chiny | ZH-CN | 10 000 |
| 2 | 2019/11/10 | Chiny | PL-US | 1000 |
| 3 | 2019/11/10 | USA | ZH-CN | 12000 |
| 4 | 2019/11/11 | USA | PL-US | 23000 |
| ... | ...| ... | ... | ... |

Jeśli *kraj* jest wymiarem, a *Język* jest ustawiony jako *ignorowany*, pierwszy i drugi wiersz będzie miał te same wymiary. Klasyfikator metryk będzie korzystać z jednej wartości z dwóch wierszy. Doradca metryk nie będzie agregować wierszy w tym przypadku.

### <a name="automatic-roll-up-settings"></a>Ustawienia automatycznego zestawiania

> [!IMPORTANT]
> Jeśli chcesz włączyć analizę głównej przyczyny i inne funkcje diagnostyczne, należy skonfigurować **Ustawienia automatycznego wycofywania** . Po włączeniu nie można zmienić ustawień automatycznego wycofywania.

Usługa Advisor Metrics może automatycznie wykonywać agregację (na przykład SUM, MAX, MIN) w każdym wymiarze podczas pozyskiwania, a następnie tworzy hierarchię, która będzie używana w głównej analizie przypadku i w innych funkcjach diagnostycznych. 

Rozważ następujące scenariusze:

* *Nie muszę dołączać analizy zbiorczej danych.*

    Nie trzeba używać rzutowania klasyfikatora metryk.

* *Moje dane zostały już rzutowane, a wartość wymiaru jest reprezentowana przez: wartość NULL lub puste (wartość domyślna), tylko wartość NULL, inne.*

    Ta opcja oznacza, że usługa Advisor metryk nie musi rzutować danych, ponieważ wiersze są już sumowane. Na przykład jeśli zaznaczysz *tylko wartość null*, drugi wiersz danych w poniższym przykładzie będzie traktowany jako agregacja wszystkich krajów i języka *en-us*; Czwarty wiersz danych, który ma pustą wartość dla *kraju* , będzie jednak widoczny jako zwykły wiersz, który może wskazywać na niekompletne dane.
    
    | Kraj | Język | Income |
    |---------|----------|--------|
    | Chiny   | ZH-CN    | 10 000  |
    | NULL  | PL-US    | 999999 |
    | USA      | PL-US    | 12000  |
    |         | PL-US    | 5000   |

* *Potrzebuję usługi Metric Advisor do rzutowania danych, obliczając sumę/max/min/AVG/Count i reprezentując ją <some string>*

    Niektóre źródła danych, takie jak Cosmos DB lub Azure Blob Storage, nie obsługują pewnych obliczeń, takich jak *Grupuj według* lub *Cube*. Klasyfikator metryk zawiera opcję rzutowania, która umożliwia automatyczne generowanie modułu danych podczas pozyskiwania.
    Ta opcja oznacza, że potrzebujesz doradcy metryk, aby obliczyć rzutowanie przy użyciu wybranego algorytmu i użyć określonego ciągu do reprezentowania zestawienia w usłudze Metric Advisor. Nie spowoduje to zmiany danych w źródle danych.
    Załóżmy na przykład, że masz zestaw szeregów czasowych, który oznacza metryki sprzedaży z wymiarem (kraj, region). Dla danego znacznika czasu może wyglądać następująco:


    | Country (Kraj)       | Region (Region)           | Sales (Sprzedaż) |
    |---------------|------------------|-------|
    | Kanada        | Alberta          | 100   |
    | Kanada        | Kolumbia Brytyjska | 500   |
    | Stany Zjednoczone | Montana          | 100   |


    Po włączeniu autouzupełniania z *sumą*, Doradca metryk oblicza kombinacje wymiarów i sumuje metryki podczas pozyskiwania danych. Wynik może być:

    | Country (Kraj)       | Region (Region)           | Sales (Sprzedaż) |
    | ------------ | --------------- | ---- |
    | Kanada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Kanada        | Kolumbia Brytyjska | 500   |
    | NULL          | Kolumbia Brytyjska | 500   |
    | Stany Zjednoczone | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Kanada        | NULL             | 600   |
    | Stany Zjednoczone | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` oznacza, że suma sprzedaży w Kanadzie (wszystkie regiony) to 600.

    Poniżej przedstawiono transformację języka SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Przed użyciem funkcji autowycofywania należy wziąć pod uwagę następujące kwestie:

    * Jeśli chcesz używać *sum* do agregowania danych, upewnij się, że metryki są addytywne w każdym wymiarze. Poniżej przedstawiono kilka przykładów metryk nienależących do *addytywne* :
      * Metryki oparte na częściach. Obejmuje to współczynnik, wartość procentową itp. Na przykład nie należy dodawać stawki bezrobotnych każdego stanu, aby obliczyć stawkę bezrobotną całego kraju.
      * Nakładanie się na wymiar. Na przykład nie należy dodawać liczby osób w każdej sportie, aby obliczyć liczbę osób, które lubią Sport, ponieważ między nimi jest nakładana jedna osoba, która może być taka sama, jak wiele różnych sportów.
    * Aby zapewnić prawidłowość całego systemu, rozmiar modułu jest ograniczony. Obecnie limit wynosi 1 000 000. Jeśli dane przekroczą ten limit, pozyskiwanie zakończy się niepowodzeniem dla tej sygnatury czasowej.

## <a name="advanced-settings"></a>Ustawienia zaawansowane

Istnieje kilka zaawansowanych ustawień umożliwiających wprowadzanie danych w dostosowany sposób, takich jak określanie przesunięcia pozyskiwania lub współbieżności. Aby uzyskać więcej informacji, zobacz sekcję [Ustawienia zaawansowane](manage-data-feeds.md#advanced-settings) w artykule Zarządzanie strumieniowym źródłem danych.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Określ nazwę strumieniowego źródła danych i sprawdź postęp pozyskiwania
 
Nadaj nazwę niestandardową strumieniowego źródła danych, która będzie wyświetlana w obszarze roboczym. Następnie kliknij pozycję **Prześlij**. Na stronie Szczegóły strumieniowego źródła danych można wyświetlić informacje o stanie przy użyciu paska postępu pozyskiwania.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Pasek postępu pozyskiwania" lightbox="../media/datafeeds/ingestion-progress.png":::


Aby sprawdzić szczegóły niepowodzenia pozyskiwania: 

1. Kliknij przycisk **Pokaż szczegóły**.
2. Kliknij pozycję **stan** , a następnie wybierz polecenie **Niepowodzenie** lub **błąd**.
3. Umieść kursor nad pomyślnym pozyskaniem, a następnie Wyświetl wyświetlony komunikat szczegóły.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Sprawdź, czy pobieranie nie powiodło się":::

Stan *niepowodzenia* wskazuje, że pozyskanie dla tego źródła danych zostanie ponowione później.
Stan *błędu* oznacza, że Doradca metryk nie ponowi próby dla źródła danych. Aby ponownie załadować dane, należy wyzwolić ręczne wypełnianie lub ponowne załadowanie.

Możesz również ponownie załadować postęp pozyskiwania, klikając przycisk **Odśwież postęp**. Po zakończeniu pozyskiwania danych można kliknąć pozycję metryki i sprawdzić wyniki wykrywania anomalii.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie źródłami danych](manage-data-feeds.md)
- [Konfiguracje dla różnych źródeł danych](../data-feeds-from-different-sources.md)
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
