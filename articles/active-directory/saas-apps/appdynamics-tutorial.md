---
title: 'Samouczek: integracja Azure Active Directory z usługą AppDynamics | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem AppDynamics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 616829c3fc666bbc6a4c860d9fb5c4e9fff59d7a
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449412"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Samouczek: integracja Azure Active Directory z usługą AppDynamics

W tym samouczku dowiesz się, jak zintegrować usługę AppDynamics z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AppDynamics z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AppDynamics.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AppDynamics przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) AppDynamics.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa AppDynamics obsługuje usługę **SP** zainicjowaną przez usługę SSO.

* AppDynamics obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="add-appdynamics-from-the-gallery"></a>Dodaj AppDynamics z galerii

Aby skonfigurować integrację oprogramowania AppDynamics z usługą Azure AD, należy dodać oprogramowanie AppDynamics z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AppDynamics** w polu wyszukiwania.
1. Wybierz pozycję **AppDynamics** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-appdynamics"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla AppDynamics

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AppDynamics przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AppDynamics.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AppDynamics, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-appdynamics-sso)** w usłudze AppDynamics, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego AppDynamics](#create-appdynamics-test-user)** , aby dysponować odpowiednikiem B. Simon w AppDynamics, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **AppDynamics** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.saas.appdynamics.com?accountName=<companyname>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania AppDynamics](https://www.appdynamics.com/support/), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie oprogramowania AppDynamics** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi AppDynamics.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **AppDynamics**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-appdynamics-sso"></a>Konfigurowanie logowania jednokrotnego AppDynamics

1. W innym oknie przeglądarki internetowej zaloguj się w witrynie firmy AppDynamics jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Ustawienia**, a następnie **Administracja**.

    ![Administracja](./media/appdynamics-tutorial/settings.png "Administracja")

1. Kliknij kartę **Dostawca uwierzytelniania**.

    ![Dostawca uwierzytelniania](./media/appdynamics-tutorial/authentication.png "Dostawca uwierzytelniania")

1. W sekcji **Dostawca uwierzytelniania** wykonaj następujące czynności:

    ![Konfiguracja SAML](./media/appdynamics-tutorial/configuration.png "Konfiguracja SAML")

   a. Jako **Dostawca uwierzytelniania** wybierz opcję **SAML**.

   b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

   c. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

   d. Otwórz swój certyfikat zakodowany w formacie Base-64 w programie Notatnik, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **Certyfikat**

   e. Kliknij pozycję **Zapisz**.

### <a name="create-appdynamics-test-user"></a>Tworzenie użytkownika testowego oprogramowania AppDynamics

W tej sekcji użytkownik o nazwie B. Simon został utworzony w AppDynamics. AppDynamics obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik nie istnieje jeszcze w usłudze AppDynamics, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania AppDynamics, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania AppDynamics i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka AppDynamics w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania AppDynamics. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu AppDynamics można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).