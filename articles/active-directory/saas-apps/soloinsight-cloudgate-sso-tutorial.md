---
title: 'Samouczek: integracja Azure Active Directory z Soloinsight-CloudGate Logowanie jednokrotne | Microsoft Docs'
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545067"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Samouczek: integracja Soloinsight-CloudGate rejestracji jednokrotnej z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Soloinsight-CloudGate Logowanie jednokrotne z usługą Azure Active Directory (Azure AD). Podczas integrowania Soloinsight-CloudGate Logowanie jednokrotne z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Soloinsight-CloudGate rejestracji jednokrotnej.
* Zezwól użytkownikom na automatyczne logowanie się, aby Soloinsight-CloudGate Logowanie jednokrotne przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Soloinsight-CloudGate.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Soloinsight-CloudGate Logowanie jednokrotne obsługuje zainicjowane przez usługę **SP** SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie aplikacji Soloinsight-CloudGate SSO z galerii

Aby skonfigurować integrację aplikacji Soloinsight-CloudGate SSO z usługą Azure AD, należy dodać aplikację Soloinsight-CloudGate SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SOLOINSIGHT-CloudGate SSO** w polu wyszukiwania.
1. Wybierz pozycję **Soloinsight-CLOUDGATE SSO** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD Soloinsight-CloudGate za pomocą logowania jednokrotnego przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Soloinsight-CloudGate rejestracji jednokrotnej.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze Soloinsight-CloudGate, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Soloinsight-CloudGate Logowanie jednokrotne](#configure-soloinsight-cloudgate-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Soloinsight-CloudGate użytkownika testowego logowania jednokrotnego](#create-soloinsight-cloudgate-sso-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w Soloinsight-CloudGate rejestracji jednokrotnej, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SSO Soloinsight-CloudGate** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/login`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO** samouczka.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Soloinsight-CloudGate rejestracji jednokrotnej** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Skonfiguruj Soloinsight-CloudGate Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach Soloinsight-CloudGate rejestracji jednokrotnej, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **instalator Soloinsight-CloudGate Logowanie jednokrotne** spowoduje przekierowanie do aplikacji Soloinsight-CloudGate Logowanie jednokrotne. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do Soloinsight-CloudGate rejestracji jednokrotnej. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Soloinsight-CloudGate Logowanie jednokrotne, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmowej Soloinsight-CloudGate rejestracji jednokrotnej jako administrator i wykonaj następujące czynności:

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

    * Skopiowany **adres URL logowania** z Azure Portal **skonfigurowany Soloinsight-CloudGate konfiguracja logowania jednokrotnego** jest wprowadzany w sekcji Ustawienia rejestracji jednokrotnej w portalu sieci Web CloudGate.

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze Britta Simon, przyznając dostęp do Soloinsight-CloudGate logowania jednokrotnego.

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

Po Soloinsight-CloudGate wybraniu kafelka rejestracji jednokrotnej w panelu dostępu należy automatycznie zalogować się do Soloinsight-CloudGate logowania jednokrotnego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)