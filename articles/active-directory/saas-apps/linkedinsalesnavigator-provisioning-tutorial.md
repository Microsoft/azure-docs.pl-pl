---
title: 'Samouczek: Inicjowanie obsługi administracyjnej użytkowników — Nawigator sprzedaży LinkedIn, usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i wyrównywalizacji kont użytkowników do usługi LinkedIn Sales Navigator.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b9f2dc64d1d3ddd8253a253dcab8ef972032f9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869741"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie nawigatora sprzedaży LinkedIn do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w linkedin sales navigator i usługi Azure AD, aby automatycznie aprowizować i usuwać z aprowizowania konta użytkowników z usługi Azure AD do LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawa nawigatora sprzedaży LinkedIn 
* Konto administratora w Nawigatorze Sprzedaży LinkedIn z dostępem do Centrum kont LinkedIn

> [!NOTE]
> Usługa Azure Active Directory integruje się z LinkedIn Sales Navigator przy użyciu protokołu [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Przypisywanie użytkowników do Nawigatora Sprzedaży LinkedIn

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD zostaną zsynchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do usługi LinkedIn Sales Navigator. Po podjęciu decyzji, można przypisać tych użytkowników do LinkedIn Sales Navigator, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Ważne wskazówki dotyczące przypisywania użytkowników do LinkedIn Sales Navigator

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi LinkedIn Sales Navigator w celu przetestowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Nawigatora Sprzedaży LinkedIn należy wybrać rolę **użytkownika** w oknie dialogowym przypisania. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w nawigatorze sprzedaży linkedin

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API obsługi administracyjnej konta scim usługi LinkedIn Sales Navigator i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w programie LinkedIn Sales Navigator na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na saml dla LinkedIn Sales Navigator, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika w nawigatorze sprzedaży LinkedIn w usłudze Azure AD:

Pierwszym krokiem jest pobranie tokenu dostępu linkedin. Jeśli jesteś administratorem przedsiębiorstwa, możesz samodzielnie aprowizować token dostępu. W centrum kont przejdź do **ustawień globalnych ustawień &gt; ** i otwórz panel Ustawienia **SCIM.**

> [!NOTE]
> Jeśli uzyskujesz dostęp do centrum kont bezpośrednio, a nie za pośrednictwem łącza, możesz do niego dotrzeć, wykonując następujące kroki.

1. Zaloguj się do Centrum kont.

2. Wybierz ** &gt; pozycję Ustawienia administratora** .

3. Kliknij pozycję **Zaawansowane integracje** na lewym pasku bocznym. Jesteś kierowany do centrum kont.

4. Kliknij **+ Dodaj nową konfigurację SCIM** i postępuj zgodnie z procedurą, wypełniając każde pole.

    > [!NOTE]
    > Jeśli automatyczne przypisywanie licencji nie jest włączone, oznacza to, że synchronizowane są tylko dane użytkownika.

    ![Inicjowanie obsługi administracyjnej nawigatora sprzedaży linkedin](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Gdy przypisanie autolicencji jest włączone, należy zanotować wystąpienie aplikacji i typ licencji. Licencje są przypisywane na zasadzie "kto pierwszy, ten lepszy", dopóki nie zostaną podjęte wszystkie licencje.

    ![Inicjowanie obsługi administracyjnej nawigatora sprzedaży linkedin](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Kliknij **pozycję Generuj token**. Powinien zostać wyświetlony token dostępu wyświetlany w polu **token dostępu.**

6. Zapisz token dostępu w schowku lub komputerze przed opuszczeniem strony.

7. Następnie zaloguj się do [witryny Azure Portal](https://portal.azure.com)i przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

8. Jeśli navigator sprzedaży linkedin został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Nawigatora Sprzedaży LinkedIn za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **Nawigatora Sprzedaży LinkedIn** w galerii aplikacji. Wybierz LinkedIn Sales Navigator z wyników wyszukiwania i dodaj go do listy aplikacji.

9. Wybierz wystąpienie Nawigatora Sprzedaży LinkedIn, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

10. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej nawigatora sprzedaży linkedin](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Wypełnij następujące pola w obszarze **Poświadczenia administratora:**

    * W polu Adres URL https://developer.linkedin.com **dzierżawy** wprowadź .

    * W polu **Tajny token** wprowadź token dostępu wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

    * Powiadomienie o sukcesie powinno być widoczne po prawej stronie portalu.

12. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

13. Kliknij pozycję **Zapisz**.

14. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika i grupy, które będą synchronizowane z usługi Azure AD do LinkedIn Sales Navigator. Należy zauważyć, że atrybuty wybrane jako **właściwości dopasowania** będą używane do dopasowania kont użytkowników i grup w LinkedIn Sales Navigator do operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej nawigatora sprzedaży linkedin](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla nawigatora sprzedaży linkedin, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

16. Kliknij pozycję **Zapisz**.

Spowoduje to rozpoczęcie początkowej synchronizacji wszystkich użytkowników i/lub grup przypisanych do LinkedIn Sales Navigator w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa potrwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji LinkedIn Sales Navigator.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
