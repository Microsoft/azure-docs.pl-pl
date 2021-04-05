---
title: 'Samouczek: integracja Azure Active Directory ze kanwą | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653060"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Samouczek: integracja Azure Active Directory ze kanwą

W tym samouczku dowiesz się, jak zintegrować kanwę z Azure Active Directory (Azure AD). Gdy integrujesz kanwę z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do kanwy.
* Zezwól użytkownikom na automatyczne logowanie do kanwy przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) na kanwie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Canvas obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-canvas-from-the-gallery"></a>Dodawanie kanwy z galerii

Aby skonfigurować integrację aplikacji Canvas z usługą Azure AD, musisz dodać aplikację Canvas z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **kanwę** w polu wyszukiwania.
1. Wybierz pozycję **Kanwa** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla kanwy

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą kanwy przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na kanwie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą kanwy, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania jednokrotnego dla kanwy](#configure-canvas-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego na kanwie](#create-canvas-test-user)** , aby uzyskać odpowiedniki B. Simon na kanwie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **kanwy** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Canvas — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.instructure.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta Canvas](https://community.canvaslms.com/community/help). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Skonfiguruj aplikację Canvas** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz funkcję B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do kanwy.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Canvas**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-canvas-sso"></a>Konfigurowanie logowania jednokrotnego dla kanwy

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Canvas jako administrator.

2. Przejdź do pozycji **Kursy \> Konta zarządzane \> Microsoft**.

    ![Kanwa](./media/canvas-lms-tutorial/ic775990.png "Kanwa")

3. W okienku nawigacji po lewej stronie wybierz pozycję **Uwierzytelnianie**, a następnie kliknij pozycję **Dodaj nową konfigurację SAML**.

    ![Authentication](./media/canvas-lms-tutorial/ic775991.png "Authentication")

4. Na stronie Bieżąca integracja wykonaj następujące kroki:

    ![Bieżąca integracja](./media/canvas-lms-tutorial/save.png "Bieżąca integracja")

    a. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Link do zmiany hasła** wklej wartość **linku do zmiany hasła** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **Odcisk palca certyfikatu** wklej wartość **odcisku palca** certyfikatu skopiowaną z witryny Azure Portal.

    f. Z listy **Atrybut logowania** wybierz pozycję **NameID**.

    przykład Z listy **Format identyfikatora** wybierz pozycję **emailAddress**.

    h. Kliknij pozycję **Zapisz ustawienia uwierzytelniania**.

### <a name="create-canvas-test-user"></a>Tworzenie użytkownika testowego aplikacji Canvas

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Canvas, muszą być oni aprowizowani w tej aplikacji. W przypadku aplikacji Canvas aprowizowanie użytkowników wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy aplikacji **Canvas**.

2. Przejdź do pozycji **Kursy \> Konta zarządzane \> Microsoft**.

   ![Kanwa](./media/canvas-lms-tutorial/ic775990.png "Kanwa")

3. Kliknij pozycję **Użytkownicy**.

   ![Zrzut ekranu przedstawia menu kanwy z wybranymi użytkownikami.](./media/canvas-lms-tutorial/ic775995.png "Użytkownicy")

4. Kliknij pozycję **Dodaj nowego użytkownika**.

   ![Zrzut ekranu przedstawia przycisk Dodaj nowego użytkownika.](./media/canvas-lms-tutorial/ic775996.png "Użytkownicy")

5. W oknie dialogowym Dodawanie nowego użytkownika wykonaj następujące kroki:

   ![Dodaj użytkownika](./media/canvas-lms-tutorial/ic775997.png "Dodaj użytkownika")

   a. W polu tekstowym **Imię i nazwisko** wprowadź nazwę użytkownika, na przykład **BrittaSimon**.

   b. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **brittasimon \@ contoso.com**.

   c. W polu tekstowym **Login (logowanie** ) wprowadź adres E-mail użytkownika usługi Azure AD, taki jak **brittasimon \@ contoso.com**.

   d. Wybierz pozycję **Poinformuj użytkownika pocztą e-mail o utworzeniu tego konta**.

   e. Kliknij pozycję **Dodaj użytkownika**.

> [!NOTE]
> Możesz użyć innych narzędzi do tworzenia kont użytkowników kanwy lub interfejsów API udostępnionych przez kanwę do udostępniania kont użytkowników usługi Azure AD.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do obszaru roboczego, w którym można zainicjować przepływ logowania. 

* Bezpośrednio przejdź do adresu URL logowania kanwy i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka kanwy w obszarze Moje aplikacje należy automatycznie zalogować się do kanwy, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu kanwy można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).