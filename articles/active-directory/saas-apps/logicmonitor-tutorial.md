---
title: 'Samouczek: integracja Azure Active Directory z usługą LogicMonitor | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: ab2aa1df0719b685800529f32e36674bf85e57ee
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625317"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Samouczek: integracja Azure Active Directory z usługą LogicMonitor

W tym samouczku dowiesz się, jak zintegrować usługę LogicMonitor z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi LogicMonitor z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do LogicMonitor.
* Zezwól użytkownikom na automatyczne logowanie się do usługi LogicMonitor przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą LogicMonitor, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz w [tym miejscu](https://azure.microsoft.com/pricing/free-trial/)uzyskać miesięczną wersję próbną.
* Subskrypcja z włączonym logowaniem jednokrotnym w LogicMonitor.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* LogicMonitor obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-logicmonitor-from-the-gallery"></a>Dodaj LogicMonitor z galerii

Aby skonfigurować integrację programu LogicMonitor z usługą Azure AD, musisz dodać LogicMonitor z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **LogicMonitor** w polu wyszukiwania.
1. Wybierz pozycję **LogicMonitor** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla LogicMonitor

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą LogicMonitor przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w LogicMonitor.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą LogicMonitor, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-logicmonitor-sso)** w usłudze LogicMonitor, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego LogicMonitor](#create-logicmonitor-test-user)** , aby dysponować odpowiednikiem B. Simon w LogicMonitor, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **LogicMonitor** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![LogicMonitor domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.logicmonitor.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta LogicMonitor](https://www.logicmonitor.com/contact/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie LogicMonitor** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi LogicMonitor.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **LogicMonitor**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-logicmonitor-sso"></a>Konfigurowanie logowania jednokrotnego LogicMonitor

1. Zaloguj się do swojej witryny firmowej **LogicMonitor** jako administrator.

2. W menu u góry kliknij pozycję **Settings** (Ustawienia).

    ![Ustawienia](./media/logicmonitor-tutorial/ic790052.png "Ustawienia")

3. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **Logowanie** jednokrotne.

    ![Logowanie jednokrotne](./media/logicmonitor-tutorial/ic790053.png "Logowanie jednokrotne")

4. W sekcji **Ustawienia rejestracji jednokrotnej (SSO)** wykonaj następujące czynności:

    ![Ustawienia pojedynczego Sign-On](./media/logicmonitor-tutorial/ic790054.png "Ustawienia pojedynczego Sign-On")

    a. Wybierz pozycję **Włącz logowanie jednokrotne**.

    b. Jako **domyślne przypisanie roli** wybierz opcję **tylko do odczytu**.

    c. Otwórz pobrany plik metadanych w Notatniku, a następnie wklej zawartość pliku do pola tekstowego **metadanych dostawcy tożsamości** .

    d. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-logicmonitor-test-user"></a>Utwórz użytkownika testowego LogicMonitor

Aby użytkownicy usługi Azure AD mogli się zalogować, muszą być obsługiwani do aplikacji LogicMonitor przy użyciu ich nazw użytkowników Azure Active Directory.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej witryny firmowej LogicMonitor jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia**, a następnie kliknij pozycję **role i użytkownicy**.

    ![Role i użytkownicy](./media/logicmonitor-tutorial/ic790056.png "Role i użytkownicy")

3. Kliknij pozycję **Dodaj**.

4. W sekcji **Dodawanie konta** wykonaj następujące czynności:

    ![Dodaj konto](./media/logicmonitor-tutorial/ic790057.png "Dodaj konto")

    a. Wpisz wartości w polach **Nazwa** użytkownika, **adres e-mail** i **hasło**, a następnie wpisz **hasło** , które mają zostać objęte obsługą administracyjną Azure Active Directory.

    b. Wybierz **role**, **Wyświetl uprawnienia** i **stan**.

    c. Kliknij przycisk **Prześlij**.

> [!NOTE]
> Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program LogicMonitor.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania LogicMonitor, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania LogicMonitor i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka LogicMonitor w obszarze Moje aplikacje należy automatycznie zalogować się do LogicMonitor, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu LogicMonitor można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
