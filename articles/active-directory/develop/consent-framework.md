---
title: Struktura zgody na usługę Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o strukturze wyrażania zgody w Azure Active Directory i sposobach tworzenia wielodostępnych aplikacji sieci Web i natywnych klientów z wieloma dzierżawcami.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2020
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.openlocfilehash: a5e53c29c41b3c4a5a2dd1f421e128231aa5eec3
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327553"
---
# <a name="azure-active-directory-consent-framework"></a>Platforma wyrażania zgody w usłudze Azure Active Directory

Struktura wyrażania zgody na Azure Active Directory (Azure AD) ułatwia tworzenie wielodostępnych aplikacji sieci Web i natywnych klientów. Te aplikacje umożliwiają logowanie się przy użyciu kont użytkowników z dzierżawy usługi Azure AD, która różni się od tej, w której jest zarejestrowana aplikacja. Mogą także mieć dostęp do interfejsów API sieci Web, takich jak interfejs API Microsoft Graph (Aby uzyskać dostęp do usługi Azure AD, usługi Intune i usług w Microsoft 365) i innych interfejsów API usług firmy Microsoft, oprócz własnych interfejsów API sieci Web.

Struktura jest oparta na użytkowniku lub administratorze, który wyraża zgodę na dostęp do aplikacji, która prosi o zarejestrowanie w ich katalogu, co może dotyczyć dostępu do danych katalogu. Na przykład jeśli aplikacja kliencka sieci Web musi odczytać informacje kalendarza o użytkowniku z Microsoft 365, użytkownik musi najpierw wyrazić zgodę na tę aplikację klienta. Po udzieleniu zgody aplikacja kliencka będzie w stanie wywołać interfejs API Microsoft Graph w imieniu użytkownika i użyć informacji o kalendarzu zgodnie z wymaganiami. [Interfejs API Microsoft Graph](https://developer.microsoft.com/graph) zapewnia dostęp do danych w Microsoft 365 (takich jak kalendarze i komunikaty z programu Exchange, witryn i list z programu SharePoint, dokumentów z usługi OneDrive, notesów z programu OneNote, zadań z usługi Planner i skoroszytów z programu Excel), a także użytkowników i grup z innych obiektów danych z większej liczby usług w chmurze firmy Microsoft.

Struktura zgody jest oparta na uwierzytelnianiu OAuth 2,0 i jego różnych przepływach, takich jak przyznawanie kodu autoryzacji i udzielanie poświadczeń klienta, przy użyciu klientów publicznych lub poufnych. Dzięki użyciu protokołu OAuth 2,0 usługa Azure AD umożliwia tworzenie wielu różnych typów aplikacji klienckich — takich jak telefon, tablet, serwer lub aplikacja sieci Web — i uzyskiwanie dostępu do wymaganych zasobów.

Aby uzyskać więcej informacji na temat korzystania z struktury zgody z przyznanymi autoryzacją OAuth 2.0, zobacz [Autoryzuj dostęp do aplikacji sieci Web przy użyciu protokołu oauth 2,0 i usługi Azure AD](v2-oauth2-auth-code-flow.md) oraz [scenariusze uwierzytelniania dla usługi Azure AD](./authentication-vs-authorization.md). Aby uzyskać informacje na temat uzyskiwania autoryzowanego dostępu do Microsoft 365 za pomocą Microsoft Graph, zobacz [uwierzytelnianie aplikacji przy użyciu Microsoft Graph](/graph/).

## <a name="consent-experience---an-example"></a>Środowisko zgody — przykład

Poniższe kroki pokazują, jak środowisko zgody działa zarówno dla deweloperów aplikacji, jak i dla użytkownika.

1. Załóżmy, że masz aplikację klienta sieci Web, która musi zażądać określonych uprawnień dostępu do zasobu/interfejsu API. Dowiesz się, jak wykonać tę konfigurację w następnej sekcji, ale zasadniczo Azure Portal jest używany do deklarowania żądań uprawnień podczas konfiguracji. Podobnie jak inne ustawienia konfiguracji, staną się one częścią rejestracji aplikacji w usłudze Azure AD:

    ![Uprawnienia do innych aplikacji](./media/consent-framework/permissions.png)

1. Należy wziąć pod uwagę, że uprawnienia aplikacji zostały zaktualizowane, aplikacja jest uruchomiona, a użytkownik zamierza korzystać z niego po raz pierwszy. Najpierw aplikacja musi uzyskać kod autoryzacji z punktu końcowego usługi Azure AD `/authorize` . Może wtedy użyć kodu autoryzacji do uzyskania nowego tokenu dostępu i odświeżania.

1. Jeśli użytkownik nie jest już uwierzytelniony, punkt końcowy usługi Azure AD `/authorize` poprosi użytkownika o zalogowanie się.

    ![Użytkownik lub administrator loguje się do usługi Azure AD](./media/consent-framework/usersignin.png)

1. Po zalogowaniu się użytkownika usługa Azure AD określi, czy użytkownik musi zostać pokazany na stronie zgody. Jest to uzależnione do tego, czy użytkownik (lub administrator w jego organizacji) już wyraził zgodę na aplikację. Jeśli wyrażanie zgody nie zostało jeszcze przyznane, usługa Azure AD wyświetli komunikat z prośbą o zgodę i wyświetli wymagane uprawnienia potrzebne do działania. Zestaw uprawnień, które są wyświetlane w oknie dialogowym wyrażania zgody, są zgodne z wybranymi **uprawnieniami delegowanymi** w Azure Portal.

    ![Pokazuje przykład uprawnień wyświetlanych w oknie dialogowym zgody](./media/consent-framework/consent.png)

1. Po udzieleniu zgody przez użytkownika kod autoryzacji jest zwracany do aplikacji, która jest realizowana w celu uzyskania tokenu dostępu i tokenu odświeżania. Aby uzyskać więcej informacji o tym przepływie, zobacz [przepływ kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md).

1. Jako administrator możesz także wyrazić zgodę na uprawnienia delegowane aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgoda na administrowanie uniemożliwia wyświetlenie okna dialogowego wyrażania zgody dla każdego użytkownika w dzierżawie i może zostać wykonana w [Azure Portal](https://portal.azure.com) przez użytkowników z rolą administratora. Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Aby wyrazić zgodę na uprawnienia delegowane aplikacji**

   1. Przejdź do strony **uprawnień interfejsu API** dla aplikacji
   1. Kliknij przycisk **Udziel zgody administratora** .

      ![Przyznawanie uprawnień dla jawnej zgody administratora](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Udzielanie jawnej zgody przy użyciu przycisku **Udziel uprawnień** jest obecnie wymagane dla aplikacji jednostronicowych (Spa), które używają ADAL.js. W przeciwnym przypadku wystąpi błąd aplikacji przy żądaniu tokenu dostępu.

## <a name="next-steps"></a>Następne kroki

Zobacz [, jak przekonwertować aplikację w celu korzystania z wielu dzierżawców](howto-convert-app-to-be-multi-tenant.md)
