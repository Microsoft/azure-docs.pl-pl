---
title: 'Samouczek: integracja Azure Active Directory z usługą ArcGIS Online | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługami Azure Active Directory i ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 28e15cb7e0b519932eeee3a6a6445449affe2f35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551374"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Samouczek: integracja Azure Active Directory z usługą ArcGIS Online

W tym samouczku dowiesz się, jak zintegrować usługę ArcGIS Online z usługą Azure Active Directory (Azure AD).
Integracja usługi ArcGIS Online z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi ArcGIS Online.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie w usłudze ArcGIS Online (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą ArcGIS Online potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi ArcGIS Online z obsługą logowania jednokrotnego

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa ArcGIS Online obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-arcgis-online-from-the-gallery"></a>Dodawanie usługi ArcGIS Online z galerii

Aby skonfigurować integrację usługi ArcGIS Online z usługą Azure AD, należy dodać usługę ArcGIS Online z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę ArcGIS Online z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ArcGIS Online**, wybierz opcję **ArcGIS Online** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa ArcGIS Online na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą usługi ArcGIS Online, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi ArcGIS.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą ArcGIS, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego usługi ArcGIS Online](#configure-arcgis-online-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi ArcGIS Online](#create-arcgis-online-test-user)** — aby utworzyć odpowiednik użytkownika Britta Simon w usłudze ArcGIS Online, który będzie powiązany z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z usługą ArcGIS Online, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ArcGIS Online** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące adresów URL logowania jednokrotnego i domeny usługi ArcGIS Online](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.maps.arcgis.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta usługi ArcGIS Online](https://support.esri.com/en/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Aby zautomatyzować konfigurację w usłudze **ArcGIS Online**, musisz zainstalować **rozszerzenie przeglądarki do bezpiecznego logowania Moje aplikacje**, klikając pozycję **Zainstaluj rozszerzenie**.

    ![image (obraz)](./media/arcgis-tutorial/install_extension.png)

7. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację ArcGIS Online**. Spowoduje to bezpośrednie przejście do aplikacji ArcGIS Online. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji ArcGIS Online. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki w sekcji **Konfigurowanie logowania jednokrotnego usługi ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi ArcGIS Online

1. Jeśli chcesz ręcznie skonfigurować usługę ArcGIS Online, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmowej usługi ArcGIS Online jako administrator, a następnie wykonaj następujące czynności:

2. Kliknij pozycję **EDYTUJ USTAWIENIA**.

    ![Edytuj ustawienia](./media/arcgis-tutorial/ic784742.png "Edytuj ustawienia")

3. Kliknij pozycję **Security** (Zabezpieczenia).

    ![Zabezpieczenia](./media/arcgis-tutorial/ic784743.png "Zabezpieczenia")

4. W obszarze **Identyfikatory logowania warstwy Enterprise** kliknij przycisk **SET IDENTITY PROVIDER** (USTAW DOSTAWCĘ TOŻSAMOŚCI).

    ![Identyfikatory logowania przedsiębiorstwa](./media/arcgis-tutorial/ic784744.png "Identyfikatory logowania przedsiębiorstwa")

5. Na stronie konfiguracji **Ustawienie dostawcy tożsamości** wykonaj następujące działania:

    ![Ustaw dostawcę tożsamości](./media/arcgis-tutorial/ic784745.png "Ustaw dostawcę tożsamości")

    a. W polu tekstowym **Nazwa** wpisz nazwę swojej organizacji.

    b. W pozycji **Metadata for the Enterprise Identity Provider will be supplied using** (Metadane dla dostawcy tożsamości przedsiębiorstwa zostaną dostarczone przy użyciu) wybierz opcję **A File** (Plik).

    c. Aby przekazać pobrany plik metadanych, kliknij pozycję **Wybierz plik**.

    d. Kliknij opcję **SET IDENTITY PROVIDER** (USTAW DOSTAWCĘ TOŻSAMOŚCI).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi ArcGIS Online.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **ArcGIS Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz opcję **ArcGIS Online**.

    ![Link usługi ArcGIS Online na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-arcgis-online-test-user"></a>Tworzenie użytkownika testowego usługi ArcGIS Online

Aby umożliwić użytkownikom usługi Azure AD logowanie w usłudze ArcGIS Online, należy ich aprowizować w usłudze ArcGIS Online.  
W przypadku usługi ArcGIS Online aprowizacja jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawcy **ArcGIS**.

2. Kliknij przycisk **INVITE MEMBERS** (ZAPROŚ CZŁONKÓW).
   
    ![Zapraszanie członków](./media/arcgis-tutorial/ic784747.png "Zapraszanie członków")

3. Wybierz opcję **Add members automatically without sending an email** (Dodaj członków automatycznie bez wysyłania wiadomości e-mail), a następnie kliknij przycisk **NEXT** (DALEJ).
   
    ![Dodaj członków automatycznie](./media/arcgis-tutorial/ic784748.png "Dodaj członków automatycznie")

4. Na stronie okna dialogowego **Members** (Członkowie) wykonaj następujące czynności:
   
     ![Dodaj i przejrzyj](./media/arcgis-tutorial/ic784749.png "Dodaj i przejrzyj")
    
     a. Wprowadź **adres e-mail**, **imię**i nazwisko prawidłowego konta usługi Azure AD **, dla którego** chcesz zainicjować obsługę administracyjną.
  
     b. Kliknij przycisk **ADD AND REVIEW** (DODAJ I PRZEJRZYJ).
5. Przejrzyj wprowadzone dane, a następnie kliknij przycisk **ADD MEMBERS** (DODAJ CZŁONKÓW).
   
    ![Dodaj członka](./media/arcgis-tutorial/ic784750.png "Dodaj członka")
        
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ArcGIS Online w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi ArcGIS Online, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

