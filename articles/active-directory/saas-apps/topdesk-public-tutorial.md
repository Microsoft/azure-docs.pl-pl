---
title: 'Samouczek: integracja Azure Active Directory z usługą TOPdesk — publiczna | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TOPdesk-Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520442"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: integracja Azure Active Directory z usługą TOPdesk — Public

W tym samouczku dowiesz się, jak zintegrować usługę TOPdesk z usługą Azure Active Directory (Azure AD).
Integracja TOPdesk — publiczna z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi TOPdesk — publiczna.
* Możesz pozwolić użytkownikom na automatyczne logowanie do TOPdesk — publiczną (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą TOPdesk — publiczną, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk — subskrypcja z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TOPdesk — publiczna usługa **SP** zainicjowała Logowanie jednokrotne

## <a name="adding-topdesk---public-from-the-gallery"></a>Dodawanie TOPdesk — publiczna z galerii

Aby skonfigurować integrację usługi TOPdesk — publiczną w usłudze Azure AD, należy dodać TOPdesk — publiczną z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TOPdesk — publiczną z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TOPdesk-Public**, wybierz pozycję **TOPdesk — Public** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![TOPdesk — publiczna na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą TOPdesk-Public na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TOPdesk-Public.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi TOPdesk — Public, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj TOPdesk — publiczne logowanie](#configure-topdesk---public-single-sign-on)** jednokrotne — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego TOPdesk —](#create-topdesk---public-test-user)** Aby uzyskać odpowiednik Britta Simon w TOPdesk-Public, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą TOPdesk-Public, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **TOPdesk-publiczną** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4.  W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >**Plik metadanych dostawcy usług** zostanie pobrany z sekcji **Konfiguracja TOPdesk — publiczne Logowanie jednokrotne** , która została omówiona w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.
    
    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![TOPdesk — informacje o rejestracji jednokrotnej w domenie publicznej i adresach URL](common/sp-identifier-reply.png)

    d. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net`

    e. W polu tekstowym **adres URL identyfikatora** wprowadź adres URL metadanych TOPdesk, który można pobrać z konfiguracji TOPdesk. Powinien on używać następującego wzorca: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie są automatycznie wypełniane, należy wprowadzić je ręcznie. W przypadku identyfikatora należy postępować zgodnie z wzorcem wymienionym powyżej i uzyskać wartość adresu URL odpowiedzi z sekcji **Konfigurowanie TOPdesk — publiczna Rejestracja jednokrotna** , która została omówiona w dalszej części tego samouczka. Wartość **adresu URL logowania** nie jest prawdziwa, dlatego należy zaktualizować wartość przy użyciu rzeczywistego adresu URL Sign-On. Contact [TOPdesk — zespół pomocy technicznej dla klientów publicznych](https://help.topdesk.com/saas/enterprise/user/) w celu uzyskania wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie TOPdesk-Public** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurowanie TOPdesk-Public Single Sign-On

1. Zaloguj się do witryny firmy **TOPdesk-Public** jako administrator.

2. W menu **TOPdesk** kliknij pozycję **Settings** (Ustawienia).
   
    ![Ustawienia](./media/topdesk-public-tutorial/ic790598.png "Ustawienia")

3. Kliknij pozycję **Login Settings** (Ustawienia logowania).
   
    ![Ustawienia logowania](./media/topdesk-public-tutorial/ic790599.png "Ustawienia logowania")

4. Rozwiń menu **Login Settings** (Ustawienia logowania), a następnie kliknij pozycję **General** (Ogólne).
   
    ![Ogólne](./media/topdesk-public-tutorial/ic790600.png "Ogólne")

5. W sekcji **Public** (Konfiguracja **logowania SAML** ) w sekcji publicznej wykonaj następujące czynności:
   
    ![Ustawienia techniczne](./media/topdesk-public-tutorial/ic790601.png "Ustawienia techniczne")
   
    a. Kliknij pozycję **Download** (Pobierz), aby pobrać publiczny plik metadanych, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz pobrany plik metadanych, a następnie zlokalizuj węzeł **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Skopiuj wartość **AssertionConsumerService** , wklej tę wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** .      
   
6. Aby utworzyć plik certyfikatu, wykonaj następujące kroki:
    
    ![Certyfikat](./media/topdesk-public-tutorial/ic790606.png "Certyfikat")
    
    a. Otwórz plik metadanych pobrany w witrynie Azure Portal.
    
    b. Rozwiń węzeł **RoleDescriptor**, w którym element **xsi:type** ma wartość **fed:ApplicationServiceType**.
    
    c. Skopiuj wartość węzła **X509Certificate**.
    
    d. Zapisz skopiowaną wartość **X509Certificate** lokalnie na komputerze w pliku.

7. W sekcji **Public** (Publiczne) kliknij przycisk **Add** (Dodaj).
    
    ![Logowanie SAML](./media/topdesk-public-tutorial/ic790625.png "Logowanie SAML")

8. W oknie dialogowym **SAML configuration assistant** (Asystent konfiguracji SAML) wykonaj następujące kroki:
    
    ![Asystent konfiguracji SAML](./media/topdesk-public-tutorial/ic790608.png "Asystent konfiguracji SAML")
    
    a. Aby przekazać plik metadanych pobrany w witrynie Azure Portal, w obszarze **Federation Metadata** (Metadane federacji) kliknij przycisk **Browse** (Przeglądaj).

    b. Aby przekazać plik certyfikatu, w obszarze **Certificate (RSA)** (Certyfikat — RSA) kliknij przycisk **Browse** (Przeglądaj).

    c. Aby przekazać plik logo uzyskany od zespołu pomocy technicznej TOPdesk, w obszarze **Logo icon** (Ikona logo) kliknij przycisk **Browse** (Przeglądaj).

    d. W polu tekstowym **User name attribute** (Atrybut nazwy użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W polu tekstowym **Display name** (Nazwa wyświetlana) wpisz nazwę konfiguracji.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TOPdesk-Public.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **TOPdesk — publiczna**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **TOPdesk — publiczna**.

    ![Link TOPdesk-Public na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-topdesk---public-test-user"></a>Utwórz użytkownika testowego TOPdesk — publiczny

Aby umożliwić użytkownikom usługi Azure AD logowanie się do TOPdesk-Public, muszą one być obsługiwane w usłudze TOPdesk — Public. W przypadku TOPdesk — obsługa administracyjna jest zadaniem ręcznym.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny firmy **TOPdesk-Public** jako administrator.

2. W menu u góry kliknij pozycję **TOPdesk \> nowe \> pliki obsługi \>**.
   
    ![Person (Osoba)](./media/topdesk-public-tutorial/ic790628.png "Person (Osoba)")

3. W oknie dialogowym Nowa osoba wykonaj następujące czynności:
   
    ![Nowa osoba](./media/topdesk-public-tutorial/ic790629.png "Nowa osoba")
   
    a. Kliknij kartę General (Ogólne).

    b. W polu tekstowym **nazwisko** wpisz nazwisko użytkownika, np. Simon
 
    c. Wybierz **witrynę** dla konta.
 
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników TOPdesk i interfejsów API udostępnianych przez usługę TOPdesk — Public.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TOPdesk-Public w panelu dostępu należy automatycznie zalogować się do TOPdesk-Public, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)