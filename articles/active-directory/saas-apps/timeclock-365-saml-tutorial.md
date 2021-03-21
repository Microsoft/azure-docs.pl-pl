---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą TimeClock 365 SAML | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TimeClock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99050807"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą TimeClock 365 SAML

W tym samouczku dowiesz się, jak zintegrować TimeClock 365 SAML z Azure Active Directory (Azure AD). Podczas integrowania protokołu SAML TimeClock 365 z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TimeClock 365 SAML.
* Zezwól użytkownikom na automatyczne logowanie do TimeClock 365 SAML przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z obsługą logowania jednokrotnego (SSO) w systemie TimeClock 365.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TimeClock 365 SAML obsługuje protokół SSO zainicjowany przez usługę **SP**

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Dodawanie TimeClock 365 SAML z galerii

Aby skonfigurować integrację protokołu SAML TimeClock 365 w usłudze Azure AD, należy dodać do listy zarządzanych aplikacji SaaS TimeClock 365 SAML z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TimeClock 365 SAML** w polu wyszukiwania.
1. Wybierz pozycję **Timeclock 365 SAML** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla TimeClock 365 SAML

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą TimeClock 365 SAML przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TimeClock 365 SAML.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu protokołu SAML TimeClock 365, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Timeclock 365 SAML SSO](#configure-timeclock-365-saml-sso)** -, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAML w systemie Timeclock 365](#create-timeclock-365-saml-test-user)** , aby uzyskać odpowiednik B. Simon w Timeclocke SAML 365, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SAML Timeclock 365** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://live.timeclock365.com/login`


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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do TimeClock 365 SAML.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Timeclock 365 SAML**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-timeclock-365-saml-sso"></a>Konfigurowanie logowania jednokrotnego dla TimeClock 365 SAML

1. Aby zautomatyzować konfigurację w programie TimeClock 365 SAML, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj Timeclock 365 SAML** przekieruje Cię do aplikacji SAML TimeClock 365. W tym miejscu podaj poświadczenia administratora, aby zalogować się do TimeClock 365 SAML. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować TimeClock 365 SAML w innym oknie przeglądarki sieci Web, zaloguj się do witryny firmy TimeClock 365 jako administrator.

1. Wykonaj poniższe czynności.

    ![Konfiguracja TimeClock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Przejdź do pozycji **ustawienia > profil firmy > kartę Ustawienia** .

    b. W **ścieżce metadanych dostawcy tożsamości** wklej **adres URL metadanych federacji aplikacji** , który został skopiowany z Azure Portal.

    c. Następnie kliknij pozycję **Aktualizuj**.

### <a name="create-timeclock-365-saml-test-user"></a>Tworzenie użytkownika testowego SAML w programie TimeClock 365

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy TimeClock 365 SAML jako administrator.

1. Przejdź do pozycji **użytkownicy > Dodaj nowego użytkownika**.

    ![Utwórz test Użytkownik1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Podaj wszystkie wymagane informacje na stronie **Informacje o użytkowniku** , a następnie kliknij przycisk **Zapisz**.

    ![Utwórz test ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć użytkownika testowego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania TimeClock 365 SAML, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do usługi TimeClock 365 SAML i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Kliknięcie kafelka SAML TimeClock 365 w obszarze Moje aplikacje spowoduje przekierowanie do adresu URL logowania do TimeClock 365. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SAML TimeClock 365 można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
