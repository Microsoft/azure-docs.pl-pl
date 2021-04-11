---
title: 'Samouczek: integracja Azure Active Directory z usługą LaunchDarkly | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954806"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Samouczek: integracja Azure Active Directory z usługą LaunchDarkly

W tym samouczku dowiesz się, jak zintegrować usługę LaunchDarkly z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi LaunchDarkly z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do LaunchDarkly.
* Zezwól użytkownikom na automatyczne logowanie się do usługi LaunchDarkly przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

    > [!NOTE]
    > Integracja z LaunchDarkly Azure Active Directory jest jednokierunkowa. Po skonfigurowaniu integracji można używać usługi Azure AD do zarządzania użytkownikami, logowaniem jednokrotnym i kontami w usłudze LaunchDarkly, ale **nie** można używać LaunchDarkly do zarządzania użytkownikami, logowaniem jednokrotnym i kontami na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w LaunchDarkly.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa LaunchDarkly obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.
* LaunchDarkly obsługuje Inicjowanie obsługi użytkowników **just in Time** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-launchdarkly-from-the-gallery"></a>Dodaj LaunchDarkly z galerii

Aby skonfigurować integrację platformy LaunchDarkly z usługą Azure AD, musisz dodać platformę LaunchDarkly z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **LaunchDarkly** w polu wyszukiwania.
1. Wybierz pozycję **LaunchDarkly** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla LaunchDarkly

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą LaunchDarkly przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w LaunchDarkly.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą LaunchDarkly, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-launchdarkly-sso)** w usłudze LaunchDarkly, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego LaunchDarkly](#create-launchdarkly-test-user)** , aby dysponować odpowiednikiem B. Simon w LaunchDarkly, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **LaunchDarkly** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `app.launchdarkly.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Ta wartość zostanie zaktualizowana przy użyciu rzeczywistego adresu URL odpowiedzi, co objaśniono w dalszej części tego samouczka. Jeśli zamierzasz używać aplikacji w trybie **dostawcy tożsamości**, pozostaw pole **Adres URL logowania** puste. W przeciwnym razie nie będzie możliwe zainicjowanie logowania z poziomu **dostawcy tożsamości**. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie platformy LaunchDarkly** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi LaunchDarkly.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **LaunchDarkly**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-launchdarkly-sso"></a>Konfigurowanie logowania jednokrotnego LaunchDarkly

1. W osobnym oknie przeglądarki internetowej zaloguj się w swojej witrynie firmowej platformy LaunchDarkly jako administrator.

2. Wybierz pozycję **Ustawienia konta** w panelu nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia element ustawień konta wybrany w obszarze produkcja.](./media/launchdarkly-tutorial/configure-1.png)

3. Kliknij kartę **Zabezpieczenia**.

    ![Zrzut ekranu przedstawia kartę Zabezpieczenia ustawień konta.](./media/launchdarkly-tutorial/configure-2.png)

4. Kliknij pozycję **WŁĄCZ LOGOWANIE JEDNOKROTNE**, a następnie pozycję **EDYTUJ KONFIGURACJĘ SAML**.

    ![Zrzut ekranu przedstawia stronę logowania jednokrotnego, na której można włączyć S s i edytować konfigurację SAML.](./media/launchdarkly-tutorial/configure-3.png)

5. W sekcji **Edytowanie konfiguracji protokołu SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję edytowanie konfiguracji języka SAML, w której można wprowadzić zmiany opisane w tym miejscu.](./media/launchdarkly-tutorial/configure-4.png)

    a. Skopiuj **adres URL usługi konsumenckiej SAML** na potrzeby wystąpienia i wklej go w polu tekstowym Adres URL odpowiedzi w sekcji **Adresy URL i domena platformy LaunchDarkly** w witrynie Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. Otwórz certyfikat pobrany z witryny Azure Portal w Notatniku, skopiuj zawartość, a następnie wklej ją w polu **Certyfikat X.509**, lub przekaż certyfikat bezpośrednio, klikając pozycję **Przekaż jeden**.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-launchdarkly-test-user"></a>Tworzenie użytkownika testowego platformy LaunchDarkly

W tej sekcji użytkownik o nazwie B. Simon został utworzony w LaunchDarkly. LaunchDarkly obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik nie istnieje jeszcze w usłudze LaunchDarkly, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do LaunchDarkly, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka LaunchDarkly w obszarze Moje aplikacje należy automatycznie zalogować się do LaunchDarkly, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu LaunchDarkly można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).