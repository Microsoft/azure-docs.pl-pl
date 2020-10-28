---
title: 'Samouczek: integracja Azure Active Directory z piaskownicą usługi Salesforce | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i piaskownicą usługi Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: bc27b1fea97471e9ca1991467902e666c1451605
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z piaskownicą usługi Salesforce

W tym samouczku dowiesz się, jak zintegrować piaskownicę usługi Salesforce z usługą Azure Active Directory (Azure AD). W przypadku integrowania piaskownicy usług Salesforce z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do piaskownicy usługi Salesforce.
* Zezwól użytkownikom na automatyczne logowanie do piaskownicy usług Salesforce przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) w usłudze Salesforce.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Piaskownica usługi Salesforce obsługuje usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Piaskownica usługi Salesforce obsługuje funkcję aprowizacji użytkowników **just in Time**
* Piaskownica usługi Salesforce obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](salesforce-sandbox-provisioning-tutorial.md)
* Po skonfigurowaniu piaskownicy usługi Salesforce można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych w organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Dodawanie piaskownicy usługi Salesforce z galerii

Aby skonfigurować integrację piaskownicy usług Salesforce z usługą Azure AD, musisz dodać piaskownicę usługi Salesforce z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **piaskownicę Salesforce** w polu wyszukiwania.
1. Wybierz pozycję **piaskownica usługi Salesforce** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla piaskownicy usług Salesforce

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą piaskownicy usługi Salesforce przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w piaskownicy Salesforce.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą piaskownicy Salesforce, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-salesforce-sandbox-sso)** w usłudze Salesforce w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego piaskownicy usługi Salesforce](#create-salesforce-sandbox-test-user)** , aby dysponować odpowiednikiem B. Simon w piaskownicy usług Salesforce, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **piaskownicy Salesforce** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie zainicjowania programu **dostawcy tożsamości** , wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych** .

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu** , aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż** .

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    > [!NOTE]
    > Plik metadanych dostawcy usług zostanie pobrany z portalu administracyjnego piaskownicy usługi Salesforce, który został wyjaśniony w dalszej części tego samouczka.

    c. Po pomyślnym przekazaniu pliku metadanych wartość **adresu URL odpowiedzi** zostanie automatycznie wypełniona w polu tekstowym **adres URL odpowiedzi** .

    ![image (obraz)](common/both-replyurl.png)

    > [!Note]
    > Jeśli wartość **adresu URL odpowiedzi** nie pobiera autopolulated, wprowadź wartość ręcznie zgodnie z wymaganiami.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie piaskownicy usługi Salesforce** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do piaskownicy usługi Salesforce.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **piaskownica usługi Salesforce** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-salesforce-sandbox-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Salesforce

1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora piaskownicy usługi Salesforce.

2. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

3. Przewiń w dół do **ustawień** w okienku nawigacji po lewej stronie, kliknij pozycję **tożsamość** , aby rozwinąć sekcję powiązaną. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

5. Wybierz pozycję **Włączona obsługa protokołu SAML** , a następnie kliknij pozycję **Zapisz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kliknij pozycję **Wybierz plik** , aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na stronie **Ustawienia logowania jednokrotnego SAML** pola zostaną wypełnione automatycznie. Kliknij pozycję Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na stronie **Ustawienia pojedynczej Sign-On** kliknij przycisk **Pobierz metadane** , aby pobrać plik metadanych dostawcy usług. Użyj tego pliku w sekcji **Podstawowa konfiguracja SAML** w Azure Portal, aby skonfigurować niezbędne adresy URL, jak wyjaśniono powyżej.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure4.png)

10. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , poniżej przedstawiono wymagania wstępne dotyczące programu:

    a. Należy mieć zweryfikowaną domenę.

    b. Należy skonfigurować i włączyć domenę w piaskownicy usługi Salesforce, kroki opisane w dalszej części tego samouczka.

    c. W Azure Portal w sekcji **Podstawowa konfiguracja języka SAML** kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:
  
    ![Informacje logowania jednokrotnego w domenie piaskownicy usługi Salesforce](common/both-signonurl.png)

    W polu tekstowym **adres URL logowania** wpisz wartość przy użyciu następującego wzorca: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość powinna być kopiowana z portalu piaskownicy usługi Salesforce po włączeniu domeny.

11. W sekcji **certyfikat podpisywania SAML** kliknij pozycję **XML metadanych Federacji** , a następnie Zapisz plik XML na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

12. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora piaskownicy usługi Salesforce.

13. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

14. Przewiń w dół do **ustawień** w okienku nawigacji po lewej stronie, kliknij pozycję **tożsamość** , aby rozwinąć sekcję powiązaną. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

16. Wybierz pozycję **Włączona obsługa protokołu SAML** , a następnie kliknij pozycję **Zapisz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Kliknij pozycję **Wybierz plik** , aby przekazać plik XML metadanych, a następnie kliknij przycisk **Utwórz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na stronie **Ustawienia pojedynczej Sign-On SAML** pola wypełniają automatycznie, wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test* ), w polu tekstowym **Nazwa** , a następnie kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Aby włączyć domenę w piaskownicy usługi Salesforce, wykonaj następujące czynności:

    > [!NOTE]
    > Przed włączeniem domeny należy utworzyć tę samą w piaskownicy usługi Salesforce. Aby uzyskać więcej informacji, zobacz [Definiowanie nazwy domeny](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po utworzeniu domeny upewnij się, że została prawidłowo skonfigurowana.

21. W okienku nawigacji po lewej stronie w obszarze piaskownicy usługi Salesforce kliknij pozycję **Ustawienia firmy** , aby rozwinąć sekcję powiązaną, a następnie kliknij pozycję **moja domena** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. W sekcji **konfiguracja uwierzytelniania** kliknij pozycję **Edytuj** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. W sekcji **konfiguracja uwierzytelniania** jako **Usługa uwierzytelniania** wybierz nazwę ustawienia Single Sign-On SAML, które zostało ustawione podczas konfigurowania logowania jednokrotnego w piaskownicy usługi Salesforce, a następnie kliknij przycisk **Zapisz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Utwórz użytkownika testowego piaskownicy usługi Salesforce

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w piaskownicy usługi Salesforce. Piaskownica usługi Salesforce obsługuje obsługę just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w piaskownicy usługi Salesforce, zostanie utworzony nowy, gdy spróbujesz uzyskać dostęp do piaskownicy usługi Salesforce. Piaskownica usługi Salesforce obsługuje również automatyczne Inicjowanie obsługi użytkowników. więcej informacji można znaleźć w [tym miejscu](salesforce-sandbox-provisioning-tutorial.md) w artykule Jak skonfigurować automatyczne Inicjowanie obsługi użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka piaskownicy usługi Salesforce w panelu dostępu należy automatycznie zalogować się do piaskownicy usługi Salesforce, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj piaskownicę usług Salesforce z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/protect-salesforce)

- [Konfigurowanie aprowizacji użytkowników](salesforce-sandbox-provisioning-tutorial.md)

- [Jak chronić piaskownicę usługi Salesforce przy użyciu zaawansowanej widoczności i kontroli](/cloud-app-security/proxy-intro-aad)