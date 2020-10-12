---
title: Zapisane zapytania w Azure Monitor Log Analytics
description: Zapytania, które można uruchomić z i zmodyfikować w razie potrzeby
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85959846"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Zapisane zapytania w Azure Monitor Log Analytics

Log Analytics oferuje zestawy przykładowych zapytań, które można uruchomić samodzielnie lub użyć jako punktu wyjścia dla własnych zapytań. W tym artykule opisano przykładowe zapytania i sposoby ich używania.

Jeśli nie znasz Log Analytics lub języka zapytań KQL, przykładowe zapytania są znakomitym sposobem na rozpoczęcie pracy. Mogą one zapewnić błyskawiczny wgląd w zasoby i zapewnić świetny sposób rozpoczęcia uczenia się i korzystania z KQL, co skraca czas potrzebny na rozpoczęcie korzystania z Log Analytics. Zostały zebrane i nadzorowane przez 250 przykładowych zapytań zaprojektowanych w celu zapewnienia błyskawicznej wartości, a liczba przykładowych zapytań stale rośnie.

## <a name="in-context-queries"></a>Zapytania w kontekście

Nowe środowisko obsługuje filtry i sugeruje zapytania w kontekście. Innymi słowy system automatycznie wyświetla tylko zapytania dotyczące wybranego zakresu.

- Dla **pojedynczego zasobu** — zapytania są filtrowane według typu zasobu.
- W przypadku **grupy zasobów** zapytania są filtrowane zgodnie z zasobami w określonej grupie zasobów.
- W przypadku **obszaru roboczego** — zapytania są filtrowane zgodnie z rozwiązaniami zainstalowanymi w obszarze roboczym.

To zachowanie jest spójne dla wszystkich zakresów Log Analytics. Jeśli nie widzisz przykładowego zapytania dla danego typu zasobu, może to być spowodowane filtrami ze względu na kontekst. W dalszej części opisano sposób usuwania zakresu kontekstowego, aby można było wyświetlić wszystkie możliwe zapytania.

> [!TIP]
> Im więcej zasobów znajduje się w zakresie, tym dłużej będzie przefiltrować Portal i wyświetlić okno dialogowe przykładowe zapytanie.

## <a name="example-query-user-interface"></a>Przykładowy interfejs użytkownika zapytania

Możesz na przykład uzyskać przykładowe zapytania z dwóch różnych lokalizacji.

### <a name="example-query-dialog"></a>Przykładowe okno dialogowe zapytania

Po wprowadzeniu pierwszego środowiska Log Analytics wyświetlane jest *okno dialogowe przykładowe zapytania* .  Można też uzyskać do niego dostęp, klikając w prawym górnym rogu ekranu na **przykład zapytań**.

![—](media/saved-queries/sidebar-2.png)

Zostanie wyświetlone Przykładowe okno dialogowe zapytania, jak pokazano poniżej:  

![Ekran przykładowych zapytań](media/saved-queries/example-query-start.png)

Poprzedni zrzut ekranu wyświetla ekran dzienników dla wystąpienia Azure Key Vault. Jak wspomniano wcześniej w tym artykule, zapytania są wyświetlane w kontekście.  W związku z tym zrzut ekranu pokazuje tylko Key Vault powiązane przykłady. W przypadku wybrania całej subskrypcji zostaną wyświetlone zapytania dotyczące wszystkich typów zasobów w danej subskrypcji.  

Każde przykładowe zapytanie jest reprezentowane przez kartę. Możesz szybko skanować zapytania, aby znaleźć to, czego potrzebujesz. Zapytanie można uruchomić bezpośrednio z okna dialogowego lub załadować je do edytora zapytań w celu uzyskania dodatkowych dostosowań i dostosowywania.

### <a name="sidebar-query-experience"></a>Środowisko zapytań paska bocznego

Dostęp do tych samych funkcji środowiska dialogu można uzyskać z okienka zapytania na pasku bocznym po lewej stronie Log Analytics. Możesz umieścić kursor na nazwie zapytania, aby uzyskać opis zapytania i dodatkowe funkcje.

