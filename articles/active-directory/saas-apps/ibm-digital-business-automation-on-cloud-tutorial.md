---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą IBM Digital Business Automation w chmurze | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i IBM Digital Business Automation w chmurze.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: c3c2e9b8b37d3d81b9683e871844e2dd8d099074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88517065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą IBM Digital Business Automation w chmurze

W tym samouczku dowiesz się, jak zintegrować usługę IBM Digital Business Automation w chmurze z usługą Azure Active Directory (Azure AD). W przypadku integrowania usługi IBM Digital Business Automation w chmurze z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do automatyzacji cyfrowej firmy IBM w chmurze.
* Zezwól użytkownikom na automatyczne logowanie do usługi IBM Digital Business Automation w chmurze przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Usługa IBM Digital Business Automation w ramach subskrypcji z włączonym logowaniem jednokrotnym (SSO) w chmurze.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa IBM Digital Business Automation w chmurze obsługuje logowanie jednokrotne w usłudze **SP i dostawcy tożsamości**
* Po skonfigurowaniu usługi IBM Digital Business Automation w chmurze można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Dodawanie programu IBM Digital Business Automation w chmurze z galerii

Aby skonfigurować integrację usługi IBM Digital Business Automation w chmurze z usługą Azure AD, musisz dodać usługę IBM Digital Business Automation w chmurze z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz ciąg **IBM Digital Business Automation w chmurze** w polu wyszukiwania.
1. Wybierz pozycję **IBM Digital Business Automation w chmurze** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi IBM Digital Business Automation w chmurze

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi IBM Digital Business Automation w chmurze przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze IBM Digital Business Automation w chmurze.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi IBM Digital Business Automation w chmurze, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę IBM Digital Business Automation na potrzeby logowania jednokrotnego w chmurze](#configure-ibm-digital-business-automation-on-cloud-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz usługę IBM Digital Business Automation na potrzeby użytkownika testowego w chmurze](#create-ibm-digital-business-automation-on-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze IBM Digital Business Automation w chmurze, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z integracją z aplikacjami w **chmurze firmy IBM** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:
    
    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w polu tekstowym w usłudze IBM Digital Business Automation w chmurze:

    > [!Note]
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie zostaną wypełnione automatycznie, wprowadź wartości ręcznie zgodnie z wymaganiami.

    > [!Note]
    > Klienci mogą uzyskać plik metadanych dla swojej subskrypcji chmury od [zespołu pomocy technicznej IBM Digital Business Automation w chmurze](mailto:supportbpmoncloud@us.ibm.com).

1. Jeśli nie masz **pliku metadanych dostawcy usług**, w sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej IBM Digital Business Automation w chmurze](mailto:supportbpmoncloud@us.ibm.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu IBM Digital Business Automation w chmurze** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do automatyzacji Digital Business Automation w chmurze.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **IBM Digital Business Automation w chmurze**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Konfigurowanie usługi IBM Digital Business Automation na serwerze rejestracji jednokrotnej w chmurze

Aby skonfigurować Logowanie jednokrotne w usłudze **IBM Digital Business Automation** po stronie chmury, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [usługi IBM Digital Business Automation w chmurze pomocy technicznej chmury](mailto:supportbpmoncloud@us.ibm.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Utwórz użytkownika testowego IBM Digital Business Automation w chmurze

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze IBM Digital Business Automation w chmurze. Współpracuj z [zespołem pomocy technicznej IBM Digital Business Automation w chmurze](mailto:supportbpmoncloud@us.ibm.com) , aby dodać użytkowników w usłudze automatyzacji Digital Business Automation na platformie chmurowej. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka programu IBM Digital Business Automation na chmurze w panelu dostępu należy automatycznie zalogować się do usługi Automatyzacja firmy IBM w chmurze, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę IBM Digital Business Automation w chmurze z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić usługę IBM Digital Business Automation w chmurze z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

