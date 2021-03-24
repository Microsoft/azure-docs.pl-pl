---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pojedynczą Sign-Oną kamień Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i samoobsługowego logowania jednokrotnego.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/09/2021
ms.author: jeedes
ms.openlocfilehash: f7167df523ca6f84eacd92fc7af1011e8b3b00b6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z pojedynczą Sign-Oną kamień

W tym samouczku dowiesz się, jak zintegrować pojedyncze Sign-On z Azure Active Directory (Azure AD). W przypadku integrowania pojedynczej Sign-On z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do jednolite logowania jednokrotnego.
* Zezwól użytkownikom na automatyczne logowanie się do jednego Sign-Onu, w którym można korzystać z kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Samoobsługowa pojedyncza Sign-On subskrypcję przy użyciu logowania jednokrotnego (SSO).

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pojedyncze Sign-Onowe są obsługiwane przez usługę **SP** zainicjowaną przez usługę SSO.
* Pojedyncze Sign-Onowe są obsługiwane przez [Automatyczne Inicjowanie obsługi użytkowników](cornerstone-ondemand-provisioning-tutorial.md).


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Dodawanie Sign-On pojedynczego kamień z galerii

Aby skonfigurować integrację pojedynczej Sign-On z usługą Azure AD, należy dodać pojedyncze Sign-On z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz opcję jednokrotne **Logowanie** w polu wyszukiwania.
1. Wybierz pozycję **wylogowanie jednokrotne w** panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pojedynczej Sign-On

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pojedynczej Sign-On przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w jednolite logowanie jednokierunkowe.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednolite, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj kamień węgielny single Sign-On Logowanie jednokrotne](#configure-cornerstone-single-sign-on-sso)** — aby skonfigurować ustawienia pojedynczego Sign-On po stronie aplikacji.
    1. **[Utwórz kamień węgielny single Sign-On test](#create-cornerstone-single-sign-on-test-user)** , aby uzyskać odpowiednika B. Simon w jednej stawce Sign-On, która jest połączona z reprezentacją usługi Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja jednokrotnego **logowania** do aplikacji, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<PORTAL_NAME>.csod.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistym adresem URL odpowiedzi, identyfikatorem i adresem URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Single Sign-On Client support](mailto:moreinfo@csod.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie jednolite logowanie** jednokrotne skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do jednolite logowania jednokrotnego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy Aplikacje wybierz pozycję **stawka** jednokrotne logowanie.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cornerstone-single-sign-on-sso"></a>Konfigurowanie pojedynczych Sign-On Logowanie jednokrotne

1. Zaloguj się do pojedynczej Sign-On, jako administrator.

1. Przejdź do **narzędzia > administratora**.

    ![Strona screeenshot dla administratora.](./media/cornerstone-ondemand-tutorial/admin.png)

1. Wybierz pozycję Panel **krawędzi** w **Narzędzia konfiguracji**.

    ![screeenshot dla panelu krawędzi.](./media/cornerstone-ondemand-tutorial/edge-panel.png)

1. Wybierz pozycję Single Sign-On w sekcji **Integruj** .

    ![screeenshot dla opcji Single Sign-On.](./media/cornerstone-ondemand-tutorial/single-sign-on.png)

1. Kliknij przycisk **Dodaj Logowanie jednokrotne** . W poniższym wyświetlonym oknie podręcznym wybierz pozycję **przychodzące SAML** , a następnie kliknij przycisk **Dodaj**.

    ![screeenshot dla przychodzącego protokołu SAML.](./media/cornerstone-ondemand-tutorial/inbound.png)

1. Wykonaj poniższe kroki na poniższej stronie:

    ![Sekcja screeenshot for Configuration dla elementu "kamień".](./media/cornerstone-ondemand-tutorial/configuration.png)

    a. We **właściwościach ogólnych** kliknij opcję **Przekaż plik** , aby przekazać plik **certyfikatu (base64)** , który został pobrany z Azure Portal.

    b. Zaznacz pole wyboru **Włącz** i w polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Utwórz jednolite Sign-On użytkownika testowego

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w pojedynczej jednolite logowanie. Pojedyncze Sign-Onowe są obsługiwane przez automatyczne Inicjowanie obsługi użytkowników, co jest domyślnie włączone. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](./cornerstone-ondemand-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do pojedynczej Sign-On, jako administrator.

1. Przejdź do obszaru **administrator > użytkownicy** i kliknij pozycję **Dodaj użytkownika** w dolnej części strony.

    ![screeenshot do tworzenia podstaw dla użytkowników testowych.](./media/cornerstone-ondemand-tutorial/user-1.png)

1. Wypełnij pola wymagane na stronie **Dodaj nowy użytkownik** i kliknij przycisk **Zapisz**.

    ![screeenshot do tworzenia użytkowników testowych z wymaganymi polami.](./media/cornerstone-ondemand-tutorial/user-2.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania jednokrotnego Sign-On, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do pojedynczego adresu URL logowania jednoSign-Onowego i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka pojedynczej Sign-On w obszarze Moje aplikacje zostanie przekierowany na adres URL logowania jednoSign-Onowego. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pojedynczej Sign-On można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)