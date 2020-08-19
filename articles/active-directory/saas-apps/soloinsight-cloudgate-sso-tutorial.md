---
title: 'Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Soloinsight — CloudGate | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Soloinsight-CloudGate SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545067"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Samouczek: Integrowanie logowania jednokrotnego Soloinsight-CloudGate z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w usłudze Soloinsight-CloudGate z usługą Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne w usłudze Soloinsight-CloudGate z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego w usłudze Soloinsight-CloudGate.
* Zezwól użytkownikom na automatyczne logowanie do usługi logowania jednokrotnego (Soloinsight-CloudGate) przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Soloinsight — CloudGate.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Soloinsight-CloudGate Logowanie jednokrotne obsługuje usługę **SP** zainicjowaną przez usługę SSO

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie aplikacji Soloinsight-CloudGate SSO z galerii

Aby skonfigurować integrację aplikacji Soloinsight-CloudGate SSO z usługą Azure AD, należy dodać aplikację Soloinsight-CloudGate SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SOLOINSIGHT-CloudGate SSO** w polu wyszukiwania.
1. Wybierz pozycję **Soloinsight-CLOUDGATE SSO** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego Soloinsight-CloudGate przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Soloinsight-CloudGate SSO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Soloinsight-CloudGate, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne w usłudze Soloinsight-CloudGate](#configure-soloinsight-cloudgate-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego logowania jednokrotnego (Soloinsight-CloudGate](#create-soloinsight-cloudgate-sso-test-user)** ), aby uzyskać odpowiednik Britta Simon w Soloinsight-CloudGate SSO, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SSO Soloinsight-CloudGate** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/login`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO** samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Soloinsight-CLOUDGATE SSO** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurowanie rejestracji jednokrotnej Soloinsight-CloudGate

1. Aby zautomatyzować konfigurację w ramach rejestracji jednokrotnej Soloinsight-CloudGate, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Setup Soloinsight-CloudGate Logowanie jednokrotne** spowoduje przekierowanie do aplikacji SSO Soloinsight-CloudGate. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi logowania jednokrotnego w usłudze Soloinsight-CloudGate. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Soloinsight-CloudGate Logowanie jednokrotne, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Soloinsight-CloudGate SSO jako administrator i wykonaj następujące czynności:

4. Aby uzyskać wartości, które mają zostać wklejone w Azure Portal podczas konfigurowania podstawowego protokołu SAML, zaloguj się do portalu sieci Web CloudGate przy użyciu swoich poświadczeń, a następnie uzyskaj dostęp do ustawień logowania jednokrotnego, które znajdują się w poniższej ścieżce **głównej>administracja>ustawienia systemowe>ogólne**.

    ![Ustawienia aplikacji CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **Adres URL użytkownika języka SAML**

    * Skopiuj linki dostępne dla **adresu URL odbiorcy protokołu SAML** i pola **Przekieruj adresy URL** i wklej je w sekcji **Azure Portal Podstawowa konfiguracja SAML** w polu **Identyfikator (identyfikator jednostki)** i pola **adresu URL odpowiedzi** .

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certyfikat podpisywania SAML**

    * Przejdź do źródła pliku certyfikatu (base64), który został pobrany z Azure Portal listy certyfikatów podpisywania SAML i kliknij go prawym przyciskiem myszy. Wybierz opcję **Edit with Notepad++** (Edytuj za pomocą Notatnika++) z listy. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Skopiuj zawartość pliku Notatnika++ certyfikatu (Base64).

        ![Kopiowanie certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Wklej zawartość w obszarze ustawień logowania jednokrotnego portalu internetowego CloudGate w polu **Certificate** (Certyfikat), a następnie kliknij przycisk zapisywania.

        ![Portal certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupa domyślna**

    * Wybierz pozycję **Business Admin** (Administrator firmy) z listy rozwijanej **Default Group** (Grupa domyślna) w portalu internetowym CloudGate

        ![Grupa domyślna](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Adres URL identyfikatora usługi AD i adres URL logowania**

    * Skopiowanego **adresu URL logowania** z Azure Portal **konfigurowania konfiguracji logowania jednokrotnego w usłudze Soloinsight-CloudGate** należy wprowadzić w sekcji ustawień logowania jednokrotnego w portalu sieci Web CloudGate.

    * Wklej link **adresu URL logowania** z Azure Portal w polu **adres URL logowania usługi AD** w portalu sieci Web CloudGate.

    * Wklej łącze **identyfikatora usługi Azure AD** z Azure Portal w polu **Identyfikator usługi AD** portalu sieci Web CloudGate

        ![Logowanie w usłudze AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze Britta Simon, przyznając dostęp do usługi Soloinsight-CloudGate SSO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Tworzenie użytkownika testowego aplikacji Soloinsight-CloudGate SSO

Aby utworzyć użytkownika testowego, wybierz pozycję **Employees** z menu głównego w internetowym portalu aplikacji CloudGate i wypełnij formularz Add new employee (Dodawanie nowego pracownika). Poziom urzędu do przypisania do użytkownika testowego to **Business Admin** (Administrator firmy). Kliknij raz pozycję **Create** (Utwórz) po wypełnieniu wszystkich wymaganych pól.

![Testowanie pracownika](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka rejestracji jednokrotnej Soloinsight-CloudGate w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego Soloinsight-CloudGate, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)