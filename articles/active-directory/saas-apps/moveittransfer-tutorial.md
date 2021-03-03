---
title: 'Samouczek: integracja Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i transfer MOVEit — integracja z usługą Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653068"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: integracja Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD

W tym samouczku dowiesz się, jak zintegrować usługę MOVEit transfer — integrację z usługą Azure AD z usługą Azure Active Directory (Azure AD). W przypadku integracji transferu MOVEit — integracji usługi Azure AD z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do MOVEit transfer — integracja z usługą Azure AD.
* Zezwól użytkownikom na automatyczne logowanie do MOVEit transfer — integracja z usługą Azure AD z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Transfer MOVEit — subskrypcja z włączonym logowaniem jednokrotnym (SSO) integracji usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Transfer MOVEit — integracja z usługą Azure AD obsługuje logowanie jednokrotne w ramach platformy **SP**

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie MOVEit transfer — integracja z usługą Azure AD z galerii

Aby skonfigurować integrację transferu MOVEit — integrację usługi Azure AD z usługą Azure AD, musisz dodać integrację MOVEit-Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MOVEit transfer-Azure AD Integration** w polu wyszukiwania.
1. Wybierz pozycję **transfer MOVEit — integracja z usługą Azure AD** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD do MOVEit transfer — integracja z usługą Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą transferu MOVEit — integracja z usługą Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach transferu MOVEit — integracja z usługą Azure AD.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą transferu MOVEit — integracja z usługą Azure AD, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj transfer MOVEit — logowanie JEDNOkrotne w usłudze Azure AD Integration](#configure-moveit-transfer---azure-ad-integration-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie transferu MOVEit — Użytkownik testowy integracji usługi Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** ma odpowiedni odpowiednik B. Simon w usłudze MOVEit transfer — integracja z usługą Azure AD, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z **usługą MOVEit transfer-Azure AD Integration** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** i **adres URL odpowiedzi** są pobierane automatycznie w sekcji **Podstawowa konfiguracja SAML** :

    ![Transfer MOVEit — informacje dotyczące logowania jednokrotnego w ramach domeny i adresów URL integracji usługi Azure AD](common/sp-identifier-reply.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://contoso.com`

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Contact [MOVEit transfer — zespół obsługi klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support) , aby pobrać wartość. **Plik metadanych dostawcy usług** można pobrać z **adresu URL metadanych dostawcy usług** , który został wyjaśniony w dalszej części artykułu **Konfigurowanie usługi MOVEit transfer — Azure AD Integration logowanie** jednokrotne w samouczku. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi MOVEit transfer — Azure AD Integration** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi MOVEit transfer — integracja z usługą Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **transfer MOVEit — integracja z usługą Azure AD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Konfigurowanie transferu MOVEit — Logowanie jednokrotne w usłudze Azure AD

1. Zaloguj się do dzierżawy transferu MOVEit jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

    ![Sekcja ustawień po stronie aplikacji](./media/moveittransfer-tutorial/settings.png)

3. Kliknij link **Single jednokrotnego** , który znajduje się w obszarze **zasady zabezpieczeń — > uwierzytelnienie użytkownika**.

    ![Zasady zabezpieczeń po stronie aplikacji](./media/moveittransfer-tutorial/sso.png)

4. Kliknij link adresu URL metadanych, aby pobrać dokument metadanych.

    ![Adres URL metadanych dostawcy usług](./media/moveittransfer-tutorial/metadata.png)
    
   * Sprawdź, czy **entityID** dopasowuje **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** .
   * Sprawdź, czy adres URL lokalizacji **AssertionConsumerService** pasuje do **adresu URL odpowiedzi**  w sekcji **Podstawowa konfiguracja protokołu SAML** .
    
     ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/moveittransfer-tutorial/xml.png)

5. Kliknij przycisk **Dodaj dostawcę tożsamości** , aby dodać nowego dostawcy tożsamości federacyjnych.

    ![Dodawanie dostawcy tożsamości](./media/moveittransfer-tutorial/idp.png)

6. Kliknij przycisk **Przeglądaj...** , aby wybrać plik metadanych pobrany z Azure Portal, a następnie kliknij pozycję **Dodaj dostawcę tożsamości** w celu przekazania pobranego pliku.

    ![Dostawca tożsamości SAML](./media/moveittransfer-tutorial/saml.png)

7. Wybierz pozycję "**tak**" jako **włączone** na stronie **Edytowanie ustawień dostawcy tożsamości federacyjnych** , a następnie kliknij przycisk **Zapisz**.

    ![Ustawienia federacyjnego dostawcy tożsamości](./media/moveittransfer-tutorial/save.png)

8. Na stronie **Edytowanie ustawień użytkownika dostawcy tożsamości federacyjnych** wykonaj następujące czynności:
    
    ![Edytowanie ustawień dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/attributes.png)
    
    a. Wybierz pozycję **SAML NameID** jako **nazwę logowania**.
    
    b. Wybierz opcję **inne** jako **pełna nazwa** i w polu tekstowym **nazwa atrybutu** wprowadź wartość: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Wybierz pozycję **inne** jako **wiadomość e-mail** , a w polu tekstowym **nazwa atrybutu** wprowadź wartość: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Wybierz pozycję **tak** jako **AutoCreate Account na jednokrotnego**.
    
    e. Kliknij przycisk **Save** (Zapisz).

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Tworzenie transferu MOVEit — Użytkownik testowy integracji usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w usłudze transfer MOVEit — integracja z usługą Azure AD. MOVEit transfer — integracja z usługą Azure AD obsługuje obsługę just in Time, która została włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu do usługi MOVEit transfer — integracja z usługą Azure AD, jeśli jeszcze nie istnieje.

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej MOVEit transfer — Azure AD Integration Client](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do MOVEit transfer — adres URL logowania do integracji usługi Azure AD, w którym można zainicjować przepływ logowania. 

* Przejdź do MOVEit transfer — adres URL logowania do usługi Azure AD Integration bezpośrednio i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka MOVEit transfer — integracja z usługą Azure AD w obszarze Moje aplikacje należy automatycznie zalogować się do integracji MOVEit transfer-Azure AD, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu MOVEit transfer — integracja z usługą Azure AD można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).