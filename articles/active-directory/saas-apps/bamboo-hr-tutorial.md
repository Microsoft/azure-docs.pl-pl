---
title: 'Samouczek: integracja Azure Active Directory z usługą BambooHR | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 8025728ffc40aca27807068eff29f5a889a8d76e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331400"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Samouczek: integracja Azure Active Directory z usługą BambooHR

W tym samouczku dowiesz się, jak zintegrować usługę BambooHR z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi BambooHR z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do BambooHR.
* Zezwól użytkownikom na automatyczne logowanie się do usługi BambooHR przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) BambooHR.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja BambooHR obsługuje logowanie jednokrotne inicjowane przez **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.


## <a name="adding-bamboohr-from-the-gallery"></a>Dodawanie aplikacji BambooHR z galerii

Aby skonfigurować integrację aplikacji BambooHR z usługą Azure AD, należy dodać aplikację BambooHR z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BambooHR** w polu wyszukiwania.
1. Wybierz pozycję **BambooHR** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla BambooHR

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą BambooHR przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w BambooHR.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą BambooHR, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-bamboohr-sso)** w usłudze BambooHR, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    * **[Tworzenie użytkownika testowego aplikacji BambooHR](#create-bamboohr-test-user)** — aby zapewnić odpowiednik użytkownika Britta Simon w aplikacji BambooHR powiązany z reprezentacją użytkownika usługi Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **BambooHR** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company>.bamboohr.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | Adres URL odpowiedzi |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta BambooHR](https://www.bamboohr.com/contact.php) , aby uzyskać wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację BambooHR** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi BambooHR.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BambooHR**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-bamboohr-sso"></a>Konfigurowanie logowania jednokrotnego BambooHR

1. W nowym oknie zaloguj się jako administrator do witryny firmowej aplikacji BambooHR.

2. Na stronie głównej wykonaj następujące czynności:
   
    ![Strona BambooHR Single Sign-On](./media/bamboo-hr-tutorial/ic796691.png "Logowanie jednokrotne")   

    a. Wybierz pozycję **Aplikacje**.
   
    b. W okienku **Aplikacje** wybierz opcję **Logowanie jednokrotne**.
   
    c. Wybierz pozycję **Logowanie jednokrotne SAML**.

3. W okienku **Logowanie jednokrotne SAML** wykonaj następujące czynności:
   
    ![Okienko SAML Single Sign-On](./media/bamboo-hr-tutorial/IC796692.png "Single Sign-On SAML")
   
    a. W polu **Adres URL logowania SSO** wklej **adres URL logowania** skopiowany z witryny Azure Portal w kroku 6.
      
    b. W programie Notatnik otwórz certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej go w polu **Certyfikat X.509**.
   
    c. Wybierz pozycję **Zapisz**.

### <a name="create-bamboohr-test-user"></a>Tworzenie użytkownika testowego BambooHR

Aby umożliwić użytkownikom usługi Azure AD logowanie w aplikacji BambooHR, skonfiguruj ich ręcznie w aplikacji BambooHR, wykonując następujące czynności:

1. Zaloguj się do witryny **BambooHR** jako administrator.

2. Na pasku narzędzi u góry wybierz pozycję **Ustawienia**.
   
    ![Przycisk Settings (Ustawienia)](./media/bamboo-hr-tutorial/IC796694.png "Ustawienie")

3. Wybierz pozycję **Omówienie**.

4. W lewym okienku wybierz pozycję Użytkownicy **zabezpieczeń**  >  **Users**.

5. Wpisz nazwę użytkownika, hasło i adres e-mail prawidłowego konta usługi Azure AD, które chcesz skonfigurować.

6. Wybierz pozycję **Zapisz**.
        
>[!NOTE]
>Do skonfigurowania kont użytkowników usługi Azure AD można użyć również narzędzi do tworzenia kont użytkowników w aplikacji BambooHR lub interfejsów API.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania BambooHR, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania BambooHR i zainicjuj w nim przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka BambooHR w panelu dostępu zostanie on przekierowany do adresu URL logowania BambooHR. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu BambooHR można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).