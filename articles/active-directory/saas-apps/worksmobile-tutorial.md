---
title: 'Samouczek: integracja Azure Active Directory z usługą LINE WORKS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i WIERSZem działania.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: a8b344cd9e4428c3508534edaec4fd98fb67d832
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182048"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Samouczek: integracja Azure Active Directory z WIERSZem działa

Z tego samouczka dowiesz się, jak zintegrować usługę LINE z usługą Azure Active Directory (Azure AD).
Integracja LINIOWa współpracuje z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do programu LINE WORKS.
* Możesz zezwolić użytkownikom na automatyczne logowanie do usługi LINE WORKS (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu LINE, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* WIERSZ obsługuje subskrypcję z włączoną rejestracją jednokrotną

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa LINE WORKS obsługuje usługę zainicjowana przez usługę **SP** SSO

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-line-works-from-the-gallery"></a>Dodawanie wierszy działa z galerii

Aby skonfigurować integrację linii z usługą Azure AD, musisz dodać wiersz działania z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **wiersz Works** w polu wyszukiwania.
1. Wybierz pozycję **wiersz działa** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD za pomocą wiersza działa przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w wierszu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu LINE, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj wiersz Logowanie jednokrotne](#configure-line-works-sso)** — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
    * **[Utwórz wiersz Works test użytkownika](#create-line-works-test-user)** — Aby uzyskać odpowiednik Britta Simon w wierszu działa, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal, na stronie **wiersz** integracja aplikacji, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://auth.worksmobile.com/d/login/<domain>/`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca: `https://auth.worksmobile.com/acs/ <domain>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistym adresem URL Sign-On i adresem URL odpowiedzi. [Linia kontaktowa współpracuje z zespołem pomocy technicznej](https://line.worksmobile.com/jp/en/contactus/) w celu uzyskania wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie programu Works** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do programu LINE.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **wiersz działa**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-line-works-sso"></a>Skonfiguruj wiersz Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne w **wierszu** , zapoznaj się z [wierszem Uruchom dokumenty logowania jednokrotnego](https://developers.worksmobile.com/jp/document/1001080101) i skonfiguruj ustawienie line Works.

> [!NOTE]
> Należy skonwertować pobrany plik certyfikatu z. CERT na. pem


### <a name="create-line-works-test-user"></a>Utwórz użytkownika testowego programu LINE

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w wierszu działa. [Stronę Administracja wiersz](https://admin.worksmobile.com) dostępu programu Access i Dodawanie użytkowników w wierszu Works platform.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania do wiersza, gdzie można zainicjować przepływ logowania. 

2. Przejdź do pozycji wiersz adres URL logowania bezpośrednio i zainicjuj tam przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka LINE WORKS w panelu dostępu zostanie on przekierowany do adresu URL logowania w wierszu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wiersza programu można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).