![—](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Znajdowanie i filtrowanie zapytań

Opcje w tej sekcji są dostępne zarówno w przypadku zapytań okna dialogowego, jak i paska bocznego, ale z nieco innym interfejsem użytkownika.  

### <a name="use-favorites"></a>Użyj ulubionych

Możesz przydzielić często używane zapytania, aby uzyskać szybszy dostęp.

> [!TIP]
> Późniejsze zbieranie i wyświetlanie zapytań jest dobrym sposobem na rozpoczęcie pracy. Znajdź potrzebne zapytania i kliknij gwiazdkę obok zapytania, aby dodać je do ulubionych. Jeśli później okaże się, że zapytanie nie jest przydatne, możesz je wycofać.  

### <a name="filtering-and-group-by"></a>Filtrowanie i grupowanie według

Podczas gdy okno dialogowe zapytania zawiera pliki do wyświetlania tylko zapytań z kontekstem prawnym, można usunąć filtr i zobaczyć wszystkie zapytania.

### <a name="group-by"></a>Grupuj według

Aby zmienić grupowanie zapytań, kliknij listę rozwijaną *Grupuj według* :

![Przykładowy ekran zapytań — GroupBy](media/saved-queries/example-query-groupby.png)

Okno dialogowe obsługuje grupowanie według:

- **Typ zasobu** — zasób zdefiniowany na platformie Azure, na przykład maszynę wirtualną. Zapoznaj się z [Azure Monitorą tabeli](/azure/azure-monitor/reference/tables/tables-resourcetype) , aby uzyskać pełne mapowanie tabel Azure monitor dzienników/log Analytics na typ zasobu.  
- **Category** — typ informacji, takich jak *zabezpieczenia* czy *Inspekcja*. Kategorie są identyczne z kategoriami zdefiniowanymi w okienku po stronie tabele. Aby uzyskać pełną listę kategorii, zobacz [odwołanie do tabeli Azure monitor](/azure/azure-monitor/reference/tables/tables-category) .  
- **Rozwiązanie** — rozwiązanie Azure monitor skojarzone z zapytaniami
- **Temat** — temat przykładowego zapytania, na przykład *dzienniki aktywności* lub *Dzienniki aplikacji*. Właściwość tematu jest unikatowa dla przykładowych zapytań i może się różnić w zależności od określonego typu zasobu.

Wartości grupowania również działają jako aktywny Spis treści. Kliknięcie jednej z wartości po lewej stronie ekranu powoduje przewinięcie widoku zapytania bezpośrednio do klikniętego elementu.

### <a name="filter"></a>Filtrowanie

Można również filtrować zapytania zgodnie z wymienionymi wcześniej wartościami GroupBy. W oknie dialogowym przykładowego zapytania filtry znajdują się u góry.

![Przykładowy filtr ekranu zapytań](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Łączenie grup według i filtrowanie

Funkcja Filter i Group by została zaprojektowana tak, aby działała wspólnie. Zapewniają one elastyczność w zakresie uporządkowania zapytań. Na przykład jeśli używasz grupy zasobów z wieloma zasobami, możesz filtrować w dół do określonego typu zasobu i rozmieścić wyniki zapytania według tematu.

## <a name="sample-query-dialog-appearance-behavior"></a>Zachowanie wyświetlania przykładowego okna dialogowego zapytania

Jeśli jesteś KQL Pro i wolisz bezpośrednio korzystać z edytora zapytań, możesz przełączać okno dialogowe zapytania "wyłączone". Po wyłączeniu tego okna okno dialogowe kwerendy nie jest ładowane w przypadku Log Analytics ładowania ekranu.

![Przykłady wyłączone](media/saved-queries/examples-on-off.png)

Możesz zawsze uzyskać dostęp do przykładowego okna podręcznego zapytania z *przykładowego przycisku zapytania* na górnym pasku log Analytics.

## <a name="query-explorer"></a>Eksplorator zapytań

Eksplorator zapytań w celu zapisywania i udostępniania zapytań generowanych przez użytkownika pozostaje niezmieniony przez czas.

## <a name="next-steps"></a>Następne kroki

[Wprowadzenie do zapytań KQL](get-started-queries.md)

