---
title: 'Samouczek: integracja Azure Active Directory z usługą usługi PagerDuty | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługi PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 57c6057e2515a497faa5731a20ce84e6fc9d1185
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą usługi PagerDuty

W tym samouczku dowiesz się, jak zintegrować usługę usługi PagerDuty z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi usługi PagerDuty z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi PagerDuty.
* Zezwól użytkownikom na automatyczne logowanie się do usługi usługi PagerDuty przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi PagerDuty.

> [!NOTE]
> Jeśli używasz uwierzytelniania MFA lub bez hasła w usłudze Azure AD, wyłącz wartość AuthnContext w żądaniu SAML. W przeciwnym razie usługa Azure AD zgłosi błąd w przypadku niezgodności AuthnContext i nie wyśle tokenu z powrotem do aplikacji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi PagerDuty obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-pagerduty-from-the-gallery"></a>Dodaj usługi PagerDuty z galerii

Aby skonfigurować integrację programu usługi PagerDuty z usługą Azure AD, musisz dodać usługi PagerDuty z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **usługi PagerDuty** w polu wyszukiwania.
1. Wybierz pozycję **usługi PagerDuty** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi PagerDuty

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi PagerDuty przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi PagerDuty.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi PagerDuty, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-pagerduty-sso)** w usłudze usługi PagerDuty, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi PagerDuty](#create-pagerduty-test-user)** , aby dysponować odpowiednikiem B. Simon w usługi PagerDuty, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **usługi PagerDuty** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.pagerduty.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.pagerduty.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta usługi PagerDuty](https://www.pagerduty.com/support/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi PagerDuty** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi usługi PagerDuty.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **usługi PagerDuty**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-pagerduty-sso"></a>Konfigurowanie logowania jednokrotnego usługi PagerDuty

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny usługi PagerDuty jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.

    ![Ustawienia konta](./media/pagerduty-tutorial/ic778535.png "Ustawienia konta")

3. Kliknij pozycję **Logowanie jednokrotne**.

    ![Rejestracja jednokrotna](./media/pagerduty-tutorial/ic778536.png "Logowanie jednokrotne")

4. Na stronie **Włącz** Logowanie jednokrotne wykonaj następujące czynności:

    ![Włącz logowanie jednokrotne](./media/pagerduty-tutorial/ic778537.png "Włącz logowanie jednokrotne")

    a. Otwórz certyfikat z kodowaniem Base-64 pobrany z Azure Portal w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu X. 509**
  
    b. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
  
    c. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Wybierz pozycję **Zezwalaj na logowanie do nazwy użytkownika/hasła**.

    e. Zaznacz pole wyboru **Wymagaj dokładnego porównania kontekstu uwierzytelniania** .

    f. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-pagerduty-test-user"></a>Utwórz użytkownika testowego usługi PagerDuty

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze usługi PagerDuty, muszą one być obsługiwane w usługi PagerDuty. W przypadku usługi PagerDuty, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

> [!NOTE]
> Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program usługi PagerDuty.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **usługi PagerDuty** .

2. W menu u góry kliknij pozycję **Użytkownicy**.

3. Kliknij pozycję **Dodaj użytkowników**.
   
    ![Dodaj użytkowników](./media/pagerduty-tutorial/ic778539.png "Dodawanie użytkowników")

4.  W oknie dialogowym **zapraszanie zespołu** wykonaj następujące czynności:
   
    ![Zapraszanie zespołu](./media/pagerduty-tutorial/ic778540.png "Zapraszanie zespołu")

    a. Wpisz **imię i nazwisko** użytkownika, np. **B. Simon**. 
   
    b. Wprowadź adres **e-mail** użytkownika, na przykład **b. Simon \@ contoso.com**.
   
    c. Kliknij przycisk **Dodaj**, a następnie kliknij przycisk **Wyślij zaproszenia**.
   
    > [!NOTE]
    > Wszyscy dodani użytkownicy otrzymają zaproszenie do utworzenia konta usługi PagerDuty.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania usługi PagerDuty, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania usługi PagerDuty i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka usługi PagerDuty w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania usługi PagerDuty. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi PagerDuty można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
