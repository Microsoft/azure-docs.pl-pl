---
title: Zarządzanie dostępem zewnętrznym przy użyciu Azure Active Directory dostępu warunkowego
description: Jak korzystać z zasad dostępu warunkowego Azure Active Directory, aby zabezpieczyć zewnętrzny dostęp do zasobów.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222313"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Zarządzanie dostępem zewnętrznym przy użyciu zasad dostępu warunkowego 

[Dostęp warunkowy](../conditional-access/overview.md) jest narzędziem wykorzystywanym przez usługę Azure AD do łączenia sygnałów, wymuszania zasad i określania, czy użytkownik powinien mieć dostęp do zasobów. Aby uzyskać szczegółowe informacje na temat tworzenia i używania zasad dostępu warunkowego (zasady dostępu warunkowego), zobacz [Planowanie wdrożenia dostępu warunkowego](../conditional-access/plan-conditional-access.md). 

![Diagram sygnałów dostępu warunkowego i decyzji](media/secure-external-access//7-conditional-access-signals.png)



W tym artykule omówiono stosowanie zasad dostępu warunkowego do użytkowników zewnętrznych i przyjęto założenie, że nie masz dostępu do funkcji [zarządzania prawami](../governance/entitlement-management-overview.md) . Zasady dostępu warunkowego mogą być i są używane razem z zarządzaniem prawami.

Wcześniej w tym zestawie dokumentów został [utworzony plan zabezpieczeń](3-secure-access-plan.md) , który zawiera opis:

* Aplikacje i zasoby mają te same wymagania dotyczące zabezpieczeń i mogą być pogrupowane w celu uzyskania dostępu.

* Wymagania dotyczące logowania dla użytkowników zewnętrznych.

Ten plan zostanie użyty do utworzenia zasad dostępu warunkowego dla dostępu zewnętrznego. 

> [!IMPORTANT]
> Utwórz kilka zewnętrznych kont testów użytkowników, aby umożliwić przetestowanie utworzonych zasad przed ich zastosowaniem do wszystkich użytkowników zewnętrznych.

## <a name="conditional-access-policies-for-external-access"></a>Zasady dostępu warunkowego dla dostępu zewnętrznego

Poniżej przedstawiono najlepsze rozwiązania dotyczące zarządzania dostępem zewnętrznym przy użyciu zasad dostępu warunkowego.

* Jeśli nie możesz użyć połączonych organizacji w usłudze zarządzania prawami, Utwórz grupę zabezpieczeń usługi Azure AD lub grupę Microsoft 365 dla każdej organizacji partnera, z którą pracujesz. Przypisz wszystkim użytkownikom z tego partnera do grupy. Następnie można używać tych grup w zasadach dostępu warunkowego.

* Utwórz możliwie najmniejsze zasady dostępu warunkowego. W przypadku aplikacji, które mają taki sam dostęp, należy dodać je wszystkie do tych samych zasad.  
‎ 
   > [!NOTE]
   > Zasady dostępu warunkowego mogą dotyczyć maksymalnie 250 aplikacji. Jeśli więcej niż 250 aplikacji ma takie same potrzeby dostępu, Utwórz zduplikowane zasady. Zasady A zostaną zastosowane do aplikacji 1-250, zasady B będą stosowane do aplikacji 251-500 itd.

* Jasno nazywaj zasady specyficzne dla dostępu zewnętrznego z konwencją nazewnictwa. Jedna Konwencja nazewnictwa jest *ExternalAccess_actiontaken_AppGroup*. Na przykład ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Blokuj wszystkich użytkowników zewnętrznych z zasobów

Można zablokować użytkownikom zewnętrznym dostęp do określonych zestawów zasobów przy użyciu zasad dostępu warunkowego. Po ustaleniu zestawu zasobów, do których ma zostać zablokowany dostęp, Utwórz zasady.

Aby utworzyć zasadę, która blokuje dostęp użytkownikom zewnętrznym do zestawu aplikacji:

1. Uzyskaj dostęp do **Azure Portal**, wybierz pozycję **Azure Active Directory**, wybierz pozycję **zabezpieczenia**, a następnie wybierz pozycję **dostęp warunkowy**.

2. Wybierz pozycję **nowe zasady**, a następnie wprowadź **nazwę**, na przykład ExternalAccess_Block_FinanceApps

3. Wybierz pozycję **Użytkownicy i grupy**. Na karcie Dołącz wybierz **pozycję Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Wszyscy Goście i użytkownicy zewnętrzni**. 

4. Wybierz pozycję **Wyklucz** , a następnie wprowadź grupy administratorów i wszystkie konta dostępu awaryjnego (po awarii).

5. Wybierz pozycję **aplikacje w chmurze lub akcje**, wybierz **pozycję Wybierz aplikacje**, zaznacz wszystkie aplikacje, do których chcesz zablokować dostęp zewnętrzny, a następnie wybierz **pozycję Wybierz**.

6. Wybierz pozycję **warunki**, wybierz pozycję **lokalizacje**, w obszarze Konfiguracja wybierz opcję **tak**, a następnie wybierz **dowolną lokalizację**.

7. W obszarze kontrole dostępu wybierz pozycję **Udziel**, Zmień przełącznik na **blok**, a następnie wybierz **pozycję Wybierz**.

8. Upewnij się, że ustawienie Włącz zasady ma wartość **tylko raport**, a następnie wybierz pozycję **Utwórz**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Blokuj dostęp zewnętrzny do wszystkich poza określonymi użytkownikami zewnętrznymi

Mogą wystąpić sytuacje, w których chcesz zablokować użytkowników zewnętrznych, z wyjątkiem określonej grupy. Na przykład możesz chcieć zablokować wszystkim użytkownikom zewnętrznym, z wyjątkiem tych, którzy pracują z aplikacjami finansowymi. W tym celu:

1. Utwórz grupę zabezpieczeń, aby pomieścić użytkowników zewnętrznych, którzy powinni mieć dostęp do grupy finanse.

2. Wykonaj kroki 1-3 w bloku dostęp zewnętrzny z zasobów powyżej.

3. W kroku 4 Dodaj grupę zabezpieczeń, która ma zostać wykluczona z aplikacji finanse.

4. Wykonaj pozostałe kroki.

## <a name="implement-conditional-access"></a>Zaimplementuj dostęp warunkowy

Udokumentowano wiele typowych zasad dostępu warunkowego. Zobacz, co można dostosować do użytkowników zewnętrznych.

* [Wymagaj Multi-Factor Authentication dla wszystkich użytkowników](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Dostęp warunkowy oparty na ryzyku użytkownika](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Wymagaj Multi-Factor Authentication dostępu z niezaufanych sieci](../conditional-access/untrusted-networks.md) 

* [Wymagaj warunków użytkowania](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określ żądany stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp za pomocą zasad dostępu warunkowego](7-secure-access-conditional-access.md) (Jesteś tutaj)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)
