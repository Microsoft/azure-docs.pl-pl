---
title: 'Samouczek: Konfigurowanie płatnej śniegu w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063164"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie płatnej śniegu na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie kroków, które należy wykonać w śniegu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup na płatki śniegu.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawca płatka śniegu](https://www.Snowflake.com/pricing/).
* Konto użytkownika w śniegu z uprawnieniami administratora.

## <a name="assigning-users-to-snowflake"></a>Przypisywanie użytkowników do płatka śniegu

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do płatnego śniegu. Po ustaleniu tych użytkowników i/lub grup można przypisywać do śniegu, postępując zgodnie z poniższymi instrukcjami:
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Ważne porady dotyczące przypisywania użytkowników do śniegu

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do płatnej śniegu w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do płaty śniegu należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-snowflake-for-provisioning"></a>Konfigurowanie zainicjowania obsługi płatnej śniegu

Przed skonfigurowaniem płatnej śniegu w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD należy włączyć funkcję aprowizacji Standard scim na śniegu.

1. Zaloguj się do konsoli administracyjnej płatnej śniegu. Wprowadź poniżej zapytanie w wyróżnionym arkuszu, a następnie kliknij przycisk **Uruchom**.

    ![Konsola administracyjna płatka śniegu](media/Snowflake-provisioning-tutorial/image00.png)

2.  Token dostępu Standard scim zostanie wygenerowany dla dzierżawy płatnej. Aby go pobrać, kliknij link wyróżniony poniżej.

    ![Dodaj Standard SCIMa śniegu](media/Snowflake-provisioning-tutorial/image01.png)

3. Skopiuj wygenerowaną wartość tokenu i kliknij przycisk **gotowe**. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji płatnej śniegu w Azure Portal.

    ![Dodaj Standard SCIMa śniegu](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Dodaj płatną śnieg z galerii

Aby skonfigurować płatną śnieg w celu automatycznego aprowizacji użytkowników w usłudze Azure AD, musisz odliczać Płatkę z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać płatną śnieg z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **płatka śniegu**, wybierz pozycję **płatka** śniegu w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Aplikacja Snowflake na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Konfigurowanie automatycznego aprowizacji użytkowników na płatki śniegu 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w śniegu na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML w przypadku płatka za pośrednictwem języka, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](Snowflake-tutorial.md)jednokrotne. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla płatnych śniegów w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **płatka śnieg**.

    ![Link aplikacji Snowflake na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji poświadczenia administratora wprowadź `https://<Snowflake Account URL>/scim/v2` adres URL dzierżawy. Przykład adresu URL dzierżawy:`https://acme.snowflakecomputing.com/scim/v2`

6. Wprowadź wartość **tokenu uwierzytelniania Standard scim** pobraną wcześniej w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z płatą Jeśli połączenie zakończy się niepowodzeniem, upewnij się, że Twoje konto płatne w sieci śnieg ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij przycisk **Zapisz**.

9. W sekcji **mapowania** wybierz opcję **Synchronizuj Azure Active Directory użytkownicy z płatą śniegu**.

    ![Mapowania użytkowników płatka śniegu](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w śniegu w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika płatka śniegu](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z płatą śniegu**.

    ![Mapowania grup płatnych śniegu](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w śniegu dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy płatka śniegu](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę Azure AD Provisioning dla płatka śnieg, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić śnieg, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** . Jeśli ta opcja jest niedostępna, skonfiguruj wymagane pola w obszarze poświadczenia administratora, a następnie kliknij przycisk **Zapisz** i Odśwież stronę. 

    ![Zakres aprowizacji](common/provisioning-scope.png)

16. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

    Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem o aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na śniegu.

    Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Tokeny Standard scim wygenerowały płaty śniegu wygaśnie w ciągu 6 miesięcy. Należy pamiętać, że należy je odświeżyć przed wygaśnięciem, aby umożliwić kontynuowanie synchronizacji aprowizacji. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).
