---
title: 'Samouczek: integracja Azure Active Directory z tablicami uczyć się-Shibboleth | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Blackboard Learn — Shibboleth.
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
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649909"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Samouczek: integracja Azure Active Directory z tablicami uczyć się — Shibboleth

W tym samouczku dowiesz się, jak zintegrować z platformą Shibboleth z usługą Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD informacji o tablicach Shibboleth można:

* Kontrolka w usłudze Azure AD, która ma dostęp do informacji o tablicach Shibboleth.
* Zezwól użytkownikom na automatyczne logowanie się, aby uzyskać informacje na temat Shibboleth z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Tablica zawiera informacje o subskrypcji z obsługą logowania jednokrotnego (SSO) Shibboleth.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Blackboard Learn — Shibboleth obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Dodaj tablica nauka — Shibboleth z galerii

Aby skonfigurować integrację rozwiązania Blackboard Learn — Shibboleth z usługą Azure AD, należy dodać rozwiązanie Blackboard Learn — Shibboleth z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Tablica nauka — Shibboleth** w polu wyszukiwania.
1. Wybierz pozycję **Tablica nauka — Shibboleth** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD w celu poznania informacji — Shibboleth

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji Tablica-Shibboleth przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w tablicach uczenie — Shibboleth.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Shibboleth, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj informacje o usłudze tablica-Shibboleth Logowanie jednokrotne](#configure-blackboard-learn---shibboleth-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz tablica Uczenie się — Shibboleth test użytkownika](#create-blackboard-learn---shibboleth-test-user)** — Aby uzyskać odpowiednik B. Simon w tablicach informacje-Shibboleth, które są połączone z usługą Azure AD reprezentacją użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w rozwiązaniu Blackboard Learn — Shibboleth, wykonaj następujące kroki:

1. W Azure Portal na stronie **Rozpoznaj się** z integracją aplikacji Shibboleth wybierz pozycję **Logowanie jednokrotne**.

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

3. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę ołówka, aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania Blackboard Learn — Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Skonfiguruj rozwiązanie Blackboard Learn — Shibboleth** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do tablicami uczyć się-Shibboleth.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Blackboard Learn — Shibboleth**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Konfigurowanie informacji o tablicach — Shibboleth Logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne po stronie rozwiązania **Blackboard Learn — Shibboleth**, musisz wysłać pobrany **kod XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej rozwiązania Blackboard Learn — Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Tworzenie użytkownika testowego rozwiązania Blackboard Learn — Shibboleth

W tej sekcji utworzysz użytkownika Britta Simon w rozwiązaniu Blackboard Learn — Shibboleth. Współpracuj z [zespołem pomocy technicznej — Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) , aby dodać użytkowników z platformy tablica Uczenie Shibboleth. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Shibboleth adres URL logowania, w którym można zainicjować przepływ logowania. 

* Przejdź do pozycji informacje o tablicach adres URL logowania do Shibboleth bezpośrednio i zainicjuj w tym miejscu przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka nauka uczenie Shibboleth w obszarze Moje aplikacje należy automatycznie zalogować się do tej tabeli, aby poznać konfigurację logowania jednokrotnego. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu tabeli informacje o Shibboleth można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).