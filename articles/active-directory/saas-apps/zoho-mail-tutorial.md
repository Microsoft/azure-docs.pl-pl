---
title: 'Samouczek: integracja Azure Active Directory z usługą Zoho | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648377"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Samouczek: integracja Azure Active Directory z usługą Zoho

W tym samouczku dowiesz się, jak zintegrować usługę Zoho z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Zoho z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Zoho.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Zoho przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Zoho, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w Zoho.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Zoho obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="add-zoho-from-the-gallery"></a>Dodaj Zoho z galerii

Aby skonfigurować integrację aplikacji Zoho z usługą Azure AD, musisz dodać aplikację Zoho z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zoho** w polu wyszukiwania.
1. Wybierz pozycję **Zoho** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Zoho

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Zoho przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Zoho.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Zoho, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-zoho-sso)** w usłudze Zoho, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Zoho](#create-zoho-test-user)** , aby dysponować odpowiednikiem B. Simon w Zoho, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Zoho** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoho](https://www.zoho.com/mail/contact.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Zoho** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Zoho.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Zoho**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-zoho-sso"></a>Konfigurowanie logowania jednokrotnego Zoho

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny usługi Zoho Mail jako administrator.

2. Przejdź do obszaru **Control panel** (Panel sterowania).
   
    ![Panel sterowania](./media/zoho-mail-tutorial/control-panel.png "Panel sterowania")

3. Kliknij kartę **SAML Authentication** (Uwierzytelnianie SAML).
   
    ![Uwierzytelnianie SAML](./media/zoho-mail-tutorial/saml-authentication.png "Uwierzytelnianie SAML")

4. W sekcji **SAML Authentication Details** (Szczegóły uwierzytelniania SAML) wykonaj następujące kroki:
   
    ![Szczegóły uwierzytelniania SAML](./media/zoho-mail-tutorial/details.png "Szczegóły uwierzytelniania SAML")
   
    a. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    b. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.
   
    c. W polu tekstowym **Change Password URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.
       
    d. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **PublicKey** (Klucz publiczny).
   
    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.
   
    f. Kliknij przycisk **OK**.

### <a name="create-zoho-test-user"></a>Tworzenie użytkownika testowego aplikacji Zoho

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Zoho Mail, należy ich aprowizować w aplikacji Zoho Mail. W przypadku aplikacji Zoho Mail aprowizację wykonuje się ręcznie.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników poczty Zoho lub interfejsów API dostarczonych przez pocztę Zoho.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny usługi **Zoho Mail** jako administrator.

1. Przejdź do pozycji **Control Panel \> Mail & Docs** (Panel sterowania > Poczta i dokumenty).

1. Przejdź do pozycji **User Details \> Add User** (Szczegóły użytkownika > Dodaj użytkownika).
   
    ![Zrzut ekranu przedstawia witrynę poczty Zoho z informacjami o użytkowniku i wybraną przez użytkownika.](./media/zoho-mail-tutorial/add-user-1.png "Dodaj użytkownika")

1. W oknie dialogowym **Add users** (Dodawanie użytkowników) wykonaj następujące kroki:
   
    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie użytkowników, w którym można wprowadzić podane wartości.](./media/zoho-mail-tutorial/add-user-2.png "Dodaj użytkownika")
   
    a. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak **Britta**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W polu tekstowym **Identyfikator wiadomości e-mail** wpisz identyfikator poczty e-mail użytkownika, np **. \@ brittasimon contoso.com**.

    d. W polu tekstowym **Password** (Hasło) wprowadź hasło użytkownika.
   
    e. Kliknij przycisk **OK**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem w celu potwierdzenia konta, zanim stanie się ono aktywne.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Zoho, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Zoho i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Zoho w obszarze Moje aplikacje należy automatycznie zalogować się do Zoho, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Zoho można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).