---
title: Tworzenie nowego pakietu dostępu w usłudze zarządzania prawami — Azure AD
description: Dowiedz się, jak utworzyć nowy pakiet dostępu do zasobów, które chcesz udostępnić w Azure Active Directory Zarządzanie prawami.
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
ms.openlocfilehash: b2ce3b362d02e7acb0a11e6d93b8e94ca8e4d04e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903534"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Utwórz nowy pakiet dostępu w usłudze Azure AD uprawnienia do zarządzania

Pakiet dostępu umożliwia jednorazowe skonfigurowanie zasobów i zasad, które automatycznie zarządzają dostępem do czasu życia pakietu dostępu. W tym artykule opisano sposób tworzenia nowego pakietu dostępu.

## <a name="overview"></a>Omówienie

Wszystkie pakiety dostępu muszą być umieszczone w kontenerze o nazwie wykaz. Katalog definiuje zasoby, które można dodać do pakietu dostępu. Jeśli nie określisz katalogu, pakiet dostępu zostanie umieszczony w katalogu ogólnym. Obecnie nie można przenieść istniejącego pakietu dostępu do innego katalogu.

Jeśli jesteś menedżerem pakietów dostępu, nie możesz dodać zasobów do katalogu. Korzystasz z zasobów dostępnych w wykazie. Jeśli musisz dodać zasoby do wykazu, możesz polecić właściciela katalogu.

Wszystkie pakiety dostępu muszą mieć co najmniej jedną zasadę. Zasady określają, kto może zażądać pakietu dostępu, a także ustawień zatwierdzania i cyklu życia. Podczas tworzenia nowego pakietu dostępu można utworzyć zasady początkowe dla użytkowników w katalogu, w przypadku użytkowników, którzy nie są w katalogu, tylko do przypisywania bezpośrednio dla administratorów lub wybrać opcję tworzenia zasad później.

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-create/access-package-create.png)

Poniżej przedstawiono procedurę wysokiego poziomu służącą do tworzenia nowego pakietu dostępu.

1. W obszarze Zarządzanie tożsamościami Uruchom proces, aby utworzyć nowy pakiet dostępu.

1. Wybierz katalog, w którym chcesz utworzyć pakiet dostępu.

1. Dodaj zasoby z katalogu do pakietu dostępu.

1. Przypisz role zasobów dla każdego zasobu.

1. Określ użytkowników, którzy mogą żądać dostępu.

1. Określ ustawienia zatwierdzania.

1. Określ ustawienia cyklu życia.

## <a name="start-new-access-package"></a>Uruchom nowy pakiet dostępu

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu**.

