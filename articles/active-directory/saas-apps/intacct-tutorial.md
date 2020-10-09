---
title: 'Samouczek: integracja Azure Active Directory z programem Sage Intacct | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: jeedes
ms.openlocfilehash: e01bc36f0f30cec779652a603aaf7bdb496e5190
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850578"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Samouczek: Integrowanie programu Sage Intacct z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować program Sage Intacct z Azure Active Directory (Azure AD). Gdy integrujesz program Sage Intacct z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do Intacct Sage.
* Zezwól użytkownikom na automatyczne logowanie do programu Sage Intacct przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w programie Sage Intacct.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Sage Intacct obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne
* Po skonfigurowaniu programu Sage Intacct można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sage-intacct-from-the-gallery"></a>Dodawanie Intacct Sage z galerii

Aby skonfigurować integrację programu Sage Intacct z usługą Azure AD, musisz dodać Sage Intacct z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Sage Intacct** w polu wyszukiwania.
1. Wybierz pozycję **Sage Intacct** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Sage Intacct

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Sage Intacct przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Sage Intacct.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Sage Intacct, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
2. **[Skonfiguruj Logowanie jednokrotne w programie Sage Intacct](#configure-sage-intacct-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Intacct](#create-sage-intacct-test-user)** w programie Sage, aby dysponować odpowiednikiem B. Simon w programie Sage Intacct, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji w programie **Sage Intacct** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL:  `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Aplikacja Sage Intacct oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe atrybuty użytkownika...

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Sage Intacct oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W oknie dialogowym **atrybuty użytkownika & oświadczenia** wykonaj następujące kroki, aby dodać ATRYBUT tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa atrybutu  |  Atrybut źródłowy|
    | ---------------| --------------- |
    | Nazwa firmy | **Identyfikator firmy Sage Intacct** |
    | name | Wartość powinna być taka sama jak **Nazwa użytkownika**Sage Intacct, którą wprowadzasz w **sekcji Tworzenie użytkownika testowego programu Sage Intacct**, która została omówiona w dalszej części tego samouczka |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Z listy **atrybutów źródłowych** wpisz lub wybierz wartość atrybutu wyświetlaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu Sage Intacct** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Sage Intacct.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Sage Intacct**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sage-intacct-sso"></a>Konfigurowanie logowania jednokrotnego w programie Sage Intacct

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Sage Intacct jako administrator.

1. Kliknij kartę **firma** , a następnie kliknij pozycję **Informacje o firmie**.

    ![Przedsiębiorstwo](./media/intacct-tutorial/ic790037.png "Firma")

1. Kliknij kartę **zabezpieczenia** , a następnie kliknij przycisk **Edytuj**.

    ![Zabezpieczenia](./media/intacct-tutorial/ic790038.png "Zabezpieczenia")

1. W sekcji Logowanie jednokrotne **(SSO)** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/intacct-tutorial/ic790039.png "Logowanie jednokrotne")

    a. Wybierz pozycję **Włącz logowanie jednokrotne**.

    b. Jako **Typ dostawcy tożsamości**wybierz pozycję **SAML 2,0**.

    c. W polu tekstowym **adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    e. Otwórz certyfikat zakodowany w formacie **Base-64** w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola **certyfikat** .

    f. Kliknij przycisk **Zapisz**.

### <a name="create-sage-intacct-test-user"></a>Utwórz użytkownika testowego Intacct Sage

Aby skonfigurować użytkowników usługi Azure AD, aby mogli się zalogować do Intacct Sage, muszą one być obsługiwane w programie Sage Intacct. W przypadku programu Sage Intacct Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy w programie **Sage Intacct** .

1. Kliknij kartę **firma** , a następnie kliknij pozycję **Użytkownicy**.

    ![Użytkownicy](./media/intacct-tutorial/ic790041.png "Użytkownicy")

1. Kliknij kartę **Dodaj** .

    ![Dodaj](./media/intacct-tutorial/ic790042.png "Dodaj")

1. W sekcji **Informacje o użytkowniku** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Informacje o użytkowniku, w której można wprowadzić informacje w tym kroku.](./media/intacct-tutorial/ic790043.png "Informacje o użytkowniku")

    a. Wprowadź **Identyfikator użytkownika**, **nazwisko**, **imię**i nazwisko, **adres E-mail**, **tytuł**oraz **numer telefonu** konta usługi Azure AD, które chcesz udostępnić w sekcji **Informacje o użytkowniku** .

    > [!NOTE]
    > Upewnij się, że **Identyfikator użytkownika** na wyższym zrzucie i wartość **atrybutu źródłowego** , który jest mapowany przy użyciu atrybutu **name** w sekcji **atrybuty użytkownika** w Azure Portal powinna być taka sama.

    b. Wybierz **uprawnienia administratora** konta usługi Azure AD, które chcesz udostępnić.

    c. Kliknij przycisk **Zapisz**. 
    
    d. Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail, a następnie łączy się z linkiem, aby potwierdzić swoje konto.

1. Kliknij kartę **Logowanie jednokrotne** i upewnij się, że **Identyfikator użytkownika federacyjnego logowania jednokrotnego** na poniższym zrzucie ekranu oraz wartość **atrybutu źródłowego** , która jest mapowana za pomocą `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` w sekcji **atrybuty użytkownika** w Azure Portal powinna być taka sama.

    ![Zrzut ekranu przedstawia sekcję Informacje o użytkowniku, w której można wprowadzić federacyjne i d.](./media/intacct-tutorial/ic790044.png "Informacje o użytkowniku")

> [!NOTE]
> Aby zainicjować obsługę kont użytkowników usługi Azure AD, możesz użyć innych narzędzi do tworzenia kont użytkowników w programie Sage Intacct i interfejsów API udostępnianych przez program Sage Intacct.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Sage Intacct w panelu dostępu należy automatycznie zalogować się do programu Sage Intacct, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
