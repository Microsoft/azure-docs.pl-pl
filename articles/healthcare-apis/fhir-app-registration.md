---
title: Zarejestruj Azure Active Directory aplikacje dla interfejsu API platformy Azure dla usługi FHIR
description: W tym samouczku wyjaśniono, które aplikacje muszą być zarejestrowane dla interfejsu API platformy Azure dla FHIR i serwera FHIR dla platformy Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: aa95dc5cc052fbff6c553de50f4f52dc5df850a5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398117"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Zarejestruj Azure Active Directory aplikacje dla interfejsu API platformy Azure dla usługi FHIR

Istnieje kilka opcji konfiguracji, które można wybrać podczas konfigurowania interfejsu API platformy Azure dla FHIR lub serwera FHIR dla platformy Azure (OSS). W przypadku usługi Open Source należy utworzyć własną rejestrację aplikacji zasobów. W przypadku interfejsu API platformy Azure dla usługi FHIR ta aplikacja zasobów jest tworzona automatycznie.

## <a name="application-registrations"></a>Rejestracje aplikacji

Aby aplikacja mogła korzystać z usługi Azure AD, musi zostać zarejestrowana. W kontekście serwera FHIR istnieją dwa rodzaje rejestracji aplikacji do omówienia:

1. Rejestracje aplikacji zasobów.
1. Rejestracje aplikacji klienckich.

**Aplikacje zasobów** są reprezentacjami w usłudze Azure AD interfejsu API lub zasobu, który jest zabezpieczony za pomocą usługi Azure AD, w odniesieniu do usługi Azure API for FHIR. Aplikacja zasobów dla interfejsu API platformy Azure dla FHIR zostanie utworzona automatycznie podczas aprowizacji usługi, ale jeśli korzystasz z serwera typu "open source", musisz [zarejestrować aplikację zasobów](register-resource-azure-ad-client-app.md) w usłudze Azure AD. Ta aplikacja zasobów będzie miała identyfikator URI identyfikatora. Zaleca się, aby ten identyfikator URI był taki sam jak identyfikator URI serwera FHIR. Ten identyfikator URI powinien być używany jako `Audience` dla serwera FHIR. Aplikacja kliencka może zażądać dostępu do tego serwera FHIR, gdy żąda tokenu.

*Aplikacje klienckie* to rejestracje klientów, którzy będą żądać tokenów. Często w przypadku protokołu OAuth 2,0 rozróżniamy co najmniej trzy różne typy aplikacji:

1. **Poufne klienci** , nazywani również aplikacjami sieci Web w usłudze Azure AD. Poufne klienci są aplikacjami, które używają [przepływu kodu autoryzacji](../active-directory/azuread-dev/v1-protocols-oauth-code.md) w celu uzyskania tokenu w imieniu zalogowanego użytkownika, który wysłał prawidłowe poświadczenia. Są one nazywane klientami poufnymi, ponieważ mogą przechowywać klucz tajny i zaprezentować ten klucz tajny w usłudze Azure AD podczas wymiany kodu uwierzytelniania dla tokenu. Ze względu na to, że poufne klienci mogą uwierzytelniać się przy użyciu klucza tajnego klienta, są one zaufane na więcej niż publiczne komputery klienckie i mogą mieć już inne tokeny i mieć przypisany token odświeżania. Zapoznaj się ze szczegółowymi informacjami na temat [rejestrowania klienta poufnego](register-confidential-azure-ad-client-app.md). Należy pamiętać, że jest ważne, aby zarejestrować adres URL odpowiedzi, pod którym klient będzie otrzymywał kod autoryzacji.
1. **Klienci publiczni**. Są to klienci, którzy nie mogą zachować klucza tajnego. Zwykle jest to aplikacja urządzenia przenośnego lub aplikacja JavaScript jednostronicowa, w której użytkownik może odnaleźć wpis tajny klienta. Klienci publiczni również używają przepływu kodu autoryzacji, ale nie mogą przedstawić wpisu tajnego podczas uzyskiwania tokenu i mogą mieć krótsze tokeny, a nie tokeny odświeżania. Zapoznaj się ze szczegółowymi informacjami na temat [rejestrowania klienta publicznego](register-public-azure-ad-client-app.md).
1. Klienci usługi. Ci klienci uzyskują tokeny w imieniu własnym (a nie w imieniu użytkownika) przy użyciu [przepływu poświadczeń klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Zazwyczaj reprezentują aplikacje, które uzyskują dostęp do serwera FHIR w sposób nieinteraktywny. Przykładem może być proces pozyskiwania. W przypadku korzystania z klienta usługi nie jest konieczne rozpoczęcie procesu uzyskiwania tokenu z wywołaniem do `/authorize` punktu końcowego. Klient usługi może przejść bezpośrednio do `/token` punktu końcowego i przedstawić identyfikator klienta i klucz tajny klienta w celu uzyskania tokenu. Zapoznaj się ze szczegółami dotyczącymi [rejestrowania klienta usługi](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>Następne kroki

W tym omówieniu przedstawiono typy rejestracji aplikacji, które mogą być potrzebne do pracy z interfejsem API FHIR.

Na podstawie konfiguracji zapoznaj się z przewodnikiem, aby zarejestrować swoje aplikacje

* [Rejestrowanie aplikacji zasobów](register-resource-azure-ad-client-app.md)
* [Rejestrowanie poufnej aplikacji klienckiej](register-confidential-azure-ad-client-app.md)
* [Rejestrowanie publicznej aplikacji klienckiej](register-public-azure-ad-client-app.md)
* [Rejestrowanie aplikacji usługi](register-service-azure-ad-client-app.md)

Po zarejestrowaniu aplikacji można wdrożyć interfejs API platformy Azure dla usługi FHIR.

>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-powershell-quickstart.md)