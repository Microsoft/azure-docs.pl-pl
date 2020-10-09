---
title: 'Samouczek: Konfigurowanie Foodee w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników w usłudze Foodee.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 78ba57d485f9842ad8531ce22a2b932aa1a1d28b
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850459"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Foodee na potrzeby automatycznego aprowizacji użytkowników

W tym artykule opisano sposób konfigurowania Azure Active Directory (Azure AD) w Foodee i usłudze Azure AD w celu automatycznego aprowizacji lub anulowania aprowizacji użytkowników lub grup w usłudze Foodee.

> [!NOTE]
> W tym artykule opisano łącznik, który jest oparty na usłudze aprowizacji użytkowników usługi Azure AD. Aby dowiedzieć się, co to jest usługa i jak to działa, i aby uzyskać odpowiedzi na często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat funkcji "warunki użytkowania" platformy Azure na potrzeby funkcji wersji zapoznawczej, przejdź do [uzupełniających warunków użytkowania dla Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)wersjach zapoznawczych.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że zostały spełnione następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Foodee](https://www.food.ee/about/)
* Konto użytkownika w Foodee z uprawnieniami administratora

## <a name="assign-users-to-foodee"></a>Przypisywanie użytkowników do Foodee 

Usługa Azure AD korzysta ze koncepcji o nazwie *przypisania* , aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Foodee. Po dokonaniu tego ustalenia można przypisać tych użytkowników lub grupy do Foodee, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Foodee 

Podczas przypisywania użytkowników należy pamiętać o następujących wskazówkach:

* Zalecamy przypisanie tylko jednego użytkownika usługi Azure AD do Foodee w celu przetestowania konfiguracji automatycznego aprowizacji użytkowników. Możesz później przypisywać dodatkowych użytkowników lub grupy.

* W przypadku przypisywania użytkownika do Foodee wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w okienku **przypisanie** . Użytkownicy z domyślną rolą *dostępu* są wykluczeni z aprowizacji.

## <a name="set-up-foodee-for-provisioning"></a>Konfigurowanie Foodee na potrzeby aprowizacji

Przed skonfigurowaniem Foodee na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników przy użyciu usługi Azure AD należy włączyć obsługę międzydomenowego zarządzania tożsamościami (standard scim) w Foodee.

1. Zaloguj się do [Foodee](https://www.food.ee/login/), a następnie wybierz swój identyfikator dzierżawy.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

1. W obszarze **Enterprise Portal**wybierz pozycję **Logowanie**jednokrotne.

    ![Menu z okienka po lewej Enterprise Portal Foodee](media/Foodee-provisioning-tutorial/scim.png)

1. Skopiuj wartość w polu **token interfejsu API** w celu późniejszego użycia. Wprowadzisz ją w polu **token tajny** na karcie **aprowizacji** aplikacji Foodee w Azure Portal.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Dodaj Foodee z galerii

Aby skonfigurować Foodee automatycznej aprowizacji użytkowników przy użyciu usługi Azure AD, musisz dodać Foodee z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

Aby dodać Foodee z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Azure Active Directory polecenie](common/select-azuread.png)

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **Foodee**, w okienku wyników wybierz pozycję **Foodee** , a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![Foodee na liście wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Foodee 

W tej sekcji skonfigurujesz usługę Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w usłudze Foodee na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla Foodee, postępując zgodnie z instrukcjami zawartymi w [samouczku logowanie](Foodee-tutorial.md)jednokrotne w Foodee. Można skonfigurować Logowanie jednokrotne niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

Skonfiguruj automatyczne Inicjowanie obsługi użytkowników dla Foodee w usłudze Azure AD, wykonując następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Okienko aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście **aplikacje** wybierz pozycję **Foodee**.

    ![Link Foodee na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

1. Z listy rozwijanej **tryb aprowizacji** wybierz pozycję **automatycznie**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

1. W obszarze **poświadczenia administratora**wykonaj następujące czynności:

   a. W polu **adres URL dzierżawy** wprowadź wcześniej pobraną wartość **https: \/ /Concierge.food.ee/scim/v2** .

   b. W polu **token Secret** wprowadź wcześniej pobraną wartość **tokenu interfejsu API** .
   
   c. Aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Foodee, wybierz pozycję **Testuj połączenie**. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Foodee ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Link połączenia testowego](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Pole tekstowe powiadomienia E-mail](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W obszarze **mapowania**wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

1. W obszarze **mapowania atrybutów**Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Foodee. Atrybuty, które są wybrane jako **pasujące** właściwości są używane do dopasowywania do *kont użytkowników* w Foodee dla operacji aktualizacji. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

1. Aby zatwierdzić zmiany, wybierz pozycję **Zapisz**.
1. W obszarze **mapowania**wybierz pozycję **Synchronizuj grupy Azure Active Directory do Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

1. W obszarze **mapowania atrybutów**Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Foodee. Atrybuty, które są wybrane jako **pasujące** właściwości są używane do dopasowania do *kont grupy* w Foodee dla operacji aktualizacji.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Zrzut ekranu przedstawiający menu główne witryny Foodee Enterprise Portal. Symbol zastępczy identyfikatora dzierżawy jest widoczny w menu." border="false":::

1. Aby zatwierdzić zmiany, wybierz pozycję **Zapisz**.
1. Skonfiguruj filtry zakresu. Aby dowiedzieć się, jak to zrobić, zapoznaj się z instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning for Foodee, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Przełącznik stanu aprowizacji](common/provisioning-toggle-on.png)

1. W obszarze **Ustawienia**, na liście rozwijanej **zakres** Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Foodee.

    ![Lista rozwijana zakresu aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Przycisk zapisywania konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Poprzednia operacja uruchamia początkową synchronizację użytkowników lub grup, które zostały zdefiniowane na liście rozwijanej **zakres** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Aby uzyskać więcej informacji, zobacz [jak długo trwa inicjowanie obsługi administracyjnej użytkowników?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Możesz użyć bieżącej sekcji **stanu** , aby monitorować postęp i postępować zgodnie z raportem dotyczącym aktywności aprowizacji. Raport opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w systemie Foodee. Aby uzyskać więcej informacji, zobacz [Sprawdzanie stanu aprowizacji użytkowników](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Aby zapoznać się z dziennikami aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
