---
title: Omówienie skoroszytów usługi Azure Monitor
description: Dowiedz się, jak skoroszyty zapewniają elastyczną kanwę do analizy danych i tworzenia zaawansowanych raportów wizualnych w ramach Azure Portal.
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e401eb4a5608db6e5767298f53046099a42679b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100613740"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor skoroszyty

Skoroszyty udostępniają elastyczną kanwę do analizy danych i tworzenia zaawansowanych raportów wizualnych w witrynie Azure Portal. Umożliwiają one naciskanie na wiele źródeł danych z platformy Azure i łączenie ich w ujednolicone interaktywne środowiska. 

Oto przewodnik wideo dotyczący tworzenia skoroszytów.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Źródła danych

Skoroszyty umożliwiają wykonywanie zapytań o dane z wielu źródeł na platformie Azure. Autorzy skoroszytów mogą przekształcać te dane, aby zapewnić wgląd w dostępność, wydajność, użycie i ogólną kondycję składników. Na przykład analizowanie dzienników wydajności z maszyn wirtualnych w celu zidentyfikowania dużych wystąpień procesora lub małej ilości pamięci oraz wyświetlenie wyników jako siatki w raportach interaktywnych.
  
Jednak największą zaletą skoroszytów jest możliwość połączenia danych z różnych źródeł w jednym raporcie. Pozwala to na tworzenie złożonych widoków zasobów lub sprzężeń między zasobami, co umożliwia bogatsze dane i szczegółowe informacje, które w przeciwnym razie byłyby niemożliwe.

Aktualnie skoroszyty są zgodne z następującymi źródłami danych:

