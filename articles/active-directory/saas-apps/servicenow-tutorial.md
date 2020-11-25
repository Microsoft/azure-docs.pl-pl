---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą usługi ServiceNow | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 3413e594179cdca0704cb5db7908276b7502e719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004759"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą usługi ServiceNow

W tym samouczku dowiesz się, jak zintegrować usługę usługi ServiceNow z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi usługi ServiceNow z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi ServiceNow.
* Zezwól użytkownikom na automatyczne logowanie się do usługi usługi ServiceNow przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi usługi ServiceNow.
* W przypadku usługi usługi ServiceNow wystąpienie lub dzierżawca usługi ServiceNow obsługuje Calgary, Kingston, Londyn, Madryt, Nowy Jork, Orlando i Paryż w wersji lub nowszej.
* W przypadku programu usługi ServiceNow Express wystąpienie klasy usługi ServiceNow Express, w wersji Helsinki lub nowszej.
* W dzierżawie usługi ServiceNow musi być włączona wtyczka [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (wtyczka logowania jednokrotnego u wielu dostawców).
* W przypadku konfiguracji automatycznej włącz wtyczkę wielu dostawców dla usługi ServiceNow.
* Aby zainstalować klasyczną aplikację usługi ServiceNow (Mobile), przejdź do odpowiedniego magazynu i Wyszukaj aplikację klasyczną usługi ServiceNow. Następnie pobierz go.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Usługa usługi ServiceNow obsługuje usługę **SP** zainicjowaną przez usługę SSO.

* Usługi ServiceNow obsługuje [Automatyczne Inicjowanie obsługi użytkowników](servicenow-provisioning-tutorial.md).

* Aby włączyć logowanie jednokrotne, można skonfigurować aplikację klasyczną usługi ServiceNow (Mobile) w usłudze Azure AD. Obsługuje zarówno użytkowników systemu Android, jak i iOS. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

## <a name="add-servicenow-from-the-gallery"></a>Dodaj usługi ServiceNow z galerii

Aby skonfigurować integrację usługi ServiceNow z usługą Azure AD, musisz dodać usługę ServiceNow z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub za pomocą konto Microsoft osobistych.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** i wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **usługi ServiceNow** w polu wyszukiwania.
1. Wybierz pozycję **usługi ServiceNow** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi ServiceNow

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi ServiceNow przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi ServiceNow.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi ServiceNow, wykonaj następujące czynności:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
    1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD dla programu usługi ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express) , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj usługi ServiceNow](#configure-servicenow) , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego usługi ServiceNow](#create-servicenow-test-user) , aby miał odpowiednik B. Simon w usługi ServiceNow, połączony z reprezentacją użytkownika usługi Azure AD.
    1. [Skonfiguruj Logowanie jednokrotne w usłudze usługi ServiceNow Express](#configure-servicenow-express-sso) , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.  
3. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.
4. [Przetestuj Logowanie jednokrotne dla usługi ServiceNow klasyczny (Mobile)](#test-sso-for-servicenow-classic-mobile) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **usługi ServiceNow** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczego Sign-On ze stroną SAML z wyróżnioną ikoną pióra](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    a. W polu **adres URL logowania** wprowadź adres URL, który używa następującego wzorca: `https://<instancename>.service-now.com/navpage.do`

    b. W **identyfikatorze (identyfikator jednostki)** wprowadź adres URL, który używa następującego wzorca: `https://<instance-name>.service-now.com`

    c. W polu **adres URL odpowiedzi** wprowadź jeden z następujących wzorców URL:

    | Adres URL odpowiedzi|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. W polu **adres URL wylogowywania** wprowadź adres URL, który używa następującego wzorca: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Jeśli w wartości identyfikatora zostanie dodany parametr "/", usuń to ręcznie.

    > [!NOTE]
    > To nie są rzeczywiste wartości. Musisz zaktualizować te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi, adresu URL wylogowania i identyfikatora, który został wyjaśniony w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)**. 

   ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnioną pozycją Pobierz](common/certificatebase64.png)

   a. Wybierz przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i wkleić go do Notatnika. Ten adres URL zostanie użyty w dalszej części tego samouczka.

    b. Wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)**, a następnie Zapisz plik certyfikatu na komputerze.

