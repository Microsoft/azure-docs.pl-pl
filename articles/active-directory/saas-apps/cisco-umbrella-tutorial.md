---
title: 'Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym administratora Cisco parasol | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Cisco parasol administratora SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592510"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym administratora Cisco parasol

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne administratora Cisco parasol z usługą Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne administratora Cisco parasol z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego administratora Cisco parasol.
* Zezwól użytkownikom na automatyczne logowanie do logowania jednokrotnego administratora Cisco parasol przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Cisco parasol administrator.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne przez administratora Cisco parasol obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego administratora Cisco parasol z galerii

Aby skonfigurować integrację logowania jednokrotnego dla administratora Cisco parasol z usługą Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS aplikację Cisco parasol administrator (SSO) z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Cisco parasol administrator SSO** w polu wyszukiwania.
1. Wybierz pozycję **Cisco parasol administrator Logowanie jednokrotne** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego administratora Cisco

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego administratora Cisco parasol przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Cisco parasol administrator SSO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego administratora Cisco, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Cisco parasol administrator logowanie](#configure-cisco-umbrella-admin-sso-sso)** jednokrotne — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego logowania jednokrotnego dla administratora Cisco parasol](#create-cisco-umbrella-admin-sso-test-user)** -w celu posiadania odpowiedników B. Simon w usłudze Cisco parasol administrator SSO, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SSO programu Cisco parasol administrator** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    a. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

    b. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    c. W polu tekstowym **adres URL logowania** wpisz adres URL: `https://login.umbrella.com/sso`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie programu Cisco parasol administrator SSO** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do logowania jednokrotnego administratora Cisco parasol.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Cisco parasol administrator Logowanie jednokrotne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Konfigurowanie administratora Cisco parasol administrator Logowanie jednokrotne

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Cisco parasol administrator SSO jako administrator.

2. W menu po lewej stronie kliknij pozycję **Administrator** i przejdź do obszaru **Uwierzytelnianie**, a następnie kliknij pozycję **SAML**.

    ![Administrator](./media/cisco-umbrella-tutorial/admin.png)

3. Wybierz pozycję **Inne** i kliknij przycisk **DALEJ**.

    ![Inne](./media/cisco-umbrella-tutorial/other.png)

4. Na stronie **metadanych rejestracji jednokrotnej administratora Cisco parasol** kliknij przycisk **dalej**.

    ![Metadane](./media/cisco-umbrella-tutorial/metadata.png)

5. Na karcie **Przekazywanie metadanych**, jeśli wstępnie skonfigurowano język SAML, wybierz opcję **Kliknij tutaj, aby je zmienić** i wykonaj poniższe kroki.

    ![Dalej](./media/cisco-umbrella-tutorial/next.png)

6. W **opcji A: przekazywanie pliku** XML, Przekaż plik **XML metadanych federacji** pobrany z Azure Portal i po przekazaniu metadanych poniższe wartości są automatycznie wypełniane, a następnie kliknij przycisk **dalej**.

    ![Wybieranie pliku](./media/cisco-umbrella-tutorial/choose-file.png)

7. W sekcji **Walidowanie konfiguracji języka SAML** kliknij pozycję **TESTUJ KONFIGURACJĘ JĘZYKA SAML**.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Kliknij przycisk **Zapisz**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Utwórz użytkownika testowego logowania jednokrotnego administratora Cisco parasol

Aby umożliwić użytkownikom usługi Azure AD logowanie się do logowania jednokrotnego administratora Cisco, muszą one być obsługiwane w usłudze Cisco parasol administrator SSO.  
W przypadku logowania jednokrotnego administratora Cisco parasol Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Cisco parasol administrator SSO jako administrator.

2. W menu po lewej stronie kliknij pozycję **Administrator** i przejdź do pozycji **Konta**.

    ![Konta](./media/cisco-umbrella-tutorial/account.png)

3. Na stronie **Konta** kliknij przycisk **Dodaj** w prawej górnej części strony i wykonaj poniższe kroki.

    ![Użytkownik](./media/cisco-umbrella-tutorial/create-user.png)

    a. W polu **Imię** wprowadź imię, takie jak **Britta**.

    b. W polu **Nazwisko** wprowadź nazwisko, takie jak **simon**.

    c. Z listy **Wybierz rolę administratora delegowanego** wybierz swoją rolę.

    d. W polu **adres e-mail** wprowadź EmailAddress użytkownika, na przykład **brittasimon \@ contoso.com**.

    e. W polu **Hasło** wprowadź hasło.

    f. W polu **Potwierdź hasło** ponownie wprowadź hasło.

    przykład Kliknij przycisk **Utwórz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania jednokrotnego administratora Cisco parasol, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania jednokrotnego administratora Cisco parasol i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal, a użytkownik powinien być automatycznie zalogowany do logowania jednokrotnego administratora Cisco parasol, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka rejestracji jednokrotnej administratora Cisco parasol w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do logowania jednokrotnego administratora Cisco, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego dla administratora Cisco parasol można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).