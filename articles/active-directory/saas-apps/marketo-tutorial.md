---
title: 'Samouczek: integracja Azure Active Directory z usługą Marketo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługą Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686958"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: integracja Azure Active Directory z usługą Marketo

W tym samouczku dowiesz się, jak zintegrować program Marketo z Azure Active Directory (Azure AD).
Integracja programu Marketo z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do programu Marketo.
* Możesz pozwolić użytkownikom na automatyczne logowanie do usługi Marketo (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu Marketo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym dla programu Marketo

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Market obsługuje zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie programu Marketo z galerii

Aby skonfigurować integrację programu Marketo z usługą Azure AD, musisz dodać program Marketo z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Marketo** w polu wyszukiwania.
1. Wybierz pozycję **Marketo** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Marketo

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu Marketo w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Marketo.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu Marketo, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne w usłudze Marketo](#configure-marketo-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu Marketo](#create-marketo-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w usłudze Marketo, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji programu **Marketo** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://saml.marketo.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistym adresem URL odpowiedzi i stanem przekazywania. Skontaktuj się z [zespołem obsługi klienta programu Marketo](https://investors.marketo.com/contactus.cfm) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie programu Marketo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Marketo.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Marketo**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-marketo-sso"></a>Konfigurowanie logowania jednokrotnego programu Marketo

1. Aby zautomatyzować konfigurację w ramach programu Marketo, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj program Marketo** kieruje do aplikacji Marketo. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu Marketo. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Marketo w innym oknie przeglądarki sieci Web, zaloguj się do witryny firmy Marketa jako administrator.

1. Aby uzyskać Munchkin identyfikator aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Konfigurowanie pojedynczego Sign-On1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracja i kliknij **link Munchkin**.
   
    ![Konfigurowanie pojedynczego Sign-On2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkin wyświetlany na ekranie i Uzupełnij adres URL odpowiedzi w Kreatorze konfiguracji usługi Azure AD.
   
    ![Konfigurowanie pojedynczego Sign-On3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Aby skonfigurować Logowanie jednokrotne w aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Konfigurowanie pojedynczego Sign-On4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracja i kliknij pozycję **Logowanie jednokrotne**.
   
    ![Konfigurowanie pojedynczego Sign-On5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia SAML, kliknij przycisk **Edytuj** .
   
    ![Konfigurowanie pojedynczego Sign-On6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączone** Ustawienia pojedynczej Sign-On.
   
    f. Wklej **Identyfikator usługi Azure AD**, w polu tekstowym **Identyfikator wystawcy** .
   
    przykład W polu tekstowym **Identyfikator jednostki** wprowadź adres URL jako `http://saml.marketo.com/sp` .
   
    h. Wybierz lokalizację identyfikatora użytkownika jako **element identyfikatora nazwy**.
   
    ![Konfigurowanie pojedynczego Sign-On7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli identyfikator użytkownika nie jest wartością UPN, Zmień wartość na karcie atrybut.
   
    i. Przekaż certyfikat pobrany z Kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytuj ustawienia stron przekierowania.
   
    k. Wklej **adres URL logowania** w polu tekstowym **adres URL logowania** .
   
    l. Wklej **adres URL wylogowania** w polu tekstowym **adres URL wylogowywania** .
   
    m. W **adresie URL błędu** skopiuj swój **adres URL wystąpienia programu Marketo** , a następnie kliknij przycisk **Zapisz** , aby zapisać ustawienia.
   
    ![Konfigurowanie pojedynczego Sign-On8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Aby włączyć logowanie jednokrotne dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Konfigurowanie pojedynczego Sign-On9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu **zabezpieczenia** , a następnie kliknij pozycję **Ustawienia logowania**.
   
    ![Konfigurowanie pojedynczego Sign-On10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Zaznacz opcję **Wymagaj logowania jednokrotnego** i **Zapisz** ustawienia.
   
    ![Konfigurowanie pojedynczego Sign-On11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Utwórz użytkownika testowego programu Marketo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie Marketo. wykonaj następujące kroki, aby utworzyć użytkownika na platformie programu Marketo.

1. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.

2. Kliknij przycisk **administratora** w górnym okienku nawigacji.
   
    ![Test Użytkownik1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Przejdź do menu **zabezpieczenia** i kliknij pozycję **Użytkownicy & role**
   
    ![Test](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kliknij link **Zaproś nowego użytkownika** na karcie Użytkownicy
   
    ![Test user3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. W Kreatorze Zaproś nowego użytkownika wypełnij następujące informacje:
   
    a. Wprowadź adres **e-mail** użytkownika w polu tekstowym
   
    ![Test user4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w polu tekstowym
   
    c. Wprowadź **nazwisko**  w polu tekstowym
   
    d. Kliknij przycisk **dalej** .

6. Na karcie **uprawnienia** wybierz pozycję **roli użytkownika** , a następnie kliknij przycisk **dalej** .
   
    ![Test user5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknij przycisk **Wyślij** , aby wysłać zaproszenie użytkownika
   
    ![Test user6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Użytkownik otrzymuje powiadomienie e-mail i kliknie łącze i zmieni hasło w celu aktywowania konta. 

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do programu Marketo, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka usługi Marketo w obszarze Moje aplikacje należy automatycznie zalogować się do programu Marketo, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Marketo można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).