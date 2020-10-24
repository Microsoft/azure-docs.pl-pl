---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Onit | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Onit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 40ed33021a892e140dfff47417137046557c0aef
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522363"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Onit

W tym samouczku dowiesz się, jak zintegrować usługę Onit z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Onit z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Onit.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Onit przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Onit.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Onit obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-onit-from-the-gallery"></a>Dodawanie Onit z galerii

Aby skonfigurować integrację programu Onit z usługą Azure AD, musisz dodać Onit z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Onit** w polu wyszukiwania.
1. Wybierz pozycję **Onit** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Onit

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Onit przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Onit.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Onit, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-onit-sso)** w usłudze Onit, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Onit](#create-onit-test-user)** , aby dysponować odpowiednikiem B. Simon w Onit, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Onit** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.onit.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Onit](https://www.onit.com/support) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie Onit** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Onit.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Onit**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-onit-sso"></a>Konfigurowanie logowania jednokrotnego Onit

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Onit jako administrator.

2. W menu u góry kliknij pozycję **Administracja**.
   
    ![Zrzut ekranu pokazujący menu w górnej części strony "M S s S test" z wybraną akcją "Administracja".](./media/onit-tutorial/IC791174.png "Administracja")

3. Kliknij pozycję **Edytuj firmę**.
   
    ![Edytuj firmę](./media/onit-tutorial/IC791175.png "Edytuj firmę")
   
4. Kliknij kartę **Zabezpieczenia**.
    
    ![Edytowanie informacji o firmie](./media/onit-tutorial/IC791176.png "Edytowanie informacji o firmie")

5. Na karcie **zabezpieczenia** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/onit-tutorial/IC791177.png "Logowanie jednokrotne")

    a. Jako **strategię uwierzytelniania**wybierz pozycję Logowanie jednokrotne **i hasło**.
    
    b. W polu tekstowym **docelowy adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL wylogowywania dostawcy tożsamości** wklej wartość  **adresu URL wylogowania**, którą skopiowano z Azure Portal.

    d. W polu tekstowym **dostawcy tożsamości odcisk palca certyfikatu (SHA1)** wklej wartość  **odcisku palca** certyfikatu, który został skopiowany z Azure Portal.

### <a name="create-onit-test-user"></a>Utwórz użytkownika testowego Onit

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Onit, muszą one być obsługiwane w Onit. W przypadku Onit, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny **Onit** jako administrator.

2. Kliknij pozycję **Dodaj użytkownika**.

    ![Administracja](./media/onit-tutorial/IC791180.png "Administracja")

3. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/onit-tutorial/IC791181.png "Dodaj użytkownika")

    a. Wpisz **nazwę** i **adres e-mail** prawidłowego konta usługi Azure AD, które chcesz udostępnić do powiązanych pól tekstowych.

    b. Kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Onit w panelu dostępu należy automatycznie zalogować się do Onit, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Onit z usługą Azure AD](https://aad.portal.azure.com/)