---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą BlueJeans dla usługi Azure AD | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i BlueJeans dla usługi Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: a920abacb0df030f7d38aed11e04a693cfa352a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686601"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą BlueJeans dla usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować usługę BlueJeans for Azure AD z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi BlueJeans for Azure AD z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do BlueJeans dla usługi Azure AD.
* Zezwól użytkownikom na automatyczne logowanie do usługi BlueJeans w usłudze Azure AD przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* BlueJeans dla subskrypcji z obsługą logowania jednokrotnego (SSO) usługi Azure AD.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* BlueJeans dla usługi Azure AD **obsługuje** zainicjowanie rejestracji jednokrotnej w programie

* Usługa BlueJeans for Azure AD obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Dodawanie BlueJeans dla usługi Azure AD z galerii

Aby skonfigurować integrację BlueJeans dla usługi Azure AD z usługą Azure AD, musisz dodać BlueJeans do usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BlueJeans dla usługi Azure AD** w polu wyszukiwania.
1. Wybierz pozycję **BlueJeans dla usługi Azure AD** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla BlueJeans dla usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą BlueJeans dla usługi Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze BlueJeans dla usługi Azure AD.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z usługą BlueJeans dla usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj BlueJeans dla logowania jednokrotnego usługi Azure AD](#configure-bluejeans-for-azure-ad-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz BlueJeans dla użytkownika testowego usługi Azure AD](#create-bluejeans-for-azure-ad-test-user)** , aby uzyskać odpowiednik B. Simon w BlueJeans dla usługi Azure AD, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **BlueJeans dla integracji aplikacji usługi Azure AD** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.bluejeans.com`

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `http://samlsp.bluejeans.com`

    a. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z [BlueJeans dla zespołu pomocy technicznej usługi Azure AD Client](https://support.bluejeans.com/contact) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja BlueJeans oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja BlueJeans oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------| --------- |
    | Telefon | user.telephonenumber |
    | tytuł | user.jobtitle |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi BlueJeans for Azure AD** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do BlueJeans dla usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BlueJeans dla usługi Azure AD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurowanie BlueJeans na potrzeby logowania jednokrotnego w usłudze Azure AD

1. W innym oknie przeglądarki sieci Web Zaloguj się do usługi **BlueJeans dla witryny firmy Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> GROUP SETTINGS (USTAWIENIA GRUPY) \> SECURITY (ZABEZPIECZENIA)**.

    ![Zrzut ekranu przedstawia część okna przeglądarki z wybraną kartą administrator z ustawieniem Grupa i zabezpieczenia wybrane.](./media/bluejeans-tutorial/ic785868.png "Administracja")

3. W sekcji **SECURITY** (ZABEZPIECZENIA) wykonaj następujące czynności:

    ![Logowanie jednokrotne SAML](./media/bluejeans-tutorial/ic785869.png "Logowanie jednokrotne SAML")

    a. Wybierz pozycję **SAML Single Sign On** (Logowanie jednokrotne SAML).

    b. Wybierz pozycję **Enable automatic provisioning** (Włącz automatyczną aprowizację).

4. Przejdź dalej, wykonując następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/ic785870.png "Ścieżka certyfikatu")

    a. Kliknij pozycję **Choose File** (Wybierz plik), aby przekazać certyfikat zakodowany w formacie base-64, pobrany wcześniej z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Password Change URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

5. Przejdź dalej, wykonując następujące czynności:

    ![Zapisz zmiany](./media/bluejeans-tutorial/ic785874.png "Zapisz zmiany")

    a. W polu tekstowym **Identyfikator użytkownika** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    b. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Tworzenie BlueJeans dla użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w BlueJeans dla usługi Azure AD. Usługa BlueJeans dla usługi Azure AD obsługuje automatyczne Inicjowanie obsługi użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](bluejeans-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do usługi **BlueJeans dla witryny firmy Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> MANAGE USERS (ZARZĄDZAJ UŻYTKOWNIKAMI) \> ADD USER (DODAJ UŻYTKOWNIKA)**.

    ![Zrzut ekranu przedstawia część okna przeglądarki z wybraną kartą administrator, z opcją Zarządzaj użytkownikami i Dodaj wybranych użytkowników.](./media/bluejeans-tutorial/ic785877.png "Administracja")

    > [!IMPORTANT]
    > Karta **ADD USER** (DODAJ UŻYTKOWNIKA) jest dostępna tylko wtedy, gdy na **karcie SECUTIRY** (ZABEZPIECZENIA) nie zaznaczono pola wyboru **Enable automatic provisioning** (Włącz automatyczną aprowizację).

3. W sekcji **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Dodawanie użytkownika, w której wprowadzane są informacje opisane w tym kroku.](./media/bluejeans-tutorial/ic785886.png "Dodaj użytkownika")

    a. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    b. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

    c. W polu tekstowym **Wybierz BlueJeans dla nazwy użytkownika usługi Azure AD** wprowadź nazwę użytkownika, na przykład **Brittasimon**

    d. W polu tekstowym **Create a Password** (Utwórz hasło) wprowadź hasło.

    e. W polu tekstowym **Company** (Firma) wprowadź nazwę Twojej firmy.

    f. W polu tekstowym **Email Address** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `b.simon@contoso.com`.

    przykład W polu tekstowym **Utwórz BlueJeans dla spotkania usługi Azure AD** wpisz swój identyfikator spotkania.

    h. W polu tekstowym **Pick a Moderator Passcode** (Wybierz kod dostępu moderatora) wprowadź kod dostępu.

    i. Kliknij przycisk **Kontynuuj**.

    ![Zrzut ekranu przedstawia sekcję Dodawanie użytkownika, w której można wyświetlić ustawienia i funkcje, po wybraniu przycisku Dodaj użytkownika.](./media/bluejeans-tutorial/ic785887.png "Dodaj użytkownika")

    J. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

> [!NOTE]
> Możesz użyć innych BlueJeans dla narzędzi do tworzenia kont użytkowników usługi Azure AD lub interfejsów API udostępnianych przez usługę Azure AD do obsługi kont użytkowników usługi Azure AD.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka BlueJeans dla usługi Azure AD w panelu dostępu należy automatycznie zalogować się do BlueJeans usługi Azure AD, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę BlueJeans dla usługi Azure AD z usługą Azure AD](https://aad.portal.azure.com/)