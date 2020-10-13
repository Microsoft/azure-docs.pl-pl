---
title: Konfigurowanie wystąpienia usługi Advisor metryk przy użyciu portalu sieci Web
titleSuffix: Azure Cognitive Services
description: Jak skonfigurować wystąpienie usługi Advisor metryk i dostosować wyniki wykrywania anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 93fdf2884ca6593cfdb4fb2878ba0dd21246266d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446351"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Instrukcje: Konfigurowanie metryk i dostrajanie konfiguracji wykrywania

Ten artykuł służy do uruchamiania konfigurowania wystąpienia usługi Advisor metryk przy użyciu portalu internetowego. Aby przeglądać metryki dla określonego strumieniowego źródła danych, przejdź do strony **strumieniowe źródła danych** i wybierz jeden z kanałów informacyjnych. Spowoduje to wyświetlenie listy skojarzonych z nią metryk.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Wybierz metrykę" lightbox="../media/metrics/select-metric.png":::

Kliknij jedną z nazw metryk, aby wyświetlić jej szczegóły. W tym widoku szczegółowym można przełączyć się na inną metrykę w tym samym strumieniu danych przy użyciu listy rozwijanej w prawym górnym rogu ekranu.

Po pierwszym wyświetleniu szczegółów metryk można załadować szeregi czasowe, przez co wybór usługi Metric Advisor wybiera jeden dla Ciebie lub przez określenie wartości do uwzględnienia dla każdego wymiaru. 

Możesz również wybrać zakresy czasowe i zmienić układ strony.

> [!NOTE]
> - Godzina rozpoczęcia jest włącznie.
> - Czas zakończenia jest na wyłączność. 

Możesz kliknąć kartę **incydenty** , aby wyświetlić anomalie i znaleźć łącze do [centrum zdarzeń](diagnose-incident.md).

## <a name="tune-the-detecting-configuration"></a>Dostrajanie konfiguracji wykrywania

Metryka może zastosować co najmniej jedną konfigurację wykrywania. Istnieje konfiguracja domyślna dla każdej metryki, którą można edytować lub dodać do, zgodnie z potrzebami monitorowania.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Dostosuj konfigurację dla wszystkich serii w bieżącej metryce

Ta konfiguracja zostanie zastosowana do wszystkich serii w tej metryce, z wyjątkiem tych, które mają osobną konfigurację. Konfiguracja poziomu metryki jest stosowana domyślnie podczas dołączania danych i jest wyświetlana na panelu po lewej stronie. Użytkownicy mogą bezpośrednio edytować konfigurację poziomu metryk na stronie metryki. 

Istnieją dodatkowe parametry, takie jak **kierunek**i **prawidłowe anomalie** , których można użyć do dokładniejszego dostosowania konfiguracji. Można również łączyć różne metody wykrywania. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Wybierz metrykę" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Dostosuj konfigurację dla określonej serii lub grupy

Kliknij pozycję **Konfiguracja zaawansowana** poniżej opcji konfiguracja poziomu metryki, aby wyświetlić konfigurację poziomu grupy. Możesz dodać konfigurację dla pojedynczej serii lub grupy serii, klikając **+** ikonę w tym oknie. Parametry są podobne do parametrów konfiguracji poziomu metryk, ale może być konieczne określenie co najmniej jednej wartości wymiaru dla konfiguracji na poziomie grupy, aby zidentyfikować grupę serii. Określ wszystkie wartości wymiarów dla konfiguracji na poziomie serii, aby zidentyfikować konkretną serię. 

Ta konfiguracja zostanie zastosowana do grupy serii lub określonej serii zamiast konfiguracji poziomu metryki. Po ustawieniu warunków dla tej grupy Zapisz ją.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Wybierz metrykę" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Metody wykrywania anomalii

Klasyfikator metryk oferuje wiele metod wykrywania anomalii. Możesz użyć jednego lub połączyć je za pomocą operatorów logicznych, klikając **+** przycisk. 

**Inteligentne wykrywanie** 

Inteligentne wykrywanie jest obsługiwane przez uczenie maszynowe, które uczy się od wzorców danych historycznych i używa ich do wykrywania w przyszłości. W przypadku korzystania z tej metody **czułość** jest najważniejszym parametrem służącym do dostrajania wyników wykrywania. Możesz przeciągnąć ją do mniejszej lub większej wartości, aby mieć wpływ na wizualizację po prawej stronie. Wybierz jedną, która pasuje do danych i Zapisz ją. 


W trybie wykrywania inteligentnego parametry czułości i wersji granicy są używane do precyzyjnego dostrajania wyniku wykrywania anomalii.

