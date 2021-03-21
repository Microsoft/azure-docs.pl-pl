---
title: Informacje na temat sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
description: Dowiedz się, jak użytkownicy są przypisani do aplikacji, która używa Azure Active Directory do zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257444"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Informacje na temat sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
Ten artykuł ułatwia zrozumienie sposobu, w jaki użytkownicy są przypisani do aplikacji w dzierżawie.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak użytkownicy są przypisani do aplikacji w usłudze Azure AD?
Aby użytkownik mógł uzyskać dostęp do aplikacji, musi zostać najpierw przypisany do niej w jakiś sposób. Przypisanie może być wykonywane przez administratora, delegata biznesowego lub czasami samego użytkownika. Poniżej opisano sposób, w jaki użytkownicy mogą uzyskać przypisane do aplikacji:

*  Administrator [przypisuje użytkownikowi](./assign-user-or-group-access-portal.md) bezpośrednio aplikację
*  Administrator [przypisuje grupę](./assign-user-or-group-access-portal.md) , do której należy użytkownik, w tym:
    * Grupa, która została zsynchronizowana z poziomu lokalnego
    * Statyczna Grupa zabezpieczeń utworzona w chmurze
    * [Dynamiczna grupa zabezpieczeń](../enterprise-users/groups-dynamic-membership.md) utworzona w chmurze
    * Grupa Microsoft 365 utworzona w chmurze
    * Grupa [Wszyscy użytkownicy](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Administrator umożliwia [samoobsługowy dostęp do aplikacji](./manage-self-service-access.md) , aby umożliwić użytkownikowi dodawanie aplikacji przy użyciu funkcji [](../user-help/my-apps-portal-end-user-access.md) **Dodaj** aplikacje do aplikacji **bez konieczności zatwierdzania firmy**
*  Administrator umożliwia [samoobsługowy dostęp do aplikacji](./manage-self-service-access.md) , aby umożliwić użytkownikowi dodawanie aplikacji za pomocą funkcji [Moje aplikacje](../user-help/my-apps-portal-end-user-access.md) **Dodaj aplikację** , ale tylko **z wcześniejszym zatwierdzeniem z wybranego zestawu osób zatwierdzających firmy** .
*  Administrator umożliwia samoobsługowe [Zarządzanie grupami](../enterprise-users/groups-self-service-management.md) , aby umożliwić użytkownikowi dołączenie do grupy, do której aplikacja jest przypisana **bez zgody firmy**
*  Administrator umożliwia samoobsługowe [Zarządzanie grupami](../enterprise-users/groups-self-service-management.md) , aby umożliwić użytkownikowi dołączenie do grupy, do której przypisano aplikację, ale tylko **z poprzednią zgodą z wybranego zestawu osób zatwierdzających firmy** .
*  Administrator przypisuje licencję do użytkownika bezpośrednio dla aplikacji innej firmy, takiej jak [Microsoft 365](https://products.office.com/)
*  Administrator przypisuje licencję do grupy, do której należy użytkownik, do aplikacji pierwszej firmy, takiej jak [Microsoft 365](https://products.office.com/)
*  [Administrator wyraża zgodę na użycie aplikacji](../develop/howto-convert-app-to-be-multi-tenant.md) przez wszystkich użytkowników, a następnie loguje się do aplikacji.
* Użytkownik [wyraża zgodę na samą aplikację](../develop/howto-convert-app-to-be-multi-tenant.md) , logując się do aplikacji

## <a name="next-steps"></a>Następne kroki
* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
* [Co to jest zarządzanie aplikacjami?](what-is-application-management.md)
* [Co to jest logowanie jednokrotne?](what-is-single-sign-on.md)