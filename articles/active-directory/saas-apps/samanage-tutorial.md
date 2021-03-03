---
title: 'Samouczek: integracja Azure Active Directory z obsługą techniczną SolarWinds (wcześniej Samanage) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SolarWinds Service Desk (wcześniej Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 96118a840c57932ae8e99b084766bf12493d9f7a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652735"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Samouczek: integracja Azure Active Directory z obsługą techniczną SolarWinds (wcześniej Samanage)

W tym samouczku dowiesz się, jak zintegrować usługę SolarWinds z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SolarWinds z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SolarWinds.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SolarWinds przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SolarWinds.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SolarWinds obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="add-solarwinds-from-the-gallery"></a>Dodaj SolarWinds z galerii

Aby skonfigurować integrację programu SolarWinds z usługą Azure AD, musisz dodać SolarWinds z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SolarWinds** w polu wyszukiwania.
1. Wybierz pozycję **SolarWinds** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SolarWinds

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SolarWinds.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SolarWinds, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-solarwinds-sso)** w usłudze SolarWinds, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SolarWinds](#create-solarwinds-test-user)** , aby dysponować odpowiednikiem B. Simon w SolarWinds, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SolarWinds** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej klienta usługi Samanage](https://www.samanage.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie SolarWinds** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SolarWinds.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SolarWinds**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Konfigurowanie logowania jednokrotnego SolarWinds

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny SolarWinds jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Pulpit nawigacyjny](./media/samanage-tutorial/tutorial-samanage-1.png "Pulpit nawigacyjny")

3. Kliknij pozycję **Single Sign-On** (Logowanie jednokrotne).
   
    ![Logowanie jednokrotne](./media/samanage-tutorial/tutorial-samanage-2.png "Logowanie jednokrotne")

4. Przejdź do sekcji **Login using SAML** (Logowanie za pośrednictwem protokołu SAML) i wykonaj następujące kroki:
   
    ![Logowanie przy użyciu protokołu SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Logowanie przy użyciu protokołu SAML")
 
    a. Kliknij pozycję **Enable Single Sign-On with SAML** (Włącz logowanie jednokrotne z użyciem protokołu SAML).  
 
    b. W polu tekstowym **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.    
 
    c. Upewnij się, że wartość pola **Login URL** (Adres URL logowania) odpowiada **adresowi URL logowania** z sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
 
    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wprowadź wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.
 
    e. W polu tekstowym **SAML Issuer** (Wystawca SAML) wpisz identyfikator URI aplikacji ustawiony u dostawcy tożsamości.
 
    f. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **Paste your Identity Provider x.509 Certificate below** (Wklej poniżej swój certyfikat x.509 dostawcy tożsamości).
 
    przykład Kliknij pozycję **Utwórz użytkowników, jeśli nie istnieją w SolarWinds**.
 
    h. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-solarwinds-test-user"></a>Utwórz użytkownika testowego SolarWinds

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze SolarWinds, muszą one być obsługiwane w usłudze SolarWinds.  
W przypadku SolarWinds, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny SolarWinds jako administrator.

2. Kliknij pozycję **Dashboard** (Pulpit nawigacyjny) i wybierz pozycję **Setup** (Konfigurowanie) w lewym okienku nawigacji.
   
    ![Instalacja](./media/samanage-tutorial/tutorial-samanage-1.png "Konfigurowanie")

3. Kliknij kartę **Użytkownicy** .
   
    ![Użytkownicy](./media/samanage-tutorial/tutorial-samanage-6.png "Użytkownicy")

4. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Nowy użytkownik](./media/samanage-tutorial/tutorial-samanage-7.png "Nowy użytkownik")

5. W polach **Name** (Nazwa) i **Email Address** (Adres e-mail) wpisz nazwę i adres e-mail konta usługi Azure Active Directory, które chcesz aprowizować, a następnie kliknij przycisk **Create User** (Utwórz użytkownika).
   
    ![Tworzenie użytkownika](./media/samanage-tutorial/tutorial-samanage-8.png "Utwórz użytkownika")
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne. Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program SolarWinds.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania SolarWinds, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania SolarWinds i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka SolarWinds w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania SolarWinds. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SolarWinds można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).