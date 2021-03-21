---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu programu cukier CRM | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 8c0bbebf9fdc9e8027b947beb037dde47b26b67b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644854"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą programu cukier CRM

W tym samouczku dowiesz się, jak zintegrować program cukier CRM z Azure Active Directory (Azure AD). W przypadku integrowania programu cukier CRM z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu cukier CRM.
* Zezwól użytkownikom na automatyczne logowanie do programu cukier CRM przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w programie CRM.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program cukier CRM obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-sugar-crm-from-the-gallery"></a>Dodawanie programu cukier CRM z galerii

Aby skonfigurować integrację programu cukier CRM z usługą Azure AD, musisz dodać program CRM z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **CRM** .
1. Wybierz pozycję **cukier CRM** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu cukier

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu cukier CRM przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie cukier CRM.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu cukier CRM, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania jednokrotnego](#configure-sugar-crm-sso)** dla programu CRM — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu Dynamics CRM](#create-sugar-crm-test-user)** , aby uzyskać odpowiednika B. Simon w programie CRM, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z aplikacją **CRM** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta w programie cukier](https://support.sugarcrm.com/) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu cukier CRM** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu cukier CRM.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **cukier CRM**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sugar-crm-sso"></a>Konfigurowanie rejestracji jednokrotnej w programie CRM

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny programu CRM jako administrator.

1. Przejdź do pozycji **Administracja**.

    ![Administracja](./media/sugarcrm-tutorial/ic795888.png "Administracja")

1. W sekcji **Administracja** kliknij pozycję **Zarządzanie hasłami**.

    ![Zrzut ekranu przedstawia sekcję Administracja, w której można wybrać pozycję Zarządzanie hasłami.](./media/sugarcrm-tutorial/ic795889.png "Administracja")

1. Wybierz pozycję **Włącz uwierzytelnianie SAML**.

    ![Zrzut ekranu przedstawia opcję wyboru uwierzytelniania SAML.](./media/sugarcrm-tutorial/ic795890.png "Administracja")

1. W sekcji **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![Uwierzytelnianie SAML](./media/sugarcrm-tutorial/save.png "Uwierzytelnianie SAML")  

    a. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.
  
    b. W polu tekstowym **adres URL SLO** wklej wartość **adres URL wylogowania**, który został skopiowany z Azure Portal.
  
    c. Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej cały certyfikat do pola tekstowego **certyfikatu X. 509** .
  
    d. Kliknij pozycję **Zapisz**.

### <a name="create-sugar-crm-test-user"></a>Utwórz użytkownika testowego CRM

Aby umożliwić użytkownikom usługi Azure AD logowanie się do programu cukier CRM, muszą one być obsługiwane w programie CRM. W przypadku programu CRM Provisioning jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmowej programu **Dynamics CRM** jako administrator.

1. Przejdź do pozycji **Administracja**.

    ![Administracja](./media/sugarcrm-tutorial/ic795888.png "Administracja")

1. W sekcji **Administracja** kliknij pozycję **Zarządzanie użytkownikami**.

    ![Zrzut ekranu przedstawia sekcję Administracja, w której można wybrać pozycję Zarządzanie użytkownikami.](./media/sugarcrm-tutorial/ic795893.png "Administracja")

1. Przejdź do pozycji **Użytkownicy \> Utwórz nowego użytkownika**.

    ![Utwórz nowego użytkownika](./media/sugarcrm-tutorial/ic795894.png "Utwórz nowego użytkownika")

1. Na karcie **profil użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę profil użytkownika, na której można wprowadzić podane wartości.](./media/sugarcrm-tutorial/ic795895.png "Nowy użytkownik")

    * Wpisz **nazwę użytkownika** **, nazwisko i** **adres e-mail** prawidłowego użytkownika Azure Active Directory w powiązanych polach tekstowych.
  
1. Jako **stan** wybierz pozycję **aktywny**.

1. Na karcie hasło wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę hasło, na której można wprowadzić podane wartości.](./media/sugarcrm-tutorial/ic795896.png "Nowy użytkownik")

    a. Wpisz hasło w powiązanym polu tekstowym.

    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać dowolnego innego narzędzia do tworzenia kont użytkowników i interfejsów API programu CRM dostarczonych przez program cukier CRM.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do programu CRM, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania w programie CRM i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka programu cukier CRM w obszarze Moje aplikacje zostanie ono przekierowany na adres URL logowania do programu CRM. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu cukier CRM można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).