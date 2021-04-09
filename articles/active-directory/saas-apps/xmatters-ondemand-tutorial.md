---
title: 'Samouczek: integracja Azure Active Directory z usługą xMatters OnDemand | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735621"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Samouczek: integracja Azure Active Directory z usługą xMatters OnDemand

W tym samouczku dowiesz się, jak zintegrować usługę xMatters OnDemand z usługą Azure Active Directory (Azure AD).
Integracja usługi xMatters OnDemand z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do xMatters OnDemand.
* Możesz umożliwić użytkownikom automatyczne logowanie do xMatters OnDemand (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą xMatters OnDemand, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączonym logowaniem jednokrotnym w usłudze xMatters OnDemand

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa xMatters OnDemand obsługuje **dostawcy tożsamości** zainicjowane przez usługę SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Dodawanie xMatters OnDemand z galerii

Aby skonfigurować integrację usługi xMatters OnDemand w usłudze Azure AD, musisz dodać OnDemand xMatters z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **xMatters OnDemand** w polu wyszukiwania.
1. Wybierz pozycję **XMatters OnDemand** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla xMatters OnDemand

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą xMatters OnDemand przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w xMatters OnDemand.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą xMatters OnDemand, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    2. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj XMatters OnDemand Logowanie jednokrotne](#configure-xmatters-ondemand-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego XMatters OnDemand](#create-xmatters-ondemand-test-user)** , aby uzyskać odpowiednik Simon Britta w xMatters OnDemand, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **XMatters OnDemand** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Identyfikator |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Adres URL odpowiedzi |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta XMatters OnDemand](https://www.xmatters.com/company/contact-us/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Musisz przesłać dalej certyfikat do [zespołu pomocy technicznej XMatters OnDemand](https://www.xmatters.com/company/contact-us/). Aby można było sfinalizować konfigurację logowania jednokrotnego, certyfikat musi zostać przekazany przez zespół pomocy technicznej xMatters.

6. W sekcji **Konfigurowanie XMatters OnDemand** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do xMatters OnDemand.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **XMatters OnDemand**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola &quot;domyślny dostęp&quot;.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .


## <a name=&quot;configure-xmatters-ondemand-sso&quot;></a>Konfigurowanie logowania jednokrotnego w usłudze xMatters OnDemand

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy XMatters OnDemand jako administrator.

2. Kliknij pozycję **administrator**, a następnie kliknij pozycję **szczegóły firmy**.

    ![Strona administratora](./media/xmatters-ondemand-tutorial/admin.png &quot;Administracja")

3. Na stronie **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki:

    ![Sekcja konfiguracji SAML ](./media/xmatters-ondemand-tutorial/saml-configuration.png "Konfiguracja SAML")

    a. Wybierz pozycję **Enable SAML** (Włącz SAML).

    b. W polu tekstowym **Identyfikator dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    c. W polu tekstowym **adres URL** logowania jednokrotnego wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **przekierowanie adresu URL logowania** wklej **adres URL wylogowywania**, który został skopiowany z Azure Portal.

    e. Kliknij pozycję **Wybierz plik** , aby przekazać **certyfikat (base64)** pobrany z Azure Portal. 

    f. Na stronie Szczegóły firmy u góry kliknij przycisk **Zapisz zmiany**.

    ![Szczegóły firmy](./media/xmatters-ondemand-tutorial/save-button.png "Szczegóły firmy")

### <a name="create-xmatters-ondemand-test-user"></a>Utwórz użytkownika testowego xMatters OnDemand

1. Zaloguj się do dzierżawy **OnDemand XMatters** .

2. Przejdź do **ikony użytkownicy**  >   , a następnie kliknij pozycję **Dodaj użytkowników**.

    ![Użytkownicy](./media/xmatters-ondemand-tutorial/add-user.png "Użytkownicy")

3. W sekcji **Dodaj użytkowników** Wypełnij pola wymagane i kliknij przycisk **Dodaj użytkownika** .

    ![Dodawanie użytkownika](./media/xmatters-ondemand-tutorial/add-user-2.png "Dodawanie użytkownika")



### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do xMatters OnDemand, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka xMatters OnDemand w obszarze Moje aplikacje należy automatycznie zalogować się do OnDemand xMatters, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu xMatters OnDemand można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).