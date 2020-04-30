---
title: 'Samouczek: integracja Azure Active Directory z portalem programu Palo Alto Networks Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Palo Alto Networks Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160160"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Samouczek: integracja Azure Active Directory z portalem programu Palo Alto Networks

Z tego samouczka dowiesz się, jak zintegrować aplikację Palo Alto Networks Captive Portal z usługą Azure Active Directory (Azure AD).

Zintegrowanie aplikacji Palo Alto Networks Captive Portal z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do aplikacji Palo Alto Networks Captive Portal.
* Możesz automatycznie logować użytkowników do aplikacji Palo Alto Networks Captive Portal (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do zintegrowania usługi Azure AD z aplikacją Palo Alto Networks Captive Portal potrzebne są następujące elementy:

* Subskrypcja usługi Azure Active Directory. Jeśli nie masz usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja aplikacji Palo Alto Networks Captive Portal z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

Aplikacja Palo Alto Networks Captive Portal obsługuje następujące scenariusze:

* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**
* **Aprowizowanie użytkowników typu just in time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Dodawanie aplikacji Palo Alto Networks Captive Portal z galerii

Aby rozpocząć pracę, w galerii dodaj aplikację Palo Alto Networks Captive Portal do listy zarządzanych aplikacji SaaS:

1. W witrynie [Azure Portal](https://portal.azure.com) w menu po lewej stronie wybierz usługę **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Wybierz pozycję **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje**.

    ![Opcja Aplikacje dla przedsiębiorstw w menu](common/enterprise-applications.png)

3. Wybierz pozycję **Nowa aplikacja**.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Palo Alto Networks Captive Portal**. W wynikach wyszukiwania wybierz pozycję **Palo Alto Networks Captive Portal**, a następnie wybierz pozycję **Dodaj**.

     ![Pozycja Palo Alto Networks - Captive Portal na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Palo Alto Networks Captive Portal, korzystając z danych testowego użytkownika o nazwie *Britta Simon*. Aby logowanie jednokrotne działało, należy ustanowić relację między użytkownikiem usługi Azure AD i tym samym użytkownikiem w aplikacji Palo Alto Networks Captive Portal. 

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Palo Alto Networks Captive Portal, należy wykonać następujące zadania:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-single-sign-on)**: Zezwól użytkownikowi na korzystanie z tej funkcji.
2. Konfigurowanie logowania jednokrotnego w **[portalu Palo Alto Networks](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Skonfiguruj ustawienia logowania jednokrotnego w aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**: testowanie logowania jednokrotnego w usłudze Azure AD za pomocą użytkownika *Britta Simon*.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**: Konfigurowanie usługi Britta Simon do korzystania z logowania jednokrotnego w usłudze Azure AD.
5. **Utwórz użytkownika testowego portalu Palo Alto Networks**: Utwórz odpowiedni użytkownika *Britta Simon* w portalu internetowym w Palo Alto Networks, który jest połączony z użytkownikiem usługi Azure AD.
6. **[Testuj Logowanie jednokrotne](#test-single-sign-on)**: Sprawdź, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Najpierw włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Palo Alto Networks - Captive Portal** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W okienku **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. W okienku **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka **Edytuj**.

    ![Ikona ołówka Edytuj](common/edit-urls.png)

4. W okienku **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Okienko podstawowej konfiguracji protokołu SAML](common/idp-intiated.png)

   1. W polu **Identyfikator** wprowadź adres URL ze wzorcem `https://<customer_firewall_host_name>/SAML20/SP`.

   2. W polu **Adres URL odpowiedzi** wprowadź adres URL ze wzorcem `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > W tym kroku zaktualizuj wartości symboli zastępczych przy użyciu rzeczywistego identyfikatora i adresów URL odpowiedzi. Aby uzyskać te rzeczywiste wartości, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. W sekcji **Certyfikat podpisywania SAML** obok pola **Kod XML metadanych federacji** wybierz pozycję **Pobierz**. Zapisz pobrany plik na komputerze.

    ![Link pobierania w obszarze kodu XML metadanych federacji](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Palo Alto Networks Captive Portal

Następnie skonfiguruj logowanie jednokrotne w aplikacji Palo Alto Networks Captive Portal:

1. W innym oknie przeglądarki zaloguj się w witrynie internetowej aplikacji Palo Alto Networks jako administrator.

2. Wybierz kartę **Device** (Urządzenie).

    ![Karta Device (Urządzenie) w aplikacji Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. W menu wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML), a następnie wybierz przycisk **Import** (Importuj).

    ![Przycisk Import (Importuj)](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. W oknie dialogowym **SAML Identity Provider Server Profile Import** (Importowanie profilu serwera dostawcy tożsamości SAML) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego w aplikacji Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. W polu **Profile Name** (Nazwa profilu) wprowadź nazwę, taką jak **AzureAD-CaptivePortal**.
    
    2. Obok pola **Identity Provider Metadata** (Metadane dostawcy tożsamości) wybierz przycisk **Browse** (Przeglądaj). Wybierz plik metadata.xml pobrany w witrynie Azure Portal.
    
    3. Wybierz przycisk **OK**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Następnie utwórz użytkownika testowego o nazwie *Britta Simon* w witrynie Azure Portal:

1. W Azure Portal wybierz pozycję **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik**.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W okienku **Użytkownik** wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wprowadź ciąg **BrittaSimon**.
  
    2. W obszarze **Nazwa użytkownika**wprowadź **BrittaSimon\@\<your_company_domain\>**. Na przykład **BrittaSimon\@contoso.com**.

    3. W polu **Hasło** wprowadź hasło. Zalecamy zapisanie i zachowanie wprowadzonego hasła. Możesz zaznaczyć pole wyboru **Pokaż hasło**, aby wyświetlić hasło.

    4. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Następnie przyznaj dostęp do aplikacji Palo Alto Networks Captive Portal, aby użytkownik Britta Simon mógł korzystać z logowania jednokrotnego na platformie Azure:

1. W Azure Portal wybierz pozycję **aplikacje** > dla przedsiębiorstw**wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wprowadź **Palo Alto Networks - Captive Portal**, a następnie wybierz aplikację.

    ![Link do aplikacji Palo Alto Networks - Captive Portal na liście aplikacji](common/all-applications.png)

3. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W okienku **Użytkownicy i grupy** na liście **Użytkownicy** wybierz pozycję **Britta Simon**. Wybierz przycisk **Wybierz**.

6. Aby dodać wartość roli do potwierdzenia SAML, w okienku **Wybieranie roli ** wybierz odpowiednią rolę użytkownika. Wybierz przycisk **Wybierz**.

7. W okienku **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks Captive Portal

Następnie utwórz użytkownika o nazwie *Britta Simon* w aplikacji Palo Alto Networks Captive Portal. Aplikacja Palo Alto Networks Captive Portal obsługuje aprowizację użytkowników typu just in time, która jest domyślnie włączona. W tej sekcji nie trzeba wykonywać żadnych zadań. Jeśli użytkownik jeszcze nie istnieje w aplikacji Palo Alto Networks Captive Portal, zostanie utworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Aplikacja Palo Alto Networks Captive Portal jest instalowana za zaporą na maszynie wirtualnej systemu Windows. Aby przetestować logowanie jednokrotne w aplikacji Palo Alto Networks Captive Portal, zaloguj się na maszynie wirtualnej z systemem Windows przy użyciu protokołu RDP (Remote Desktop Protocol). W sesji protokołu RDP otwórz przeglądarkę i przejdź do dowolnej witryny internetowej. Zostanie otwarty adres URL logowania jednokrotnego i pojawi się monit o uwierzytelnienie. Po zakończeniu uwierzytelniania będzie można uzyskiwać dostęp do witryn internetowych.

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby dowiedzieć się więcej, zobacz następujące artykuły:

- [Tutorials about integrating SaaS apps with Azure Active Directory (Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Conditional Access in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) (Dostęp warunkowy w usłudze Azure Active Directory)

