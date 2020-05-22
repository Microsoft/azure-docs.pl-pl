---
title: 'Samouczek: integracja Azure Active Directory z usługą Amazon Business | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a firmą Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8218b3dbe09e5ce7e6c28e1084b26c6eec4a16ca
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773052"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Samouczek: Integrowanie usługi Amazon Business z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Amazon Business z usługą Azure Active Directory (Azure AD). Gdy integrujesz [firmę Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Amazon Business.
* Zezwól użytkownikom na automatyczne logowanie do usługi Amazon Business przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) firmy Amazon. Przejdź do strony [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) , aby utworzyć konto usługi Amazon Business.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD na istniejącym koncie usługi Amazon Business.

* Firma Amazon Business obsługuje usługi **SP i dostawcy tożsamości** zainicjowane Logowanie jednokrotne
* Firma Amazon Business obsługuje funkcję inicjowania obsługi klienta **just in Time**

## <a name="adding-amazon-business-from-the-gallery"></a>Dodawanie firmy Amazon Business z galerii

Aby skonfigurować integrację usługi Amazon Business w usłudze Azure AD, musisz dodać firmę Amazon z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Business** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Business** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Amazon Business przy użyciu użytkownika testowego o nazwie **B. Simon**.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Amazon Business, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego dla firm Amazon](#configure-amazon-business-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika usługi Amazon Business testowego](#create-amazon-business-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Amazon Business, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Amazon Business** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować w trybie zainicjowanym przy użyciu programu **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
       | | |
       |-|-|
       | `https://www.amazon.com`| Ameryka Północna |
       | `https://www.amazon.co.jp`| Azja Wschodnia |
       | `https://www.amazon.de`| Europa |

    1. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Ameryka Północna |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Azja Wschodnia |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Otrzymasz `<idpid>` wartość z sekcji Konfiguracja usługi Amazon Business SSO, która została omówiona w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , musisz dodać pełny adres URL podany w konfiguracji firmy Amazon Business do **adresu URL logowania** w sekcji **Ustaw dodatkowe adresy** URL.

1. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Aby edytować atrybuty, kliknij ikonę **Edytuj** w sekcji **atrybuty użytkownika & oświadczenia** .

    ![Atrybuty](media/amazon-business-tutorial/map-attribute3.png)

1. Edytuj atrybuty i skopiuj wartość **przestrzeni nazw** tych atrybutów do Notatnika.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute4.png)

1. Oprócz powyższych, aplikacja Amazon Business oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji **atrybuty użytkownika & oświadczenia** w oknie dialogowym **oświadczenia grupy** wykonaj następujące czynności:

    a. Kliknij **pióro** obok **grup zwróconych w ramach żądania**.

    ![image (obraz)](./media/amazon-business-tutorial/config04.png)

    ![image (obraz)](./media/amazon-business-tutorial/config05.png)

    b. Wybierz pozycję **wszystkie grupy** z listy radiowej.

    c. Wybierz **Identyfikator grupy** jako **atrybut źródłowy**.

    d. Zaznacz pole wyboru **Dostosuj nazwę tego żądania** , a następnie wprowadź nazwę grupy zgodnie z wymaganiami organizacji.

    e. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **metadane XML** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie firmy Amazon Business** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Konfigurowanie logowania jednokrotnego dla firm Amazon

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej firmy Amazon jako administrator.

1. Kliknij **profil użytkownika** i wybierz pozycję **Ustawienia biznesowe**.

    ![Profil użytkownika](media/amazon-business-tutorial/user-profile.png)

