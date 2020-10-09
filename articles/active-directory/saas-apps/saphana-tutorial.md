---
title: 'Samouczek: integracja Azure Active Directory z SAP HANA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fc3b5b096e339d0c0f4bca7afc92d7315246fcd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552292"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Samouczek: integracja Azure Active Directory z SAP HANA

Z tego samouczka dowiesz się, jak zintegrować oprogramowanie SAP HANA z usługą Azure Active Directory (Azure AD).
Zintegrowanie oprogramowania SAP HANA z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do oprogramowania SAP HANA za pomocą usługi Azure AD.
* Możliwość skonfigurowania użytkowników pod kątem automatycznego logowania do oprogramowania SAP HANA przy użyciu kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP HANA, potrzebujesz następujących elementów:

- Subskrypcji usługi Azure AD
- Subskrypcja oprogramowania SAP HANA z włączonym logowaniem jednokrotnym
- Wystąpienie oprogramowania HANA uruchomionego w ramach dowolnego dużego wystąpienia publicznego usługi IaaS, lokalnej, Azure VM lub SAP na platformie Azure
- Internetowy interfejs administracyjny XSA oraz program HANA Studio zainstalowane w wystąpieniu oprogramowania HANA

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego oprogramowania SAP HANA do testowania kroków w tym samouczku. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja oprogramowania SAP HANA z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie SAP HANA obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Oprogramowanie SAP HANA obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-sap-hana-from-the-gallery"></a>Dodawanie oprogramowania SAP HANA z galerii

Aby skonfigurować integrację oprogramowania SAP HANA z usługą Azure AD, należy dodać oprogramowanie SAP HANA z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie SAP HANA z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **SAP HANA**, wybierz pozycję **SAP HANA** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Oprogramowanie SAP HANA na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP HANA, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem oprogramowania SAP HANA.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP HANA, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego oprogramowania SAP HANA](#configure-sap-hana-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego oprogramowania SAP HANA](#create-sap-hana-test-user)** — aby udostępnić w oprogramowaniu SAP HANA odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu SAP HANA, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji oprogramowania **SAP HANA** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL logowania jednokrotnego oprogramowania SAP HANA](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz następujący tekst: `HA100`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania SAP HANA](https://cloudplatform.sap.com/contact.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja SAP HANA oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image (obraz)](common/edit-attribute.png)

6. W sekcji **atrybuty użytkownika** w oknie dialogowym **atrybuty użytkownika & oświadczenia** wykonaj następujące czynności:
 
    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image (obraz)](./media/saphana-tutorial/tutorial_usermail.png)

    ![image (obraz)](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    c. Z listy **Parametr 1** wybierz pozycję **user.mail**.

    d. Kliknij przycisk **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Konfigurowanie logowania jednokrotnego oprogramowania SAP HANA

1. Aby skonfigurować logowanie jednokrotne po stronie oprogramowania SAP HANA, zaloguj się do **konsoli internetowej XSA oprogramowania HANA**, przechodząc do odpowiedniego punktu końcowego HTTPS.

    > [!NOTE]
    > W przypadku konfiguracji domyślnej adres URL przekierowuje żądanie do ekranu logowania, który wymaga poświadczeń uwierzytelnionego użytkownika bazy danych SAP HANA. Logujący się użytkownik musi mieć uprawnienia do wykonywania zadań administracyjnych dotyczących technologii SAML.

2. W interfejsie internetowym XSA wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML). W tym miejscu wybierz **+** przycisk w dolnej części ekranu, aby wyświetlić okienko **Dodawanie informacji o dostawcy tożsamości** . Następnie wykonaj następujące kroki:

    ![Dodawanie dostawcy tożsamości](./media/saphana-tutorial/sap1.png)

    a. W okienku **Add Identity Provider Info** (Dodawanie informacji o dostawcy tożsamości) wklej kod XML metadanych (pobrany z witryny Azure Portal) w polu **Metadata** (Metadane).

    ![Dodawanie ustawień dostawcy tożsamości](./media/saphana-tutorial/sap2.png)

    b. Jeśli zawartość dokumentu XML jest prawidłowa, proces analizy wyodrębni informacje wymagane dla pól **Subject (Podmiot), Entity ID (Identyfikator jednostki) i Issuer (Wystawca)** w obszarze ekranu **General data** (Dane ogólne). Wyodrębni także informacje niezbędne dla pól adresu URL w obszarze ekranu **Destination** (Cel), na przykład **Base URL (Podstawowy adres URL) i SingleSignOn URL (Adres URL logowania jednokrotnego) (*)**.

    ![Dodawanie ustawień dostawcy tożsamości](./media/saphana-tutorial/sap3.png)

    c. W polu **Name** (Nazwa) obszaru ekranu **General Data** (Dane ogólne) podaj nazwę dla nowego dostawcy tożsamości SAML logowania jednokrotnego.

    > [!NOTE]
    > Nazwa dostawcy tożsamości SAML jest wymagana i musi być unikatowa. Jest ona pokazywana na liście dostępnych dostawców tożsamości SAML wyświetlanej po wybraniu standardu SAML jako metody uwierzytelniania używanej przez aplikacje XS oprogramowania SAP HANA. Na przykład możesz to zrobić w obszarze ekranu **Authentication** (Uwierzytelnianie) narzędzia XS Artifact Administration.

