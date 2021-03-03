---
title: 'Samouczek: integracja Azure Active Directory z usługą Cherwell | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 5e2cf4ac43fa2a828ebe9e9a5cb5d2e1d630a59a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649384"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Samouczek: integracja Azure Active Directory z usługą Cherwell

W tym samouczku dowiesz się, jak zintegrować usługę Cherwell z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Cherwell z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Cherwell.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Cherwell przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Cherwell, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

* Subskrypcja z włączonym logowaniem jednokrotnym w Cherwell.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Cherwell obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-cherwell-from-the-gallery"></a>Dodaj Cherwell z galerii

Aby skonfigurować integrację aplikacji Cherwell z usługą Azure AD, musisz dodać aplikację Cherwell z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Cherwell** w polu wyszukiwania.
1. Wybierz pozycję **Cherwell** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Cherwell

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Cherwell przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Cherwell.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Cherwell, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    2. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-cherwell-sso)** w usłudze Cherwell, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Cherwell](#create-cherwell-test-user)** , aby dysponować odpowiednikiem B. Simon w Cherwell, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Cherwell** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.cherwellondemand.com/cherwellclient`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj wartość przy użyciu adresu URL logowania i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Cherwell](https://cherwellsupport.com/CherwellPortal), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Cherwell** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wpisz **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź wartość `<username>@<companydomain>.<extension>` . Na przykład: `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Cherwell.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cherwell**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cherwell-sso"></a>Konfigurowanie logowania jednokrotnego Cherwell

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Cherwell**, musisz wysłać pobrany **certyfikat (Base64)** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Cherwell](https://cherwellsupport.com/CherwellPortal). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!NOTE]
> Twój zespół pomocy technicznej aplikacji Cherwell musi przeprowadzić rzeczywistą konfigurację logowania jednokrotnego. Gdy logowanie jednokrotne zostanie włączone dla Twojej subskrypcji, otrzymasz o tym powiadomienie.

### <a name="create-cherwell-test-user"></a>Tworzenie użytkownika testowego aplikacji Cherwell

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Cherwell, muszą one być obsługiwane w usłudze Cherwell. W przypadku aplikacji Cherwell konta użytkowników muszą zostać utworzone przez Twój [zespół pomocy technicznej aplikacji Cherwell](https://cherwellsupport.com/CherwellPortal).

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnianych przez aplikację Cherwell.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Cherwell, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Cherwell i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Cherwell w obszarze Moje aplikacje należy automatycznie zalogować się do Cherwell, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Cherwell można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)