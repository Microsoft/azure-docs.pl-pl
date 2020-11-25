---
title: 'Samouczek: Konfigurowanie Elium dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005507"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Elium na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono sposób konfigurowania usług Elium i Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i cofania aprowizacji użytkowników lub grup do Elium.

> [!NOTE]
> Ten samouczek zawiera opis łącznika, który jest zbudowany na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania, a także często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Ogólne warunki użytkowania funkcji systemu Azure w wersji zapoznawczej znajdują się w temacie [uzupełnienie warunków użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)wersji zapoznawczych Microsoft Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Elium](https://www.elium.com/pricing/)
* Konto użytkownika w Elium z uprawnieniami administratora

## <a name="assigning-users-to-elium"></a>Przypisywanie użytkowników do Elium

Usługa Azure AD korzysta ze koncepcji o nazwie *przypisania* , aby określić, którzy użytkownicy otrzymują dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i grupy w usłudze Azure AD potrzebują dostępu do Elium. Następnie przypisz tych użytkowników i grupy do Elium, wykonując czynności opisane w sekcji [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Elium 

Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do Elium w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkownika. Więcej użytkowników i grup można przypisać później.

Podczas przypisywania użytkownika do Elium należy wybrać prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą **dostępu** są wykluczeni z aprowizacji.

## <a name="set-up-elium-for-provisioning"></a>Konfigurowanie Elium na potrzeby aprowizacji

Przed skonfigurowaniem usługi Elium na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć opcję System na potrzeby aprowizacji międzydomenowego zarządzania tożsamościami (standard scim) na Elium. Wykonaj następujące kroki:

1. Zaloguj się do Elium i przejdź do pozycji **Moje**  >  **Ustawienia** profilu.

    ![Element menu Ustawienia w Elium](media/Elium-provisioning-tutorial/setting.png)

1. W lewym dolnym rogu w obszarze **Zaawansowane** wybierz pozycję **zabezpieczenia**.

    ![Link zabezpieczeń w Elium](media/Elium-provisioning-tutorial/security.png)

1. Skopiuj **adres URL dzierżawy** i wartości **tokenów tajnych** . Te wartości będą używane później, w odpowiednich polach na karcie **aprowizacji** aplikacji Elium w Azure Portal.

    ![Pola adresu URL dzierżawy i klucza tajnego w Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Dodaj Elium z galerii

Aby skonfigurować Elium automatycznej aprowizacji użytkowników w usłudze Azure AD, należy również dodać Elium z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji typu "oprogramowanie jako usługa" (SaaS). Wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Element menu Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

     ![Blok aplikacji Azure AD Enterprise](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okienka.

    ![Link nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wpisz **Elium**, na liście wyników wybierz pozycję **Elium** , a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![Pole wyszukiwania galerii](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Elium

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w programie Elium na podstawie przypisań użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego dla Elium na podstawie SAML (SAML), postępując zgodnie z instrukcjami zawartymi w [samouczku logowanie](Elium-tutorial.md)jednokrotne w Elium. Można skonfigurować Logowanie jednokrotne niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Elium w usłudze Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok aplikacji Azure AD Enterprise](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **Elium**.

    ![Lista aplikacji w bloku aplikacje dla przedsiębiorstw](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Karta aprowizacji w bloku aplikacje dla przedsiębiorstw](common/provisioning.png)

1. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Ustawienie automatyczne dla trybu aprowizacji](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wpisz **\<tenantURL\> /scim/v2** w polu **adres URL dzierżawy** . ( **TenantURL** jest wartością pobieraną wcześniej z konsoli administracyjnej Elium). W polu **token tajny** wpisz również wartość **token Elium Secret** . Na koniec wybierz pozycję **Testuj połączenie** , aby sprawdzić, czy usługa Azure AD może nawiązać połączenie z usługą Elium. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Elium ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Pola adresu URL dzierżawy i klucza tajnego w poświadczeniach administratora](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, która będzie otrzymywać powiadomienia o błędach aprowizacji. Następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

1. Kliknij pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Elium**.

    ![Synchronizuj link do mapowania użytkowników usługi Azure AD do Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Elium w sekcji **mapowania atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Elium for Updates. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Mapowania atrybutów między usługą Azure AD i Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla Elium, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest ustawiony na wartość włączone](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników i grupy, które chcesz udostępnić Elium, wybierając odpowiednie wartości w polu listy rozwijanej **zakres** w sekcji **Ustawienia** .

    ![Pole listy zakresu aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz dla konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Ten proces synchronizacji początkowej trwa dłużej niż później. Aby uzyskać więcej informacji na temat czasu wymaganego do aprowizacji, zobacz [jak długo trwa inicjowanie obsługi administracyjnej użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Użyj **bieżącej sekcji stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji. Raport dotyczący działań aprowizacji opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie Elium. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
