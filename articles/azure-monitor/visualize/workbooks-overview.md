---
title: Omówienie skoroszytów usługi Azure Monitor
description: Dowiedz się, jak skoroszyty zapewniają elastyczną kanwę do analizy danych i tworzenia rozbudowanych raportów wizualnych w Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482776"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor skoroszytów

Skoroszyty udostępniają elastyczną kanwę do analizy danych i tworzenia zaawansowanych raportów wizualnych w witrynie Azure Portal. Umożliwiają one korzystanie z wielu źródeł danych z całej platformy Azure i łączenie ich w ujednolicone, interaktywne środowisko.

Oto przewodnik wideo na temat tworzenia skoroszytów.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Źródła danych

Skoroszyty umożliwiają wykonywanie zapytań o dane z wielu źródeł na platformie Azure. Autorzy skoroszytów mogą przekształcać te dane, aby zapewnić wgląd w dostępność, wydajność, użycie i ogólną kondycję składników. Na przykład analizowanie dzienników wydajności z maszyn wirtualnych w celu zidentyfikowania wystąpień wysokiego użycia procesora CPU lub małej ilości pamięci i wyświetlenie wyników jako siatki w interaktywnym raporcie.
  
Jednak największą zaletą skoroszytów jest możliwość połączenia danych z różnych źródeł w jednym raporcie. Umożliwia to tworzenie złożonych widoków zasobów lub sprzężenia między zasobami, co zapewnia bardziej rozbudowane dane i szczegółowe informacje, które w przeciwnym razie byłyby niemożliwe.

Aktualnie skoroszyty są zgodne z następującymi źródłami danych:

