---
title: Zmiana ról zasobów dla pakietu dostępu w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak zmienić role zasobów dla istniejącego pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
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
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644184"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmiana ról zasobów dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Jako Menedżer pakietów programu Access można w dowolnym momencie zmienić zasoby w pakiecie dostępu bez konieczności aprowizacji dostępu użytkownika do nowych zasobów lub usuwania ich dostępu z poprzednich zasobów. W tym artykule opisano sposób zmiany ról zasobów dla istniejącego pakietu dostępu.

Ten film wideo zawiera omówienie sposobu zmiany pakietu dostępu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Sprawdź wykaz zasobów

Jeśli musisz dodać zasoby do pakietu dostępu, sprawdź, czy zasoby, których potrzebujesz, są dostępne w wykazie. Jeśli jesteś menedżerem pakietów dostępu, nie możesz dodać zasobów do wykazu, nawet jeśli jesteś ich własnością. Korzystasz z zasobów dostępnych w wykazie.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **katalog** , a następnie otwórz wykaz.

1. W menu po lewej stronie kliknij pozycję **zasoby** , aby wyświetlić listę zasobów w tym katalogu.

    ![Lista zasobów w wykazie](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Jeśli jesteś menedżerem pakietów dostępu i musisz dodać zasoby do wykazu, możesz poproś właściciela katalogu o ich dodanie.

## <a name="add-resource-roles"></a>Dodawanie ról zasobów

Rola zasobu to kolekcja uprawnień skojarzonych z zasobem. Aby udostępnić zasoby użytkownikom do żądania, należy dodać role zasobów do pakietu dostępu. Można dodawać role zasobów dla grup, zespołów, aplikacji i witryn programu SharePoint.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **role zasobów**.

1. Kliknij pozycję **Dodaj role zasobów** , aby otworzyć stronę Dodawanie ról zasobów do pakietu dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. W zależności od tego, czy chcesz dodać witrynę grupy, zespołu, aplikacji lub programu SharePoint, wykonaj kroki z jednej z poniższych sekcji ról zasobów.

## <a name="add-a-group-or-team-resource-role"></a>Dodawanie grupy lub roli zasobu zespołu

Zarządzanie prawami może automatycznie dodawać użytkowników do grupy lub zespołu w usłudze Microsoft Teams, gdy przypiszesz do nich pakiet dostępu. 

- Gdy grupa lub zespół jest częścią pakietu dostępu, a użytkownik jest przypisany do tego pakietu, użytkownik zostanie dodany do tej grupy lub zespołu, jeśli jeszcze nie istnieje.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika są one usuwane z grupy lub zespołu, chyba że są obecnie przypisane do innego pakietu dostępu, który zawiera tę samą grupę lub zespół.

Możesz wybrać dowolną [grupę zabezpieczeń usługi Azure AD lub grupę Microsoft 365](../fundamentals/active-directory-groups-create-azure-portal.md). Administratorzy mogą dodać dowolną grupę do wykazu; Właściciele wykazu mogą dodać dowolną grupę do wykazu, jeśli są właścicielami grupy. Podczas wybierania grupy należy pamiętać o następujących ograniczeniach usługi Azure AD:

- Gdy użytkownik, łącznie z gościem, jest dodawany jako członek do grupy lub zespołu, może zobaczyć wszystkich innych członków tej grupy lub zespołu.
- Usługa Azure AD nie może zmienić członkostwa w grupie, która została zsynchronizowana z systemem Windows Server Active Directory przy użyciu Azure AD Connect lub która została utworzona w usłudze Exchange Online jako grupa dystrybucyjna.  
- Członkostwa w grupach dynamicznych nie można zaktualizować przez dodanie lub usunięcie elementu członkowskiego, więc członkostwa w grupie dynamicznej nie są odpowiednie do użytku z zarządzaniem prawami.

Aby uzyskać więcej informacji, zobacz [porównywanie grup](/office365/admin/create-groups/compare-groups) i [grup Microsoft 365 i Microsoft Teams](/microsoftteams/office-365-groups).

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **grupy i zespoły** , aby otworzyć okienko Wybieranie grup.

1. Wybierz grupy i zespoły, które chcesz uwzględnić w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie grup](./media/entitlement-management-access-package-resources/group-select.png)

1. Kliknij pozycję **Wybierz**.

    Po wybraniu grupy lub zespołu w kolumnie **podtyp** zostanie wystawiona jedna z następujących podtypów:

    | Podtyp | Opis |
    | --- | --- |
    | Zabezpieczenia | Służy do udzielania dostępu do zasobów. |
    | Dystrybucja | Służy do wysyłania powiadomień do grupy osób. |
    | Microsoft 365 | Grupa Microsoft 365, która nie jest włączona dla zespołów. Używany do współpracy między użytkownikami i spoza niej. |
    | Zespół | Grupa Microsoft 365 z włączoną obsługą zespołów. Używany do współpracy między użytkownikami i spoza niej. |

