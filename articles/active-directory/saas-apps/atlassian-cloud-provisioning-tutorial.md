---
title: 'Samouczek: Konfigurowanie chmury Atlassian na potrzeby automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w chmurze Atlassian.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181071"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie chmury Atlassian na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w chmurze Atlassian i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w [chmurze Atlassian](https://www.atlassian.com/licensing/cloud). Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w chmurze Atlassian
> * Usuń użytkowników w chmurze Atlassian, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i chmurą Atlassian
> * Udostępnianie grup i członkostw w grupach w chmurze Atlassian
> * [Logowanie](./atlassian-cloud-tutorial.md) jednokrotne do chmury Atlassian (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego).
* [Dzierżawa w chmurze Atlassian](https://www.atlassian.com/licensing/cloud)
* Konto użytkownika w chmurze Atlassian z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i chmurą Atlassian](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie chmury Atlassian w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Przejdź do programu [Atlassian Organization Manager](https://admin.atlassian.com) **,> wybierz katalog > organizacji**.

    ![Zrzut ekranu przedstawiający stronę Administracja z opcją katalogową o nazwie out.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Kliknij pozycję **Inicjowanie obsługi użytkowników** i kliknij pozycję **Utwórz katalog**. Skopiuj **podstawowy adres URL katalogu** i **token okaziciela** , który zostanie wprowadzony w polach **adres URL dzierżawy** i **klucz tajny tokenu** na karcie aprowizacji aplikacji w chmurze Atlassian w portalu usługi Azure AD.

    ![Zrzut ekranu strony Administracja z opcją aprowizacji użytkowników o nazwie out. ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Zrzut ekranu przedstawiający stronę Tworzenie tokenu.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Zrzut ekranu przedstawiający stronę tokenu katalogu czasu pokazu.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie chmury Atlassian z galerii aplikacji usługi Azure AD

Dodaj chmurę Atlassian z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą do chmury Atlassian. Jeśli wcześniej skonfigurowano chmurę Atlassian dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do chmury Atlassian należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w chmurze Atlassian 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w chmurze Atlassian na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w chmurze Atlassian w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **chmura Atlassian**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Atlassian Cloud**.

    ![Link do usługi Atlassian Cloud na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **token tajny** pobrany wcześniej z konta w chmurze Atlassian. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z chmurą Atlassian. Jeśli połączenie nie powiedzie się, upewnij się, że konto w chmurze Atlassian ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy z chmurą Atlassian**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Atlassian Cloud w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w chmurze Atlassian dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |name.familyName|Ciąg|
   |name.givenName|Ciąg|
   |emails[type eq "work"].value|Ciąg|   

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z chmurą Atlassian**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Atlassian Cloud w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w chmurze Atlassian dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |externalId|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla chmury Atlassian, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić w chmurze Atlassian, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

16. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Chmura Atlassian umożliwia inicjowanie obsługi użytkowników tylko z [zweryfikowanych domen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Chmura Atlassian nie obsługuje już dzisiaj nazw grup. Oznacza to, że wszelkie zmiany wartości displayName grupy w usłudze Azure AD nie będą aktualizowane i odzwierciedlane w chmurze Atlassian.
* Wartość atrybutu użytkownika **poczty** w usłudze Azure AD jest wypełniana tylko wtedy, gdy użytkownik ma skrzynkę pocztową programu Microsoft Exchange. Jeśli użytkownik nie ma takiego elementu, zaleca się zamapowanie innego żądanego atrybutu na atrybut **wiadomości e-mail** w chmurze Atlassian.

## <a name="change-log"></a>Dziennik zmian

* 06/15/2020 — dodano obsługę poprawek wsadowych dla grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png