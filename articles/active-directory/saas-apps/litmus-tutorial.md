---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Litmus | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: a050f7a7d86ef8586cab3e90d9c1bac310a7f18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854549"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Litmus

W tym samouczku dowiesz się, jak zintegrować usługę Litmus z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Litmus z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Litmus.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Litmus przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Litmus.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Litmus obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu Litmus można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-litmus-from-the-gallery"></a>Dodawanie Litmus z galerii

Aby skonfigurować integrację programu Litmus z usługą Azure AD, musisz dodać Litmus z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Litmus** w polu wyszukiwania.
1. Wybierz pozycję **Litmus** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Litmus

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Litmus przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Litmus.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Litmus, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-litmus-sso)** w usłudze Litmus, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Litmus](#create-litmus-test-user)** , aby dysponować odpowiednikiem B. Simon w Litmus, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Litmus** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://litmus.com/sessions/new`

1. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie Litmus** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Litmus.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Litmus**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-litmus-sso"></a>Konfigurowanie logowania jednokrotnego Litmus

1. W innym oknie przeglądarki sieci Web Zaloguj się do aplikacji Litmus jako administrator.

1. Kliknij pozycję **zabezpieczenia** w lewym panelu nawigacyjnym.

    ![Zrzut ekranu przedstawia wybrany element zabezpieczeń.](./media/litmus-tutorial/security-img.png)

1. W sekcji **Konfigurowanie uwierzytelniania SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Konfigurowanie uwierzytelniania SAML, w której można wprowadzić podane wartości.](./media/litmus-tutorial/configure1.png)

    a. **Włącz przełącznik SAML** .

    b. Wybierz pozycję **Ogólne** dla dostawcy.

    c. Wprowadź nazwę **dostawcy tożsamości**. na przykład `Azure AD`

1. Wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję, w której można wprowadzić podane wartości.](./media/litmus-tutorial/configure3.png)

    a. W polu tekstowym **punkt końcowy SAML 2,0 (http)** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    b. Otwórz pobrany plik **certyfikatu** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu X. 509** .

    c. Kliknij pozycję **Zapisz ustawienia SAML**.

### <a name="create-litmus-test-user"></a>Utwórz użytkownika testowego Litmus

1. W innym oknie przeglądarki sieci Web Zaloguj się do aplikacji Litmus jako administrator.

1. Kliknij pozycję **konta** w lewym panelu nawigacyjnym.

    ![Zrzut ekranu przedstawia wybrany element konta.](./media/litmus-tutorial/accounts-img.png)

1. Kliknij przycisk **Dodaj nową kartę użytkownika** .

    ![Zrzut ekranu pokazuje wybrany nowy element użytkownika.](./media/litmus-tutorial/add-new-user.png)

1. W sekcji **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Dodawanie użytkownika, w której można wprowadzić podane wartości.](./media/litmus-tutorial/user-profile.png)

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, na przykład **B. Simon \@ contoso.com**

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **B**.

    c. W polu **tekstowym nazwisko Wprowadź nazwisko użytkownika** , np. **Simon**.

    d. Kliknij pozycję **Create User** (Utwórz użytkownika).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Litmus w panelu dostępu należy automatycznie zalogować się do Litmus, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Litmus z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Litmus z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
