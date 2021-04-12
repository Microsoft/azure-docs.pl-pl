---
title: 'Samouczek: integracja Azure Active Directory z nowymi Relic według konta | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a nowym Relic przez konto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649974"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z nowym Relic według konta

W tym samouczku dowiesz się, jak zintegrować nowe Relic przez konto z usługą Azure Active Directory (Azure AD). Po zintegrowaniu nowego Relic przez konto z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do nowego Relic według konta.
* Zezwól użytkownikom na automatyczne logowanie do nowego Relic przez konto przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Relic.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa New Relic by Account obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-new-relic-by-account-from-the-gallery"></a>Dodawanie nowego Relic przez konto z galerii

Aby skonfigurować integrację nowych Relic według konta z usługą Azure AD, musisz dodać nowe Relic przez konto z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **nowe Relic według konta** w polu wyszukiwania.
1. Wybierz pozycję **Nowy Relic wg konta** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla nowego Relic według konta

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą nowego Relic przez konto przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w nowym Relic według konta.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu nowego Relic przez konto, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj nowe Relic za pomocą logowania jednokrotnego dla konta](#configure-new-relic-by-account-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz nowy Relic według konta użytkownika testowego](#create-new-relic-by-account-test-user)** , aby uzyskać odpowiednik B. Simon w nowych Relic według konta, które jest połączone z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie Nowa integracja aplikacji **Relic przez konto** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)
   
1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` — Pamiętaj `acc_id` o zastąpieniu własnego identyfikatora konta nowym Relic według konta.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `rpm.newrelic.com`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Na stronie **Konfigurowanie nowego Relic według konta** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do nowego Relic przez konto.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **nowe Relic według konta**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola &quot;domyślny dostęp&quot;.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name=&quot;configure-new-relic-by-account-sso&quot;></a>Konfigurowanie nowego Relic przez logowanie jednokrotne za pomocą konta

1. W innym oknie przeglądarki sieci Web Zaloguj się do nowej witryny **Relic przez konto** firmowe jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Zrzut ekranu przedstawia stronę powitalną z wybranymi ustawieniami konta.](./media/new-relic-tutorial/settings.png &quot;Ustawienia konta")

3. Kliknij kartę **zabezpieczenia i uwierzytelnianie** , a następnie kliknij kartę **Logowanie** jednokrotne.
   
    ![Logowanie jednokrotne](./media/new-relic-tutorial/single-sign-on-tab.png "Logowanie jednokrotne")

4. Na stronie okno dialogowe SAML wykonaj następujące czynności:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany certyfikat Azure Active Directory.

    b. W polu tekstowym **adres URL logowania zdalnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.
   
    c. W polu tekstowym **Wyloguj adres URL** , wklej wartość **adres URL wylogowania**, który został skopiowany z Azure Portal.

    d. Kliknij przycisk **Zapisz moje zmiany**.

### <a name="create-new-relic-by-account-test-user"></a>Utwórz nowego Relic według konta użytkownika testowego

1. Zaloguj się do **nowej witryny Relic przez konto** firmowe jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.
   
    ![Zrzut ekranu przedstawia ustawienia konta wybrane na stronie powitalnej.](./media/new-relic-tutorial/account.png "Ustawienia konta")

3. W okienku **konto** po lewej stronie kliknij pozycję **Podsumowanie**, a następnie kliknij pozycję **Dodaj użytkownika**.
   
    ![Zrzut ekranu przedstawia okienko Podsumowanie, w którym można wybrać pozycję Dodaj użytkownika.](./media/new-relic-tutorial/add.png "Ustawienia konta")

4. W oknie dialogowym **aktywni użytkownicy** wykonaj następujące czynności:
   
    ![Aktywni użytkownicy](./media/new-relic-tutorial/user.png "Aktywni użytkownicy")
   
    a. W polu tekstowym **adres e-mail** wpisz adres e-mail prawidłowego użytkownika Azure Active Directory, który chcesz udostępnić.

    b. Jako **rola** wybierz **użytkownika**.

    c. Kliknij pozycję **Dodaj tego użytkownika**.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można korzystać z innych nowych Relic według narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnianych przez nowe konto.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do nowego Relic za pomocą adresu URL logowania do konta, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do pozycji nowy Relic według adresu URL logowania do konta, a następnie zainicjuj w tym miejscu przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka nowy Relic według konta w obszarze Moje aplikacje zostanie on przekierowany do nowego Relic przy użyciu adresu URL logowania do konta. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu nowego Relic przez konto można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).