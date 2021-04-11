---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu usług Samsung KNOX i Business Services | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Samsung KNOX i Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 330c02f15c0818f0a5c69088757c92a91a523589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu usług Samsung KNOX i Business Services

W tym samouczku dowiesz się, jak zintegrować usługi Samsung KNOX i Business z usługą Azure Active Directory (Azure AD). W przypadku integrowania usług Samsung KNOX i Business z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usług Samsung KNOX i Business Services.
* Umożliwia użytkownikom automatyczne logowanie do usług Samsung KNOX i Business przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto Samsung KNOX.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Samsung KNOX i Business obsługują zainicjowanie rejestracji jednokrotnej w programie **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Dodawanie usług Samsung KNOX i Business Services z galerii

Aby skonfigurować integrację usług Samsung KNOX i Business z usługą Azure AD, musisz dodać usługi Samsung KNOX i Business Services z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Samsung KNOX i Business Services** w polu wyszukiwania.
1. Wybierz pozycję **Samsung KNOX i Business Services** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usług Samsung KNOX i Business

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usług Samsung KNOX i Business przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w [SamsungKnox.com](https://samsungknox.com/).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usług Samsung KNOX i Business Services, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj ustawienia logowania jednokrotnego w usłudze **[Samsung KNOX i Business Services](#configure-samsung-knox-and-business-services-sso)** w celu skonfigurowania ustawień rejestracji jednokrotnej na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usług Samsung KNOX i Business Services](#create-samsung-knox-and-business-services-test-user)** , aby dysponować odpowiednikiem B. Simon w usługach Samsung KNOX i Business, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Samsung KNOX i Business Services** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    * W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://www.samsungknox.com`
    * W polu tekstowym **adres URL odpowiedzi (adres url Assertion Consumer Service)** wpisz adres URL: `https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![Podstawowe wartości konfiguracyjne SAML](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usług Samsung KNOX i Business.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy Aplikacje wybierz pozycję **Samsung KNOX i Business Services**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-samsung-knox-and-business-services-sso"></a>Konfigurowanie logowania jednokrotnego dla usług Samsung KNOX i Business

1. W innym oknie przeglądarki sieci Web Zaloguj się do [SamsungKnox.com](https://samsungknox.com/) jako administrator.

1. Kliknij **awatar** w prawym górnym rogu.

    ![Awatar Samsung KNOX](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Na pasku bocznym po lewej stronie kliknij pozycję **Ustawienia usługi Active Directory** i wykonaj następujące czynności.

    ![USTAWIENIA USŁUGI ACTIVE DIRECTORY](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wklej wartość **identyfikatora** wprowadzoną w Azure Portal.

    b. W polu tekstowym **adres URL metadanych federacji aplikacji** wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z Azure Portal.

    c. Kliknij pozycję **Połącz z logowaniem JEDNOkrotnym usługi AD**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Tworzenie użytkownika testowego usług Samsung KNOX i Business Services

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usługach Samsung KNOX i Business Services. Zapoznaj się z przewodnikami administratora [rejestracji mobilnej](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) [Konfiguracja](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) lub Knox, aby uzyskać instrukcje dotyczące zapraszania podadministratora lub użytkownika testowego do organizacji Samsung KNOX. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do [SamsungKnox.com](https://samsungknox.com/), w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do [SamsungKnox.com](https://samsungknox.com/) i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka usługi Samsung KNOX i Business Services w obszarze Moje aplikacje zostanie ono przekierowanie do [SamsungKnox.com](https://samsungknox.com/). Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usług Samsung KNOX i Business można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).