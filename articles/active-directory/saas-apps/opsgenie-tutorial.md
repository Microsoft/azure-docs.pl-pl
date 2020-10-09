---
title: 'Samouczek: integracja Azure Active Directory z usługą OpsGenie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 0ebcb746e10ae910c695e52fb053fc5d0b316e70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543911"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą OpsGenie

W tym samouczku dowiesz się, jak zintegrować usługę OpsGenie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi OpsGenie z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do OpsGenie.
* Zezwól użytkownikom na automatyczne logowanie się do usługi OpsGenie przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) OpsGenie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa OpsGenie obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne
* Po skonfigurowaniu OpsGenie można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii

Aby skonfigurować integrację programu OpsGenie z usługą Azure AD, musisz dodać OpsGenie z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OpsGenie** w polu wyszukiwania.
1. Wybierz pozycję **OpsGenie** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla OpsGenie

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą OpsGenie przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w OpsGenie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą OpsGenie, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-opsgenie-sso)** w usłudze OpsGenie, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego OpsGenie](#create-opsgenie-test-user)** , aby dysponować odpowiednikiem B. Simon w OpsGenie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **OpsGenie** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL odpowiedzi, który został wyjaśniony w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie OpsGenie** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi OpsGenie.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **OpsGenie**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-opsgenie-sso"></a>Konfigurowanie logowania jednokrotnego OpsGenie

1. Otwórz inne wystąpienie przeglądarki, a następnie zaloguj się do OpsGenie jako administrator.

2. Kliknij pozycję **Ustawienia**, a następnie kliknij kartę **Logowanie** jednokrotne.
   
    ![OpsGenie pojedyncze Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Aby włączyć logowanie jednokrotne, wybierz pozycję **włączone**.
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. W sekcji **dostawca** kliknij kartę **Azure Active Directory** .
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na stronie okna dialogowego Azure Active Directory wykonaj następujące czynności:
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Skopiuj wartość **Identyfikator URI identyfikatora aplikacji** i wklej ją do pola **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    a. Skopiuj wartość **adres URL odpowiedzi** i wklej ją do pola tekstowego **adresu URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    a. W polu tekstowym **punkt końcowy protokołu SAML 2,0** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.
    
    b. W polu tekstowym **URL metadanych:** wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z Azure Portal.
    
    c. Aby włączyć logowanie jednokrotne, włącz przełącznik **Włącz logowanie jednokrotne** .

    d. Kliknij przycisk **Zastosuj ustawienia rejestracji jednokrotnej**.

### <a name="create-opsgenie-test-user"></a>Utwórz użytkownika testowego OpsGenie

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w OpsGenie. 

1. W oknie przeglądarki sieci Web Zaloguj się do dzierżawy OpsGenie jako administrator.

2. Przejdź do listy użytkowników, klikając pozycję **Użytkownicy** w lewym panelu.
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Kliknij pozycję **Dodaj użytkownika**.

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. W polu tekstowym **adres e-mail** wpisz adres e-mail B. Simon, który jest adresowany do Azure Active Directory.
   
    b. W polu tekstowym **pełna nazwa** wpisz **B. Simon**.
   
    c. Kliknij przycisk **Zapisz**. 

> [!NOTE]
> B. Simon Pobiera wiadomość e-mail z instrukcjami dotyczącymi konfigurowania profilu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpsGenie w panelu dostępu należy automatycznie zalogować się do OpsGenie, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj OpsGenie z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)