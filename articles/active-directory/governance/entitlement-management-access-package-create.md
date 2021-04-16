---
title: Tworzenie nowego pakietu dostępu w zarządzaniu uprawnieniami — Azure AD
description: Dowiedz się, jak utworzyć nowy pakiet dostępu dla zasobów, które chcesz udostępnić w Azure Active Directory zarządzania uprawnieniami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0312d905284f8c5a9817e9550d340bf6135032
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532210"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Tworzenie nowego pakietu dostępu w usłudze Azure AD do zarządzania uprawnieniami

Pakiet dostępu umożliwia instalację zasobów i zasad w jeden raz, która automatycznie administruje dostępem przez okres życia pakietu dostępu. W tym artykule opisano sposób tworzenia nowego pakietu dostępu.

## <a name="overview"></a>Omówienie

Wszystkie pakiety dostępu należy umieścić w kontenerze nazywanym wykazem. Katalog określa, jakie zasoby można dodać do pakietu dostępu. Jeśli nie określisz katalogu, pakiet dostępu zostanie umieszczany w wykazie ogólnym. Obecnie nie można przenieść istniejącego pakietu dostępu do innego katalogu.

Jeśli jesteś menedżerem pakietów dostępu, nie możesz dodać zasobów, których jesteś właścicielem do katalogu. Korzystanie z zasobów dostępnych w wykazie jest ograniczone. Jeśli musisz dodać zasoby do katalogu, możesz poprosić właściciela katalogu.

Wszystkie pakiety dostępu muszą mieć co najmniej jedną zasady. Zasady określają, kto może żądać pakietu dostępu, a także ustawienia zatwierdzenia i cyklu życia. Podczas tworzenia nowego pakietu dostępu można utworzyć początkowe zasady dla użytkowników w katalogu, dla użytkowników, którzy nie mają dostępu do katalogu, tylko dla bezpośrednich przypisań administratora lub później utworzyć zasady.

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-create/access-package-create.png)

Poniżej znajdują się instrukcje wysokiego poziomu tworzenia nowego pakietu dostępu.

1. W łasce zarządzania tożsamościami rozpocznij proces tworzenia nowego pakietu dostępu.

1. Wybierz katalog, w którym chcesz utworzyć pakiet dostępu.

1. Dodaj zasoby z wykazu do pakietu dostępu.

1. Przypisz role zasobów dla każdego zasobu.

1. Określ użytkowników, którzy mogą żądać dostępu.

1. Określ ustawienia zatwierdzania.

1. Określ ustawienia cyklu życia.

## <a name="start-new-access-package"></a>Uruchamianie nowego pakietu dostępu

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników, właściciel katalogu lub menedżer pakietów dostępu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Pakiety dostępu**.

