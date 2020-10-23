---
title: 'Samouczek: integracja Azure Active Directory z usługą iLMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 7289fe2ec1f39679dcec95f1f48a6efa9ed0cdfc
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460346"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Samouczek: integracja iLMS z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę iLMS z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi iLMS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do iLMS.
* Zezwól użytkownikom na automatyczne logowanie się do usługi iLMS przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* subskrypcja z włączonym logowaniem jednokrotnym (SSO) iLMS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. iLMS obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii

Aby skonfigurować integrację programu iLMS z usługą Azure AD, musisz dodać iLMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **iLMS** w polu wyszukiwania.
1. Wybierz pozycję **iLMS** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą iLMS przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w iLMS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą iLMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-ilms-sso)** w usłudze iLMS, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego iLMS](#create-ilms-test-user)** , aby uzyskać odpowiednik Britta Simon w iLMS, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ILMS** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu usługi **dostawcy tożsamości** , na stronie **Podstawowa konfiguracja protokołu SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wklej wartość **identyfikatora** skopiowaną z sekcji **dostawca usług** ustawień SAML w portalu administracyjnym iLMS.

    b. W polu tekstowym **adres URL odpowiedzi** wklej wartość **punktu końcowego (adres URL)** skopiowaną z sekcji **dostawca usług** ustawień protokołu SAML w portalu administratora iLMS, który ma następujący wzorzec `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wklej wartość **punktu końcowego (adres URL)** skopiowaną z sekcji **dostawca usług** ustawień SAML w portalu administratora iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Aby włączyć Inicjowanie obsługi JIT, aplikacja iLMS oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    > [!NOTE]
    > Musisz włączyć opcję **Utwórz nierozpoznane konto użytkownika** w iLMS, aby zmapować te atrybuty. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) , aby uzyskać pomysł dotyczący konfiguracji atrybutów.

1. Oprócz powyższych, aplikacja iLMS oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | --------|------------- |
    | przegrod | user.department |
    | region | User. State |
    | działu, | user.jobtitle |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie iLMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ilms-sso"></a>Konfigurowanie logowania jednokrotnego iLMS

1. W innym oknie przeglądarki sieci Web Zaloguj się do **portalu administratora iLMS** jako administrator.

2. Kliknij pozycję **Logowanie jednokrotne: SAML** na karcie **Ustawienia** , aby otworzyć ustawienia SAML, i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Ustawienia I L M S, w której można wybrać polecenie S S O: SAML.](./media/ilms-tutorial/1.png)

3. Rozwiń sekcję **dostawca usług** i skopiuj wartość **Identyfikator** i **punkt końcowy (adres URL)** .

    ![Zrzut ekranu przedstawia ustawienia protokołu SAML, w których można uzyskać wartości.](./media/ilms-tutorial/2.png) 

4. W obszarze **dostawca tożsamości** kliknij pozycję **Importuj metadane**.

5. Wybierz plik **metadanych Federacji** pobrany z Azure Portal z sekcji **certyfikat podpisywania SAML** .

    ![Zrzut ekranu przedstawia ustawienia protokołu SAML, w których można wybrać plik metadanych.](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Jeśli chcesz włączyć Inicjowanie obsługi JIT, aby utworzyć konta usługi iLMS dla użytkowników nierozpoznających, wykonaj następujące czynności:

    a. Zaznacz pole wyboru **Utwórz nierozpoznane konto użytkownika**.

    ![Zrzut ekranu przedstawia opcję Utwórz nierozpoznane konto użytkownika.](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapuj atrybuty w usłudze Azure AD o atrybuty w iLMS. W kolumnie atrybut Określ nazwę atrybutów lub wartość domyślną.

    c. Przejdź do karty **reguły biznesowe** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia ustawienia reguł firmy, w których można wprowadzić informacje w tym kroku.](./media/ilms-tutorial/5.png)

    d. Zaznacz **opcję Utwórz nierozpoznane regiony, działy i działy** , aby utworzyć regiony, działy i działy, które jeszcze nie istnieją w czasie rejestracji jednokrotnej.

    e. Sprawdź **aktualizowanie profilu użytkownika podczas logowania** , aby określić, czy profil użytkownika jest aktualizowany przy użyciu każdego logowania jednokrotnego.

    f. Jeśli opcja **Aktualizuj puste wartości dla pól nieobowiązkowych w profilu użytkownika** jest zaznaczona, opcjonalne pola profilu, które są puste po zalogowaniu, również spowodują, że profil iLMS użytkownika będzie zawierał puste wartości dla tych pól.

    przykład Zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem o błędzie** i wprowadź adres e-mail użytkownika, na którym chcesz otrzymać wiadomość e-mail z powiadomieniem o błędzie.

7. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    ![Zrzut ekranu przedstawiający przycisk Zapisz.](./media/ilms-tutorial/save.png)

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

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi iLMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **iLMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-ilms-test-user"></a>Utwórz użytkownika testowego iLMS

Aplikacja obsługuje funkcję udostępniania just in Time użytkownika i po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Po kliknięciu pola wyboru **Utwórz nierozpoznane konto użytkownika** podczas ustawienia konfiguracji SAML w portalu administracyjnym iLMS zostanie wykonane działanie JIT.

Jeśli musisz ręcznie utworzyć użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny iLMS jako administrator.

2. Kliknij pozycję **zarejestruj użytkownika** na karcie **Użytkownicy** , aby otworzyć stronę **Rejestrowanie użytkownika** .

   ![Zrzut ekranu przedstawia kartę Ustawienia I L M S, w której można wybrać pozycję Zarejestruj użytkownika.](./media/ilms-tutorial/3.png)

3. Na stronie **Rejestrowanie użytkownika** wykonaj następujące czynności.

    ![Zrzut ekranu przedstawia stronę rejestrowanie użytkownika, na której wprowadzane są określone informacje.](./media/ilms-tutorial/create_testuser_add.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię, np. Britta.

    b. W polu **tekstowym nazwisko wpisz nazwisko,** takie jak Simon.

    c. W polu tekstowym **Identyfikator wiadomości e-mail** wpisz adres e-mail użytkownika, np BrittaSimon@contoso.com .

    d. Z listy rozwijanej **region** wybierz wartość dla regionu.

    e. Z listy rozwijanej **podział** wybierz wartość dla dzielenia.

    f. Z listy rozwijanej **dział** wybierz wartość dla działu.

    przykład Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Możesz wysłać wiadomość e-mail z rejestracją do użytkownika, zaznaczając pole wyboru **Wyślij wiadomość e-mail z rejestracją** .

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka iLMS w panelu dostępu należy automatycznie zalogować się do iLMS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)