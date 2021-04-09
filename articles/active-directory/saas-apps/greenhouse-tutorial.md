---
title: 'Samouczek: integracja Azure Active Directory z szklarnią | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731923"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Samouczek: integracja Azure Active Directory z szklarnią

W tym samouczku dowiesz się, jak zintegrować szklarnię z Azure Active Directory (Azure AD). W przypadku integrowania szklarni z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do szklarni.
* Zezwól użytkownikom na automatyczne logowanie do szklarni przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO).

> [!NOTE] 
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Greenhouse obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-greenhouse-from-the-gallery"></a>Dodawanie aplikacji Greenhouse z galerii

Aby skonfigurować integrację aplikacji Greenhouse z usługą Azure AD, należy dodać aplikację Greenhouse z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz wartość **szklarni** w polu wyszukiwania.
1. Wybierz pozycję **szklarnia** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemu szklarni

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą szklarni przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w szklarni.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą szklarni, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-greenhouse-sso)** w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Greenhouse](#create-greenhouse-test-user)** — aby w aplikacji Greenhouse utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **cieplarnianyej** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.greenhouse.io`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Greenhouse](https://www.greenhouse.io/contact) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Greenhouse** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do szklarni.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Greenhouse**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-greenhouse-sso"></a>Konfigurowanie logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web Zaloguj się jako administrator w sieci Web.

1. Przejdź do pozycji **konfiguruj > Dev Center > Logowanie jednokrotne**.

    ![zrzut ekranu strony logowania jednokrotnego](./media/greenhouse-tutorial/configure.png)

1. Wykonaj następujące kroki na stronie pojedynczej Sign-On.

    ![zrzut ekranu przedstawiający stronę konfiguracji logowania jednokrotnego](./media/greenhouse-tutorial/sso-page.png)

    a. Skopiuj wartość **adresu URL odbiorcy potwierdzenia logowania jednokrotnego** , wklej tę wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. W polu tekstowym **Identyfikator jednostki/wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    c. W polu tekstowym **adres URL pojedynczego Sign-On** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany **plik XML metadanych Federacji** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **odcisku palca certyfikatu dostawcy tożsamości** .

    e. Wybierz wartość **formatu identyfikatora nazwy** z listy rozwijanej.

    f. Kliknij przycisk **Rozpocznij testowanie**.

    >[!NOTE]
    >Alternatywnie można również przekazać plik **XML metadanych Federacji** , klikając opcję **Wybierz plik** .

### <a name="create-greenhouse-test-user&quot;></a>Tworzenie użytkownika testowego aplikacji Greenhouse

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Greenhouse, należy ich aprowizować w aplikacji Greenhouse. W przypadku aplikacji Greenhouse aprowizacja jest zadaniem ręcznym.

>[!NOTE]
>Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API dostarczonych przez dane szklarni. 

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej firmowej witryny aplikacji **Greenhouse** jako administrator.

2. Przejdź do pozycji **konfiguruj > użytkownicy > nowych użytkowników**
   
    ![Użytkownicy](./media/greenhouse-tutorial/create-user-1.png &quot;Użytkownicy")

4. W sekcji **Dodaj nowych użytkowników** wykonaj następujące czynności:
   
    ![Dodaj nowego użytkownika](./media/greenhouse-tutorial/create-user-2.png "Dodaj nowego użytkownika")

    a. W polu tekstowym **Enter user emails** (Wprowadź adresy e-mail użytkowników) wpisz adres e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij pozycję **Zapisz**.    
   
      >[!NOTE]
      >Właściciele kont usługi Azure Active Directory otrzymają wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania szklarniowego, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do trybu szklarni i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka szklarni w obszarze Moje aplikacje zostanie przekierowany na adres URL logowania w trybie szklarni. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu szklarni można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).