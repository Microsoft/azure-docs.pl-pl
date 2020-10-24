---
title: 'Samouczek: integracja Azure Active Directory z systemami Pega | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i systemami Pega.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 4fb117b7f7b9a0c7a6a67e2714380a01cd53a4e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515648"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: integracja Azure Active Directory z systemami Pega

W tym samouczku dowiesz się, jak zintegrować systemy Pega z usługą Azure Active Directory (Azure AD).

Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do systemów Pega.
* Możesz umożliwić użytkownikom automatyczne logowanie do systemów Pega (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z systemami Pega, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz zarejestrować się w celu uzyskania [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja systemów Pega z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Systemy Pega obsługują Logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.

## <a name="add-pega-systems-from-the-gallery"></a>Dodawanie systemów Pega z galerii

Aby skonfigurować integrację systemów Pega z usługą Azure AD, musisz dodać systemy Pega z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **systemy Pega**. W wynikach wyszukiwania wybierz pozycję **systemy Pega** , a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w systemach Pega przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w systemach Pega.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD z systemami Pega, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne w systemach Pega](#configure-pega-systems-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego systemów Pega](#create-a-pega-systems-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD z systemami Pega, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji **Pega systemu** wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu dostawcy tożsamości, w oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/idp-intiated.png)

    1. W polu **Identyfikator** wprowadź adres URL w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. W polu **adres URL odpowiedzi** wprowadź adres URL w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności.

    ![Informacje o rejestracji jednokrotnej w systemach Pega i adresach URL](common/both-advanced-urls.png)

    1. W polu **adres URL logowania** wprowadź wartość adres URL logowania.

    1. W polu **Stan przekazywania** wprowadź adres URL w tym wzorcu: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Podane tu wartości są symbolami zastępczymi. Musisz użyć rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i adresu URL stanu przekazywania. Możesz uzyskać identyfikatory i adresy URL odpowiedzi z aplikacji Pega, jak wyjaśniono w dalszej części tego samouczka. Aby uzyskać wartość stanu przekazywania, skontaktuj się z [zespołem pomocy technicznej systemów Pega](https://www.pega.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Pega Systems wymaga, aby potwierdzenia SAML były w określonym formacie. Aby uzyskać poprawny format, należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia domyślne atrybuty. Wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** :

    ![Atrybuty użytkownika](common/edit-attribute.png)

7. Oprócz atrybutów przedstawionych na poprzednim zrzucie ekranu aplikacja Pega Systems wymaga, aby w odpowiedzi SAML z powrotem przekazano kilka atrybutów. W sekcji **oświadczenia użytkownika** okna dialogowego **atrybuty użytkownika** wykonaj następujące kroki, aby dodać te atrybuty tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Te wartości są specyficzne dla Twojej organizacji. Podaj odpowiednie wartości.

    1. Wybierz pozycję **Dodaj nowe oświadczenie** , aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkowników** :

    ![Wybierz pozycję Dodaj nowe zgłoszenie](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    1. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    1. Pozostaw puste pole **przestrzeni nazw** .

    1. W polu **Źródło**wybierz pozycję **atrybut**.

    1. Na liście **atrybutów źródłowych** wybierz wartość atrybutu wyświetlaną dla tego wiersza.

    1. Wybierz przycisk **OK**.

    1. Wybierz pozycję **Zapisz**.

8. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **XML metadanych Federacji**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie systemów Pega** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w systemach Pega

1. Aby skonfigurować Logowanie jednokrotne na stronie **systemy Pega** , zaloguj się do portalu Pega przy użyciu konta administratora w innym oknie przeglądarki.

2. Wybierz pozycję **Utwórz**  >  **SysAdmin**  >  **usługę uwierzytelniania**sysadmin:

    ![Wybieranie usługi uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Wykonaj następujące kroki na ekranie **Tworzenie usługi uwierzytelniania** .

    ![Ekran tworzenia usługi uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Na liście **Typ** wybierz pozycję **SAML 2,0**.

    1. W polu **Nazwa** wprowadź dowolną nazwę (na przykład **Logowanie jednokrotne w usłudze Azure AD**).

    1. W polu **Krótki opis** wprowadź opis.  

    1. Wybierz pozycję **Utwórz i Otwórz**.
    
4. W sekcji **Informacje o dostawcy tożsamości (dostawcy tożsamości)** wybierz pozycję **Importuj metadane dostawcy tożsamości** i przejdź do pliku metadanych pobranego z Azure Portal. Kliknij pozycję **Prześlij** , aby załadować metadane:

    ![Sekcja informacji o dostawcy tożsamości (dostawcy tożsamości)](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Import wypełni dane dostawcy tożsamości, jak pokazano poniżej:

    ![Zaimportowane dane dostawcy tożsamości](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Wykonaj następujące kroki w sekcji **Ustawienia dostawcy usług (SP)** .

    ![Ustawienia dostawcy usług](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Skopiuj wartość **identyfikacji jednostki** i wklej ją w polu **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    1. Skopiuj wartość **lokalizacji usługi konsumenckej odbiorcy (ACS)** i wklej ją do pola **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    1. Wybierz pozycję **Wyłącz podpisywanie żądania**.

7. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wprowadź **brittasimon@ \<yourcompanydomain> . \<extension> **. (Na przykład BrittaSimon@contoso.com .)

    c. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, zapewniając dostęp do systemów Pega.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **systemy Pega**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **systemy Pega**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-pega-systems-test-user"></a>Tworzenie użytkownika testowego systemów Pega

Następnie należy utworzyć użytkownika o nazwie Britta Simon w systemach Pega. Współpraca z [zespołem pomocy technicznej systemów Pega](https://www.pega.com/contact-us) umożliwia tworzenie użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka systemy Pega w panelu dostępu należy automatycznie zalogować się do wystąpienia systemu Pega, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)