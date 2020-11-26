---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z pakietem servicesuite | Microsoft Docs'
description: Dowiedz się, jak można skonfigurować funkcję logowania jednokrotnego między usługą Azure Active Directory i aplikacją NetSuite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: jeedes
ms.openlocfilehash: 9272114dd472717e59680b40c07d8435dea19682
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181972"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Samouczek: integracja logowania jednokrotnego w usłudze Azure AD z pakietem

W tym samouczku dowiesz się, jak zintegrować pakiet z usługą Azure Active Directory (Azure AD). Podczas integrowania pakietu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pakietu.
* Zezwól użytkownikom na automatyczne logowanie do pakietu z użyciem kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) dla pakietu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

Pakiet servicesuite obsługuje:

* Zainicjowane przez dostawcy tożsamości Logowanie jednokrotne.
* Inicjowanie obsługi administracyjnej użytkowników w trybie JIT (just-in-Time).
* [Automatyczne Inicjowanie obsługi użytkowników](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Ponieważ identyfikator tej aplikacji jest stałą wartością ciągu, można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-netsuite-from-the-gallery"></a>Dodaj pakiet z galerii

Aby skonfigurować integrację pakietu w usłudze Azure AD, Dodaj pakiet z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. Zaloguj się do Azure Portal za pomocą konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **pakiet** w polu wyszukiwania.
1. W okienku wyników wybierz pozycję **zestaw**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu z użyciem użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika B. Simon.  
    * [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić użytkownikowi B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj logowanie](#configure-netsuite-sso) jednokrotne w ramach pakietu, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * [Utwórz użytkownika testowego pakietu](#create-the-netsuite-test-user) dla sieci, aby dysponować odpowiednikiem użytkownika B. Simon w pakiecie, który jest połączony z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące czynności:

1. W Azure Portal na stronie **integracja aplikacji sieci** Web Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** ("ołówek") obok pozycji **Podstawowa konfiguracja SAML**.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** , w polu tekstowym **adres URL odpowiedzi** wpisz adres URL w jednym z następujących formatów:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Uzyskasz **<`Instance ID`>** wartość w sekcji konfiguracja pakietu, która została omówiona w dalszej części samouczka w kroku 8 w obszarze Konfiguracja pakietu. Dokładna domena (na przykład system.na0.netsuite.com w tym przypadku) zostanie znaleziona.

        ![Zrzut ekranu przedstawia stronę konfiguracji protokołu SAML, na której można uzyskać domenę.](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Wartości w poprzednich adresach URL nie są prawdziwe. Zaktualizuj je za pomocą adresu URL rzeczywistej odpowiedzi. Aby uzyskać wartość, skontaktuj się z [zespołem obsługi klienta dla pakietu](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Można również odnieść się do formatów przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. Aplikacja dla pakietu servicesuite oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja pakietu servicesuite oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > Wartość atrybutu Account nie jest prawdziwa. Ta wartość zostanie zaktualizowana zgodnie z opisem w dalszej części tego samouczka.

1. Na stronie Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML w sekcji certyfikat podpisywania SAML Znajdź plik XML metadanych Federacji i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie pakietu** , skopiuj odpowiedni adres URL lub adresy URL, w zależności od wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

1. W okienku właściwości **użytkownika** wykonaj następujące kroki:

   a. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   b. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension (na przykład B.Simon@contoso.com ).  
   c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.  
   d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączona funkcja User B. Simon, która umożliwia logowanie jednokrotne na platformie Azure przez przyznanie dostępu do pakietu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Z listy Aplikacje wybierz pozycję **zestaw**.
1. W okienku Przegląd poszukaj sekcji **Zarządzanie** , a następnie wybierz łącze **Użytkownicy i grupy** .
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.
1. W okienku **Użytkownicy i grupy** , na liście rozwijanej **Użytkownicy** wybierz pozycję **B. Simon**, a następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, wykonaj następujące czynności:

   a. W okienku **Wybierz rolę** z listy rozwijanej wybierz odpowiednią rolę dla użytkownika.  
   b. W dolnej części ekranu wybierz przycisk **Wybierz** .
1. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** .

## <a name="configure-netsuite-sso"></a>Konfigurowanie logowania jednokrotnego dla pakietu

1. Otwórz nową kartę w przeglądarce i zaloguj się do swojej witryny firmowej jako administrator.

2. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**, a następnie **Company** wybierz pozycję  >  **funkcje włączania** firmy.

    ![Zrzut ekranu przedstawia funkcje włączania wybrane z firmy.](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na pasku narzędzi w środku strony wybierz pozycję **SuiteCloud**.

    ![Zrzut ekranu przedstawia SuiteCloud zaznaczone.](./media/NetSuite-tutorial/ns-suitecloud.png)

4. W obszarze **Zarządzanie uwierzytelnianiem** zaznacz pole wyboru Protokół **SAML logowanie** jednokrotne, aby włączyć opcję logowania jednokrotnego protokołu SAML w zestawie.

    ![Zrzut ekranu przedstawia Zarządzanie uwierzytelnianiem, w którym można wybrać opcję Logowanie jednokrotne w protokole SAML.](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na górnym pasku nawigacyjnym wybierz pozycję **Konfiguracja**.

    ![Zrzut ekranu przedstawia konfigurację wybraną na pasku nawigacyjnym pakietu.](./media/NetSuite-tutorial/ns-setup.png)

6. Na liście **zadania Instalatora** wybierz pozycję **integracja**.

    ![Zrzut ekranu przedstawia integrację wybraną z zadań Instalatora.](./media/NetSuite-tutorial/ns-integration.png)

7. W obszarze **Zarządzanie uwierzytelnianiem** wybierz pozycję **SAML logowanie** jednokrotne.

    ![Zrzut ekranu pokazujący Logowanie jednokrotne SAML wybrane z elementu Integration w ZADANIAch INSTALACYJNYch.](./media/NetSuite-tutorial/ns-saml.png)

8. W okienku **Instalatora SAML** w obszarze **Konfiguracja pakietu**, wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia konfigurację języka SAML, w której można wprowadzić podane wartości.](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Zaznacz pole wyboru **podstawowa metoda uwierzytelniania** .

    b. W obszarze **metadane dostawcy tożsamości SAMLV2** wybierz pozycję **Przekaż plik metadanych dostawcy tożsamości**, a następnie wybierz pozycję **Przeglądaj** , aby przekazać pobrany plik metadanych z Azure Portal.

    c. Wybierz pozycję **Prześlij**.

9. Na górnym pasku nawigacyjnym pakietu, wybierz pozycję **Instalator**, a następnie **Wybierz pozycję**  >  **Informacje o firmie**.

    ![Zrzut ekranu przedstawia informacje o firmie wybrane z firmy.](./media/NetSuite-tutorial/ns-com.png)

    ![Zrzut ekranu przedstawia okienko, w którym można wprowadzić podane wartości.](./media/NetSuite-tutorial/ns-account-id.png)

    b. W okienku **Informacje o firmie** w prawej kolumnie skopiuj wartość **Identyfikator konta** .

    c. Wklej **Identyfikator konta** skopiowany z konta usługi w polu **wartość atrybutu** w usłudze Azure AD.

    ![Zrzut ekranu przedstawiający dodanie wartości identyfikatora konta](./media/netsuite-tutorial/attribute-value.png)

10. Zanim użytkownicy będą mogli wykonywać logowanie jednokrotne do aplikacji NetSuite, należy im wcześniej przypisać odpowiednie uprawnienia w tej aplikacji. Aby przypisać te uprawnienia, wykonaj następujące czynności:

    a. Na górnym pasku nawigacyjnym wybierz pozycję **Konfiguracja**.

    ![Zrzut ekranu przedstawia konfigurację wybraną na pasku nawigacyjnym pakietu.](./media/NetSuite-tutorial/ns-setup.png)

    b. W lewym okienku wybierz pozycję **użytkownicy/role**, a następnie wybierz pozycję **Zarządzaj rolami**.

    ![Zrzut ekranu przedstawia okienko Zarządzanie rolami, w którym można wybrać nową rolę.](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Wybierz pozycję **Nowa rola**.

    d. Wprowadź **nazwę** nowej roli.

    ![Zrzut ekranu przedstawia Menedżera ustawień, w którym można wprowadzić nazwę roli.](./media/NetSuite-tutorial/ns-new-role.png)

    e. Wybierz pozycję **Zapisz**.

    f. Na górnym pasku nawigacyjnym wybierz pozycję **uprawnienia**. Następnie wybierz pozycję **Konfiguracja**.

    ![Zrzut ekranu przedstawia kartę Konfiguracja, w której można wprowadzić podane wartości.](./media/NetSuite-tutorial/ns-sso.png)

    przykład Wybierz pozycję **SAML logowanie** jednokrotne, a następnie wybierz pozycję **Dodaj**.

    h. Wybierz pozycję **Zapisz**.

    i. Na górnym pasku nawigacyjnym wybierz pozycję **Instalator**, a następnie wybierz pozycję **Menedżer instalacji**.

    ![Zrzut ekranu przedstawia konfigurację wybraną na pasku nawigacyjnym pakietu.](./media/NetSuite-tutorial/ns-setup.png)

    j. W lewym okienku wybierz pozycję **użytkownicy/role**, a następnie wybierz pozycję **Zarządzaj użytkownikami**.

    ![Zrzut ekranu przedstawia okienko zarządzanie użytkownikami, w którym można wybrać zespół demonstracyjny zestawu.](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego, wybierz pozycję **Edytuj**, a następnie wybierz kartę **dostęp** .

    ![Zrzut ekranu przedstawia okienko zarządzanie użytkownikami, w którym można wybrać opcję Edytuj.](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W okienku **role** Przypisz odpowiednią rolę utworzoną przez użytkownika.

    ![Zrzut ekranu przedstawia administratora wybranego przez pracownika.](./media/NetSuite-tutorial/ns-add-role.png)

    m. Wybierz pozycję **Zapisz**.

### <a name="create-the-netsuite-test-user"></a>Tworzenie użytkownika testowego pakietu

W tej sekcji użytkownik o nazwie B. Simon został utworzony w pakiecie. Aplikacja NetSuite obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik jeszcze nie istnieje w aplikacji NetSuite, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

- Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do pakietu, dla którego skonfigurowano Logowanie jednokrotne

- Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka pakietu w obszarze Moje aplikacje należy automatycznie zalogować się do pakietu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pakietu można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)