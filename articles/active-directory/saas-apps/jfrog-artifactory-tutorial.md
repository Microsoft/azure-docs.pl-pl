---
title: 'Samouczek: integracja Azure Active Directory z usługą JFrog Artifactory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648949"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Samouczek: Integrowanie JFrog Artifactory z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę JFrog Artifactory z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi JFrog Artifactory z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do JFrog Artifactory.
* Zezwól użytkownikom na automatyczne logowanie do JFrog Artifactory przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, JFrog Artifactory).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* JFrog Artifactory obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości** .
* JFrog Artifactory obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Dodaj JFrog Artifactory z galerii

Aby skonfigurować integrację usługi JFrog Artifactory w usłudze Azure AD, musisz dodać Artifactory JFrog z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **JFrog Artifactory** w polu wyszukiwania.
1. Wybierz pozycję **JFrog Artifactory** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla JFrog Artifactory

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą JFrog Artifactory przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w JFrog Artifactory.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą JFrog Artifactory, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze JFrog Artifactory](#configure-jfrog-artifactory-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu JFrog Artifactory](#create-jfrog-artifactory-test-user)** , aby uzyskać odpowiednika B. Simon w JFrog Artifactory, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **JFrog Artifactory** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<servername>.jfrog.io`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    - Dla Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Dla Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    - Dla Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Dla Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta JFrog Artifactory](https://support.jfrog.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja JFrog Artifactory oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe atrybuty użytkownika.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z kontrolką edycji o nazwie.](common/edit-attribute.png)

1. Oprócz powyższych JFrog Artifactory oczekuje szeregu dodatkowych atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **atrybuty użytkownika & oświadczenia** w oknie dialogowym **oświadczenia grupy (wersja zapoznawcza)** wykonaj następujące czynności:

    a. Kliknij **pióro** obok **grup zwróconych w ramach żądania**.

    ![Zrzut ekranu przedstawia atrybuty użytkownika & oświadczenia z wybraną ikoną Edytuj.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Zrzut ekranu przedstawia sekcję oświadczenia grupy ze wszystkimi wybranymi grupami.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Wybierz pozycję **wszystkie grupy** z listy radiowej.

    c. Kliknij pozycję **Zapisz**.

4. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Zlokalizuj **certyfikat (base64)** , a następnie wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Skonfiguruj Artifactory (nazwę dostawcy usług SAML) przy użyciu pola "Identyfikator" (zobacz krok 4). W sekcji **Konfigurowanie programu JFrog Artifactory** skopiuj odpowiednie adresy URL na podstawie wymagań.

   - Dla Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Dla Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do JFrog Artifactory.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **JFrog Artifactory**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-jfrog-artifactory-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze JFrog Artifactory

Aby skonfigurować Logowanie jednokrotne na stronie **JFrog Artifactory** , musisz wysłać pobrany **certyfikat (RAW)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej JFrog Artifactory](https://support.jfrog.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-jfrog-artifactory-test-user"></a>Utwórz użytkownika testowego JFrog Artifactory

W tej sekcji użytkownik o nazwie B. Simon został utworzony w JFrog Artifactory. JFrog Artifactory obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie JFrog Artifactory, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL JFrog Artifactory logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania JFrog Artifactory i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do JFrog Artifactory, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka JFrog Artifactory w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do JFrog Artifactory, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu JFrog Artifactory można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).