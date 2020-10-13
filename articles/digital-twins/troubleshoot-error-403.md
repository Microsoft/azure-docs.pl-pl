---
title: 'Żądanie bliźniaczych reprezentacji cyfrowego platformy Azure nie powiodło się. stan: 403 (niedozwolone)'
description: 'Przyczyny i rozwiązania dotyczące "żądania obsługi nie powiodły się. Stan: 403 (dostęp zabroniony) "w usłudze Azure Digital bliźniaczych reprezentacji.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: bc4fbbc265bef00be27c890c3f090a49591dc415
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90562744"
---
# <a name="service-request-failed-status-403-forbidden"></a>Żądanie usługi nie powiodło się. Stan: 403 (dostęp zabroniony)

W tym artykule opisano przyczyny i 403 kroki rozwiązywania problemów z żądaniami obsługi do usługi Azure Digital bliźniaczych reprezentacji. 

## <a name="symptoms"></a>Objawy

Ten błąd może wystąpić w przypadku wielu typów żądań obsługi, które wymagają uwierzytelniania. W efekcie żądanie interfejsu API nie powiedzie się, zwracając stan błędu `403 (Forbidden)` .

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna #1

Najczęściej ten błąd wskazuje, że uprawnienia kontroli dostępu opartej na rolach (RBAC) dla usługi nie są poprawnie skonfigurowane. Wiele akcji wystąpienia usługi Azure Digital bliźniaczych reprezentacji wymaga posiadania roli *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* **w wystąpieniu, które chcesz zarządzać**. 

### <a name="cause-2"></a>Przyczyna #2

Jeśli używasz aplikacji klienckiej do komunikowania się z usługą Azure Digital bliźniaczych reprezentacji, ten błąd może wystąpić, ponieważ Rejestracja aplikacji [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) nie ma uprawnień skonfigurowanych dla usługi Azure Digital bliźniaczych reprezentacji.

Rejestracja aplikacji musi mieć skonfigurowane uprawnienia dostępu dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Następnie, gdy aplikacja kliencka będzie uwierzytelniana względem rejestracji aplikacji, zostanie mu przyznany uprawnienia skonfigurowane do rejestracji aplikacji.

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>#1 rozwiązania

Pierwsze rozwiązanie polega na sprawdzeniu, czy użytkownik platformy Azure ma rolę _**właściciela cyfrowego bliźniaczych reprezentacji (wersja zapoznawcza)**_ na wystąpieniu, które próbujesz zarządzać. Jeśli nie masz tej roli, skonfiguruj ją.

Należy zauważyć, że ta rola różni się od...
* rola *właściciela* w całej subskrypcji platformy Azure. *Usługa Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza)* jest rolą w ramach usługi Azure Digital bliźniaczych reprezentacji i jest objęta zakresem tego indywidualnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
* rola *właściciela* w usłudze Azure Digital bliźniaczych reprezentacji. Są to dwie różne role usługi Azure Digital bliźniaczych reprezentacji Management, a *usługa Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza)* to rola, która powinna być używana do zarządzania w ramach wersji zapoznawczej.

#### <a name="check-current-setup"></a>Sprawdź bieżącą konfigurację

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Rozwiązywanie problemów 

Jeśli nie masz tego przypisania roli, ktoś z rolą właściciela w **subskrypcji platformy Azure** powinien uruchomić następujące polecenie, aby udzielić użytkownikowi platformy Azure roli *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* w **wystąpieniu usługi Azure Digital bliźniaczych reprezentacji**. 

Jeśli jesteś właścicielem subskrypcji, możesz uruchomić to polecenie samodzielnie. Jeśli nie, skontaktuj się z właścicielem, aby uruchomić to polecenie w Twoim imieniu.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Aby uzyskać więcej informacji na temat tego wymagania roli i procesu przypisywania, zobacz [sekcję *Konfigurowanie uprawnień dostępu użytkownika* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) w temacie *How to: Konfigurowanie wystąpienia i uwierzytelniania (CLI lub portal)*.

Jeśli masz już to przypisanie roli i nadal napotkasz problem 403, przejdź do następnego rozwiązania.

### <a name="solution-2"></a>#2 rozwiązania

Drugie rozwiązanie polega na sprawdzeniu, czy rejestracja aplikacji usługi Azure AD ma uprawnienia skonfigurowane dla usługi Azure Digital bliźniaczych reprezentacji. Jeśli nie skonfigurowano tego ustawienia, należy je skonfigurować.

#### <a name="check-current-setup"></a>Sprawdź bieżącą konfigurację

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Najpierw sprawdź, czy ustawienia uprawnień Digital bliźniaczych reprezentacji systemu Azure zostały prawidłowo ustawione podczas rejestracji. W tym celu wybierz pozycję *manifest* z paska menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj tych pól w obszarze `requiredResourceAccess` . Wartości powinny być zgodne z poniższymi zrzutu ekranu:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Rozwiązywanie problemów

Jeśli którykolwiek z tych elementów pojawia się inaczej niż opisano, postępuj zgodnie z instrukcjami dotyczącymi sposobu konfigurowania rejestracji aplikacji w [sekcji *Konfigurowanie uprawnień dostępu dla aplikacji klienckich* ](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) w artykule *instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI lub portal)*.

## <a name="next-steps"></a>Następne kroki

Przeczytaj czynności konfiguracyjne dotyczące tworzenia i uwierzytelniania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI)*](how-to-set-up-instance-cli.md)

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)