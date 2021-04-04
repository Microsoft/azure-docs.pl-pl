---
title: 'Samouczek: integracja Azure Active Directory z przeglądarką InstaVR | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją InstaVR Viewer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460011"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Samouczek: integracja Azure Active Directory z przeglądarką InstaVR

Z tego samouczka dowiesz się, jak zintegrować aplikację InstaVR Viewer z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji InstaVR Viewer z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji InstaVR Viewer.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji InstaVR Viewer (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją InstaVR Viewer potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji InstaVR Viewer z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja InstaVR Viewer obsługuje logowanie jednokrotne inicjowane przez **SP**
* Aplikacja InstaVR Viewer obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Dodawanie aplikacji InstaVR Viewer z galerii

Aby skonfigurować integrację aplikacji InstaVR Viewer z usługą Azure AD, musisz dodać aplikację InstaVR Viewer z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację InstaVR Viewer z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **InstaVR Viewer**, wybierz pozycję **InstaVR Viewer** w panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja InstaVR Viewer na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją InstaVR Viewer, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji InstaVR Viewer.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją InstaVR Viewer, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji InstaVR Viewer](#configure-instavr-viewer-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji InstaVR Viewer](#create-instavr-viewer-test-user)** — aby mieć w aplikacji InstaVR Viewer odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji InstaVR Viewer, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **InstaVR Viewer** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji InstaVR Viewer — informacje o logowaniu jednokrotnym](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Nie ma ustalonego wzorca adresu URL logowania. Jest on generowany, gdy klient InstaVR Viewer tworzy pakiety internetowe. Wzorzec jest unikatowy dla każdego klienta i pakietu. W celu uzyskania dokładnego adresu URL logowania musisz zalogować się do wystąpienia aplikacji InstaVR Viewer i utworzyć pakiety internetowe.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość przy użyciu rzeczywistej wartości identyfikatora opisanej w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** i **plik metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisać je na komputerze.

    ![Link do pobierania certyfikatu](common/metadata-certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację InstaVR Viewer** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-instavr-viewer-single-sign-on"></a>Konfigurowanie logowania jednokrotnego do aplikacji InstaVR Viewer

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do firmowej witryny aplikacji InstaVR Viewer jako administrator.

2. Kliknij **ikonę użytkownika** i wybierz pozycję **Konto**.

    ![Zrzut ekranu przedstawia witrynę przeglądarki InstaVR z wybranym użytkownikiem.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Przewiń w dół do pozycji **Uwierzytelnianie SAML** i wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę uwierzytelniania języka SAML, na której można wprowadzić wartości opisane w tym kroku.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. W polu tekstowym **Adres URL logowania jednokrotnego** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Aby przekazać pobrany plik certyfikatu, kliknij pozycję **Aktualizuj**.

    e. Aby przekazać pobrany plik metadanych federacji, kliknij pozycję **Aktualizuj**.

    f. Skopiuj wartość **identyfikatora jednostki** i wklej ją w polu tekstowym **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

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

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji InstaVR Viewer.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **InstaVR Viewer**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **InstaVR Viewer**.

    ![Link do aplikacji InstaVR Viewer na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-instavr-viewer-test-user"></a>Tworzenie użytkownika testowego aplikacji InstaVR Viewer

W tej sekcji w aplikacji InstaVR Viewer jest tworzony użytkownik o nazwie Britta Simon. Aplikacja InstaVR Viewer obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji InstaVR Viewer, zostanie utworzony po uwierzytelnieniu. Jeśli napotkasz problemy, skontaktuj się z [zespołem pomocy technicznej ds. aplikacji InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do firmowej witryny aplikacji InstaVR Viewer jako administrator.

2. Wybierz pozycję **Pakiet** w panelu nawigacyjnym po lewej stronie i wybierz pozycję **Utwórz pakiet internetowy**.

    ![Zrzut ekranu przedstawia witrynę firmy InstaVR Viewer z wybraną pozycją pakiet i Utwórz pakiet dla sieci Web.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Kliknij pozycję **Pobierz**.

    ![Zrzut ekranu przedstawia wybraną ikonę pobierania.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Wybierz pozycję **Otwórz hostowaną stronę**, co spowoduje przekierowanie do usługi Azure AD w celu zalogowania.

    ![Zrzut ekranu przedstawia wybraną otwartą stronę hostowaną.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Wprowadź swoje poświadczenia usługi Azure AD, aby pomyślnie zalogować się do usługi Azure AD za pomocą logowania jednokrotnego.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)