1. Na liście **rola** wybierz pozycję **właściciel** lub **członek**.

    Zwykle wybierasz rolę elementu członkowskiego. W przypadku wybrania roli właściciela, która umożliwi użytkownikom dodawanie lub usuwanie innych członków lub właścicieli.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla grupy lub zespołu](./media/entitlement-management-access-package-resources/group-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu automatycznie staną się członkami tej grupy lub zespołu po dodaniu.

## <a name="add-an-application-resource-role"></a>Dodawanie roli zasobu aplikacji

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do aplikacji usługi Azure AD Enterprise, w tym zarówno aplikacji SaaS, jak i aplikacji organizacji federacyjnych do usługi Azure AD, gdy użytkownik ma przypisany pakiet dostępu. W przypadku aplikacji, które integrują się z usługą Azure AD za pomocą federacyjnego logowania jednokrotnego, usługa Azure AD wystawia tokeny federacyjne dla użytkowników przypisanych do aplikacji.

Aplikacje mogą mieć wiele ról. W przypadku dodawania aplikacji do pakietu dostępu, jeśli aplikacja ma więcej niż jedną rolę, należy określić odpowiednią rolę dla tych użytkowników. Jeśli tworzysz aplikacje, możesz dowiedzieć się więcej o tym, jak te role są dodawane do aplikacji, w temacie [How to: Configure role rola wystawiona w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-enterprise-app-role-management.md).

Gdy rola aplikacji jest częścią pakietu dostępu:

- Gdy użytkownik ma przypisany pakiet dostępu, użytkownik zostanie dodany do tej roli aplikacji, jeśli jeszcze nie istnieje.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika jego dostęp zostanie usunięty z aplikacji, chyba że ma przypisanie do innego pakietu dostępu zawierającego tę rolę aplikacji.

Oto kilka kwestii, które należy wziąć pod uwagę podczas wybierania aplikacji:

- Aplikacje mogą także mieć również grupy przypisane do ich ról.  Możesz wybrać opcję dodania grupy zamiast roli aplikacji w pakiecie dostępu, ale aplikacja nie będzie widoczna dla użytkownika jako część pakietu dostępu w portalu My Access.

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **aplikacje** , aby otworzyć okienko wybierz aplikacje.

1. Wybierz aplikacje, które mają zostać uwzględnione w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie aplikacji](./media/entitlement-management-access-package-resources/application-select.png)

1. Kliknij pozycję **Wybierz**.

1. Z listy **rola** wybierz rolę aplikacji.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla aplikacji](./media/entitlement-management-access-package-resources/application-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu będą automatycznie mieli dostęp do tej aplikacji po dodaniu.

## <a name="add-a-sharepoint-site-resource-role"></a>Dodaj rolę zasobów witryny programu SharePoint

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do witryny usługi SharePoint Online lub kolekcji witryn usługi SharePoint Online po przypisaniu do nich pakietu dostępu.

1. Na stronie **Dodaj rolę zasobów do pakietu dostępu** kliknij pozycję **witryny programu SharePoint** , aby otworzyć okienko Wybieranie witryn usługi SharePoint Online.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie witryn programu SharePoint — widok portalu":::

1. Wybierz witryny usługi SharePoint Online, które mają zostać uwzględnione w pakiecie dostępu.

    ![Dostęp do pakietu — Dodawanie ról zasobów — Wybieranie witryn usługi SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Kliknij pozycję **Wybierz**.

1. Na liście **rola** wybierz rolę witryny usługi SharePoint Online.

    ![Dostęp do pakietu — Dodawanie roli zasobów dla witryny usługi SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Kliknij pozycję **Dodaj**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu będą automatycznie mieli dostęp do tej witryny usługi SharePoint Online, gdy zostanie ona dodana.

## <a name="remove-resource-roles"></a>Usuń role zasobów

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **role zasobów**.

1. Na liście ról zasobów Znajdź rolę zasobu, którą chcesz usunąć.

1. Kliknij przycisk wielokropka (**...**), a następnie kliknij pozycję **Usuń rolę zasobu**.

    Wszyscy użytkownicy z istniejącymi przypisaniami do pakietu dostępu automatycznie otrzymają dostęp do tej roli zasobu po jej usunięciu.

## <a name="when-changes-are-applied"></a>Po zastosowaniu zmian

W obszarze Zarządzanie prawami usługa Azure AD będzie przetwarzać zmiany zbiorcze dotyczące przypisywania i zasobów w pakietach dostępu kilka razy dziennie. Dlatego w przypadku przypisania lub zmiany ról zasobów pakietu dostępu może upłynąć nawet 24 godziny, gdy zmiana zostanie wprowadzona w usłudze Azure AD, oraz czas, w którym konieczne jest propagowanie tych zmian do innych usług online firmy Microsoft lub połączonych aplikacji SaaS. Jeśli zmiana ma wpływ tylko na kilka obiektów, zmiana będzie prawdopodobnie trwać tylko kilka minut w usłudze Azure AD, po której inne składniki usługi Azure AD będą wykrywać zmiany i aktualizować aplikacje SaaS. Jeśli zmiana wpłynie na tysiące obiektów, zmiana będzie trwać dłużej. Jeśli na przykład masz pakiet dostępu z 2 aplikacjami i przypisaniami użytkowników 100 i zdecydujesz się dodać rolę witryny programu SharePoint do pakietu dostępu, może się zdarzyć, że wszyscy użytkownicy będą częścią tej roli witryny programu SharePoint. Postęp można monitorować w dzienniku inspekcji usługi Azure AD, dzienniku aprowizacji usługi Azure AD oraz w dziennikach inspekcji witryny programu SharePoint.

Po usunięciu członka zespołu są one również usuwane z grupy Microsoft 365. Usunięcie z funkcji czatu zespołu może być opóźnione. Aby uzyskać więcej informacji, zobacz [członkostwo w grupie](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instrukcje: Konfigurowanie roszczeń ról wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-enterprise-app-role-management.md)
- [Wprowadzenie do usługi SharePoint Online](/sharepoint/introduction)