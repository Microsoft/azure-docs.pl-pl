---
title: 'Samouczek: integracja Azure Active Directory z Aha! | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Aha!.
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
ms.openlocfilehash: a8a33290c7fa904fc16b728a6e2411bd5b42683f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259670"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Samouczek: integracja z usługą z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Aha! z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Aha! za pomocą usługi Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Aha!.
* Zezwól użytkownikom na automatyczne logowanie do programu Aha! z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Aha! subskrypcja z włączonym logowaniem jednokrotnym (SSO).

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aha! Obsługa logowania jednokrotnego zainicjowane przez usługę **SP**
* Aha! obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="add-aha-from-the-gallery"></a>Dodaj Aha! z galerii

Aby skonfigurować integrację z programem Aha! w usłudze Azure AD musisz dodać Aha! z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Aha!** .
1. Wybierz pozycję **Aha!** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-aha"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Aha!

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Aha! przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Aha!.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Aha!, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Aha! Logowanie jednokrotne](#configure-aha-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz wersję Aha! użytkownika testowego](#create-aha-test-user)** , aby uzyskać odpowiednik elementu B. Simon w programie Aha! jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal, w witrynie **Aha!** Strona integracja aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.aha.io/session/new`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.aha.io`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z nami [! Zespół obsługi klienta](https://www.aha.io/company/contact) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na stronie **Konfiguracja Aha!** Skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Aha!.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Aha!**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-aha-sso"></a>Skonfiguruj Aha! Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach programu Aha!, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguracja Aha!** pokieruje Cię do Ciebie! Aplikacja. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu Aha!. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować Aha! ręcznie Otwórz nowe okno przeglądarki sieci Web i zaloguj się do Ciebie. Lokacja firmy jako administrator i wykonaj następujące czynności:

4. W menu u góry kliknij pozycję **Settings** (Ustawienia).

    ![Ustawienia](./media/aha-tutorial/setting.png "Ustawienia")

5. Kliknij pozycję **Account** (Konto).

    ![Profil](./media/aha-tutorial/account.png "Profil")

6. Kliknij pozycję **Security and single sign-on** (Zabezpieczenia i logowanie jednokrotne).

    ![Zrzut ekranu, który podświetla opcję menu zabezpieczenia i logowanie jednokrotne.](./media/aha-tutorial/security.png "Zabezpieczenia i logowanie jednokrotne")

7. W sekcji **Single Sign-On** (Logowanie jednokrotne) wybierz w polu **Identity Provider** (Dostawca tożsamości) opcję **SAML2.0**.

    ![Zabezpieczenia i logowanie jednokrotne](./media/aha-tutorial/saml.png "Zabezpieczenia i logowanie jednokrotne")

8. Na stronie konfiguracji **Single Sign On** (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/aha-tutorial/sso.png "Logowanie jednokrotne")

    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.

    b. W polu **Configure using** (Konfiguruj za pomocą) wybierz opcję **Metadata File** (Plik metadanych).

    c. Aby przekazać pobrany plik metadanych, kliknij pozycję **Browse** (Przeglądaj).

    d. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-aha-test-user"></a>Utwórz Aha! użytkownik testowy

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Aha!. Aha! obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Aha!, zostanie on utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do programu Aha! Adres URL logowania, w którym można zainicjować przepływ logowania. 

* Przejdź do programu Aha! Bezpośrednio Zaloguj się adres URL i zainicjuj przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu przycisku Aha! kafelek w moich aplikacjach zostanie przekierowany do programu Aha! Adres URL logowania. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Aha! można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
