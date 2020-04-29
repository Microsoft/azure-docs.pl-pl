---
title: 'Samouczek: Konfigurowanie TheOrgWiki dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063151"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie TheOrgWiki na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w TheOrgWiki i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do TheOrgWiki.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa OrgWiki](https://www.theorgwiki.com/welcome/).
* Konto użytkownika w TheOrgWiki z uprawnieniami administratora.

## <a name="assign-users-to-theorgwiki"></a>Przypisywanie użytkowników do TheOrgWiki

Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do TheOrgWiki. Po ustaleniu tych użytkowników i/lub grup można przypisywać do TheOrgWiki, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Ważne wskazówki dotyczące przypisywania użytkowników do TheOrgWiki

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do TheOrgWiki w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do TheOrgWiki należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-theorgwiki-for-provisioning"></a>Konfigurowanie TheOrgWiki na potrzeby aprowizacji

Przed skonfigurowaniem usługi TheOrgWiki na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na TheOrgWiki.

1. Zaloguj się do [konsoli administracyjnej TheOrgWiki](https://www.theorgwiki.com/login/). Kliknij pozycję **Konsola administracyjna**.

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/login.png)

2. W konsoli administracyjnej programu kliknij **kartę Ustawienia**. 

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Przejdź do **konta usługi**.

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Kliknij pozycję **+ konto usługi**. W obszarze **Typ konta usługi**wybierz pozycję **oparty na tokenach**. Kliknij przycisk **Zapisz**.

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Skopiuj **aktywne tokeny**. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji TheOrgWiki w Azure Portal.
     
    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Dodaj TheOrgWiki z galerii

Aby skonfigurować TheOrgWiki automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać TheOrgWiki z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TheOrgWiki**, a następnie wybierz pozycję **TheOrgWiki** w panelu wyniki. 

    ![TheOrgWiki na liście wyników](common/search-new-app.png)

5. Wybierz przycisk **Utwórz konto w usłudze TheOrgWiki** , który przekieruje Cię do strony logowania usługi TheOrgWiki. 

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/image00.png)

6.  W prawym górnym rogu wybierz pozycję **Zaloguj**.

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/image02.png)

7. Ponieważ TheOrgWiki jest aplikacją OpenIDConnect, wybierz logowanie do OrgWiki przy użyciu konta służbowego firmy Microsoft.

    ![TheOrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Po pomyślnym uwierzytelnieniu aplikacja zostanie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta TheOrgWiki.

    ![OrgWiki Dodaj Standard scim](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurowanie automatycznej aprowizacji użytkowników do TheOrgWiki 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TheOrgWiki na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla TheOrgWiki w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **TheOrgWiki**.

    ![Link OrgWiki na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **adres URL dzierżawy**. 

    Przykład: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Wartość domeny** podrzędnej można ustawić tylko podczas początkowego procesu tworzenia konta dla TheOrgWiki.
 
6. Wprowadź wartość tokenu w polu **token tajny** , który został pobrany wcześniej z TheOrgWiki. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą TheOrgWiki. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi TheOrgWiki ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do TheOrgWiki**.

    ![TheOrgWiki mapowania użytkowników](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do TheOrgWiki w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie TheOrgWiki for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![TheOrgWiki atrybuty użytkownika](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę Azure AD Provisioning dla TheOrgWiki, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

13. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić OrgWiki, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

14. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji o tym, jak długo będzie trwać dla użytkowników i/lub grup, zobacz [jak długo trwa inicjowanie obsługi użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie TheOrgWiki. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).