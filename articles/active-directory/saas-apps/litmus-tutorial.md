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
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: f2168715d59f9698cba58b7e91bbc897a8cd94f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727288"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Litmus

W tym samouczku dowiesz się, jak zintegrować usługę Litmus z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Litmus z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Litmus.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Litmus przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Litmus.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Litmus obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-litmus-from-the-gallery"></a>Dodawanie Litmus z galerii

Aby skonfigurować integrację programu Litmus z usługą Azure AD, musisz dodać Litmus z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Litmus** w polu wyszukiwania.
1. Wybierz pozycję **Litmus** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Litmus

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Litmus przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Litmus.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Litmus, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-litmus-sso)** w usłudze Litmus, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Litmus](#create-litmus-test-user)** , aby dysponować odpowiednikiem B. Simon w Litmus, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Litmus** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://litmus.com/sessions/new`

1. Kliknij pozycję **Zapisz**.

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
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Litmus.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Litmus**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-litmus-sso"></a>Konfigurowanie logowania jednokrotnego Litmus

1. Aby zautomatyzować konfigurację w programie Litmus, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji Litmus. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Litmus. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Litmus, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Litmus jako administrator.

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

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Litmus, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL logowania Litmus i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Litmus, dla którego skonfigurowano Logowanie jednokrotne

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Litmus w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Litmus, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Litmus można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).