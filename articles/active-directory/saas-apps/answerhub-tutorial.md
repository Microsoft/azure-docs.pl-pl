---
title: 'Samouczek: integracja Azure Active Directory z usługą AnswerHub | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: ef09ef7d2ca6dd68def732831f9341dcd3aa6d62
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458085"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Samouczek: integracja Azure Active Directory z usługą AnswerHub

Z tego samouczka dowiesz się, jak zintegrować aplikację AnswerHub z usługą Azure Active Directory (Azure AD).
Integracja aplikacji AnswerHub z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, kto ma dostęp do aplikacji AnswerHub, za pomocą usługi Azure AD.
* Możesz umożliwić użytkownikom automatyczne logowanie do aplikacji AnswerHub przy użyciu ich kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami w centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją AnswerHub, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja aplikacji AnswerHub z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja AnswerHub obsługuje logowanie jednokrotne inicjowane przez dostawcę usług.

## <a name="add-answerhub-from-the-gallery"></a>Dodawanie aplikacji AnswerHub z galerii

Aby skonfigurować integrację aplikacji AnswerHub z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację AnswerHub z galerii:**

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **AnswerHub**. Wybierz aplikację **AnswerHub** z listy wyników, a następnie wybierz pozycję **Dodaj**.

     ![Aplikacja AnswerHub na liście wyników](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją AnswerHub, korzystając z danych użytkownika testowego o nazwie Britta Simon.
Na potrzeby logowania jednokrotnego należy ustanowić połączenie między użytkownikiem usługi Azure AD a odpowiadającym mu użytkownikiem aplikacji AnswerHub.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji AnswerHub, wykonaj następujące zadania:

1. [Skonfiguruj logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj logowanie jednokrotne w aplikacji AnswerHub](#configure-answerhub-single-sign-on), aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. [Utwórz testowego użytkownika usługi Azure AD](#create-an-azure-ad-test-user) o nazwie Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. Utwórz testowego użytkownika aplikacji AnswerHub, który odpowiada użytkownikowi testowemu usługi Azure AD i jest z nim połączony.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

**Aby skonfigurować logowanie jednokrotne usługi Azure AD dla aplikacji AnswerHub:**

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **AnswerHub** wybierz pozycję **Logowanie jednokrotne**.

    ![Przycisk Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Okno dialogowe Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wybierz ikonę Edytuj, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Strona Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Sekcja Podstawowa konfiguracja protokołu SAML](common/sp-identifier.png)

    a. W polu **Adres URL logowania** wpisz adres URL zgodny z wzorcem `https://<company>.answerhub.com`

    b. W polu **Identyfikator (identyfikator jednostki)** wpisz adres URL zgodny z wzorcem `https://<company>.answerhub.com`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji AnswerHub](mailto:success@answerhub.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** obok pozycji **Certyfikat (Base64)**, zgodnie z wymaganiami, a następnie zapisz certyfikat na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji AnswerHub** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

   Możesz skopiować te adresy URL:
    - Adres URL logowania

    - Identyfikator usługi Azure AD

    - Adres URL wylogowywania

### <a name="configure-answerhub-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji AnswerHub

W tej sekcji skonfigurujesz logowanie jednokrotne w aplikacji AnswerHub.  

**Aby skonfigurować logowanie jednokrotne w aplikacji AnswerHub:**

1. W innym oknie przeglądarki internetowej zaloguj się w firmowej witrynie aplikacji AnswerHub jako administrator.

    > [!NOTE]
    > Jeśli potrzebujesz pomocy związanej z konfigurowaniem aplikacji AnswerHub, skontaktuj się z [zespołem pomocy technicznej aplikacji AnswerHub](mailto:success@answerhub.com.).

2. Przejdź do sekcji **Administration** (Administracja).

3. Na karcie **Users and Groups** (Użytkownicy i grupy) w okienku po lewej stronie w sekcji **Social Settings** (Ustawienia społecznościowe) wybierz pozycję **SAML Setup** (Konfiguracja protokołu SAML).

4. Na karcie **IDP Config** (Konfiguracja dostawcy tożsamości) wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę AnswerHub z wybraną kartą użytkownicy & grupy.](./media/answerhub-tutorial/ic785172.png "Konfiguracja protokołu SAML")  
  
    a. W polu **IDP Login URL** (Adres URL logowania dostawcy tożsamości) wklej **Adres URL logowania** skopiowany z witryny Azure Portal.
  
    b. W polu **IDP Logout URL** (Adres URL wylogowywania dostawcy tożsamości) wklej **Adres URL wylogowywania** skopiowany z witryny Azure Portal.

    c. W polu **IDP Name Identifier Format** (Format identyfikatora nazwy dostawcy tożsamości) wprowadź wartość **identyfikatora** wybraną w sekcji **Atrybuty użytkownika** w witrynie Azure Portal.
  
    d. Wybierz pozycję **Keys and Certificates** (Klucze i certyfikaty).

5. W sekcji **Keys and Certificates** (Klucze i certyfikaty) wykonaj następujące czynności:

    ![Sekcja klucze i certyfikaty](./media/answerhub-tutorial/ic785173.png "Klucze i certyfikaty")  

    a. Otwórz w programie Notatnik certyfikat w formacie Base64 pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej ją w polu **IDP Public Key (x509 Format)** (Klucz publiczny dostawcy tożsamości w formacie x509).
  
    b. Wybierz pozycję **Zapisz**.

6. Na karcie **IDP Config** (Konfiguracja dostawcy tożsamości) ponownie wybierz pozycję **Save** (Zapisz).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

**Aby utworzyć użytkownika testowego usługi Azure AD:**

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Wybieranie pozycji Azure Active Directory, Użytkownicy, Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. Wykonaj następujące czynności w sekcji Właściwości użytkownika.

    ![Właściwości użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wprowadź **brittasimon \@<yourcompanydomain. Extension>**.  
    Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji skonfigurujesz użytkownika Britta Simon do korzystania z logowania jednokrotnego usługi Azure AD, udzielając Użytkownikowi dostępu do usługi AnswerHub.

**Aby przypisać użytkownika testowego usługi Azure AD:**

1. W witrynie Azure Portal wybierz kolejno pozycje **Aplikacje dla przedsiębiorstw**, **Wszystkie aplikacje** i **AnswerHub**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **AnswerHub**.

    ![Lista aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście **Użytkownicy**, a następnie wybierz przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. 

7. Wybierz przycisk **Wybierz** u dołu ekranu.

8. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-answerhub-test-user"></a>Tworzenie użytkownika testowego aplikacji AnswerHub

Aby umożliwić użytkownikom usługi Azure AD logowanie w aplikacji AnswerHub, należy dodać tych użytkowników do aplikacji AnswerHub. W aplikacji AnswerHub to zadanie wykonuje się ręcznie.

**Aby skonfigurować konto użytkownika:**

1. Zaloguj się w firmowej witrynie aplikacji **AnswerHub** jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

3. Wybierz kartę **Users & Groups** (Użytkownicy i grupy).

4. W okienku po lewej stronie w sekcji **Manage users** (Zarządzanie użytkownikami) wybierz polecenie **Create or import users** (Utwórz lub importuj użytkowników), a następnie wybierz pozycję **Users & Groups** (Użytkownicy i grupy).

   ![Zrzut ekranu przedstawia stronę AnswerHub z wybraną kartą użytkownicy & grupy, a łącze Utwórz lub Importuj użytkowników jest wywoływane.](./media/answerhub-tutorial/ic785175.png "Użytkownicy & grupy")

5. W polach **Email address** (Adres e-mail), **Username** (Nazwa użytkownika) i **Password** (Hasło) wprowadź odpowiednie wartości dla istniejącego konta usługi Azure AD, które chcesz dodać, a następnie wybierz pozycję **Save** (Zapisz).

> [!NOTE]
> Do konfigurowania kont użytkowników usługi Azure AD możesz użyć jakichkolwiek innych narzędzi lub interfejsów API służących do tworzenia kont użytkowników, dostępnych w aplikacji AnswerHub.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka AnswerHub na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji AnswerHub, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the access panel (Wprowadzenie do panelu dostępu)](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Tutorials for integrating SaaS apps with Azure Active Directory (Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory)](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)