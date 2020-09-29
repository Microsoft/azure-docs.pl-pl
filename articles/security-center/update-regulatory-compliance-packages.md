---
title: Jak zaktualizować dynamiczne monitorowanie zgodności z przepisami na pulpicie nawigacyjnym zgodności z przepisami Azure Security Center | Microsoft Docs
description: Aktualizowanie pakietów zgodności z przepisami
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 306a4773563b829ecad09a021f6763192e8246f5
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445863"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami

Azure Security Center ciągle porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, regulacji i testów porównawczych. **Pulpit nawigacyjny zgodności z przepisami** zapewnia wgląd w stan zgodności w zależności od tego, jak spełniasz określone wymagania kontroli zgodności.


## <a name="overview-of-compliance-packages"></a>Przegląd pakietów zgodności

Standardy branżowe, standardy prawne i testy porównawcze są reprezentowane w Security Center jako *pakiety zgodności*.  Każdy pakiet jest inicjatywą zdefiniowaną w Azure Policy. Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, Dodaj pakiet zgodności do grupy zarządzania lub subskrypcji z poziomu strony **zasady zabezpieczeń** . (Dowiedz się więcej na temat Azure Policy i inicjatyw w [pracy z zasadami zabezpieczeń](tutorial-security-policy.md)).

Po dodaniu standardowego lub testu porównawczego do wybranego zakresu przypisujemy inicjatywę do zakresu, a standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami ze wszystkimi skojarzonymi danymi zgodności mapowanymi jako oceny. Można również pobrać raporty podsumowujące dla dowolnych ze standardów, które zostały dołączone.

Firma Microsoft śledzi również same standardy prawne i automatycznie ulepsza jego pokrycie w niektórych pakietach z upływem czasu. Gdy firma Microsoft wystawia nową zawartość dla inicjatywy (nowe zasady, które mapują do większej liczby kontrolek w standardzie), dodatkowa zawartość zostanie automatycznie wyświetlona na pulpicie nawigacyjnym.

> [!TIP]
> Jednym standardem, który zwiększa się w miarę upływu czasu, ponieważ wersja firmy Microsoft jest nowością **usługi Azure CIS 1.1.0 (nowość** [Microsoft Azure](https://www.cisecurity.org/benchmark/azure/)) Musisz dodać to do pulpitu nawigacyjnego obok "Azure CIS 1.1.0", reprezentację usługi Azure CIS, która jest domyślnie konfigurowana w każdym środowisku Security Center. Ten pakiet opiera się na statycznym zestawie reguł. Nowszy pakiet zawiera więcej zasad i zostanie automatycznie zaktualizowany w miarę upływu czasu. Zaktualizuj do nowego pakietu dynamicznego zgodnie z poniższym opisem.


## <a name="available-packages"></a>Dostępne pakiety

Możesz dodać standardy, takie jak NIST SP 800-53 R4, SWIFT CSP CSCF-V2020, Zjednoczone Królestwo brytyjskie i Zjednoczone Królestwo, NHS, Kanada Federal PBMM i Azure CIS 1.1.0 (NOWOŚĆ) — dokładniejszą reprezentację usługi Azure CIS 1.1.0. 

Ponadto możesz dodać **test wydajności Azure Security**, opracowane przez firmę Microsoft, wytyczne dotyczące zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. ([Dowiedz się więcej o teście zabezpieczeń Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)).

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

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób **dodawania pakietów zgodności** w celu monitorowania zgodności z dodatkowymi standardami. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Pulpit nawigacyjny zgodności z przepisami Centrum zabezpieczeń](security-center-compliance-dashboard.md)
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)