3. Wybierz polecenie **Save** (Zapisz), aby zapisać szczegóły dostawcy tożsamości SAML i dodać nowego dostawcę tożsamości SAML do listy znanych dostawców tożsamości SAML.

    ![Przycisk Zapisz](./media/saphana-tutorial/sap4.png)

4. W programie Studio HANA na karcie **Configuration** (Konfiguracja) przefiltruj ustawienia za pomocą ciągu **saml**. Następnie dostosuj parametr **assertion_timeout**, zmieniając wartość **10 sec** na **120 sec**.

    ![Ustawienie assertion_timeout](./media/saphana-tutorial/sap7.png)

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

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do oprogramowania SAP HANA.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **SAP HANA**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz ciąg **SAP HANA** i wybierz odpowiednią pozycję.

    ![Link oprogramowania SAP HANA na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-hana-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP HANA

Aby umożliwić użytkownikom usługi Azure AD logowanie do oprogramowania SAP HANA, należy aprowizować ich w oprogramowaniu SAP HANA.
Oprogramowanie SAP HANA obsługuje **aprowizację just in time**, która jest domyślnie włączona.

Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące kroki:

>[!NOTE]
>Opcjonalnie zmień uwierzytelnianie zewnętrzne używane przez użytkownika. Uwierzytelnianie można przeprowadzać za pomocą systemu zewnętrznego, takiego jak Kerberos. Aby uzyskać szczegółowe informacje o tożsamościach zewnętrznych, skontaktuj się z [administratorem domeny](https://cloudplatform.sap.com/contact.html).

1. Otwórz program [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako administrator, a następnie włącz logowanie jednokrotne SAML dla użytkownika DB-User.

    ![Tworzenie użytkownika](./media/saphana-tutorial/sap5.png)

2. Zaznacz niewidoczne pole wyboru po lewej stronie pozycji **SAML**, a następnie wybierz link **Configure** (Konfiguruj).

3. Wybierz pozycję **Add** (Dodaj), aby dodać dostawcę tożsamości SAML.  Wybierz odpowiedniego dostawcę tożsamości SAML, a następnie wybierz pozycję **OK**.

4. Dodaj **tożsamość zewnętrzną** (w tym przypadku BrittaSimon) lub wybierz pozycję **Any** (Dowolna). Następnie wybierz przycisk **OK**.

   > [!Note]
   > Jeśli pole wyboru **Any** (Dowolna) nie jest zaznaczone, nazwa użytkownika w oprogramowaniu HANA musi dokładnie pasować do głównej nazwy użytkownika przed sufiksem domeny. (Na przykład BrittaSimon@contoso.com to BrittaSimon w oprogramowaniu HANA).

5. Dla celów testowych przypisz wszystkie role **XS** do użytkownika.

    ![Przypisywanie ról](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Należy udzielić tylko uprawnień odpowiednich dla danego przypadku użycia.

6. Zapisz użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP HANA na panelu dostępu powinno nastąpić automatyczne zalogowanie do oprogramowania SAP HANA, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

