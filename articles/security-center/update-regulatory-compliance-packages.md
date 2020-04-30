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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537784"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Aktualizowanie dynamicznych pakietów zgodności na pulpicie nawigacyjnym zgodności z przepisami

Azure Security Center ciągle porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, regulacji i testów porównawczych. **Pulpit nawigacyjny zgodności z przepisami** zapewnia wgląd w stan zgodności w zależności od tego, jak spełniasz określone wymagania kontroli zgodności.

Jednym standardem, dla którego można śledzić stan zgodności, jest [usługa Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (bardziej formalnie — wersja testowa "CIS Microsoft Azure w wersji próbnej"). 

Reprezentacja usługi Azure CIS, która początkowo pojawia się na pulpicie nawigacyjnym zgodności, bazuje na statycznym zestawie reguł, które są zawarte w Security Center.

Przy użyciu funkcji **dynamicznych pakietów zgodności** Security Center automatycznie ulepsza jej pokrycie standardów branżowych w miarę upływu czasu. Pakiety zgodności są zasadniczo inicjatywami zdefiniowanymi w Azure Policy. Mogą być przypisane do wybranego zakresu (subskrypcja, Grupa zarządzania itd.). Aby wyświetlić dane zgodności mapowane jako oceny na pulpicie nawigacyjnym, Dodaj pakiet zgodności do grupy zarządzania lub subskrypcji z poziomu zasad zabezpieczeń. Dodanie pakietu zgodności skutecznie przypisuje inicjatywę zgodności z przepisami do wybranego zakresu. W ten sposób można śledzić nowo opublikowane inicjatywy prawne jako standardy zgodności na pulpicie nawigacyjnym. Gdy firma Microsoft wystawia nową zawartość dla inicjatywy (nowe zasady, które mapują do większej liczby kontrolek w standardzie), dodatkowa zawartość zostanie automatycznie wyświetlona na pulpicie nawigacyjnym.

Dynamiczny pakiet zgodności dla usługi Azure CIS dla testów porównawczych, **Azure CIS 1.1.0 (NOWOŚĆ)**, usprawnia w pierwotnej wersji *statycznej* przez:

* Uwzględnienie więcej zasad
* Automatyczne aktualizowanie o nowe pokrycie po jego dodaniu 

Zaktualizuj do nowego pakietu dynamicznego zgodnie z poniższym opisem.

## <a name="adding-a-dynamic-compliance-package"></a>Dodawanie pakietu zgodności dynamicznej

Poniższe kroki wyjaśniają, jak dodać pakiet dynamiczny do monitorowania zgodności z usługą Azure CIS test porównawczy 1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Aktualizacja pakietu zgodności dynamicznej usługi Azure CIS 1.1.0 (nowy) 

1. Otwórz stronę **zasady zabezpieczeń** . Ta strona zawiera liczbę grup zarządzania, subskrypcji, obszarów roboczych i struktury grupy zarządzania.

1. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz zarządzać stan zgodności z przepisami. Zalecamy wybranie najwyższego zakresu, dla którego stosuje się standard, aby dane zgodności były agregowane i śledzone dla wszystkich zasobów zagnieżdżonych. 

1. W sekcji dotyczącej branżowych & standardów prawnych zobaczysz, że usługi Azure CIS 1.1.0 można zaktualizować pod kątem nowej zawartości. Kliknij przycisk **Aktualizuj teraz**. 

1. Opcjonalnie kliknij pozycję **Dodaj więcej standardów** , aby otworzyć stronę **Dodaj standardy zgodności z przepisami** . W tym miejscu możesz wyszukiwać ręcznie **usługi Azure CIS 1.1.0 (nowe)** i pakiety dynamiczne dla innych standardów zgodności, takich jak **NIST SP 800-53 R4**, **SWIFT CSP CSCF-V2020**, **uko i brytyjski NHS**i **Kanada PBMM**.
    
    > [!TIP]
    > Tylko użytkownicy, którzy są właścicielami lub współautorem zasad, mają uprawnienia niezbędne do dodawania standardów zgodności. 

    ![Dodawanie pakietów wykonawczych do pulpitu nawigacyjnego zgodności z przepisami Azure Security Center](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Na pasku bocznym Security Center wybierz pozycję **zgodność z przepisami** , aby otworzyć pulpit nawigacyjny zgodności z przepisami. 
    * Usługa Azure CIS 1.1.0 (Nowa) jest teraz wyświetlana na liście standardów branżowych &. 
    * Oryginalny widok *statyczny* zgodności usługi Azure CIS 1.1.0 również pozostanie obok niego. Może zostać automatycznie usunięta w przyszłości.

    > [!NOTE]
    > Dodanie nowego standardu do pulpitu nawigacyjnego zgodności może potrwać kilka godzin.


    [![Pulpit nawigacyjny zgodności z przepisami zawierający stare i nowe usługi Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono następujące informacje:

* Jak **uaktualnić standardy** widoczne na pulpicie nawigacyjnym zgodności z przepisami na nowe pakiety *dynamiczne*
* Jak **dodać pakiety zgodności** w celu monitorowania zgodności z dodatkowymi standardami. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Pulpit nawigacyjny zgodności z przepisami Centrum zabezpieczeń](security-center-compliance-dashboard.md)
- [Praca z zasadami zabezpieczeń](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Azure Security Center](security-center-recommendations.md) — Dowiedz się, jak używać zaleceń w programie Azure Security Center, aby pomóc w ochronie zasobów platformy Azure.