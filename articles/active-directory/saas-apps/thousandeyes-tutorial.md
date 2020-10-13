---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą ThousandEyes | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: 2b99d5ea33693431fb3811af50385891684a366e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546461"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą ThousandEyes

W tym samouczku dowiesz się, jak zintegrować usługę ThousandEyes z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ThousandEyes z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do ThousandEyes.
* Zezwól użytkownikom na automatyczne logowanie się do usługi ThousandEyes przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) ThousandEyes.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ThousandEyes obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* ThousandEyes obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-thousandeyes-from-the-gallery"></a>Dodawanie ThousandEyes z galerii

Aby skonfigurować integrację programu ThousandEyes z usługą Azure AD, musisz dodać ThousandEyes z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ThousandEyes** w polu wyszukiwania.
1. Wybierz pozycję **ThousandEyes** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla ThousandEyes

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą ThousandEyes przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ThousandEyes.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą ThousandEyes, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-thousandeyes-sso)** w usłudze ThousandEyes, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego ThousandEyes](#create-thousandeyes-test-user)** , aby dysponować odpowiednikiem B. Simon w ThousandEyes, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ThousandEyes** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.thousandeyes.com/login/sso`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie ThousandEyes** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi ThousandEyes.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ThousandEyes**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-thousandeyes-sso"></a>Konfigurowanie logowania jednokrotnego ThousandEyes

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny **ThousandEyes** jako administrator.

2. W menu u góry kliknij pozycję **Settings** (Ustawienia).

    ![Ustawienia](./media/thousandeyes-tutorial/ic790066.png "Ustawienia")

3. Kliknij pozycję **konto**

    ![Konto](./media/thousandeyes-tutorial/ic790067.png "Konto")

4. Kliknij kartę **uwierzytelnianie & zabezpieczeń** .

    ![Uwierzytelnianie & zabezpieczeń](./media/thousandeyes-tutorial/ic790068.png "Uwierzytelnianie & zabezpieczeń")

5. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    ![Skonfiguruj Logowanie jednokrotne](./media/thousandeyes-tutorial/ic790069.png "Skonfiguruj pojedynczy Sign-On")

    a. Wybierz pozycję **Włącz logowanie jednokrotne**.

    b. W polu tekstowym **adres URL strony logowania** wklej **adres URL logowania**, który został skopiowany z Azure Portal.

    c. W polu tekstowym **adres URL strony wylogowywania** wklej **adres URL wylogowania**, który został skopiowany z Azure Portal.

    d. Pole tekstowe **wystawcy dostawcy tożsamości** , wklej **Identyfikator usługi Azure AD**, który został skopiowany z Azure Portal.

    e. W obszarze **certyfikat weryfikacji**kliknij pozycję **Wybierz plik**, a następnie przekaż certyfikat pobrany z Azure Portal.

    f. Kliknij przycisk **Zapisz**.

### <a name="create-thousandeyes-test-user"></a>Utwórz użytkownika testowego ThousandEyes

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w ThousandEyes. ThousandEyes obsługuje automatyczne Inicjowanie obsługi użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](thousandeyes-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny ThousandEyes jako administrator.

2. Kliknij polecenie **Ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/IC790066.png "Ustawienia")

3. Kliknij pozycję **Account** (Konto).

    ![Konto](./media/thousandeyes-tutorial/IC790067.png "Konto")

4. Kliknij kartę **konta & użytkownicy** .

    ![Konta & użytkowników](./media/thousandeyes-tutorial/IC790073.png "Konta & użytkowników")

5. W sekcji **Dodawanie użytkowników & kont** wykonaj następujące czynności:

    ![Dodawanie kont użytkowników](./media/thousandeyes-tutorial/IC790074.png "Dodawanie kont użytkowników")

    a. W polu tekstowym **Nazwa** wpisz nazwę użytkownika, na przykład **B. Simon**.

    b. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika, np b.simon@contoso.com .

    b. Kliknij pozycję **Dodaj nowego użytkownika do konta**.

    > [!NOTE]
    > Właściciel konta Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie i aktywowanie konta.

> [!NOTE]
> Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program ThousandEyes.


## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ThousandEyes w panelu dostępu należy automatycznie zalogować się do ThousandEyes, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj ThousandEyes z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)