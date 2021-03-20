---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z powiększeniem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: d105c83ba3db9502cf83f943dec6fcbfd5640d07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Zoom

W tym samouczku dowiesz się, jak zintegrować powiększenie z Azure Active Directory (Azure AD). Po zintegrowaniu powiększania z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do powiększenia.
* Umożliwia użytkownikom automatyczne logowanie się w celu powiększania przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Powiększ subskrypcję obsługującą Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Powiększenie obsługuje logowanie jednokrotne w programie **SP** i 
* Powiększenie obsługuje [ **zautomatyzowane** Inicjowanie obsługi użytkowników](./zoom-provisioning-tutorial.md).

## <a name="adding-zoom-from-the-gallery"></a>Dodawanie aplikacji Zoom z galerii

Aby skonfigurować integrację aplikacji Zoom w usłudze Azure AD, należy dodać aplikację Zoom z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **zoom** w polu wyszukiwania.
1. Wybierz pozycję **Powiększ** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w celu powiększania

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą powiększenia przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w powiększeniu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu powiększenia, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj powiększenie Logowanie jednokrotne](#configure-zoom-sso)** — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego powiększenia](#create-zoom-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w powiększeniu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Powiększ** integrację aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.zoom.us`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<companyname>.zoom.us`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoom](https://support.zoom.us/hc/) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie powiększenia** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

> [!NOTE]
> Aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD, zobacz [Konfigurowanie roszczeń ról wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> Powiększenie może oczekiwać wystąpienia grupy w ładunku SAML. Jeśli zostały utworzone wszystkie grupy, skontaktuj się z [zespołem pomocy technicznej powiększenia klienta](https://support.zoom.us/hc/) z informacjami o grupie, aby można było skonfigurować informacje o grupie na ich końcu. Należy również podać identyfikator obiektu, aby [powiększyć zespół obsługi klienta](https://support.zoom.us/hc/) , aby mógł on skonfigurować identyfikator obiektu na końcu. Aby uzyskać identyfikator obiektu, zobacz [Konfigurowanie powiększenia z platformą Azure](https://support.zoom.us/hc/articles/115005887566).

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

W tej sekcji włączysz funkcję B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do powiększenia.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **powiększenie**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zoom-sso"></a>Skonfiguruj powiększenie Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach powiększenia, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj powiększenie** spowoduje przekierowanie do aplikacji do powiększania. W tym miejscu podaj poświadczenia administratora, aby zalogować się do powiększenia. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować powiększenie ręcznie, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Powiększ jako administrator.

2. Kliknij kartę **Single Sign-On** (Logowanie jednokrotne).

    ![Karta Logowanie jednokrotne](./media/zoom-tutorial/zoom-sso1.png "Logowanie jednokrotne")

3. Kliknij kartę **Security Control** (Kontrola zabezpieczeń), a następnie przejdź do ustawień **Single Sign-On** (Logowanie jednokrotne).

4. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Sekcja logowania jednokrotnego](./media/zoom-tutorial/zoom-sso2.png "Logowanie jednokrotne")

    a. W polu tekstowym **Sign-in page URL** (Adres URL strony logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu **adres URL strony wylogowania** należy przejść do Azure Portal i kliknąć pozycję **Azure Active Directory** po lewej stronie, a następnie przejść do pozycji **rejestracje aplikacji**.

    ![Przycisk Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Kliknij **punkty końcowe**

    ![Przycisk punktu końcowego](./media/zoom-tutorial/endpoint.png)

    d. Skopiuj **punkt końcowy wylogowania protokołu SAML-P** i wklej go do pola tekstowego **adres URL strony wylogowania** .

    ![Przycisk kopiowania punktu końcowego](./media/zoom-tutorial/endpoint1.png)

    e. Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w zawartość w polu tekstowym **Certyfikat dostawcy tożsamości**.

    f. W polu tekstowym **wystawca** wklej wartość identyfikatora usługi **Azure AD** , który został skopiowany z Azure Portal. 

    przykład Kliknij przycisk **Zapisz zmiany**.

    > [!NOTE]
    > Aby uzyskać więcej informacji, zapoznaj się z dokumentacją powiększania [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Tworzenie użytkownika testowego aplikacji Zoom

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w powiększeniu. Powiększenie obsługuje automatyczne Inicjowanie obsługi użytkowników, które domyślnie jest włączone. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](./zoom-provisioning-tutorial.md).

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej powiększenia klienta](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie w celu pomniejszenia adresu URL logowania, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do pozycji Powiększ adres URL logowania i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka powiększenie w obszarze Moje aplikacje zostanie ono przekierowanie w celu pomniejszenia adresu URL logowania. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu powiększenia usługi Azure AD można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)