---
title: 'Samouczek: integracja Azure Active Directory z optymalizacją | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zoptymalizować.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c10681a053631a338bd0b9a7d19c5625151a9a5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515801"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Samouczek: integracja Azure Active Directory z optymalizacją

W tym samouczku dowiesz się, jak przeprowadzić integrację z usługą Azure Active Directory (Azure AD).
Integracja z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do optymalizacji.
* Możesz umożliwić użytkownikom automatyczne logowanie się w celu optymalizacji (logowania jednokrotnego) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu optymalizacji, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Optymalizacja subskrypcji z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Optymalizacja obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-optimizely-from-the-gallery"></a>Dodawanie optymalizacji z galerii

Aby skonfigurować integrację programu z usługą Azure AD, należy dodać optymalizację z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać optymalizację z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **optymalizację**, wybierz opcję **Optymalizuj** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Optymalizacja na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD z optymalizacją na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w optymalizacji.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu optymalizacji, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj optymalizację przy użyciu logowania jednokrotnego](#configure-optimizely-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz optymalizację użytkownika testowego](#create-optimizely-test-user)** , aby uzyskać odpowiednik Britta Simon w sposób optymalny, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu optymalizacji, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Optymalizacja** integracji aplikacji wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Optymalizowanie informacji o rejestracji jednokrotnej w domenach i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://app.optimizely.net/<instance name>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Wartość będzie aktualizowana przy użyciu rzeczywistego adresu URL logowania i identyfikatora, który jest wyjaśniony w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Twoja aplikacja optymalizacji oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Zrzut ekranu pokazujący okno dialogowe "atrybuty użytkownika" z ikoną "Edytuj" w prawym górnym rogu.](common/edit-attribute.png)

6. Oprócz powyższych, optymalizacja aplikacji oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | poczta e-mail | user.mail |
    
    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawiający okno dialogowe "oświadczenia użytkownika" z wyróżnionymi akcjami "Dodaj nowe oświadczenie" i "Zapisz".](common/new-save-attribute.png)

    ![image (obraz)](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj optymalizację** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-optimizely-single-sign-on"></a>Konfigurowanie optymalizacji pojedynczego Sign-On

1. Aby **skonfigurować logowanie** jednokrotne, należy skontaktować się z zarządzanym menedżerem kont i udostępnić pobrany **certyfikat (base64)** oraz odpowiednie skopiowane adresy URL.

2. W odpowiedzi na wiadomość e-mail Optymalizacja zapewnia optymalizację adresów URL logowania (Logowanie jednokrotne w usłudze SP) i identyfikator (identyfikator jednostki dostawcy usług).

    a. Skopiuj **zainicjowany przez usługę Sp adres URL logowania jednokrotnego** , który został przez siebie zoptymalizowany, i wklej do pola tekstowego **Logowanie w adresie URL** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    b. Skopiuj **Identyfikator jednostki dostawcy usług** zapewniony przez optymalizację i wklej do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

3. W innym oknie przeglądarki Zaloguj się do aplikacji Optymalizacja.

4. Kliknij nazwę konta w prawym górnym rogu, a następnie pozycję **Ustawienia konta**.

    ![Zrzut ekranu przedstawiający nazwę konta wybraną w prawym górnym rogu z menu.](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Na karcie konto zaznacz pole wyboru **Włącz logowanie jednokrotne** w obszarze Rejestracja jednokrotna w sekcji **Przegląd** .
  
    ![Pojedynczy Sign-On usługi Azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do optymalizacji.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz opcję **Optymalizacja**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **Optymalizacja**.

    ![Link zoptymalizowany na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-optimizely-test-user"></a>Tworzenie zoptymalizowanego użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w sposób optymalny.

1. Na stronie głównej wybierz kartę **współpracowników** .

2. Aby dodać nowego współpracownika do projektu, kliknij przycisk **Nowy współpracownik**.
   
    ![Zrzut ekranu, na którym jest zaznaczona wybrana Strona główna "współpracownicy" i przycisk "nowy współpracownik".](./media/optimizely-tutorial/create_aaduser_10.png)

3. Wprowadź adres e-mail i przypisz im rolę. Kliknij pozycję **Invite** (Zaproś).

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Otrzymają wiadomość e-mail z zaproszeniem. Przy użyciu adresu e-mail muszą oni zalogować się w celu optymalizacji.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Optymalizacja w panelu dostępu należy automatycznie zalogować się do programu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)