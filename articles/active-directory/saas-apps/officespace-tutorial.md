---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z oprogramowaniem OfficeSpace | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a oprogramowaniem OfficeSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: ae8a1fa68e61e160ce08b93bf66776a050b45613
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554183"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania OfficeSpace

W tym samouczku dowiesz się, jak zintegrować oprogramowanie OfficeSpace z usługą Azure Active Directory (Azure AD). Po zintegrowaniu oprogramowania OfficeSpace z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do oprogramowania OfficeSpace.
* Zezwól użytkownikom na automatyczne logowanie do OfficeSpace oprogramowania przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) OfficeSpace.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie OfficeSpace obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**


* Oprogramowanie OfficeSpace obsługuje Inicjowanie obsługi użytkowników **just in Time**


## <a name="adding-officespace-software-from-the-gallery"></a>Dodawanie oprogramowania OfficeSpace z galerii

Aby skonfigurować integrację oprogramowania OfficeSpace w usłudze Azure AD, musisz dodać oprogramowanie OfficeSpace z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OfficeSpace Software** w polu wyszukiwania.
1. Wybierz pozycję **OfficeSpace oprogramowanie** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla oprogramowania OfficeSpace

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania OfficeSpace przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu OfficeSpace.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania OfficeSpace, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-officespace-software-sso)** w usłudze OfficeSpace, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego oprogramowania OfficeSpace](#create-officespace-software-test-user)** , aby dysponować odpowiednikiem B. Simon w oprogramowaniu OfficeSpace, które jest połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **OfficeSpace oprogramowania** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta oprogramowania OfficeSpace](mailto:support@officespacesoftware.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja OfficeSpace Software oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja OfficeSpace Software oczekuje, że **NameIdentifier** mają być mapowane z **użytkownikiem. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych OfficeSpace aplikacja oprogramowania oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | poczta e-mail | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie oprogramowania OfficeSpace** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do oprogramowania OfficeSpace.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **OfficeSpace oprogramowanie**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-officespace-software-sso"></a>Konfigurowanie rejestracji jednokrotnej OfficeSpace oprogramowania

1. W innym oknie przeglądarki sieci Web Zaloguj się do dzierżawy oprogramowania OfficeSpace jako administrator.

2. Przejdź do pozycji **Ustawienia** i kliknij pozycję **Łączniki**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Kliknij pozycję **uwierzytelnianie SAML**.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_003.png)

4. W sekcji **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. W polu tekstowym **adres URL dostawcy wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.

    b. W polu tekstowym **adres URL docelowa dostawcy tożsamości klienta** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. Wklej wartość **odcisku palca** skopiowaną z Azure Portal do pola tekstowego **odcisk palca certyfikatu dostawcy tożsamości klienta** . 

    d. Kliknij przycisk **Zapisz ustawienia**.

### <a name="create-officespace-software-test-user"></a>Utwórz użytkownika testowego OfficeSpace oprogramowania

W tej sekcji użytkownik o nazwie B. Simon został utworzony w oprogramowaniu OfficeSpace. Oprogramowanie OfficeSpace obsługuje Inicjowanie obsługi użytkowników just in Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w oprogramowaniu OfficeSpace, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej OfficeSpace](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka oprogramowanie OfficeSpace w panelu dostępu należy automatycznie zalogować się do oprogramowania OfficeSpace, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj oprogramowanie OfficeSpace w usłudze Azure AD](https://aad.portal.azure.com/)

