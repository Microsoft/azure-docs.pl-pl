---
title: Wymagania wstępne dotyczące interfejsu API | Portal Azure Marketplace
description: Wymagania wstępne związane z używaniem portal Cloud Partner interfejsów API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255978"
---
<a name="api-prerequisites"></a>Wymagania wstępne dotyczące interfejsu API
================

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Istnieją dwa wymagane zasoby programistyczne, które muszą używać portal Cloud Partner interfejsów API: nazwy głównej usługi i tokenu dostępu Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Tworzenie nazwy głównej usługi w dzierżawie Azure Active Directory
----------------------------------------------------------------

Najpierw musisz utworzyć nazwę główną usługi w dzierżawie usługi Azure AD. Do tej dzierżawy zostanie przypisany własny zestaw uprawnień w portal Cloud Partner. Kod będzie wywoływał interfejsy API używające jako tej dzierżawy zamiast korzystać z poświadczeń osobistych.  Aby uzyskać pełne wyjaśnienie tworzenia nazwy głównej usługi, zobacz temat [Używanie portalu do tworzenia Azure Active Directory aplikacji i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Dodawanie jednostki usługi do konta
-----------------------------------------

Teraz, po utworzeniu jednostki usługi w dzierżawie, możesz ją dodać jako użytkownika do konta portal Cloud Partner. Podobnie jak użytkownik, nazwa główna usługi może być właścicielem lub współautorem portalu.

Aby dodać jednostkę usługi, wykonaj następujące kroki:

1. Zaloguj się do portal Cloud Partner. 
2. Kliknij pozycję **Użytkownicy** na lewym pasku menu, a następnie wybierz pozycję **Dodaj użytkownika**.

   ![Dodawanie użytkownika do portalu](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Z listy rozwijanej **Typ** wybierz pozycję Nazwa **główna usługi** i Dodaj następujące szczegóły:

-   **Przyjazna nazwa** dla nazwy głównej usługi, na przykład `spAccount`.
-   **Identyfikator aplikacji**. Aby znaleźć ten identyfikator, przejdź do [Azure Portal](https://portal.azure.com), kliknij przycisk **Azure Active Directory**, wybierz **rejestracje aplikacji**i kliknij aplikację.
-   **Identyfikator dzierżawy**, znany również jako **Identyfikator katalogu**dla dzierżawy usługi Azure AD. Ten identyfikator można znaleźć na stronie Azure Active Directory w [Azure Portal](https://portal.azure.com), w obszarze **Właściwości**.
-   **Identyfikator obiektu** dla obiektu głównego usługi. Ten identyfikator można uzyskać z Azure Portal. Przejdź do **Azure Active Directory**, wybierz **rejestracje aplikacji**, kliknij aplikację, a następnie kliknij nazwę aplikacji w obszarze **aplikacja zarządzana w katalogu lokalnym**. Następnie przejdź do strony **Właściwości** , aby znaleźć identyfikator obiektu. Upewnij się, że nie jest używany początkowy identyfikator obiektu, który znajduje się w aplikacji, ale zamiast tego identyfikator obiektu w zarządzanej aplikacji.
-   **Rola** skojarzona z kontem, która będzie używana w ramach RBAC.

     ![Dodawanie aplikacji zarządzanej do portalu](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kliknij przycisk **Dodaj** , aby dodać jednostkę usługi do Twojego konta.

   ![Dodawanie nazwy głównej usługi](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD
----------------------------

Podczas uwierzytelniania portal Cloud Partner interfejsy API używają następujących zasobów i protokołów:

- Token okaziciela sieci Web JSON (JWT), aby zażądać dostępu do zasobów
- Protokół [OpenID Connect Connect](https://openid.net/connect/) (OIDC) do weryfikacji tożsamości
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako urząd tożsamości

Istnieją dwie podejścia do programistycznego uzyskiwania tokenu JWT:

- Użyj biblioteki uwierzytelniania firmy Microsoft dla platformy .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  To podejście wyższego poziomu jest zalecane dla deweloperów platformy .NET. 
- Utwórz żądanie **http post** w punkcie końcowym **tokenu** OAuth usługi Azure AD, który przyjmuje postać:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Teraz można przekazać ten token jako część nagłówka autoryzacji dla żądań interfejsu API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> W przypadku wszystkich interfejsów API w tym odwołaniu nagłówek autoryzacji jest zawsze traktowany jako zakończony, więc nie został jawnie wymieniony.

Jeśli w żądaniu wystąpią błędy uwierzytelniania, zobacz [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
