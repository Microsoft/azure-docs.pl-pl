---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu produktu Workday | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527177"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu produktu Workday

W tym samouczku dowiesz się, jak zintegrować usługę Workday z usługą Azure Active Directory (Azure AD). W przypadku integrowania produktu Workday z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do produktu Workday.
* Zezwól użytkownikom na automatyczne logowanie do produktu Workday przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w ramach usługi Workday.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Workday **obsługuje usługę** zainicjowaną przez usługę SSO.

* Po skonfigurowaniu produktu Workday można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Dodawanie produktu Workday z galerii

Aby skonfigurować integrację programu Workday z usługą Azure AD, musisz dodać dzień roboczy z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Workday** w polu wyszukiwania.
1. Wybierz pozycję **Workday** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla produktu Workday

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą produktu Workday przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Workday.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą produktu Workday, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj aplikację Workday](#configure-workday)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego produktu Workday](#create-workday-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Workday, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Workday** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `http://www.workday.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania i adresu URL odpowiedzi. Adres URL odpowiedzi musi mieć poddomenę na przykład: www, WD2, WD3, WD3-Impl, WD5, WD5-Impl).
    > Użycie podobnej do `http://www.myworkday.com` programu Works, ale nie `http://myworkday.com` . Skontaktuj się z [zespołem obsługi klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Workday oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Workday oczekuje, że **NameIdentifier** ma być mapowany z **użytkownikiem. mail**, **UPN**itp., dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

    > [!NOTE]
    > W tym miejscu zamapowano identyfikator nazwy z nazwą UPN (User. userPrincipalName) jako domyślną. Aby pomyślnie pracować z logowaniem jednokrotnym, należy zmapować identyfikator nazwy z rzeczywistym IDENTYFIKATORem użytkownika na koncie w usłudze Workday (adres e-mail, nazwę UPN itp.).

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować opcje **podpisywania** zgodnie z wymaganiami, kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **certyfikatu podpisywania SAML** .

    ![image (obraz)](common/edit-certificate.png) 

    ![image (obraz)](./media/workday-tutorial/signing-option.png)

    a. Wybierz opcję **Podpisz odpowiedź SAML i potwierdzenie** dla **podpisywania**.

    b. Kliknij pozycję **Zapisz**

1. W sekcji **Konfigurowanie produktu Workday** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do produktu Workday.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Workday**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-workday"></a>Konfigurowanie produktu Workday

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej jako administrator.

2. W **polu wyszukiwania** Wyszukaj nazwę **Edycja dzierżawy — zabezpieczenia** w lewym górnym rogu strony głównej.

    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")

3. W sekcji **adresy URL przekierowania** wykonaj następujące czynności:

    ![Adresy URL przekierowania](./media/workday-tutorial/IC7829581.png "Adresy URL przekierowania")

    a. Kliknij przycisk **Dodaj wiersz**.

    b. W polu tekstowym **adres URL przekierowania logowania**, **adres URL przekierowania limitu czasu** i **adres URL przekierowania urządzenia przenośnego** , wklej **adres URL logowania** skopiowany z sekcji **Konfigurowanie produktu Workday** Azure Portal.

    c. W polu tekstowym **adres URL przekierowania Wyloguj** wklej **adres URL wylogowania** , który został skopiowany z sekcji **Konfigurowanie produktu Workday** Azure Portal.

    d. W polu tekstowym **używane dla środowisk** wybierz nazwę środowiska.  

   > [!NOTE]
   > Wartość atrybutu Environment jest powiązana z wartością adresu URL dzierżawcy:  
   > — Jeśli nazwa domeny adresu URL dzierżawy produktu Workday zaczyna się od Impl na przykład: * https://www.myworkday.com/ "dzierżawca"/login-saml2.htmLD*), atrybut **Environment** musi być ustawiony na implementację.  
   > — Jeśli nazwa domeny zaczyna się od czegoś innego, należy skontaktować się z [zespołem obsługi klienta](https://www.workday.com/en-us/partners-services/services/support.html) w programie Workday w celu uzyskania pasującej wartości **środowiska** .

4. W sekcji **Instalator języka SAML** wykonaj następujące czynności:

    ![Konfiguracja protokołu SAML](./media/workday-tutorial/IC782926.png "Konfiguracja protokołu SAML")

    a.  Wybierz pozycję **Włącz uwierzytelnianie SAML**.

    b.  Kliknij przycisk **Dodaj wiersz**.

5. W sekcji **dostawcy tożsamości SAML** wykonaj następujące czynności:

    ![Dostawcy tożsamości języka SAML](./media/workday-tutorial/IC7829271.png "Dostawcy tożsamości języka SAML")

    a. W polu tekstowym **Nazwa dostawcy tożsamości** wpisz nazwę dostawcy (na przykład: *SPInitiatedSSO*).

    b. W Azure Portal w sekcji **Konfigurowanie produktu Workday** skopiuj wartość **Identyfikator usługi Azure AD** , a następnie wklej ją do pola tekstowego **wystawcy** .

    ![Dostawcy tożsamości języka SAML](./media/workday-tutorial/IC7829272.png "Dostawcy tożsamości języka SAML")

    c. W Azure Portal w sekcji **Konfigurowanie produktu Workday** skopiuj wartość **adres URL wylogowania** , a następnie wklej ją do pola tekstowego **adres URL odpowiedzi na wylogowanie** .

    d. W Azure Portal w sekcji **Konfigurowanie produktu Workday** skopiuj wartość **adres URL logowania** , a następnie wklej ją do pola tekstowego **adres URL usługi SSO dostawcy tożsamości** .

    e. W polu tekstowym **używane dla środowisk** wybierz nazwę środowiska.

    f. Kliknij pozycję **certyfikat klucza publicznego dostawcy tożsamości**, a następnie kliknij przycisk **Utwórz**.

    ![Utwórz](./media/workday-tutorial/IC782928.png "Utwórz")

    przykład Kliknij pozycję **Utwórz klucz publiczny x509**.

    ![Utwórz](./media/workday-tutorial/IC782929.png "Utwórz")

6. W sekcji **Wyświetl klucz publiczny x509** wykonaj następujące czynności:

    ![Wyświetl klucz publiczny x509](./media/workday-tutorial/IC782930.png "Wyświetl klucz publiczny x509")

    a. W polu tekstowym **Nazwa** wpisz nazwę certyfikatu (na przykład: *ŚOI \_ SP*).

    b. W polu tekstowym **prawidłowy** typ wpisz prawidłową wartość z atrybutu certyfikatu.

    c.  W polu tekstowym **prawidłowy dla** wpisz prawidłową wartość atrybutu dla certyfikatu.

    > [!NOTE]
    > Możesz uzyskać aktualną datę od i datę ważności z pobranego certyfikatu, klikając go dwukrotnie.  Daty są wyświetlane na karcie **szczegóły** .
    >
    >

    d.  Otwórz zaszyfrowany certyfikat Base-64 w Notatniku, a następnie skopiuj jego zawartość.

    e.  W polu tekstowym **certyfikat** wklej zawartość schowka.

    f.  Kliknij przycisk **OK**.

7. Wykonaj następujące czynności:

    ![Konfiguracja logowania jednokrotnego](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Konfiguracja logowania jednokrotnego")

    a.  W polu tekstowym **Identyfikator dostawcy usług** wpisz **http://www.workday.com** .

    b. Wybierz pozycję **nie rób korekt w zainicjowanym programie Sp żądanie uwierzytelniania**.

    c. Jako **metodę podpisu żądania uwierzytelniania**wybierz pozycję **SHA256**.

    ![Metoda podpisu żądania uwierzytelniania](./media/workday-tutorial/WorkdaySSOConfiguration.png "Metoda podpisu żądania uwierzytelniania")

    d. Kliknij przycisk **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Upewnij się, że poprawnie skonfigurowano Logowanie jednokrotne. Jeśli włączysz logowanie jednokrotne przy użyciu nieprawidłowej konfiguracji, możesz nie być w stanie wprowadzić aplikacji z poświadczeniami i zablokować. W tej sytuacji dzień Workday zawiera adres URL logowania do kopii zapasowej, w którym użytkownicy mogą logować się przy użyciu swojej zwykłej nazwy użytkownika i hasła w następującym formacie: [adres URL w dniach roboczych]/login.Flex? redirect = n

### <a name="create-workday-test-user"></a>Utwórz użytkownika testowego produktu Workday

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze Workday. Współpraca z [zespołem obsługi klienta](https://www.workday.com/en-us/partners-services/services/support.html) w programie Workday umożliwia dodanie użytkowników z platformy Workday. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Workday w panelu dostępu należy automatycznie zalogować się do dnia roboczego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Workday z usługą Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić dzień roboczy przy użyciu zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/protect-workday)