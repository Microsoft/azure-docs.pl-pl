---
title: 'Samouczek: integracja Azure Active Directory z usługą Expensify | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 5c68453f7f7d638877683920a17022cf3d29bdc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597814"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Samouczek: integracja Expensify z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Expensify z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Expensify z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Expensify.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Expensify przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Expensify.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Expensify obsługuje usługę **SP** zainicjowaną przez usługę SSO.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-expensify-from-the-gallery"></a>Dodaj Expensify z galerii

Aby skonfigurować integrację aplikacji Expensify z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Expensify** w polu wyszukiwania.
1. Wybierz pozycję **Expensify** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Expensify

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Expensify przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Expensify.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Expensify, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-expensify-sso)** w usłudze Expensify, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Expensify](#create-expensify-test-user)** , aby dysponować odpowiednikiem B. Simon w Expensify, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Expensify** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://www.expensify.com/authentication/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://www.expensify.com`

    c. b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Expensify](mailto:help@expensify.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **XML metadanych** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Expensify** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Expensify.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Expensify**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-expensify-sso"></a>Konfigurowanie logowania jednokrotnego Expensify

Aby włączyć logowanie jednokrotne w aplikacji Expensify, należy najpierw włączyć **kontrolę domeny** w aplikacji. Kontrolę domeny można włączyć w aplikacji, wykonując kroki opisane [tutaj](https://help.expensify.com/domain-control). Aby uzyskać dodatkową pomoc, skontaktuj się z [zespołem obsługi klienta aplikacji Expensify](mailto:help@expensify.com). Po włączeniu kontroli domeny wykonaj następujące kroki:

![Konfigurowanie logowania jednokrotnego](./media/expensify-tutorial/domain-control.png)

1. Zaloguj się do aplikacji Expensify.

2. W okienku po lewej stronie kliknij pozycję **Settings** (Ustawienia), a następnie przejdź do sekcji **SAML**.

3. Przełącz wartość opcji **SAML Login** (Logowanie protokołu SAML) na wartość **Enabled** (Włączone).

4. W Notatniku otwórz metadane federacji pobrane z usługi Azure AD, skopiuj ich zawartość, a następnie wklej je do pola tekstowego **Identity Provider Metadata** (Metadane dostawcy tożsamości).

### <a name="create-expensify-test-user"></a>Tworzenie użytkownika testowego aplikacji Expensify

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Expensify. Skontaktuj się z [zespołem obsługi klienta aplikacji Expensify](mailto:help@expensify.com), aby dodać użytkowników na platformie Expensify.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Expensify, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Expensify i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Expensify w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania Expensify. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Expensify można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).