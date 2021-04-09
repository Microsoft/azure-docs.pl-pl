---
title: 'Samouczek: integracja Azure Active Directory z usługą Tableau online | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Tableau online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732004"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Tableau online

W tym samouczku dowiesz się, jak zintegrować usługę Tableau online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Tableau w trybie online z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Tableau online.
* Zezwól użytkownikom na automatyczne logowanie do Tableau online przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Tableau online.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Tableau online obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau online z galerii

Aby skonfigurować integrację usługi Tableau w usłudze Azure AD, musisz dodać Tableau online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Tableau online** w polu wyszukiwania.
1. Wybierz pozycję **Tableau online** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi Tableau online

W tej sekcji można skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze Tableau online na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Tableau online.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze Tableau online, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze Tableau online](#configure-tableau-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Tableau online](#create-tableau-online-test-user)** , aby uzyskać odpowiednik B. Simon w usłudze Tableau online, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Tableau online** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Otrzymasz `<entityid>` wartość z sekcji Konfigurowanie usługi **Tableau online** w tym samouczku. Wartość identyfikatora jednostki będzie wartością **identyfikatora usługi Azure AD** w sekcji **Konfiguracja Tableau online** .

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi Tableau online** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Tableau w trybie online.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Tableau online**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-tableau-online-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Tableau online

1. Aby zautomatyzować konfigurację w usłudze Tableau online, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj Tableau online** przekieruje Cię do aplikacji online Tableau. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Tableau online. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Tableau online, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Tableau w trybie online jako administrator.

1. Przejdź do pozycji **Ustawienia** , a następnie pozycję **uwierzytelnianie**.

    ![Zrzut ekranu przedstawia uwierzytelnianie wybrane z menu Ustawienia.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Aby włączyć protokół SAML, w sekcji **typy uwierzytelniania** . Zaznacz **opcję Włącz dodatkową metodę uwierzytelniania** , a następnie zaznacz pole wyboru **SAML** .

    ![Zrzut ekranu przedstawia sekcję typy uwierzytelniania, w której można wybrać wartości.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Przewiń w dół, aby **zaimportować plik metadanych do sekcji Tableau online** .  Kliknij przycisk Przeglądaj i zaimportuj plik metadanych, który został pobrany z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.

   ![Zrzut ekranu przedstawia sekcję, w której można zaimportować plik metadanych.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. W sekcji **potwierdzenia dopasowania** Wstaw odpowiednią nazwę potwierdzenia dostawcy tożsamości dla **adresu e-mail**, **imienia** i **nazwiska**. Aby uzyskać te informacje z usługi Azure AD: 
  
    a. W Azure Portal przejdź na stronę integracji aplikacji **online Tableau** .

    b. W sekcji **atrybuty użytkownika & oświadczenia** kliknij ikonę edycji.

   ![Zrzut ekranu przedstawia sekcję "atrybuty użytkownika & oświadczenia, w której można wybrać ikonę edycji.](./media/tableauonline-tutorial/attributesection.png)

    c. Skopiuj wartość przestrzeni nazw dla tych atrybutów: imięname, email i nazwisko, wykonując następujące czynności:

   ![Zrzut ekranu pokazuje podaną wartośćname, nazwisko i atrybuty EmailAddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kliknij pozycję **użytkownik. dana** wartośćname

    e. Skopiuj wartość z pola tekstowego **przestrzeń nazw** .

    ![Zrzut ekranu przedstawia sekcję Zarządzanie oświadczeniami użytkowników, w której można wprowadzić przestrzeń nazw.](./media/tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować wartości przestrzeni nazw dla wiadomości e-mail i nazwiska, powtórz powyższe kroki.

    przykład Przejdź do aplikacji Tableau online, a następnie ustaw **& atrybuty użytkownika** w następujący sposób:

    * Wiadomość e-mail: **poczta** lub **userPrincipalName**

    * Imię: **podaną** nazwę

    * Nazwisko: **nazwisko**

    ![Zrzut ekranu przedstawia sekcję dopasowanie atrybutów, w której można wprowadzić wartości.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Utwórz użytkownika testowego usługi Tableau online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Tableau online.

1. W witrynie **Tableau online**, kliknij pozycję **Ustawienia** , a następnie sekcję **uwierzytelnianie** . Przewiń w dół do sekcji **Zarządzanie użytkownikami** . Kliknij pozycję **Dodaj użytkowników** , a następnie kliknij pozycję **wprowadź adresy e-mail**.
  
    ![Zrzut ekranu przedstawia sekcję Zarządzanie użytkownikami, w której można wybrać opcję Dodaj użytkowników.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wybierz pozycję **Dodaj użytkowników dla (SAML) uwierzytelnianie**. W polu tekstowym **wprowadź adresy e-mail** Dodaj Britta. Simon \@ contoso.com
  
    ![Zrzut ekranu przedstawia stronę Dodawanie użytkowników, na której można wprowadzić adres e-mail.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kliknij pozycję **Dodaj użytkowników**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi Tableau online, na którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL logowania do usługi Tableau online i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Tableau online w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do usługi Tableau online. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Tableau online można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)