---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą KnowledgeOwl | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 5fe09d1543b26b721b621cc6bd31fc034b54c967
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556787"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą KnowledgeOwl

W tym samouczku dowiesz się, jak zintegrować usługę KnowledgeOwl z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi KnowledgeOwl z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do KnowledgeOwl.
* Zezwól użytkownikom na automatyczne logowanie się do usługi KnowledgeOwl przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) KnowledgeOwl.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa KnowledgeOwl obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .
* KnowledgeOwl obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="add-knowledgeowl-from-the-gallery"></a>Dodaj KnowledgeOwl z galerii

Aby skonfigurować integrację programu KnowledgeOwl z usługą Azure AD, musisz dodać KnowledgeOwl z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **KnowledgeOwl** w polu wyszukiwania.
1. Wybierz pozycję **KnowledgeOwl** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla KnowledgeOwl

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą KnowledgeOwl przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w KnowledgeOwl.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą KnowledgeOwl, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-knowledgeowl-sso)** w usłudze KnowledgeOwl, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego KnowledgeOwl](#create-knowledgeowl-test-user)** , aby dysponować odpowiednikiem B. Simon w KnowledgeOwl, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **KnowledgeOwl** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości z rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL Sign-On, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja KnowledgeOwl oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja KnowledgeOwl oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy | Przestrzeń nazw |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie KnowledgeOwl** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi KnowledgeOwl.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **KnowledgeOwl**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-knowledgeowl-sso"></a>Konfigurowanie logowania jednokrotnego KnowledgeOwl

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny KnowledgeOwl jako administrator.

1. Kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **zabezpieczenia**.

    ![Zrzut ekranu przedstawia zabezpieczenia wybrane w menu Ustawienia.](./media/knowledgeowl-tutorial/configure-1.png)

1. Przewiń do **integracji SAML SSO** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia integrację protokołu SAML S O, w której można wprowadzić zmiany opisane tutaj.](./media/knowledgeowl-tutorial/configure-2.png)

    a. Wybierz pozycję **Włącz logowanie jednokrotne SAML**.

    b. Skopiuj wartość **Identyfikator jednostki Sp** i wklej ją do **identyfikatora (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    c. Skopiuj wartość **adres URL logowania** do programu SP i wklej ją do pól tekstowych **adres URL logowania i adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    d. W polu tekstowym **dostawcy tożsamości entityID** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    f. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    przykład Przekaż pobrany certyfikat w Azure Portal przez kliknięcie przycisku **Przekaż certyfikat dostawcy tożsamości**.

    h. Kliknij pozycję **Mapuj atrybuty SAML** , aby mapować atrybuty, i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia Mapowanie atrybutów SAML, w których można wprowadzać zmiany w tym miejscu.](./media/knowledgeowl-tutorial/configure-3.png)

    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do pola tekstowego **identyfikatora logowania jednokrotnego**
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` w polu tekstowym **username/email** .
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` w polu tekstowym **imię i nazwisko** .
    * Wprowadź tekst `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` w polu tekstowym **nazwisko** .
    * Kliknij pozycję **Zapisz**.

    i. Kliknij przycisk **Zapisz** w dolnej części strony.

    ![Zrzut ekranu przedstawiający przycisk Zapisz.](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>Utwórz użytkownika testowego KnowledgeOwl

W tej sekcji użytkownik o nazwie B. Simon został utworzony w KnowledgeOwl. KnowledgeOwl obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze KnowledgeOwl, zostanie utworzony nowy po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania KnowledgeOwl, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania KnowledgeOwl i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do KnowledgeOwl, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka KnowledgeOwl w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do KnowledgeOwl, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu KnowledgeOwl można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
