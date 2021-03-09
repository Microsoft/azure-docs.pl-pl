---
title: 'Samouczek: integracja Azure Active Directory z usługą Dropbox Business | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługą Dropbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d4c9d2e290f6a8f07878a019b755c8add2ab69ea
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488448"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Samouczek: Integrowanie usługi Dropbox Business z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Dropbox Business z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Dropbox Business z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Dropbox.
* Zezwól użytkownikom na automatyczne logowanie do usługi Dropbox firmy przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w usłudze Dropbox.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

* W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Dropbox firmy obsługuje logowanie jednokrotne z użyciem usługi **SP** .

* Usługa Dropbox firmy obsługuje [Automatyczne Inicjowanie obsługi użytkowników i](dropboxforbusiness-tutorial.md)cofanie aprowizacji.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-dropbox-business-from-the-gallery"></a>Dodaj firmę Dropbox z galerii

Aby skonfigurować integrację usługi Dropbox z usługą Azure AD, musisz dodać usługę Dropbox z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Dropbox Business** w polu wyszukiwania.
1. Wybierz pozycję **Dropbox Business** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD dla usługi Dropbox

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Dropbox Business przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Dropbox Business.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Dropbox Business, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.    
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-dropbox-business-sso)** w usłudze Dropbox, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika usługi Dropbox Business testowego](#create-dropbox-business-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w usłudze Dropbox Business, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji usługi **Dropbox dla firm** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.dropbox.com/sso/<id>`
    
     b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `Dropbox`
    
    > [!NOTE]
    > **Identyfikator logowania JEDNOkrotnego** dla usługi Dropbox można znaleźć w witrynie Dropbox w obszarze usługi Dropbox > konsoli administracyjnej > ustawienia > logowania jednokrotnego > adresem URL logowania jednoosobowego.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie firmy usługi Dropbox** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Dropbox.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Dropbox Business**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-dropbox-business-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Dropbox

1. Aby zautomatyzować konfigurację w usłudze Dropbox Business, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij kolejno pozycje **konfiguracja usługa Dropbox firma** przekieruje Cię do aplikacji usługi Dropbox. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Dropbox. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować usługi Dropbox firmy ręcznie, Otwórz nowe okno przeglądarki sieci Web i przejdź do dzierżawy biznesowej usługi Dropbox i zaloguj się do dzierżawy biznesowej usługi Dropbox. i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający stronę "Logowanie w usłudze Dropbox".](./media/dropboxforbusiness-tutorial/account.png "Konfigurowanie logowania jednokrotnego")

4. Kliknij **ikonę użytkownika** i wybierz kartę **Settings** (Ustawienia).

    ![Zrzut ekranu pokazujący akcję "ikona użytkownika" i "Ustawienia" wybrane.](./media/dropboxforbusiness-tutorial/configure-1.png "Konfigurowanie logowania jednokrotnego")

5. W okienku nawigacji po lewej stronie kliknij pozycję **Admin console** (Konsola administracyjna).

    ![Zrzut ekranu przedstawiający wybraną wartość "Konsola administracyjna".](./media/dropboxforbusiness-tutorial/configure-2.png "Konfigurowanie logowania jednokrotnego")

6. W obszarze **Admin console** (Konsola administracyjna) kliknij pozycję **Settings** (Ustawienia) w okienku nawigacji po lewej stronie.

    ![Zrzut ekranu pokazujący wybrane ustawienia.](./media/dropboxforbusiness-tutorial/configure-3.png "Konfigurowanie logowania jednokrotnego")

7. Wybierz opcję **Single sign-on** (Logowanie jednokrotne) w sekcji **Authentication** (Uwierzytelnianie).

    ![Zrzut ekranu przedstawiający sekcję "Authentication" z wybraną pozycją "Logowanie jednokrotne".](./media/dropboxforbusiness-tutorial/configure-4.png "Konfigurowanie logowania jednokrotnego")

8. W sekcji **Logowanie** jednokrotne wykonaj następujące czynności:  

    ![Zrzut ekranu pokazujący ustawienia konfiguracji logowania jednokrotnego.](./media/dropboxforbusiness-tutorial/configure-5.png "Konfigurowanie logowania jednokrotnego")

    a. Wybierz pozycję **wymagane** jako opcję z listy rozwijanej dla **logowania jednokrotnego**.

    b. Kliknij pozycję **Add sign-in URL** (Dodaj adres URL logowania), a następnie w polu tekstowym **Identity provider sign-in URL** (Adres URL logowania dostawcy tożsamości) wklej wartość **adresu URL logowania**, która została skopiowana z witryny Azure Portal, i wybierz pozycję **Done** (Gotowe).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/sso.png "Konfigurowanie logowania jednokrotnego")

    c. Kliknij pozycję **Upload certificate** (Przekaż certyfikat), a następnie przejdź do **pliku certyfikatu zakodowanego w formacie Base64**, który został pobrany z witryny Azure Portal.

    d. Kliknij **link Kopiuj** , a następnie wklej skopiowaną wartość w polu tekstowym **adres URL logowania** , w sekcji **domena biznesowa usługi Dropbox i adresy URL** na Azure Portal.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-dropbox-business-test-user"></a>Utwórz użytkownika testowego usługi Dropbox

W tej sekcji użytkownik o nazwie B. Simon został utworzony w usłudze Dropbox Business. Usługa Dropbox obsługuje funkcję aprowizacji użytkowników just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Dropbox, zostanie utworzony nowy po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z działem [pomocy technicznej usługi Dropbox Business Client](https://www.dropbox.com/business/contact) .

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi Dropbox, gdzie można zainicjować przepływ logowania. 

* Bezpośrednio przejdź do adresu URL logowania do usługi Dropbox i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka biznesowego usługi Dropbox w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do usługi Dropbox. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Dropbox można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).