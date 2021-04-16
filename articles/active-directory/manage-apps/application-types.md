---
title: Wyświetlanie aplikacji przy użyciu dzierżawy Azure Active Directory do zarządzania tożsamościami
description: Dowiedz się, jak wyświetlać wszystkie aplikacje przy użyciu dzierżawy Azure Active Directory do zarządzania tożsamościami.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378077"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Wyświetlanie aplikacji przy użyciu dzierżawy usługi Azure AD do zarządzania tożsamościami
Przewodnik [Szybki start z serii dotyczącej](view-applications-portal.md) zarządzania aplikacją zapewnia podstawowe informacje. Z tego tematu dowiesz się, jak wyświetlać wszystkie aplikacje przy użyciu dzierżawy usługi Azure AD do zarządzania tożsamościami. W tym artykule bardziej zagłębiamy się w typy aplikacji, które znajdziesz.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określonej aplikacji pojawia się na mojej liście wszystkich aplikacji?
Po odfiltrowania **do wszystkich aplikacji** lista Wszystkie **aplikacje**  pokazuje każdy obiekt jednostki usługi w dzierżawie. Obiekty jednostki usługi mogą być wyświetlane na tej liście na różne sposoby:
- Podczas dodawania dowolnej aplikacji z galerii aplikacji, w tym:
   - **Azure AD — aplikacje dla przedsiębiorstw** — aplikacje dodane do dzierżawy przy użyciu opcji Aplikacje dla **przedsiębiorstw** w portalu usługi Azure AD. Zazwyczaj aplikacje zintegrowane przy użyciu standardu SAML.
   - **Azure AD — Rejestracje aplikacji** — aplikacje dodane do dzierżawy przy **użyciu Rejestracje aplikacji** w portalu usługi Azure AD. Zazwyczaj niestandardowe aplikacje opracowane przy użyciu standardów Open ID Connect i OAuth.
   - **serwer proxy aplikacji Applications** — aplikacja uruchomiona w środowisku lokalnym, dla której chcesz zapewnić bezpieczne logowanie pojedyncze na zewnątrz
- Podczas rejestracji lub logowania się do aplikacji innej firmy zintegrowanej z aplikacją Azure Active Directory. Jednym z przykładów [jest Smartsheet](https://app.smartsheet.com/b/home) lub [DocuSign.](https://www.docusign.net/member/MemberLogin.aspx)
- Aplikacje firmy Microsoft, takie jak Microsoft 365.
- Podczas dodawania nowej rejestracji aplikacji przez utworzenie niestandardowej aplikacji przy użyciu [rejestru aplikacji](../develop/quickstart-register-app.md)
- Podczas dodawania nowej rejestracji aplikacji przez utworzenie niestandardowej aplikacji przy użyciu portalu rejestracji aplikacji w wersji [2.0](../develop/quickstart-register-app.md)
- Podczas dodawania aplikacji programuje się przy użyciu Visual Studio uwierzytelniania ASP.NET [usług](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) [połączonych](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Podczas tworzenia obiektu jednostki usługi przy użyciu modułu [Programu PowerShell usługi Azure AD](/powershell/azure/active-directory/install-adv2)
- Gdy [wyrażasz zgodę na używanie](../develop/howto-convert-app-to-be-multi-tenant.md) danych w dzierżawie przez aplikację jako administrator
- Gdy [użytkownik wyraża zgodę na używanie danych w](../develop/howto-convert-app-to-be-multi-tenant.md) dzierżawie przez aplikację
- Po włączeniu niektórych usług, które przechowują dane w dzierżawie. Przykładem jest resetowanie hasła, które jest modelowane jako jednostki usługi do bezpiecznego przechowywania zasad resetowania haseł.

Aby dowiedzieć się więcej o tym, jak i dlaczego aplikacje są dodawane do katalogu, zobacz [How applications are added to Azure AD (Jak aplikacje są dodawane do usługi Azure AD).](../develop/active-directory-how-applications-are-added.md)

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
