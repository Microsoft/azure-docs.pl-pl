---
title: 'Samouczek: integracja Azure Active Directory z adaptacyjnymi wglądami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i adaptacyjne szczegółowe informacje.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649091"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Samouczek: Integrowanie adaptacyjnych szczegółowych informacji za pomocą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować adaptacyjne szczegółowe informacje z Azure Active Directory (Azure AD). Gdy integrujesz szczegółowe informacje z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do szczegółowych informacji.
* Umożliwia użytkownikom automatyczne logowanie się w celu adaptacyjnego wglądu w szczegółowe dane przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją adaptacyjnego wglądu w szczegółowe dane.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Adaptacyjne wglądy w dane obsługują zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne

## <a name="add-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjnych szczegółowych informacji z galerii

Aby skonfigurować integrację adaptacyjnego wglądu w usługę Azure AD, musisz dodać adaptacyjne szczegółowe informacje z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **adaptacyjne szczegółowe** dane w polu wyszukiwania.
1. Wybierz pozycję **adaptacyjne szczegółowe** dane z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w celu dostosowania szczegółowych informacji

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z adaptacyjnym wglądem przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oknie adaptacyjne szczegółowe dane.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą adaptacyjnego wglądu, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj adaptacyjne logowanie JEDNOkrotne](#configure-adaptive-insights-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego adaptacyjnego wglądu w szczegółowe](#create-adaptive-insights-test-user)** dane, aby uzyskać odpowiedniki B. Simon w adaptacyjnych wglądach, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie z integracją **propoznawczej** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Możesz uzyskać informacje o identyfikatorze (identyfikator jednostki) i adresie URL odpowiedzi ze strony **ustawień protokołu SAML SSO** usługi adaptacyjnej.

4. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie usługi adaptacyjnej Insights** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do szczegółowych informacji.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **adaptacyjne szczegółowe** dane.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-adaptive-insights-sso"></a>Konfigurowanie rejestracji jednokrotnej usługi adaptacyjnej Insights

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy z możliwością adaptacyjnej usługi Insights jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

    ![Zrzut ekranu, który wyróżnia administrację w panelu nawigacji.](./media/adaptivesuite-tutorial/administration.png "Administracja")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Ustawienia logowania jednokrotnego** dla protokołu SAML.

    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/settings.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

4. Na stronie **Ustawienia rejestracji jednokrotnej protokołu SAML** wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/saml.png "Ustawienia logowania jednokrotnego SAML")

    a. W polu tekstowym **Nazwa dostawcy tożsamości** wpisz nazwę konfiguracji.

    b. Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal do pola tekstowego **Identyfikator jednostki dostawcy tożsamości** .

    c. Wklej wartość **adresu URL logowania** skopiowaną z Azure Portal do pola tekstowego **adres URL logowania jednokrotnego dostawcy tożsamości** .

    d. Wklej wartość **adresu URL wylogowywania** skopiowaną z Azure Portal do pola tekstowego **adres URL wylogowywania niestandardowego** .

    e. Aby przekazać pobrany certyfikat, kliknij pozycję **Wybierz plik**.

    f. Wybierz następujące polecenie, aby:

     * **Identyfikator użytkownika SAML**, wybierz **nazwę użytkownika adaptacyjnego wglądu w dane użytkownika**.

     * **Lokalizacja identyfikatora użytkownika SAML**, wybierz pozycję **Identyfikator użytkownika w NameID tematu**.

     * **Format SAML NameID**, wybierz opcję **adres e-mail**.

     * **Włącz protokół SAML**, wybierz opcję **Zezwalaj na logowanie za pomocą rejestracji jednokrotnej i bezpośredniego adaptacyjnego wglądu w szczegółowe** dane.

    przykład Skopiuj **adres URL rejestracji jednokrotnej usługi adaptacyjnej Insights** i wklej do pól **identyfikatora (identyfikator jednostki)** i **adres URL odpowiedzi** w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

    h. Kliknij pozycję **Zapisz**.

### <a name="create-adaptive-insights-test-user"></a>Utwórz użytkownika testowego adaptacyjnego wglądu w szczegółowe dane

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu uzyskania adaptacyjnego wglądu, muszą one być obsługiwane w ramach adaptacyjnego wglądu w szczegółowe dane. W przypadku adaptacyjnych szczegółowych informacji Inicjowanie obsługi jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej witryny usługi **adaptacyjnej Insights** jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

   ![Administracja](./media/adaptivesuite-tutorial/administration.png "Administracja")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Użytkownicy**.

   ![Dodaj użytkownika](./media/adaptivesuite-tutorial/users.png "Dodaj użytkownika")

4. W sekcji **nowy użytkownik** wykonaj następujące czynności:

   ![Prześlij](./media/adaptivesuite-tutorial/new.png "Prześlij")

   a. Wpisz **nazwę**, nazwa **użytkownika**, **adres e-mail** i **hasło** prawidłowego użytkownika Azure Active Directory, który chcesz udostępnić do powiązanych pól tekstowych.

   b. Wybierz **rolę**.

   c. Kliknij przycisk **Prześlij**.

> [!NOTE]
> Możesz użyć dowolnych innych narzędzi do tworzenia kont użytkowników i interfejsów API z możliwością adaptacyjnego wglądu w szczegółowe dane, aby udostępnić konta użytkowników usługi Azure AD.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do usługi adaptacyjnej szczegółowej, dla której skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka adaptacyjnego wglądu w moich aplikacjach należy automatycznie zalogować się do usługi adaptacyjnych szczegółowych informacji, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu adaptacyjnego wglądu w szczegółowe dane można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).