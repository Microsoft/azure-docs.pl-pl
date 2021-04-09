---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podróży i wydatków Concur | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a kosztami podróży i wydatków Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737035"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podróży i wydatków Concur

W tym samouczku dowiesz się, jak zintegrować Concure i koszty za pomocą usługi Azure Active Directory (Azure AD). Po zintegrowaniu Concur podróży i wydatków z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do podróży i wydatków usługi Concur.
* Zezwól użytkownikom na automatyczne logowanie do Concur podróży i wydatków przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja podróży i wydatków usługi Concur.
* Rola "administrator firmy" w ramach konta użytkownika Concur. Możesz sprawdzić, czy masz odpowiednie uprawnienia dostępu, przechodząc do [Concur Self-Service rejestracji jednokrotnej](https://www.concursolutions.com/nui/authadmin/ssoadmin). Jeśli nie masz dostępu, skontaktuj się z pomocą techniczną Concur lub zaimplementowanie menedżera projektu. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD.

* Concur podróże i wydatki obsługują Logowanie jednokrotne w **dostawcy tożsamości** i **SP**
* Concur podróże i wydatki obsługują testowanie logowania jednokrotnego w środowisku produkcyjnym i implementacji 

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu dla każdego z trzech regionów: US, EMEA i Chiny. Można skonfigurować tylko jedno wystąpienie dla każdego regionu w jednej dzierżawie. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Dodawanie podróży i wydatków usługi Concur z galerii

Aby skonfigurować integrację usługi Concur w podróży i obchodzić z usługą Azure AD, musisz dodać przemieszczenie Concur i wydatki z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Concur podróże i wydatki** w polu wyszukiwania.
1. Wybierz pozycję **Concur podróże i wydatki** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla podróży i wydatków usługi Concur

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Concurego ruchu i wydatków przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Concur podróże i wydatki.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu podróży Concur i wydatków, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj podróże i wydatki logowania jednokrotnego w usłudze Concur,](#configure-concur-travel-and-expense-sso)** aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego podróży i wydatków usługi Concur,](#create-concur-travel-and-expense-test-user)** aby dysponować odpowiednikiem B. Simon w Concur podróży i wydatków, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji do **podróży i wydatków Concur** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

    > [!NOTE]
    > Identyfikator (identyfikator jednostki) i adres URL odpowiedzi (adres URL usługi Konsumenckej potwierdzenia) są specyficzne dla regionu. Wybierz pozycję na podstawie centrum danych jednostki Concur. Jeśli nie znasz centrum danych jednostki Concur, skontaktuj się z pomocą techniczną Concur. 

5. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra dla **atrybutu użytkownika** , aby edytować ustawienia. Unikatowy identyfikator użytkownika musi pasować do login_id użytkownika Concur. Zwykle należy zmienić **User. userPrincipalName** na **User. mail**.

    ![Edytowanie atrybutu użytkownika](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać metadane i zapisać je na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Concur podróży i wydatków.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Concur podróże i wydatki**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurowanie Concur podróży i wydatków logowania jednokrotnego

1. Aby zautomatyzować konfigurację w ramach podróży Concur i wydatków, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj podróże Concure i** obsłużymy do aplikacji do podróży i wydatków na Concur. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Concur i wydatków. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować podróże i wydatki usługi Concur, w innym oknie przeglądarki sieci Web musisz przekazać pobrany **plik XML metadanych Federacji** do [Narzędzia Concur logowania jednoSelf-Service krotnego](https://www.concursolutions.com/nui/authadmin/ssoadmin) , a następnie zalogować się do witryny firmy w Concure i wydatków jako administrator.

1. Kliknij pozycję **Dodaj**.
1. Wprowadź niestandardową nazwę dostawcy tożsamości, na przykład "Azure AD (US)". 
1. Kliknij pozycję **Przekaż plik XML** i Dołącz wcześniej pobrany **kod XML metadanych Federacji** .
1. Kliknij przycisk **Dodaj metadane** , aby zapisać zmiany.

    ![Zrzut ekranu narzędzia samoobsługowego logowania jednokrotnego Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Utwórz użytkownika testowego podróży i wydatków Concur

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Concur podróży i obrachunku. Współpracuj z zespołem pomocy technicznej Concur, aby dodać użytkowników z platformy podróży i wydatków Concur. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

> [!NOTE]
> B. identyfikator logowania Concur Simon musi pasować do unikatowego identyfikatora B. Simon w usłudze Azure AD. Na przykład jeśli Simon usługi Azure AD `B.Simon@contoso.com` Identyfikator. B. Simon identyfikator logowania Concur musi być `B.Simon@contoso.com` również. 

## <a name="configure-concur-mobile-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Concur Mobile
Aby włączyć logowanie jednokrotne w usłudze Concur Mobile, należy przyznać Concur obsługę **adresu URL dostępu użytkownika** zespołu. Wykonaj poniższe kroki, aby uzyskać **adres URL dostępu użytkownika** z usługi Azure AD:
1. Przejdź do **aplikacji dla przedsiębiorstw**
1. Kliknij pozycję **Concur podróże i wydatki**
1. Kliknij pozycję **Właściwości**
1. Kopiuj **adres URL dostępu użytkowników** i nadaj temu adresowi URL obsługę Concur

> [!NOTE]
> Opcja Self-Service konfigurowania logowania jednokrotnego jest niedostępna, dlatego należy skontaktować się z zespołem pomocy technicznej Concur. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Concur wyjazdów i wydatków na adres URL, gdzie można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL Concur podróży i wydatków, a następnie zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do podróży i wydatków usługi Concur, dla których skonfigurowano Logowanie jednokrotne.

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka podróży i wydatków usługi Concur w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do usługi Concur podróży i wydatków, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Concur podróży i wydatków można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).