1. W kreatorze **integracji systemu** wybierz pozycję Logowanie jednokrotne **(SSO)**.

    ![Logowanie jednokrotne (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. W kreatorze **konfigurowania logowania jednokrotnego** wybierz dostawcę zgodnie z wymaganiami organizacji i kliknij przycisk **dalej**.

    ![Grupa domyślna](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > Mimo że usługa Microsoft AD FS jest wymieniona, nie działa z logowaniem jednokrotnym w usłudze Azure AD.

1. W kreatorze **nowego konta użytkownika** domyślne wybierz **grupę domyślną** , a następnie wybierz **domyślną rolę kupowania** zgodnie z rolą użytkownika w organizacji i kliknij przycisk **dalej**.

    ![Grupa domyślna](media/amazon-business-tutorial/dafault-group2.png)

1. W kreatorze **przekazywania pliku metadanych** kliknij przycisk **Przeglądaj** , aby przesłać plik **XML metadanych** pobrany z Azure Portal i kliknij przycisk **Przekaż**.

    ![Dane połączenia](media/amazon-business-tutorial/connection-data1.png)

1. Po przekazaniu pobranego pliku metadanych pola w sekcji **dane połączenia** będą wypełniane automatycznie. Po kliknięciu **przycisku Dalej**.

    ![Dane połączenia](media/amazon-business-tutorial/connection-data2.png)

1. W kreatorze **przekazywania instrukcji atrybutu** kliknij przycisk **Pomiń**.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute1.png)

1. W kreatorze **mapowania atrybutów** Dodaj pola wymagania, klikając opcję **+ Dodaj pole** . Dodaj wartości atrybutów, w tym przestrzeń nazw, skopiowane z sekcji **atrybuty użytkownika & oświadczenia** Azure Portal do pola **SAML AttributeName** , a następnie kliknij przycisk **dalej**.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute2.png)

1. W kreatorze **danych połączenia usługi Amazon** kliknij przycisk **dalej**.

    ![Połączenie](media/amazon-business-tutorial/amazon-connect.png)

1. Sprawdź **stan** kroków, które zostały skonfigurowane, a następnie kliknij przycisk **Rozpocznij testowanie**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection1.png)

1. W kreatorze **testowania połączenia rejestracji jednokrotnej** kliknij przycisk **Testuj**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection2.png)

1. Przed kliknięciem przycisku **Aktywuj**w kreatorze **zainicjowanych adresów URL dostawcy tożsamości** , skopiuj wartość przypisaną do **idpid** i wklej do parametru **idpid** w **adresie URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    ![Połączenie](media/amazon-business-tutorial/sso-connection3.png)

1. Na stronie **czy jesteś gotowy do przełączenia do aktywnego kreatora logowania jednokrotnego?** Sprawdź, czy w **pełni PRZEtestowano Logowanie jednokrotne i wszystko jest gotowe do użycia na żywo** , a następnie kliknij pozycję **Przełącz na aktywny**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection4.png)

1. Na koniec w sekcji **szczegóły połączenia z logowaniem jednokrotnym** **stan** jest wyświetlany jako **aktywny**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **SP** , wykonaj następujące czynności, a następnie wklej adres URL logowania z zrzutu ekranu powyżej w polu tekstowym **Ustaw dodatkowe adresy** **URL w** Azure Portal. Użyj następującego formatu:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

> [!NOTE]
> W razie potrzeby Administratorzy muszą utworzyć użytkowników testowych w swojej dzierżawie. Poniższe kroki pokazują, jak utworzyć użytkownika testowego.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. Kliknij **Azure Active Directory > wszystkie grupy**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Kliknij pozycję **Nowa grupa**:

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Wypełnij pola **Typ grupy**, **Nazwa grupy**, **Opis grupy**, **Typ członkostwa**. Kliknij strzałkę, aby wybrać elementy członkowskie, a następnie wyszukaj lub kliknij element członkowski, który chcesz dodać do grupy. Kliknij pozycję **Wybierz** , aby dodać wybrane elementy członkowskie, a następnie kliknij pozycję **Utwórz**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do firmy Amazon Business.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Amazon Business**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

    >[!NOTE]
    > Jeśli nie przypiszesz użytkowników w usłudze Azure AD, zostanie wyświetlony następujący błąd.

    ![Link Dodaj użytkownika](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Przypisz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Amazon Business**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacje wpisz i wybierz pozycję **Amazon Business**.

    ![Link do usługi Amazon Business na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Wyszukaj grupę zabezpieczeń, której chcesz użyć, a następnie kliknij grupę, aby dodać ją do sekcji Wybierz członków. Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz**.

    ![Wyszukaj grupę zabezpieczeń](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Sprawdź powiadomienia na pasku menu, aby otrzymywać powiadomienia o pomyślnym przypisaniu grupy do aplikacji przedsiębiorstwa w Azure Portal.

### <a name="create-amazon-business-test-user"></a>Tworzenie użytkownika usługi Amazon Business testowego

W tej sekcji użytkownik o nazwie B. Simon został utworzony w usłudze Amazon Business. Firma Amazon Business obsługuje funkcję aprowizacji użytkowników just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Amazon Business, zostanie utworzony nowy po uwierzytelnieniu.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Amazon Business w panelu dostępu należy automatycznie zalogować się do firmy Amazon, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
