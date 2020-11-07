---
title: 'Samouczek: obsługa administracyjna użytkowników w przypadku podniesienia uprawnień do usługi Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w celu podniesienia uprawnień do serwisu LinkedIn.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358953"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie podniesienia uprawnień w serwisie LinkedIn na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach podniesienia uprawnień i usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w celu podniesienia uprawnień do serwisu LinkedIn.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Podniesienie poziomu dzierżawy w serwisie LinkedIn
* Konto administratora w serwisie LinkedIn podnosi poziom dostępu do centrum konta LinkedIn

> [!NOTE]
> Azure Active Directory integruje się z usługą LinkedIn przy użyciu protokołu [Standard scim](http://www.simplecloud.info/) .

## <a name="assigning-users-to-linkedin-elevate"></a>Przypisywanie użytkowników do podniesienia uprawnień do serwisu LinkedIn

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi konta użytkownika zostaną zsynchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do podniesienia uprawnień do serwisu LinkedIn. Po ustaleniu decyzji można przypisać tych użytkowników do podniesienia uprawnień do serwisu LinkedIn, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Ważne porady dotyczące przypisywania użytkowników do podniesienia uprawnień do serwisu LinkedIn

* Zaleca się, aby jeden użytkownik usługi Azure AD mógł zostać przypisany do serwisu LinkedIn w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do podniesienia uprawnień do serwisu LinkedIn należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurowanie aprowizacji użytkowników w celu podniesienia uprawnień do serwisu LinkedIn

Ta sekcja przeprowadzi Cię przez proces nawiązywania połączenia z usługą Azure AD do serwisu LinkedIn podniesienia poziomu interfejsu użytkownika Standard scim użytkowników i konfigurowania usługi aprowizacji w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w serwisie LinkedIn podniesienia uprawnień na podstawie przypisania użytkowników i grup w usłudze Azure AD.

**Porada:** Możesz również włączyć obsługę protokołu SAML Sign-On single dla podniesienia uprawnień w serwisie LinkedIn, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Aby skonfigurować funkcję automatycznego inicjowania obsługi konta użytkownika w usłudze Azure AD:

Pierwszym krokiem jest pobranie tokenu dostępu do serwisu LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa, możesz automatycznie zainicjować obsługę administracyjną tokenu dostępu. W centrum konta przejdź do pozycji **Ustawienia ustawienia &gt; globalne** i Otwórz panel **ustawień Standard scim** .

> [!NOTE]
> Jeśli uzyskujesz dostęp do centrum kont bezpośrednio, a nie za pośrednictwem linku, możesz uzyskać do niego dostęp, wykonując poniższe kroki.

1. Zaloguj się do centrum konta.

2. Wybierz **pozycję &gt; Ustawienia administratora administratora** .

3. Kliknij przycisk **integracji zaawansowane** na lewym pasku bocznym. Nastąpi przekierowanie do centrum konta.

4. Kliknij pozycję **+ Dodaj nową konfigurację Standard scim** i postępuj zgodnie z procedurą, wypełniając każde pole.

    > [!NOTE]
    > Gdy nie włączono licencji autoassign, oznacza to, że synchronizowane są tylko dane użytkownika.

    ![Zrzut ekranu przedstawia ustawienia globalne centrum konta LinkedIn.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Gdy jest włączone przypisanie autolicencji, należy zanotować wystąpienie aplikacji i typ licencji. Licencje są przypisywane w pierwszej kolejności, w pierwszej kolejności do momentu, aż zostaną wykonane wszystkie licencje.

    ![Zrzut ekranu przedstawia stronę instalatora S C I M.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Kliknij przycisk **Generuj token**. Token dostępu powinien zostać wyświetlony w polu **token dostępu** .

6. Zapisz token dostępu do Schowka lub komputera przed opuszczeniem strony.

7. Następnie zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**  .

8. Jeśli już skonfigurowano podnoszenie uprawnień usługi LinkedIn do logowania jednokrotnego, Wyszukaj wystąpienie funkcji podniesienia uprawnień w serwisie LinkedIn przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj wartość **Podnieś poziom** w galerii aplikacji. Wybierz pozycję LinkedIn Podnieś poziom wyników wyszukiwania i Dodaj ją do listy aplikacji.

9. Wybierz wystąpienie podniesienia uprawnień do serwisu LinkedIn, a następnie wybierz kartę **Inicjowanie obsługi** .

10. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawia stronę podwyższanie poziomu udostępniania w serwisie LinkedIn.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Wypełnij następujące pola w obszarze **poświadczenia administratora** :

    * W polu **adres URL dzierżawy** wprowadź wartość `https://api.linkedin.com` .

    * W polu **token Secret** wprowadź token dostępu wygenerowany w kroku 1, a następnie kliknij pozycję **Testuj połączenie** .

    * Na stronie upperright portalu powinna zostać wyświetlona powiadomienie o powodzeniu.

12. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , a następnie zaznacz pole wyboru poniżej.

13. Kliknij przycisk **Zapisz**.

14. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkowników i grup, które zostaną zsynchronizowane z usługi Azure AD w celu podniesienia uprawnień do serwisu LinkedIn. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane w celu dopasowania do kont użytkowników i grup w serwisie LinkedIn podwyższanie poziomu dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawia mapowania, w tym mapowania atrybutów.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Aby włączyć podwyższenie poziomu usługi Azure AD Provisioning w usłudze LinkedIn, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

16. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do serwisu LinkedIn podniesienia uprawnień w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa zajmie więcej czasu niż kolejne synchronizacje, co będzie odbywać się około co 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji podniesienia uprawnień w serwisie LinkedIn.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
