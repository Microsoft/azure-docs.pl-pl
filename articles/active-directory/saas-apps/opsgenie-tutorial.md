---
title: 'Samouczek: integracja Azure Active Directory z usługą OpsGenie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728547"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą OpsGenie

W tym samouczku dowiesz się, jak zintegrować usługę OpsGenie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi OpsGenie z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do OpsGenie.
* Zezwól użytkownikom na automatyczne logowanie się do usługi OpsGenie przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) OpsGenie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa OpsGenie obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii

Aby skonfigurować integrację programu OpsGenie z usługą Azure AD, musisz dodać OpsGenie z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OpsGenie** w polu wyszukiwania.
1. Wybierz pozycję **OpsGenie** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla OpsGenie

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą OpsGenie przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w OpsGenie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą OpsGenie, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-opsgenie-sso)** w usłudze OpsGenie, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego OpsGenie](#create-opsgenie-test-user)** , aby dysponować odpowiednikiem B. Simon w OpsGenie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **OpsGenie** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL odpowiedzi, który został wyjaśniony w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie OpsGenie** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi OpsGenie.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **OpsGenie**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-opsgenie-sso"></a>Konfigurowanie logowania jednokrotnego OpsGenie

1. Aby zautomatyzować konfigurację w programie OpsGenie, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji OpsGenie. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi OpsGenie. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować OpsGenie, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy OpsGenie jako administrator.

2. Kliknij pozycję **Ustawienia**, a następnie kliknij kartę **Logowanie** jednokrotne.
   
    ![OpsGenie pojedyncze Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Aby włączyć logowanie jednokrotne, wybierz pozycję **włączone**.
   
    ![Zrzut ekranu, na którym jest zaznaczone pole wyboru "włączone".](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. W sekcji **dostawca** kliknij kartę **Azure Active Directory** .
   
    ![Zrzut ekranu pokazujący sekcję "dostawca" z wybraną kartą "Azure Active Directory".](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na stronie okna dialogowego Azure Active Directory wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawiający sekcję "Logowanie jednokrotne" z przełącznikiem "Włącz logowanie jednokrotne", "S A M L 2,0 Endpoint" i "Metadata U R L".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Skopiuj wartość **Identyfikator URI identyfikatora aplikacji** i wklej ją do pola **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    a. Skopiuj wartość **adres URL odpowiedzi** i wklej ją do pola tekstowego **adresu URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    a. W polu tekstowym **punkt końcowy protokołu SAML 2,0** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.
    
    b. W polu tekstowym **URL metadanych:** wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z Azure Portal.
    
    c. Aby włączyć logowanie jednokrotne, włącz przełącznik **Włącz logowanie jednokrotne** .

    d. Kliknij przycisk **Zastosuj ustawienia rejestracji jednokrotnej**.

### <a name="create-opsgenie-test-user"></a>Utwórz użytkownika testowego OpsGenie

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w OpsGenie. 

1. W oknie przeglądarki sieci Web Zaloguj się do dzierżawy OpsGenie jako administrator.

2. Przejdź do listy użytkowników, klikając pozycję **Użytkownicy** w lewym panelu.
   
    ![Ustawienia OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Kliknij pozycję **Dodaj użytkownika**.

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Zrzut ekranu pokazujący okno dialogowe "Dodawanie użytkownika" z wyróżnionymi polami tekstowymi "email" i "Full Name" i wybranym przyciskiem "Zapisz".](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. W polu tekstowym **adres e-mail** wpisz adres e-mail B. Simon, który jest adresowany do Azure Active Directory.
   
    b. W polu tekstowym **pełna nazwa** wpisz **B. Simon**.
   
    c. Kliknij pozycję **Zapisz**. 

> [!NOTE]
> B. Simon Pobiera wiadomość e-mail z instrukcjami dotyczącymi konfigurowania profilu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do OpsGenie, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka OpsGenie w obszarze Moje aplikacje należy automatycznie zalogować się do OpsGenie, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

* Po skonfigurowaniu OpsGenie można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).