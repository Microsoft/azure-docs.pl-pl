---
title: 'Samouczek: Inicjowanie obsługi użytkowników — Nawigator sprzedaży w serwisie LinkedIn, usługa Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w usłudze LinkedIn Sales Navigator.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 458b527194c1123e266bd6abedf25de18e0cee09
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359361"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie nawigatora sprzedaży serwisu LinkedIn na potrzeby automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Nawigatorze sprzedaży w serwisie LinkedIn i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w usłudze LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa w usłudze LinkedIn Sales Navigator 
* Konto administratora w programie LinkedIn Sales Navigator z dostępem do centrum konta LinkedIn

> [!NOTE]
> Azure Active Directory integruje się z nawigatorem sprzedaży w serwisie LinkedIn przy użyciu protokołu [Standard scim](http://www.simplecloud.info/) .

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Przypisywanie użytkowników do nawigatora sprzedaży w serwisie LinkedIn

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi konta użytkownika zostaną zsynchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do nawigatora sprzedaży w serwisie LinkedIn. Po ustaleniu tych użytkowników możesz przypisać je do nawigatora sprzedaży w serwisie LinkedIn, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Ważne porady dotyczące przypisywania użytkowników do nawigatora sprzedaży w serwisie LinkedIn

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do nawigatora sprzedaży w serwisie LinkedIn w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do nawigatora sprzedaży w serwisie LinkedIn należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurowanie aprowizacji użytkowników w usłudze LinkedIn Sales Navigator

Ta sekcja przeprowadzi Cię przez proces nawiązywania połączenia z INTERFEJSem użytkownika usługi Azure AD w usłudze LinkedIn Sales nawigatora kont użytkowników Standard scim i konfigurowania usługi aprowizacji w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w Nawigatorze sprzedaży w serwisie LinkedIn na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć opcję Single Sign-On opartą na protokole SAML dla nawigatora sprzedaży w serwisie LinkedIn, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi kont użytkowników w usłudze Azure AD w usłudze w serwisie LinkedIn:

Pierwszym krokiem jest pobranie tokenu dostępu do serwisu LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa, możesz automatycznie zainicjować obsługę administracyjną tokenu dostępu. W centrum konta przejdź do pozycji **Ustawienia ustawienia &gt; globalne** i Otwórz panel **ustawień Standard scim** .

> [!NOTE]
> Jeśli uzyskujesz dostęp do centrum kont bezpośrednio, a nie za pośrednictwem linku, możesz uzyskać do niego dostęp, wykonując poniższe kroki.

1. Zaloguj się do centrum konta.

2. Wybierz **pozycję &gt; Ustawienia administratora administratora** .

3. Kliknij przycisk **integracji zaawansowane** na lewym pasku bocznym. Nastąpi przekierowanie do centrum konta.

4. Kliknij pozycję **+ Dodaj nową konfigurację Standard scim** i postępuj zgodnie z procedurą, wypełniając każde pole.

    > [!NOTE]
    > Gdy nie włączono licencji autoassign, oznacza to, że synchronizowane są tylko dane użytkownika.

    ![Zrzut ekranu przedstawia ustawienia globalne centrum konta LinkedIn.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Gdy jest włączone przypisanie autolicencji, należy zanotować wystąpienie aplikacji i typ licencji. Licencje są przypisywane w pierwszej kolejności, w pierwszej kolejności do momentu, aż zostaną wykonane wszystkie licencje.

    ![Zrzut ekranu przedstawia stronę instalatora S C I M.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Kliknij przycisk **Generuj token**. Token dostępu powinien zostać wyświetlony w polu **token dostępu** .

6. Zapisz token dostępu do Schowka lub komputera przed opuszczeniem strony.

7. Następnie zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**  .

8. Jeśli już skonfigurowano nawigatora sprzedaży w serwisie LinkedIn na potrzeby logowania jednokrotnego, Wyszukaj wystąpienie usługi LinkedIn Sales Navigator przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Nawigator sprzedaży w serwisie LinkedIn** w galerii aplikacji. Wybierz opcję Nawigator sprzedaży w serwisie LinkedIn z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

9. Wybierz wystąpienie z nawigatora sprzedaży w serwisie LinkedIn, a następnie wybierz kartę **aprowizacji** .

10. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawia stronę podwyższanie poziomu udostępniania w serwisie LinkedIn.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Wypełnij następujące pola w obszarze **poświadczenia administratora** :

    * W polu **adres URL dzierżawy** wprowadź wartość https://developer.linkedin.com .

    * W polu **token Secret** wprowadź token dostępu wygenerowany w kroku 1, a następnie kliknij pozycję **Testuj połączenie** .

    * Na stronie upperright portalu powinna zostać wyświetlona powiadomienie o powodzeniu.

12. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , a następnie zaznacz pole wyboru poniżej.

13. Kliknij przycisk **Zapisz**.

14. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkowników i grup, które zostaną zsynchronizowane z usługi Azure AD w usłudze LinkedIn Sales Navigator. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane w celu dopasowania do kont użytkowników i grup w Nawigatorze sprzedaży LinkedIn dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawia mapowania, w tym mapowania atrybutów.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Aby włączyć usługę Azure AD Provisioning dla nawigatora sprzedaży w serwisie LinkedIn, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

16. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do nawigatora sprzedaży serwisu LinkedIn w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa zajmie więcej czasu niż kolejne synchronizacje, co będzie odbywać się około co 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji w Nawigatorze sprzedaży w serwisie LinkedIn.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
