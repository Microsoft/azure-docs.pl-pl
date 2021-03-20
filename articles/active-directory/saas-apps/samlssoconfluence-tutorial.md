---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla Confluence za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją SAML SSO for Confluence by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651284"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla Confluence przez rozwiązanie GmbH

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne SAML dla Confluence za pomocą rozwiązania GmbH z Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne SAML dla Confluence za pomocą rozwiązania GmbH z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego SAML dla Confluence za pomocą rozwiązania GmbH.
* Zezwól użytkownikom na automatyczne logowanie do logowania jednokrotnego SAML dla Confluence za pomocą rozwiązania GmbH z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Logowanie jednokrotne SAML dla usługi Confluence za pomocą rozdzielczości "Logowanie jednokrotne" w systemie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja SAML SSO for Confluence by resolution GmbH obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Dodaj Logowanie jednokrotne SAML dla Confluence za pomocą rozdzielczości GmbH z galerii

Aby skonfigurować integrację aplikacji SAML SSO for Confluence by resolution GmbH z usługą Azure AD, należy z poziomu galerii dodać tę aplikację do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **SSO logowania jednokrotnego w usłudze Confluence przez rozwiązanie GmbH** w polu wyszukiwania.
1. Wybierz pozycję **SAML SSO dla Confluence za pomocą rozdzielczości GmbH** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla logowania jednokrotnego SAML dla Confluence za pomocą rozwiązania GmbH

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w protokole SAML przez rozwiązanie GmbH przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze SAML SSO dla Confluence za pomocą rozwiązania GmbH.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z logowaniem jednokrotnym SAML dla Confluence za pomocą rozwiązania GmbH, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne SAML dla Confluence za pomocą rozdzielczości GmbH Logowanie jednokrotne](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** — aby w aplikacji SAML SSO for Confluence by resolution GmbH istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie " **Logowanie jednokrotne w usłudze SAML dla Confluence za pomocą rozwiązania GmbH** Application Integration" Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

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
1. Na liście Aplikacje wybierz pozycję **SAML SSO dla Confluence za pomocą rozdzielczości GmbH**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Konfigurowanie logowania jednokrotnego SAML dla Confluence za pomocą rozdzielczości GmbH Logowanie jednokrotne

1. W innym oknie przeglądarki internetowej zaloguj się do **portalu administracyjnego aplikacji SAML SSO for Confluence by resolution GmbH** jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.
    
    ![Zrzut ekranu pokazujący wybraną ikonę "koło zębate" oraz "Dodatki" wybrane z listy rozwijanej.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Nastąpi przekierowanie na stronę dostępu administratora. Wprowadź hasło, a następnie kliknij przycisk **Confirm** (Potwierdź).

    ![Zrzut ekranu przedstawiający stronę "dostęp administratora" z wybranym przyciskiem "Potwierdź".](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Na karcie **ATLASSIAN MARKETPLACE** (Platforma handlowa Atlassian) kliknij pozycję **Find new add-ons** (Znajdź nowe dodatki). 

    ![Zrzut ekranu pokazujący kartę "Attlassian Marketplace" z wybraną pozycją "Znajdź nowe dodatki".](./media/saml-sso-confluence-tutorial/add-on.png)

5. Wyszukaj pozycję **SAML Single Sign On (SSO) for Confluence**, a następnie kliknij przycisk **Install** (Zainstaluj), aby zainstalować nową wtyczkę protokołu SAML.

    ![Zrzut ekranu przedstawiający stronę "Znajdź nowe dodatki" z opcją "S A M L Logowanie jednokrotne (S S O) dla Confluence" w polu wyszukiwania i wybranym przyciskiem "Zainstaluj".](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Rozpocznie się instalacja wtyczki. Kliknij przycisk **Zamknij**.

    ![Zrzut ekranu przedstawiający okno dialogowe "Instalowanie".](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Zrzut ekranu pokazujący "zainstalowane i gotowe do użycia!" okno dialogowe z wybraną akcją "Zamknij".](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Kliknij pozycję **Zarządzaj**.

    ![Zrzut ekranu, na którym jest zaznaczona przycisk "S" Logowanie jednokrotne (y S O) dla Confluence "na stronie aplikacji.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu pokazujący stronę "Zarządzaj" z wybranym przyciskiem "Konfiguruj".](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Ten nowy dodatek plug-in można także znaleźć na karcie **USERS & SECURITY** (Użytkownicy i zabezpieczenia).

    ![Zrzut ekranu pokazujący kartę "Użytkownicy & zabezpieczenia" z wybraną pozycją "S A M L SingleSignOn".](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Na stronie **SAML SingleSignOn Plugin Configuration** (Konfiguracja wtyczki logowania jednokrotnego protokołu SAML) kliknij przycisk **Add new IdP** (Dodaj nowego dostawcę tożsamości), aby skonfigurować ustawienia dostawcy tożsamości.

    ![Zrzut ekranu przedstawiający stronę "S A M L SingleSignOn (Konfiguracja wtyczki") z wybranym przyciskiem "Dodaj nowe I d P".](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Na stronie **Choose your SAML Identity Provider** (Wybieranie dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający stronę "wybieranie dostawcy tożsamości A M L" z wyróżnionymi polami tekstowymi "I d", "nazwa" i "opis".](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. W polu **Name** (Nazwa) wprowadź nazwę dostawcy tożsamości, np. Azure AD.
    
    c. W polu **Description** (Opis) wprowadź opis dostawcy tożsamości, np. Azure AD.
    
    d. Kliknij przycisk **Dalej**.
    
12. Na stronie **Identity provider configuration** (Konfiguracja dostawcy tożsamości) kliknij przycisk **Next** (Dalej).

    ![Zrzut ekranu przedstawiający stronę "Konfiguracja dostawcy tożsamości" z wybranym przyciskiem "dalej".](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający stronę "Importuj S A M g I d P" z wybranymi przyciskami "Importuj", "Załaduj plik" i "dalej".](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Kliknij przycisk **Load File** (Załaduj plik), a następnie wybierz plik XML metadanych pobrany w kroku 5.

    b. Kliknij przycisk **Import** (Importuj).
    
    c. Zaczekaj chwilę na pomyślne zakończenie importowania.
    
    d. Kliknij przycisk **Next** (Dalej).
    
14. Na stronie **User ID attribute and transformation** (Atrybut identyfikatora użytkownika i transformacja) kliknij przycisk **Next** (Dalej).

    ![Zrzut ekranu przedstawiający stronę "atrybut identyfikatora użytkownika i transformacja" z wybranym przyciskiem "dalej".](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Na stronie **User creation and update** (Tworzenie i aktualizowanie użytkownika) kliknij pozycję **Save & Next** (Zapisz i przejdź dalej), aby zapisać ustawienia.   
    
    ![Zrzut ekranu przedstawiający stronę "Tworzenie i aktualizowanie użytkownika" z wybranym przyciskiem Zapisz & dalej.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Na stronie **Test your settings** (Przetestuj ustawienia) kliknij pozycję **Skip test & configure manually** (Pomiń test i skonfiguruj ręcznie), aby pominąć na razie test użytkownika. Zostanie on wykonany w następnej sekcji i będzie wymagał wprowadzenia pewnych ustawień w witrynie Azure Portal. 
    
    ![Zrzut ekranu przedstawiający stronę "Testowanie ustawień" z wybranym przyciskiem "Pomiń test & Skonfiguruj ręcznie".](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. W wyświetlonym oknie dialogowym odczytywanie **pominięcia testu...**, kliknij przycisk **OK**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego aplikacji SAML SSO for Confluence by resolution GmbH

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji SAML SSO for Confluence by resolution GmbH, należy aprowizować ich w tym rozwiązaniu.  
W przypadku aplikacji SAML SSO for Confluence by resolution GmbH aprowizację wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji SAML SSO for Confluence by resolution GmbH jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Zrzut ekranu pokazujący wybraną ikonę "koło zębate" i opcję "Zarządzanie użytkownikami" wybraną z menu.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. W obszarze Użytkownicy kliknij przycisk **Dodaj użytkowników** kartę. Na stronie **"Dodawanie użytkownika"** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, np. Britta Simon.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika Britta Simon.

    e. Kliknij pozycję **Confirm Password** (Potwierdź hasło), a następnie ponownie wprowadź hasło.
    
    f. Kliknij przycisk **Add** (Dodaj).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do logowania jednokrotnego protokołu SAML dla Confluence za pomocą rozdzielczości GmbH adres URL, gdzie można zainicjować przepływ logowania.  

* Przejdź do logowania jednokrotnego SAML dla usługi Confluence, podając w adresie URL Logowanie do usługi GmbH, i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i zaloguj się do logowania jednokrotnego SAML dla Confluence za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SSO protokołu SAML dla usługi Confluence za pomocą rozwiązania GmbH w obszarze Moje aplikacje, jeśli jest skonfigurowana w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do rejestracji jednokrotnej SAML dla Confluence za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego SAML dla Confluence za pomocą rozdzielczości GmbH można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).