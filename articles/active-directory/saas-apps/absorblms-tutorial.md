---
title: 'Samouczek: integracja Azure Active Directory z systemem LMS Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Absorb LMS.
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
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646524"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Samouczek: integracja Azure Active Directory z systemem LMS

W tym samouczku dowiesz się, jak zintegrować system LMS z usługą Azure Active Directory (Azure AD). Gdy integrujesz system LMS z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do systemu LMS.
* Zezwól użytkownikom na automatyczne logowanie się w celu zaabsorbowania systemu LMS przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Absorb LMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaabsorbowana subskrypcja obsługująca Logowanie jednokrotne w systemie LMS.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Absorb LMS obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-absorb-lms-from-the-gallery"></a>Dodawanie systemu LMS z galerii

Aby skonfigurować integrację usługi Absorb LMS z usługą Azure AD, należy dodać usługę Absorb LMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **pochłanianie systemu LMS** .
1. Wybierz pozycję **wchłanianie systemu LMS** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemu LMS

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą narzędzia do ochrony systemu LMS przy użyciu użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w systemie LMS.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu LMS w systemie, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie ustawień logowania JEDNOkrotnego](#configure-absorb-lms-sso)** dla systemu LMS w celu skonfigurowania ustawienia rejestracji jednokrotnej na stronie aplikacji.
    1. **[Utwórz użytkownika testowego systemu LMS do testowania](#create-absorb-lms-test-user)** , aby uzyskać odpowiedniki B. Simon w systemie LMS, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji systemu LMS na platformie **wchłonąć** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    Jeśli korzystasz z **interfejsu użytkownika usługi Absorb 5**, użyj następującej konfiguracji:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Jeśli korzystasz ze **środowiska nowego ucznia usługi Absorb 5**, użyj następującej konfiguracji:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Absorb LMS](https://support.absorblms.com/hc/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**.

    ![image (obraz)](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie usługi Absorb LMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do systemu LMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **pochłanianie systemu LMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-absorb-lms-sso"></a>Konfigurowanie ochrony logowania jednokrotnego dla systemu LMS

1. W nowym oknie przeglądarki internetowej zaloguj się jako administrator do firmowej witryny usługi Absorb LMS.

2. Wybierz przycisk **Account** (Konto) w prawym górnym rogu.

    ![Przycisk Account (Konto)](./media/absorblms-tutorial/account.png)

3. W okienku Account (Konto) wybierz pozycję **Portal Settings** (Ustawienia portalu).

    ![Link Portal Settings (Ustawienia portalu)](./media/absorblms-tutorial/portal.png)

4. Wybierz kartę **Manage SSO Settings** (Zarządzaj ustawieniami logowania jednokrotnego).

    ![Karta Users (Użytkownicy)](./media/absorblms-tutorial/sso.png)

5. Na stronie **Manage Single Sign-On Settings** (Zarządzanie ustawieniami logowania jednokrotnego) wykonaj następujące czynności:

    ![Strona konfiguracji logowania jednokrotnego](./media/absorblms-tutorial/settings.png)

    a. W polu tekstowym **Name** (Nazwa) wprowadź nazwę, np. Logowanie jednokrotne witryny Azure AD Marketplace.

    b. W polu **Method** (Metoda) wybierz pozycję **SAML**.

    c. W Notatniku otwórz certyfikat pobrany z witryny Azure Portal. Usuń tagi **---BEGIN CERTIFICATE---** i **---END CERTIFICATE---**. Następnie w polu **Key** (Klucz) wklej pozostałą zawartość.

    d. W polu **Mode** (Tryb) wybierz pozycję **Identity Provider Initiated** (Inicjowane przez dostawcę tożsamości).

    e. W polu **Id Property** (Właściwość identyfikatora) wybierz atrybut, który został skonfigurowany jako identyfikator użytkownika w usłudze Azure AD. Na przykład jeśli wybrano pozycję *NameIdentifier* w usłudze Azure AD, wybierz pozycję **username**.

    f. W polu **Signature Type** (Typ podpisu) wybierz pozycję **Sha256**.

    przykład W polu **Login URL** (Adres URL logowania) wklej wartość pola **Adres URL na potrzeby uzyskiwania dostępu przez użytkowników** ze strony **Właściwości** aplikacji w witrynie Azure Portal.

    h. W polu **Logout URL** (Adres URL wylogowywania) wklej wartość pola **Adres URL wylogowywania** skopiowaną z okna **Konfigurowanie logowania** w witrynie Azure Portal.

    i. Przełącz opcję **Automatically Redirect** (Automatyczne przekierowywanie) na wartość **On** (Włączone).

6. Wybierz pozycję **Zapisz.**

    ![Przełącznik zezwalania na logowanie tylko za pomocą funkcji logowania jednokrotnego](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Tworzenie użytkownika testowego usługi Absorb LMS

Aby użytkownicy usługi Azure AD mogli zalogować się do usługi Absorb LMS, muszą być oni skonfigurowani w usłudze Absorb LMS. W przypadku usługi Absorb LMS aprowizacja to zadanie wykonywane ręcznie.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się jako administrator do firmowej witryny usługi Absorb LMS.

2. W okienku **Users** (Użytkownicy) wybierz pozycję **Users** (Użytkownicy).

    ![Link Users (Użytkownicy)](./media/absorblms-tutorial/users.png)

3. Wybierz kartę **User** (Użytkownik).

    ![Lista rozwijana Add New (Dodaj nowego)](./media/absorblms-tutorial/add.png)

4. Na stronie **Add User** (Dodawanie użytkownika) wykonaj następujące czynności:

    ![Strona Add User (Dodawanie użytkownika)](./media/absorblms-tutorial/user.png)

    a. W polu **First Name** (Imię) wpisz imię, np. **Britta**.

    b. W polu **Last Name** (Nazwisko) wpisz nazwisko, np. **Simon**.

    c. W polu **Username** (Nazwa użytkownika) wpisz imię i nazwisko, np. **Britta Simon**.

    d. W polu **Password** (Hasło) wpisz hasło użytkownika.

    e. W polu **Confirm Password** (Potwierdź hasło) ponownie wpisz hasło.

    f. Ustaw przełącznik **Is Active** (Jest aktywny) na wartość **Active** (Aktywny).

5. Wybierz pozycję **Zapisz.**

    ![Przełącznik zezwalania na logowanie tylko za pomocą funkcji logowania jednokrotnego](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Domyślnie aprowizacja użytkowników nie jest włączona w ramach logowania jednokrotnego. Jeśli klient chce włączyć tę funkcję, musi ją skonfigurować w sposób opisany w [tej](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentacji. Ponadto należy pamiętać, że aprowizacja użytkowników jest dostępna tylko w **środowisku nowego ucznia usługi Absorb 5** przy użyciu adresu URL usługi ACS: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do systemu LMS, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka systemu LMS w obszarze Moje aplikacje należy automatycznie zalogować się do systemu LMS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu systemu LMS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).