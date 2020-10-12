---
title: Grupy dynamiczne i współpraca B2B — Azure Active Directory | Microsoft Docs
description: Pokazuje, jak używać grup dynamicznych usługi Azure AD w celu Azure Active Directory współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6adf7e6c5abbba3c018f9a03b5167aec7537c704
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908964"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupy dynamiczne i Azure Active Directory współpracy B2B

## <a name="what-are-dynamic-groups"></a>Co to są grupy dynamiczne?
Dynamiczna konfiguracja przynależności do grupy zabezpieczeń dla Azure Active Directory (Azure AD) jest dostępna w [Azure Portal](https://portal.azure.com). Administratorzy mogą ustawiać reguły do wypełniania grup utworzonych w usłudze Azure AD na podstawie atrybutów użytkownika (takich jak UserType, Department lub Country/region). Elementy członkowskie mogą być automatycznie dodawane lub usuwane z grupy zabezpieczeń na podstawie ich atrybutów. Te grupy mogą zapewniać dostęp do aplikacji lub zasobów w chmurze (witryn programu SharePoint, dokumentów) i przypisywać licencje do członków. Przeczytaj więcej na temat grup dynamicznych w [dedykowanych grupach w Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Do tworzenia grup dynamicznych i korzystania z nich wymagane są [Licencjonowanie w Azure AD — wersja Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/) . Więcej informacji znajduje się w artykule [Tworzenie reguł opartych na atrybutach dla członkostwa w grupie dynamicznej w Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Tworzenie grupy dynamicznej "Wszyscy użytkownicy"
Można utworzyć grupę zawierającą wszystkich użytkowników w dzierżawie przy użyciu reguły członkostwa. Gdy użytkownicy są dodawani lub usuwani z dzierżawy w przyszłości, członkostwo w grupie jest dostosowywane automatycznie.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, któremu przypisano rolę Administrator globalny lub administrator użytkowników w dzierżawie.
1. Wybierz pozycję **Azure Active Directory**.
2. W obszarze **Zarządzaj**wybierz pozycję **grupy**, a następnie wybierz pozycję **Nowa grupa**.
1. Na stronie **Nowa grupa** w obszarze **Typ grupy**wybierz pozycję **zabezpieczenia**. Wypełnij pola **Nazwa grupy** i **Opis grupy** dotyczące nowej grupy. 
2. W obszarze **Typ członkostwa**wybierz pozycję **użytkownik dynamiczny**, a następnie wybierz pozycję **Dodaj zapytanie dynamiczne**. 
4. W polu tekstowym **składnia reguły** wybierz pozycję **Edytuj**. Na stronie **Edytuj składnię reguły** wpisz następujące wyrażenie w polu tekstowym:

   ```
   user.objectId -ne null
   ```
1. Wybierz przycisk **OK**. Reguła zostanie wyświetlona w polu składnia reguły:

   ![Składnia reguły dla grupy dynamicznej wszyscy użytkownicy](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Wybierz pozycję **Zapisz**. Nowa grupa dynamiczna będzie teraz zawierać użytkowników z Gośćmi B2B, a także użytkowników-członków.


1. Na stronie **Nowa grupa** wybierz pozycję **Utwórz** , aby utworzyć grupę.

## <a name="creating-a-group-of-members-only"></a>Tworzenie grupy tylko członków

Jeśli chcesz, aby grupa wykluczać użytkowników-Gości i dołączać tylko członków dzierżawy, Utwórz grupę dynamiczną, jak opisano powyżej, ale w polu **składnia reguły** wprowadź następujące wyrażenie:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Na poniższej ilustracji przedstawiono składnię reguły dla grupy dynamicznej zmodyfikowane w celu uwzględnienia tylko członków i wykluczania Gości.

![Pokazuje regułę, w której typ użytkownika jest równy elementowi członkowskiemu](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Tworzenie grupy tylko Gości

Przydatne może być również utworzenie nowej grupy dynamicznej, która zawiera tylko użytkowników-Gości, dzięki czemu można zastosować do nich zasady (na przykład zasady dostępu warunkowego usługi Azure AD). Utwórz grupę dynamiczną, jak opisano powyżej, ale w polu **składnia reguły** wprowadź następujące wyrażenie:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Na poniższej ilustracji przedstawiono składnię reguły dla grupy dynamicznej zmodyfikowane w celu uwzględnienia tylko Gości i wykluczania użytkowników należących do członków.

![Pokazuje regułę, w której typ użytkownika jest równy gościa](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Następne kroki

- [B2B collaboration user properties (Właściwości użytkowników współpracy B2B)](user-properties.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
- [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md)

