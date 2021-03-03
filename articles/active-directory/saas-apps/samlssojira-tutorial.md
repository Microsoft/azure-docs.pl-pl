---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla JIRA za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e1440feacd10935b15195110cd7ea3e20b4d523c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687638"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla JIRA przez rozwiązanie GmbH

W tym samouczku dowiesz się, jak skonfigurować protokół SAML SSO dla JIRA za pomocą rozwiązania GmbH z Azure Active Directory (Azure AD).
Zintegrowanie usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto może się zalogować do JIRA za pomocą wtyczki protokołu SAML SSO przez rozwiązanie GmbH.
* Możesz włączyć automatyczne logowanie użytkowników, aby JIRA się z kontami usługi Azure AD za pomocą logowania jednokrotnego w protokole SAML przez rozwiązanie GmbH (Logowanie jednokrotne).
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD i logowanie jednokrotne SAML dla JIRA za pomocą rozwiązania GmbH, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne SAML dla JIRA przez rozwiązanie GmbH obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Dodawanie aplikacji dla przedsiębiorstw na potrzeby logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD, musisz dodać nową aplikację dla przedsiębiorstw. W galerii jest wstępnie skonfigurowane wstępne ustawienie aplikacji dla tego elementu, **Logowanie jednokrotne SAML dla JIRA za pomocą rozdzielczości GmbH**.

**Aby dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji przedsiębiorstwa**, a następnie kliknij pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz polecenie **SSO logowania jednokrotnego w usłudze JIRA przez rozwiązanie GmbH**, wybierz pozycję **SAML SSO dla JIRA przy użyciu rozwiązania GmbH** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację. Możesz również zmienić nazwę aplikacji dla przedsiębiorstw.

     ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Skonfiguruj i przetestuj Logowanie jednokrotne za pomocą wtyczki SAML SSO i usługi Azure AD

Ta sekcja zawiera instrukcje testowania i konfigurowania logowania jednokrotnego w usłudze JIRA dla użytkownika usługi Azure AD. Ta czynność zostanie wykonana dla użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.

Aby skonfigurować i przetestować Logowanie jednokrotne, należy wykonać następujące czynności:

