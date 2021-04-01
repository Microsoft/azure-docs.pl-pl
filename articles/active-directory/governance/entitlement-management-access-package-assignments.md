---
title: Wyświetlanie, Dodawanie i usuwanie przypisań pakietu dostępu w usłudze Azure AD uprawnienia zarządzania — Azure Active Directory
description: Dowiedz się, jak wyświetlać, dodawać i usuwać przypisania dla pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
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
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980072"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Wyświetlanie, Dodawanie i usuwanie przypisań pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

W usłudze Azure AD uprawnienia do zarządzania można zobaczyć, kto został przypisany do pakietów, ich zasad i stanu. Jeśli pakiet dostępu ma odpowiednie zasady, możesz również bezpośrednio przypisać użytkownika do pakietu dostępu. W tym artykule opisano sposób wyświetlania, dodawania i usuwania przypisań dla pakietów dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać usługi Azure AD do zarządzania prawami i przypisywać użytkownikom dostęp do pakietów, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — wersja Premium P2
- Licencja Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Wyświetl, kto ma przypisanie

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **przypisania** , aby wyświetlić listę aktywnych przypisań.

    ![Lista przypisań do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Aby wyświetlić listę przypisań, dla których nie zainicjowano prawidłowo wszystkich ról zasobów, kliknij stan filtru i wybierz pozycję **dostarczanie**.

    Aby wyświetlić dodatkowe szczegóły dotyczące błędów dostarczania, można zlokalizować odpowiednie żądanie użytkownika na stronie **żądania** .

1. Aby wyświetlić wygasłe przypisania, kliknij stan filtru i wybierz pozycję **wygasłe**.

1. Aby pobrać plik CSV z filtrowanej listy, kliknij przycisk **Pobierz**.

### <a name="viewing-assignments-programmatically"></a>Programistyczne Wyświetlanie przypisań

Możesz również pobrać przypisania w pakiecie dostępu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienie, `EntitlementManagement.ReadWrite.All` może wywołać interfejs API, aby [wyświetlić listę accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Bezpośrednie przypisanie użytkownika

W niektórych przypadkach może być konieczne bezpośrednie przypisanie określonych użytkowników do pakietu dostępu, dzięki czemu użytkownicy nie muszą przechodzić przez proces żądania pakietu dostępu. Aby bezpośrednio przypisywać użytkowników, pakiet dostępu musi mieć zasady umożliwiające bezpośrednie przypisania administratora.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania**.

1. Kliknij pozycję **nowe przypisanie** , aby otworzyć aplet Dodaj użytkownika do pakietu.

    ![Przypisania — Dodawanie użytkownika do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kliknij przycisk **Dodaj użytkowników** , aby wybrać użytkowników, do których chcesz przypisać ten pakiet dostępu.

1. Z listy **Wybierz zasady** wybierz zasady, według których będą podlegać przyszłe żądania i cykl życia użytkowników. Jeśli chcesz, aby wybrani użytkownicy mieli inne ustawienia zasad, kliknij pozycję **Utwórz nowe zasady** , aby dodać nowe zasady.

1. Ustaw datę i godzinę, o której chcesz rozpocząć i zakończyć przypisanie wybranych użytkowników. Jeśli data zakończenia nie zostanie podana, zostaną użyte ustawienia cyklu życia zasad.

1. Opcjonalnie możesz podać uzasadnienie dla bezpośredniego przypisania do przechowywania rekordów.

1. Kliknij przycisk **Dodaj** , aby bezpośrednio przypisać wybranych użytkowników do pakietu dostępu.

    Po kilku chwilach kliknij pozycję **Odśwież** , aby wyświetlić użytkowników na liście przydziałów.

### <a name="directly-assigning-users-programmatically"></a>Programowe przypisywanie użytkowników bezpośrednio

Możesz również bezpośrednio przypisać użytkownika do pakietu dostępu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienie, `EntitlementManagement.ReadWrite.All` może wywołać interfejs API, aby [utworzyć accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Usuwanie przypisania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania**.
 
1. Kliknij pole wyboru obok użytkownika, którego przypisanie chcesz usunąć z pakietu dostępu. 

1. Kliknij przycisk **Usuń** w górnej części okienka po lewej stronie. 
 
    ![Przypisania — Usuwanie użytkownika z pakietu dostępu](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Zostanie wyświetlone powiadomienie informujące o tym, że przypisanie zostało usunięte. 

## <a name="next-steps"></a>Następne kroki

- [Żądanie zmiany i ustawienia pakietu dostępu](entitlement-management-access-package-request-policy.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)