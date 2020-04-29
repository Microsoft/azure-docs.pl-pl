---
title: Zarządzanie uwierzytelnianiem | Mapy Microsoft Azure
description: Użyj Azure Portal, aby zarządzać uwierzytelnianiem w usłudze Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335527"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w Azure Maps

Po utworzeniu konta Azure Maps, identyfikator klienta i klucze są tworzone w celu obsługi uwierzytelniania Azure Active Directory (Azure AD) i uwierzytelniania klucza współużytkowanego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Zalecamy używanie klucza podstawowego jako klucza subskrypcji podczas [korzystania z uwierzytelniania za pomocą klucza współużytkowanego do wywoływania Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Klucza pomocniczego można używać w scenariuszach, takich jak stopniowe zmiany klawiszy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie w Azure Maps](https://aka.ms/amauth).

Szczegóły uwierzytelniania można wyświetlić w Azure Portal. Na Twoim koncie w menu **Ustawienia** wybierz pozycję **uwierzytelnianie**.

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Rejestrowanie i Konfigurowanie aplikacji usługi Azure AD

1. W Azure Portal na liście usług platformy Azure wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **Nowa rejestracja**.  

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

1. Jeśli aplikacja została już zarejestrowana, przejdź do następnego kroku. Jeśli aplikacja nie została zarejestrowana, wprowadź **nazwę**, wybierz **Typ konta pomocy technicznej**, a następnie wybierz pozycję **zarejestruj**.  

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

1. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji. Następnie w obszarze **rejestracje aplikacji**wybierz pozycję **uprawnienia** > interfejsu API**Dodaj uprawnienie**. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, Wyszukaj i wybierz **Azure Maps**.

    ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

1. Zaznacz pole wyboru obok pozycji **dostęp Azure Maps**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

1. Wykonaj jedną z następujących czynności, w zależności od metody uwierzytelniania. 

    * Jeśli aplikacja używa uwierzytelniania przy użyciu tokenu użytkownika w Azure Maps Web SDK, Włącz `oauth2AllowImplicitFlow`. Aby ją włączyć, w sekcji **manifestu** rejestracji aplikacji ustaw `oauth2AllowImplicitFlow` wartość PRAWDA. 
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    * Jeśli aplikacja używa uwierzytelniania serwera lub aplikacji, na stronie rejestracji aplikacji przejdź do pozycji **certyfikaty & wpisy tajne**. Następnie Przekaż certyfikat klucza publicznego lub Utwórz hasło, wybierając pozycję **Nowy wpis tajny klienta**. 
    
       ![Tworzenie klucza tajnego klienta](./media/how-to-manage-authentication/app-keys.png)

        Jeśli utworzysz hasło, po wybraniu opcji **Dodaj**Skopiuj hasło i Zapisz je w bezpiecznym miejscu. To hasło będzie używane do uzyskiwania tokenów z usługi Azure AD.

       ![Dodawanie klucza tajnego klienta](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Udzielanie kontroli dostępu opartej na rolach do Azure Maps

Po skojarzeniu konta Azure Maps z dzierżawą usługi Azure AD można udzielić kontroli dostępu. *Kontrolę dostępu opartą na rolach* (RBAC) można przypisywać użytkownikowi, grupie lub aplikacji do co najmniej jednej Azure Maps ról kontroli dostępu. 

1. Przejdź do swojego **konta Azure Maps**. Wybierz pozycję **Kontrola dostępu (IAM)** > **przypisanie roli**.

    ![Przyznaj RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na karcie **przypisania roli** w obszarze **rola**wybierz pozycję **Azure Maps Data Reader (wersja zapoznawcza)**. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**. Wybierz użytkownika lub aplikację. Następnie wybierz pozycję **Zapisz**.

    ![Dodaj przypisanie roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetl dostępne Azure Maps ról RBAC

Aby wyświetlić role RBAC dostępne dla Azure Maps, przejdź do pozycji **Kontrola dostępu (IAM)**. Wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od *Azure Maps*. Te role Azure Maps są rolami, do których można udzielić dostępu.

![Wyświetl dostępne role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Wyświetl Azure Maps RBAC

RBAC zapewnia szczegółową kontrolę dostępu.

Aby wyświetlić użytkowników i aplikacje, którym udzielono dostępu RBAC dla Azure Maps, przejdź do **Access Control (IAM)**. W tym miejscu wybierz pozycję **przypisania ról**, a następnie Przefiltruj według **Azure Maps**.

![Wyświetlanie użytkowników i aplikacji z przyznanymi RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Tokeny żądania dla Azure Maps

Po zarejestrowaniu aplikacji i skojarzeniu jej z Azure Maps można zażądać tokenów dostępu.

Jeśli aplikacja używa uwierzytelniania przy użyciu tokenu użytkownika w Azure Maps Web SDK, skonfiguruj stronę HTML przy użyciu identyfikatora klienta Azure Maps i identyfikatora aplikacji usługi Azure AD.

Jeśli aplikacja używa uwierzytelniania serwera lub aplikacji, zażądaj tokenu z punktu końcowego `https://login.microsoftonline.com`tokenu usługi Azure AD. W żądaniu Użyj następujących szczegółów: 

* Identyfikator zasobu usługi Azure AD`https://atlas.microsoft.com/`
* Identyfikator klienta Azure Maps
* Identyfikator aplikacji usługi Azure AD
* Hasło lub certyfikat rejestracji aplikacji usługi Azure AD

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD | Identyfikator zasobu platformy Azure |
| --------------------|-------------------------|-------------------|
| Chmura publiczna Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government Cloud   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i podmiotów usługi, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure AD i Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"] 
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
