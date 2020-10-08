---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z logowaniem jednokrotnym w usłudze HRworks | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowanie jednokrotne w usłudze HRworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 1a0fff21c85104498895c9de20de9961a77cffeb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820484"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z logowaniem jednokrotnym w usłudze HRworks

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w usłudze HRworks przy użyciu usługi Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne w usłudze HRworks z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do rejestracji jednokrotnej HRworks.
* Zezwól użytkownikom na automatyczne logowanie do HRworks logowania jednokrotnego przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) HRworks.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne w usłudze **HRworks obsługuje** zainicjowanie rejestracji Jednokrotnej dla

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Dodawanie logowania jednokrotnego HRworks z galerii

Aby skonfigurować integrację logowania jednokrotnego HRworks w usłudze Azure AD, musisz dodać Logowanie jednokrotne HRworks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **HRworks Logowanie jednokrotne** w polu wyszukiwania.
1. Wybierz pozycję **HRworks Logowanie jednokrotne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego HRworks

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze HRworks przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze HRworks Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego HRworks, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie](#configure-hrworks-single-sign-on-sso)** jednokrotne w usłudze HRworks, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego logowania](#create-hrworks-single-sign-on-test-user)** jednokrotnego (HRworks), aby uzyskać odpowiednika B. Simon w usłudze HRworks logowania jednokrotnego, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **logowania** jednokrotnego HRworks Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta rejestracji](mailto:nadja.sommerfeld@hrworks.de) jednokrotnej HRworks, aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie logowania** jednokrotnego HRworks skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do logowania jednokrotnego HRworks.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **HRworks Logowanie jednokrotne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-hrworks-single-sign-on-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze HRworks

1. Aby zautomatyzować konfigurację w ramach logowania jednokrotnego HRworks, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj logowanie** jednokrotne, aby przekierować użytkownika do aplikacji logowania jednokrotnego (HRworks). Z tego miejsca podaj poświadczenia administratora, aby zalogować się do HRworks logowania jednokrotnego. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować HRworks Logowanie jednokrotne, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmowej logowania jednokrotnego HRworks jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Administrator**  >  **podstawowe informacje**na temat zabezpieczeń Administrator Logowanie jednokrotne  >  **Security**  >  **Single Sign-on** z lewej strony paska menu i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego** .

    b. Wybierz pozycję **metadane XML** jako **metodę wejścia**metadanych.

    c. Wybierz **indywidualny identyfikator NameID** jako **wartość dla NameID**.

    d. W Notatniku otwórz plik XML metadanych pobrany z Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola tekstowego **metadanych** .

    e. Kliknij przycisk **Zapisz**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Utwórz użytkownika testowego logowania jednokrotnego HRworks

Aby włączyć użytkowników usługi Azure AD, zaloguj się, aby korzystać z logowania jednokrotnego w usłudze HRworks, musisz zapewnić obsługę logowania jednokrotnego HRworks. W programie HRworks Logowanie jednokrotne jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby HRworks Logowanie jednokrotne jako administrator.

1. Kliknij pozycję **administratorzy**  >  **osoby**  >  **Persons**  >  **New person** z lewej strony na pasku menu.

     ![Zrzut ekranu przedstawia zrzut ekranu przedstawiający stronę c R Works z wybranymi osobami i nową osobą.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. W oknie podręcznym kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia listę krajów, które można wybrać dla osoby.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Na wyskakującym okienku **Utwórz nową osobę z krajem dla warunków prawnych** Wypełnij odpowiednie szczegóły, takie jak **imię**i nazwisko **, nazwisko, a następnie** kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawia pola tekstowe, w których można wprowadzić imiona i nazwiska osoby.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logowania jednokrotnego HRworks w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego HRworks, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Logowanie jednokrotne w usłudze HRworks przy użyciu usługi Azure AD](https://aad.portal.azure.com/)