1. W sekcji **Konfiguracja usługi ServiceNow** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

   ![Zrzut ekranu przedstawiający sekcję Set up usługi ServiceNow z wyróżnionymi adresami URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W obszarze **Nazwa** wprowadź `B.Simon` .  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi usługi ServiceNow.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ServiceNow**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie wybierz **pozycję Wybierz**.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow Express

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi ServiceNow** wybierz pozycję **Logowanie jednokrotne**.

    ![Zrzut ekranu strony integracji aplikacji usługi ServiceNow z wyróżnionym logowaniem jednokrotnym](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Zrzut ekranu przedstawiający wybór metody logowania jednokrotnego z wyróżnioną pozycją SAML](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę pióra, aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Zrzut ekranu przedstawiający Konfigurowanie logowania jednokrotnego przy użyciu strony SAML z wyróżnioną ikoną pióra](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    a. W polu **adres URL logowania** wprowadź adres URL, który używa następującego wzorca: `https://<instancename>.service-now.com/navpage.do`

    b. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL, który używa następującego wzorca: `https://<instance-name>.service-now.com`

    c. W polu **adres URL odpowiedzi** wprowadź jeden z następujących adresów URL:

    | Adres URL odpowiedzi |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. W polu **adres URL wylogowywania** wprowadź adres URL, który używa następującego wzorca: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Jeśli w wartości identyfikatora zostanie dodany parametr "/", usuń to ręcznie.

    > [!NOTE]
    > To nie są rzeczywiste wartości. Musisz zaktualizować te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi, adresu URL wylogowania i identyfikatora, który został wyjaśniony w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)** z określonych opcji zgodnie z wymaganiami. Zapisz go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnioną pozycją Pobierz](common/certificatebase64.png)

6. Usługa Azure AD może automatycznie konfigurować usługi ServiceNow na potrzeby uwierzytelniania opartego na protokole SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfigurowanie usługi ServiceNow** i wybierz pozycję **Wyświetl instrukcje krok po kroku** , aby otworzyć okno **Konfigurowanie logowania** .

    ![Zrzut ekranu przedstawiający sekcję Set up usługi ServiceNow z wyróżnioną instrukcją krok po kroku](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. W formularzu **Konfigurowanie logowania** wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora. Wybierz pozycję **Konfiguruj teraz**. Podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usługi ServiceNow do działania. W przeciwnym razie aby ręcznie skonfigurować usługi ServiceNow do korzystania z usługi Azure AD jako dostawcy tożsamości SAML, wybierz pozycję **ręcznie skonfiguruj Logowanie jednokrotne**. Skopiuj **adres URL wylogowania, identyfikator usługi Azure AD i adres URL logowania** z sekcji Szybkie informacje.

    ![Zrzut ekranu przedstawiający formularz konfigurowania logowania z wyróżnioną pozycją Skonfiguruj teraz](./media/servicenow-tutorial/configure.png "Konfiguruj adres URL aplikacji")

## <a name="configure-servicenow"></a>Konfigurowanie usługi ServiceNow

1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

1. Aktywuj **integrację — wtyczka Instalatora logowania** jednokrotnego z wieloma dostawcami, wykonując następujące czynności:

    a. W okienku po lewej stronie Wyszukaj sekcję **Definicja systemu** w polu wyszukiwania, a następnie wybierz pozycję **wtyczki**.

    ![Zrzut ekranu sekcji definicji systemu z wyróżnioną definicją systemu i wtyczkami](./media/servicenow-tutorial/tutorial-servicenow-03.png "Aktywuj wtyczkę")

    b. Wyszukaj **integrację — Instalator logowania** jednokrotnego dla wielu dostawców.

     ![Zrzut ekranu strony Dodatki systemowe z integracją — wyróżniono pojedynczy Instalator Sign-On jednego dostawcy](./media/servicenow-tutorial/tutorial-servicenow-04.png "Aktywuj wtyczkę")

    c. Wybierz wtyczkę. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Aktywuj/Uaktualnij**.

     ![Zrzut ekranu przedstawiający menu po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Aktywuj/Uaktualnij](./media/servicenow-tutorial/tutorial-activate.png "Aktywuj wtyczkę")

    d. Wybierz pozycję **Aktywuj**.

     ![Zrzut ekranu okna dialogowego aktywowanie wtyczki z wyróżnioną pozycją Aktywuj](./media/servicenow-tutorial/tutorial-activate-1.png "Aktywuj wtyczkę")

1. W okienku po lewej stronie Wyszukaj sekcję **Logowanie jednokrotne dla rejestracji jednoportowej** na pasku wyszukiwania, a następnie wybierz pozycję **Właściwości**.

    ![Zrzut ekranu wieloskładnikowego logowania jednokrotnego, z wyróżnionym LOGOWANIEm jednoportowym i właściwościami](./media/servicenow-tutorial/tutorial-servicenow-06.png "Konfiguruj adres URL aplikacji")

1. W oknie dialogowym **wiele właściwości logowania jednokrotnego dla dostawcy** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Właściwości logowania jednokrotnego dla wielu dostawców](./media/servicenow-tutorial/ic7694981.png "Konfiguruj adres URL aplikacji")

    * Aby **włączyć logowanie jednokrotne dla wielu dostawców**, wybierz pozycję **tak**.
  
    * Dla opcji **Włącz autoimportowanie użytkowników ze wszystkich dostawców tożsamości do tabeli użytkownik** wybierz pozycję **tak**.

    * Aby **włączyć rejestrowanie debugowania dla integracji z logowaniem JEDNOkrotnym dla wielu dostawców**, wybierz pozycję **tak**.

    * Dla **pola w tabeli User...** wprowadź **adres e-mail**.
  
    * Wybierz pozycję **Zapisz**.

