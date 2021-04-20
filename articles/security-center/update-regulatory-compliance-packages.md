---
title: Korzystanie z pulpitu nawigacyjnego zgodności z przepisami w Azure Security Center
description: Dowiedz się, jak dodawać i usuwać standardy prawne z pulpitu nawigacyjnego zgodności z przepisami w Security Center
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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: dab6b504fe026324251b7284fc3abdb52bb9911a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738958"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami

Azure Security Center stale porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, przepisów i testów porównawczych. Pulpit **nawigacyjny zgodności z przepisami** zapewnia wgląd w twój wskaźnik zgodności w oparciu o sposób spełniania określonych wymagań dotyczących zgodności.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>W jaki sposób standardy zgodności z przepisami są reprezentowane w Security Center?

Standardy branżowe, standardy prawne i testy porównawcze są reprezentowane na pulpicie nawigacyjnym zgodności Security Center z przepisami firmy Microsoft. Każdy standard jest inicjatywą zdefiniowaną w Azure Policy.

Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, dodaj standard zgodności do grupy zarządzania lub subskrypcji na **stronie Zasady** zabezpieczeń. Aby dowiedzieć się więcej na temat Azure Policy i inicjatyw, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

Po przypisaniu standardu lub testu porównawczego do wybranego zakresu standard jest wyświetlany na pulpicie nawigacyjnym zgodności z przepisami ze wszystkimi skojarzonymi danymi zgodności zamapowanych jako oceny. Możesz również pobrać raporty podsumowujące dla dowolnego z przypisanych standardów.

Firma Microsoft śledzi same standardy prawne i automatycznie zwiększa ich pokrycie w niektórych pakietach w czasie. Gdy firma Microsoft wyda nową zawartość dla inicjatywy, będzie ona automatycznie wyświetlana na pulpicie nawigacyjnym jako nowe zasady mapowane na kontrolki w standardzie.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Jakie standardy zgodności z przepisami są dostępne w Security Center?

Domyślnie każda subskrypcja ma przypisany test **porównawczy zabezpieczeń** platformy Azure. Są to wytyczne specyficzne dla platformy Azure autorstwa firmy Microsoft dotyczące najlepszych rozwiązań w zakresie zabezpieczeń i zgodności oparte na typowych platformach zgodności. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).

Możesz również dodać standardy, takie jak:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- Uk Official i UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standardy są dodawane do pulpitu nawigacyjnego, gdy tylko staną się dostępne.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Dodawanie standardu prawnego do pulpitu nawigacyjnego

W poniższych krokach wyjaśniono, jak dodać pakiet w celu monitorowania zgodności z jednym z obsługiwanych standardów prawnych.

> [!NOTE]
> Aby dodać standardy do pulpitu nawigacyjnego, subskrypcja musi Azure Defender włączona. Ponadto tylko użytkownicy, którzy są właścicielami lub współautorami zasad, mają uprawnienia niezbędne do dodawania standardów zgodności. 

1. Na Security Center bocznym wybierz pozycję **Zgodność** z przepisami, aby otworzyć pulpit nawigacyjny zgodności z przepisami. W tym miejscu można zobaczyć standardy zgodności aktualnie przypisane do aktualnie wybranych subskrypcji.   

1. W górnej części strony wybierz pozycję **Zarządzaj zasadami zgodności.** Zostanie wyświetlona strona Zarządzanie zasadami.

1. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz zarządzać zgodnością z przepisami. 

    > [!TIP]
    > Zalecamy wybranie najwyższego zakresu, dla którego ma zastosowanie standard, tak aby dane zgodności są agregowane i śledzone dla wszystkich zagnieżdżonych zasobów. 

1. Aby dodać standardy odpowiednie dla twojej organizacji, kliknij **pozycję Dodaj więcej standardów.** 

1. Na stronie **Dodawanie standardów zgodności z przepisami** można wyszukać dowolne z dostępnych standardów, w tym:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO i UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Dodawanie standardów prawnych Azure Security Center pulpitu nawigacyjnego zgodności z przepisami](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Wybierz **pozycję** Dodaj i wprowadź wszystkie niezbędne szczegóły dla określonej inicjatywy, takie jak zakres, parametry i korygowanie.

1. Na Security Center bocznym wybierz ponownie  pozycję Zgodność z przepisami, aby wrócić do pulpitu nawigacyjnego zgodności z przepisami.

    Nowy standard pojawi się na liście standardów branżowych & przepisami. 

    > [!NOTE]
    > Dodanie nowo dodanego standardu na pulpicie nawigacyjnym zgodności może potrwać kilka godzin.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Pulpit nawigacyjny zgodności z przepisami" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Usuwanie standardu z pulpitu nawigacyjnego

Jeśli którykolwiek z dostarczonych standardów prawnych nie jest istotny dla Twojej organizacji, usuwanie ich z interfejsu użytkownika jest prostym procesem. Umożliwia to dalsze dostosowywanie pulpitu nawigacyjnego zgodności z przepisami i skoncentrowanie się tylko na odpowiednich dla Ciebie standardach.

Aby usunąć standard:

1. Z Security Center wybierz pozycję **Zasady zabezpieczeń.**

1. Wybierz odpowiednią subskrypcję, z której chcesz usunąć standard.

    > [!NOTE]
    > Możesz usunąć standard z subskrypcji, ale nie z grupy zarządzania. 

    Zostanie otwarta strona zasad zabezpieczeń. Dla wybranej subskrypcji są w nim przedstawiane domyślne zasady, standardy branżowe i prawne oraz wszystkie utworzone przez Ciebie inicjatywy niestandardowe.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Usuwanie standardu prawnego z pulpitu nawigacyjnego zgodności z przepisami w Azure Security Center":::

1. Dla standardu, który chcesz usunąć, wybierz pozycję **Wyłącz.** Zostanie wyświetlone okno potwierdzenia.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Potwierdź, że naprawdę chcesz usunąć wybrany standard prawnych":::

1. Wybierz pozycję **Tak**. Standard zostanie usunięty. 


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób dodawania **standardów zgodności w** celu monitorowania zgodności ze standardami regulacyjną i branżowymi.

Powiązane materiały można znaleźć na następujących stronach:

- [Test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Pulpit nawigacyjny zgodności z przepisami usługi Security Center](security-center-compliance-dashboard.md) — informacje na temat śledzenia i eksportowania danych zgodności z przepisami przy użyciu Security Center i narzędzi zewnętrznych
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)