---
title: Praca z zasadami zabezpieczeń | Microsoft Docs
description: W tym artykule opisano sposób pracy z zasadami zabezpieczeń w programie Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101328"
---
# <a name="manage-security-policies"></a>Zarządzanie zasadami zabezpieczeń

W tym artykule opisano sposób konfigurowania zasad zabezpieczeń oraz sposób ich wyświetlania w Security Center. 

## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?

Zasady zabezpieczeń można edytować za pośrednictwem portalu Azure Policy przy użyciu interfejsu API REST lub środowiska Windows PowerShell.

Security Center używa kontroli dostępu opartej na rolach platformy Azure (Azure RBAC), która udostępnia wbudowane role, które można przypisać do użytkowników, grup i usług platformy Azure. Gdy użytkownicy otworzą Security Center, zobaczą tylko informacje związane z zasobami, do których mogą uzyskać dostęp. Oznacza to, że użytkownicy mają przypisaną rolę *właściciela*, *współautora* lub *czytelnika* do subskrypcji zasobu. Istnieją również dwie konkretne role Security Center:

- **Czytelnik zabezpieczeń**: ma uprawnienia do wyświetlania Security Center elementów, takich jak zalecenia, alerty, zasady i kondycja. Nie można wprowadzić zmian.
- **Administrator zabezpieczeń**: ma takie same uprawnienia do wyświetlania jak *czytelnik zabezpieczeń*. Może także aktualizować zasady zabezpieczeń i odrzucać alerty.

## <a name="manage-your-security-policies"></a>Zarządzanie zasadami zabezpieczeń

Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

1. Na pulpicie nawigacyjnym **Security Center** wybierz pozycję **zasady zabezpieczeń**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Strona Zarządzanie zasadami":::

   Na ekranie **Zarządzanie zasadami** można zobaczyć liczbę grup zarządzania, subskrypcji i obszarów roboczych, a także strukturę grupy zarządzania.

1. Wybierz subskrypcję lub grupę zarządzania, której zasady chcesz wyświetlić.

1. Zostanie wyświetlona strona zasady zabezpieczeń dla tej subskrypcji lub grupy zarządzania. Są w nim wyświetlane zasady dostępne i przypisane.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Strona zasad zabezpieczeń Security Center" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Jeśli istnieje etykieta "MG odziedziczona" wraz z zasadami domyślnymi, oznacza to, że zasady zostały przypisane do grupy zarządzania i są dziedziczone przez przeglądaną subskrypcję.

