---
title: 'Samouczek: integracja Azure Active Directory ze znakiem Adobe | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649976"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Samouczek: integracja Azure Active Directory ze znakiem Adobe

W tym samouczku dowiesz się, jak zintegrować usługę Adobe Sign z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Adobe Sign z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Adobe Sign.
* Zezwól użytkownikom na automatyczne logowanie do usługi Adobe Sign przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) firmy Adobe.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Sign obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-adobe-sign-from-the-gallery"></a>Dodawanie programu Adobe Sign z galerii

Aby skonfigurować integrację rozwiązania Adobe Sign z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz ciąg **Adobe Sign** w polu wyszukiwania.
1. Wybierz pozycję **Adobe Sign** from Results, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Adobe Sign

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem Adobe Sign, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania Adobe Sign.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu programu Adobe Sign, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj funkcję rejestracji jednokrotnej](#configure-adobe-sign-sso)** dla programu Adobe, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego w rozwiązaniu Adobe Sign](#create-adobe-sign-test-user)** — aby w aplikacji Adobe Sign utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w rozwiązaniu Adobe Sign, wykonaj następujące czynności:

1. W Azure Portal na stronie **Adobe Sign** Application Integration wybierz pozycję **Logowanie jednokrotne**.

1. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę ołówka, aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Adobe Sign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Adobe Sign.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Adobe Sign**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-adobe-sign-sso"></a>Skonfiguruj Adobe Sign Logowanie jednokrotne

1. Przed rozpoczęciem konfiguracji skontaktuj się z [zespołem pomocy technicznej programu Adobe Sign](https://helpx.adobe.com/in/contact/support.html) , aby dodać swoją domenę na liście dozwolonych podpisów Adobe. Poniżej przedstawiono sposób dodawania domeny:

    a. [Zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html) wysyła do Ciebie losowo wygenerowany token. W przypadku Twojej domeny token będzie wyglądał podobnie do następującego: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Opublikuj token weryfikacji w rekordzie tekstu DNS tekstu i powiadom [zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Może to potrwać co najmniej kilka dni. Pamiętaj, że opóźnienia propagacji systemu DNS oznaczają, że wartość opublikowana w systemie DNS może być niewidoczna przez godzinę lub dłużej. Administrator IT powinien mieć odpowiednią wiedzę na temat sposobu publikowania tego tokenu w rekordzie tekstu DNS.

    c. Gdy powiadomisz [zespół pomocy technicznej klienta rozwiązania Adobe Sign](https://helpx.adobe.com/in/contact/support.html) za pośrednictwem biletu pomocy technicznej, po opublikowaniu tokenu członkowie tego zespołu zweryfikują domenę i dodadzą ją do konta.

    d. Mówiąc ogólnie, poniżej przedstawiono sposób publikowania tokenu rekordu DNS:

    * Zaloguj się do konta domeny.
    * Znajdź stronę służącą do aktualizowania rekordów DNS. Ta strona może mieć nazwę Zarządzanie systemem DNS, Zarządzanie serwerem nazw lub Ustawienia zaawansowane.
    * Znajdź rekordy TXT dla domeny.
    * Dodaj rekord TXT z pełną wartością tokenu dostarczony przez firmy Adobe.
    * Zapisz zmiany.

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Adobe Sign jako administrator.

1. W menu SAML wybierz pozycję **Ustawienia konta**  >  **Ustawienia SAML**.

    ![Zrzut ekranu strony ustawień SAML podpisywania programu Adobe](./media/adobe-echosign-tutorial/settings.png "Konto")

1. W sekcji **Ustawienia języka SAML** wykonaj następujące czynności:

    ![Zrzut ekranu, który wyróżnia ustawienia SAML, w tym SAML obowiązkowy.](./media/adobe-echosign-tutorial/saml1.png "Ustawienia SAML")

   ![Zrzut ekranu ustawień protokołu SAML](./media/adobe-echosign-tutorial/saml.png "Ustawienia SAML")

   a. W obszarze **Tryb SAML** wybierz pozycję **SAML — obowiązkowe**.

   b. Wybierz pozycję **Zezwalaj administratorom konta Echosign na logowanie się przy użyciu ich poświadczeń rozwiązania Echosign**.

   c. W obszarze **Tworzenie użytkownika** wybierz pozycję **Automatycznie dodawaj użytkowników uwierzytelnionych przy użyciu języka SAML**.

   d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal w polu tekstowym **Identyfikator jednostki dostawcy tożsamości**.

   e. Wklej **adres URL logowania** skopiowany z witryny Azure Portal w polu tekstowym **Adres URL logowania dostawcy tożsamości**.

   f. Wklej **adres URL wylogowywania** skopiowany z witryny Azure Portal w polu tekstowym **Adres URL wylogowywania dostawcy tożsamości**.

   przykład Otwórz pobrany plik **Certificate(Base64)** w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym **Certyfikat dostawcy tożsamości**.

   h. Wybierz pozycję **Zapisz zmiany**.

### <a name="create-adobe-sign-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Sign

Aby umożliwić użytkownikom usługi Azure AD logowanie się w rozwiązaniu Adobe Sign, należy ich aprowizować w tym rozwiązaniu. Jest to zadanie wykonywane ręcznie.

>[!NOTE]
>Aby aprowizować konta użytkowników usługi Azure AD, można użyć dowolnych innych interfejsów API lub narzędzi tworzenia konta użytkownika aplikacji Adobe Sign dostępnych w tym rozwiązaniu. 

1. Zaloguj się do firmowej witryny aplikacji **Adobe Sign** jako administrator.

2. W menu w górnej części strony wybierz pozycję **Konto**. Następnie w okienku po lewej stronie wybierz pozycję **Użytkownicy & grupy**  >  **Utwórz nowego użytkownika**.

    ![Zrzut ekranu przedstawiający witrynę firmy Adobe Podpisz, z kontami, użytkownikami &grupami i Utwórz nowy użytkownik wyróżniony](./media/adobe-echosign-tutorial/account.png "Konto")

3. W sekcji **Tworzenie nowego użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję Tworzenie nowego użytkownika](./media/adobe-echosign-tutorial/user.png "Utwórz użytkownika")

    a. Wpisz wartości pól **Adres e-mail**, **Imię** i **Nazwisko** prawidłowego konta usługi Azure AD, które chcesz aprowizować, w powiązanych polach tekstowych.

    b. Wybierz pozycję **Utwórz użytkownika**.

>[!NOTE]
>Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne. 

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do konta Adobe, gdzie można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania za pomocą konta Adobe bezpośrednio i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Adobe Sign w moich aplikacjach należy automatycznie zalogować się do konta Adobe, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Adobe Sign można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).