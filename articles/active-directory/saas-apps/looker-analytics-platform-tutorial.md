---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu platformy do analizy wyszukiwania | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directoryą i szukaną platformą Analytics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180748"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu platformy do analizy wyszukiwania

W tym samouczku dowiesz się, jak zintegrować platformę analityczną wyszukiwania z Azure Active Directory (Azure AD). Gdy integrujesz platformę analizy wyszukiwania z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do platformy analitycznej szukania.
* Umożliwienie użytkownikom automatycznego logowania się do usługi wyszukiwania na platformie analiz przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Zapoznaj się z subskrypcją logowania jednokrotnego (SSO) na platformie analizy.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma szukana Analytics obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Platforma szukająca analiz obsługuje Inicjowanie obsługi użytkowników **just in Time**


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Dodawanie platformy szukającej analiz z galerii

Aby skonfigurować integrację narzędzia do analizy wyszukiwania z usługą Azure AD, musisz dodać do listy aplikacji zarządzanych SaaS platformę analizy wyszukiwania z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Znajdź platformę analizy** .
1. Wybierz pozycję **Szukaj programu Wyszukaj platformę** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu szukającego analiz

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu platformy szukającej analizy za pomocą użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na platformie wyszukiwania.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu platformy wyszukiwania, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj szukanie platformy analiza Logowanie jednokrotne](#configure-looker-analytics-platform-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego platformy do analizy](#create-looker-analytics-platform-test-user)** , aby dysponować odpowiednikiem B. Simon na platformie wyszukiwania, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na Azure Portal na stronie integracji aplikacji do **analizy platformy** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<SPN>_looker`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej dla platformy analizy klienta](mailto:support@looker.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. Na stronie **Konfigurowanie platformy do analizy Znajdź** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do platformy analizy wyszukiwania.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Szukaj platformy Analytics**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-looker-analytics-platform-sso"></a>Konfigurowanie wyszukiwania Logowanie jednokrotne na platformie analiz

1. W innym oknie przeglądarki sieci Web Zaloguj się jako administrator, aby znaleźć witrynę internetową platformy analizy.

1. Przejdź do uwierzytelniania przez **administratora**  ->    ->  **SAML**

    ![zrzut ekranu dla opcji SAML](./media/looker-analytics-platform-tutorial/admin.png)

1. Wklej informacje o **metadanych Federacji** skopiowane z Azure Portal do pola tekstowego **metadanych dostawcy tożsamości** i kliknij pozycję **Załaduj**.

    ![zrzut ekranu przedstawiający przekazywanie metadanych](./media/looker-analytics-platform-tutorial/metadata.png)

1. Wykonaj następujące kroki w sekcji **ustawienia atrybutu użytkownika** .

    ![zrzut ekranu przedstawiający ustawienia atrybutu użytkownika](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Dodaj następującą wartość w polu atrybut wiadomości E-mail: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Dodaj następującą wartość do pola sqlattr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Dodaj następującą wartość do pola lname ATTR: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. W obszarze **uwierzytelnianie użytkownika testowego** kliknij pozycję **Testuj uwierzytelnianie SAML**. Jeśli na stronie ładującej powiedzie się "pomyślnie zweryfikowano odpowiedź serwera", pomyślnie skonfigurowano wystąpienie dla integracji SAML.
    
    e. W obszarze **Zapisz i Zastosuj ustawienia** Sprawdź, czy pole **potwierdziło powyższą konfigurację i włącz je globalnie**.

    f. Kliknij przycisk **Ustawienia aktualizacji** .

### <a name="create-looker-analytics-platform-test-user"></a>Tworzenie użytkownika testowego platformy do analiz szukanych

W tej sekcji użytkownik o nazwie Britta Simon został utworzony na platformie wyszukiwania. Funkcja Wyszukaj w usłudze wyszukiwania obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje na platformie wyszukiwania, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do szukanego adresu URL logowania platformy Analytics, w którym można zainicjować przepływ logowania.  

* Przejdź kolejno do pozycji Wyszukaj Znajdź platformę adres URL logowania bezpośrednio i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do platformy wyszukiwania, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Wyszukaj platformę w panelu dostępu, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania i jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do platformy wyszukiwania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu platformy do analiz szukanych można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).