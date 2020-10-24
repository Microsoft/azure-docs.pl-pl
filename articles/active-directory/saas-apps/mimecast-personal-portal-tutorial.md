---
title: 'Samouczek: integracja Azure Active Directory z portalem osobistym Mimecast | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a portalem Mimecast Personal Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: ad7b5b97149d38b64d75f5a02cd0aa776893e832
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z portalem osobistym Mimecast

W tym samouczku dowiesz się, jak zintegrować Portal osobisty Mimecast z usługą Azure Active Directory (Azure AD). Po zintegrowaniu portalu osobistego Mimecast z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do portalu osobistego Mimecast.
* Zezwól użytkownikom na automatyczne logowanie do portalu osobistego Mimecast przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w portalu Mimecast.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Portal osobisty Mimecast obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu portalu osobistego Mimecast można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Dodawanie portalu Mimecast Personal Portal z galerii

Aby skonfigurować integrację portalu Mimecast Personal Portal z usługą Azure AD, należy dodać portal Mimecast Personal Portal z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Mimecast Personal Portal** w polu wyszukiwania.
1. Wybierz pozycję **Mimecast Personal Portal** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla portalu osobistego Mimecast

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą portalu osobistego Mimecast przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w portalu osobistym Mimecast.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą portalu osobistego Mimecast, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj Logowanie jednokrotne w **[portalu osobistym](#configure-mimecast-personal-portal-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego portalu osobistego Mimecast](#create-mimecast-personal-portal-test-user)** , aby uzyskać odpowiednika B. Simon w portalu osobistym Mimecast, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji **portalu osobistego Mimecast** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji dostawcy tożsamości, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | Region  |  Wartość | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > `accountcode`Wartość w portalu osobistym Mimecast można znaleźć w obszarze Ustawienia **konta**  >  **Settings**  >  **Kod konta**. Dołącz `accountcode` do identyfikatora.

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:

    | Region  |  Wartość |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL:

    | Region  |  Wartość |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do portalu osobistego Mimecast.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Mimecast Personal Portal**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-mimecast-personal-portal-sso"></a>Konfigurowanie logowania jednokrotnego w portalu osobistym Mimecast

1. W innym oknie przeglądarki sieci Web Zaloguj się do konsoli administracyjnej Mimecast.

1. Przejdź do **Administration**  >  **Services**  >  **aplikacji**usług administracyjnych.

    ![Zrzut ekranu przedstawia okno Mimecast z wybranymi aplikacjami.](./media/mimecast-personal-portal-tutorial/services.png)

1. Kliknij kartę **Profile uwierzytelniania** .
    
    ![Zrzut ekranu przedstawia kartę aplikacja z wybranymi profilami uwierzytelniania.](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Kliknij przycisk **Nowa karta Profil uwierzytelniania** .

    ![Zrzut ekranu przedstawia wybrany nowy profil uwierzytelniania.](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Podaj prawidłowy opis w polu tekstowym **Opis** i zaznacz pole wyboru **Wymuszaj uwierzytelnianie SAML dla usługi Mimecast Personal Portal** .

    ![Zrzut ekranu przedstawia wybrany nowy profil uwierzytelniania.](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Na stronie **Konfiguracja SAML portalu osobistego Mimecast** wykonaj następujące czynności:

    ![Zrzut ekranu pokazuje, gdzie zaznaczyć opcję Wymuś uwierzytelnianie SAML dla konsoli administracyjnej.](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. W obszarze **dostawca**wybierz pozycję **Azure Active Directory** z listy rozwijanej.

    b. W polu tekstowym **adres URL metadanych** wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal.

    c. Kliknij pozycję **Importuj**. Po zaimportowaniu adresu URL metadanych pola są wypełniane automatycznie, nie trzeba wykonywać żadnych akcji dla tych pól.

    d. Usuń zaznaczenie pola wyboru **Użyj kontekstu chronionego hasłem** i **Użyj opcji kontekstu uwierzytelniania zintegrowanego** .

    e. Kliknij pozycję **Zapisz**.

### <a name="create-mimecast-personal-portal-test-user"></a>Tworzenie użytkownika testowego portalu Mimecast Personal Portal

1. W innym oknie przeglądarki sieci Web Zaloguj się do konsoli administracyjnej Mimecast.

1. Przejdź do katalogu **Administracja**  >  **Directories**  >  **katalogi wewnętrzne**.

    ![Zrzut ekranu przedstawia konfigurację języka SAML dla portalu osobistego Mimecast, w którym można wprowadzić podane wartości.](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Wybierz domenę, jeśli domena jest wymieniona poniżej. w przeciwnym razie Utwórz nową domenę, klikając **nową domenę**.

    ![Zrzut ekranu przedstawia okno Mimecast z wybranymi katalogami wewnętrznymi.](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Kliknij przycisk **Nowa karta adres** .

    ![Zrzut ekranu przedstawia wybraną domenę.](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Podaj wymagane informacje o użytkowniku na następującej stronie:

    ![Zrzut ekranu przedstawia stronę, na której można wprowadzić podane wartości.](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika `B.Simon@yourdomainname.com` .

    b. W polu tekstowym **Nazwa globalna** wprowadź **pełną nazwę** użytkownika.

    c. W polach tekstowych **hasło** i **Potwierdź hasło** wprowadź hasło użytkownika.

    d. Zaznacz pole wyboru **Wymuszaj zmianę przy logowaniu** .

    e. Kliknij pozycję **Zapisz**.

    f. Aby przypisać role do użytkownika, kliknij pozycję **rola Edytuj** i przypisz wymagane role do użytkownika zgodnie z wymaganiami organizacji.

    ![Zrzut ekranu przedstawia ustawienia adresów, w których można wybrać opcję Edytuj rolę.](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Mimecast Personal Portal na panelu dostępu powinno nastąpić automatyczne zalogowanie do portalu Mimecast Personal Portal, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Portal osobisty Mimecast z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Ochrona portalu osobistego Mimecast z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)