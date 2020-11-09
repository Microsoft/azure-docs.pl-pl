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
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: e7e1567a487dc6cadc94a42f02c597ff0e02665b
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372765"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami

Azure Security Center ciągle porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, regulacji i testów porównawczych. **Pulpit nawigacyjny zgodności z przepisami** zapewnia wgląd w stan zgodności w zależności od tego, jak spełniasz określone wymagania kontroli zgodności.


## <a name="overview-of-compliance-packages"></a>Omówienie pakietów zgodności

Standardy branżowe, standardy prawne i testy porównawcze są reprezentowane w Security Center jako *pakiety zgodności*.  Każdy pakiet jest inicjatywą zdefiniowaną w Azure Policy. Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, Dodaj pakiet zgodności do grupy zarządzania lub subskrypcji z poziomu strony **zasady zabezpieczeń** . (Dowiedz się więcej na temat Azure Policy i inicjatyw w [pracy z zasadami zabezpieczeń](tutorial-security-policy.md)).

Po dodaniu standardowego lub testu porównawczego do wybranego zakresu przypisujemy inicjatywę do zakresu, a standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami ze wszystkimi skojarzonymi danymi zgodności mapowanymi jako oceny. Można również pobrać raporty podsumowujące dla dowolnych ze standardów, które zostały dołączone.

Firma Microsoft śledzi również same standardy prawne i automatycznie ulepsza jego pokrycie w niektórych pakietach z upływem czasu. Gdy firma Microsoft wystawia nową zawartość dla inicjatywy (nowe zasady, które mapują do większej liczby kontrolek w standardzie), dodatkowa zawartość zostanie automatycznie wyświetlona na pulpicie nawigacyjnym.

> [!TIP]
> Jednym standardem, który zwiększa się w miarę upływu czasu, ponieważ wersja firmy Microsoft jest nowością **usługi Azure CIS 1.1.0 (nowość** [Microsoft Azure](https://www.cisecurity.org/benchmark/azure/)) Musisz dodać to do pulpitu nawigacyjnego obok "Azure CIS 1.1.0", reprezentację usługi Azure CIS, która jest domyślnie konfigurowana w każdym środowisku Security Center. Ten pakiet opiera się na statycznym zestawie reguł. Nowszy pakiet zawiera więcej zasad i zostanie automatycznie zaktualizowany w miarę upływu czasu. Zaktualizuj do nowego pakietu dynamicznego zgodnie z poniższym opisem.


## <a name="available-packages"></a>Dostępne pakiety

Możesz dodać standardy, takie jak NIST SP 800-53 R4, SWIFT CSP CSCF-V2020, Zjednoczone Królestwo brytyjskie i Zjednoczone Królestwo, NHS, Kanada Federal PBMM i Azure CIS 1.1.0 (NOWOŚĆ) — dokładniejszą reprezentację usługi Azure CIS 1.1.0. 

Ponadto możesz dodać **test wydajności Azure Security** , opracowane przez firmę Microsoft, wytyczne dotyczące zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. ([Dowiedz się więcej o teście zabezpieczeń Azure](../security/benchmarks/introduction.md)).

Dodatkowe standardy będą obsługiwane na pulpicie nawigacyjnym, gdy staną się dostępne. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Dodawanie standardu regulowanego do pulpitu nawigacyjnego

Poniższe kroki wyjaśniają, jak dodać pakiet do monitorowania zgodności z jednym z obsługiwanych standardów prawnych.

> [!NOTE]
> Tylko użytkownicy, którzy są właścicielami lub współautorem zasad, mają uprawnienia niezbędne do dodawania standardów zgodności. 

1. Na pasku bocznym Security Center wybierz pozycję **zgodność z przepisami** , aby otworzyć pulpit nawigacyjny zgodności z przepisami. W tym miejscu możesz zobaczyć standardy zgodności aktualnie przypisane do aktualnie wybranych subskrypcji.   

1. W górnej części strony wybierz pozycję **Zarządzaj zasadami zgodności**. Zostanie wyświetlona strona Zarządzanie zasadami.

1. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz zarządzać stan zgodności z przepisami. 

    > [!TIP]
    > Zalecamy wybranie najwyższego zakresu, dla którego stosuje się standard, aby dane zgodności były agregowane i śledzone dla wszystkich zasobów zagnieżdżonych. 

1. Aby dodać standardy odpowiednie dla organizacji, kliknij pozycję **Dodaj więcej standardów**. 

1. Na stronie **Dodaj standardy zgodności z przepisami** można wyszukać pakiety dla dowolnych dostępnych standardów. Dostępne są następujące standardy:

    - **Test porównawczy zabezpieczeń platformy Azure**
    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF-V2020**
    - **UKO i Wielka Brytania NHS**
    - **Kanada PBMM**
    
    ![Dodawanie pakietów wykonawczych do pulpitu nawigacyjnego zgodności z przepisami Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Wybierz pozycję **Dodaj** i wprowadź wszystkie niezbędne szczegóły dotyczące konkretnej inicjatywy, takie jak zakres, parametry i korygowanie.

1. Na pasku bocznym Security Center wybierz ponownie **zgodność z przepisami** , aby wrócić do pulpitu nawigacyjnego zgodności z przepisami.
    * Twój nowy standard zostanie wyświetlony na liście branżowych & standardów prawnych. 
    * Po dodaniu **usługi Azure CIS 1.1.0 (NOWOŚĆ)** oryginalny widok *statyczny* zgodności z usługą Azure CIS 1.1.0 również będzie nadal używany razem z nim. Może zostać automatycznie usunięta w przyszłości.

    > [!NOTE]
    > Dodanie nowego standardu do pulpitu nawigacyjnego zgodności może potrwać kilka godzin.

    [![Pulpit nawigacyjny zgodności z przepisami zawierający stare i nowe usługi Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Usuwanie standardu z pulpitu nawigacyjnego

Jeśli którekolwiek z podanych norm prawnych nie jest istotne dla Twojej organizacji, jest to prosty proces po prostu usuń je z interfejsu użytkownika. Pozwala to na dalsze dostosowanie pulpitu nawigacyjnego zgodności z przepisami i skoncentrowanie się wyłącznie na standardach, które mają zastosowanie.

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

W tym artykule opisano sposób **dodawania pakietów zgodności** w celu monitorowania zgodności z dodatkowymi standardami. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/introduction.md)
- [Pulpit nawigacyjny zgodności z przepisami Centrum zabezpieczeń](security-center-compliance-dashboard.md)
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)