1. Kliknij **pozycję Nowy pakiet dostępu.**
   
    ![Zarządzanie uprawnieniami w Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Podstawy

Na **karcie Podstawy** nadaj pakietowi dostępu nazwę i określ katalog, w którym chcesz utworzyć pakiet dostępu.

1. Wprowadź nazwę wyświetlaną i opis pakietu dostępu. Użytkownicy zobaczą te informacje podczas przesyłania żądania pakietu dostępu.

1. Z **listy rozwijanej** Wykaz wybierz wykaz, w którym chcesz utworzyć pakiet dostępu. Na przykład właściciel katalogu może zarządzać wszystkimi zasobami marketing, których można zażądać. W tym przypadku możesz wybrać katalog marketingowy.

    Zobaczysz tylko wykazy, w których masz uprawnienia do tworzenia pakietów dostępu. Aby utworzyć pakiet dostępu w istniejącym wykazie, musisz być administratorem administrator globalny lub administratorem użytkowników albo właścicielem katalogu lub menedżerem pakietów dostępu w tym wykazie.

    ![Pakiet dostępu — podstawy](./media/entitlement-management-access-package-create/basics.png)

    Jeśli jesteś administratorem administrator globalny, administratorem użytkowników lub twórcą katalogu i chcesz utworzyć pakiet dostępu w nowym wykazie, który nie znajduje się na liście, kliknij pozycję Utwórz **nowy katalog.** Wprowadź nazwę i opis katalogu, a następnie kliknij pozycję **Utwórz.**

    Pakiet dostępu, który tworzysz, i wszystkie zawarte w nim zasoby zostaną dodane do nowego katalogu. Możesz również dodać kolejnych właścicieli wykazu później.

1. Kliknij przycisk **Dalej**.

## <a name="resource-roles"></a>Role zasobów

Na **karcie Role zasobów** wybierz zasoby do dołączyć do pakietu dostępu. Użytkownicy, którzy zażądają i otrzymają pakiet dostępu, otrzymają wszystkie role zasobów w pakiecie dostępu.

1. Kliknij typ zasobu, który chcesz dodać **(grupy** i zespoły, **aplikacje** lub witryny **programu SharePoint).**

1. W wyświetlonym okienku Wybierz wybierz jeden lub więcej zasobów z listy.

    ![Pakiet dostępu — role zasobów](./media/entitlement-management-access-package-create/resource-roles.png)

    Jeśli tworzysz pakiet dostępu w katalogu Ogólnym lub nowym katalogu, możesz wybrać dowolny zasób z własnego katalogu. Musisz mieć co najmniej jedną administrator globalny, administratora użytkowników lub twórcę katalogu.

    Jeśli tworzysz pakiet dostępu w istniejącym wykazie, możesz wybrać dowolny zasób, który znajduje się już w wykazie, bez jego właścicielem.

    Jeśli jesteś użytkownikiem administrator globalny, administratorem użytkowników lub właścicielem katalogu, masz dodatkową opcję wyboru zasobów, których jesteś właścicielem, które nie znajdują się jeszcze w wykazie. Jeśli wybierzesz zasoby, które nie są obecnie dostępne w wybranym wykazie, zostaną one również dodane do katalogu, aby inni administratorzy katalogu mogą tworzyć pakiety dostępu. Aby wyświetlić wszystkie zasoby, które można dodać  do wykazu, zaznacz pole wyboru Zobacz wszystko w górnej części okienka Wybierz. Jeśli chcesz tylko wybrać zasoby, które znajdują się obecnie  w wybranym wykazie, pozostaw pole wyboru Zobacz wszystkie niezaznaczone (stan domyślny).

1. Po wybraniu zasobów na liście **Rola** wybierz rolę, którą użytkownicy mają być przypisani do zasobu.

    ![Pakiet dostępu — wybór roli zasobu](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kliknij przycisk **Dalej**.

>[!NOTE]
>Grupy dynamiczne można dodawać do wykazu i pakietu dostępu. Podczas zarządzania dynamicznym zasobem grupy w pakiecie dostępu będzie można jednak wybrać tylko rolę Właściciela.

## <a name="requests"></a>Żądania

Na karcie **Żądania** należy utworzyć pierwsze zasady, aby określić, kto może żądać pakietu dostępu, a także ustawienia zatwierdzania. Później można utworzyć więcej zasad żądań, aby umożliwić dodatkowym grupom użytkowników żądanie pakietu dostępu z własnymi ustawieniami zatwierdzania.

![Pakiet dostępu — karta Żądania](./media/entitlement-management-access-package-create/requests.png)

W zależności od tego, kto ma być w stanie zażądać tego pakietu dostępu, wykonaj kroki opisane w jednej z poniższych sekcji.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Przeglądanie i tworzenie

Na karcie **Przeglądanie + tworzenie** możesz przejrzeć ustawienia i sprawdzić, czy występują błędy weryfikacji.

1. Przeglądanie ustawień pakietu dostępu

    ![Pakiet dostępu — włączanie ustawienia zasad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknij **pozycję Utwórz,** aby utworzyć pakiet dostępu.

    Nowy pakiet dostępu zostanie wyświetlony na liście pakietów dostępu.

## <a name="creating-an-access-package-programmatically"></a>Programowe tworzenie pakietu dostępu

Pakiet dostępu można również utworzyć przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może `EntitlementManagement.ReadWrite.All` wywołać interfejs API, aby

1. [Utwórz listę elementów accessPackageResources](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta&preserve-view=true) w wykazie i utwórz element [accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta&preserve-view=true) dla wszystkich zasobów, które nie znajdują się jeszcze w wykazie.
1. [Wylikuj listę elementów accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) każdego accessPackageResource w poszczególnych elementach accessPackageCatalog. Ta lista ról zostanie następnie użyta do wybrania roli podczas tworzenia accessPackageResourceRoleScope.
1. [Utwórz element accessPackage.](/graph/tutorial-access-package-api&view=graph-rest-beta&preserve-view=true)
1. [Utwórz element accessPackageAssignmentPolicy.](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta&preserve-view=true)
1. [Utwórz element accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) dla każdej roli zasobów wymaganej w pakiecie dostępu.

## <a name="next-steps"></a>Następne kroki

- [Udostępnij link do żądania pakietu dostępu](entitlement-management-access-package-settings.md)
- [Zmienianie ról zasobów dla pakietu dostępu](entitlement-management-access-package-resources.md)
- [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-assignments.md)
- [Tworzenie przeglądu dostępu dla pakietu dostępu](entitlement-management-access-reviews-create.md)
