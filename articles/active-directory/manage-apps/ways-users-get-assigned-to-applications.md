---
title: Informacje na temat sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
description: Dowiedz się, jak użytkownicy są przypisani do aplikacji, która używa Azure Active Directory do zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604159"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Informacje na temat sposobu przypisywania użytkowników do aplikacji w Azure Active Directory
Ten artykuł ułatwia zrozumienie sposobu, w jaki użytkownicy są przypisani do aplikacji w dzierżawie.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak użytkownicy są przypisani do aplikacji w usłudze Azure AD?
Aby użytkownik mógł uzyskać dostęp do aplikacji, musi zostać najpierw przypisany do niej w jakiś sposób. Przypisanie może być wykonywane przez administratora, delegata biznesowego lub czasami samego użytkownika. Poniżej opisano sposób, w jaki użytkownicy mogą uzyskać przypisane do aplikacji:

*  Administrator [przypisuje użytkownikowi](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) bezpośrednio aplikację
*  Administrator [przypisuje grupę](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , do której należy użytkownik, w tym:
    * Grupa, która została zsynchronizowana z poziomu lokalnego
    * Statyczna Grupa zabezpieczeń utworzona w chmurze
    * [Dynamiczna grupa zabezpieczeń](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) utworzona w chmurze
    * Grupa Microsoft 365 utworzona w chmurze
    * Grupa [Wszyscy użytkownicy](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Administrator umożliwia [samoobsługowy dostęp do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , aby umożliwić użytkownikowi dodawanie aplikacji przy użyciu funkcji [My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Dodaj** aplikacje do aplikacji **bez konieczności zatwierdzania firmy**
*  Administrator umożliwia [samoobsługowy dostęp do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) , aby umożliwić użytkownikowi dodawanie aplikacji za pomocą funkcji [Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Dodaj aplikację** , ale tylko **z wcześniejszym zatwierdzeniem z wybranego zestawu osób zatwierdzających firmy** .
*  Administrator umożliwia samoobsługowe [Zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , aby umożliwić użytkownikowi dołączenie do grupy, do której aplikacja jest przypisana **bez zgody firmy**
*  Administrator umożliwia samoobsługowe [Zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) , aby umożliwić użytkownikowi dołączenie do grupy, do której przypisano aplikację, ale tylko **z poprzednią zgodą z wybranego zestawu osób zatwierdzających firmy** .
*  Administrator przypisuje licencję do użytkownika bezpośrednio dla aplikacji innej firmy, takiej jak [Microsoft 365](https://products.office.com/)
*  Administrator przypisuje licencję do grupy, do której należy użytkownik, do aplikacji pierwszej firmy, takiej jak [Microsoft 365](https://products.office.com/)
*  [Administrator wyraża zgodę na użycie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) przez wszystkich użytkowników, a następnie loguje się do aplikacji.
* Użytkownik [wyraża zgodę na samą aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , logując się do aplikacji

## <a name="next-steps"></a>Następne kroki
* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
* [Co to jest zarządzanie aplikacjami?](what-is-application-management.md)
* [Co to jest logowanie jednokrotne?](what-is-single-sign-on.md)