* [Dzienniki](../visualize/workbooks-data-sources.md#logs)
* [Metryki](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Alerty (wersja zapoznawcza)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Kondycja obciążenia](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Wizualizacje

Skoroszyty zapewniają bogaty zestaw funkcji wizualizacji danych. Aby uzyskać szczegółowe przykłady poszczególnych typów wizualizacji, możesz skorzystać z przykładowych linków poniżej:

* [Tekst](../visualize/workbooks-text-visualizations.md)
* [Wykresy](../visualize/workbooks-chart-visualizations.md)
* [Siatki](../visualize/workbooks-grid-visualizations.md)
* [Kafelki](../visualize/workbooks-tile-visualizations.md)
* [Drzewo](../visualize/workbooks-tree-visualizations.md)
* [Diagram](../visualize/workbooks-graph-visualizations.md)
* [Słupek złożony](../visualize/workbooks-composite-bar.md)

![Przykładowe wizualizacje skoroszytu](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Wprowadzenie

Aby poznać środowisko skoroszytów, najpierw przejdź do usługi Azure Monitor. Można to zrobić, wpisując **monitor** w polu wyszukiwania w Azure Portal.

Następnie wybierz pozycję **skoroszyty**.

![Zrzut ekranu przedstawiający przyciski skoroszyty wyróżnione czerwoną ramką](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Galeria

Spowoduje to przejście do galerii skoroszytów:

![Zrzut ekranu przedstawiający Widok galerii skoroszytów Azure Monitor](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Skoroszyty a szablony skoroszytu

_Skoroszyt_ można zobaczyć w kolorze zielonym i w różnych _szablonach skoroszytów_ . Szablony służą jako nadzorowane raporty, które są przeznaczone do elastycznego ponownego wykorzystania przez wielu użytkowników i zespoły. Otwarcie szablonu powoduje utworzenie skoroszytu zawierającego zawartość szablonu. 

Można dostosować parametry skoroszytu opartego na szablonach i przeprowadzać analizę bez obaw o rozdzielenie przyszłego środowiska raportowania dla współpracowników. Jeśli otworzysz szablon, wprowadź pewne zmiany, a następnie wybierz ikonę Zapisz, aby zapisać szablon jako skoroszyt, który będzie wyświetlany w kolorze zielonym, pozostawiając oryginalny szablon bez zmian. 

Pod okapem szablony różnią się także od zapisanych skoroszytów. Zapisanie skoroszytu powoduje utworzenie skojarzonego zasobu Azure Resource Manager, podczas gdy po prostu otwierając szablon nie jest skojarzony żaden unikatowy zasób. Aby dowiedzieć się więcej na temat zarządzania kontrolą dostępu w skoroszytach, zobacz [artykuł kontrola dostępu do skoroszytów](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Eksplorowanie szablonu skoroszytu

Wybierz pozycję **Analiza błędów aplikacji** , aby wyświetlić jeden z domyślnych szablonów skoroszytów aplikacji.

![Zrzut ekranu szablonu analizy błędów aplikacji](./media/workbooks-overview/failure-analysis.png)

Jak wspomniano wcześniej, otwarcie szablonu tworzy tymczasowy skoroszyt, aby móc korzystać z programu. Domyślnie skoroszyt jest otwierany w trybie odczytywania, który wyświetla tylko informacje dla zamierzonego środowiska analizy, które zostało utworzone przez oryginalnego autora szablonu.

W przypadku tego konkretnego skoroszytu środowisko pracy jest interaktywne. Można dostosować subskrypcję, aplikacje przeznaczone dla aplikacji i zakres czasu danych, które mają być wyświetlane. Po dokonaniu wyboru siatka żądań HTTP jest również interaktywna, przy czym wybranie pojedynczego wiersza spowoduje zmianę sposobu renderowania danych na dwóch wykresach w dolnej części raportu.

### <a name="editing-mode"></a>Tryb edycji

Aby zrozumieć, jak ten szablon skoroszytu został umieszczony razem, należy przełączyć się do trybu edycji, wybierając pozycję **Edytuj**.

![Zrzut ekranu przycisku Edytuj w skoroszytach.](./media/workbooks-overview/edit.png)

Po przełączeniu do trybu edycji zobaczysz kilka pól **edycji** , które są wyświetlane z prawej strony w każdym z aspektów skoroszytu.

![Zrzut ekranu przedstawiający przycisk Edytuj](./media/workbooks-overview/edit-mode.png)

Jeśli wybierzesz przycisk Edytuj bezpośrednio pod siatką danych żądania, zobaczysz, że ta część naszego skoroszytu składa się z Kusto zapytania dotyczącego danych z zasobu Application Insights.

![Zrzut ekranu bazowego zapytania Kusto](./media/workbooks-overview/kusto.png)

Kliknięcie innych przycisków **edycji** po prawej stronie spowoduje wyświetlenie wielu podstawowych składników, które składają się na skoroszyty, takie jak [pola tekstowe](../visualize/workbooks-text-visualizations.md)oparte na promocji, [elementy interfejsu użytkownika](../visualize/workbooks-parameters.md) i inne [typy wykresów/wizualizacji](#visualizations). 

Eksplorowanie wstępnie skompilowanych szablonów w trybie edycji, a następnie modyfikowanie ich w celu dopasowania do Twoich potrzeb i zapisanie własnego skoroszytu niestandardowego jest doskonałym sposobem na rozpoczęcie uczenia się, co jest możliwe dzięki Azure Monitor skoroszytów.

## <a name="pinning-visualizations"></a>Przypinanie wizualizacji

Kroki dotyczące tekstu, zapytania i metryk w skoroszycie można przypinać przy użyciu przycisku Przypnij dla tych elementów, gdy skoroszyt jest w trybie przypinania lub jeśli Autor skoroszytu włączył ustawienia dla tego elementu, aby ikona pinezki była widoczna. 

Aby uzyskać dostęp do trybu kodu PIN, kliknij przycisk **Edytuj** , aby przejść do trybu edycji, a następnie wybierz niebieską ikonę pinezki na górnym pasku. Po prawej stronie ekranu zostanie wyświetlona ikona numeru PIN odpowiadająca poszczególnym osobom z pola *edycji* .

![Obsługa numeru PIN](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Stan skoroszytu jest zapisywany w momencie wprowadzenia numeru PIN, a przypięte skoroszyty na pulpicie nawigacyjnym nie będą aktualizowane w przypadku zmodyfikowania źródłowego skoroszytu. Aby można było zaktualizować przypięty skoroszyt, należy usunąć i ponownie przypiąć tę część.

## <a name="dashboard-time-ranges"></a>Zakresy czasu pulpitu nawigacyjnego

Przypięte fragmenty zapytania skoroszytu będą respektują zakres czasu pulpitu nawigacyjnego, jeśli przypięty element jest skonfigurowany do używania parametru *zakresu czasu* . Wartość zakresu czasu pulpitu nawigacyjnego będzie używana jako wartość parametru zakres czasu, a wszystkie zmiany zakresu czasu pulpitu nawigacyjnego spowodują aktualizację przypiętego elementu. Jeśli przypięta część używa zakresu czasu pulpitu nawigacyjnego, zobaczysz podtytuł aktualizacji przypiętej części, aby pokazać zakres czasu pulpitu nawigacyjnego za każdym razem, gdy zmieni się zakres czasu. 

Ponadto przypięte fragmenty skoroszytu korzystające z parametru zakresu czasu będą odświeżane w ramach częstotliwości ustalonej przez zakres czasu pulpitu nawigacyjnego. Czas ostatniego uruchomienia zapytania będzie wyświetlany na podtytuł części przypiętej.

Jeśli przypięty krok ma jawnie ustawiony zakres czasu (nie używa parametru zakresu czasu), ten zakres czasu będzie zawsze używany dla pulpitu nawigacyjnego, niezależnie od ustawień pulpitu nawigacyjnego. Podtytuł przypiętej części nie będzie pokazywał zakresu czasu pulpitu nawigacyjnego, a zapytanie nie będzie odświeżane na pulpicie nawigacyjnym. Podtytuł będzie widoczny podczas ostatniego wykonywania zapytania.

> [!NOTE]
> Zapytania korzystające ze źródła danych *scalania* nie są obecnie obsługiwane w przypadku przypinania do pulpitów nawigacyjnych.

## <a name="sharing-workbook-templates"></a>Udostępnianie szablonów skoroszytów

Po rozpoczęciu tworzenia własnych szablonów skoroszytów warto udostępnić je szerszej społeczności. Aby dowiedzieć się więcej i zapoznać się z innymi szablonami, które nie są częścią domyślnego widoku Galerii Azure Monitor, odwiedź nasze [repozytorium GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Aby przeglądać istniejące skoroszyty, odwiedź [bibliotekę skoroszytów](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) w witrynie GitHub.

## <a name="next-step"></a>Następny krok

* [Rozpocznij](#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](../visualize/workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.