1. **[Konfigurowanie aplikacji Azure AD Enterprise na potrzeby logowania jednokrotnego](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** — Skonfiguruj aplikację Azure AD Enterprise dla logowania jednokrotnego
2. **[Skonfiguruj wtyczkę SSO protokołu SAML wystąpienia JIRA](#configure-the-saml-sso-plugin-of-your-jira-instance)** — Skonfiguruj ustawienia pojedynczej Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — Tworzenie użytkownika testowego w usłudze Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — włączenie użytkownika testowego do korzystania z logowania jednokrotnego po stronie platformy Azure.
1. **[Utwórz użytkownika testowego w JIRA](#create-the-test-user-also-in-jira)** — Utwórz odpowiedni użytkownika testowego w JIRA dla użytkownika testowego usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-single-sign-on)** — Sprawdź, czy konfiguracja działa.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurowanie aplikacji Azure AD Enterprise na potrzeby logowania jednokrotnego

W tej sekcji skonfigurujesz Logowanie jednokrotne w Azure Portal.

Aby skonfigurować Logowanie jednokrotne przy użyciu protokołu SAML SSO dla JIRA za pomocą rozwiązania GmbH, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/), w okienku po prostu utworzone logowanie **jednokrotne SAML dla JIRA przy użyciu rozwiązania GmbH** Enterprise aplikacji, wybierz pozycję **Logowanie** jednokrotne w lewym panelu.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W polu **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Następnie kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe Konfigurowanie protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowania **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący sekcję "podstawowa S A M L" konfiguracji z wyróżnionymi polami tekstowymi "identifier" i "reply U R L" i wybranym przyciskiem "Zapisz".](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Aby uzyskać identyfikator, adres URL odpowiedzi i adres URL logowania, należy zastąpić **\<server-base-url>** podstawowy adres URL wystąpienia JIRA. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. Jeśli wystąpił problem, skontaktuj się z nami przy [użyciu protokołu SAML SSO dla usługi JIRA przez rozwiązanie GmbH Client Support Team](https://www.resolution.de/go/support).

4. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Pobierz **plik XML metadanych Federacji** i Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Skonfiguruj wtyczkę SSO protokołu SAML wystąpienia JIRA 

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojego wystąpienia JIRA jako administrator.

2. Umieść kursor nad koło zębate po prawej stronie, a następnie kliknij pozycję **Zarządzaj aplikacjami**.
    
    ![Zrzut ekranu, który pokazuje strzałkę wskazującą na ikonie "koło zębate" i opcję "Zarządzaj aplikacjami" wybraną z listy rozwijanej.](./media/samlssojira-tutorial/addon1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** .

    ![Zrzut ekranu przedstawiający stronę "dostęp administratora".](./media/samlssojira-tutorial/addon2.png)

4. JIRA zwykle przekierowuje Cię do portalu Atlassian. Jeśli nie, kliknij przycisk **Znajdź nowe aplikacje** w lewym panelu. Wyszukaj pozycję Logowanie jednokrotne **(SSO) dla usługi JIRA** , a następnie kliknij przycisk **Instaluj** , aby zainstalować wtyczkę SAML.

    ![Zrzut ekranu przedstawiający stronę "Atlassian Marketplace for JIRA" za pomocą strzałki wskazującej na przycisk "Zainstaluj" w przypadku logowania jednokrotnego "S A M L" (S S) JIRA, S A M/S "s".](./media/samlssojira-tutorial/store.png)

5. Rozpocznie się instalacja wtyczki. Gdy skończysz, kliknij przycisk **Zamknij** .

    ![Zrzut ekranu przedstawiający okno dialogowe "Instalowanie".](./media/samlssojira-tutorial/store-2.png)

    ![Zrzut ekranu pokazujący "zainstalowane i gotowe do użycia!" okno dialogowe z wybranym przyciskiem "Zamknij".](./media/samlssojira-tutorial/store-3.png)

6. Następnie kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu, na którym jest wyświetlany przycisk "S A M L logowania jednokrotnego (S S O) JIRA, S A M/S S" z wybraną opcją "Zarządzaj".](./media/samlssojira-tutorial/store-4.png)
    
8. Następnie kliknij pozycję **Konfiguruj** , aby skonfigurować zainstalowaną wtyczkę.

    ![Zrzut ekranu przedstawiający stronę "Zarządzanie aplikacjami" z wybranym przyciskiem "Konfiguruj" dla aplikacji "S A M L SingleSignOn for JIRA".](./media/samlssojira-tutorial/store-5.png)

9. W kreatorze **konfiguracji wtyczki SAML SingleSignon** kliknij pozycję **Dodaj nowe dostawcy tożsamości** , aby skonfigurować usługę Azure AD jako nowego dostawcę tożsamości.

    ![Zrzut ekranu przedstawia stronę "Welcome" z wybranym przyciskiem "Dodaj nowe I d P".](./media/samlssojira-tutorial/addon4.png) 

10. Na stronie **Wybierz dostawcę tożsamości SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający stronę "wybieranie dostawcy tożsamości S A M" z wyróżnionymi polami tekstowymi "i d" i "nazwa" oraz wybranym przyciskiem "dalej".](./media/samlssojira-tutorial/addon5a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. Dodaj **nazwę** dostawcy tożsamości (np. Azure AD).
    
    c. Dodaj (opcjonalnie) **Opis** dostawcy tożsamości (np. Azure AD).
    
    d. Kliknij przycisk **Dalej**.
    
11. Na stronie **Konfiguracja dostawcy tożsamości** kliknij przycisk **dalej**.
 
    ![Zrzut ekranu przedstawiający stronę "Konfiguracja dostawcy tożsamości".](./media/samlssojira-tutorial/addon5b.png)

12. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Zrzut ekranu, na którym jest zaznaczona akcja "Importuj S A M g I d P" z "Select Metadata X M L".](./media/samlssojira-tutorial/addon5c.png)

    a. Kliknij przycisk **Wybierz plik XML metadanych** i wybierz pobrany wcześniej plik **XML metadanych Federacji** .

    b. Kliknij przycisk **Importuj** .
     
    c. Poczekaj chwilę, aż import zakończy się pomyślnie.  
     
    d. Kliknij przycisk **Dalej**.
    
13. Na stronie **ATRYBUT identyfikatora użytkownika i transformacja** kliknij przycisk **dalej** .

    ![Zrzut ekranu przedstawiający stronę "atrybut i przekształcenie użytkownika" z wybranym przyciskiem "dalej".](./media/samlssojira-tutorial/addon5d.png)
    
14. Na stronie **Tworzenie i aktualizowanie użytkownika** kliknij przycisk **Zapisz & dalej** , aby zapisać ustawienia.
    
    ![Zrzut ekranu przedstawiający stronę "Tworzenie i aktualizowanie użytkownika" z wybranym przyciskiem Zapisz & dalej.](./media/samlssojira-tutorial/addon6a.png)
    
15. Na stronie **testowanie ustawień** kliknij przycisk **Pomiń test & Skonfiguruj ręcznie** , aby pominąć test użytkownika. Zostanie to wykonane w następnej sekcji i będzie wymagało pewnych ustawień w Azure Portal.
    
    ![Zrzut ekranu przedstawiający stronę "Testowanie ustawień" z wybranym przyciskiem "Pomiń test & Skonfiguruj ręcznie".](./media/samlssojira-tutorial/addon6b.png)
    
16. Kliknij przycisk **OK** , aby pominąć ostrzeżenie.
    
    ![Zrzut ekranu pokazujący okno dialogowe ostrzeżenia z wybranym przyciskiem "O K".](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon. Użytkownik przetestuje Logowanie jednokrotne.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We **właściwościach użytkownika** wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **Britta Simon**.
  
    b. W polu **Nazwa użytkownika** wprowadź wartość <b>BrittaSimon@contoso.com</b> .

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji dodasz Britta Simon do aplikacji korporacyjnej, która umożliwia korzystanie z logowania jednokrotnego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**. 

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje Wyszukaj aplikację przedsiębiorstwa utworzoną na początku tego samouczka. Jeśli korzystasz z kroków tego samouczka, jest on nazywany **logowaniem jednokrotnym SAML dla JIRA za pomocą rozwiązania GmbH**. Jeśli poznasz inną nazwę, Wyszukaj tę nazwę.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście aplikacji](common/all-applications.png)

3. Na panelu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-the-test-user-also-in-jira"></a>Utwórz użytkownika testowego również w JIRA

Aby umożliwić użytkownikom usługi Azure AD logowanie się do logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH, muszą one być obsługiwane w protokole SSO protokołu SAML dla JIRA przy użyciu rozwiązania GmbH. W przypadku tego samouczka konieczne jest ręczne przeprowadzenie aprowizacji. Istnieją jednak również inne modele aprowizacji dostępne dla wtyczki SAML SSO, które są obsługiwane przez rozwiązanie, na przykład **po prostu na czas** aprowizacji. Zapoznaj się z dokumentacją dotyczącą protokołu [SAML SSO przez rozwiązanie GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Jeśli masz pytanie dotyczące tego problemu, skontaktuj się z pomocą techniczną, aby uzyskać pomoc [techniczną](https://www.resolution.de/go/support).

**Aby ręcznie zainicjować obsługę administracyjną konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do wystąpienia JIRA jako administrator.

2. Umieść kursor nad koło zębate i wybierz pozycję **Zarządzanie użytkownikami**.

   ![Zrzut ekranu przedstawiający strzałkę wskazującą ikonę "koło zębate" z opcją "Zarządzanie użytkownikami" wybraną z listy rozwijanej.](./media/samlssojira-tutorial/user1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** .

    ![Zrzut ekranu przedstawiający stronę "dostęp administratora" z wyróżnioną polem tekstowym "hasło".](./media/samlssojira-tutorial/user2.png) 

4. Na karcie **Zarządzanie użytkownikami** kliknij pozycję **Utwórz użytkownika**.

    ![Zrzut ekranu pokazujący kartę "Zarządzanie użytkownikami" z wybranym przyciskiem "Utwórz użytkownika".](./media/samlssojira-tutorial/user3-new.png) 

5. Na stronie **"Tworzenie nowego użytkownika"** wykonaj następujące czynności. Musisz utworzyć użytkownika tak samo jak w usłudze Azure AD:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user4-new.png) 

    a. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika:  <b>BrittaSimon@contoso.com</b> .

    b. W polu tekstowym **pełna nazwa** wpisz pełną nazwę użytkownika: **Britta Simon**.

    c. W polu tekstowym **Nazwa użytkownika** wpisz adres e-mail użytkownika: <b>BrittaSimon@contoso.com</b> . 

    d. W polu tekstowym **hasło** wprowadź hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika** , aby zakończyć tworzenie użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie się do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

Możesz również przetestować Logowanie jednokrotne, jeśli przejdziesz do `https://<server-base-url>/plugins/servlet/samlsso` . Zamień na **\<server-base-url>** podstawowy adres URL wystąpienia JIRA.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Włącz przekierowanie logowania jednokrotnego dla JIRA

Jak wspomniano w sekcji przed, istnieją dwa sposoby wyzwalania logowania jednokrotnego. Za pomocą **Azure Portal** lub **specjalnego linku do wystąpienia usługi JIRA**. Wtyczka SSO protokołu SAML przez rozwiązanie GmbH umożliwia również wyzwalanie logowania jednokrotnego, **uzyskując dostęp do dowolnego adresu URL wskazującego wystąpienie JIRA**.

W zasadzie wszyscy użytkownicy uzyskujący dostęp do JIRA zostaną przekierowani do logowania jednokrotnego po aktywowaniu opcji w wtyczki.

Aby uaktywnić przekierowywać Logowanie jednokrotne, wykonaj następujące czynności w **wystąpieniu programu JIRA**:

1. Dostęp do strony konfiguracji wtyczki rejestracji jednokrotnej protokołu SAML w JIRA.
1. Kliknij pozycję **przekierowanie** w lewym panelu.

   ![Zrzut ekranu przedstawiający stronę konfiguracji wtyczki JIRA SAML SingleSignOn wyróżnienie linku przekierowania w lewym okienku nawigacji.](./media/samlssojira-tutorial/ssore1.png)

1. **Włączenie przekierowania rejestracji JEDNOkrotnej**.

   ![Zrzut ekranu przedstawiający stronę konfiguracyjną wtyczki SAML SingleSignOn JIRA, zaznaczając pole wyboru Włącz przekierowanie rejestracji jednokrotnej.](./media/samlssojira-tutorial/ssore2.png) 

1. Naciśnij przycisk **Zapisz ustawienia** w prawym górnym rogu.

Po aktywowaniu opcji można nadal uzyskać dostęp do monitu o podanie nazwy użytkownika/hasła, jeśli opcja **Włącz Nosso** jest pokryta przez przechodzenie do `https://<server-base-url>/login.jsp?nosso` . Jak zawsze, należy zastąpić **\<server-base-url>** podstawowym adresem URL.


## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)