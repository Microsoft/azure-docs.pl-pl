---
title: Używanie reguł pomijania alertów w celu pomijania fałszywie dodatnich lub innych niechcianych alertów zabezpieczeń w Azure Security Center.
description: W tym artykule wyjaśniono, jak używać reguł pomijania Azure Security Center w celu ukrycia niechcianych alertów zabezpieczeń
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 646495597565bbb033ac3adaa15f3754f33e8fd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634173"
---
# <a name="suppress-alerts-from-azure-defender"></a>Pomijanie alertów z usługi Azure Defender

Na tej stronie wyjaśniono, jak można użyć reguł pomijania alertów, aby pominąć fałszywe pozytywne lub inne niepożądane alerty zabezpieczeń z usługi Azure Defender.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Bezpłatna<br>(Większość alertów zabezpieczeń jest dostępnych tylko w usłudze Azure Defender)|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń** i **właściciel** mogą tworzyć/usuwać reguły.<br>**Czytelnik zabezpieczeń** i **czytelnik** mogą wyświetlać reguły.|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="what-are-suppression-rules"></a>Co to są reguły pomijania?

Różne plany usługi Azure Defender wykrywają zagrożenia w dowolnym obszarze środowiska i generują alerty zabezpieczeń.

Jeśli pojedynczy alert nie jest interesujący lub istotny, można go ręcznie odrzucić. Alternatywnie można użyć funkcji pomijania reguł, aby automatycznie odrzucić podobne alerty w przyszłości. Zazwyczaj można użyć reguły pomijania, aby:

- Pomijaj alerty, które zostały zidentyfikowane jako fałszywe pozytywne

- Pomijanie alertów, które są wyzwalane zbyt często, aby być przydatne

Reguły pomijania definiują kryteria, dla których alerty powinny być automatycznie odrzucane.

> [!CAUTION]
> Pomijanie alertów zabezpieczeń zmniejsza efektywność ochrony przed zagrożeniami w usłudze Azure Defender. Należy uważnie sprawdzić potencjalny wpływ każdej reguły pomijania i monitorować ją w czasie.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Utwórz regułę pomijania alertu":::

## <a name="create-a-suppression-rule"></a>Tworzenie reguły pomijania

Istnieje kilka sposobów tworzenia reguł pomijania niepożądanych alertów zabezpieczeń:

- Aby pominąć alerty na poziomie grupy zarządzania, użyj usługi Azure Policy
- Aby pominąć alerty na poziomie subskrypcji, możesz użyć witryny Azure Portal lub interfejsu API REST, jak wyjaśniono poniżej

Reguły pomijania mogą odrzucać tylko alerty, które zostały już wyzwolone w wybranych subskrypcjach.

Aby utworzyć regułę bezpośrednio w Azure Portal:

