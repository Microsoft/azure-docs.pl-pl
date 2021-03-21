---
title: Korzystanie z zakładek łowiectwa do badań danych na platformie Azure — wskaźnik
description: W tym artykule opisano, jak używać zakładek polowania na platformie Azure do śledzenia danych.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94651316"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Keep track of data during hunting with Azure Sentinel (Śledzenie danych podczas wykrywania zagrożeń przy użyciu rozwiązania Azure Sentinel)

Polowanie zagrożeń zwykle wymaga przejrzenia górach danych dzienników szukających dowodu złośliwego zachowania. W trakcie tego procesu badacze znajdą zdarzenia, które chcą zapamiętać, odwiedzać i przeanalizować w ramach walidacji potencjalnych postanowień, a także zrozumieć pełną historię naruszenia.

Zakładki polowania w usłudze Azure badającej pomoc można wykonać, zachowując zapytania wykonane w **dziennikach badania platformy Azure**, a także wyniki zapytania, które są odpowiednie. Możesz również rejestrować uwagi kontekstowe i odwoływać się do wyników, dodając notatki i tagi. Dane oznaczone zakładką są widoczne dla Ciebie i członków zespołu w celu ułatwienia współpracy.

W dowolnym momencie możesz ponownie odwiedzić dane z zakładkami na karcie **zakładki** okienka **polowania** . Możesz użyć opcji filtrowania i wyszukiwania, aby szybko znaleźć konkretne dane dla bieżącego badania. Alternatywnie możesz wyświetlać dane z zakładek bezpośrednio w tabeli **HuntingBookmark** w obszarze roboczym log Analytics. Na przykład:

> [!div class="mx-imgBorder"]
> ![Wyświetl tabelę HuntingBookmark](./media/bookmarks/bookmark-table.png)

Wyświetlanie zakładek z tabeli pozwala filtrować, podsumowywać i łączyć dane z zakładkami z innymi źródłami danych, ułatwiając wyszukiwanie corroborating.

Obecnie w wersji zapoznawczej, jeśli okaże się, że w przypadku niektórych problemów należy rozwiązać problem w dziennikach, w kilku kliknięciach można utworzyć zakładkę i podwyższyć poziom do zdarzenia lub dodać zakładkę do istniejącego zdarzenia. Aby uzyskać więcej informacji na temat zdarzeń, zobacz [Samouczek: badanie zdarzeń za pomocą platformy Azure — wskaźnik](tutorial-investigate-cases.md). 

Ponadto w wersji zapoznawczej można wizualizować dane oznaczone zakładką, klikając polecenie **Zbadaj** w obszarze Szczegóły zakładki. Spowoduje to uruchomienie środowiska dochodzeniowego, w którym można wyświetlać, badać i wizualnie komunikować swoje wyniki przy użyciu interaktywnego diagramu i osi czasu grafu obiektów.

## <a name="add-a-bookmark"></a>Dodawanie zakładki

1. W Azure Portal przejdź do   >  polowania do **zarządzania zagrożeniami** wskaźnikiem,  >   aby uruchomić zapytania dotyczące podejrzanego i nietypowego zachowania.

2. Wybierz jedną z zapytań polowania i po prawej stronie, w szczegółach zapytania polowania wybierz pozycję **Uruchom zapytanie**. 

3. Wybierz pozycję **Wyświetl wyniki zapytania**. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Wyświetl wyniki zapytania z polowania wskaźnikowego platformy Azure](./media/bookmarks/new-processes-observed-example.png)
    
    Ta akcja powoduje otwarcie wyników zapytania w okienku **dzienniki** .

4. Z listy wyników zapytania dziennika Użyj pól wyboru, aby wybrać co najmniej jeden wiersz, w którym znajdują się interesujące informacje.

5. Wybierz pozycję **Dodaj zakładkę**:
    
    > [!div class="mx-imgBorder"]
    > ![Dodaj zakładkę polowania do zapytania](./media/bookmarks/add-hunting-bookmark.png)

