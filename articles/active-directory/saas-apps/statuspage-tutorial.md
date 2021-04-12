---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą StatusPage | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 6bc2508220a8c0193efb55eb6cd11f7e151c80be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą StatusPage

W tym samouczku dowiesz się, jak zintegrować usługę StatusPage z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi StatusPage z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do StatusPage.
* Zezwól użytkownikom na automatyczne logowanie się do usługi StatusPage przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą StatusPage, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w StatusPage.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa StatusPage obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

## <a name="add-statuspage-from-the-gallery"></a>Dodaj StatusPage z galerii

Aby skonfigurować integrację programu StatusPage z usługą Azure AD, musisz dodać StatusPage z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **StatusPage** w polu wyszukiwania.
1. Wybierz pozycję **StatusPage** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla StatusPage

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą StatusPage na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w StatusPage.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą StatusPage, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-statuspage-sso)** w usłudze StatusPage, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego StatusPage](#create-statuspage-test-user)** , aby uzyskać odpowiednik Britta Simon w StatusPage, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **AskYourTeam** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Identyfikator |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:

     | Adres URL odpowiedzi |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Skontaktuj się z zespołem pomocy technicznej StatusPage pod adresem, [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) Aby zażądać metadanych niezbędnych do skonfigurowania logowania jednokrotnego. 
    >
    > a. Z metadanych skopiuj wartość wystawcy, a następnie wklej ją do pola tekstowego **identyfikatora** .
    >
    > b. Skopiuj adres URL odpowiedzi z metadanych, a następnie wklej go do pola tekstowego **adres URL odpowiedzi** .

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie StatusPage** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi StatusPage.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **StatusPage**.

2. Na liście Aplikacje wybierz pozycję **StatusPage**.

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-statuspage-sso"></a>Konfigurowanie logowania jednokrotnego StatusPage

1. Aby zautomatyzować konfigurację w programie StatusPage, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji StatusPage. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi StatusPage. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować StatusPage, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy StatusPage jako administrator.

1. Na głównym pasku narzędzi kliknij pozycję **Zarządzaj kontem**.

    ![Zrzut ekranu przedstawia konto zarządzania wybrane z witryny firmy StatusPage.](./media/statuspage-tutorial/account.png)

1. Kliknij kartę **Logowanie** jednokrotne.

    ![Zrzut ekranu przedstawia kartę Logowanie jednokrotne.](./media/statuspage-tutorial/tab.png)

1. Na stronie Konfiguracja logowania jednokrotnego wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę instalatora S-O, na której można wprowadzić podane wartości.](./media/statuspage-tutorial/setup.png)

    ![Zrzut ekranu przedstawia przycisk Zapisz konfigurację.](./media/statuspage-tutorial/configuration.png)

    a. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    b. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu** .

    c. Kliknij pozycję **Zapisz konfigurację**.

### <a name="create-statuspage-test-user"></a>Utwórz użytkownika testowego StatusPage

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w StatusPage.

StatusPage obsługuje Inicjowanie obsługi just-in-Time. Włączono już ją w obszarze [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso).

**Aby utworzyć użytkownika o nazwie Britta Simon in StatusPage, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny StatusPage jako administrator.

1. W menu u góry kliknij pozycję **Zarządzaj kontem**.

    ![Zrzut ekranu przedstawia konto zarządzania wybrane z witryny firmy StatusPage.](./media/statuspage-tutorial/account.png)

1. Kliknij kartę **członkowie zespołu** .
  
    ![Zrzut ekranu przedstawia kartę Członkowie zespołu.](./media/statuspage-tutorial/sandbox.png) 

1. Kliknij pozycję **Dodaj członka zespołu**.
  
    ![Zrzut ekranu przedstawia przycisk Dodaj członka zespołu.](./media/statuspage-tutorial/team.png) 

1. Wpisz **adres e-mail**, **imię** i **nazwisko** poprawnego użytkownika, który chcesz udostępnić do powiązanych pól tekstowych. 

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie użytkownika, w którym można wprowadzić podane wartości.](./media/statuspage-tutorial/user.png) 

1. Jako **rolę** wybierz pozycję **administrator klienta**.

1. Kliknij pozycję **Utwórz konto**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do StatusPage, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka StatusPage w obszarze Moje aplikacje należy automatycznie zalogować się do StatusPage, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu StatusPage można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).