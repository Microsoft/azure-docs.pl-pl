---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Freshservice | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Freshservice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Freshservice

W tym samouczku dowiesz się, jak zintegrować usługę Freshservice z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Freshservice z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Freshservice.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Freshservice przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Freshservice.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Freshservice obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="add-freshservice-from-the-gallery"></a>Dodaj Freshservice z galerii

Aby skonfigurować integrację aplikacji Freshservice z usługą Azure AD, musisz dodać aplikację Freshservice z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freshservice** w polu wyszukiwania.
1. Wybierz pozycję **Freshservice** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Freshservice

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Freshservice przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Freshservice.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Freshservice, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-freshservice-sso)** w usłudze Freshservice, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Freshservice](#create-freshservice-test-user)** , aby dysponować odpowiednikiem B. Simon w Freshservice, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Freshservice** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company-name>.freshservice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<company-name>.freshservice.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Freshservice](https://support.freshservice.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Freshservice** na **Azure Portal** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Freshservice.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Freshservice**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-freshservice-sso"></a>Konfigurowanie logowania jednokrotnego Freshservice

1. Aby zautomatyzować konfigurację w programie Freshservice, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji Freshservice. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Freshservice. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować Freshservice, zaloguj się do witryny firmy Freshservice jako administrator.

1. W menu po lewej stronie kliknij pozycję **administrator** , a następnie wybierz pozycję **zabezpieczenia pomocy technicznej** w **ustawieniach ogólnych**.

    ![Administracja](./media/freshservice-tutorial/configure-1.png "Administracja")

1. W obszarze **zabezpieczenia** kliknij pozycję **przejdź do Freshservice 360 zabezpieczenia**.

    ![Bezpieczeństwo](./media/freshservice-tutorial/configure-2.png "Zabezpieczenia")

1. W sekcji **zabezpieczenia** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/freshservice-tutorial/configure-3.png "Logowanie jednokrotne")
  
    a. W przypadku **logowania jednokrotnego** wybierz pozycję **włączone**.

    b. W **metodzie logowania** wybierz pozycję **SAML SSO**.

    c. W **identyfikatorze jednostki podanej przez** pole tekstowe dostawcy tożsamości wklej wartość **identyfikatora jednostki** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL logowania JEDNOkrotnego SAML** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W **opcjach podpisywania** wybierz **tylko podpisane potwierdzenia** z listy rozwijanej.

    f. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    przykład W polu tekstowym **certyfikat zabezpieczeń** wklej wartość **certyfikatu (base64)** , którą uzyskano wcześniej.
  
    h. Kliknij pozycję **Zapisz**.


## <a name="create-freshservice-test-user"></a>Tworzenie użytkownika testowego aplikacji Freshservice

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze FreshService, muszą one być obsługiwane w usłudze FreshService. W przypadku aplikacji FreshService jest to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny **FreshService** jako administrator.

2. W menu po lewej stronie kliknij pozycję **Administracja**.

3. W sekcji **User Management** (Zarządzanie użytkownikami) kliknij pozycję **Requesters** (Inicjatorzy żądania).

    ![Jednostek żądających](./media/freshservice-tutorial/create-user-1.png "Jednostek żądających")

4. Kliknij przycisk **New Requester** (Nowy inicjator żądania).

    ![Nowi osoby żądające](./media/freshservice-tutorial/create-user-2.png "Nowi osoby żądające")

5. W **nowej sekcji żądającej** wprowadź wymagane pola i kliknij przycisk **Zapisz**.
    ![Nowy obiekt żądający](./media/freshservice-tutorial/create-user-3.png "Nowy obiekt żądający")  

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    >  

    > [!NOTE]
    > Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników FreshService i interfejsów API udostępnionych przez usługę FreshService.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Freshservice, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Freshservice i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Freshservice w obszarze Moje aplikacje należy automatycznie zalogować się do Freshservice, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

 Po skonfigurowaniu Freshservice można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).