1. Kliknij pozycję **nowy pakiet dostępu**.
   
    ![Zarządzanie prawami w Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Informacje podstawowe

Na karcie **podstawowe** można nadać pakietowi dostępu nazwę i określić katalog, w którym ma zostać utworzony pakiet dostępu.

1. Wprowadź nazwę wyświetlaną i Opis pakietu dostępu. Użytkownicy będą widzieć te informacje podczas przesyłania żądania dla pakietu dostępu.

1. Z listy rozwijanej **wykaz** wybierz katalog, w którym chcesz utworzyć pakiet dostępu. Na przykład może istnieć właściciel katalogu, który zarządza wszystkimi zasobami marketingowymi, które mogą być żądane. W takim przypadku można wybrać Katalog Marketing.

    Zostaną wyświetlone tylko wykazy, do których masz uprawnienia do tworzenia pakietów dostępu w programie. Aby utworzyć pakiet dostępu w istniejącym wykazie, użytkownik musi być administratorem globalnym lub administratorem użytkownika albo musi być właścicielem katalogu lub Menedżera pakietów dostępu w tym katalogu.

    ![Pakiet dostępu — podstawy](./media/entitlement-management-access-package-create/basics.png)

    Jeśli jesteś administratorem globalnym, administratorem użytkownika lub autorem katalogu i chcesz utworzyć pakiet dostępu w nowym wykazie, którego nie ma na liście, kliknij przycisk **Utwórz nowy wykaz**. Wprowadź nazwę wykazu i opis, a następnie kliknij przycisk **Utwórz**.

    Tworzony pakiet dostępu i wszystkie zawarte w nim zasoby zostaną dodane do nowego katalogu. Możesz również później dodać dodatkowych właścicieli katalogu.

1. Kliknij przycisk **Dalej**.

## <a name="resource-roles"></a>Role zasobów

Na karcie **role zasobów** Wybierz zasoby do uwzględnienia w pakiecie dostępu. Użytkownicy, którzy żądają i otrzymują pakiet dostępu, otrzymają wszystkie role zasobów w pakiecie dostępu.

1. Kliknij typ zasobu, który chcesz dodać (**grupy i zespoły**, **aplikacje** lub **witryny programu SharePoint**).

1. W wyświetlonym okienku wybierz pozycję co najmniej jeden zasób z listy.

    ![Dostęp do ról zasobów](./media/entitlement-management-access-package-create/resource-roles.png)

    Jeśli tworzysz pakiet dostępu w wykazie ogólnym lub w nowym katalogu, będziesz mieć możliwość wybrania dowolnego zasobu z katalogu, którego jesteś członkiem. Musisz być co najmniej administratorem globalnym, administratorem użytkownika lub autorem katalogu.

    Jeśli tworzysz pakiet dostępu w istniejącym wykazie, możesz wybrać dowolny zasób, który znajduje się już w katalogu bez jego właściciela.

    Jeśli jesteś administratorem globalnym, administratorem użytkowników lub właścicielem katalogu, masz dodatkową możliwość wyboru zasobów, których jesteś właścicielem, które nie znajdują się jeszcze w wykazie. W przypadku wybrania zasobów, które nie są obecnie w wybranym wykazie, te zasoby zostaną również dodane do wykazu dla innych administratorów wykazu do tworzenia pakietów dostępu za pomocą programu. Aby wyświetlić wszystkie zasoby, które można dodać do wykazu, zaznacz pole wyboru **Zobacz wszystkie** w górnej części okienka wybieranie. Jeśli chcesz tylko wybrać zasoby, które znajdują się obecnie w wybranym wykazie, pozostaw pole wyboru **Zobacz wszystkie** niezaznaczone (stan domyślny).

1. Po wybraniu zasobów na liście **rola** wybierz rolę, do której użytkownicy mają być przypisani.

    ![Dostęp do pakietu — wybór roli zasobów](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kliknij przycisk **Dalej**.

>[!NOTE]
>Można dodać grupy dynamiczne do wykazu i pakietu dostępu. Jednak podczas zarządzania zasobem grupy dynamicznej w pakiecie dostępu będzie można wybrać tylko rolę właściciela.

## <a name="requests"></a>Żądania

Na karcie **żądania** Utwórz pierwsze zasady, aby określić, kto może zażądać pakietu dostępu, a także ustawienia zatwierdzenia. Później można utworzyć więcej zasad żądań, aby umożliwić dodatkowym grupom użytkowników zażądanie pakietu dostępu przy użyciu własnych ustawień zatwierdzenia.

![Pakiet dostępu — karta żądania](./media/entitlement-management-access-package-create/requests.png)

W zależności od tego, kto ma być w stanie zażądać tego pakietu dostępu, wykonaj czynności opisane w jednej z następujących sekcji.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Przeglądanie i tworzenie

Na karcie **Przegląd i tworzenie** możesz przejrzeć ustawienia i sprawdzić, czy występują błędy walidacji.

1. Przejrzyj ustawienia pakietu dostępu

    ![Pakiet dostępu — Włączanie ustawienia zasad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknij przycisk **Utwórz** , aby utworzyć pakiet dostępu.

    Nowy pakiet dostępu zostanie wyświetlony na liście pakietów dostępu.

## <a name="creating-an-access-package-programmatically"></a>Programistyczne tworzenie pakietu dostępu

Możesz również utworzyć pakiet dostępu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienie, `EntitlementManagement.ReadWrite.All` może wywołać interfejs API, aby

1. [Wyświetl listę accessPackageResources w wykazie](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) i [Utwórz accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) dla wszystkich zasobów, które nie znajdują się jeszcze w wykazie.
1. [Wyświetl listę accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) każdego AccessPackageResource w accessPackageCatalog. Ta lista ról będzie następnie używana do wybierania roli podczas tworzenia accessPackageResourceRoleScope.
1. [Utwórz element accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Utwórz element accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Utwórz accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) dla każdej roli zasobu wymaganej w pakiecie dostępu.

## <a name="next-steps"></a>Następne kroki

- [Link udostępniania do żądania pakietu dostępu](entitlement-management-access-package-settings.md)
- [Zmiana ról zasobów dla pakietu dostępu](entitlement-management-access-package-resources.md)
- [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-assignments.md)
