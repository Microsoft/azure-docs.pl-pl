---
title: Zwalnianie zasobu z Azure Security Center zaleceń dotyczących zabezpieczeń i bezpiecznego oceny
description: Dowiedz się, jak wykluczać zasób z zaleceń dotyczących zabezpieczeń i uzyskać bezpieczny wynik
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7780a0ef70a89a88070d5883cc840319a67fa3d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122364"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Zwalnianie zasobu z zaleceń i wskaźnika bezpieczeństwa

Podstawowy priorytet każdego zespołu ds. zabezpieczeń próbuje zapewnić analitykom możliwość skoncentrowania się na zadaniach i zdarzeniach, które odnoszą się do organizacji. Security Center ma wiele funkcji służących do dostosowywania informacji, które są bardziej priorytetowe i zapewniają, że bezpieczny wynik jest ważnym odbiciem decyzji w zakresie bezpieczeństwa organizacji. Wykluczone zasoby to jedna z tych funkcji.

Po zbadaniu zalecenia dotyczącego zabezpieczeń w Azure Security Center jedną z pierwszych informacji, którą przeglądasz, jest lista zasobów, których to dotyczy.

Czasami zasób zostanie wyświetlony na liście, który nie powinien być uwzględniony. Być może został skorygowany przez proces, który nie jest śledzony przez Security Center. Lub być może Twoja organizacja zdecydowała się zaakceptować ryzyko związane z tym konkretnym zasobem. 

W takich przypadkach można utworzyć regułę wykluczania i upewnić się, że zasób nie znajduje się na liście niezdrowych zasobów w przyszłości i nie ma wpływu na bezpieczny wynik. 

Zasób będzie wyświetlany jako nie dotyczy i powód zostanie wyświetlony jako "zwolniony" z uzasadnieniem, które wybierzesz.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Wpisaną|Jest to funkcja Premium platformy Azure oferowana dla klientów usługi Azure Defender bez dodatkowych kosztów. W przypadku innych użytkowników opłaty mogą być stosowane w przyszłości.|
|Wymagane role i uprawnienia:|**Właściciel subskrypcji** lub **współautor zasad** , aby utworzyć wykluczenie<br>Aby utworzyć regułę, musisz mieć uprawnienia do edytowania zasad w Azure Policy.<br>Dowiedz się więcej na temat [uprawnień kontroli RBAC platformy Azure w Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="create-an-exemption-rule"></a>Utwórz regułę wykluczania

1. Na liście zasobów w złej kondycji wybierz menu wielokropka ("...") dla zasobu, który ma zostać wykluczony.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Utwórz opcję wykluczenia z menu kontekstowego":::

    Zostanie otwarte okienko Utwórz wykluczenie.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Utwórz okienko wykluczenia":::

1. Wprowadź kryteria i wybierz kryteria, dla których ten zasób powinien być wykluczony:
    - **Skorygowane** — ten problem nie dotyczy zasobu, ponieważ został obsłużony przez inne narzędzie lub proces niż ten, który jest sugerowany
    - **Rezygnacja** z akceptacji ryzyka dla tego zasobu
1. Wybierz pozycję **Zapisz**.
1. Po czasie (może to potrwać do 24 godzin):
    - Zasób nie ma wpływu na swój Bezpieczny wynik.
    - Zasób jest wymieniony na karcie **nie dotyczy** na stronie szczegółów rekomendacji
    - Pasek informacji w górnej części strony Szczegóły rekomendacji zawiera liczbę wykluczonych zasobów:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Liczba wykluczonych zasobów":::

1. Aby przejrzeć wykluczone zasoby, Otwórz kartę **nie dotyczy** .

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modyfikowanie wykluczenia":::

    Przyczyną każdego wykluczenia jest dołączenie do tabeli (1).

    Aby zmodyfikować lub usunąć wykluczenie, wybierz menu wielokropka ("..."), jak pokazano (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Przejrzyj wszystkie reguły wykluczania w Twojej subskrypcji

Reguły wykluczania używają zasad platformy Azure, aby utworzyć wykluczenie dla zasobu w przypisaniu zasad.

Za pomocą Azure Policy można śledzić wszystkie wykluczenia na stronie **zwolnienia** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Strona wykluczenia Azure Policy":::



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wykluczenia zasobu z rekomendacji, dzięki czemu nie ma to wpływu na swój Bezpieczny wynik. Aby uzyskać więcej informacji na temat bezpiecznego oceny, zobacz:

- [Wskaźnik bezpieczeństwa w usłudze Azure Security Center](secure-score-security-controls.md)