---
title: 'Samouczek: integracja Azure Active Directory z aplikacją BIZNESową z włączonym tokenem SAML 1,1 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją SAML 1.1 Token enabled LOB App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 477180a6576d52e3386e18b6e2ba12dd33e4d794
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654415"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Samouczek: integracja Azure Active Directory z aplikacją BIZNESową z włączonym tokenem SAML 1,1

W tym samouczku dowiesz się, jak zintegrować aplikację LOB z włączonym tokenem SAML 1,1 przy użyciu usługi Azure Active Directory (Azure AD). W przypadku integracji aplikacji LOB z włączonym tokenem SAML 1,1 w usłudze Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do aplikacji LOB z włączonym tokenem SAML 1,1.
* Zezwól użytkownikom na automatyczne logowanie do aplikacji LOB z włączonym tokenem SAML 1,1 przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Token SAML 1,1 z włączoną obsługą rejestracji jednokrotnej aplikacji LOB.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja SAML 1.1 Token enabled LOB App obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Dodaj aplikację BIZNESową z włączonym tokenem SAML 1,1 z galerii

Aby skonfigurować integrację aplikacji SAML 1.1 Token enabled LOB App z usługą Azure AD, należy dodać aplikację SAML 1.1 Token enabled LOB App z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **aplikacja biznesowa z włączonym tokenem SAML 1,1** .
1. Wybierz pozycję **aplikacja LOB z włączonym tokenem SAML 1,1** z poziomu panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla aplikacji LOB z włączonym tokenem SAML 1,1

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą aplikacji LOB z włączonym tokenem SAML 1,1 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji LOB z włączonym tokenem SAML 1,1.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą aplikacji LOB z włączonym tokenem SAML 1,1, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję Logowanie jednokrotne dla aplikacji LOB przy użyciu tokenu SAML 1,1](#configure-saml-11-token-enabled-lob-app-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego z włączonym tokenem saml 1,1](#create-saml-11-token-enabled-lob-app-test-user)** w aplikacji LOB — Aby uzyskać odpowiednik elementu B. Simon w aplikacji LOB z włączonym tokenem SAML 1,1, która jest połączona z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **LOB z włączonym tokenem SAML 1,1** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://your-app-url`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://your-app-url`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z pomocą techniczną klienta aplikacji SAML 1.1 Token enabled LOB App. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji SAML 1.1 Token enabled LOB App** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do aplikacji LOB z włączonym tokenem SAML 1,1.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **aplikacja LOB z włączonym tokenem SAML 1,1**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Konfigurowanie tokenu SAML 1,1 — Logowanie jednokrotne aplikacji LOB

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **SAML 1.1 Token enabled LOB App**, należy wysłać pobrany **certyfikat (Base64)** i odpowiednie adresy URL skopiowane z witryny Azure Portal zespołowi pomocy technicznej aplikacji SAML 1.1 Token enabled LOB App. Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Tworzenie użytkownika testowego aplikacji SAML 1.1 Token enabled LOB App

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji SAML 1.1 Token enabled LOB App. Aby dodać użytkowników do platformy aplikacji SAML 1.1 Token enabled LOB App, skontaktuj się z pomocą techniczną tej platformy. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji LOB z włączonym tokenem SAML 1,1, gdzie można zainicjować przepływ logowania. 

* Przejdź do adresu SAML 1,1 tokenu z włączonym adresem URL logowania aplikacji LOB i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka aplikacji LOB z włączonym tokenem SAML 1,1 w obszarze Moje aplikacje zostanie przekierowany do adresu URL logowania aplikacji LOB z włączonym tokenem SAML 1,1. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji LOB z włączonym tokenem SAML 1,1 można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).