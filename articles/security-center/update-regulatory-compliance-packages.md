---
title: Korzystanie z pulpitu nawigacyjnego zgodność z przepisami w Azure Security Center
description: Dowiedz się, jak dodawać i usuwać standardy prawne z pulpitu nawigacyjnego zgodność z przepisami w Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 5f70f74accba5df439351547fda7497656e2c06e
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986688"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami

Azure Security Center ciągle porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, regulacji i testów porównawczych. **Pulpit nawigacyjny zgodności z przepisami** zapewnia wgląd w stan zgodności w zależności od tego, jak spełniasz określone wymagania dotyczące zgodności.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Jak są zgodne normy zgodności z przepisami Security Center?

Standardy branżowe, standardy prawne i testy porównawcze są reprezentowane w Security Center pulpicie nawigacyjnym zgodności z przepisami. Każdy standard jest inicjatywą zdefiniowaną w Azure Policy.

Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, należy dodać Standard zgodności do grupy zarządzania lub subskrypcji z poziomu strony **zasady zabezpieczeń** . Aby dowiedzieć się więcej na temat Azure Policy i inicjatyw, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

Po przypisaniu standardowego lub testu porównawczego do wybranego zakresu Standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami ze wszystkimi skojarzonymi danymi zgodności mapowanymi jako oceny. Można również pobrać raporty podsumowujące dla dowolnych standardów, które zostały przypisane.

Firma Microsoft śledzi same standardy prawne i automatycznie ulepsza jego pokrycie w niektórych pakietach z upływem czasu. Gdy firma Microsoft wystawia nową zawartość dla tej inicjatywy, zostanie automatycznie wyświetlona na pulpicie nawigacyjnym jako nowe zasady zamapowane na kontrolki w warstwie Standardowa.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Jakie standardy zgodności są dostępne w Security Center?

Domyślnie każda subskrypcja ma przypisany **wzorzec zabezpieczeń Azure** . Jest to opracowane przez firmę Microsoft wskazówki dotyczące zabezpieczeń i zgodności z najlepszymi rozwiązaniami w oparciu o typowe struktury zgodności. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Możesz również dodać standardy, takie jak:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-V2020
- Oficjalne Królestwo brytyjskie i Zjednoczone Królestwo NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standardy są dodawane do pulpitu nawigacyjnego, gdy staną się dostępne.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Dodawanie standardu regulowanego do pulpitu nawigacyjnego

Poniższe kroki wyjaśniają, jak dodać pakiet do monitorowania zgodności z jednym z obsługiwanych standardów prawnych.

> [!NOTE]
> Aby dodać standardy do pulpitu nawigacyjnego, subskrypcja musi mieć włączoną usługę Azure Defender. Ponadto tylko użytkownicy, którzy są właścicielami lub współautorem zasad, mają uprawnienia niezbędne do dodawania standardów zgodności. 

1. Na pasku bocznym Security Center wybierz pozycję **zgodność z przepisami** , aby otworzyć pulpit nawigacyjny zgodności z przepisami. W tym miejscu możesz zobaczyć standardy zgodności aktualnie przypisane do aktualnie wybranych subskrypcji.   

1. W górnej części strony wybierz pozycję **Zarządzaj zasadami zgodności**. Zostanie wyświetlona strona Zarządzanie zasadami.

1. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz zarządzać stan zgodności z przepisami. 

    > [!TIP]
    > Zalecamy wybranie najwyższego zakresu, dla którego stosuje się standard, aby dane zgodności były agregowane i śledzone dla wszystkich zasobów zagnieżdżonych. 

1. Aby dodać standardy odpowiednie dla organizacji, kliknij pozycję **Dodaj więcej standardów**. 

1. Na stronie **Dodaj standardy zgodności z przepisami** można wyszukać dowolne dostępne standardy, takie jak:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO i Wielka Brytania NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Dodawanie standardów regulacyjnych do pulpitu nawigacyjnego zgodności z przepisami Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Wybierz pozycję **Dodaj** i wprowadź wszystkie niezbędne szczegóły dotyczące konkretnej inicjatywy, takie jak zakres, parametry i korygowanie.

1. Na pasku bocznym Security Center wybierz ponownie **zgodność z przepisami** , aby wrócić do pulpitu nawigacyjnego zgodności z przepisami.

    Twój nowy standard zostanie wyświetlony na liście branżowych & standardów prawnych. 

    > [!NOTE]
    > Dodanie nowego standardu do pulpitu nawigacyjnego zgodności może potrwać kilka godzin.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="removing-a-standard-from-your-dashboard"></a>Usuwanie standardu z pulpitu nawigacyjnego

Jeśli którekolwiek z podanych norm prawnych nie jest istotne dla Twojej organizacji, jest to prosty proces usuwania ich z interfejsu użytkownika. Pozwala to na dalsze dostosowanie pulpitu nawigacyjnego zgodności z przepisami i skoncentrowanie się wyłącznie na standardach, które mają zastosowanie.

Aby usunąć standard:

1. Z menu Security Center wybierz pozycję **zasady zabezpieczeń**.

1. Wybierz odpowiednią subskrypcję, z której chcesz usunąć Standard.

    > [!NOTE]
    > Możesz usunąć Standard z subskrypcji, ale nie z grupy zarządzania. 

    Zostanie otwarta strona zasady zabezpieczeń. W ramach wybranej subskrypcji są wyświetlane zasady domyślne, branżowe i zgodne ze standardami, a także wszelkie utworzone niestandardowe inicjatywy.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Usuwanie normy regulacyjnej z pulpitu nawigacyjnego zgodności z przepisami w Azure Security Center":::

1. Dla standardu, który chcesz usunąć, wybierz pozycję **Wyłącz**. Zostanie wyświetlone okno potwierdzenia.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Potwierdź, że na pewno chcesz usunąć wybrany Standard regulacji":::

1. Wybierz pozycję **Tak**. Standard zostanie usunięty. 


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób **dodawania standardów zgodności** w celu monitorowania zgodności z przepisami i standardami branżowymi.

Pokrewnych materiałów można znaleźć na następujących stronach:

- [Test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/introduction.md)
- [Pulpit nawigacyjny zgodności z przepisami Centrum zabezpieczeń](security-center-compliance-dashboard.md)
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)