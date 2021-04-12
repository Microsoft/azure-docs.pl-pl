---
title: 'Samouczek: integracja Azure Active Directory z platformą produktywności ClickUp | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją ClickUp Productivity Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285216"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Samouczek: integracja Azure Active Directory z platformą produktywności ClickUp

W tym samouczku dowiesz się, jak zintegrować platformę ClickUp produktywność z usługą Azure Active Directory (Azure AD). Gdy integrujesz platformę produktywną ClickUp z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do platformy produktywności ClickUp.
* Zezwól użytkownikom na automatyczne logowanie się na platformie ClickUp produktywności przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) platformy produktywności ClickUp.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma produktywności ClickUp obsługuje logowanie jednokrotne w ramach platformy **SP** .

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Dodaj platformę produktywną ClickUp z galerii

Aby skonfigurować integrację aplikacji ClickUp Productivity Platform z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ClickUp produktywność** w polu wyszukiwania.
1. Wybierz pozycję **ClickUp produktywność** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Konfigurowanie i testowanie platformy Azure AD SSO na potrzeby ClickUp produktywności

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy ClickUp produktywności przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ClickUp produktywnej platformy.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platformy ClickUp produktywności, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj konfigurację](#configure-clickup-productivity-platform-sso)** logowania jednokrotnego dla platformy ClickUp, aby skonfigurować ustawienia rejestracji jednokrotnej na stronie aplikacji.
    1. **[Utwórz użytkownika testowego platformy ClickUp produktywności](#create-clickup-productivity-platform-test-user)** , aby dysponować odpowiednikiem B. Simon na platformie ClickUp produktywności, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **platformy ClickUp** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://app.clickup.com/login/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość przy użyciu rzeczywistej wartości identyfikatora opisanej w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do platformy produktywności ClickUp.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ClickUp Productivity Platform**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-clickup-productivity-platform-sso"></a>Konfigurowanie rejestracji jednokrotnej platformy produktywności ClickUp

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji ClickUp Productivity Platform jako administrator.

2. Kliknij **profil użytkownika**, a następnie wybierz pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia dzierżawę produktywność ClickUp z wybraną ikoną ustawienia.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Zrzut ekranu przedstawia ustawienia.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. W obszarze „Single Sign-On (SSO) Provider” (Dostawca logowania jednokrotnego) wybierz pozycję **Microsoft**.

    ![Zrzut ekranu przedstawia okienko uwierzytelnianie z wybranym przez firmę Microsoft.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Na stronie **Configure Microsoft Single Sign On** (Konfigurowanie logowania jednokrotnego firmy Microsoft) wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Konfigurowanie logowania jednokrotnego firmy Microsoft, na której można skopiować jednostkę I D i zapisać metadane federacji platformy Azure U R L.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Kliknij przycisk **Copy** (Kopiuj), aby skopiować wartość identyfikatora jednostki, i wklej ją w polu tekstowym **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    b. W polu tekstowym **Azure Federation Metadata URL** (Adres URL metadanych federacyjnych platformy Azure) wklej wartość pola „Adres URL metadanych federacyjnych aplikacji” skopiowaną z witryny Azure Portal i kliknij przycisk **Save** (Zapisz).

5. Aby ukończyć instalację, kliknij pozycję **Authenticate With Microsoft to complete setup** (Uwierzytelnij za pomocą konta Microsoft w celu ukończenia instalacji) i uwierzytelnij się przy użyciu konta Microsoft.

    ![Zrzut ekranu przedstawia przycisk Uwierzytelnij z firmą Microsoft, aby zakończyć instalację.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Tworzenie użytkownika testowego w aplikacji ClickUp Productivity Platform

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji ClickUp Productivity Platform jako administrator.

2. Kliknij **profil użytkownika**, a następnie wybierz pozycję **osoby**.

    ![Zrzut ekranu przedstawia dzierżawę wydajności ClickUp.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Zrzut ekranu przedstawia wybrany link osoby.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. W polu tekstowym wprowadź adres e-mail użytkownika i kliknij przycisk **Invite** (Zaproś).

    ![Zrzut ekranu przedstawia ustawienia użytkowników zespołu, w których można zapraszać osoby za pośrednictwem poczty e-mail.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > Użytkownik otrzyma powiadomienie i musi zaakceptować zaproszenie do aktywowania konta.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania platformy produktywności ClickUp, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania platformy ClickUp produktywności i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka platforma ClickUp produktywność w obszarze Moje aplikacje zostanie przekierowana na adres URL logowania platformy ClickUp. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu platformy produktywności ClickUp można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).