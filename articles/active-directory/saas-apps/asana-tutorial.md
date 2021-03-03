---
title: 'Samouczek: integracja Azure Active Directory z usługą Asana | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Asana.
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
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651012"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Samouczek: integracja Azure Active Directory z usługą Asana

W tym samouczku dowiesz się, jak zintegrować usługę Asana z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Asana z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Asana.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Asana przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Asana.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Asana obsługuje logowanie jednokrotne inicjowane przez **SP**

* Aplikacja obsługuje [**zautomatyzowaną** aprowizację użytkowników](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Dodaj Asana z galerii

Aby skonfigurować integrację aplikacji Asana w usłudze Azure AD, należy dodać aplikację Asana z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Asana** w polu wyszukiwania.
1. Wybierz pozycję **Asana** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Asana

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Asana przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Asana.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Asana, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-asana-sso)** w usłudze Asana, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Asana](#create-asana-test-user)** , aby dysponować odpowiednikiem B. Simon w Asana, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Asana** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Asana — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://app.asana.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://app.asana.com/`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Asana** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Asana.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Asana**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-asana-sso"></a>Konfigurowanie logowania jednokrotnego Asana

1. W innym oknie przeglądarki zaloguj się do aplikacji Asana. Aby skonfigurować logowanie jednokrotne w aplikacji Asana, uzyskaj dostęp do ustawień obszaru roboczego, klikając nazwę obszaru roboczego w prawym górnym rogu ekranu. Następnie kliknij pozycję **\<your workspace name\> Ustawienia**.

    ![Ustawienia logowania jednokrotnego w aplikacji Asana](./media/asana-tutorial/settings.png)

2. W oknie **Ustawienia organizacji** kliknij przycisk **Administracja**. Następnie kliknij pozycję **Członkowie muszą logować się za pośrednictwem SAML**, aby włączyć konfigurację logowania jednokrotnego. Wykonaj następujące czynności:

    ![Konfigurowanie ustawień organizacji logowania jednokrotnego](./media/asana-tutorial/save.png)  

    a. W polu tekstowym **Adres URL strony logowania** wklej **adres URL logowania**.

    b. Kliknij prawym przyciskiem myszy certyfikat pobrany z witryny Azure Portal, a następnie otwórz plik certyfikatu w Notatniku lub preferowanym edytorze tekstów. Skopiuj zawartość między początkowym i końcowym tytułem certyfikatu, a następnie wklej ją w polu tekstowym **Certyfikat X.509**.

3. Kliknij pozycję **Zapisz**. Przejdź do [części dotyczącej konfigurowania logowania jednokrotnego w przewodniku po aplikacji Asana](https://asana.com/guide/help/premium/authentication#gl-saml), jeśli potrzebujesz dalszej pomocy.

### <a name="create-asana-test-user"></a>Tworzenie użytkownika testowego aplikacji Asana

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji Asana. Aplikacja Asana obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](asana-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Asana.

1. W aplikacji **Asana** przejdź do sekcji **Teams** (Zespoły) w panelu po lewej stronie. Kliknij przycisk ze znakiem plus.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/asana-tutorial/teams.png)

2. Wpisz adres e-mail użytkownika, np **. Britta. simon \@ contoso.com** w polu tekstowym, a następnie wybierz pozycję **Zaproś**.

3. Kliknij pozycję **Send Invite** (Wyślij zaproszenie). Nowy użytkownik otrzyma wiadomość e-mail na swoim koncie poczty e-mail. Użytkownik będzie musiał utworzyć i zweryfikować konto.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Asana, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Asana i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Asana w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania Asana. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Asana można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).