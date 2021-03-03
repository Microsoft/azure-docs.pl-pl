---
title: 'Samouczek: integracja Azure Active Directory z usługą Lucidchart | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651584"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Lucidchart

W tym samouczku dowiesz się, jak zintegrować usługę Lucidchart z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Lucidchart z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Lucidchart.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Lucidchart przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Lucidchart.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Lucidchart obsługuje logowanie jednokrotne w usłudze **SP**
* Lucidchart obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="add-lucidchart-from-the-gallery"></a>Dodaj Lucidchart z galerii

Aby skonfigurować integrację programu Lucidchart z usługą Azure AD, musisz dodać Lucidchart z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Lucidchart** w polu wyszukiwania.
1. Wybierz pozycję **Lucidchart** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Lucidchart

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Lucidchart przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Lucidchart.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Lucidchart, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-lucidchart-sso)** w usłudze Lucidchart, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Lucidchart](#create-lucidchart-test-user)** , aby dysponować odpowiednikiem B. Simon w Lucidchart, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Lucidchart** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

   W polu tekstowym **adres URL logowania** wpisz adres URL w postaci:  `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie Lucidchart** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Lucidchart.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Lucidchart**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-lucidchart-sso"></a>Konfigurowanie logowania jednokrotnego Lucidchart

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Lucidchart jako administrator.

2. W menu u góry kliknij pozycję **zespół**.

    ![Zespół](./media/lucidchart-tutorial/ic791190.png "Zespół")

3. Kliknij kolejno pozycje **aplikacje \> Zarządzanie SAML**.

    ![Zarządzanie SAML](./media/lucidchart-tutorial/ic791191.png "Zarządzanie SAML")

4. Na stronie **Ustawienia uwierzytelniania SAML** wykonaj następujące czynności:

    a. Wybierz pozycję **Włącz uwierzytelnianie SAML**, a następnie kliknij pozycję **opcjonalne**.

    ![Ustawienia uwierzytelniania SAML](./media/lucidchart-tutorial/ic791192.png "Ustawienia uwierzytelniania SAML")

    b. W polu tekstowym **domena** wpisz domenę, a następnie kliknij przycisk **Zmień certyfikat**.

    ![Zmień certyfikat](./media/lucidchart-tutorial/ic791193.png "Zmień certyfikat")

    c. Otwórz pobrany plik metadanych, skopiuj zawartość, a następnie wklej ją do pola tekstowego **przekazywanie metadanych** .

    ![Przekazywanie metadanych](./media/lucidchart-tutorial/ic791194.png "Przekazywanie metadanych")

    d. Wybierz pozycję **automatycznie Dodaj nowych użytkowników do zespołu**, a następnie kliknij przycisk **Zapisz zmiany**.

    ![Zapisz zmiany](./media/lucidchart-tutorial/ic791195.png "Zapisz zmiany")

### <a name="create-lucidchart-test-user"></a>Utwórz użytkownika testowego Lucidchart

Nie ma elementu akcji, aby skonfigurować Inicjowanie obsługi administracyjnej użytkowników do Lucidchart.  Gdy przypisany użytkownik próbuje zalogować się do usługi Lucidchart przy użyciu panelu dostępu, Lucidchart sprawdza, czy użytkownik istnieje.  

Jeśli nie ma jeszcze dostępnego konta użytkownika, jest on automatycznie tworzony przez Lucidchart.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Lucidchart, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Lucidchart i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Lucidchart w obszarze Moje aplikacje należy automatycznie zalogować się do Lucidchart, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

 Po skonfigurowaniu Lucidchart można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)