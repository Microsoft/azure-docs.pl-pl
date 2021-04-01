---
title: 'Samouczek: integracja Azure Active Directory z usługą Egnyte | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 3d102b492326971ef186ba50a557ad8d1df5b6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454202"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Egnyte

W tym samouczku dowiesz się, jak zintegrować usługę Egnyte z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Egnyte z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Egnyte.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Egnyte przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Egnyte.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Egnyte obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Po skonfigurowaniu Egnyte można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie aplikacji Egnyte z galerii

Aby skonfigurować integrację aplikacji Egnyte z usługą Azure AD, musisz dodać aplikację Egnyte z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Egnyte** w polu wyszukiwania.
1. Wybierz pozycję **Egnyte** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji Egnyte.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Egnyte, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-egnyte-sso)** w usłudze Egnyte, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Egnyte](#create-egnyte-test-user)** , aby dysponować odpowiednikiem B. Simon w Egnyte, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Egnyte** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Egnyte](common/sp-signonurl.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.egnyte.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL Sign-On i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Egnyte](https://www.egnyte.com/corp/contact_egnyte.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

5. W sekcji **Konfigurowanie aplikacji Egnyte** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Egnyte.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Egnyte**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-egnyte-sso"></a>Konfigurowanie logowania jednokrotnego Egnyte

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Egnyte jako administrator.

2. Kliknij polecenie **Ustawienia**.
   
    ![Ustawienia 1](./media/egnyte-tutorial/ic787819.png "Ustawienia")

3. W menu kliknij pozycję **Settings (Ustawienia)**.

    ![Ustawienia](./media/egnyte-tutorial/ic787820.png "Ustawienia")

4. Kliknij kartę **Configuration (Konfiguracja)**, a następnie kliknij pozycję **Security (Zabezpieczenia)**.

    ![Bezpieczeństwo](./media/egnyte-tutorial/ic787821.png "Zabezpieczenia")

5. W sekcji **Single Sign-On Authentication (Uwierzytelnianie logowania jednokrotnego)** wykonaj następujące kroki:

    ![Uwierzytelnianie logowania jednokrotnego](./media/egnyte-tutorial/ic787822.png "Uwierzytelnianie logowania jednokrotnego")   
    
    a. Dla pozycji **Single sign-on authentication (Uwierzytelnianie logowania jednokrotnego)** wybierz pozycję **SAML 2.0**.
   
    b. Dla pozycji **Identity provider (Dostawca tożsamości)** wybierz pozycję **AzureAD**.
   
    c. Wklej **adres URL logowania** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider login URL (Adres URL logowania dostawcy tożsamości)**.
   
    d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider entity ID (Identyfikator jednostki dostawcy tożsamości)**.
      
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Identity provider certificate (Certyfikat dostawcy tożsamości)**.
   
    f. Dla pozycji **Default user mapping (Domyślne mapowanie użytkownika)** wybierz pozycję **Email address (Adres e-mail)**.
   
    przykład Dla pozycji **Use domain-specific issuer value (Użyj wartości wystawcy specyficznej dla domeny)** wybierz pozycję **disabled (wyłączone)**.
   
    h. Kliknij pozycję **Zapisz**.

### <a name="create-egnyte-test-user"></a>Tworzenie użytkownika testowego aplikacji Egnyte

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Egnyte, muszą one być obsługiwane w usłudze Egnyte. W przypadku aplikacji Egnyte aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy **Egnyte** jako administrator.

2. Przejdź do pozycji **Settings (Ustawienia) \> Users & Groups (Użytkownicy i grupy)**.

3. Kliknij pozycję **Add New User (Dodaj nowego użytkownika)**, a następnie wybierz typ użytkownika, którego chcesz dodać.
   
    ![Użytkownicy](./media/egnyte-tutorial/ic787824.png "Użytkownicy")

4. W sekcji **New Power User (Nowy użytkownik zaawansowany)** wykonaj następujące kroki:
    
    ![Nowy użytkownik standardowy](./media/egnyte-tutorial/ic787825.png "Nowy użytkownik standardowy")   

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **Brittasimon \@ contoso.com**.

    b. W polu tekstowym **Username (Nazwa użytkownika)** wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    c. Wybierz pozycję **Single Sign-On (Logowanie jednokrotne)** dla pozycji **Authentication Type (Typ uwierzytelniania)**.
   
    d. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Egnyte i interfejsów API udostępnionych przez usługę Egnyte.
>

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Egnyte na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Egnyte, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)