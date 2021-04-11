---
title: 'Samouczek: Konfigurowanie TravelPerk dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z usługi Azure AD do TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950384"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie TravelPerk na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie TravelPerk i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i unieważnia użytkowników i grupy do [TravelPerk](https://www.travelperk.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
>
> - Tworzenie użytkowników w TravelPerk
> - Usuń użytkowników w TravelPerk, gdy nie wymagają już dostępu
> - Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i TravelPerk
> - [Logowanie](./travelperk-tutorial.md) jednokrotne do TravelPerk (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

- [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
- Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
- Konto administratora aktywnego [TravelPerk](https://app.travelperk.com/signup) .
- [Plan](https://www.travelperk.com/pricing/)Premium/Pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ, które dane mają być [mapowane między usługą Azure AD i TravelPerk](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie TravelPerk w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do aplikacji [TravelPerk](https://app.travelperk.com/company/integrations/scim) przy użyciu konta administratora.

2. Przejdź do **ustawień firmy**  >  **Integrations**  >  **Standard scim**

3. Kliknij pozycję **Włącz interfejs API Standard scim**

   ![Włącz](./media/travelperk-provisioning-tutorial/configuration.png)

4. Możesz również włączyć zatwierdzenia przez Standard scim. Zatwierdzenia ułatwiają ustawienie dodatkowego ładu przez zagwarantowanie, że podróże zostaną zatwierdzone najpierw przez określone osoby zatwierdzające. Więcej informacji na ten temat można znaleźć [tutaj](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Możesz określić, czy chcesz, aby Menedżer każdej osoby automatycznie stał się użytkownikiem odpowiedzialnym za zatwierdzenie podróży. W związku z tym zatwierdzenie zostanie przypisane w odpowiednim procesie automatycznego zatwierdzania. TravelPerk zamapuje wartość **Menedżera** platformy Azure na pożądaną osobę zatwierdzającą użytkownika. Użytkownik musi znajdować się na platformie przed przystąpieniem do osoby zatwierdzającej użytkownika.
Osoby zatwierdzające nie zostaną utworzone, jeśli nie zostały prawidłowo skonfigurowane na TravelPerk.

6. Tworzenie procesu automatycznego zatwierdzania jest dostępne w **ustawieniach Standard scim** po włączeniu Standard scim na stronie integracji. Aby ją włączyć, wybierz **dostawcę tożsamości** i Przełącz przełącznik, aby **włączyć automatyczne tworzenie procesów zatwierdzania**.

7. Kliknij pozycję **Zapisz zmiany** po skonfigurowaniu wymaganego procesu zatwierdzania.

   ![Automatyzacja](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie TravelPerk z galerii aplikacji usługi Azure AD

Dodaj TravelPerk z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do TravelPerk. Jeśli wcześniej skonfigurowano TravelPerk na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- Podczas przypisywania użytkowników do TravelPerk należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról.

- Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do TravelPerk

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla TravelPerk w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

   ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **TravelPerk**.

   ![Link TravelPerk na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

   ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

   ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij pozycję **Autoryzuj**. Nastąpi przekierowanie do strony logowania **TravelPerk**. Wprowadź **nazwę użytkownika** i **hasło** , a następnie kliknij przycisk **Zaloguj się** . Kliknij pozycję **Autoryzuj aplikację** na stronie Autoryzacja. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą TravelPerk. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi SecureLogin ma uprawnienia administratora, a następnie spróbuj ponownie.

   ![Poświadczenia administratora](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Powitanie](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

   ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do TravelPerk**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do TravelPerk w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie TravelPerk for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API TravelPerk obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   | Atrybut                                                                         | Typ      | Obsługiwane na potrzeby filtrowania |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | Ciąg    | &check;                 |
   | externalId                                                                        | Ciąg    |
   | aktywne                                                                            | Wartość logiczna   |
   | name.honorificPrefix                                                              | Ciąg    |
   | name.familyName                                                                   | Ciąg    |
   | name.givenName                                                                    | Ciąg    |
   | Name. middleName                                                                   | Ciąg    |
   | preferredLanguage                                                                 | Ciąg    |
   | locale                                                                            | Ciąg    |
   | phoneNumbers[type eq "work"].value                                                | Ciąg    |
   | externalId                                                                        | Ciąg    |
   | title                                                                             | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: costCenter             | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager                | Odwołanie |
   | urn: IETF: params: Standard scim: schematy: Extension: travelperk: 2.0: User: płeć                 | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: Extension: travelperk: 2.0: User: dateOfBirth            | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: Extension: travelperk: 2.0: User: invoiceProfiles        | Tablica     |
   | urn:IETF:params:SCIM:schemas:Extension:travelperk: 2.0:User:emergencyContact. Name  | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: Extension: travelperk: 2.0: User: emergencyContact. Phone | Ciąg    |
   | urn: IETF: params: Standard scim: schematy: Extension: travelperk: 2.0: User: travelPolicy           | Ciąg    |

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla TravelPerk, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić TravelPerk, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)