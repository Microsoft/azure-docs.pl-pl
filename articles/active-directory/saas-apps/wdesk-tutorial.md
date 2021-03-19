---
title: 'Samouczek: integracja Azure Active Directory z usługą Wdesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603274"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Wdesk

W tym samouczku dowiesz się, jak zintegrować usługę Wdesk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Wdesk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Wdesk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Wdesk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Wdesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Wdesk obsługuje funkcję SSO zainicjowaną przez usługę **SP** i **dostawcy tożsamości** .

## <a name="add-wdesk-from-the-gallery"></a>Dodaj Wdesk z galerii

Aby skonfigurować integrację programu Wdesk z usługą Azure AD, musisz dodać Wdesk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Wdesk** w polu wyszukiwania.
1. Wybierz pozycję **Wdesk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Wdesk

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Wdesk na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Wdesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Wdesk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-wdesk-sso)** w usłudze Wdesk, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Wdesk](#create-wdesk-test-user)** , aby dysponować odpowiednikiem B. Simon w Wdesk, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Wdesk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są uzyskiwane z portalu WDesk podczas konfigurowania logowania jednokrotnego.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie Wdesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Wdesk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Wdesk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-wdesk-sso"></a>Konfigurowanie logowania jednokrotnego Wdesk

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Wdesk jako administrator zabezpieczeń.

1. W lewym dolnym rogu kliknij pozycję **administrator** i wybierz pozycję **administrator konta**:
 
    ![Zrzut ekranu przedstawia administratora konta wybranego z menu administratora.](./media/wdesk-tutorial/account.png)

1. W obszarze Administrator Wdesk przejdź do obszarze **zabezpieczenia**, a następnie pozycję **SAML**  >  **SAML Settings**:

    ![Zrzut ekranu przedstawia ustawienia protokołu SAML wybrane z karty SAML.](./media/wdesk-tutorial/settings.png)

1. W obszarze **ustawienia identyfikatora użytkownika SAML** Sprawdź, czy **Identyfikator użytkownika SAML to Wdesk username**.

    ![Zrzut ekranu przedstawia ustawienia użytkownika protokołu SAML I D, w których można wybrać użytkownika SAML, I D jest nazwą użytkownika W usłudze.](./media/wdesk-tutorial/wdesk-username.png)

4. W obszarze **Ustawienia ogólne** zaznacz pole wyboru **Włącz logowanie jednokrotne SAML**:

    ![Zrzut ekranu przedstawia Edytowanie ustawień SAML, w których można wybrać opcję Włącz logowanie jednokrotne SAML.](./media/wdesk-tutorial/user-settings.png)

5. W obszarze **szczegóły dostawcy usług** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia szczegóły dostawcy usług, w którym można wprowadzić podane wartości.](./media/wdesk-tutorial/service-provider.png)

    1. Skopiuj **adres URL logowania** i wklej go w polu tekstowym **adres url logowania** na Azure Portal.

    1. Skopiuj **adres URL metadanych** i wklej go w polu tekstowym **identyfikatora** na Azure Portal.

    1. Skopiuj **adres URL klienta** i wklej go w polu tekstowym **adres url odpowiedzi** na Azure Portal.

    1. Kliknij przycisk **Zapisz** na Azure Portal, aby zapisać zmiany.      

1. Kliknij pozycję **Konfiguruj ustawienia dostawcy tożsamości** , aby otworzyć okno dialogowe **Edytowanie ustawień dostawcy tożsamości** . Kliknij pozycję **Wybierz plik** , aby zlokalizować plik **Metadata.xml** zapisany z Azure Portal, a następnie Przekaż go.
    
    ![Zrzut ekranu przedstawia ustawienia w obszarze Edycja I d P, w których można przekazać metadane.](./media/wdesk-tutorial/metadata.png)
  
1. Kliknij przycisk **Zapisz zmiany**.

    ![Zrzut ekranu przedstawia przycisk Zapisz zmiany.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Utwórz użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Wdesk, muszą one być obsługiwane w usłudze Wdesk. W Wdesk, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Wdesk jako administrator zabezpieczeń.

2. Przejdź do   >  **administratora konta** administratora.

     ![Zrzut ekranu przedstawia administratora konta wybranego z menu administratora.](./media/wdesk-tutorial/account.png)

3. Kliknij pozycję **Członkowie** w obszarze **osoby**.

4. Teraz kliknij przycisk **Dodaj członka** , aby otworzyć okno dialogowe **Dodawanie elementu członkowskiego** . 
   
    ![Zrzut ekranu przedstawia kartę Członkowie, w której można wybrać opcję Dodaj członka.](./media/wdesk-tutorial/create-user-1.png)  

5. W polu tekstowym **użytkownik** wprowadź nazwę użytkownika, jak b.simon@contoso.com i kliknij przycisk **Kontynuuj** .

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie elementu członkowskiego, w którym można wprowadzić użytkownika.](./media/wdesk-tutorial/create-user-3.png)

6.  Wprowadź szczegóły, jak pokazano poniżej:
  
    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie elementu członkowskiego, w którym można dodać podstawowe informacje dla użytkownika.](./media/wdesk-tutorial/create-user-4.png)
 
    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np b.simon@contoso.com .

    b. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    c. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

7. Kliknij przycisk **Zapisz element członkowski** .  

    ![Zrzut ekranu przedstawia przycisk Wyślij wiadomość e-mail powitalnego za pomocą przycisku Zapisz członka.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Wdesk, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania Wdesk i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Wdesk, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Wdesk w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Wdesk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Wdesk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
