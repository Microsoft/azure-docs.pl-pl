---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą IntelligenceBank | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i IntelligenceBank.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 9887c435c2aa8dc7ba8cab9481cf30195c4b334e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą IntelligenceBank

W tym samouczku dowiesz się, jak zintegrować usługę IntelligenceBank z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi IntelligenceBank z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do IntelligenceBank.
* Zezwól użytkownikom na automatyczne logowanie się do usługi IntelligenceBank przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) IntelligenceBank.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* IntelligenceBank obsługuje logowanie jednokrotne w usłudze **SP**

* Po skonfigurowaniu IntelligenceBank można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-intelligencebank-from-the-gallery"></a>Dodawanie IntelligenceBank z galerii

Aby skonfigurować integrację programu IntelligenceBank z usługą Azure AD, musisz dodać IntelligenceBank z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **IntelligenceBank** w polu wyszukiwania.
1. Wybierz pozycję **IntelligenceBank** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-intelligencebank"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla IntelligenceBank

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą IntelligenceBank przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w IntelligenceBank.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą IntelligenceBank, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-intelligencebank-sso)** w usłudze IntelligenceBank, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego IntelligenceBank](#create-intelligencebank-test-user)** , aby dysponować odpowiednikiem B. Simon w IntelligenceBank, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **IntelligenceBank** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.intelligencebank.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** Użyj dowolnej z następujących elementów:

    - `IB`
    - `IntelligenceBank`
    - `https://<SUBDOMAIN>.intelligencebank.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta IntelligenceBank](mailto:helpdesk@intelligencebank.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie IntelligenceBank** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi IntelligenceBank.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **IntelligenceBank**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-intelligencebank-sso"></a>Konfigurowanie logowania jednokrotnego IntelligenceBank

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny IntelligenceBank jako administrator.

1. Kliknij pozycję **wystawca uwierzytelnienia** i kliknij pozycję **Dodaj nowe**

    ![Zrzut ekranu przedstawia wybraną kartę administrator i Dodaj nową ikonę.](./media/intelligencebank-tutorial/authenticator.PNG)

1. Wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia pola, w których wprowadzane są informacje w tym kroku.](./media/intelligencebank-tutorial/urls.PNG)

    a. W polu tekstowym **Nazwa** wprowadź nazwę na przykład `azureadsso` .

    b. W polu tekstowym **Opis** Wprowadź prawidłowy opis.

    c. Wybierz pozycję **SAML** z listy rozwijanej jako **Typ**.

    d. W polu tekstowym **zdalny adres URL** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **hosta** wklej wartość **identyfikatora jednostki** , która została skopiowana z Azure Portal.

    f. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **CertData**

    przykład W polu tekstowym **SingleLogoutService** wklej wartość pola **adres URL wylogowania** , która została skopiowana z Azure Portal.

    h. Kliknij przycisk **Zapisz** .

### <a name="create-intelligencebank-test-user"></a>Utwórz użytkownika testowego IntelligenceBank

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny IntelligenceBank jako administrator.

1. Przejdź do pozycji użytkownicy **administracyjni**  ->   i wybierz **ikonę Dodaj nową użytkownika** , aby dodać **użytkownika**.

    ![Zrzut ekranu przedstawia ikonę Użytkownicy wybraną na karcie Użytkownicy.](./media/intelligencebank-tutorial/creating-user.PNG)

1. Wypełnij niezbędne pola zgodnie z wymaganiami organizacji, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawia stronę Dodawanie nowego użytkownika, w której wprowadzane są informacje o użytkowniku.](./media/intelligencebank-tutorial/creating-user-1.PNG)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka IntelligenceBank w panelu dostępu należy automatycznie zalogować się do IntelligenceBank, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj IntelligenceBank z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić IntelligenceBank z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)