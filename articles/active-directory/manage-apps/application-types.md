---
title: Wyświetlanie aplikacji przy użyciu dzierżawy Azure Active Directory na potrzeby zarządzania tożsamościami
description: Dowiedz się, jak wyświetlać wszystkie aplikacje za pomocą dzierżawy Azure Active Directory na potrzeby zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 18e29ce8bf744cabb31c5634c9b0ca547bca5acc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020676"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Wyświetlanie aplikacji przy użyciu dzierżawy usługi Azure AD na potrzeby zarządzania tożsamościami
[Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md) zawiera podstawowe informacje. W tym temacie dowiesz się, jak wyświetlać wszystkie aplikacje korzystające z dzierżawy usługi Azure AD na potrzeby zarządzania tożsamościami. W tym artykule omówieniach się nieco bardziej szczegółowe informacje o typach aplikacji, które znajdziesz.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określona Aplikacja pojawia się na liście moje wszystkie aplikacje?
Po przefiltrowaniu do **wszystkich aplikacji** na **liście** **wszystkie aplikacje** są wyświetlane wszystkie obiekty główne usługi w dzierżawie. Obiekty główne usługi mogą znajdować się na tej liście na różne sposoby:
- Po dodaniu aplikacji z galerii aplikacji, w tym:
   - **Azure AD — aplikacje dla przedsiębiorstw** — aplikacje dodane do dzierżawy przy użyciu opcji **aplikacje dla przedsiębiorstw** w portalu usługi Azure AD. Zwykle aplikacje zintegrowane przy użyciu standardu SAML.
   - **Azure AD — rejestracje aplikacji** — aplikacje dodane do dzierżawy przy użyciu opcji **rejestracje aplikacji** w portalu usługi Azure AD. Zazwyczaj niestandardowe aplikacje opracowane przy użyciu funkcji Open ID Connect i uwierzytelniania OAuth.
   - **Aplikacje serwera proxy aplikacji** — aplikacja działająca w środowisku lokalnym, w której chcesz zapewnić bezpieczne logowanie jednokrotne na zewnątrz
- Podczas rejestrowania się w programie lub zalogowania się do programu aplikacja innej firmy jest zintegrowana z Azure Active Directory. Przykładem jest [Narzędzia Smartsheet](https://app.smartsheet.com/b/home) lub [Docusign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplikacje firmy Microsoft, takie jak Microsoft 365.
- Po dodaniu nowej rejestracji aplikacji, tworząc aplikację niestandardową przy użyciu [rejestru aplikacji](../develop/quickstart-register-app.md)
- Po dodaniu nowej rejestracji aplikacji przez utworzenie niestandardowej aplikacji za pomocą [portalu rejestracji aplikacji w wersji 2.0](../develop/quickstart-register-app.md)
- Gdy dodajesz aplikację, opracowujesz przy użyciu [metod uwierzytelniania ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) lub [usług połączonych](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) programu Visual Studio
- Podczas tworzenia obiektu jednostki usługi przy użyciu [modułu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)
- Gdy [wyrażasz zgodę na aplikację](../develop/howto-convert-app-to-be-multi-tenant.md) jako administrator do korzystania z danych w dzierżawie
- Gdy [użytkownik wyraża zgodę na aplikację](../develop/howto-convert-app-to-be-multi-tenant.md) w celu korzystania z danych w dzierżawie
- Po włączeniu niektórych usług, które przechowują dane w dzierżawie. Przykładem jest Resetowanie hasła, które jest modelowane jako nazwa główna usługi do bezpiecznego przechowywania zasad resetowania hasła.

Dowiedz się więcej o tym, jak i dlaczego aplikacje są dodawane do katalogu, zobacz [jak aplikacje są dodawane do usługi Azure AD](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
