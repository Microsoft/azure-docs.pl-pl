---
title: Wyświetlanie, dodawanie i usuwanie przypisań dla pakietu dostępu w usłudze Azure AD entitlement management — Azure Active Directory
description: Dowiedz się, jak wyświetlać, dodawać i usuwać przypisania dla pakietu dostępu w Azure Active Directory zarządzania uprawnieniami.
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
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532143"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Wyświetlanie, dodawanie i usuwanie przypisań dla pakietu dostępu w usłudze Azure AD entitlement management

W usłudze Azure AD entitlement management możesz zobaczyć, kto został przypisany do pakietów dostępu, ich zasad i stanu. Jeśli pakiet dostępu ma odpowiednie zasady, możesz również bezpośrednio przypisać użytkownika do pakietu dostępu. W tym artykule opisano sposób wyświetlania, dodawania i usuwania przypisań pakietów dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z zarządzania uprawnieniami usługi Azure AD i przypisywać użytkowników do pakietów dostępu, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — wersja Premium P2
- Licencja Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Wyświetlanie osób, które mają przypisanie

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisania pakietów dostępu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Pakiety dostępu,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Przypisania,** aby wyświetlić listę aktywnych przypisań.

    ![Lista przypisań do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Aby wyświetlić listę przypisań, które nie mają prawidłowo aprowizowanych ról zasobów, kliknij stan filtru i wybierz **pozycję Dostarczanie**.

    Dodatkowe szczegóły dotyczące błędów dostarczania można wyświetlić, lokalizując odpowiednie żądanie użytkownika na **stronie** Żądania.

1. Aby wyświetlić wygasłe przypisania, kliknij stan filtru i wybierz pozycję **Wygasłe.**

1. Aby pobrać plik CSV z przefiltrowanych list, kliknij pozycję **Pobierz.**

### <a name="viewing-assignments-programmatically"></a>Programowe wyświetlanie przypisań

Przypisania można również pobrać w pakiecie dostępu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może wywołać interfejs API, aby wyświetlić listę `EntitlementManagement.ReadWrite.All` [accessPackageAssignments.](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)

## <a name="directly-assign-a-user"></a>Bezpośrednie przypisywanie użytkownika

W niektórych przypadkach może być konieczne bezpośrednie przypisanie określonych użytkowników do pakietu dostępu, dzięki czemu użytkownicy nie muszą przechodzić przez proces żądania pakietu dostępu. Aby bezpośrednio przypisywać użytkowników, pakiet dostępu musi mieć zasady, które zezwalają na bezpośrednie przypisania administratorów.

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników, właściciel katalogu, menedżer pakietów dostępu lub menedżer przypisania pakietów dostępu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Pakiety dostępu,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **pozycję Przypisania.**

1. Kliknij **pozycję Nowe przypisanie,** aby otworzyć okno Dodawanie użytkownika w celu uzyskania dostępu do pakietu.

    ![Przypisania — dodawanie użytkownika w celu uzyskania dostępu do pakietu](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kliknij **pozycję Dodaj** użytkowników, aby wybrać użytkowników, do których chcesz przypisać ten pakiet dostępu.

1. Na liście **Wybierz zasady** wybierz zasady, według których przyszłe żądania i cykl życia użytkowników będą podlegać i według których będą śledzone. Jeśli chcesz, aby wybrani użytkownicy mieli różne ustawienia zasad, możesz kliknąć pozycję **Utwórz nowe zasady,** aby dodać nowe zasady.

1. Ustaw datę i godzina rozpoczęcia i zakończenia przypisania wybranych użytkowników. Jeśli nie podano daty zakończenia, zostaną użyte ustawienia cyklu życia zasad.

1. Opcjonalnie podaj uzasadnienie bezpośredniego przypisania do przechowywania rekordów.

1. Kliknij **przycisk Dodaj,** aby bezpośrednio przypisać wybranych użytkowników do pakietu dostępu.

    Po kilku chwilach kliknij przycisk **Odśwież,** aby wyświetlić użytkowników na liście Przypisania.

### <a name="directly-assigning-users-programmatically"></a>Programowe bezpośrednie przypisywanie użytkowników

Możesz również bezpośrednio przypisać użytkownika do pakietu dostępu przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może wywołać interfejs API, aby utworzyć `EntitlementManagement.ReadWrite.All` [element accessPackageAssignmentRequest.](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-an-assignment"></a>Usuwanie przypisania

**Rola wymagań wstępnych:** administrator globalny, administrator użytkowników, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. Na stronie Azure Portal kliknij pozycję **Azure Active Directory** a następnie kliknij pozycję **Nadzór nad tożsamościami.**

1. W menu po lewej stronie kliknij pozycję **Pakiety dostępu,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij **pozycję Przypisania.**
 
1. Kliknij pole wyboru obok użytkownika, którego przypisanie chcesz usunąć z pakietu dostępu. 

1. Kliknij przycisk **Usuń** w górnej części okienka po lewej stronie. 
 
    ![Przypisania — usuwanie użytkownika z pakietu dostępu](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Zostanie wyświetlone powiadomienie informujące o usunięciu przypisania. 

## <a name="next-steps"></a>Następne kroki

- [Zmienianie żądania i ustawień pakietu dostępu](entitlement-management-access-package-request-policy.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)