Czułość może wpływać na szerokość oczekiwanego zakresu wartości każdego punktu. Po zwiększeniu oczekiwany zakres wartości będzie większy i zostanie zgłoszonych więcej anomalii:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Wybierz metrykę":::

Gdy czułość jest wyłączona, oczekiwany zakres wartości będzie szerszy i zostanie zgłoszonych mniej anomalii:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Wybierz metrykę":::

**Próg zmiany** 

Próg zmiany jest zwykle używany, gdy dane metryk są zwykle wokół określonego zakresu. Próg jest ustawiany zgodnie z **wartością procentową zmiany**. Tryb **progu zmiany** może wykrywać anomalie w scenariuszach:

* Dane są zwykle stabilne i bezproblemowe. Chcesz otrzymywać powiadomienia o wahaniach.
* Dane są zwykle bardzo niestabilne i wymagają dużej ilości danych. Chcesz otrzymywać powiadomienia, gdy staną się zbyt stabilne lub płaskie.

Wykonaj następujące kroki, aby użyć tego trybu:

1. Wybierz pozycję **Zmień próg** jako metodę wykrywania anomalii, gdy ustawisz konfiguracje wykrywania anomalii dla metryk lub szeregów czasowych.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="Wybierz metrykę":::

2. Wybierz **poza zakres** lub parametr **zakres w** oparciu o twój scenariusz.

    Jeśli chcesz wykryć wahania, zaznacz **poza zakresem**. Na przykład z poniższymi ustawieniami wszystkie punkty danych, które zmieniają się ponad 10% w porównaniu z poprzednią, zostaną wykryte jako elementy odstające.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="Wybierz metrykę":::

    Jeśli chcesz wykryć linie płaskie w danych, wybierz **zakres**. Na przykład z poniższymi ustawieniami każdy punkt danych, który zmienia się w ciągu 0,01% w porównaniu do poprzedniej, zostanie wykryty jako element odstający. Ponieważ próg jest tak mały (0,01%), wykrywa płaskie linie w danych jako elementy odstające.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="Wybierz metrykę":::

3. Ustaw wartość procentową zmiany, która będzie liczona jako anomalia i które wcześniej przechwycone punkty danych zostaną użyte do porównania. To porównanie jest zawsze między bieżącym punktem danych, a jeden punkt danych N wskazuje przed nim.
    
    **Kierunek** jest prawidłowy tylko wtedy, gdy używany jest tryb **poza zakresem** :
    
    * Umożliwia **skonfigurowanie wykrywania** w celu wykrywania anomalii tylko wtedy, gdy (bieżący punkt danych) — (punkt danych porównania) > **+** procent progu.
    * **W dół** konfiguruje wykrywanie, aby wykrywać anomalie tylko wtedy, gdy (bieżący punkt danych) — (porównywanie punktów danych) < **-** procent progu.
 
**Próg sztywny**

 Próg twardy to podstawowa metoda wykrywania anomalii. Można ustawić górną i/lub dolną granicę, aby określić oczekiwany zakres wartości. Wszystkie punkty wykraczające poza granicę zostaną zidentyfikowane jako anomalie. 


## <a name="preset-events"></a>Wstępnie zdefiniowane zdarzenia

Czasami oczekiwane zdarzenia i wystąpienia (takie jak dni wolne) mogą generować anomalie dane. Przy użyciu wstępnie ustawionych zdarzeń można dodać flagi do danych wyjściowych wykrywania anomalii w określonym czasie. Ta funkcja powinna zostać skonfigurowana po dodaniu strumieniowego źródła danych. Każda Metryka może mieć tylko jedną wstępnie ustawioną konfigurację zdarzenia.

> [!Note]
> Konfiguracja wstępnie ustawionych zdarzeń będzie uwzględniać dni wolne podczas wykrywania anomalii i może zmienić wyniki. Zostanie ona zastosowana do punktów danych pozyskanych po zapisaniu konfiguracji. 

Kliknij przycisk **Konfiguruj wstępnie ustawiony zdarzenie** obok listy rozwijanej metryki na każdej stronie szczegółów metryk.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="Wybierz metrykę":::

W wyświetlonym oknie Skonfiguruj opcje zgodnie z użyciem. Upewnij się, że wybrano **opcję Włącz zdarzenie urlopowe** , aby użyć konfiguracji. 

Sekcja **zdarzeń wolnych** ułatwia pomijanie niepotrzebnych anomalii wykrytych podczas świąt. Dostępne są dwie opcje **strategii** , które można zastosować:

