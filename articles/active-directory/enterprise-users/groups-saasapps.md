---
title: Używanie grupy do zarządzania dostępem do aplikacji SaaS — Azure AD | Microsoft Docs
description: Jak używać grup w Azure Active Directory do przypisywania dostępu do aplikacji SaaS zintegrowanych z Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58d5cc64b21207efbdefdafe020248212eed7efe
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650567"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Używanie grupy do zarządzania dostępem do aplikacji SaaS

Korzystając z Azure Active Directory (Azure AD) z planem licencjonowania Azure AD — wersja Premium, możesz użyć grup, aby przypisać dostęp do aplikacji SaaS zintegrowanej z usługą Azure AD. Na przykład jeśli chcesz przypisać dostęp dla działu marketingu do korzystania z pięciu różnych aplikacji SaaS, możesz utworzyć grupę zawierającą użytkowników w dziale marketingu, a następnie przypisać tę grupę do tych pięciu aplikacji SaaS, które są wymagane przez dział marketingu. W ten sposób można zaoszczędzić czas, zarządzając członkostwem działu marketingu w jednym miejscu. Następnie użytkownicy są przypisani do aplikacji, gdy zostaną dodani jako członkowie grupy Marketing i zostaną usunięci z aplikacji po ich usunięciu z grupy marketingowej. Ta funkcja może być używana z setkami aplikacji, które można dodać z poziomu galerii aplikacji usługi Azure AD.

> [!IMPORTANT]
> Tej funkcji można użyć dopiero po rozpoczęciu planu Azure AD — wersja Premium wersji próbnej lub zakupu Azure AD — wersja Premium.
> Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń.
> Aktualnie w ramach przypisywania do aplikacji na podstawie grup nie jest obsługiwane członkostwo w grupach zagnieżdżonych.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Aby przypisać dostęp dla użytkownika lub grupy do aplikacji SaaS

1. W [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com)wybierz pozycję **aplikacje dla przedsiębiorstw**.
2. Wybierz aplikację dodaną z galerii aplikacji, aby ją otworzyć.
3. Wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz pozycję **Dodaj użytkownika**.
4. Na stronie **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** , aby otworzyć listę Wybieranie **użytkowników i grup** .
6. Wybierz dowolną liczbę grup lub użytkowników, a następnie kliknij lub naciśnij pozycję **Wybierz** , aby dodać je do listy **Dodaj przypisanie** . Na tym etapie można także przypisać rolę do użytkownika.
7. Wybierz pozycję **Przypisz** , aby przypisać użytkowników lub grupy do wybranej aplikacji przedsiębiorstwa.

## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