1. Usługi ServiceNow można skonfigurować automatycznie lub ręcznie. Aby skonfigurować usługi ServiceNow automatycznie, wykonaj następujące kroki:

    1. Wróć do strony logowania jednokrotnego **usługi ServiceNow** w Azure Portal.

    1. Dla usługi usługi ServiceNow jest dostępna jedna usługa konfiguracji. Aby włączyć tę usługę, przejdź do sekcji **Konfiguracja usługi ServiceNow** i wybierz pozycję **Konfiguruj usługi ServiceNow** , aby otworzyć okno **Konfigurowanie logowania** .

        ![Zrzut ekranu przedstawiający konfigurację usługi ServiceNow z wyróżnionymi instrukcjami krok po kroku](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. W formularzu **Konfigurowanie logowania** wprowadź nazwę wystąpienia usługi ServiceNow, nazwa użytkownika administratora i hasło administratora. Wybierz pozycję **Konfiguruj teraz**. Podana nazwa użytkownika administratora musi mieć przypisaną rolę **administrator zabezpieczeń** w programie usługi ServiceNow, aby to działało. W przeciwnym razie aby ręcznie skonfigurować usługi ServiceNow do korzystania z usługi Azure AD jako dostawcy tożsamości SAML, wybierz pozycję **ręcznie skonfiguruj Logowanie jednokrotne**. Skopiuj adres **URL wylogowania, identyfikator jednostki SAML i adres URL usługi logowania** jednokrotnego SAML z sekcji Szybkie informacje.

        ![Zrzut ekranu przedstawiający formularz konfigurowania logowania z wyróżnioną pozycją Skonfiguruj teraz](./media/servicenow-tutorial/configure.png "Konfiguruj adres URL aplikacji")

    1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

       * W konfiguracji automatycznej wszystkie wymagane ustawienia są konfigurowane po stronie **usługi ServiceNow** , ale **certyfikat X. 509** nie jest domyślnie włączony i nadaj **pojedynczej Sign-On wartość skryptu** jako **MultiSSOv2_SAML2_custom**. Musisz ręcznie zmapować dostawcę tożsamości na usługi ServiceNow. Wykonaj następujące kroki:

         1. W okienku po lewej stronie Wyszukaj sekcję **Logowanie jednokrotne SSO** w polu wyszukiwania, a następnie wybierz pozycję **dostawcy tożsamości**.

            ![Zrzut ekranu wieloskładnikowego logowania jednokrotnego z wyróżnionymi dostawcami tożsamości](./media/servicenow-tutorial/tutorial-servicenow-07.png "Konfigurowanie logowania jednokrotnego")

         1. Wybierz automatycznie generowanego dostawcę tożsamości.

            ![Zrzut ekranu dostawców tożsamości z wyróżnionym automatycznie wygenerowanym dostawcą tożsamości](./media/servicenow-tutorial/tutorial-servicenow-08.png "Konfigurowanie logowania jednokrotnego")

         1.  W sekcji **Identity Provider** (Dostawca tożsamości) wykonaj następujące kroki:

             ![Zrzut ekranu przedstawiający sekcję dostawca tożsamości](./media/servicenow-tutorial/automatic-config.png "Konfigurowanie logowania jednokrotnego")

               a. W obszarze **Nazwa** wprowadź nazwę konfiguracji (na przykład **Microsoft Azure federacyjne Logowanie jednokrotne**).

               b. Skopiuj wartość **strony głównej usługi ServiceNow** i wklej ją w **adresie URL logowania** w sekcji **Podstawowa konfiguracja SAML usługi ServiceNow** Azure Portal.

                > [!NOTE]
                > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

              c. Skopiuj wartość **Identyfikator jednostki/wystawcy** i wklej ją w **identyfikatorze** w sekcji **usługi servicenow Podstawowa konfiguracja SAML** w Azure Portal.

              d. Upewnij się, że **zasady NameID** są ustawione na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` wartość. 

              e. Kliknij pozycję **Zaawansowane** i nadaj **pojedynczej Sign-On wartość skryptu** jako **MultiSSOv2_SAML2_custom**.

         1. Przewiń w dół do sekcji **certyfikat X. 509** , a następnie wybierz pozycję **Edytuj**.

             ![Zrzut ekranu przedstawiający sekcję certyfikatu X. 509 z wyróżnioną pozycją Edycja](./media/servicenow-tutorial/tutorial-servicenow-09.png "Konfigurowanie logowania jednokrotnego")

         1. Wybierz certyfikat, a następnie wybierz ikonę strzałki w prawo, aby dodać certyfikat

            ![Zrzut ekranu kolekcji z wyróżnioną ikoną certyfikat i Strzałka w prawo](./media/servicenow-tutorial/tutorial-servicenow-11.png "Konfigurowanie logowania jednokrotnego")

          1. Wybierz pozycję **Zapisz**.

          1. W prawym górnym rogu strony wybierz pozycję **Testuj połączenie**.

             ![Zrzut ekranu strony z wyróżnionym połączeniem testowym](./media/servicenow-tutorial/tutorial-activate-2.png "Aktywuj wtyczkę")

             > [!NOTE]
             > Jeśli połączenie testowe kończy się niepowodzeniem i nie można aktywować tego połączenia, usługi ServiceNow oferuje przełącznik przesłonięcia. Musisz wprowadzić **Sys_properties. Lista** w obszarze **nawigacji wyszukiwania** i zostanie otwarta nowa strona właściwości systemu. W tym miejscu trzeba utworzyć nową właściwość o nazwie **Glide. Authenticate. multisso. test. Connection. obowiązkowy** z **typem danych** jako **true/false** , a następnie ustawić **wartość** **false**.

             > ![Zrzut ekranu przedstawiający stronę Test connection](./media/servicenow-tutorial/test-connection-fail.png "Konfigurowanie logowania jednokrotnego")
        
          1. Po wyświetleniu monitu o podanie poświadczeń wprowadź je. Zostanie wyświetlona następująca strona. Jest oczekiwany błąd **wyniki testów wylogowywania z logowaniem jednokrotnym** . Zignoruj błąd i wybierz pozycję  **Aktywuj**.

             ![Zrzut ekranu przedstawiający stronę poświadczeń](./media/servicenow-tutorial/servicenow-activate.png "Konfigurowanie logowania jednokrotnego")
  
1. Aby ręcznie skonfigurować **usługi ServiceNow** , wykonaj następujące kroki:

    1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    1. W lewym okienku wybierz pozycję **dostawcy tożsamości**.

        ![Zrzut ekranu wielowymiarowego logowania jednokrotnego z wyróżnionymi dostawcami tożsamości](./media/servicenow-tutorial/tutorial-servicenow-07.png "Konfigurowanie logowania jednokrotnego")

    1. W oknie dialogowym **dostawcy tożsamości** wybierz pozycję **Nowy**.

        ![Zrzut ekranu przedstawiający okno dialogowe dostawcy tożsamości z wyróżnioną pozycją nowe](./media/servicenow-tutorial/ic7694977.png "Konfigurowanie logowania jednokrotnego")

    1. W oknie dialogowym **dostawcy tożsamości** wybierz pozycję **SAML**.

        ![Zrzut ekranu przedstawiający okno dialogowe dostawcy tożsamości z wyróżnionym elementem SAML](./media/servicenow-tutorial/ic7694978.png "Konfigurowanie logowania jednokrotnego")

    1. W obszarze **Importuj metadane dostawcy tożsamości** wykonaj następujące czynności:

        ![Zrzut ekranu przedstawiający Importowanie metadanych dostawcy tożsamości z wyróżnionymi adresami URL i importowanymi](./media/servicenow-tutorial/idp.png "Konfigurowanie logowania jednokrotnego")

        1. Wprowadź **adres URL metadanych federacji aplikacji** , który został skopiowany z Azure Portal.

        1. Wybierz pozycję **Importuj**.

    1. Odczytuje adres URL metadanych dostawcy tożsamości i wypełnia wszystkie informacje o polach.

        ![Zrzut ekranu dostawcy tożsamości](./media/servicenow-tutorial/ic7694982.png "Konfigurowanie logowania jednokrotnego")

        a. W obszarze **Nazwa** wprowadź nazwę konfiguracji (na przykład **Microsoft Azure federacyjne Logowanie jednokrotne**).

        b. Skopiuj wartość **strony głównej usługi ServiceNow** . Wklej go w **adresie URL logowania** w sekcji **Podstawowa konfiguracja SAML usługi ServiceNow** Azure Portal.

        > [!NOTE]
        > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        c. Skopiuj wartość **Identyfikator jednostki/wystawcy** . Wklej ją w polu **Identyfikator** w sekcji **usługi SERVICENOW podstawowe konfigurowanie SAML** w Azure Portal.

        d. Upewnij się, że **zasady NameID** są ustawione na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` wartość.

        e. Wybierz pozycję **Zaawansowane**. W **polu Użytkownik** wprowadź **adres e-mail**.

        > [!NOTE]
        > Usługę Azure AD można skonfigurować do emisji identyfikatora użytkownika usługi Azure AD (głównej nazwy użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML. W tym celu należy przejść do sekcji **usługi ServiceNow**  >  **atrybutów**  >  **logowania** jednokrotnego w Azure Portal i mapowania żądanego pola na atrybut **NameIdentifier** . Wartość przechowywana dla wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością przechowywaną w usługi ServiceNow dla wprowadzonego pola (na przykład user_name).

        przykład Wybierz pozycję **Testuj połączenie** w prawym górnym rogu strony.

        > [!NOTE]
        > Jeśli połączenie testowe kończy się niepowodzeniem i nie można aktywować tego połączenia, usługi ServiceNow oferuje przełącznik przesłonięcia. Musisz wprowadzić **Sys_properties. Lista** w obszarze **nawigacji wyszukiwania** i zostanie otwarta nowa strona właściwości systemu. W tym miejscu trzeba utworzyć nową właściwość o nazwie **Glide. Authenticate. multisso. test. Connection. obowiązkowy** z **typem danych** jako **true/false** , a następnie ustawić **wartość** **false**.

          > ![Zrzut ekranu przedstawiający Test connection](./media/servicenow-tutorial/test-connection-fail.png "Konfigurowanie logowania jednokrotnego")

        h. Po wyświetleniu monitu o podanie poświadczeń wprowadź je. Zostanie wyświetlona następująca strona. Jest oczekiwany błąd **wyniki testów wylogowywania z logowaniem jednokrotnym** . Zignoruj błąd i wybierz pozycję  **Aktywuj**.

          ![uwierzytelniające](./media/servicenow-tutorial/servicenow-activate.png "Konfigurowanie logowania jednokrotnego")

### <a name="create-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w usługi ServiceNow. Usługi ServiceNow obsługuje automatyczne Inicjowanie obsługi użytkowników, która jest domyślnie włączona.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurowanie logowania jednokrotnego usługi ServiceNow Express

1. Zaloguj się do swojej aplikacji ServiceNow Express jako administrator.

2. W lewym okienku wybierz pozycję **Logowanie jednokrotne**.

    ![Zrzut ekranu aplikacji usługi ServiceNow Express z wyróżnionym pojedynczym Sign-On](./media/servicenow-tutorial/ic7694980ex.png "Konfiguruj adres URL aplikacji")

3. W oknie dialogowym **Logowanie** jednokrotne wybierz ikonę konfiguracji w prawym górnym rogu, a następnie ustaw następujące właściwości:

    ![Zrzut ekranu przedstawiający okno dialogowe z pojedynczym Sign-On](./media/servicenow-tutorial/ic7694981ex.png "Konfiguruj adres URL aplikacji")

    a. Przestaw przełącznik **Enable multiple provider SSO** (Włącz logowanie jednokrotne u wielu dostawców) w prawo.

    b. Przestaw przełącznik **Enable debug logging for the multiple provider SSO integration** (Włącz rejestrowanie debugowania dla integracji logowania jednokrotnego u wielu dostawców) w prawo.

    c. W **polu tabeli User (użytkownik**) wpisz **user_name**.

4. W oknie dialogowym **Logowanie** jednokrotne wybierz pozycję **Dodaj nowy certyfikat**.

    ![Zrzut ekranu okna dialogowego pojedyncze Sign-On z wyróżnionym przyciskiem Dodaj nowy certyfikat](./media/servicenow-tutorial/ic7694973ex.png "Konfigurowanie logowania jednokrotnego")

5. W oknie dialogowym **certyfikaty X. 509** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe certyfikaty X. 509](./media/servicenow-tutorial/ic7694975.png "Konfigurowanie logowania jednokrotnego")

    a. W obszarze **Nazwa** wprowadź nazwę konfiguracji (na przykład: **testsaml 2.0**).

    b. Wybierz pozycję **Active** (Aktywne).

    c. W obszarze **Format** wybierz opcję **PEM**.

    d. W obszarze **Typ** wybierz pozycję **certyfikat magazynu zaufania**.

    e. Otwórz certyfikat szyfrowanego algorytmem Base64 pobrany z Azure Portal w Notatniku. Skopiuj zawartość IT do schowka, a następnie wklej ją do pola tekstowego **certyfikat PEM** .

    f. Wybierz pozycję **Aktualizuj**

6. W oknie dialogowym **Logowanie** jednokrotne wybierz pozycję **Dodaj nowy dostawcy tożsamości**.

    ![Zrzut ekranu przedstawiający okno dialogowe pojedyncze Sign-On z wyróżnioną pozycją Dodaj nowe dostawcy tożsamości](./media/servicenow-tutorial/ic7694976ex.png "Konfigurowanie logowania jednokrotnego")

7. W oknie dialogowym **Dodawanie nowego dostawcy tożsamości** w obszarze **Konfigurowanie dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości](./media/servicenow-tutorial/ic7694982ex.png "Konfigurowanie logowania jednokrotnego")

    a. W obszarze **Nazwa** wprowadź nazwę konfiguracji (na przykład: **SAML 2,0**).

    b. W polu **adres URL dostawcy tożsamości** wklej wartość identyfikatora dostawcy tożsamości skopiowanego z Azure Portal.

    c. W przypadku **AuthnRequest dostawcy tożsamości** wklej wartość adresu URL żądania uwierzytelniania skopiowanego z Azure Portal.

    d. W przypadku **SingleLogoutRequest dostawcy tożsamości** wklej wartość adresu URL wylogowywania skopiowanego z Azure Portal.

    e. W przypadku **certyfikatu dostawcy tożsamości** wybierz certyfikat utworzony w poprzednim kroku.

