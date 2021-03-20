---
title: 'Samouczek: integracja Azure Active Directory z usługą Work.com | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Work.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636157"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Samouczek: integracja Azure Active Directory z usługą Work.com

W tym samouczku dowiesz się, jak zintegrować usługę Work.com z usługą Azure Active Directory (Azure AD).
Integracja Work.com z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Work.com.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Work.com (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Work.com, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Work.com

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Work.com obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-workcom-from-the-gallery"></a>Dodawanie Work.com z galerii

Aby skonfigurować integrację programu Work.com z usługą Azure AD, musisz dodać Work.com z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Work.com z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Work.com**, wybierz pozycję **Work.com** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Work.com na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Work.com na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Work.com.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Work.com, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-workcom-single-sign-on)** jednokrotne w usłudze Work.com, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Work.com](#create-workcom-test-user)** , aby uzyskać odpowiednik Britta Simon w Work.com, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

>[!NOTE]
>W celu skonfigurowania logowania jednokrotnego należy jeszcze skonfigurować niestandardową nazwę domeny Work.com. Należy zdefiniować co najmniej nazwę domeny, przetestować nazwę domeny i wdrożyć ją w całej organizacji.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Work.com, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Work.com** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Work.com domenę i adresy URL Logowanie jednokrotne](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **konfigurowanie Work.com** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-workcom-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On Work.com

1. Zaloguj się do swojej dzierżawy Work.com jako administrator.

2. Przejdź do pozycji **Konfiguracja**.
   
    ![Zrzut ekranu przedstawia konfigurację wybraną z menu użytkownika.](./media/work-com-tutorial/ic794108.png "Konfiguracja")

3. W okienku nawigacji po lewej stronie w sekcji **Administruj** kliknij pozycję **Zarządzanie domeną** , aby rozwinąć sekcję pokrewne, a następnie kliknij pozycję **moja domena** , aby otworzyć stronę **Moje domeny** . 
   
    ![Zrzut ekranu przedstawia moją domenę Zarządzanie domeną w okienku Administruj.](./media/work-com-tutorial/ic767825.png "Moja domena")

4. Aby sprawdzić, czy domena została prawidłowo skonfigurowana, upewnij się, że znajduje się w sekcji "**krok 4 wdrożony dla użytkowników**" i przejrzyj "**Moje ustawienia domeny**".
   
    ![Domena wdrożona dla użytkownika](./media/work-com-tutorial/ic784377.png "Domena wdrożona dla użytkownika")

5. Zaloguj się do dzierżawy Work.com.

6. Przejdź do pozycji **Konfiguracja**.
    
    ![Zrzut ekranu przedstawia konfigurację wybraną z menu użytkownika.](./media/work-com-tutorial/ic794108.png "Konfiguracja")

7. Rozwiń menu **Security Controls** , a następnie kliknij pozycję **Single Sign-On Settings (ustawienia**).
    
    ![Ustawienia pojedynczego Sign-On](./media/work-com-tutorial/ic794113.png "Ustawienia pojedynczego Sign-On")

8. Na stronie okna dialogowego **Ustawienia pojedynczej Sign-On** wykonaj następujące czynności:
    
    ![Włączono protokół SAML](./media/work-com-tutorial/ic781026.png "Włączono protokół SAML")
    
    a. Wybierz pozycję **SAML Enabled** (SAML włączone).
    
    b. Kliknij pozycję **Nowy**.

9. W sekcji **Ustawienia protokołu SAML Single Sign-On** wykonaj następujące czynności:
    
    ![Ustawienie protokołu SAML Single Sign-On](./media/work-com-tutorial/ic794114.png "Ustawienie protokołu SAML Single Sign-On")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.  
       
    > [!NOTE]
    > Podanie wartości dla **nazwy** powoduje automatyczne wypełnienie pola tekstowego **nazwy interfejsu API** .
    
    b. W polu tekstowym **wystawca** wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal.
    
    c. Aby przekazać pobrany certyfikat z Azure Portal, kliknij przycisk **Przeglądaj**.
    
    d. W polu tekstowym **Identyfikator jednostki** wpisz `https://salesforce-work.com` .
    
    e. Jako **Typ tożsamości SAML**, wybierz pozycję **potwierdzenie zawiera identyfikator Federacji z obiektu użytkownika**.
    
    f. Jako **lokalizację tożsamości SAML**, **w elemencie NameIdentfier instrukcji subject** wybierz pozycję Identity (tożsamość).
    
    przykład W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    h. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowywania** skopiowanego z Azure Portal.
    
    i. Jako **powiązanie żądania zainicjowane przez dostawcę usług** wybierz pozycję **http post**.
    
    j. Kliknij pozycję **Zapisz**.

10. W klasycznym portalu Work.com w okienku nawigacji po lewej stronie kliknij pozycję **Zarządzanie domeną** , aby rozwinąć sekcję pokrewne, a następnie kliknij pozycję **moja domena** , aby otworzyć stronę **Moje domeny** . 
    
    ![Zrzut ekranu przedstawia moją domenę wybraną z zarządzania domeną.](./media/work-com-tutorial/ic794115.png "Moja domena")

11. Na stronie **moja domena** w sekcji **znakowanie strony logowania** kliknij przycisk **Edytuj**.
    
    ![Zrzut ekranu przedstawia sekcję znakowania strony logowania, w której można wybrać opcję Edytuj.](./media/work-com-tutorial/ic767826.png "Znakowanie strony logowania")

12. Na stronie **znakowanie strony logowania** w sekcji **Usługa uwierzytelniania** zostanie wyświetlona nazwa **ustawień logowania jednokrotnego protokołu SAML** . Wybierz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Zrzut ekranu przedstawia znakowanie strony logowania, w którym można wybrać nazwę ustawienia, czyli P P E.](./media/work-com-tutorial/ic784366.png "Znakowanie strony logowania")

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Work.com.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Work.com**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Work.com**.

    ![Link Work.com na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workcom-test-user"></a>Utwórz użytkownika testowego Work.com

Aby użytkownicy Azure Active Directory mogli się zalogować, muszą zostać zaobsługiwani do Work.com. W przypadku Work.com, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do firmowej witryny Work.com jako administrator.

2. Przejdź do pozycji **Konfiguracja**.
   
    ![Zrzut ekranu przedstawia konfigurację wybraną z menu użytkownika.](./media/work-com-tutorial/ic794108.png "Konfiguracja")

3. Przejdź do pozycji **Zarządzaj \> użytkownikami** użytkownicy.
   
    ![Zarządzanie użytkownikami](./media/work-com-tutorial/IC784369.png "Zarządzanie użytkownikami")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Wszyscy użytkownicy](./media/work-com-tutorial/IC794117.png "Wszyscy użytkownicy")

5. W sekcji Edycja użytkownika wykonaj następujące kroki w polu atrybuty prawidłowego konta usługi Azure AD, które chcesz udostępnić do powiązanych pól tekstowych:
   
    ![Edycja użytkownika](./media/work-com-tutorial/ic794118.png "Edycja użytkownika")
   
    a. W polu tekstowym **imię i nazwisko** , wpisz **imię** **Britta** użytkownika.
    
    b. W polu **tekstowym nazwisko** wpisz **nazwisko** użytkownika **Simon**.
    
    c. W polu tekstowym **alias** wpisz **nazwę** użytkownika **BrittaS**.
    
    d. W polu tekstowym adres **e-mail** wpisz **adres e-mail** użytkownika Brittasimon@contoso.com .
    
    e. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika, np Brittasimon@contoso.com .
    
    f. W polu tekstowym **Nazwa nick** wpisz **nazwę nick** użytkownika **Simon**.
    
    przykład Wybierz **rolę**, **licencję użytkownika** i **profil**.
    
    h. Kliknij pozycję **Zapisz**.  
      
    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim staną się aktywne.
    > 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Work.com w panelu dostępu należy automatycznie zalogować się do Work.com, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)