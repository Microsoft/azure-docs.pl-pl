---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą rozwiązania Zscaler | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i rozwiązania Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.openlocfilehash: 41867e0c085d96c8eb00042e84dd3f030317a217
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować usługę rozwiązania Zscaler z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi rozwiązania Zscaler z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania Zscaler.
* Zezwól użytkownikom na automatyczne logowanie się do usługi rozwiązania Zscaler przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) rozwiązania Zscaler.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązania Zscaler obsługuje logowanie jednokrotne w usłudze **SP**
* Rozwiązania Zscaler obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie rozwiązania Zscaler z galerii

Aby skonfigurować integrację programu rozwiązania Zscaler z usługą Azure AD, musisz dodać rozwiązania Zscaler z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **rozwiązania Zscaler** w polu wyszukiwania.
1. Wybierz pozycję **rozwiązania Zscaler** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla rozwiązania Zscaler

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą rozwiązania Zscaler przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w rozwiązania Zscaler.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą rozwiązania Zscaler, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-zscaler-sso)** w usłudze rozwiązania Zscaler, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego rozwiązania Zscaler](#create-zscaler-test-user)** , aby dysponować odpowiednikiem B. Simon w rozwiązania Zscaler, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **rozwiązania Zscaler** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.zscaler.net`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta rozwiązania Zscaler](https://www.zscaler.com/company/contact) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja rozwiązania Zscaler oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja rozwiązania Zscaler oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](../develop/active-directory-enterprise-app-role-management.md), aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie rozwiązania Zscaler** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi rozwiązania Zscaler.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler**.

    ![Link rozwiązania Zscaler na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

    ![image (obraz)](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. W oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę użytkownika na liście, a następnie kliknij przycisk **Wybierz** znajdujący się u dołu ekranu.

    ![image (obraz)](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

    ![image (obraz)](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Konfigurowanie logowania jednokrotnego rozwiązania Zscaler

1. Aby zautomatyzować konfigurację w programie rozwiązania Zscaler, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup rozwiązania Zscaler** , aby skierować do aplikacji rozwiązania Zscaler. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi rozwiązania Zscaler. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfigurowanie logowania jednokrotnego](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować rozwiązania Zscaler, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy rozwiązania Zscaler jako administrator i wykonaj następujące czynności:

1. Przejdź do obszaru **Administracja > Uwierzytelnianie > Ustawienia uwierzytelniania** i wykonaj następujące kroki:

    ![Administracja](./media/zscaler-tutorial/ic800206.png "Administracja")

    a. W obszarze Typ uwierzytelniania wybierz pozycję **SAML**.

    b. Kliknij pozycję **Skonfiguruj język SAML**.

1. W oknie **Edytowanie języka SAML** wykonaj następujące kroki i kliknij pozycję Zapisz.  

    ![Zarządzanie użytkownikami & uwierzytelnianie](./media/zscaler-tutorial/ic800208.png "Zarządzanie użytkownikami & uwierzytelnianie")
    
    a. W polu tekstowym **Adres URL portalu języka SAML** wklej **adres URL logowania** skopiowany z witryny Azure Portal.

    b. W polu tekstowym **Atrybut nazwy logowania** wprowadź identyfikator **NameID**.

    c. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    d. Przełącz element **Włącz automatyczne aprowizowanie języka SAML**.

    e. W polu tekstowym **Atrybut nazwy wyświetlanej użytkownika** wprowadź ciąg **displayName**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu displayName.

    f. W polu tekstowym **Atrybut nazwy grupy** wprowadź ciąg **memberOf**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu memberOf.

    przykład W polu **Atrybut nazwy działu** wprowadź ciąg **department**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu department.

    h. Kliknij pozycję **Zapisz**.

1. Na stronie okna dialogowanie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące kroki:

    ![Administracja](./media/zscaler-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurowanie ustawień serwera proxy w programie Internet Explorer

1. Uruchom program **Internet Explorer**.

1. Wybierz pozycję **Opcje internetowe** z menu **Narzędzia**, aby otworzyć okno dialogowe **Opcje internetowe**.

    ![Opcje internetowe](./media/zscaler-tutorial/ic769492.png "Opcje internetowe")

1. Kliknij kartę **Połączenia**.
  
    ![Połączenia](./media/zscaler-tutorial/ic769493.png "Połączenia")

1. Kliknij przycisk **Ustawienia sieci LAN**, aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

1. W sekcji Serwer proxy wykonaj następujące kroki:   

    ![Serwer proxy](./media/zscaler-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym Adres wpisz **Gateway.Zscaler.NET**.

    c. W polu tekstowym Port wpisz **80**.

    d. Zaznacz pole wyboru **Nie używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Opcje internetowe**.

### <a name="create-zscaler-test-user"></a>Utwórz użytkownika testowego rozwiązania Zscaler

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w rozwiązania Zscaler. Rozwiązania Zscaler obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze rozwiązania Zscaler, zostanie utworzony nowy po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler w panelu dostępu należy automatycznie zalogować się do rozwiązania Zscaler, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj rozwiązania Zscaler z usługą Azure AD](https://aad.portal.azure.com/)