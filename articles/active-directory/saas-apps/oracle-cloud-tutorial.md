---
title: 'Samouczek: integracja Azure Active Directory z konsolą infrastruktury w chmurze firmy Oracle | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i konsolą infrastruktury w chmurze firmy Oracle.
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
ms.openlocfilehash: d4a478c402346e33a4f27ff2a363de75285b435b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543843"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Samouczek: Integrowanie konsoli infrastruktury chmurowej Oracle z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować konsolę infrastruktury w chmurze firmy Oracle z usługą Azure Active Directory (Azure AD). Po zintegrowaniu konsoli infrastruktury chmurowej Oracle z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do konsoli infrastruktury w chmurze firmy Oracle.
* Zezwól użytkownikom na automatyczne logowanie do konsoli infrastruktury firmy Oracle przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) w konsoli infrastruktury chmurowej firmy Oracle.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Konsola infrastruktury w chmurze firmy Oracle obsługuje funkcję SSO zainicjowaną przez usługę **SP** .
* Po skonfigurowaniu konsoli infrastruktury w chmurze firmy Oracle można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Dodawanie konsoli platformy Oracle Cloud Infrastructure z galerii

Aby skonfigurować integrację konsoli infrastruktury w chmurze firmy Oracle z usługą Azure AD, musisz dodać konsolę infrastruktury w chmurze Oracle z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **konsola infrastruktury w chmurze firmy Oracle** .
1. Wybierz pozycję **Oracle Cloud Infrastructure Console** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Oracle Cloud Infrastructure Console przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w konsoli infrastruktury w chmurze firmy Oracle.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Oracle Cloud Infrastructure Console, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj konsolę infrastruktury w chmurze firmy Oracle](#configure-oracle-cloud-infrastructure-console)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego konsoli infrastruktury firmy Oracle](#create-oracle-cloud-infrastructure-console-test-user)** w celu uzyskania odpowiedników B. Simon w konsoli infrastruktury w chmurze firmy Oracle, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **konsoli infrastruktury w chmurze firmy Oracle** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

   > [!NOTE]
   > Plik metadanych dostawcy usług zostanie pobrany z sekcji Konfigurowanie logowania jednokrotnego w **konsoli infrastruktury w chmurze firmy Oracle** w samouczku.
    
   1. Kliknij pozycję **Przekaż plik metadanych**.

   1. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

   1. Po pomyślnym przekazaniu pliku metadanych wartości **Identyfikator** i **adres URL odpowiedzi** są uzyskiwane automatycznie wypełniane w polu tekstowym **Podstawowa konfiguracja SAML** .
    
      > [!NOTE]
      > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

      W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z firmą [Oracle Cloud Infrastructure Client Console Support Team](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. Aplikacja konsolowa usługi Oracle Cloud Infrastructure oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

   ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, Aplikacja konsolowa infrastruktury w chmurze firmy Oracle oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji **atrybuty użytkownika & oświadczenia** w oknie dialogowym **oświadczenia grupy (wersja zapoznawcza)** wykonaj następujące czynności:

   1. Kliknij **pióro** obok pozycji **Nazwa identyfikator wartość**.

   1. Wybierz pozycję **trwały** jako **Wybierz format identyfikatora nazwy**.
 
   1. Kliknij przycisk **Zapisz**.

      ![image (obraz)](./media/oracle-cloud-tutorial/config07.png)
    
      ![image (obraz)](./media/oracle-cloud-tutorial/config11.png)

   1. Kliknij **pióro** obok **grup zwróconych w ramach żądania**.

   1. Z listy radiowej wybierz pozycję **grupy zabezpieczeń** .

   1. Wybierz **atrybut Source** **identyfikatora grupy**.

   1. Zaznacz pole wyboru **Dostosuj nazwę tego żądania**.

   1. W polu tekstowym **Nazwa** wpisz **GroupName**.

   1. W polu tekstowym **przestrzeń nazw (opcjonalnie)** wpisz polecenie `https://auth.oraclecloud.com/saml/claims` .

   1. Kliknij przycisk **Zapisz**.

      ![image (obraz)](./media/oracle-cloud-tutorial/config08.png)

1. W sekcji **Konfigurowanie konsoli infrastruktury firmy Oracle w chmurze** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do konsoli infrastruktury w chmurze firmy Oracle.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **konsola infrastruktury w chmurze firmy Oracle**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurowanie konsoli infrastruktury w chmurze firmy Oracle

1. W innym oknie przeglądarki sieci Web Zaloguj się do konsoli infrastruktury firmy Oracle jako administrator.

1. Kliknij po lewej stronie menu i kliknij pozycję **tożsamość** , a następnie przejdź do **Federacji**.

   ![Konfiguracja](./media/oracle-cloud-tutorial/config01.png)

1. Zapisz **plik metadanych dostawcy usług** , klikając link **Pobierz ten dokument** i przekaż go do sekcji **Podstawowa konfiguracja SAML** w Azure Portal a następnie kliknij pozycję **Dodaj dostawcę tożsamości**.

   ![Konfiguracja](./media/oracle-cloud-tutorial/config02.png)

1. W oknie podręcznym **Dodawanie dostawcy tożsamości** wykonaj następujące czynności:

   ![Konfiguracja](./media/oracle-cloud-tutorial/config03.png)

   1. W polu tekstowym **Nazwa** wprowadź swoją nazwę.

   1. W polu tekstowym **Opis** wprowadź opis.

   1. Wybierz pozycję **Microsoft Active Directory Federation Service (AD FS) lub dostawcę tożsamości zgodnego z protokołem SAML 2,0** jako **Typ**.

   1. Kliknij przycisk **Przeglądaj** , aby przesłać plik XML metadanych Federacji, który został pobrany z Azure Portal.

   1. Kliknij przycisk **Kontynuuj** i w sekcji **Edytuj dostawcę tożsamości** wykonaj następujące czynności:

      ![Konfiguracja](./media/oracle-cloud-tutorial/config09.png)

   1. **Grupa dostawców tożsamości** powinna być wybrana jako Grupa niestandardowa. Grupa musi być IDENTYFIKATORem GUID grupy z Azure Active Directory. Grupa musi być zamapowana z odpowiednią grupą w polu **Grupa OCI** .

   1. Możesz zmapować wiele grup zgodnie z konfiguracją w Azure Portal, a Twoja organizacja wymaga. Kliknij pozycję **+ Dodaj mapowanie** , aby dodać dowolną liczbę grup.

   1. Kliknij przycisk **Prześlij**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Utwórz użytkownika testowego konsoli infrastruktury w chmurze firmy Oracle

 Konsola infrastruktury w chmurze firmy Oracle obsługuje Inicjowanie obsługi just in Time, co jest domyślnie. W tej sekcji nie musisz niczego robić. Nowy użytkownik nie zostanie utworzony podczas próby uzyskania dostępu, a także nie ma potrzeby tworzenia użytkownika.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka konsola infrastruktury w chmurze Oracle w panelu dostępu nastąpi przekierowanie do strony logowania do konsoli infrastruktury w chmurze firmy Oracle. Wybierz **dostawcę tożsamości** z menu rozwijanego, a następnie kliknij pozycję **Kontynuuj** , jak pokazano poniżej, aby się zalogować. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfiguracja](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Jak chronić konsolę infrastruktury chmury firmy Oracle z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
