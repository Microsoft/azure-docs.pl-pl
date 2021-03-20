---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z chmurą MongoDB | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i chmurą MongoDB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: af1c0702929e7cd700c8d19ab24e40f9c6f43a21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96602176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z chmurą MongoDB

W tym samouczku dowiesz się, jak zintegrować chmurę MongoDB z usługą Azure Active Directory (Azure AD). W przypadku integracji chmury MongoDB z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do MongoDB Cloud, MongoDB Atlas, społeczność MongoDB, University MongoDB i MongoDB support.
* Zezwól użytkownikom na automatyczne logowanie do chmury MongoDB przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Organizacja w chmurze MongoDB, w której włączono obsługę logowania jednokrotnego (SSO), można skorzystać z [bezpłatnego klastra](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Chmura MongoDB obsługuje funkcję SSO zainicjowaną przez usługę **SP** i **dostawcy tożsamości** .
* Chmura MongoDB obsługuje Inicjowanie obsługi użytkowników **just in Time** .
* Po skonfigurowaniu chmury MongoDB można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Dodaj chmurę MongoDB z galerii

Aby skonfigurować integrację MongoDB w chmurze z usługą Azure AD, musisz dodać chmurę MongoDB z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MongoDB Cloud** w polu wyszukiwania.
1. Wybierz z wyników pozycję **MongoDB Cloud** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla chmury MongoDB

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury MongoDB przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze MongoDB.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury MongoDB, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj logowanie](#configure-mongodb-cloud-sso) jednokrotne w chmurze MongoDB, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego w chmurze MongoDB](#create-a-mongodb-cloud-test-user) , aby miał odpowiednik B. Simon w chmurze MongoDB, połączony z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w **chmurze MongoDB** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczej Sign-On ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL, który używa następującego wzorca:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta w chmurze MongoDB](https://support.mongodb.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja w chmurze MongoDB oczekuje, że potwierdzenia SAML mają być w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu atrybutów domyślnych](common/default-attributes.png)

1. Oprócz powyższych atrybutów aplikacja w chmurze MongoDB oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | poczta e-mail | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnionym linkiem pobierania](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi MongoDB Cloud** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie chmury Mongo DB z wyróżnionymi adresami URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie wpisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury MongoDB.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **MongoDB Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze MongoDB

Aby skonfigurować Logowanie jednokrotne na stronie chmurowej MongoDB, potrzebne są odpowiednie adresy URL skopiowane z Azure Portal. Należy również skonfigurować aplikację federacyjną dla organizacji w chmurze MongoDB. Postępuj zgodnie z instrukcjami w [dokumentacji chmury MongoDB](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Jeśli wystąpi problem, skontaktuj się z [zespołem pomocy technicznej w chmurze MongoDB](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Tworzenie użytkownika testowego w chmurze MongoDB

Chmura MongoDB obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Nie ma żadnych dodatkowych akcji do wykonania. Jeśli użytkownik nie istnieje jeszcze w chmurze MongoDB, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka MongoDB Cloud w panelu dostępu użytkownik zostanie automatycznie zalogowany do chmury MongoDB, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Utwórz konto w usłudze MongoDB Atlas na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mdb_atlas_oct2020?tab=Overview)

- [Wypróbuj chmurę MongoDB z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Ochrona chmury MongoDB z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)