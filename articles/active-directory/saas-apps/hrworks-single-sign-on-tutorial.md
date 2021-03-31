---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z pojedynczym Sign-Onem HRworks | Microsoft Docs'
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
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442545"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z HRworks Single Sign-On

W tym samouczku dowiesz się, jak zintegrować HRworks Single Sign-On z Azure Active Directory (Azure AD). Po zintegrowaniu HRworks jednego Sign-On z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rejestracji jednokrotnej HRworks.
* Zezwól użytkownikom na automatyczne logowanie do HRworks jednego Sign-On z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (Single Sign-On HRworks).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* HRworks Single Sign-On obsługuje usługę **SP** zainicjowaną przez usługę SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Dodawanie jednego Sign-On HRworks z galerii

Aby skonfigurować integrację HRworks pojedynczej Sign-On z usługą Azure AD, musisz dodać do listy aplikacji zarządzanych SaaS HRworks pojedyncze Sign-On z galerii.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **HRworks Logowanie jednokrotne** w polu wyszukiwania.
1. Wybierz pozycję **HRworks Logowanie jednokrotne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla HRworks pojedynczej Sign-On

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą HRworks jednego Sign-On przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze HRworks Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego HRworks, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj pojedyncze Sign-On Logowanie jednokrotne](#configure-hrworks-single-sign-on-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego HRworks single Sign-On](#create-hrworks-single-sign-on-test-user)** , aby dysponować odpowiednikiem B. Simon w HRworks pojedynczej Sign-On, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
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
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej HRworks Single Sign-On Client](mailto:nadja.sommerfeld@hrworks.de) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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
   1. Kliknij pozycję **Utwórz**.

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

## <a name="configure-hrworks-single-sign-on-sso"></a>Skonfiguruj HRworks pojedyncze Sign-On Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach logowania jednokrotnego HRworks, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj logowanie** jednokrotne w usłudze HRworks spowoduje przekierowanie do aplikacji HRworks Single Sign-On. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do HRworks logowania jednokrotnego. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować HRworks pojedyncze Sign-On, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do swojej HRworks Sign-On pojedynczej witryny firmowej jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję   >  **podstawowe informacje** na temat zabezpieczeń Administrator Logowanie jednokrotne  >    >   z lewej strony paska menu i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego** .

    b. Wybierz pozycję **metadane XML** jako **metodę wejścia** metadanych.

    c. Wybierz **indywidualny identyfikator NameID** jako **wartość dla NameID**.

    d. W Notatniku otwórz plik XML metadanych pobrany z Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola tekstowego **metadanych** .

    e. Kliknij pozycję **Zapisz**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Utwórz użytkownika testowego HRworks Single Sign-On

Aby włączyć użytkowników usługi Azure AD, zaloguj się, aby korzystać z logowania jednokrotnego w usłudze HRworks, musisz zapewnić obsługę logowania jednokrotnego HRworks. W programie HRworks Logowanie jednokrotne jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby HRworks jeden Sign-On jako administrator.

1. Kliknij pozycję **administratorzy**  >  **osoby**  >    >   z lewej strony na pasku menu.

     ![Zrzut ekranu przedstawia zrzut ekranu przedstawiający stronę c R Works z wybranymi osobami i nową osobą.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. W oknie podręcznym kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia listę krajów, które można wybrać dla osoby.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Na wyskakującym okienku **Utwórz nową osobę z krajem dla warunków prawnych** Wypełnij odpowiednie szczegóły, takie jak **imię** i nazwisko **, nazwisko, a następnie** kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawia pola tekstowe, w których można wprowadzić imiona i nazwiska osoby.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka HRworks Single Sign-On w panelu dostępu należy automatycznie zalogować się do HRworks pojedynczego Sign-On, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj HRworks pojedyncze Sign-On z usługą Azure AD](https://aad.portal.azure.com/)