1. Na stronie alertów zabezpieczeń portalu Security Center:

    - Wybierz konkretny alert, którego nie chcesz już wyświetlać, a następnie w okienku szczegółów wybierz pozycję **podejmij akcję**.

    - Lub wybierz łącze **reguły pomijania** w górnej części strony, a następnie na stronie reguły pomijania wybierz pozycję **Utwórz nową regułę pomijania**:

        ![Przycisk tworzenia nowej reguły pomijania * *](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. W okienku Nowa reguła pomijania wprowadź szczegóły nowej reguły.
    - Reguła może odrzucić alert dotyczący **wszystkich zasobów** , aby nie otrzymywać żadnych alertów, takich jak te w przyszłości.     
    - Reguła może odrzucić alert **dotyczący określonych kryteriów** — w przypadku odnoszącego się do określonego adresu IP, nazwy procesu, konta użytkownika, zasobu platformy Azure lub lokalizacji.

    > [!TIP]
    > Jeśli otwarto nową stronę reguły z określonego alertu, alert i subskrypcja zostaną automatycznie skonfigurowane w nowej regule. W przypadku użycia łącza **Utwórz nową regułę pomijania** wybrane subskrypcje będą zgodne z bieżącym filtrem w portalu.

    [![Okienko tworzenia reguły pomijania](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Wprowadź szczegóły reguły:
    - **Nazwa** — Nazwa reguły. Nazwy reguł muszą zaczynać się literą lub cyfrą i mieć od 2 do 50 znaków oraz nie mogą zawierać symboli innych niż kreski (-) i podkreślenia (_). 
    - **Stan** — włączony lub wyłączony.
    - **Przyczyna** — wybierz jedno z wbudowanych przyczyn lub "inne", jeśli nie spełnią Twoich potrzeb.
    - **Data wygaśnięcia** — Data i godzina zakończenia dla reguły. Reguły mogą działać przez maksymalnie sześć miesięcy.
1. Opcjonalnie Przetestuj regułę przy użyciu przycisku **Symuluj** , aby zobaczyć, ile alertów zostało odrzuconych, jeśli ta reguła była aktywna.
1. Zapisz regułę. 


## <a name="edit-a-suppression-rule"></a>Edytowanie reguły pomijania

Aby edytować utworzoną regułę, użyj strony reguły pomijania.

1. Na stronie Alerty zabezpieczeń Security Center wybierz łącze **reguły pomijania** w górnej części strony.
1. Zostanie otwarta strona reguły pomijania ze wszystkimi regułami dla wybranych subskrypcji.

    [![Lista reguł pomijania](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Aby edytować pojedynczą regułę, otwórz menu wielokropka (...) dla reguły i wybierz pozycję **Edytuj**.
1. Wprowadź niezbędne zmiany i wybierz pozycję **Zastosuj**. 

## <a name="delete-a-suppression-rule"></a>Usuwanie reguły pomijania

Aby usunąć co najmniej jedną utworzoną regułę, użyj strony reguły pomijania.

1. Na stronie Alerty zabezpieczeń Security Center wybierz łącze **reguły pomijania** w górnej części strony.
1. Zostanie otwarta strona reguły pomijania ze wszystkimi regułami dla wybranych subskrypcji.
1. Aby usunąć pojedynczą regułę, otwórz menu wielokropka (...) dla reguły i wybierz polecenie **Usuń**.
1. Aby usunąć wiele reguł, zaznacz pola wyboru dla reguł, które mają zostać usunięte, a następnie wybierz pozycję **Usuń**.
    ![Usuwanie co najmniej jednej reguły pomijania](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>Tworzenie reguł pomijania i zarządzanie nimi za pomocą interfejsu API

Można tworzyć, wyświetlać i usuwać reguły pomijania alertów za pośrednictwem interfejsu API REST Security Center. 

Odpowiednie metody HTTP dla reguł pomijania w interfejsie API REST są następujące:

- **Put**: Aby utworzyć lub zaktualizować regułę pomijania w określonej subskrypcji.

- **Pobierz**:

    - Aby wyświetlić listę wszystkich reguł skonfigurowanych dla określonej subskrypcji. Ta metoda zwraca tablicę odpowiednich reguł.

    - Aby uzyskać szczegółowe informacje o konkretnej regule w określonej subskrypcji. Ta metoda zwraca jedną regułę pomijania.

    - Aby symulować wpływ reguły pomijania w fazie projektowania. To wywołanie określa, które z istniejących alertów zostałyby odrzucone, jeśli reguła była aktywna.

- **Usuń**: Usuwa istniejącą regułę (ale nie zmienia stanu alertów, które zostały już odrzucone przez niego).

Aby zapoznać się z pełnymi szczegółami i przykładami użycia, zobacz [dokumentację interfejsu API](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano reguły pomijania w Azure Security Center, które automatycznie odrzucają niechciane alerty.

Aby uzyskać więcej informacji na temat alertów zabezpieczeń usługi Azure Defender, zobacz następujące strony:

- [Alerty zabezpieczeń i łańcuch](alerts-reference.md) założeń — Przewodnik dotyczący alertów zabezpieczeń, które mogą zostać uzyskane z usługi Azure Defender.