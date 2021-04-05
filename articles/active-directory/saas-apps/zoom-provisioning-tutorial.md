---
title: 'Samouczek: Konfigurowanie powiększenia automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w celu powiększania.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: c87c3692d7e1106e0952b85791da663e03c6001d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182482"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie powiększenia automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w celu skonfigurowania automatycznego aprowizacji użytkowników w ramach powiększenia i Azure Active Directory (Azure AD). Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup w celu [powiększania](https://zoom.us/pricing/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w powiększeniu
> * Usuń użytkowników w powiększeniu, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i powiększaniem
> * [Logowanie](./zoom-tutorial.md) jednokrotne w celu powiększania (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](../roles/permissions-reference.md) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* [Dzierżawa powiększenia](https://zoom.us/pricing).
* Konto użytkownika w powiększeniu z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD a powiększeniem](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie powiększenia w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do [konsoli administracyjnej powiększenia](https://zoom.us/signin). Przejdź do **witryny Advanced > App Marketplace** w okienku nawigacji po lewej stronie.

    ![Powiększ integracje](media/zoom-provisioning-tutorial/zoom01.png)

2. Przejdź do obszaru **Zarządzanie** w prawym górnym rogu strony. 

    ![Zrzut ekranu przedstawiający witrynę Marketplace powiększenia aplikacji z opcją zarządzania o nazwie out.](media/zoom-provisioning-tutorial/zoom02.png)

3. Przejdź do utworzonej aplikacji usługi Azure AD. 
    
    ![Zrzut ekranu przedstawiający sekcję utworzone aplikacje z aplikacją Azure A D o nazwie out.](media/zoom-provisioning-tutorial/zoom03.png)

4. W okienku nawigacji po lewej stronie wybierz pozycję **poświadczenia aplikacji** .

    ![Zrzut ekranu okienka nawigacji po lewej stronie z wyróżnioną opcją poświadczenia aplikacji.](media/zoom-provisioning-tutorial/zoom04.png)

5. Skopiuj i Zapisz **token JWT**. Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji do powiększania w Azure Portal. Jeśli potrzebujesz nowego tokenu bez wygaśnięcia, musisz zmienić konfigurację czasu wygaśnięcia, który będzie automatycznie generować nowy token. 

    ![Zrzut ekranu przedstawiający stronę poświadczeń aplikacji.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie powiększenia z galerii aplikacji usługi Azure AD

Dodaj powiększenie z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą w celu powiększania. Jeśli wcześniej skonfigurowano powiększenie logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Podczas przypisywania użytkowników i grup do powiększania należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Krok 5. Konfigurowanie automatycznego aprowizacji użytkowników w celu powiększania 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników w celu powiększania w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **powiększenie**.

    ![Link aplikacji Zoom na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość `https://api.zoom.us/scim` w polu **adres URL dzierżawy**. Wprowadź wartość **tokenu JWT** pobranego wcześniej w **tokenie tajnym**. Kliknij przycisk **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto powiększenia ma uprawnienia administratora i spróbuj ponownie.

    ![Powiększ powiększanie](./media/zoom-provisioning-tutorial/provisioning.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby powiększyć**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby powiększyć sekcję **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w powiększeniu dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API powiększenia obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |wiadomości e-mail [typ EQ "Work"]|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć powiększanie usługi Azure AD Provisioning, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz powiększyć, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Ograniczenia łącznika
* Wartość zoom umożliwia tylko 9 999 użytkowników podstawowych.

## <a name="change-log"></a>Dziennik zmian
* 05/14/2020 — obsługa operacji aktualizacji dodanych dla atrybutu wiadomości e-mail [Type EQ "Work"].
* 10/20/2020 — dodano obsługę dwóch nowych ról "licencjonowane" i "on-Premium", aby zastąpić istniejące role "Pro" i "Corp". Obsługa ról "Pro" i "Corp" zostanie usunięta w przyszłości.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)