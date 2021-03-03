---
title: 'Samouczek: integracja Azure Active Directory z jednym | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650655"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Azure Active Directory integrację z usługą Zoho one

W tym samouczku dowiesz się, jak zintegrować usługę Zoho z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Zoho z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Zoho jednego.
* Zezwól użytkownikom na automatyczne logowanie się, aby Zoho je z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Zoho, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Zoho jedną subskrypcję z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zoho jeden obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-zoho-one-from-the-gallery"></a>Dodaj Zoho jeden z galerii

Aby skonfigurować integrację programu Zoho z usługą Azure AD, musisz dodać Zoho jeden z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zoho jeden** w polu wyszukiwania.
1. Wybierz pozycję **Zoho po jednym** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Zoho

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Zoho jednym przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Zoho.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Zoho, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Zoho jedno Logowanie jednokrotne](#configure-zoho-one-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Zoho jednego użytkownika testowego](#create-zoho-one-test-user)** , aby dysponować odpowiednikiem B. Simon w Zoho, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Zoho** z integracją aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `one.zoho.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Poprzednia wartość **adresu URL odpowiedzi** nie jest prawdziwa. Otrzymasz `<saml-identifier>` wartość z #step4 **konfigurowania Zoho jednej** jednokrotnej rejestracji, która została omówiona w dalszej części tego samouczka.

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **stan przekaźnika** wpisz adres URL: `https://one.zoho.com`

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Poprzednia wartość **adresu URL logowania** nie jest prawdziwa. Wartość zostanie zaktualizowana o rzeczywisty adres URL Sign-On w sekcji **Configure Zoho one Single Sign-on** , która została omówiona w dalszej części tego samouczka. 

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Na stronie **Konfigurowanie Zoho jednej** sekcji skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Zoho.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Zoho jeden**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-zoho-one-sso"></a>Konfigurowanie Zoho jednego logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej Zoho jednej witryny firmowej jako administrator.

2. Na karcie **organizacja** kliknij pozycję **Instalator** w obszarze **uwierzytelnianie SAML**.

    ![Zoho jednej organizacji](./media/zoho-one-tutorial/set-up.png)

3. Na stronie podręcznej wykonaj następujące czynności:

    ![Zoho jeden SIG](./media/zoho-one-tutorial/save.png)

    a. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    b. W polu tekstowym **adres URL wylogowania** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    c. Kliknij przycisk **Przeglądaj** , aby przekazać **certyfikat (base64)** pobrany z Azure Portal.

    d. Kliknij pozycję **Zapisz**.

4. Po zapisaniu konfiguracji uwierzytelniania SAML skopiuj wartość **identyfikatora SAML** i Dołącz ją z **adresem URL odpowiedzi** zamiast `<saml-identifier>` , podobnie jak `https://accounts.zoho.com/samlresponse/one.zoho.com` i wklej WYGENEROWANĄ wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** .

    ![Zoho jeden element SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. Przejdź do karty **domeny** , a następnie kliknij pozycję **Dodaj domenę**.

    ![Zoho jedną domenę](./media/zoho-one-tutorial/add-domain.png)

6. Na stronie **Dodawanie domeny** wykonaj następujące czynności:

    ![Zoho jedną domenę](./media/zoho-one-tutorial/add-domain-name.png)

    a. W polu tekstowym **nazwa domeny** wpisz domena, na przykład contoso.com.

    b. Kliknij pozycję **Dodaj**.

    >[!Note]
    >Po dodaniu domeny wykonaj [następujące](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Po zweryfikowaniu domeny Użyj nazwy domeny w **adresie URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

### <a name="create-zoho-one-test-user"></a>Utwórz Zoho jednego użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu Zoho jednego, muszą one być obsługiwane w Zoho. W Zoho jednym, aprowizacji jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby Zoho jeden jako administrator zabezpieczeń.

2. Na karcie **Użytkownicy** kliknij pozycję **logo użytkownika**.

    ![Zoho jednego użytkownika](./media/zoho-one-tutorial/user.png)

3. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zoho jednego użytkownika](./media/zoho-one-tutorial/add-user.png)
    
    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, np. **Britta Simon**.
    
    b. W polu tekstowym **Email Address** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład brittasimon@contoso.com.

    >[!Note]
    >Wybierz zweryfikowaną domenę z listy domen.

    c. Kliknij pozycję **Dodaj**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Zoho jednego adresu URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do Zoho jednego adresu URL logowania i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Zoho, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu Zoho jednego kafelka w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania i skonfigurowania w trybie dostawcy tożsamości, należy automatycznie zalogować się do Zoho, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Zoho można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).