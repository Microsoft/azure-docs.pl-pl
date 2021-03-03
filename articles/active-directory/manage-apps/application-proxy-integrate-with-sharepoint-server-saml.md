---
title: Publikowanie w usłudze = premises SharePoint z serwerem proxy aplikacji — Azure AD
description: Obejmuje podstawowe informacje dotyczące sposobu integrowania lokalnego programu SharePoint z usługą Azure serwer proxy aplikacji usługi Azure AD dla języka SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cadf5b7d92e26e561e570f824295e69ca421e16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644524"
---
# <a name="integrate-with-sharepoint-saml"></a>Integracja z programem SharePoint (SAML)

W tym przewodniku krok po kroku wyjaśniono, jak zabezpieczyć dostęp do [Azure Active Directory zintegrowanego lokalnego programu SharePoint (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD, gdzie użytkownicy w organizacji (Azure AD, B2B) łączą się z programem SharePoint za pośrednictwem Internetu.

> [!NOTE] 
> Jeśli dopiero zaczynasz korzystać z usługi Azure serwer proxy aplikacji usługi Azure AD i chcesz dowiedzieć się więcej, zobacz [zdalny dostęp do aplikacji lokalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](./application-proxy.md).

Istnieją trzy podstawowe zalety tej instalacji:

- Usługa Azure serwer proxy aplikacji usługi Azure AD zapewnia, że ruch uwierzytelniony może nawiązać połączenie z siecią wewnętrzną i serwerem programu SharePoint.
- Użytkownicy mogą uzyskiwać dostęp do witryn programu SharePoint w zwykły sposób, bez korzystania z sieci VPN.
- Można kontrolować dostęp przez przypisanie użytkownika na poziomie usługi Azure serwer proxy aplikacji usługi Azure AD i zwiększyć zabezpieczenia za pomocą funkcji usługi Azure AD, takich jak dostęp warunkowy i Multi-Factor Authentication (MFA).

Ten proces wymaga dwóch aplikacji dla przedsiębiorstw. Jednym z nich jest lokalne wystąpienie programu SharePoint publikowane z galerii do listy zarządzanych aplikacji SaaS. Druga to aplikacja lokalna (aplikacja bez galerii), która będzie używana do publikowania pierwszej aplikacji w galerii dla przedsiębiorstw.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę konfigurację, potrzebne są następujące zasoby:
 - Farma programu SharePoint 2013 lub nowsza. Farma programu SharePoint musi być [zintegrowana z usługą Azure AD](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Dzierżawa usługi Azure AD z planem zawierającym serwer proxy aplikacji. Dowiedz się więcej o [planach i cenach usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
 - [Niestandardowa, zweryfikowana domena](../fundamentals/add-custom-domain.md) w dzierżawie usługi Azure AD. Zweryfikowana domena musi być zgodna z sufiksem adresu URL programu SharePoint.
 - Wymagany jest certyfikat SSL. Zobacz szczegóły w obszarze [Publikowanie domen niestandardowych](./application-proxy-configure-custom-domain.md).
 - Lokalne Active Directory użytkownicy muszą być zsynchronizowane z usługą Azure AD Connect i muszą być skonfigurowane do [logowania się do platformy Azure](../hybrid/plan-connect-user-signin.md). 
 - W przypadku użytkowników korzystających tylko z chmury i gościa B2B musisz [udzielić dostępu do konta gościa w środowisku lokalnym programu SharePoint w Azure Portal](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Łącznik serwera proxy aplikacji został zainstalowany i uruchomiony na komputerze w domenie firmowej.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Krok 1. Integracja programu SharePoint w środowisku lokalnym z usługą Azure AD 

1. Skonfiguruj aplikację lokalną programu SharePoint. Aby uzyskać więcej informacji, zobacz [Samouczek: Azure Active Directory integrację logowania jednokrotnego z lokalnym programem SharePoint](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Sprawdź poprawność konfiguracji przed przejściem do następnego kroku. Aby sprawdzić poprawność, spróbuj uzyskać dostęp do lokalnego programu SharePoint z sieci wewnętrznej i upewnić się, że jest on dostępny wewnętrznie. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Krok 2. publikowanie aplikacji lokalnej programu SharePoint przy użyciu serwera proxy aplikacji

W tym kroku utworzysz aplikację w dzierżawie usługi Azure AD, która używa serwera proxy aplikacji. Należy ustawić zewnętrzny adres URL i określić wewnętrzny adres URL, który jest używany później w programie SharePoint.

> [!NOTE] 
> Wewnętrzne i zewnętrzne adresy URL muszą być zgodne z **adresem URL logowania** w konfiguracji aplikacji opartej na protokole SAML w kroku 1.

   ![Zrzut ekranu pokazujący wartość adresu URL logowania.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Utwórz nową aplikację serwer proxy aplikacji usługi Azure AD platformy Azure z domeną niestandardową. Aby uzyskać instrukcje krok po kroku, zobacz [domeny niestandardowe na platformie Azure serwer proxy aplikacji usługi Azure AD](./application-proxy-configure-custom-domain.md).

    - Wewnętrzny adres URL: https://portal.contoso.com/
    - Zewnętrzny adres URL: https://portal.contoso.com/
    - Uwierzytelnianie wstępne: Azure Active Directory
    - Tłumaczenie adresów URL w nagłówkach: nie
    - Tłumaczenie adresów URL w treści aplikacji: nie

        ![Zrzut ekranu przedstawiający opcje, których można użyć do utworzenia aplikacji.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Przypisz te [same grupy](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) przypisane do lokalnej aplikacji galerii programu SharePoint.

3. Na koniec przejdź do sekcji **Właściwości** i ustaw **dla opcji widoczne dla użytkowników** opcję **nie**. Ta opcja zapewnia, że tylko ikona pierwszej aplikacji zostanie wyświetlona w portalu Moje aplikacje ( https://myapplications.microsoft.com) .

   ![Zrzut ekranu pokazujący, gdzie ustawić widoczny dla użytkowników? zaznaczyć.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Krok 3. Testowanie aplikacji

Korzystając z przeglądarki z komputera w sieci zewnętrznej, przejdź do adresu URL ( https://portal.contoso.com/) który został skonfigurowany podczas kroku Publikuj. Upewnij się, że możesz zalogować się przy użyciu skonfigurowanego konta testowego.