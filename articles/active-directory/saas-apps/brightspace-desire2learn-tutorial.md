---
title: 'Samouczek: integracja Azure Active Directory z Brightspace przez Desire2Learn | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Brightspace by Desire2Learn.
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
ms.openlocfilehash: 61c4125038ea0c64ff8489a25f9c63c5cbb82a21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583823"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Samouczek: integracja Azure Active Directory z usługą Brightspace przez Desire2Learn

W tym samouczku dowiesz się, jak zintegrować usługę Brightspace przez Desire2Learn z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Brightspace przez Desire2Learn z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Brightspace przez Desire2Learn.
* Zezwól użytkownikom na automatyczne logowanie do Brightspace przez Desire2Learn przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Brightspace przez Desire2Learn subskrypcję logowania jednokrotnego.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Brightspace przez Desire2Learn obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne.

## <a name="add-brightspace-by-desire2learn-from-the-gallery"></a>Dodawanie Brightspace przez Desire2Learn z galerii

Aby skonfigurować integrację aplikacji Brightspace by Desire2Learn z usługą Azure AD, należy dodać aplikację Brightspace by Desire2Learn z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Brightspace by Desire2Learn** w polu wyszukiwania.
1. Wybierz pozycję **Brightspace by Desire2Learn** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-brightspace-by-desire2learn"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Brightspace przez Desire2Learn

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Brightspace przez Desire2Learn przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Brightspace przez Desire2Learn.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Brightspace przez Desire2Learn, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę Brightspace przez Desire2Learn Logowanie jednokrotne](#configure-brightspace-by-desire2learn-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Brightspace przez Desire2Learn użytkownika testowego](#create-brightspace-by-desire2learn-test-user)** , aby dysponować odpowiednikiem B. Simon w Brightspace przez Desire2Learn, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Brightspace przez Desire2Learn** integrację aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz jeden z adresów URL, korzystając z następujących wzorców:

    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Brightspace by Desire2Learn](https://www.d2l.com/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Brightspace by Desire2Learn** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Brightspace przez Desire2Learn.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Brightspace by Desire2Learn**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-brightspace-by-desire2learn-sso"></a>Konfigurowanie Brightspace przez Desire2Learn Logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Brightspace by Desire2Learn**, musisz wysłać [zespołowi pomocy technicznej aplikacji Brightspace by Desire2Learn](https://www.d2l.com/contact/) pobrany **kod XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal. Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Tworzenie użytkownika testowego aplikacji Brightspace by Desire2Learn

W tej sekcji utworzysz w aplikacji Brightspace by Desire2Learn użytkownika o nazwie Britta Simon. Współpracuj z [Brightspaceą przez zespół pomocy technicznej Desire2Learn](https://www.d2l.com/contact/) , aby dodać użytkowników w Brightspace przez platformę Desire2Learn. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników aplikacji Brightspace by Desire2Learn oferowanych przez tę aplikację.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do Brightspace przez Desire2Learn, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Brightspace by Desire2Learn w obszarze Moje aplikacje należy automatycznie zalogować się do Brightspace za pomocą Desire2Learn, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Brightspace przez Desire2Learn można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
