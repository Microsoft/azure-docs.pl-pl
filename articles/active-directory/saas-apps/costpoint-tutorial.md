---
title: 'Samouczek: integracja Azure Active Directory z usługą Costpoint | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652933"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Samouczek: integracja Costpoint z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Costpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Costpoint z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Costpoint.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Costpoint przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Costpoint.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Usługa Costpoint obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="generate-costpoint-metadata"></a>Generuj metadane Costpoint

Costpoint konfiguracja logowania jednokrotnego SAML została omówiona w przewodniku **DeltekCostpoint711Security.pdf** . Pobierz ten przewodnik z witryny obsługi Costpoint deltek, a następnie zapoznaj się z sekcją **Konfiguracja logowania** jednokrotnego protokołu SAML  >  **między Costpoint i Microsoft Azure** . Postępuj zgodnie z instrukcjami i wygeneruj plik **XML metadanych Federacji COSTPOINT Sp** . 

![Zrzut ekranu przedstawiający "Narzędzie konfiguracji produktu" z wybraną kartą "WebLogic-Security".](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Dodaj Costpoint z galerii

Aby skonfigurować integrację programu Costpoint z usługą Azure AD, musisz dodać Costpoint z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Costpoint** w polu wyszukiwania.
1. Wybierz pozycję **Costpoint** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Costpoint

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Costpoint przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Costpoint.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Costpoint, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-costpoint-sso)** w usłudze Costpoint, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Costpoint](#create-costpoint-test-user)** , aby dysponować odpowiednikiem B. Simon w Costpoint, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. Na stronie integracja aplikacji **Costpoint** wybierz pozycję **Logowanie jednokrotne**.

1. Jeśli masz **plik metadanych dostawcy usług**, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

   > [!NOTE]
   > Plik metadanych dostawcy usług jest pobierany w temacie [generowanie metadanych Costpoint](#generate-costpoint-metadata). Jak używać pliku, wyjaśniono w dalszej części tego samouczka.
 
   1. Wybierz przycisk **Przekaż plik metadanych** , a następnie wybierz plik **XML metadanych Federacji Costpoint Sp** wcześniej wygenerowany przez Costpoint, a następnie wybierz przycisk **Dodaj** , aby przekazać plik.

      ![Przekaż plik metadanych](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są wypełniane automatycznie w sekcji Costpoint.

      > [!NOTE]
      > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie są autopolulated, wprowadź wartości ręcznie zgodnie z wymaganiami. Sprawdź, czy **Identyfikator (identyfikator jednostki)** i **adres URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** są poprawnie ustawione, a ten **adres URL usług ACS** jest prawidłowym adresem URL Costpoint kończącym się **/LoginServlet.CPS**.

   1. Wybierz pozycję **Ustaw dodatkowe adresy URL**. W polu **Stan przekazywania** wprowadź wartość przy użyciu następującego wzorca: `system=[your system]` (na przykład **system = DELTEKCP**).

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz ikonę **kopiowania** , aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go w Notatniku.

   ![Certyfikat podpisywania SAML](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Costpoint.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Costpoint**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-costpoint-sso"></a>Konfigurowanie logowania jednokrotnego Costpoint

1. Wróć do narzędzia konfiguracji Costpoint. W polu tekstowym **XML metadanych Federacji dostawcy tożsamości** wklej zawartość pliku *adresów URL metadanych federacji aplikacji* . 

   ![Narzędzie konfiguracji Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Kontynuuj instrukcje z przewodnika **DeltekCostpoint711Security.pdf** , aby zakończyć konfigurację SAML Costpoint.

### <a name="create-costpoint-test-user"></a>Utwórz użytkownika testowego Costpoint

W tej sekcji utworzysz użytkownika w Costpoint. Załóżmy, że identyfikator użytkownika to **b. Simon** , a nazwa użytkownika to **b. Simon**. Współpracuj z [zespołem obsługi klienta Costpoint](https://www.deltek.com/about/contact-us) , aby dodać użytkownika na platformie Costpoint. Aby można było korzystać z logowania jednokrotnego, użytkownik musi zostać utworzony i aktywowany.

Po utworzeniu użytkownika należy zaznaczyć pole wyboru **Metoda uwierzytelniania** dla użytkownika, które musi być **Active Directory**, musi być zaznaczona opcja Logowanie jednokrotne **protokołu SAML** , a nazwa użytkownika Azure Active Directory musi być **Active Directory lub identyfikatorem certyfikatu** (pokazanym na poniższym zrzucie ekranu).

![Costpoint użytkownika](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Costpoint, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania Costpoint i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Costpoint, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Costpoint w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Costpoint, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Costpoint można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).