* [Dzienniki](../visualize/workbooks-data-sources.md#logs)
* [Metryki](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Alerty (wersja zapoznawcza)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Kondycja obciążenia](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Wizualizacje

Skoroszyty zapewniają bogaty zestaw możliwości wizualizacji danych. Aby uzyskać szczegółowe przykłady poszczególnych typów wizualizacji, możesz skorzystać z poniższych linków:

* [Tekst](../visualize/workbooks-text-visualizations.md)
* [Wykresy](../visualize/workbooks-chart-visualizations.md)
* [Siatki](../visualize/workbooks-grid-visualizations.md)
* [Kafelki](../visualize/workbooks-tile-visualizations.md)
* [Drzew](../visualize/workbooks-tree-visualizations.md)
* [Wykresy](../visualize/workbooks-graph-visualizations.md)
* [Słupek złożony](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Przykład wizualizacji skoroszytu." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Przypinanie wizualizacji

Kroki dotyczące tekstu, zapytania i metryk w skoroszycie można przypiąć przy użyciu przycisku przypinania dla tych elementów, gdy skoroszyt jest w trybie przypinania lub jeśli autor skoroszytu włączył dla tego elementu ustawienia, aby ikona pinezki była widoczna.

Aby uzyskać dostęp do trybu przypinania, kliknij pozycję **Edytuj,** aby wprowadzić tryb edycji, a następnie wybierz niebieską ikonę pinezki na górnym pasku. Ikona indywidualnego przypinania pojawi się nad  polem Edycja odpowiedniej części skoroszytu po prawej stronie ekranu.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Zrzut ekranu przedstawiający środowisko przypinania." border="false":::

> [!NOTE]
> Stan skoroszytu jest zapisywany w momencie przypinania, a przypięte skoroszyty na pulpicie nawigacyjnym nie będą aktualizowane, jeśli źródłowy skoroszyt zostanie zmodyfikowany. Aby zaktualizować przypiętą część skoroszytu, należy ją usunąć i ponownie przypiąć.

## <a name="getting-started"></a>Wprowadzenie

Aby poznać środowisko skoroszytów, najpierw przejdź do Azure Monitor usługi. Można to zrobić, wpisując **monitor w** polu wyszukiwania w Azure Portal.

Następnie wybierz **pozycję Skoroszyty.**

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Zrzut ekranu przedstawiający przycisk Skoroszyty wyróżniony czerwonym polem." border="false":::

### <a name="gallery"></a>Galeria

Galeria ułatwia organizowanie i sortowanie skoroszytów wszystkich typów oraz zarządzanie nimi.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Zrzut ekranu przedstawiający galerię na karcie Wszystkie." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Karty galerii

Galeria zawiera cztery karty, które ułatwiają organizowanie typów skoroszytów.

| Tab              | Opis                                       |
|------------------|---------------------------------------------------|
| Wszystko | Przedstawia cztery najważniejsze elementy dla każdego typu — skoroszyty, szablony publiczne i moje szablony. Skoroszyty są sortowane według daty modyfikacji, więc zobaczysz osiem najnowszych zmodyfikowanych skoroszytów.|
| Skoroszyty | Przedstawia listę wszystkich dostępnych skoroszytów, które zostały Ci utworzone lub udostępnione. |
| Szablony publiczne | Przedstawia listę wszystkich dostępnych, gotowych do użycia szablonów skoroszytów funkcjonalnych rozpoczynania pracy opublikowanych przez firmę Microsoft. Pogrupowane według kategorii. |
| Moje szablony | Przedstawia listę wszystkich dostępnych szablonów wdrożonych skoroszytów, które zostały utworzone lub zostały Ci udostępnione. Pogrupowane według kategorii. |

#### <a name="features"></a>Funkcje

* Na każdej karcie znajduje się siatka z informacjami o skoroszytach. Zawiera opis, datę ostatniej modyfikacji, tagi, subskrypcję, grupę zasobów, region i stan udostępniony. Możesz również posortować skoroszyty według tych informacji.
* Filtruj według grupy zasobów, subskrypcji, nazwy skoroszytu/szablonu lub kategorii szablonu.
* Wybierz wiele skoroszytów do usunięcia lub zbiorczego usunięcia.
* Każdy skoroszyt ma menu kontekstowe (wielokropek/trzy kropki na końcu). Wybranie go spowoduje otwarcie listy szybkich akcji.
    * Wyświetl zasób — dostęp do karty zasobów skoroszytu, aby wyświetlić identyfikator zasobu skoroszytu, dodać tagi, zarządzać blokadami itp.
    * Usuń skoroszyt lub zmień jego nazwę.
    * Przypnij skoroszyt do pulpitu nawigacyjnego.

### <a name="workbooks-versus-workbook-templates"></a>Skoroszyty a szablony skoroszytów

Skoroszyt jest wyświetlony w _kolorze_ zielonym, a kilka szablonów _skoroszytów jest_ purpurowych. Szablony pełnią funkcję raportów z możliwością elastycznego ponownego użycia przez wielu użytkowników i wiele zespołów. Otwarcie szablonu powoduje utworzenie skoroszytu przejściowego wypełnionego zawartością szablonu.

Możesz dostosować parametry skoroszytu opartego na szablonach i przeprowadzić analizę bez obaw o przerwanie przyszłego raportowania dla współpracowników. Jeśli otworzysz szablon, dokonasz pewnych korekt, a następnie wybierzesz ikonę zapisywania, którą zapiszesz jako skoroszyt, który będzie następnie pokazywany na zielono, pozostawiając oryginalny szablon bez zmian.

Pod maską szablony różnią się również od zapisanych skoroszytów. Zapisanie skoroszytu powoduje utworzenie skojarzonego Azure Resource Manager zasobów, natomiast skoroszyt przejściowy utworzony podczas otwierania szablonu nie ma skojarzonego unikatowego zasobu. Aby dowiedzieć się więcej na temat zarządzania kontrolą dostępu w skoroszytach, zapoznaj się z artykułem [dotyczącym kontroli dostępu do skoroszytów.](../visualize/workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Eksplorowanie szablonu skoroszytu

Wybierz **pozycję Analiza błędów aplikacji,** aby wyświetlić jeden z domyślnych szablonów skoroszytów aplikacji.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Zrzut ekranu przedstawiający szablon analizy błędów aplikacji." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Jak wspomniano wcześniej, otwarcie szablonu powoduje utworzenie tymczasowego skoroszytu, z który będzie można wchodzić w interakcje. Domyślnie skoroszyt jest otwierany w trybie odczytu, który wyświetla tylko informacje dotyczące zamierzonego działania analizy utworzonego przez oryginalnego autora szablonu.

W przypadku tego konkretnego skoroszytu środowisko jest interaktywne. Możesz dostosować subskrypcję, aplikacje docelowe i zakres czasu danych, które chcesz wyświetlić. Po wybraniu tych opcji siatka żądań HTTP jest również interaktywna, dzięki której wybranie pojedynczego wiersza spowoduje zmianę danych renderowanych na dwóch wykresach w dolnej części raportu.

### <a name="editing-mode"></a>Tryb edycji

Aby zrozumieć, jak ten szablon skoroszytu jest ze sobą kładący, musisz zamienić się na tryb edycji, wybierając **pozycję Edytuj**.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Zrzut ekranu przedstawiający przycisk Edytuj w skoroszytach." border="false" :::

Po przełączeniu do trybu edycji po prawej  stronie zostanie wyświetlonych wiele pól Edycja odpowiadających poszczególnym aspektom skoroszytu.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Zrzut ekranu przedstawiający przycisk Edytuj." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Jeśli wybierzemy przycisk edycji znajdujący się bezpośrednio pod siatką danych żądania, możemy zobaczyć, że ta część naszego skoroszytu składa się z zapytania Kusto względem danych z Application Insights zasobów.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Zrzut ekranu przedstawiający bazowe zapytanie Kusto." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Wybranie innych **przycisków** Edytuj po prawej stronie spowoduje ujawnienie kilku podstawowych składników, które zawierają [](../visualize/workbooks-parameters.md) skoroszyty, takich jak pola tekstowe oparte na znacznikach markdown, [](../visualize/workbooks-text-visualizations.md)elementy interfejsu użytkownika wyboru parametrów i inne typy [wykresów/wizualizacji.](#visualizations)

Eksplorowanie wstępnie sbudowaną szablonów w trybie edycji, a następnie modyfikowanie ich zgodnie z potrzebami i zapisywanie własnego skoroszytu niestandardowego to doskonały sposób, aby dowiedzieć się, co jest możliwe w przypadku Azure Monitor skoroszytów.

## <a name="dashboard-time-ranges"></a>Zakresy czasu pulpitu nawigacyjnego

Przypięte części zapytania skoroszytu będą przestrzegać zakresu czasu pulpitu nawigacyjnego, jeśli przypięty element jest skonfigurowany do używania *parametru zakresu* czasu. Wartość zakresu czasu pulpitu nawigacyjnego będzie używana jako wartość parametru zakresu czasu, a każda zmiana zakresu czasu pulpitu nawigacyjnego spowoduje zaktualizowanie przypiętego elementu. Jeśli przypięta część korzysta z zakresu czasu pulpitu nawigacyjnego, zobaczysz podtytuł aktualizacji przypiętej części, aby wyświetlić zakres czasu pulpitu nawigacyjnego przy każdej zmianie zakresu czasu.

Ponadto przypięte części skoroszytu przy użyciu parametru zakresu czasu będą automatycznie odświeżane z częstotliwością określaną przez zakres czasu pulpitu nawigacyjnego. Czas ostatniego wykonywania zapytania pojawi się w podtytułze przypiętej części.

Jeśli przypięty krok ma jawnie ustawiony zakres czasu (nie używa parametru zakresu czasu), ten zakres czasu będzie zawsze używany dla pulpitu nawigacyjnego, niezależnie od ustawień pulpitu nawigacyjnego. Podtytuł przypiętej części nie będzie pokazywać zakresu czasu pulpitu nawigacyjnego, a zapytanie nie będzie automatycznie odświeżane na pulpicie nawigacyjnym. Podtytuł będzie pokazywać czas ostatniego wykonania zapytania.

> [!NOTE]
> Zapytania korzystające *ze źródła danych scalania* nie są obecnie obsługiwane podczas przypinania do pulpitów nawigacyjnych.

## <a name="sharing-workbook-templates"></a>Udostępnianie szablonów skoroszytów

Po rozpoczęciu tworzenia własnych szablonów skoroszytów warto udostępnić je szerszej społeczności. Aby dowiedzieć się więcej i poznać inne szablony, które nie są częścią domyślnego widoku galerii Azure Monitor, odwiedź nasze [repozytorium GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Aby przeglądać istniejące skoroszyty, odwiedź [bibliotekę skoroszytów w](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) witrynie GitHub.

## <a name="next-step"></a>Następny krok

* [Dowiedz się](#visualizations) więcej na temat skoroszytów z wieloma opcjami rozbudowanych wizualizacji.
* [Kontrolowanie](../visualize/workbooks-access-control.md) i udostępnianie dostępu do zasobów skoroszytu.