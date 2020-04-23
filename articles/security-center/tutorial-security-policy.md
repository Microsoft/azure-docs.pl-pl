---
title: Praca z zasadami bezpieczeństwa | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób pracy z zasadami zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024884"
---
# <a name="working-with-security-policies"></a>Praca z zasadami zabezpieczeń

W tym artykule wyjaśniono, jak skonfigurowano zasady zabezpieczeń i jak je wyświetlać w Centrum zabezpieczeń. 

## <a name="introduction-to-security-policies"></a>Wprowadzenie do zasad bezpieczeństwa

Zasady zabezpieczeń definiują żądaną konfigurację obciążeń i pomagają upewnić się, że spełniasz wymagania dotyczące zabezpieczeń firmy lub organów regulacyjnych.

Usługa Azure Security Center udostępnia zalecenia dotyczące zabezpieczeń na podstawie wybranych zasad. Zasady usługi Security Center są oparte na inicjatywach zasad utworzonych w usłudze Azure Policy. Za pomocą [usługi Azure Policy](../governance/policy/overview.md) można zarządzać zasadami i ustawiać zasady w grupach zarządzania i w wielu subskrypcjach.

Usługa Security Center oferuje następujące opcje pracy z zasadami zabezpieczeń:

* **Wyświetlanie i edytowanie wbudowanych zasad domyślnych** — po włączeniu usługi Security Center wbudowana inicjatywa o nazwie "Domyślny asc" jest automatycznie przypisywana do wszystkich subskrypcji zarejestrowanych w umorzynym u centrum zabezpieczeń (warstwy bezpłatne lub standardowe). Aby dostosować tę inicjatywę, można włączyć lub wyłączyć poszczególne zasady w niej. Zobacz listę [wbudowanych zasad zabezpieczeń,](security-center-policy-definitions.md) aby zapoznać się z opcjami dostępnymi po wyjęciu z pudełka.

* **Dodaj własne zasady niestandardowe** — jeśli chcesz dostosować inicjatywy zabezpieczeń zastosowane do subskrypcji, możesz to zrobić w u centrum zabezpieczeń. Następnie otrzymasz rekomendacje, jeśli twoje maszyny nie zastosują się do utworzonych zasad. Aby uzyskać instrukcje dotyczące tworzenia i przypisywania zasad niestandardowych, zobacz [Korzystanie z niestandardowych zasad zabezpieczeń](custom-security-policies.md).

