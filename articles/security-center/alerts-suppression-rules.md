---
title: Używanie reguł pomijania alertów w celu pomijania fałszywie dodatnich lub innych niechcianych alertów zabezpieczeń w Azure Security Center.
description: W tym artykule wyjaśniono, jak używać reguł pomijania Azure Security Center w celu ukrycia niechcianych alertów zabezpieczeń.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 84b5cd8a59103f60249da861238acb45f8aa2fd5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871697"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Pomijanie alertów z ochrony przed zagrożeniami w usłudze Azure Security Center

Na tej stronie wyjaśniono, jak można użyć reguł pomijania alertów, aby pominąć fałszywie dodatnie lub inne niepożądane alerty zabezpieczeń w Azure Security Center.

## <a name="availability"></a>Dostępność

- Stan wydania: **wersja zapoznawcza**
- Wymagane role: **administrator zabezpieczeń** i **właściciel** mogą tworzyć/usuwać reguły. **Czytelnik zabezpieczeń** i **czytelnik** mogą wyświetlać reguły.
- Połączeń 
    - ✔ Chmury komercyjne
    - ✔ National/suwerenny (US Gov, Chiny gov, inne gov)


## <a name="introduction-to-suppression-rules"></a>Wprowadzenie do reguł pomijania

Składniki ochrony przed zagrożeniami Azure Security Center wykrywają zagrożenia w dowolnym obszarze środowiska i generują alerty zabezpieczeń.

Jeśli pojedynczy alert nie jest interesujący lub istotny, można go ręcznie odrzucić. Alternatywnie można użyć funkcji pomijania reguł, aby automatycznie odrzucić podobne alerty w przyszłości. Zazwyczaj można użyć reguły pomijania, aby:

- Pomijaj alerty, które zostały zidentyfikowane jako fałszywe pozytywne

- Pomijanie alertów, które są wyzwalane zbyt często, aby być przydatne

Reguły pomijania definiują kryteria, dla których alerty powinny być automatycznie odrzucane.

> [!CAUTION]
> Pomijanie alertów zabezpieczeń zmniejsza ochronę przed zagrożeniami Security Center. Należy uważnie sprawdzić potencjalny wpływ każdej reguły pomijania i monitorować ją w czasie.

