---
title: Zmiana planów licencji dla użytkowników i grup — Azure AD | Microsoft Docs
description: Jak migrować użytkowników w grupie do różnych planów usług przy użyciu licencjonowania grupowego w Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: enterprise-users
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ec9a215d25ce1177b015b392c4f36d7547be521
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647022"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Zmień przypisania licencji dla użytkownika lub grupy w Azure Active Directory

W tym artykule opisano sposób przenoszenia użytkowników i grup między planami licencji usługi w Azure Active Directory (Azure AD). Podejście do usługi Azure AD ma na celu upewnienie się, że podczas zmiany licencji nie utracisz usługi ani danych. Użytkownicy powinni bezproblemowo przełączać się między usługami. W krokach przydziału planu licencji w tym artykule opisano zmianę użytkownika lub grupy w pakiecie Office 365 E1 na pakiet Office 365 E3, ale kroki dotyczą wszystkich planów licencji. Podczas aktualizowania przypisań licencji dla użytkownika lub grupy, usuwanie przypisania licencji i nowe przypisania są wprowadzane jednocześnie, aby użytkownicy nie utracili dostępu do swoich usług podczas zmian licencji lub zobaczą konflikty licencji między planami.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed zaktualizowaniem przypisań licencji należy sprawdzić, czy pewne założenia są spełnione dla wszystkich użytkowników lub grup, które mają zostać zaktualizowane. Jeśli założenia nie są spełnione dla wszystkich użytkowników w grupie, migracja może zakończyć się niepowodzeniem. W związku z tym niektórzy użytkownicy mogą utracić dostęp do usług lub danych. Upewnij się, że:

- Użytkownicy mają bieżący plan licencji (w tym przypadku pakiet Office 365 E1), który jest przypisany do grupy i dziedziczony przez użytkownika i nie został przypisany bezpośrednio.

- Masz wystarczającą liczbę dostępnych licencji na przypisywany plan licencji. Jeśli nie masz wystarczającej liczby licencji, niektórzy użytkownicy mogą nie mieć przypisanego nowego planu licencjonowania. Możesz sprawdzić liczbę dostępnych licencji.

- Użytkownicy nie mają innych przypisanych licencji usługi, które mogą powodować konflikt z żądaną licencją lub uniemożliwiają usunięcie bieżącej licencji. Na przykład Licencja z usługi, np. Analiza w miejscu pracy lub Usługa Project Online, która jest zależna od innych usług.

- W przypadku zarządzania grupami lokalnymi i synchronizowania ich w usłudze Azure AD za pośrednictwem Azure AD Connect, należy dodać lub usunąć użytkowników przy użyciu systemu lokalnego. Synchronizacja zmian z usługą Azure AD w celu pobrania ich przez Licencjonowanie grup może zająć trochę czasu.

- Jeśli używasz członkostwa w grupie dynamicznej usługi Azure AD, możesz dodawać i usuwać użytkowników, zmieniając ich atrybuty, ale proces aktualizacji dla przypisań licencji pozostaje taki sam.

## <a name="change-user-license-assignments"></a>Zmień przypisania licencji użytkownika

Na stronie **Aktualizowanie przypisań licencji** , Jeśli zobaczysz, że niektóre pola wyboru są niedostępne, wskazuje usługi, których nie można zmienić, ponieważ są dziedziczone z licencji grupy.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**, a następnie otwórz stronę **profilu** dla użytkownika.
1. Wybierz pozycję **licencje**.
1. Wybierz pozycję **przypisania** , aby edytować przypisanie licencji dla użytkownika lub grupy. Na stronie **przypisania** można rozwiązać konflikty przypisywania licencji.
1. Zaznacz pole wyboru dla pakietu Office 365 E3 i upewnij się, że wybrano co najmniej wszystkie usługi E1 przypisane do użytkownika.
1. Wyczyść pole wyboru dla pakietu Office 365 E1.

    ![Strona przypisania licencji dla użytkownika pokazującego wyczyszczone pakiet Office 365 E1 i zaznaczona opcja pakiet Office 365 E3](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Wybierz pozycję **Zapisz**.

Usługa Azure AD stosuje nowe licencje i usuwa stare licencje jednocześnie w celu zapewnienia ciągłości usługi.

## <a name="change-group-license-assignments"></a>Zmień przypisania licencji grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.
1. Wybierz pozycję **Azure Active Directory**  >  **grupy**, a następnie otwórz stronę **Przegląd** dla grupy.
1. Wybierz pozycję **licencje**.
1. Wybierz polecenie **przypisania** , aby edytować przypisanie licencji dla użytkownika lub grupy.
1. Zaznacz pole wyboru dla pakietu Office 365 E3. Aby zachować ciągłość działania usługi, upewnij się, że wybrano wszystkie usługi E1, które są już przypisane do użytkownika.
1. Wyczyść pole wyboru dla pakietu Office 365 E1.

    ![Wybierz polecenie przypisania na stronie licencji użytkownika lub grupy](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Wybierz pozycję **Zapisz**.

Aby zapewnić ciągłość usługi, usługa Azure AD stosuje nowe licencje i usuwa stare licencje jednocześnie dla wszystkich użytkowników w grupie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi scenariuszami zarządzania licencjami za pomocą grup w następujących artykułach:

- [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrować pojedynczych licencjonowanych użytkowników do licencjonowania grupowego w Azure Active Directory](licensing-groups-migrate-users.md)
- [Dodatkowe scenariusze licencjonowania grup Azure Active Directory](licensing-group-advanced.md)
- [Przykłady programu PowerShell do licencjonowania grupowego w Azure Active Directory](licensing-ps-examples.md)
