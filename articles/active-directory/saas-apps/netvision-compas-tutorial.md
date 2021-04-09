---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą COMPAS Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i COMPAS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą COMPAS

W tym samouczku dowiesz się, jak zintegrować usługę COMPAS Azure Active Directory z usługą Microsoft Azure AD. Po zintegrowaniu usługi COMPAS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi COMPAS.
* Zezwól użytkownikom na automatyczne logowanie do usługi COMPAS w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) COMPAS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Visioning COMPAS obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu obsługi COMPAS można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Dodawanie Compasow z galerii

Aby skonfigurować integrację programu COMPAS z usługą Azure AD, musisz dodać COMPAS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **COMPAS** w polu wyszukiwania.
1. Wybierz pozycję **Compasow (obsługa administracyjna** ) w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby Compasow

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu COMPAS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze COMPAS.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi COMPAS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę COMPAS logowanie JEDNOkrotne](#configure-netvision-compas-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Skonfiguruj użytkownika testowego COMPAS](#configure-netvision-compas-test-user)** , aby miał odpowiednik B. Simon w programie Visioning COMPAS, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **COMPAS** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta COMPAS](mailto:contact@net.vision) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać plik metadanych i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)



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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Visioning COMPAS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **COMPAS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-netvision-compas-sso"></a>Konfigurowanie logowania jednokrotnego COMPAS

W tej sekcji włączasz Logowanie jednokrotne SAML w **COMPAS**.
1. Zaloguj się do usługi **Vision COMPAS** przy użyciu konta administracyjnego i uzyskaj dostęp do obszaru Administracja.

    ![Obszar administracyjny](media/netvision-compas-tutorial/admin.png)

1. Znajdź obszar **systemowy** i wybierz pozycję **dostawcy tożsamości**.

    ![Dostawców tożsamości administratora](media/netvision-compas-tutorial/admin-idps.png)

1. Wybierz akcję **Dodaj** , aby zarejestrować usługę Azure AD jako nową dostawcy tożsamości.

    ![Dodaj dostawcy tożsamości](media/netvision-compas-tutorial/idps-add.png)

1. Wybierz pozycję **SAML** dla **typu dostawcy**.
1. Wprowadź znaczące wartości dla pól **Nazwa wyświetlana** i **Opis** .
1. Przypisz użytkowników **COMPAS** do dostawcy tożsamości, wybierając z listy **dostępne użytkownicy** , a następnie wybierając przycisk **Dodaj wybrane** . Użytkownicy mogą również być przypisani do dostawcy tożsamości podczas procedury aprowizacji.
1. Dla opcji SAML **metadanych** kliknij przycisk **Wybierz plik** i wybierz plik metadanych, który został wcześniej zapisany na komputerze.
1. Kliknij pozycję **Zapisz**.

    ![Edytuj dostawcy tożsamości](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfiguruj użytkownika testowego COMPAS

W tej sekcji skonfigurujesz istniejącego użytkownika w usłudze **COMPAS** , aby używać usługi Azure AD do logowania jednokrotnego.
1. Postępuj zgodnie z procedurą aprowizacji użytkowników **COMPAS** , zgodnie z definicją w firmie, lub edytuj istniejące konto użytkownika.
1. Podczas definiowania profilu użytkownika upewnij się, że adres **e-mail użytkownika (Personal)** jest zgodny z nazwą użytkownika usługi Azure AD: username@companydomain.extension . Na przykład `B.Simon@contoso.com`.

    ![Edytuj użytkownika](media/netvision-compas-tutorial/user-config.png)

Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Ta sekcja umożliwia testowanie konfiguracji logowania jednokrotnego usługi Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Za pomocą panelu dostępu (dostawcy tożsamości zainicjowany).

Po kliknięciu kafelka COMPAS programu Visioning w panelu dostępu należy automatycznie zalogować się do COMPAS programu Visioning, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Bezpośredni dostęp do Compasow (SP zainicjowany).

1. Uzyskaj dostęp do adresu URL **Compasow** . Na przykład `https://tenant.compas.cloud`.
1. Wprowadź nazwę użytkownika **COMPAS** i wybierz przycisk **dalej**.

    ![Logowanie użytkownika](media/netvision-compas-tutorial/login-user.png)

1. **(opcjonalnie)** Jeśli użytkownik ma przypisaną wiele dostawców tożsamości w programie **Visioning COMPAS**, zostanie wyświetlona lista dostępnych dostawców tożsamości. Wybierz usługę Azure AD dostawcy tożsamości skonfigurowaną wcześniej w programie **Visioning COMPAS**.

    ![Wybór logowania](media/netvision-compas-tutorial/login-choose.png)

1. Nastąpi przekierowanie do usługi Azure AD w celu przeprowadzenia uwierzytelniania. Po pomyślnym uwierzytelnieniu należy automatycznie zalogować się do usługi **COMPAS** , dla której skonfigurowano Logowanie jednokrotne.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę COMPAS w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)