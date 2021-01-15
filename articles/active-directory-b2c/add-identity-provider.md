---
title: Dodawanie dostawcy tożsamości — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak dodać dostawcę tożsamości do dzierżawy Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/14/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 1665f8f595e2bb9ba2a5f2c8528f85854630ab4f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216584"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie dostawcy tożsamości do dzierżawy Azure Active Directory B2C

Można skonfigurować Azure AD B2C, aby umożliwić użytkownikom logowanie się do aplikacji przy użyciu poświadczeń od zewnętrznych dostawców tożsamości społecznościowych lub korporacyjnych (dostawcy tożsamości). Azure AD B2C obsługuje zewnętrznych dostawców tożsamości, takich jak Facebook, konto Microsoft, Google, Twitter i dowolny dostawca tożsamości, który obsługuje protokół OAuth 1,0, OAuth 2,0, OpenID Connect Connect i protokołu SAML.

Za pomocą Federacji zewnętrznego dostawcy tożsamości możesz zaoferować swoim użytkownikom możliwość zalogowania się przy użyciu istniejących kont społecznościowych lub firmowych bez konieczności tworzenia nowego konta tylko dla aplikacji.

Na stronie rejestracji lub logowania Azure AD B2C przedstawia listę zewnętrznych dostawców tożsamości, które użytkownik może wybrać do logowania. Po wybraniu jednego z zewnętrznych dostawców tożsamości zostaną one pobrane (przekierowane) do witryny sieci Web wybranego dostawcy w celu ukończenia procesu logowania. Po pomyślnym zalogowaniu się użytkownika są one zwracane do Azure AD B2C w celu uwierzytelnienia konta w aplikacji.

![Przykład logowania mobilnego przy użyciu konta społecznościowego (Facebook)](media/add-identity-provider/external-idp.png)

Można dodać dostawców tożsamości, które są obsługiwane przez Azure Active Directory B2C (Azure AD B2C) do [przepływów użytkownika](user-flow-overview.md) przy użyciu Azure Portal. Możesz również dodać dostawców tożsamości do [zasad niestandardowych](custom-policy-get-started.md).

## <a name="select-an-identity-provider"></a>Wybierz dostawcę tożsamości

W aplikacjach zwykle używany jest tylko jeden dostawca tożsamości, ale można dodać więcej. Poniższe artykuły z poradami przedstawiają sposób tworzenia aplikacji dostawcy tożsamości, dodawania dostawcy tożsamości do dzierżawy oraz dodawania dostawcy tożsamości do przepływu użytkownika lub zasad niestandardowych.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (pojedyncza dzierżawa)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (wiele dzierżaw)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [Ogólny dostawca tożsamości](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [Usługą](identity-provider-linkedin.md)
* [Konto Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protokół SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
