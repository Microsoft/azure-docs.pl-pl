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
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 450db7c01de4ea7db0133ceca962941ae1a927e5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173272"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla JIRA przez rozwiązanie GmbH

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne SAML dla JIRA za pomocą rozwiązania GmbH z Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne SAML dla JIRA za pomocą rozwiązania GmbH z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH.
* Zezwól użytkownikom na automatyczne logowanie do logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Logowanie jednokrotne SAML dla usługi JIRA za pomocą rozdzielczości "Logowanie jednokrotne" w systemie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne SAML dla JIRA przez rozwiązanie GmbH obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Dodaj Logowanie jednokrotne SAML dla JIRA za pomocą rozdzielczości GmbH z galerii

Aby skonfigurować integrację usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD, musisz dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **SSO logowania jednokrotnego w usłudze JIRA przez rozwiązanie GmbH** w polu wyszukiwania.
1. Wybierz pozycję **SAML SSO dla JIRA za pomocą rozdzielczości GmbH** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w protokole SAML przez rozwiązanie GmbH przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze SAML SSO dla JIRA za pomocą rozwiązania GmbH.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z logowaniem jednokrotnym SAML dla JIRA za pomocą rozwiązania GmbH, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne SAML dla JIRA za pomocą rozdzielczości GmbH Logowanie jednokrotne](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Logowanie jednokrotne SAML dla JIRA za pomocą rozdzielczości GmbH test użytkownika](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** -aby mieć odpowiednik B. Simon w protokole SSO protokołu SAML dla JIRA za pomocą rozdzielczości GmbH, która jest połączona z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie " **Logowanie jednokrotne w usłudze SAML dla JIRA za pomocą rozwiązania GmbH** Application Integration" Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowania **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Aby uzyskać identyfikator, adres URL odpowiedzi i adres URL logowania, należy zastąpić **\<server-base-url>** podstawowy adres URL wystąpienia JIRA. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. Jeśli wystąpił problem, skontaktuj się z nami przy [użyciu protokołu SAML SSO dla usługi JIRA przez rozwiązanie GmbH Client Support Team](https://www.resolution.de/go/support).

4. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Pobierz **plik XML metadanych Federacji** i Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do logowania jednokrotnego w protokole SAML przez rozwiązanie GmbH.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAML SSO dla JIRA za pomocą rozdzielczości GmbH**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Konfigurowanie logowania jednokrotnego SAML dla JIRA za pomocą rozdzielczości GmbH Logowanie jednokrotne 

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojego wystąpienia JIRA jako administrator.

2. Umieść kursor nad koło zębate po prawej stronie, a następnie kliknij pozycję **Zarządzaj aplikacjami**.
    
    ![Zrzut ekranu, który pokazuje strzałkę wskazującą na ikonie "koło zębate" i opcję "Zarządzaj aplikacjami" wybraną z listy rozwijanej.](./media/samlssojira-tutorial/add-on-1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** .

    ![Zrzut ekranu przedstawiający stronę "dostęp administratora".](./media/samlssojira-tutorial/add-on-2.png)

4. JIRA zwykle przekierowuje Cię do portalu Atlassian. Jeśli nie, kliknij przycisk **Znajdź nowe aplikacje** w lewym panelu. Wyszukaj pozycję Logowanie jednokrotne **(SSO) dla usługi JIRA** , a następnie kliknij przycisk **Instaluj** , aby zainstalować wtyczkę SAML.

    ![Zrzut ekranu przedstawiający stronę "Atlassian Marketplace for JIRA" za pomocą strzałki wskazującej na przycisk "Zainstaluj" w przypadku logowania jednokrotnego "S A M L" (S S) JIRA, S A M/S "s".](./media/samlssojira-tutorial/store.png)

5. Rozpocznie się instalacja wtyczki. Gdy skończysz, kliknij przycisk **Zamknij** .

    ![Zrzut ekranu przedstawiający okno dialogowe "Instalowanie".](./media/samlssojira-tutorial/store-2.png)

    ![Zrzut ekranu pokazujący "zainstalowane i gotowe do użycia!" okno dialogowe z wybranym przyciskiem "Zamknij".](./media/samlssojira-tutorial/store-3.png)

6. Następnie kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu, na którym jest wyświetlany przycisk "S A M L logowania jednokrotnego (S S O) JIRA, S A M/S S" z wybraną opcją "Zarządzaj".](./media/samlssojira-tutorial/store-4.png)
    
7. Następnie kliknij pozycję **Konfiguruj** , aby skonfigurować zainstalowaną wtyczkę.

    ![Zrzut ekranu przedstawiający stronę "Zarządzanie aplikacjami" z wybranym przyciskiem "Konfiguruj" dla aplikacji "S A M L SingleSignOn for JIRA".](./media/samlssojira-tutorial/store-5.png)

8. W kreatorze **konfiguracji wtyczki SAML SingleSignon** kliknij pozycję **Dodaj nowe dostawcy tożsamości** , aby skonfigurować usługę Azure AD jako nowego dostawcę tożsamości.

    ![Zrzut ekranu przedstawia stronę "Welcome" z wybranym przyciskiem "Dodaj nowe I d P".](./media/samlssojira-tutorial/add-on-4.png) 

9. Na stronie **Wybierz dostawcę tożsamości SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający stronę "wybieranie dostawcy tożsamości S A M" z wyróżnionymi polami tekstowymi "i d" i "nazwa" oraz wybranym przyciskiem "dalej".](./media/samlssojira-tutorial/identity-provider.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. Dodaj **nazwę** dostawcy tożsamości (np. Azure AD).
    
    c. Dodaj (opcjonalnie) **Opis** dostawcy tożsamości (np. Azure AD).
    
    d. Kliknij przycisk **Dalej**.
    
10. Na stronie **Konfiguracja dostawcy tożsamości** kliknij przycisk **dalej**.
 
    ![Zrzut ekranu przedstawiający stronę "Konfiguracja dostawcy tożsamości".](./media/samlssojira-tutorial/configuration.png)

11. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Zrzut ekranu, na którym jest zaznaczona akcja "Importuj S A M g I d P" z "Select Metadata X M L".](./media/samlssojira-tutorial/metadata.png)

    a. Kliknij przycisk **Wybierz plik XML metadanych** i wybierz pobrany wcześniej plik **XML metadanych Federacji** .

    b. Kliknij przycisk **Importuj** .
     
    c. Poczekaj chwilę, aż import zakończy się pomyślnie.  
     
    d. Kliknij przycisk **Dalej**.
    
12. Na stronie **ATRYBUT identyfikatora użytkownika i transformacja** kliknij przycisk **dalej** .

    ![Zrzut ekranu przedstawiający stronę "atrybut i przekształcenie użytkownika" z wybranym przyciskiem "dalej".](./media/samlssojira-tutorial/transformation.png)
    
13. Na stronie **Tworzenie i aktualizowanie użytkownika** kliknij przycisk **Zapisz & dalej** , aby zapisać ustawienia.
    
    ![Zrzut ekranu przedstawiający stronę "Tworzenie i aktualizowanie użytkownika" z wybranym przyciskiem Zapisz & dalej.](./media/samlssojira-tutorial/update.png)
    
14. Na stronie **testowanie ustawień** kliknij przycisk **Pomiń test & Skonfiguruj ręcznie** , aby pominąć test użytkownika. Zostanie to wykonane w następnej sekcji i będzie wymagało pewnych ustawień w Azure Portal.
    
    ![Zrzut ekranu przedstawiający stronę "Testowanie ustawień" z wybranym przyciskiem "Pomiń test & Skonfiguruj ręcznie".](./media/samlssojira-tutorial/test.png)
    
15. Kliknij przycisk **OK** , aby pominąć ostrzeżenie.
    
    ![Zrzut ekranu pokazujący okno dialogowe ostrzeżenia z wybranym przyciskiem "O K".](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH

Aby umożliwić użytkownikom usługi Azure AD logowanie się do logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH, muszą one być obsługiwane w protokole SSO protokołu SAML dla JIRA przy użyciu rozwiązania GmbH. W przypadku tego samouczka konieczne jest ręczne przeprowadzenie aprowizacji. Istnieją jednak również inne modele aprowizacji dostępne dla wtyczki SAML SSO, które są obsługiwane przez rozwiązanie, na przykład **po prostu na czas** aprowizacji. Zapoznaj się z dokumentacją dotyczącą protokołu [SAML SSO przez rozwiązanie GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Jeśli masz pytanie dotyczące tego problemu, skontaktuj się z pomocą techniczną, aby uzyskać pomoc [techniczną](https://www.resolution.de/go/support).

**Aby ręcznie zainicjować obsługę administracyjną konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do wystąpienia JIRA jako administrator.

2. Umieść kursor nad koło zębate i wybierz pozycję **Zarządzanie użytkownikami**.

   ![Zrzut ekranu przedstawiający strzałkę wskazującą ikonę "koło zębate" z opcją "Zarządzanie użytkownikami" wybraną z listy rozwijanej.](./media/samlssojira-tutorial/user-1.png)

3. Jeśli nastąpi przekierowanie do strony dostępu administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź** .

    ![Zrzut ekranu przedstawiający stronę "dostęp administratora" z wyróżnioną polem tekstowym "hasło".](./media/samlssojira-tutorial/user-2.png) 

4. Na karcie **Zarządzanie użytkownikami** kliknij pozycję **Utwórz użytkownika**.

    ![Zrzut ekranu pokazujący kartę "Zarządzanie użytkownikami" z wybranym przyciskiem "Utwórz użytkownika".](./media/samlssojira-tutorial/user-3-new.png) 

5. Na stronie **"Tworzenie nowego użytkownika"** wykonaj następujące czynności. Musisz utworzyć użytkownika tak samo jak w usłudze Azure AD:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user-4-new.png) 

    a. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika:  <b>BrittaSimon@contoso.com</b> .

    b. W polu tekstowym **pełna nazwa** wpisz pełną nazwę użytkownika: **Britta Simon**.

    c. W polu tekstowym **Nazwa użytkownika** wpisz adres e-mail użytkownika: <b>BrittaSimon@contoso.com</b> . 

    d. W polu tekstowym **hasło** wprowadź hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika** , aby zakończyć tworzenie użytkownika.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do logowania jednokrotnego protokołu SAML dla JIRA za pomocą rozdzielczości GmbH adres URL, gdzie można zainicjować przepływ logowania.  

* Przejdź do logowania jednokrotnego SAML dla usługi JIRA, podając w adresie URL Logowanie do usługi GmbH, i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i zaloguj się do logowania jednokrotnego SAML dla JIRA za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SSO protokołu SAML dla usługi JIRA za pomocą rozwiązania GmbH w obszarze Moje aplikacje, jeśli jest skonfigurowana w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do rejestracji jednokrotnej protokołu SAML dla JIRA za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="enable-sso-redirection-for-jira"></a>Włączanie przekierowania rejestracji Jednokrotnej dla JIRA

Jak wspomniano w sekcji przed, istnieją dwa sposoby wyzwalania logowania jednokrotnego. Za pomocą **Azure Portal** lub **specjalnego linku do wystąpienia usługi JIRA**. Wtyczka SSO protokołu SAML przez rozwiązanie GmbH umożliwia również wyzwalanie logowania jednokrotnego, **uzyskując dostęp do dowolnego adresu URL wskazującego wystąpienie JIRA**.

W zasadzie wszyscy użytkownicy uzyskujący dostęp do JIRA zostaną przekierowani do logowania jednokrotnego po aktywowaniu opcji w wtyczki.

Aby uaktywnić przekierowywać Logowanie jednokrotne, wykonaj następujące czynności w **wystąpieniu programu JIRA**:

1. Dostęp do strony konfiguracji wtyczki rejestracji jednokrotnej protokołu SAML w JIRA.
1. Kliknij pozycję **przekierowanie** w lewym panelu.

   ![Zrzut ekranu przedstawiający stronę konfiguracji wtyczki JIRA SAML SingleSignOn wyróżnienie linku przekierowania w lewym okienku nawigacji.](./media/samlssojira-tutorial/configure-1.png)

1. **Włączenie przekierowania rejestracji JEDNOkrotnej**.

   ![Zrzut ekranu przedstawiający stronę konfiguracyjną wtyczki SAML SingleSignOn JIRA, zaznaczając pole wyboru Włącz przekierowanie rejestracji jednokrotnej.](./media/samlssojira-tutorial/configure-2.png) 

1. Naciśnij przycisk **Zapisz ustawienia** w prawym górnym rogu.

Po aktywowaniu opcji można nadal uzyskać dostęp do monitu o podanie nazwy użytkownika/hasła, jeśli opcja **Włącz Nosso** jest pokryta przez przechodzenie do `https://<server-base-url>/login.jsp?nosso` . Jak zawsze, należy zastąpić **\<server-base-url>** podstawowym adresem URL.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego SAML dla JIRA za pomocą rozdzielczości GmbH można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).