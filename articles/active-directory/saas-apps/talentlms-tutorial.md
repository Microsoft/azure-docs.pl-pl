---
title: 'Samouczek: integracja Azure Active Directory z usługą TalentLMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 1b65191e97402f2fb352c56664dad085c448242c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650631"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Samouczek: integracja Azure Active Directory z usługą TalentLMS

W tym samouczku dowiesz się, jak zintegrować usługę TalentLMS z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi TalentLMS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TalentLMS.
* Zezwól użytkownikom na automatyczne logowanie się do usługi TalentLMS przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TalentLMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w TalentLMS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TalentLMS obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-talentlms-from-the-gallery"></a>Dodaj TalentLMS z galerii

Aby skonfigurować integrację programu TalentLMS z usługą Azure AD, musisz dodać TalentLMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TalentLMS** w polu wyszukiwania.
1. Wybierz pozycję **TalentLMS** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla TalentLMS

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą TalentLMS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TalentLMS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TalentLMS, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-talentlms-sso)** w usłudze TalentLMS, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego TalentLMS](#create-talentlms-test-user)** , aby dysponować odpowiednikiem B. Simon w TalentLMS, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **TalentLMS** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![TalentLMS domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.TalentLMSapp.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta TalentLMS](https://www.talentlms.com/contact) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie TalentLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TalentLMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **TalentLMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-talentlms-sso"></a>Konfigurowanie logowania jednokrotnego TalentLMS

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny TalentLMS jako administrator.

1. W sekcji **ustawienia & konta** kliknij kartę **Użytkownicy** .

    ![Ustawienia & konta](./media/talentlms-tutorial/IC777296.png "Ustawienia & konta")

1. Kliknij pozycję **Single Sign-On (SSO)**,

1. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/talentlms-tutorial/saml.png "Logowanie jednokrotne")

    a. Z listy **Typ integracji z logowaniem jednokrotnym** wybierz pozycję **SAML 2,0**.

    b. W polu tekstowym **dostawca tożsamości (dostawcy tożsamości)** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    c. Wklej wartość **odcisku palca** z Azure Portal do pola tekstowego **odcisku palca certyfikatu** .

    d.  W polu tekstowym **adres URL logowania zdalnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL wylogowania zdalnego** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    f. Wypełnij następujące informacje:

    * W polu tekstowym **TargetedID** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * W polu tekstowym **imię i nazwisko** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * W polu **tekstowym nazwisko** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * W polu tekstowym **adres e-mail** wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Kliknij pozycję **Zapisz**.

### <a name="create-talentlms-test-user"></a>Utwórz użytkownika testowego TalentLMS

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze TalentLMS, muszą one być obsługiwane w usłudze TalentLMS. W przypadku TalentLMS, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **TalentLMS** .

1. Kliknij pozycję **Użytkownicy**, a następnie kliknij pozycję **Dodaj użytkownika**.

1. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/talentlms-tutorial/IC777299.png "Dodaj użytkownika")  

    a. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    c. W polu tekstowym **Email address** (Adres e-mail) wprowadź adres e-mail użytkownika, np. `brittasimon\@contoso.com`.

    d. Kliknij pozycję **Dodaj użytkownika**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników TalentLMS i interfejsów API udostępnionych przez usługę TalentLMS.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania TalentLMS, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania TalentLMS i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka TalentLMS w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania TalentLMS. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu TalentLMS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).