[![Strona alerty zabezpieczeń Azure Security Center z opcjami pomijania alertów](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Tworzenie reguły pomijania

Istnieje kilka sposobów, aby można było utworzyć reguły pomijające niepożądane alerty zabezpieczeń:

- Aby pominąć alerty na poziomie grupy zarządzania, użyj Azure Policy

- Aby pominąć alerty na poziomie subskrypcji, można użyć Azure Portal lub interfejsu API REST, jak wyjaśniono poniżej

Reguły pomijania mogą odrzucać tylko alerty, które zostały już wyzwolone w wybranych subskrypcjach.

Aby utworzyć regułę bezpośrednio w Azure Portal:

1. Ze strony alertów zabezpieczeń Security Center:

    - Znajdź konkretny alert, którego nie chcesz już zobaczyć, a następnie z menu wielokropka (...) dla alertu wybierz pozycję **Utwórz regułę pomijania**:

        [![* * Tworzenie reguły pomijania * * — opcja](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Lub wybierz łącze **reguły pomijania** w górnej części strony, a następnie na stronie reguły pomijania wybierz pozycję **Utwórz nową regułę pomijania**:

        ![Przycisk tworzenia nowej reguły pomijania * *](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. W okienku Nowa reguła pomijania wprowadź szczegóły nowej reguły.

    - Reguła może odrzucić alert dotyczący **wszystkich zasobów** , aby nie otrzymywać żadnych alertów, takich jak te w przyszłości. 
    
    - Reguła może odrzucić alert **dotyczący określonych kryteriów** — w przypadku odnoszącego się do określonego adresu IP, nazwy procesu, konta użytkownika, zasobu platformy Azure lub lokalizacji.

    > [!TIP]
    > Jeśli otwarto nową stronę reguły z określonego alertu, alert i subskrypcja zostaną automatycznie skonfigurowane w nowej regule. W przypadku użycia łącza **Utwórz nową regułę pomijania** wybrane subskrypcje będą zgodne z bieżącym filtrem w portalu.

    [![Okienko tworzenia reguły pomijania](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Wprowadź szczegóły reguły:

    - **Nazwa** — Nazwa reguły. Nazwy reguł muszą zaczynać się literą lub cyfrą od 2 do 50 znaków i nie mogą zawierać symboli innych niż kreski (-) ani znaki podkreślenia (_). 
    - **Stan** — włączony lub wyłączony.
    - **Przyczyna** — wybierz jedno z wbudowanych przyczyn lub "inne", jeśli nie spełnią Twoich potrzeb.
    - **Data wygaśnięcia** — Data i godzina zakończenia dla reguły. Reguły mogą działać przez maksymalnie sześć miesięcy.

1. Opcjonalnie Przetestuj regułę przy użyciu przycisku **Symuluj** , aby zobaczyć, ile alertów zostało odrzuconych, jeśli ta reguła była aktywna.

1. Zapisz regułę. 

## <a name="editing-suppression-rules"></a>Edytowanie reguł pomijania

Aby edytować utworzone reguły, użyj strony reguły pomijania.

1. Na stronie Alerty zabezpieczeń Security Center wybierz łącze **reguły pomijania** w górnej części strony.

1. Na stronie reguły pomijania zostanie wyświetlona lista wszystkich dostępnych reguł zgodnie z aktualnie wybranymi subskrypcjami. 

    [![Lista reguł pomijania](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Aby edytować pojedynczą regułę, otwórz menu wielokropka (...) dla reguły i wybierz pozycję **Edytuj**.

1. Wprowadź niezbędne zmiany i wybierz pozycję **Zastosuj**. 

## <a name="deleting-suppression-rules"></a>Usuwanie reguł pomijania

Aby usunąć co najmniej jedną utworzoną regułę, użyj strony reguły pomijania.

1. Na stronie Alerty zabezpieczeń Security Center wybierz łącze **reguły pomijania** w górnej części strony.

1. Na stronie reguły pomijania zostanie wyświetlona lista wszystkich dostępnych reguł zgodnie z aktualnie wybranymi subskrypcjami. 

1. Aby usunąć pojedynczą regułę, otwórz menu wielokropka (...) dla reguły i wybierz polecenie **Usuń**.

1. Aby usunąć wiele reguł, zaznacz pola wyboru dla reguł, które mają zostać usunięte, a następnie wybierz pozycję **Usuń**.

    ![Usuwanie co najmniej jednej reguły pomijania](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Wyświetlanie alertów, które zostały pominięte

Alerty zgodne z włączonymi regułami pomijania będą nadal generowane, ale ich stan zostanie ustawiony na **odrzucony**. Stan można sprawdzić w Azure Portal lub uzyskać dostęp do Security Center alertów zabezpieczeń. 

> [!TIP]
> [Wskaźnik na platformie Azure](https://azure.microsoft.com/services/azure-sentinel/) nie będzie tworzyć zdarzeń dla pominiętych alertów. W przypadku innych rozwiązań Siem można filtrować pominięte alerty przy użyciu stanu alertów (odrzucono ").

Użyj filtru Security Center, aby wyświetlić alerty, które zostały odrzucone przez reguły.

* Na stronie Alerty zabezpieczeń Security Center Otwórz opcje filtru i wybierz pozycję **odrzucone**.  

   [![Wyświetlanie odrzuconych alertów](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Tworzenie reguł pomijania i zarządzanie nimi za pomocą interfejsu API

Można tworzyć, wyświetlać i usuwać reguły pomijania alertów za pośrednictwem interfejsu API REST Security Center. 

Odpowiednie metody HTTP dla reguł pomijania w interfejsie API REST są następujące:

- **Put**: Aby utworzyć lub zaktualizować regułę pomijania w określonej subskrypcji.

- **Pobierz**:

    - Aby wyświetlić listę wszystkich reguł skonfigurowanych dla określonej subskrypcji. Ta metoda zwraca tablicę odpowiednich reguł.

    - Aby uzyskać szczegółowe informacje o konkretnej regule w określonej subskrypcji. Ta metoda zwraca jedną regułę pomijania.

    - Aby symulować wpływ reguły pomijania w fazie projektowania. To wywołanie określa, które z istniejących alertów zostałyby odrzucone, jeśli reguła była aktywna.

- **Usuń**: Usuwa istniejącą regułę (ale nie zmienia stanu alertów, które zostały już odrzucone przez niego).

Aby zapoznać się z pełnymi szczegółami i przykładami użycia, zobacz [dokumentację interfejsu API](https://docs.microsoft.com/rest/api/securitycenter/). 


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano reguły pomijania w Azure Security Center, które automatycznie odrzucają niechciane alerty.

Aby uzyskać więcej informacji na temat alertów zabezpieczeń w Azure Security Center, zobacz następujące strony:

- [Alerty zabezpieczeń i łańcuch zamiar zabicia](alerts-reference.md) — Przewodnik dotyczący alertów zabezpieczeń, które mogą zostać wyświetlone w module ochrony przed zagrożeniami w Azure Security Center.
- [Ochrona przed zagrożeniami w Azure Security Center](threat-protection.md) — opis wielu aspektów środowiska monitorowanych przez moduł Azure Security Center ochrony przed zagrożeniami.