6. Po prawej stronie w okienku **Dodaj zakładkę** możesz opcjonalnie zaktualizować nazwę zakładki, dodać tagi i notatki, aby ułatwić identyfikację interesujących elementów.

7. W sekcji **Informacje o zapytaniu** Użyj pól listy rozwijanej, aby wyodrębnić informacje z wyników zapytania dla typu **konta**, **hosta** i jednostki **adresów IP** . Ta akcja mapuje wybrany typ jednostki na określoną kolumnę z wyniku zapytania. Na przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Mapowanie typów jednostek dla zakładki polowania](./media/bookmarks/map-entity-types-bookmark.png)
    
    Aby wyświetlić zakładkę na grafie badania (obecnie w wersji zapoznawczej), należy zmapować co najmniej jeden typ jednostki, który jest **kontem**, **hostem** lub **adresem IP**. 

5. Kliknij przycisk **Zapisz** , aby zatwierdzić wprowadzone zmiany i dodać zakładkę. Wszystkie dane oznaczone zakładką są współużytkowane z innymi osobami badającymi i to pierwszy krok w kierunku współpracy z zespołowym badaniem.

 
> [!NOTE]
> Wyniki zapytania dziennika obsługują zakładki, za każdym razem, gdy to okienko zostanie otwarte z platformy Azure. Na przykład wybierz pozycję Dzienniki **Ogólne** na  >   pasku nawigacyjnym, wybierz pozycję linki zdarzeń w grafie badania lub wybierz identyfikator alertu z pełnych szczegółów zdarzenia (obecnie w wersji zapoznawczej). Nie można tworzyć zakładek, gdy okienko **dzienniki** jest otwierane z innych lokalizacji, na przykład bezpośrednio z Azure monitor.

## <a name="view-and-update-bookmarks"></a>Wyświetl i Zaktualizuj zakładki 

1. W Azure Portal przejdź do polowania dotyczącego   >  **zarządzania zagrożeniami** wskaźnikiem  >  . 

2. Wybierz zakładkę **zakładki** , aby wyświetlić listę zakładek.

3. Aby ułatwić znalezienie określonej zakładki, użyj pola wyszukiwania lub opcji filtrowania.

4. Wybierz poszczególne zakładki i sprawdź szczegóły zakładki w okienku szczegółów z prawej strony.

5. Wprowadź odpowiednie zmiany, które są zapisywane automatycznie.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Eksplorowanie zakładek na grafie badania

> [!IMPORTANT]
> Eksplorowanie zakładek na grafie badania i samym grafie badania jest obecnie w publicznej wersji zapoznawczej.
> Te funkcje są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W Azure Portal przejdź do karty **wskaźnik**  >  polowania na potrzeby **zarządzania zagrożeniami**  >    >   , a następnie wybierz zakładkę lub zakładki, które chcesz zbadać.

2. Upewnij się, że w obszarze Szczegóły zakładki jest zamapowana co najmniej jedna jednostka. Na przykład w przypadku **jednostek** widoczne są wpisy dotyczące **adresu IP**, **komputera** lub **konta**.

3. Kliknij przycisk **Zbadaj** , aby wyświetlić zakładkę w grafie badania.

