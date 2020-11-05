---
title: Rejestrowanie aplikacji zasobów w usłudze Azure AD — Azure API for FHIR
description: Zarejestruj aplikację zasobów (lub interfejs API) w Azure Active Directory, aby aplikacje klienckie mogły żądać dostępu do zasobu podczas uwierzytelniania.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e22eaacd73bb15ddf43f416831ff5ff42923b6e0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393391"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Rejestrowanie aplikacji zasobów w Azure Active Directory

W tym artykule dowiesz się, jak zarejestrować aplikację zasobów (lub interfejsu API) w Azure Active Directory. Aplikacja zasobów to Azure Active Directory reprezentacja samego interfejsu API serwera FHIR, a aplikacje klienckie mogą zażądać dostępu do zasobu podczas uwierzytelniania. Aplikacja do zasobów jest również znana jako *odbiorcy* w sprzężeniem uwierzytelniania OAuth.

## <a name="azure-api-for-fhir"></a>Interfejs API platformy Azure dla standardu FHIR

Jeśli używasz interfejsu API platformy Azure dla usługi FHIR, aplikacja zasobów jest tworzona automatycznie podczas wdrażania usługi. O ile korzystasz z interfejsu API platformy Azure dla FHIR w tej samej dzierżawie Azure Active Directory podczas wdrażania aplikacji, możesz pominąć ten przewodnik, a zamiast tego wdrożyć interfejs API platformy Azure dla FHIR, aby rozpocząć pracę.

Jeśli używasz innej dzierżawy Azure Active Directory (nieskojarzonej z Twoją subskrypcją), możesz zaimportować aplikację Azure API for FHIR Resource do swojej dzierżawy za pomocą programu PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

Możesz też użyć interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Serwer FHIR dla platformy Azure

Jeśli używasz serwera FHIR typu open source dla platformy Azure, wykonaj poniższe kroki, aby zarejestrować aplikację do zasobów.

### <a name="app-registrations-in-azure-portal"></a>Rejestracje aplikacji w Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**.

2. W bloku **Azure Active Directory** kliknij pozycję **rejestracje aplikacji** :

    ![Azure Portal. Rejestracja nowej aplikacji.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kliknij **nową rejestrację**.

### <a name="add-a-new-application-registration"></a>Dodawanie nowej rejestracji aplikacji

Wprowadź szczegóły nowej aplikacji. Nie ma określonych wymagań dotyczących nazwy wyświetlanej, ale ustawienie jej na identyfikator URI serwera FHIR ułatwia znalezienie:

![Rejestrowanie nowej aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Ustaw identyfikator URI identyfikatora i Zdefiniuj zakresy

Aplikacja zasobu ma identyfikator URI identyfikatora (Identyfikator aplikacji), którego klienci mogą używać podczas żądania dostępu do zasobu. Ta wartość spowoduje wypełnienie `aud` żądania tokenu dostępu. Zaleca się, aby ustawić ten identyfikator URI jako identyfikator URI serwera FHIR. W przypadku inteligentnych aplikacji FHIR zakłada się, że *odbiorcy* są identyfikatorem URI serwera FHIR.

1. Kliknij pozycję **Uwidacznianie interfejsu API**

2. Kliknij przycisk **Ustaw** obok *identyfikatora aplikacji identyfikator URI*.

3. Wprowadź identyfikator URI identyfikatora i kliknij przycisk **Zapisz**. Identyfikator URI prawidłowego identyfikatora to identyfikator URI serwera FHIR.

4. Kliknij pozycję **Dodaj zakres** i Dodaj zakresy, które chcesz zdefiniować dla interfejsu API. Należy dodać co najmniej jeden zakres, aby przyznać uprawnienia do aplikacji zasobów w przyszłości. Jeśli nie masz żadnych określonych zakresów, które chcesz dodać, możesz dodać user_impersonation jako zakres.

![Odbiorcy i zakres](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definiowanie ról aplikacji

Interfejs Azure API for FHIR oraz serwer OSS FHIR dla platformy Azure używają [Azure Active Directory ról aplikacji](/azure/architecture/multitenant-identity/app-roles) na potrzeby kontroli dostępu opartej na rolach. Aby określić, które role mają być dostępne dla interfejsu API serwera FHIR, Otwórz [manifest](/azure/active-directory/active-directory-application-manifest/)aplikacji zasobu:

1. Kliknij pozycję **manifest** :

    ![Role aplikacji](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. We `appRoles` Właściwości Dodaj role, które mają być dostępne dla użytkowników lub aplikacji:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rejestrowania aplikacji zasobów w Azure Active Directory. Następnie zarejestruj poufną aplikację kliencką.
 
>[!div class="nextstepaction"]
>[Zarejestruj poufną aplikację kliencką](register-confidential-azure-ad-client-app.md)