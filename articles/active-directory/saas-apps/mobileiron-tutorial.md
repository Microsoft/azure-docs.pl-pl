---
title: 'Samouczek: integracja Azure Active Directory z usługą MobileIron | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653062"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Samouczek: integracja Azure Active Directory z usługą MobileIron

 W tym samouczku dowiesz się, jak zintegrować usługę MobileIron z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi MobileIron z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do MobileIron.
* Zezwól użytkownikom na automatyczne logowanie się do usługi MobileIron przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) MobileIron.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa MobileIron obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="add-mobileiron-from-the-gallery"></a>Dodaj MobileIron z galerii

Aby skonfigurować integrację aplikacji MobileIron z usługą Azure AD, musisz dodać aplikację MobileIron z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MobileIron** w polu wyszukiwania.
1. Wybierz pozycję **MobileIron** z wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla MobileIron

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą MobileIron, używając użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w MobileIron.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą MobileIron, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
     1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-mobileiron-sso)** w usłudze MobileIron, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji MobileIron](#create-mobileiron-test-user)** — aby w aplikacji MobileIron utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.
 
1. W Azure Portal na stronie integracja aplikacji **MobileIron** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://www.MobileIron.com/<key>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Wartości klucza i hosta uzyskasz z portalu administracyjnego aplikacji MobileIron, co zostało wyjaśnione w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie wpisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi MobileIron.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **MobileIron**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-mobileiron-sso"></a>Konfigurowanie logowania jednokrotnego MobileIron

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji MobileIron jako administrator.

2. Przejdź do pozycji **Administracja**  >  **tożsamość** i wybierz opcję **AAD** w polu **informacje na stronie Konfiguracja dostawcy tożsamości w chmurze** .

    ![Zrzut ekranu przedstawia kartę administrator witryny MobileIron z wybraną tożsamością.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Skopiuj wartości **klucza** i **hosta** oraz wklej je, aby ukończyć adresy URL w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Zrzut ekranu przedstawia konfigurację opcji SAML z wartością klucza i hosta.](./media/MobileIron-tutorial/key.png)

4. W pozycji **Eksportowanie pliku metadanych z usługi AAD i importowanie go do pola chmury aplikacji MobileIron** kliknij pozycję **Wybierz plik**, aby przekazać metadane pobrane z witryny Azure Portal. Po zakończeniu przekazywania kliknij przycisk **Gotowe**.

    ![Konfigurowanie przycisku metadanych administratora logowania jednokrotnego](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Tworzenie użytkownika testowego aplikacji MobileIron

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji MobileIron, należy aprowizować ich w aplikacji MobileIron.  
W przypadku aplikacji MobileIron aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji MobileIron jako administrator.

1. Przejdź do pozycji **Użytkownicy** i kliknij pozycję **Dodaj** > **Pojedynczy użytkownik**.

    ![Konfigurowanie przycisku użytkownika logowania jednokrotnego](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Na stronie okna dialogowego **„Pojedynczy użytkownik”** wykonaj następujące kroki:

    ![Konfigurowanie przycisku dodawania użytkownika logowania jednokrotnego](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. W polu tekstowym **adres e-mail** Wprowadź wiadomość e-mail użytkownika brittasimon@contoso.com .

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. Britta.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. Simon.

    d. Kliknij przycisk **Gotowe**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania MobileIron, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania MobileIron i zainicjuj w nim przepływ logowania.

### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do MobileIron, dla którego skonfigurowano Logowanie jednokrotne.

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka MobileIron w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do MobileIron, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu MobileIron można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