Aby uzyskać instrukcje dotyczące korzystania z grafu badania, zobacz temat [Korzystanie z grafu badania do głębokiego szczegółowe](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Dodaj zakładki do nowego lub istniejącego zdarzenia

> [!IMPORTANT]
> Dodawanie zakładek do nowego lub istniejącego zdarzenia jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. W Azure Portal przejdź do karty **wskaźnik**  >  polowania na potrzeby **zarządzania zagrożeniami**  >    >   , a następnie wybierz zakładkę lub zakładki, które chcesz dodać do zdarzenia.

2. Wybierz **Akcje zdarzenia (wersja zapoznawcza)** z paska poleceń:
    
    > [!div class="mx-imgBorder"]
    > ![Dodaj zakładki do zdarzenia](./media/bookmarks/incident-actions.png)

3. Wybierz opcję **Utwórz nowe zdarzenie** lub **Dodaj do istniejącego zdarzenia**, zgodnie z potrzebami. Następnie:
    
    - Dla nowego zdarzenia: Opcjonalnie Zaktualizuj szczegóły zdarzenia, a następnie wybierz pozycję **Utwórz**.
    - Aby dodać zakładkę do istniejącego zdarzenia: wybierz jedno zdarzenie, a następnie wybierz pozycję **Dodaj**. 

Aby wyświetlić zakładkę w zdarzeniu: Przejdź do   >  **zdarzenia zarządzania zagrożeniami** wskaźnikiem  >   , a następnie wybierz zdarzenie z zakładką. Wybierz pozycję **Wyświetl pełne szczegóły**, a następnie wybierz zakładkę **zakładki** .

> [!TIP]
> Jako alternatywę dla opcji **Akcje zdarzenia (wersja zapoznawcza)** na pasku poleceń możesz użyć menu kontekstowego (**...**) dla jednej lub kilku zakładek, aby wybrać opcje **tworzenia nowego zdarzenia**, **dodać do istniejącego zdarzenia** i **usunąć ze zdarzenia**. 

## <a name="view-bookmarked-data-in-logs"></a>Wyświetlanie danych z zakładkami w dziennikach

Aby wyświetlić zapytania z zakładkami, wyniki lub ich historię, wybierz zakładkę z karty zakładki **polowania**  >   i użyj linków w okienku szczegółów: 

- **Wyświetl zapytanie źródłowe** , aby wyświetlić zapytanie źródłowe w okienku **dzienniki** .

- **Wyświetl dzienniki zakładek** , aby wyświetlić wszystkie metadane zakładki, w tym osoby, które dokonały aktualizacji, zaktualizowane wartości i czas wystąpienia aktualizacji.

Możesz również wyświetlić nieprzetworzone dane zakładki dla wszystkich zakładek, wybierając pozycję **dzienniki zakładek** na pasku poleceń na karcie zakładki **polowania**  >   :

> [!div class="mx-imgBorder"]
> ![Dzienniki zakładek](./media/bookmarks/bookmark-logs.png)

Ten widok przedstawia wszystkie zakładki ze skojarzonymi metadanymi. Możesz użyć zapytań [języka KQL (keyword Query Language](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ), aby odfiltrować do najnowszej wersji konkretnej zakładki.

> [!NOTE]
> Istnieje duże opóźnienie (mierzone w minutach) między czasem utworzenia zakładki a wyświetleniem jej na karcie **zakładki** .

## <a name="delete-a-bookmark"></a>Usuwanie zakładki
 
1.  W Azure Portal przejdź do karty **wskaźnik**  >  polowania na potrzeby **zarządzania zagrożeniami**  >    >   , a następnie wybierz zakładkę lub zakładki, które chcesz usunąć. 

2. Kliknij prawym przyciskiem myszy wybrane opcje i wybierz opcję usunięcia zakładki lub zakładek. Na przykład **Usuń zakładkę** , jeśli wybrano opcję tylko jedna zakładka, a następnie **usuniesz 2 zakładki** , jeśli wybrano dwie zakładki.
    
Usunięcie zakładki spowoduje usunięcie zakładki z listy na karcie **zakładka** . Tabela **HuntingBookmark** dla obszaru roboczego log Analytics będzie nadal zawierać poprzednie wpisy zakładki, ale Najnowsza pozycja zmieni wartość **SoftDelete** na true, co ułatwia odfiltrowanie starych zakładek. Usunięcie zakładki nie powoduje usunięcia żadnych jednostek ze środowiska badania, które są skojarzone z innymi zakładkami lub alertami. 


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób uruchamiania badania polowania przy użyciu zakładek na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:


- [Proaktywne wyszukiwanie zagrożeń](hunting.md)
- [Korzystanie z notesów do uruchamiania zautomatyzowanych kampanii łowieckich](notebooks.md)