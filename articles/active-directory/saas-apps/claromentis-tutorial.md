---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Claromentis | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455905"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Claromentis

W tym samouczku dowiesz się, jak zintegrować usługę Claromentis z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Claromentis z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Claromentis.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Claromentis przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Claromentis.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Claromentis obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Claromentis obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-claromentis-from-the-gallery"></a>Dodawanie Claromentis z galerii

Aby skonfigurować integrację programu Claromentis z usługą Azure AD, musisz dodać Claromentis z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Claromentis** w polu wyszukiwania.
1. Wybierz pozycję **Claromentis** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Claromentis

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Claromentis przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Claromentis.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Claromentis, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-claromentis-sso)** w usłudze Claromentis, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Claromentis](#create-claromentis-test-user)** , aby dysponować odpowiednikiem B. Simon w Claromentis, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Claromentis** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź wartość identyfikatora zgodnie z wymaganiami organizacji.

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, który został wyjaśniony w dalszej części Turorial.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Claromentis** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Claromentis.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Claromentis**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-claromentis-sso"></a>Konfigurowanie logowania jednokrotnego Claromentis

1. W innym oknie przeglądarki Zaloguj się do witryny sieci Web Claromentis jako administrator.

1. Kliknij **ikonę aplikacje** , a następnie wybierz pozycję **administrator**.

    ![Zrzut ekranu przedstawia witrynę sieci Web Claromentis z wybranym przez administratora.](./media/claromentis-tutorial/config1.png)

1. Wybierz kartę **Niestandardowa obsługa logowania** .

    ![Zrzut ekranu przedstawia stronę Administracja z wybranym niestandardową obsługą logowania.](./media/claromentis-tutorial/config2.png)

1. Wybierz pozycję **Konfiguracja protokołu SAML**.

    ![Zrzut ekranu przedstawia stronę konfiguracji protokołu SAML.](./media/claromentis-tutorial/config3.png)

1. Na karcie **Konfiguracja protokołu SAML** przewiń w dół do sekcji **Konfiguracja** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję konfiguracji strony, na której można wprowadzić informacje opisane w tym kroku.](./media/claromentis-tutorial/config4.png)

    a. W polu tekstowym **Nazwa kontaktu technicznego** wprowadź nazwę osoby kontaktowej.

    b. W polu tekstowym adres **E-mail osoby kontaktowej skontaktuj** się z adresem e-mail osoby kontaktowej.

    c. Wprowadź hasło w polu tekstowym **hasło administratora uwierzytelniania** .

1. Przewiń w dół do **źródeł uwierzytelniania** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję źródła uwierzytelniania, w której można wprowadzić informacje opisane w tym kroku.](./media/claromentis-tutorial/config5.png)

    a. W polu tekstowym **dostawcy tożsamości** wprowadź wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    b. W polu tekstowym **Identyfikator jednostki** wprowadź wartość identyfikatora jednostki.

    c. Przekaż plik **XML metadanych Federacji** pobrany z Azure Portal.

    d. Kliknij pozycję **Zapisz**.

1. Teraz Zauważ, że wszystkie adresy URL zostały wypełnione w sekcji **dostawca tożsamości** w sekcji **Konfiguracja protokołu SAML** .

    ![Zrzut ekranu przedstawia stronę dostawcy tożsamości wypełnioną przy użyciu U R ls.](./media/claromentis-tutorial/config6.png)

    a. Kopiuj wartość **identyfikatora (identyfikatora jednostki)** , wklej tę wartość w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Kopiuj wartość **adresu URL odpowiedzi** , wklej tę wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    c. Skopiuj wartość **adresu URL logowania** , wklej tę wartość w polu tekstowym **adres URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

### <a name="create-claromentis-test-user"></a>Utwórz użytkownika testowego Claromentis

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Claromentis. Claromentis obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Claromentis, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Claromentis w panelu dostępu należy automatycznie zalogować się do Claromentis, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Claromentis z usługą Azure AD](https://aad.portal.azure.com/)