---
title: 'Samouczek: Konfigurowanie płatnej śniegu w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i anulowania aprowizacji kont użytkowników.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539539"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie płatnej śniegu na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać w śniegu i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i grup na [płatki śniegu](https://www.Snowflake.com/pricing/). Aby uzyskać ważne informacje na temat tego, jak działa ta usługa, jak to robi, i często zadawanych pytań, zobacz artykuł [co to jest zautomatyzowana obsługa użytkowników aplikacji SaaS w usłudze Azure AD?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać informacje na temat warunków użytkowania, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w śniegu
> * Usuń użytkowników w śniegu, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Płatą śniegu
> * Udostępnianie grup i członkostwa w grupach w śniegu
> * Zezwalaj na [Logowanie jednokrotne](./snowflake-tutorial.md) (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](../develop/quickstart-create-new-tenant.md)
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny)
* [Dzierżawca płatka śniegu](https://www.Snowflake.com/pricing/)
* Konto użytkownika w śniegu z uprawnieniami administratora

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i płatą śniegu](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie obsługi płatnej za pomocą usługi Azure AD

Przed skonfigurowaniem płatnej śniegu w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną międzydomenowego zarządzania tożsamościami (standard scim) na śniegu.

1. Zaloguj się do konsoli administracyjnej płatnej śniegu. Wprowadź następujące zapytanie w wyróżnionym arkuszu, a następnie wybierz pozycję **Uruchom**.

    ![Zrzut ekranu przedstawiający konsolę administracyjną płatka śnieg z przyciskami zapytania i uruchamiania.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Token dostępu Standard scim jest generowany dla dzierżawy płatnej. Aby go pobrać, wybierz link wyróżniony na poniższym zrzucie ekranu.

    ![Zrzut ekranu arkusza w płatnej wysokości U I z tokenem dostępu S C I M o nazwie out.](media/Snowflake-provisioning-tutorial/image01.png)

3. Skopiuj wygenerowaną wartość tokenu i wybierz pozycję **gotowe**. Ta wartość jest wprowadzana w polu **token tajny** na karcie **aprowizacji** aplikacji płatka śniegu w Azure Portal.

    ![Zrzut ekranu przedstawiający sekcję szczegółów, w której jest wyświetlany token skopiowany do pola tekstowego i opcja gotowe wywołana.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie płatnych śniegów z galerii aplikacji usługi Azure AD

Dodaj płatną śnieg z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą na Płatę śniegu. Jeśli wcześniej skonfigurowano płatną śnieg na potrzeby logowania jednokrotnego (SSO), możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas początkowego testowania integracji. [Dowiedz się więcej o dodawaniu aplikacji z galerii](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Definiowanie, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz użyć [kroków, aby przypisać użytkowników i grupy do aplikacji](../manage-apps/assign-user-or-group-access-portal.md). Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz [użyć filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Pamiętaj o następujących wskazówkach:

* Podczas przypisywania użytkowników i grup do śniegu należy wybrać rolę inną niż domyślny dostęp. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w płatach śniegu 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w śniegu. Konfigurację można oprzeć na przypisaniach użytkowników i grup w usłudze Azure AD.

Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla płatnych śniegów w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **płatka śnieg**.

    ![Zrzut ekranu przedstawiający listę aplikacji.](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź odpowiednio, podstawowy adres URL i token uwierzytelniania Standard scim 2,0, który został wcześniej pobrany w polach **adres URL dzierżawy** i **token tajny** . 

   Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z płatą śnieg. Jeśli połączenie zakończy się niepowodzeniem, upewnij się, że Twoje konto płatne w postaci śniegu ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu pokazujący pola dla dzierżawy U R L i klucz tajny, wraz z przyciskiem Testuj połączenie.](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu pokazujący pola dla wiadomości e-mail z powiadomieniem.](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz opcję **Synchronizuj Azure Active Directory użytkownicy z płatą śniegu**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w śniegu w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |emails[type eq "work"].value|Ciąg|
   |userName|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: defaultRole|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: defaultWarehouse|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z płatą**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do śniegu w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w śniegu dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    |Atrybut|Typ|
    |---|---|
    |displayName|Ciąg|
    |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, zapoznaj się z instrukcjami w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla płatnych śniegów, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

     ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i grupy, dla których chcesz udostępnić Płatę śniegu, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** . 

    Jeśli ta opcja jest niedostępna, skonfiguruj wymagane pola w obszarze **poświadczenia administratora**, wybierz pozycję **Zapisz**, a następnie Odśwież stronę. 

     ![Zrzut ekranu pokazujący opcje zakresu aprowizacji.](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

     ![Zrzut ekranu przedstawiający przycisk służący do zapisywania konfiguracji aprowizacji.](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje. Kolejne synchronizacje następują co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

- Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
- Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
- Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. [Dowiedz się więcej na temat stanów kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

Tokeny Standard scim wygenerowane przez płatę śniegu wygasają w ciągu 6 miesięcy. Należy pamiętać, że należy odświeżyć tokeny przed ich wygaśnięciem, aby umożliwić kontynuowanie synchronizacji aprowizacji. 

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Usługa Azure AD Provisioning aktualnie działa pod określonymi [zakresami adresów IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). W razie potrzeby można ograniczyć inne zakresy adresów IP i dodać te określone zakresy adresów IP do listy dozwolonych aplikacji. Ta technika zezwala na przepływ ruchu z usługi Azure AD Provisioning do aplikacji.

## <a name="change-log"></a>Dziennik zmian

* 07/21/2020: włączono funkcję usuwania nietrwałego dla wszystkich użytkowników (za pośrednictwem aktywnego atrybutu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