* **Dodaj zasady zgodności z przepisami** — pulpit nawigacyjny zgodności z przepisami usługi Security Center pokazuje stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub regulacji (takich jak Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Aby uzyskać więcej informacji, zobacz [Poprawianie zgodności z przepisami](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Zarządzanie zasadami zabezpieczeń

Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

1. Na pulpicie nawigacyjnym **Centrum zabezpieczeń** wybierz pozycję **Zasady zabezpieczeń**.

    ![Okienko Zarządzanie zasadami](./media/security-center-policies/security-center-policy-mgt.png)

   Na ekranie **Zarządzanie zasadami** można wyświetlić liczbę grup zarządzania, subskrypcji i obszarów roboczych, a także strukturę grupy zarządzania.

1. Wybierz subskrypcję lub grupę zarządzania, której zasady chcesz wyświetlić.

1. Zostanie wyświetlona strona zasad zabezpieczeń dla tej subskrypcji lub grupy zarządzania. Pokazuje dostępne i przypisane zasady.

   ![ekran zasad](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Jeśli obok domyślnej zasady znajduje się etykieta "MG Inherited", oznacza to, że zasada została przypisana do grupy zarządzania i dziedziczona przez przeglądaną subskrypcję.


1. Wybierz jedną z dostępnych opcji na tej stronie:

    1. Aby pracować z zasadami branżowymi, wybierz **pozycję Dodaj więcej standardów**. Aby uzyskać więcej informacji, zobacz [Aktualizowanie do dynamicznych pakietów zgodności](update-regulatory-compliance-packages.md).

    1. Aby przypisać inicjatywy niestandardowe i zarządzać nimi, wybierz pozycję **Dodaj inicjatywy niestandardowe**. Aby uzyskać więcej informacji, zobacz [Korzystanie z niestandardowych zasad zabezpieczeń](custom-security-policies.md).

    1. Aby wyświetlić i edytować zasady domyślne, wybierz pozycję **Wyświetl skuteczne zasady** i postępuj zgodnie z poniższymi opisami. 

       ![ekran zasad](./media/security-center-policies/policy-screen.png)
       
       Ten ekran **zasad zabezpieczeń** odzwierciedla działania podejmowane przez zasady przypisane do wybranej subskrypcji lub grupy zarządzania.
       
       * Użyj łączy u góry, aby otworzyć **przypisanie** zasad, które ma zastosowanie do subskrypcji lub grupy zarządzania. Te łącza umożliwiają dostęp do przypisania oraz edytowanie lub wyłączanie zasad. Na przykład jeśli widzisz, że określone przypisanie zasad skutecznie odmawia ochrony punktu końcowego, użyj łącza, aby edytować lub wyłączyć zasady.
       
       * Na liście zasad można zobaczyć skuteczne stosowanie zasad w ramach subskrypcji lub grupy zarządzania. Ustawienia każdej zasady, które mają zastosowanie do zakresu są brane pod uwagę i skumulowany wynik działań podejmowanych przez zasady jest wyświetlany. Na przykład jeśli w jednym przypisaniu zasad jest wyłączona, ale w innym jest ustawiona na AuditIfNotExist, a następnie skumulowany efekt stosuje AuditIfNotExist. Bardziej aktywny efekt zawsze ma pierwszeństwo.
       
       * Efekt zasad może być: Dołącz, Inspekcja, AuditIfNotExists, Deny, DeployIfNotExists, Wyłączone. Aby uzyskać więcej informacji na temat sposobu stosowania efektów, zobacz [Opis efektów zasad](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Podczas wyświetlania przypisanych zasad można zobaczyć wiele przypisań i zobaczyć, jak każde przypisanie jest skonfigurowane samodzielnie.


## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?

Zasady zabezpieczeń można edytować za pośrednictwem portalu Azure Policy, za pośrednictwem interfejsu API REST lub programu Windows PowerShell.

Usługa Security Center używa kontroli dostępu opartej na rolach udostępniającej wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Gdy użytkownicy otwierają Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, do których mają dostęp. Oznacza to, że użytkownikom przypisano rolę *właściciela,* *współautora*lub *czytelnika* do subskrypcji zasobu. Oprócz tych ról istnieją dwie określone role w centrum zabezpieczeń:

- **Czytnik zabezpieczeń:** mają prawa do wyświetlania w centrum zabezpieczeń, które zawiera zalecenia, alerty, zasady i kondycję, ale nie mogą wprowadzać zmian.
- **Administrator zabezpieczeń:** Mają takie same prawa do widoku jak *czytnik zabezpieczeń,* a także mogą aktualizować zasady zabezpieczeń i odrzucać zalecenia i alerty.


## <a name="disable-security-policies"></a>Wyłączanie zasad zabezpieczeń
Jeśli domyślna zasada zabezpieczeń generuje zalecenie, które nie jest istotne dla danego środowiska, można go zatrzymać, wyłączając definicję zasad, która wysyła zalecenie.
Aby uzyskać więcej informacji na temat zaleceń, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md).

1. W Centrum zabezpieczeń w sekcji **Zasady & Zgodność** wybierz pozycję Zasady **zabezpieczeń**.

   ![zarządzanie polityką](./media/tutorial-security-policy/policy-management.png)

2. Wybierz subskrypcję lub grupę zarządzania, dla której chcesz wyłączyć zalecenie.

   > [!NOTE]
   > Pamiętaj, że grupa zarządzania stosuje zasady do swoich subskrypcji. W związku z tym wyłączenie zasady subskrypcji, która należy do grupy zarządzania korzystającej nadal z tych samych zasad, sprawi, że będziesz nadal otrzymywać rekomendacje dotyczące zasad. Zasady będą nadal stosowane z poziomu zarządzania i rekomendacje będą nadal generowane.

1. Wybierz **pozycję Wyświetl skuteczne zasady**.

   ![wyłączanie zasad](./media/tutorial-security-policy/view-effective-policy.png)

1. Wybierz przypisane zasady.

   ![wyłączanie zasad](./media/tutorial-security-policy/security-policy.png)

1. W sekcji **PARAMETRY** wyszukaj zasadę, która wywołuje zalecenie, które chcesz wyłączyć, a z listy rozwijanej wybierz pozycję **Wyłączone**

   ![wyłączanie zasad](./media/tutorial-security-policy/disable-policy.png)

1. Wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Wprowadzenie zmian w zasadach wyłączenia może potrwać do 12 godzin.



## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się o zasadach zabezpieczeń. Aby uzyskać powiązane informacje, zobacz następujące artykuły:

* Aby uzyskać instrukcje dotyczące ustawiania zasad przy użyciu programu PowerShell, zobacz [Szybki start: Tworzenie przypisania zasad w celu identyfikowania niezgodnych zasobów przy użyciu modułu Programu Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Aby uzyskać instrukcje dotyczące edytowania zasad zabezpieczeń w zasadach platformy Azure, zobacz Tworzenie zasad i zarządzanie nimi w [celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md).

* Aby uzyskać instrukcje dotyczące ustawiania zasad w ramach subskrypcji lub grup zarządzania przy użyciu zasad platformy Azure, zobacz [Co to jest zasady platformy Azure?](../governance/policy/overview.md)