1. Wybierz spośród dostępnych opcji na tej stronie:

    1. Aby współpracować ze standardami branżowymi, wybierz pozycję **Dodaj więcej standardów**. Aby uzyskać więcej informacji, zobacz [Dostosowywanie zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).

    1. Aby przypisywać niestandardowe inicjatywy i zarządzać nimi, wybierz pozycję **Dodaj niestandardowe inicjatywy**. Aby uzyskać więcej informacji, zobacz [Korzystanie z niestandardowych inicjatyw i zasad dotyczących zabezpieczeń](custom-security-policies.md).

    1. Aby wyświetlić i edytować inicjatywę domyślną, wybierz pozycję **Wyświetl zasady obowiązujące** i postępuj zgodnie z poniższym opisem. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Ekran zasad obowiązujących":::

       Ten ekran **zasad zabezpieczeń** odzwierciedla akcję podejmowaną przez zasady przypisane do wybranej subskrypcji lub grupy zarządzania.
       
       * Użyj linków w górnej części strony, aby otworzyć **przypisanie** zasad, które ma zastosowanie do subskrypcji lub grupy zarządzania. Te linki umożliwiają dostęp do przypisania i edytowanie lub wyłączanie zasad. Jeśli na przykład widzisz, że określone przypisanie zasad skutecznie odmawia ochrony punktu końcowego, Użyj linku do edytowania lub wyłączania zasad.
       
       * Na liście zasad można zobaczyć obowiązującą aplikację zasad w ramach subskrypcji lub grupy zarządzania. Są brane pod uwagę ustawienia poszczególnych zasad, które mają zastosowanie do zakresu, oraz łączny wynik akcji podejmowanych przez zasady. Na przykład, jeśli w jednym przypisaniu zasad jest wyłączone, ale w innym jest ustawiona na AuditIfNotExist, to efekt skumulowany stosuje AuditIfNotExist. Większym aktywnym efektem jest zawsze pierwszeństwo.
       
       * Efektem zasad może być: dołączanie, inspekcja, AuditIfNotExists, Odmów, DeployIfNotExists, wyłączone. Aby uzyskać więcej informacji na temat sposobu stosowania efektów, zobacz [opis efektów zasad](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Podczas wyświetlania przypisanych zasad można zobaczyć wiele przypisań i zobaczyć, jak poszczególne przypisania są skonfigurowane samodzielnie.


## <a name="disable-security-policies-and-disable-recommendations"></a>Wyłączanie zasad zabezpieczeń i wyłączanie zaleceń

Gdy inicjatywa zabezpieczeń wyzwala zalecenie, które nie jest istotne dla danego środowiska, można zapobiec ponownemu wyświetlaniu tego zalecenia. Aby wyłączyć zalecenie, wyłącz określone zasady, które generują zalecenie.

Zalecenie, które ma zostać wyłączone, nadal będzie wyświetlane, jeśli jest wymagane w przypadku normy stosowanej w przypadku zastosowania do narzędzi zgodności z przepisami Security Center. Nawet jeśli zasady zostały wyłączone w ramach inicjatywy wbudowanej, zasady obowiązujące w ramach inicjatywy w ramach normy wykonawczej nadal będą wyzwalać zalecenie, jeśli jest to niezbędne do zapewnienia zgodności. Nie można wyłączyć zasad z inicjatywy standardowych inicjatyw.

Aby uzyskać więcej informacji na temat zaleceń, zobacz [Zarządzanie zaleceniami](security-center-recommendations.md)dotyczącymi zabezpieczeń.

1. W Security Center z sekcji **zgodność zasad &** wybierz pozycję **zasady zabezpieczeń**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Uruchamianie procesu zarządzania zasadami w Azure Security Center":::

2. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz wyłączyć zalecenie.

   > [!NOTE]
   > Pamiętaj, że grupa zarządzania stosuje zasady do swoich subskrypcji. W związku z tym wyłączenie zasady subskrypcji, która należy do grupy zarządzania korzystającej nadal z tych samych zasad, sprawi, że będziesz nadal otrzymywać rekomendacje dotyczące zasad. Zasady będą nadal stosowane z poziomu zarządzania i rekomendacje będą nadal generowane.

1. Wybierz pozycję **Wyświetl czynne zasady**.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Jak otworzyć obowiązujące zasady przypisane do subskrypcji":::

1. Wybierz przypisane zasady.

   ![Wybieranie zasad](./media/tutorial-security-policy/security-policy.png)

1. W sekcji **Parametry** Wyszukaj zasady wywołujące zalecenie, które chcesz wyłączyć, a następnie z listy rozwijanej wybierz pozycję **wyłączone** .

   ![Wyłącz zasady](./media/tutorial-security-policy/disable-policy.png)

1. Wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Wyłączenie zmian zasad może potrwać do 12 godzin.

## <a name="next-steps"></a>Następne kroki
Na tej stronie objaśniono zasady zabezpieczeń. Aby uzyskać powiązane informacje, zobacz następujące strony:

- [Dowiedz się, jak ustawiać zasady przy użyciu programu PowerShell](../governance/policy/assign-policy-powershell.md)
- [Dowiedz się, jak edytować zasady zabezpieczeń w Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Informacje dotyczące ustawiania zasad w ramach subskrypcji lub grup zarządzania przy użyciu Azure Policy](../governance/policy/overview.md)
- [Dowiedz się, jak włączyć Security Center we wszystkich subskrypcjach w grupie zarządzania](onboard-management-group.md)