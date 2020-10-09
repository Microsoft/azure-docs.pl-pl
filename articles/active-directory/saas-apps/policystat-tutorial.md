---
title: 'Samouczek: integracja Azure Active Directory z usługą PolicyStat | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 212bd1bc0b15ad985fc8037e99876d78132b2c22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88547940"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Samouczek: integracja Azure Active Directory z usługą PolicyStat

W tym samouczku dowiesz się, jak zintegrować usługę PolicyStat z usługą Azure Active Directory (Azure AD).
Integracja PolicyStat z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi PolicyStat.
* Możesz pozwolić użytkownikom na automatyczne logowanie do PolicyStat (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą PolicyStat, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w PolicyStat

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* PolicyStat obsługuje logowanie jednokrotne w usłudze **SP**

* PolicyStat obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-policystat-from-the-gallery"></a>Dodawanie PolicyStat z galerii

Aby skonfigurować integrację programu PolicyStat z usługą Azure AD, musisz dodać PolicyStat z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać PolicyStat z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **PolicyStat**, wybierz pozycję **PolicyStat** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![PolicyStat na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą PolicyStat na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w PolicyStat.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi PolicyStat, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-policystat-single-sign-on)** jednokrotne w usłudze PolicyStat, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
4. **[Utwórz użytkownika testowego PolicyStat](#create-policystat-test-user)** , aby uzyskać odpowiednik Britta Simon w PolicyStat, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą PolicyStat, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **PolicyStat** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![PolicyStat domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta PolicyStat](http://www.policystat.com/support/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

5. Aplikacja PolicyStat oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![image (obraz)](common/edit-attribute.png)

6. Oprócz powyższych, aplikacja PolicyStat oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy |
    |------------------- | -------------------- |
    | Identyfikator UID | ExtractMailPrefix ([Poczta]) |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.
    
    ![image (obraz)](common/new-save-attribute.png)

    ![image (obraz)](./media/policystat-tutorial/attribute01.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Wybierz źródło jako **transformację**.

    e. Z listy **przekształcenie** wpisz wartość atrybutu wyświetlaną dla tego wiersza.
    
    f. Z listy **parametr 1** wpisz wartość atrybutu wyświetlaną dla tego wiersza.

    przykład Kliknij przycisk **Zapisz**.

7. W sekcji **Konfigurowanie PolicyStat** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-policystat-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On PolicyStat

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny PolicyStat jako administrator.

2. Kliknij kartę **administrator** , a następnie kliknij pozycję **konfiguracja pojedynczej Sign-On** w lewym okienku nawigacji.
   
    ![Menu administratora](./media/policystat-tutorial/ic808633.png "Menu administratora")

3. Kliknij **swoje metadane dostawcy tożsamości**, a następnie w sekcji **metadane dostawcy tożsamości** wykonaj następujące czynności:
   
    ![Konfiguracja pojedynczego Sign-On](./media/policystat-tutorial/ic808636.png "Konfiguracja pojedynczego Sign-On")
   
    a. Otwórz pobrany plik metadanych, skopiuj zawartość, a następnie wklej ją do pola tekstowego **metadanych dostawcy tożsamości** .

    b. Kliknij przycisk **Zapisz zmiany**.

4. Kliknij pozycję **Konfiguruj atrybuty**, a następnie w sekcji **Konfigurowanie atrybutów** wykonaj następujące czynności:
   
    a. W polu tekstowym **Nazwa użytkownika** wpisz **UID**.

    b. W polu tekstowym **nazwa atrybutu** wpisz swoją pierwszą nazwę z platformy Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. W polu tekstowym **nazwa atrybutu** wpisz swoją ostatnią nazwę z platformy Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. W polu tekstowym **atrybut wiadomości e-mail** wpisz nazwę żądania atrybutu adresu E-mail z platformy Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Kliknij przycisk **Zapisz zmiany**.

5. W sekcji **Konfiguracja** wybierz opcję **Włącz integrację logowania**jednokrotnego.
   
    ![Konfiguracja pojedynczego Sign-On](./media/policystat-tutorial/ic808634.png "Konfiguracja pojedynczego Sign-On")


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć własne konto do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do PolicyStat.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **PolicyStat**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **PolicyStat**.

    ![Link PolicyStat na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz swoje konto na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-policystat-test-user"></a>Utwórz użytkownika testowego PolicyStat

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w PolicyStat. PolicyStat obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze PolicyStat, zostanie utworzony nowy po uwierzytelnieniu.

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników PolicyStat i interfejsów API udostępnionych przez usługę PolicyStat.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka PolicyStat w panelu dostępu należy automatycznie zalogować się do PolicyStat, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
