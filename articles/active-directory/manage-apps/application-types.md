---
title: Wyświetlanie aplikacji przy użyciu dzierżawy Azure Active Directory na potrzeby zarządzania tożsamościami
description: Dowiedz się, jak wyświetlać wszystkie aplikacje za pomocą dzierżawy Azure Active Directory na potrzeby zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707887"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Wyświetlanie aplikacji przy użyciu dzierżawy usługi Azure AD na potrzeby zarządzania tożsamościami
[Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md) zawiera podstawowe informacje. W tym temacie dowiesz się, jak wyświetlać wszystkie aplikacje korzystające z dzierżawy usługi Azure AD na potrzeby zarządzania tożsamościami. W tym artykule omówieniach się nieco bardziej szczegółowe informacje o typach aplikacji, które znajdziesz.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określona Aplikacja pojawia się na liście moje wszystkie aplikacje?
Po przefiltrowaniu do **wszystkich aplikacji**na **liście** **wszystkie aplikacje** są wyświetlane wszystkie obiekty główne usługi w dzierżawie. Obiekty główne usługi mogą znajdować się na tej liście na różne sposoby:
- Po dodaniu aplikacji z galerii aplikacji, w tym:
   - **Azure AD — aplikacje dla przedsiębiorstw** — aplikacje dodane do dzierżawy przy użyciu opcji **aplikacje dla przedsiębiorstw** w portalu usługi Azure AD. Zwykle aplikacje zintegrowane przy użyciu standardu SAML.
   - **Azure AD — rejestracje aplikacji** — aplikacje dodane do dzierżawy przy użyciu opcji **rejestracje aplikacji** w portalu usługi Azure AD. Zazwyczaj niestandardowe aplikacje opracowane przy użyciu funkcji Open ID Connect i uwierzytelniania OAuth.
   - **Aplikacje serwera proxy aplikacji** — aplikacja działająca w środowisku lokalnym, w której chcesz zapewnić bezpieczne logowanie jednokrotne na zewnątrz
- Podczas rejestrowania się w programie lub zalogowania się do programu aplikacja innej firmy jest zintegrowana z Azure Active Directory. Przykładem jest [Narzędzia Smartsheet](https://app.smartsheet.com/b/home) lub [Docusign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplikacje firmy Microsoft, takie jak Microsoft 365.
- Po dodaniu nowej rejestracji aplikacji, tworząc aplikację niestandardową przy użyciu [rejestru aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)
- Po dodaniu nowej rejestracji aplikacji przez utworzenie niestandardowej aplikacji za pomocą [portalu rejestracji aplikacji w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)
- Gdy dodajesz aplikację, opracowujesz przy użyciu [metod uwierzytelniania ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) lub [usług połączonych](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) programu Visual Studio
- Podczas tworzenia obiektu jednostki usługi przy użyciu [modułu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)
- Gdy [wyrażasz zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) jako administrator do korzystania z danych w dzierżawie
- Gdy [użytkownik wyraża zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) w celu korzystania z danych w dzierżawie
- Po włączeniu niektórych usług, które przechowują dane w dzierżawie. Przykładem jest Resetowanie hasła, które jest modelowane jako nazwa główna usługi do bezpiecznego przechowywania zasad resetowania hasła.

Dowiedz się więcej o tym, jak i dlaczego aplikacje są dodawane do katalogu, zobacz [jak aplikacje są dodawane do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
