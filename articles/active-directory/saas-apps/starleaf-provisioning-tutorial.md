---
title: 'Samouczek: Konfigurowanie StarLeaf dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze StarLeaf.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064295"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie StarLeaf na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w StarLeaf i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do StarLeaf.

> [!NOTE]
>  Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa StarLeaf](https://www.starleaf.com/solutions/).
* Konto użytkownika w StarLeaf z uprawnieniami administratora.

## <a name="assign-users-to-starleaf"></a>Przypisywanie użytkowników do StarLeaf
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i grupy w usłudze Azure AD potrzebują dostępu do StarLeaf. Następnie można przypisać użytkowników i grupy do StarLeaf, wykonując [te instrukcje](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Ważne wskazówki dotyczące przypisywania użytkowników do StarLeaf

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do StarLeaf w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i grup można przypisywać później.

* Po przypisaniu użytkownika do StarLeaf należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.

## <a name="set-up-starleaf-for-provisioning"></a>Konfigurowanie StarLeaf na potrzeby aprowizacji

Przed skonfigurowaniem usługi StarLeaf do automatycznego aprowizacji użytkowników w usłudze Azure AD należy skonfigurować Inicjowanie obsługi administracyjnej Standard scim w usłudze StarLeaf:

1. Zaloguj się do [konsoli administracyjnej StarLeaf](https://portal.starleaf.com/#page=login). Przejdź do obszarze **integracji** > **Dodaj integrację**.

    ![StarLeaf Dodaj Standard scim](media/starleaf-provisioning-tutorial/image00.png)

2. Wybierz **Typ** , który ma zostać Microsoft Azure Active Directory. Wprowadź odpowiednią nazwę w polu **Nazwa**. Kliknij przycisk **Zastosuj**.

    ![StarLeaf Dodaj Standard scim](media/starleaf-provisioning-tutorial/image01.png)

3.  Zostanie wyświetlona wartość **podstawowego adresu URL** i **tokenu dostępu** Standard scim. Te wartości zostaną wprowadzone w polach **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji StarLeaf w Azure Portal. 

    ![Utwórz token StarLeaf](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Dodaj StarLeaf z galerii

Aby skonfigurować StarLeaf do automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz dodać StarLeaf z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać StarLeaf z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **StarLeaf**, a następnie wybierz pozycję **StarLeaf** w panelu wyniki.
    ![StarLeaf na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurowanie automatycznej aprowizacji użytkowników do StarLeaf

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie StarLeaf na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **StarLeaf**.

    ![Link StarLeaf na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź odpowiednie **wartości w polach adres URL** i **token dostępu** **Standard scim Base** . **Secret Token** Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą StarLeaf. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi StarLeaf ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole **Wyślij powiadomienie e-mail, gdy wystąpi awaria** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do StarLeaf**.

    ![Utwórz token StarLeaf](media/starleaf-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do StarLeaf w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie StarLeaf for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Utwórz token StarLeaf](media/starleaf-provisioning-tutorial/userattribute.png)


10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Aby włączyć usługę Azure AD Provisioning dla StarLeaf, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić StarLeaf, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie StarLeaf.

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia łącznika

* StarLeaf nie obsługuje obecnie aprowizacji grup. 
* StarLeaf wymaga wartości **adresu e-mail** i **nazwy użytkownika** , aby mieć taką samą wartość źródłową.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).