8. Wybierz pozycję **Ustawienia zaawansowane**. W obszarze **dodatkowe właściwości dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości z wyróżnionymi ustawieniami zaawansowanymi](./media/servicenow-tutorial/ic7694983ex.png "Konfigurowanie logowania jednokrotnego")

    a. Dla **powiązania protokołu dla SINGLELOGOUTREQUEST dostawcy tożsamości**, wprowadź **nazwę urn: języka Oasis: names: TC: SAML: 2.0: bindings: http-redirect**.

    b. W przypadku **zasad NameID** wprowadź **nazwę urn: języka Oasis: names: TC: SAML: 1.1: NameID-format: nieokreślone**.

    c. W przypadku **metody AuthnContextClassRef** wprowadź `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` .

    d. W przypadku **tworzenia elementu AuthnContextClass** Przełącz go na wyłączony (niezaznaczony).

9. W obszarze **Additional Service Provider Properties** (Dodatkowe właściwości dostawcy usług) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości z wyróżnionymi różnymi właściwościami](./media/servicenow-tutorial/ic7694984ex.png "Konfigurowanie logowania jednokrotnego")

    a. Dla **strony głównej usługi ServiceNow** wprowadź adres URL strony głównej wystąpienia usługi usługi ServiceNow.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład: `https://fabrikam.service-now.com/navpage.do`).

    b. Dla **identyfikatora jednostki/wystawcy** wprowadź adres URL dzierżawy usługi usługi ServiceNow.

    c. W polu **Identyfikator URI odbiorców** wprowadź adres URL dzierżawy usługi usługi ServiceNow.

    d. W przypadku **przesunięcia zegara** wprowadź **60**.

    e. W **polu Użytkownik** wprowadź **adres e-mail**.

    > [!NOTE]
    > Usługę Azure AD można skonfigurować do emisji identyfikatora użytkownika usługi Azure AD (głównej nazwy użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML. W tym celu należy przejść do sekcji **usługi ServiceNow**  >  **atrybutów**  >  **logowania** jednokrotnego w Azure Portal i mapowania żądanego pola na atrybut **NameIdentifier** . Wartość przechowywana dla wybranego atrybutu w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością przechowywaną w usługi ServiceNow dla wprowadzonego pola (na przykład user_name).

    f. Wybierz pozycję **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka usługi ServiceNow w panelu dostępu należy automatycznie zalogować się do usługi ServiceNow, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testowanie logowania jednokrotnego dla usługi ServiceNow klasyczny (Mobile)

1. Otwórz aplikację **klasyczną usługi ServiceNow (Mobile)** i wykonaj następujące czynności:

    a. Wybierz znak plus w prawym dolnym rogu.

    ![Zrzut ekranu aplikacji klasycznej usługi ServiceNow z wyróżnionym znakiem plus](./media/servicenow-tutorial/test-03.png)

    b. Wprowadź nazwę wystąpienia usługi ServiceNow, a następnie wybierz pozycję **Kontynuuj**.

    ![Zrzut ekranu przedstawiający stronę Dodawanie wystąpienia z wyróżnioną pozycją Kontynuuj](./media/servicenow-tutorial/test-04.png)

    c. Na stronie **Logowanie** wykonaj następujące czynności:

    ![Zrzut ekranu strony logowania z wyróżnionym użyciem logowania zewnętrznego](./media/servicenow-tutorial/test-01.png)

    *  Wprowadź **nazwę użytkownika**, na przykład B.simon@contoso.com .

    *  Wybierz pozycję **Użyj logowania zewnętrznego**. Nastąpi przekierowanie do strony usługi Azure AD w celu zalogowania się.

    *  Wprowadź poświadczenia. Jeśli istnieje jakiekolwiek uwierzytelnianie innej firmy lub jakakolwiek inna funkcja zabezpieczeń włączona, użytkownik musi odpowiednio odpowiedzieć. Zostanie wyświetlona **Strona główna** aplikacji.

        ![Zrzut ekranu przedstawiający stronę główną aplikacji](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi ServiceNow można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

