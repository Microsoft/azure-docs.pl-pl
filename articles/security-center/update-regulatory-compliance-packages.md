---
title: Jak zaktualizować dynamiczne monitorowanie zgodności z przepisami w pulpicie nawigacyjnym zgodności z przepisami usługi Azure Security Center | Dokumenty firmy Microsoft
description: Aktualizowanie pakietów zgodności z przepisami
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537784"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Aktualizacja do dynamicznych pakietów zgodności na pulpicie nawigacyjnym zgodności z przepisami

Usługa Azure Security Center stale porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, przepisów i testów porównawczych. **Pulpit nawigacyjny zgodności z przepisami** zapewnia wgląd w postawę zgodności w zależności od tego, jak spełniasz określone mechanizmy kontroli zgodności i wymagania.

Jednym ze standardów, dla których można śledzić swoją postawę zgodności jest [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (bardziej formalnie, "CIS Microsoft Azure Foundations Benchmark w wersji 1.1.0"). 

Reprezentacja usługi Azure CIS, która początkowo pojawia się na pulpicie nawigacyjnym zgodności, opiera się na statycznym zestawie reguł dołączonym do usługi Security Center.

Dzięki funkcji **dynamicznych pakietów zgodności** usługa Security Center automatycznie zwiększa zakres standardów branżowych w czasie. Pakiety zgodności są zasadniczo inicjatyw zdefiniowanych w zasadach platformy Azure. Można je przypisać do wybranego zakresu (subskrypcja, grupa zarządzania itd.). Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, dodaj pakiet zgodności do grupy zarządzania lub subskrypcji z poziomu zasad zabezpieczeń. Dodanie pakietu zgodności skutecznie przypisuje inicjatywę zgodności z przepisami do wybranego zakresu. W ten sposób można śledzić nowo opublikowane inicjatywy regulacyjne jako standardy zgodności na pulpicie nawigacyjnym. Gdy firma Microsoft wypuszcza nową zawartość dla inicjatywy (nowe zasady, które mapują na więcej formantów w standardzie), dodatkowa zawartość pojawia się automatycznie na pulpicie nawigacyjnym.

Dynamiczny pakiet zgodności dla wzorca Azure CIS, **Azure CIS 1.1.0 (nowy),** poprawia oryginalną wersję *statyczną* o:

* W tym więcej polityk
* Automatyczne aktualizowanie z nowym pokryciem w miarę jego dodawania 

Zaktualizuj do nowego pakietu dynamicznego, jak opisano poniżej.

## <a name="adding-a-dynamic-compliance-package"></a>Dodawanie dynamicznego pakietu zgodności

W poniższych krokach wyjaśniono, jak dodać pakiet dynamiczny do monitorowania zgodności z testem porównawczym usługi Azure CIS w wersji 1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Aktualizacja pakietu zgodności dynamicznej usługi Azure CIS 1.1.0 (nowy) 

1. Otwórz stronę **Zasady zabezpieczeń.** Na tej stronie przedstawiono liczbę grup zarządzania, subskrypcji, obszarów roboczych i strukturę grupy zarządzania.

1. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz zarządzać postawą zgodności z przepisami. Zaleca się wybranie najwyższego zakresu, dla którego norma ma zastosowanie, tak aby dane zgodności były agregowane i śledzone dla wszystkich zagnieżdżonych zasobów. 

1. W sekcji Branżowe & standardy regulacyjne zobaczysz, że usługa Azure CIS 1.1.0 może być aktualizowana w celu uzyskania nowej zawartości. Kliknij **pozycję Aktualizuj teraz**. 

1. Opcjonalnie kliknij pozycję **Dodaj więcej standardów,** aby otworzyć stronę **Dodaj standardy zgodności z przepisami.** Tam można ręcznie wyszukiwać **pakiety Azure CIS 1.1.0 (Nowe)** i dynamiczne pod kątem innych standardów zgodności, takich jak **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020**, **UKO i UK NHS**oraz Canada **PBMM**.
    
    > [!TIP]
    > Tylko użytkownicy, którzy są właścicielami lub współautorami zasad, mają uprawnienia niezbędne do dodawania standardów zgodności. 

    ![Dodawanie pakietów regulacyjnych do pulpitu nawigacyjnego zgodności z przepisami usługi Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Na pasku bocznym centrum zabezpieczeń wybierz **pozycję Zgodność z przepisami,** aby otworzyć pulpit nawigacyjny zgodności z przepisami. 
    * Usługa Azure CIS 1.1.0 (Nowy) jest teraz wyświetlana na liście standardów regulacyjnych branżowych &. 
    * Oryginalny *widok statyczny* zgodności usługi Azure CIS 1.1.0 również pozostanie obok niego. Może zostać automatycznie usunięty w przyszłości.

    > [!NOTE]
    > Może upłynąć kilka godzin, aby nowo dodany standard pojawił się na pulpicie nawigacyjnym zgodności.


    [![Pulpit nawigacyjny zgodności z przepisami przedstawiający stare i nowe usługi Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiedziałeś się:

* Jak **uaktualnić standardy** wyświetlane na pulpicie nawigacyjnym zgodności z przepisami do nowych pakietów *dynamicznych*
* Jak **dodać pakiety zgodności,** aby monitorować zgodność z dodatkowymi standardami. 

W przypadku innych powiązanych materiałów zobacz następujące artykuły: 

- [Pulpit nawigacyjny zgodności z przepisami centrum zabezpieczeń](security-center-compliance-dashboard.md)
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — dowiedz się, jak używać zaleceń w usłudze Azure Security Center w celu ochrony zasobów platformy Azure.