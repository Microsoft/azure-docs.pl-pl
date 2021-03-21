---
title: Azure Monitor tworzenia parametrów w skoroszytach
description: Dowiedz się, jak parametry umożliwiają autorom skoroszytu zbieranie danych wejściowych od odbiorców i odwoływanie się do nich w innych częściach skoroszytu.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700562"
---
# <a name="workbook-parameters"></a>Parametry skoroszytu

Parametry Zezwalaj autorom skoroszytu na zbieranie danych wejściowych od odbiorców i odwoływanie się do nich w innych częściach skoroszytu — zwykle do określania zakresu zestawu wyników lub ustawiania właściwej wizualizacji. Jest to kluczowa funkcja, która umożliwia autorom tworzenie interaktywnych raportów i środowisk. 

Skoroszyty umożliwiają kontrolowanie sposobu prezentowania kontrolek parametrów dla odbiorców — pola tekstowego a listy rozwijanej, pojedynczej lub wieloskładnikowej, wartości z tekstu, JSON, KQL lub Azure Resource Graph itd.  

Obsługiwane są następujące typy parametrów:
* [Time](workbooks-time.md) — umożliwia użytkownikowi wybranie z wstępnie wypełnionych zakresów czasu lub wybranie niestandardowego zakresu
* [Lista rozwijana](workbooks-dropdowns.md) — umożliwia użytkownikowi wybranie wartości lub zestawu wartości.
* [Tekst](workbooks-text.md) — umożliwia użytkownikowi wprowadzanie dowolnego tekstu
* [Zasób](workbooks-resources.md) — umożliwia użytkownikowi wybranie jednego lub większej liczby zasobów platformy Azure
* [Subskrypcja](workbooks-resources.md) — umożliwia użytkownikowi wybranie co najmniej jednego zasobu subskrypcji platformy Azure
* Typ zasobu — umożliwia użytkownikowi wybranie co najmniej jednej wartości typu zasobów platformy Azure
* Lokalizacja — umożliwia użytkownikowi wybranie co najmniej jednej wartości lokalizacji platformy Azure

Te wartości parametrów mogą być przywoływane w innych częściach skoroszytów za pośrednictwem powiązań lub rozszerzeń wartości.

## <a name="creating-a-parameter"></a>Tworzenie parametru
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `TimeRange` *(należy zauważyć, że __nazwy__ parametrów **nie mogą** zawierać spacji ani znaków specjalnych)*
    2. Nazwa wyświetlana: `Time Range` *(jednak __nazwy wyświetlane__ mogą zawierać spacje, znaki specjalne, emoji itp.)*  
    2. Typ parametru: `Time range picker`
    3. Wymagane: `checked`
    4. Dostępne zakresy czasu: Ostatnia godzina, ostatnie 12 godzin, ostatnie 24 godziny, ostatnie 48 godzin, ostatnie 3 dni, ostatnie 7 dni i Zezwalaj na wybór niestandardowego zakresu czasu
5. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

   ![Obraz przedstawiający tworzenie parametru zakresu czasu](./media/workbooks-parameters/time-settings.png)

W ten sposób skoroszyt będzie wyglądał jak w trybie odczytu, w stylu "Pills".

   ![Obraz przedstawiający parametr zakresu czasu w trybie odczytu](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Odwoływanie się do parametru
### <a name="via-bindings"></a>Za pośrednictwem powiązań
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. Otwórz listę rozwijaną _zakres czasu_ i wybierz `Time Range` opcję z sekcji Parametry u dołu.
3. To wiąże parametr zakresu czasu z zakresem czasu wykresu. Zakres czasu zapytania przykładowego to teraz ostatnie 24 godziny.
4. Uruchom zapytanie, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr zakresu czasu przywoływany przez powiązania](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>W KQL
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. W KQL Wprowadź filtr zakresu czasu przy użyciu parametru: `| where timestamp {TimeRange}`
3. Spowoduje to rozwinięcie czasu oceny zapytania do `| where timestamp > ago(1d)` , który jest wartością zakresu czasu parametru.
4. Uruchom zapytanie, aby zobaczyć wyniki

    ![Obraz przedstawiający zakres czasu przywoływany w KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>W tekście 
1. Dodaj kontrolkę tekstową do skoroszytu.
2. W obszarze promocji wprowadź `The chosen time range is {TimeRange:label}`
3. Wybierz _gotowe do edycji_
4. Kontrolka Text wyświetli tekst: _wybrany zakres czasu to ostatnie 24 godziny_

## <a name="parameter-options"></a>Opcje parametrów
_W sekcji tekstowej_ użyto `label` parametru zamiast jego wartości. Parametry ujawniają różne opcje w zależności od typu, np. selektory zakresu czasu umożliwiają wartości, etykiety, zapytania, początku, końca i ziarna.

Użyj `Previews` sekcji okienka _Edytowanie parametru_ , aby wyświetlić opcje rozwinięcia dla parametru:

![Obraz przedstawiający opcje parametrów zakresu czasu](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.