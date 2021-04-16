---
title: Opis sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
description: Dowiedz się, jak użytkownicy są przypisywani do aplikacji, która używa usługi Azure Active Directory do zarządzania tożsamościami.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: 84700bca6ff306dbcce01a837c312c4c0c90066d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376413"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Opis sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
Ten artykuł pomaga zrozumieć, w jaki sposób użytkownicy są przypisywani do aplikacji w dzierżawie.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak użytkownicy są przypisywani do aplikacji w usłudze Azure AD?
Aby użytkownik uzyskać dostęp do aplikacji, musi najpierw zostać do niego przypisany w jakiś sposób. Przypisanie może być wykonywane przez administratora, delegata biznesowego lub czasami użytkownika. Poniżej opisano sposoby przypisywania użytkowników do aplikacji:

*  Administrator [przypisuje użytkownika bezpośrednio](./assign-user-or-group-access-portal.md) do aplikacji
*  Administrator [przypisuje do aplikacji grupę,](./assign-user-or-group-access-portal.md) do których należy użytkownik, w tym:
    * Grupa, która została zsynchronizowana ze środowisku lokalnym
    * Statyczna grupa zabezpieczeń utworzona w chmurze
    * Dynamiczna [grupa zabezpieczeń utworzona](../enterprise-users/groups-dynamic-membership.md) w chmurze
    * Grupa Microsoft 365 utworzona w chmurze
    * Grupa [Wszyscy](../fundamentals/active-directory-groups-create-azure-portal.md) użytkownicy
*  Administrator umożliwia dostęp [do aplikacji samoobsługowej,](./manage-self-service-access.md) aby umożliwić [](../user-help/my-apps-portal-end-user-access.md) użytkownikowi dodawanie aplikacji przy użyciu Moje aplikacje **Dodaj aplikację** bez **zatwierdzania firmy**
*  Administrator umożliwia [dostęp](./manage-self-service-access.md) do aplikacji samoobsługowej, aby umożliwić użytkownikowi dodawanie aplikacji przy użyciu funkcji [Moje aplikacje](../user-help/my-apps-portal-end-user-access.md) **Dodaj** aplikację, ale tylko po wcześniejszym zatwierdzeniu przez wybrany zestaw osób zatwierdzających w **firmie**
*  Administrator umożliwia [samoobsługowe zarządzanie grupą w](../enterprise-users/groups-self-service-management.md) celu umożliwienia użytkownikowi dołączenia do grupy, do których przypisano aplikację bez zatwierdzenia **firmy**
*  Administrator umożliwia [samoobsługowe zarządzanie](../enterprise-users/groups-self-service-management.md) grupą, aby umożliwić użytkownikowi dołączanie do grupy, do których przypisano aplikację, ale tylko po wcześniejszym zatwierdzeniu przez wybrany zestaw osób zatwierdzających **w firmie**
*  Administrator przypisuje licencję do użytkownika bezpośrednio dla aplikacji pierwszej firmy, na przykład [Microsoft 365](https://products.office.com/)
*  Administrator przypisuje licencję do grupy, do których należy użytkownik, na przykład do [aplikacji Microsoft 365](https://products.office.com/)
*  Administrator [wyraża zgodę na to, aby aplikacja](../develop/howto-convert-app-to-be-multi-tenant.md) została użyta przez wszystkich użytkowników, a następnie użytkownik się do aplikacji
* Użytkownik wyraża [zgodę na aplikację samodzielnie,](../develop/howto-convert-app-to-be-multi-tenant.md) logując się do aplikacji

## <a name="next-steps"></a>Następne kroki
* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
* [Co to jest zarządzanie aplikacjami?](what-is-application-management.md)
* [Co to jest logowanie pojedyncze?](what-is-single-sign-on.md)