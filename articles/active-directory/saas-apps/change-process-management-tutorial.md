---
title: 'Samouczek: integracja SSO usługi Azure AD z zarządzaniem zmianami procesów'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zmieniać zarządzanie procesami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456262"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu zarządzania procesem zmiany

W tym samouczku dowiesz się, jak zintegrować zarządzanie procesem zmiany z usługą Azure Active Directory (Azure AD). W przypadku integrowania zarządzania zmianami procesów z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskać dostęp do zarządzania procesem zmiany.
* Zezwól użytkownikom na automatyczne logowanie się, aby zmienić zarządzanie procesem przy użyciu swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja zarządzania procesem zmiany z włączonym logowaniem jednokrotnym (SSO).

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

Usługa Change Process Management obsługuje logowanie jednokrotne zainicjowane przez dostawcy tożsamości.

Po skonfigurowaniu zarządzania procesem zmiany można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-change-process-management-from-the-gallery"></a>Dodawanie zarządzania procesem zmiany z galerii

Aby skonfigurować integrację zarządzania procesem zmiany z usługą Azure AD, musisz dodać zarządzanie procesem zmiany z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź polecenie **Zmień zarządzanie procesem** w polu wyszukiwania.
1. Wybierz pozycję **Zmień zarządzanie procesem** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby zarządzania procesem zmiany

Skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą zarządzania procesem zmiany przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w ramach zarządzania procesami zmiany.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zarządzania procesem zmiany, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    1. **[Przyznaj użytkownikowi testowemu](#grant-access-to-the-test-user)** uprawnienia umożliwiające użytkownikowi korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zarządzanie procesem rejestracji jednokrotnej](#configure-change-process-management-sso)** w aplikacji.
    1. **[Utwórz użytkownika testowego zarządzania procesem](#create-a-change-process-management-test-user)** , który jest odpowiednikiem reprezentacji usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **Zarządzanie** integracją aplikacji **zarządzania procesem** wybierz pozycję **Logowanie jednokrotne** w sekcji Zarządzaj.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk ołówek dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Przycisk ołówek dla podstawowej konfiguracji języka SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** wykonaj następujące czynności:

    a. W polu **Identyfikator** wprowadź adres URL w następującym wzorcu: `https://<hostname>:8443/`

    b. W polu **adres URL odpowiedzi** wprowadź adres URL w następującym wzorcu: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Poprzedni **Identyfikator** i wartości **adresu URL odpowiedzi** nie są rzeczywistymi wartościami, których należy użyć. Aby uzyskać rzeczywiste wartości, skontaktuj się z [zespołem pomocy technicznej ds. zarządzania procesem zmiany](mailto:support@realtech-us.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** dla **certyfikatu (base64)** , aby pobrać certyfikat i zapisać go na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zarządzania procesem** skopiuj odpowiedni adres URL lub adresy URL zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące czynności:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź \<username> @ \<companydomain> . \<extension> . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="grant-access-to-the-test-user"></a>Udzielanie dostępu użytkownikowi testowemu

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie temu użytkownikowi dostępu do zarządzania procesem zmian.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Zmień proces zarządzania**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-change-process-management-sso"></a>Skonfiguruj zarządzanie procesem rejestracji logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne na stronie Zarządzanie procesem zmiany, należy wysłać pobrany certyfikat base64 i odpowiednie adresy URL skopiowane z Azure Portal do [zespołu pomocy technicznej zarządzania procesem zmiany](mailto:support@realtech-us.com). Konfigurują połączenie SSO protokołu SAML, aby były poprawne po obu stronach.

### <a name="create-a-change-process-management-test-user"></a>Tworzenie użytkownika testowego zarządzania procesem zmiany
 Aby dodać użytkownika o nazwie B. Simon w usłudze zarządzania procesami, należy skontaktować się z [zespołem pomocy technicznej zarządzania procesem zmiany](mailto:support@realtech-us.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przedstawiono Testowanie konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka zarządzanie procesem w panelu dostępu należy automatycznie zalogować się do wystąpienia zarządzania procesem zmiany, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integracji aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Spróbuj zmienić zarządzanie procesem przy użyciu usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić zarządzanie procesami zmiany za pomocą zaawansowanej widoczności i kontrolek](/cloud-app-security/proxy-intro-aad)