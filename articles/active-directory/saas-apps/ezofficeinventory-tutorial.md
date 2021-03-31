---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EZOfficeInventory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EZOfficeInventory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: dcf64e6538140905e00e0fcd9d0383f15e8ad95a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453675"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą EZOfficeInventory

W tym samouczku dowiesz się, jak zintegrować usługę EZOfficeInventory z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EZOfficeInventory z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EZOfficeInventory.
* Zezwól użytkownikom na automatyczne logowanie się do usługi EZOfficeInventory przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) EZOfficeInventory.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* EZOfficeInventory obsługuje logowanie jednokrotne w usłudze **SP**
* EZOfficeInventory obsługuje Inicjowanie obsługi użytkowników **just in Time**
* Po skonfigurowaniu EZOfficeInventory można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Dodawanie EZOfficeInventory z galerii

Aby skonfigurować integrację programu EZOfficeInventory z usługą Azure AD, musisz dodać EZOfficeInventory z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EZOfficeInventory** w polu wyszukiwania.
1. Wybierz pozycję **EZOfficeInventory** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla EZOfficeInventory

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EZOfficeInventory przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EZOfficeInventory.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EZOfficeInventory, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-ezofficeinventory-sso)** w usłudze EZOfficeInventory, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego EZOfficeInventory](#create-ezofficeinventory-test-user)** , aby dysponować odpowiednikiem B. Simon w EZOfficeInventory, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **EZOfficeInventory** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta EZOfficeInventory](mailto:support@ezofficeinventory.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja EZOfficeInventory oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja EZOfficeInventory oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | poczta e-mail | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie EZOfficeInventory** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi EZOfficeInventory.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EZOfficeInventory**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ezofficeinventory-sso"></a>Konfigurowanie logowania jednokrotnego EZOfficeInventory

1. Aby zautomatyzować konfigurację w programie EZOfficeInventory, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji EZOfficeInventory. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi EZOfficeInventory. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować EZOfficeInventory, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy EZOfficeInventory jako administrator i wykonaj następujące czynności:

1. W prawym górnym rogu strony kliknij pozycję **profil** , a następnie przejdź do obszaru **Ustawienia**  >  **Dodatki**.

    ![Zrzut ekranu przedstawiający stronę "Ustawienia" z wybraną akcją "Dodaj".](./media/ezofficeinventory-tutorial/configure01.png)

1. Przewiń w dół do sekcji **integracja SAML** , wykonaj następujące czynności:

    ![Konfiguracja EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Zaznacz opcję **włączone** .

    b. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. Otwórz w Notatniku certyfikat szyfrowany algorytmem Base64, skopiuj jego zawartość i wklej go do pola tekstowego **certyfikat dostawcy tożsamości** .

    d. W polu tekstowym tekst **przycisku logowania** wprowadź tekst przycisku Zaloguj.

    e. W polu tekstowym **imię i nazwisko** wprowadź **first_name**.

    f. W polu tekstowym **nazwisko** wpisz **last_name**.

    przykład W polu tekstowym **adres e-mail** wprowadź **adres e-mail**.

    h. Wybierz rolę zgodnie z wymaganiami z **roli EZOfficeInventory przez opcję domyślną** .

    i. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-ezofficeinventory-test-user"></a>Utwórz użytkownika testowego EZOfficeInventory

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w EZOfficeInventory. EZOfficeInventory obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze EZOfficeInventory, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka EZOfficeInventory w panelu dostępu należy automatycznie zalogować się do EZOfficeInventory, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Wypróbuj EZOfficeInventory z usługą Azure AD](https://aad.portal.azure.com/)