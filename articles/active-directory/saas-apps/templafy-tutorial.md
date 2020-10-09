---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Templafy SAML2 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Templafy SAML2.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: cff461ba2d463e88d0d5ba1943f789614aa5afa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88541871"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Templafy SAML2

W tym samouczku dowiesz się, jak zintegrować usługę Templafy SAML2 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Templafy SAML2 z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Templafy SAML2.
* Zezwól użytkownikom na automatyczne logowanie do Templafy SAML2 przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, Templafy SAML2).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Templafy SAML2 obsługuje logowanie jednokrotne w usłudze **SP**
* Templafy SAML2 obsługuje inicjowanie aprowizacji użytkowników **just in Time**

## <a name="adding-templafy-saml2-from-the-gallery"></a>Dodawanie Templafy SAML2 z galerii

Aby skonfigurować integrację usługi Templafy SAML2 w usłudze Azure AD, musisz dodać SAML2 Templafy z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Templafy SAML2** w polu wyszukiwania.
1. Wybierz pozycję **TEMPLAFY SAML2** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy-saml2"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Templafy SAML2

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Templafy SAML2 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Templafy SAML2.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Templafy SAML2, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze TEMPLAFY SAML2](#configure-templafy-saml2-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego programu TEMPLAFY SAML2](#create-templafy-saml2-test-user)** , aby uzyskać odpowiednika B. Simon w Templafy SAML2, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Templafy SAML2** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta TEMPLAFY SAML2](mailto:support@templafy.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Templafy SAML2 oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Templafy SAML2 oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy| Przestrzeń nazw  |
    | ---------------| --------------- | --------- |
    | givenname | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | emailaddress | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | streetaddress | User. StreetAddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | Użytkownik. miasto | `http://schemas.templafy.com/2016/06/identity/claims`|
    | pocztowy | User. KodPocztowy | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | stateorprovince | User. State | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | country | User. Country | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | stanowiska | user.jobtitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | działu, | user.department | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PhoneNumber | user.telephonenumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | User. facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | głównej | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Templafy SAML2.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **TEMPLAFY SAML2**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-templafy-saml2-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Templafy SAML2

Aby skonfigurować Logowanie jednokrotne na stronie **TEMPLAFY SAML2** , musisz wysłać **adres URL metadanych federacji aplikacji** do [zespołu pomocy technicznej Templafy SAML2](mailto:support@templafy.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-templafy-saml2-test-user"></a>Utwórz użytkownika testowego Templafy SAML2

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Templafy SAML2. Templafy SAML2 obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie Templafy SAML2, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Templafy SAML2 w panelu dostępu należy automatycznie zalogować się do SAML2 Templafy, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Templafy SAML2 w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)