---
title: Tworzenie widoków w celu analizowania danych dziennika w Azure Monitor | Microsoft Docs
description: Za pomocą projektanta widoków w Azure Monitor można utworzyć niestandardowe widoki, które są wyświetlane w Azure Portal i zawierać różne wizualizacje danych w obszarze roboczym Log Analytics. Ten artykuł zawiera omówienie projektanta widoków i prezentuje procedury tworzenia i edytowania widoków niestandardowych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: f3ae081c8657a3ee6a27b0f9208c3eb4e4e745cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759737"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Tworzenie widoków niestandardowych za pomocą projektanta widoków w Azure Monitor
Za pomocą projektanta widoków w Azure Monitor można utworzyć różne widoki niestandardowe w Azure Portal, które mogą ułatwić wizualizację danych w obszarze roboczym Log Analytics. W tym artykule przedstawiono omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.

> [!IMPORTANT]
> Widoki w Azure Monitor zostały przeniesione do [skoroszytów](workbooks-overview.md) , które zapewniają dodatkową funkcjonalność. Aby uzyskać szczegółowe informacje na temat konwertowania istniejących widoków do skoroszytów, zobacz [Azure monitor View Designer to skoroszyts Guide](view-designer-conversion-overview.md) .
 


Aby uzyskać więcej informacji na temat projektanta widoków, zobacz:

* [Odwołanie do kafelka](view-designer-tiles.md): zawiera Przewodnik referencyjny dotyczący ustawień dla każdego z dostępnych kafelków w widokach niestandardowych.
* [Dokumentacja części wizualizacji](view-designer-parts.md): zawiera Przewodnik referencyjny dotyczący ustawień części wizualizacji, które są dostępne w niestandardowych widokach.


## <a name="concepts"></a>Pojęcia
Widoki są wyświetlane na stronie **przegląd** Azure Monitor w Azure Portal. Otwórz Tę stronę z menu **Azure monitor** , klikając pozycję **więcej** w sekcji **szczegółowe informacje** . Kafelki w poszczególnych widokach niestandardowych są wyświetlane alfabetycznie, a kafelki rozwiązań do monitorowania są zainstalowane w tym samym obszarze roboczym.

![Strona przeglądu](media/view-designer/overview-page.png)

Widoki tworzone za pomocą projektanta widoków zawierają elementy, które są opisane w poniższej tabeli:

| Część | Opis |
|:--- |:--- |
| Kafelki | Są wyświetlane na stronie **przegląd** Azure monitor. Każdy kafelek zawiera podsumowanie wizualizacji widoku niestandardowego, który reprezentuje. Każdy typ kafelków zawiera inną wizualizację rekordów. Wybierz kafelek, aby wyświetlić widok niestandardowy. |
| Widok niestandardowy | Wyświetlane po wybraniu kafelka. Każdy widok zawiera jedną lub więcej części wizualizacji. |
| Części wizualizacji | Prezentowanie danych w obszarze roboczym Log Analytics w oparciu o co najmniej jedną [kwerendę dzienników](../log-query/log-query-overview.md). Większość części zawiera nagłówek, który zawiera wizualizację wysokiego poziomu i listę, która wyświetla najważniejsze wyniki. Każdy typ części zawiera inną wizualizację rekordów w obszarze roboczym Log Analytics. Wybierz elementy w części, aby wykonać zapytanie dziennika, które zawiera szczegółowe rekordy. |