* **Pomiń dni wolne**: pomija wszystkie anomalie i alerty w wynikach wykrywania anomalii w okresie świątecznym.
* **Dni wolne jako weekendowe**: oblicza średnią oczekiwane wartości w kilku odpowiednich weekendach przed świętem, a także określa stan anomalii poza tymi wartościami.

Istnieje kilka innych wartości, które można skonfigurować:

|Opcja  |Opis  |
|---------|---------|
|**Wybierz jeden wymiar jako kraj**     | Wybierz wymiar zawierający informacje o kraju. Na przykład kod kraju.         |
|**Mapowanie kodu kraju**     | Mapowanie między standardowym [kodem kraju](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)a wybranymi danymi kraju wymiaru.        |
|**Opcje dni wolnych**    | Bez względu na to, czy należy wziąć pod uwagę wszystkie dni wolne, PTO (płatny czas w przypadku świąt), czy tylko dni wolne od PTO.         |
|**Dni do rozwinięcia**    |  Liczba dni przed upływem dnia i po nim.        |


Sekcja **zdarzenia cyklu** może być używana w niektórych scenariuszach, aby pomóc w zmniejszeniu niepotrzebnych alertów przy użyciu wzorców cyklicznych w danych. Na przykład: 

- Metryki, które mają wiele wzorców lub cykli, takich jak wzorzec tygodniowy i miesięczny. 
- Metryki, które nie mają wyraźnego wzorca, ale dane są porównywalne rok na rok (zatrudnienia), miesiąc w miesiącu (MoM), tydzień w tygodniu (WoW) lub dzień dziennie (DoD).
 
Nie wszystkie opcje są dostępne dla każdego stopnia szczegółowości. Dostępne opcje na poziomie szczegółowości są następujące:

| Poziom szczegółowości | Zatrudnienia | Program | WoW | DoD |
|:-|:-|:-|:-|:-|
| Rocznie | X | X | X | X |
| Co miesiąc | X | X | X | X |
| Co tydzień | ✔ | X | X | X |
| Codziennie | ✔ | ✔ | ✔ | X |
| Godzinowe | ✔ | ✔ | ✔ | ✔ |
| Co minutę | X | X | X | X |
| Po drugie | X | X | X | X |
| Celnej | ✔ | ✔ | ✔ | ✔ |

X — niedostępne.  
✔ — Dostępne.
  
\* W przypadku korzystania z niestandardowego stopnia szczegółowości w sekundach dostępne tylko wtedy, gdy Metryka jest dłuższa niż godzina i krótsza niż jeden dzień.

Zdarzenie cykliczne służy do zmniejszania anomalii, jeśli są one zgodne ze wzorcem cyklicznym, ale zgłasza anomalię, jeśli wiele punktów danych nie jest zgodnych ze wzorcem. **Tryb Strict** jest używany do włączania raportowania anomalii, jeśli nawet jeden punkt danych nie jest zgodny ze wzorcem. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="Wybierz metrykę":::

## <a name="view-recent-incidents"></a>Wyświetlanie ostatnich zdarzeń

Klasyfikator metryk wykrywa anomalie dla wszystkich danych szeregów czasowych w miarę ich pozyskiwania. Jednak nie wszystkie anomalie muszą zostać przeeskalacjne, ponieważ mogą nie mieć dużego wpływu. Agregacja będzie wykonywana w przypadku anomalii w celu grupowania powiązanych z nimi zdarzeń. Te zdarzenia można wyświetlić na karcie **zdarzenie** na stronie Szczegóły metryk. 

Kliknij zdarzenie, aby przejść do strony **Analiza zdarzeń** , na której można zobaczyć więcej szczegółów na jej temat. Kliknij pozycję **Zarządzaj incydentami w nowym centrum zdarzeń**, aby znaleźć stronę [centrum zdarzeń](diagnose-incident.md) , na której można znaleźć wszystkie incydenty pod określoną metryką. 

## <a name="subscribe-anomalies-for-notification"></a>Subskrybuj anomalie dla powiadomienia

Jeśli chcesz otrzymywać powiadomienia o wykryciu anomalii, możesz subskrybować alerty dla metryki przy użyciu punktu zaczepienia. Aby uzyskać więcej informacji [, zobacz Konfigurowanie alertów i otrzymywanie powiadomień przy użyciu punktu zaczepienia](alerts.md) .


## <a name="next-steps"></a>Następne kroki 
- [Konfigurowanie alertów i otrzymywanie powiadomień przy użyciu wpięcia](alerts.md)
- [Dostosowywanie wykrywania anomalii przy użyciu opinii](anomaly-feedback.md)
- [Diagnozuj zdarzenie](diagnose-incident.md).