## <a name="required-permissions"></a>Wymagane uprawnienia
Musisz mieć co najmniej [uprawnienia poziomu współautor](manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, aby tworzyć lub modyfikować widoki. Jeśli nie masz tego uprawnienia, opcja Projektant widoków nie zostanie wyświetlona w menu.


## <a name="work-with-an-existing-view"></a>Pracuj z istniejącym widokiem
Widoki, które zostały utworzone za pomocą projektanta widoków, wyświetlają następujące opcje:

![Menu przegląd](media/view-designer/overview-menu.png)

Opcje są opisane w poniższej tabeli:

| Opcja | Opis |
|:--|:--|
| Odśwież   | Odświeża widok przy użyciu najnowszych danych. | 
| Dzienniki      | Otwiera [log Analytics](../log-query/log-query-overview.md) , aby analizować dane przy użyciu zapytań dzienników. |
| Edytuj       | Otwiera widok w projektancie widoków, aby edytować jego zawartość i konfigurację.  |
| Klonowanie      | Tworzy nowy widok i otwiera go w projektancie widoków. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z dołączoną do niej *kopią* . |
| Zakres dat | Ustaw filtr zakresu daty i godziny dla danych uwzględnionych w widoku. Ten zakres dat jest stosowany przed wszystkimi zakresami dat ustawionymi w zapytaniach w widoku.  |
| +          | Zdefiniuj filtr niestandardowy, który jest zdefiniowany dla widoku. |


## <a name="create-a-new-view"></a>Utwórz nowy widok
Nowy widok można utworzyć w projektancie widoku, wybierając pozycję **Widok projektanta** w menu obszaru roboczego log Analytics.

![Kafelek projektanta widoków](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Pracuj z projektantem widoku
Za pomocą projektanta widoków można tworzyć nowe widoki lub edytować istniejące. 

Projektant widoków ma trzy okienka: 
* **Projekt**: zawiera niestandardowy widok, który tworzysz lub edytujesz. 
* **Kontrolki**: zawiera kafelki i części dodawane do okienka **projektowania** . 
* **Właściwości**: Wyświetla właściwości kafelków lub wybranych części.

![Projektant widoków](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurowanie kafelka widoku
Widok niestandardowy może mieć tylko jeden kafelek. Aby wyświetlić bieżący kafelek lub wybrać alternatywę, wybierz kartę **kafelek** w okienku **sterowania** . W okienku **Właściwości** zostaną wyświetlone właściwości bieżącego kafelka. 

Właściwości kafelka można skonfigurować zgodnie z informacjami w [Kompendium kafelka](view-designer-tiles.md) , a następnie kliknąć przycisk **Zastosuj** , aby zapisać zmiany.

### <a name="configure-the-visualization-parts"></a>Konfigurowanie części wizualizacji
Widok może zawierać dowolną liczbę części wizualizacji. Aby dodać części do widoku, wybierz kartę **Widok** , a następnie wybierz część wizualizacji. W okienku **Właściwości** zostaną wyświetlone właściwości wybranej części. 

Można skonfigurować właściwości widoku zgodnie z informacjami w [Kompendium części wizualizacji](view-designer-parts.md) , a następnie kliknąć przycisk **Zastosuj** , aby zapisać zmiany.

Widoki mają tylko jeden wiersz części wizualizacji. Istniejące części można zmienić, przeciągając je do nowej lokalizacji.

Możesz usunąć część wizualizacji z widoku, wybierając **symbol X** w prawym górnym rogu części.


### <a name="menu-options"></a>Opcje menu
W poniższej tabeli opisano opcje pracy z widokami w trybie edycji.

![Menu Edycja](media/view-designer/edit-menu.png)

| Opcja | Opis |
|:--|:--|
| Zapisz        | Zapisuje zmiany i zamyka widok. |
| Anuluj      | Odrzuca zmiany i zamyka widok. |
| Usuń widok | Usuwa widok. |
| Eksportowanie      | Eksportuje widok do [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) , który można zaimportować do innego obszaru roboczego. Nazwa pliku to nazwa widoku, która ma rozszerzenie *omsview* . |
| Import      | Importuje plik *omsview* wyeksportowany z innego obszaru roboczego. Ta akcja zastępuje konfigurację istniejącego widoku. |
| Klonowanie       | Tworzy nowy widok i otwiera go w projektancie widoków. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z dołączoną do niej *kopią* . |

## <a name="next-steps"></a>Następne kroki
* Dodaj [kafelki](view-designer-tiles.md) do widoku niestandardowego.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
