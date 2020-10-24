---
title: 'Żądanie bliźniaczych reprezentacji cyfrowego platformy Azure nie powiodło się. stan: 403 (niedozwolone)'
description: 'Przyczyny i rozwiązania dotyczące "żądania obsługi nie powiodły się. Stan: 403 (dostęp zabroniony) "w usłudze Azure Digital bliźniaczych reprezentacji.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: d821d6dacc2620988c32e63439ec2e039819e0a5
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495901"
---
# <a name="service-request-failed-status-403-forbidden"></a>Żądanie usługi nie powiodło się. Stan: 403 (dostęp zabroniony)

W tym artykule opisano przyczyny i 403 kroki rozwiązywania problemów z żądaniami obsługi do usługi Azure Digital bliźniaczych reprezentacji. 

## <a name="symptoms"></a>Objawy

Ten błąd może wystąpić w przypadku wielu typów żądań obsługi, które wymagają uwierzytelniania. W efekcie żądanie interfejsu API nie powiedzie się, zwracając stan błędu `403 (Forbidden)` .

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna #1

Najczęściej ten błąd oznacza, że uprawnienia kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure nie są poprawnie skonfigurowane. Wiele akcji wystąpienia usługi Azure Digital bliźniaczych reprezentacji wymaga posiadania roli *właściciela danych Digital bliźniaczych reprezentacji platformy Azure* **w wystąpieniu, które próbujesz zarządzać**. 

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

### <a name="cause-2"></a>Przyczyna #2

Jeśli używasz aplikacji klienckiej do komunikowania się z usługą Azure Digital bliźniaczych reprezentacji, która jest uwierzytelniana przy użyciu [rejestracji aplikacji](how-to-create-app-registration.md), ten błąd może wystąpić, ponieważ Rejestracja aplikacji nie ma uprawnień skonfigurowanych dla usługi Azure Digital bliźniaczych reprezentacji.

Rejestracja aplikacji musi mieć skonfigurowane uprawnienia dostępu dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Następnie, gdy aplikacja kliencka będzie uwierzytelniana względem rejestracji aplikacji, zostanie mu przyznany uprawnienia skonfigurowane do rejestracji aplikacji.

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>#1 rozwiązania

Pierwsze rozwiązanie polega na sprawdzeniu, czy użytkownik platformy Azure ma rolę _**właściciela danych Digital bliźniaczych reprezentacji systemu Azure**_ w wystąpieniu, które próbujesz zarządzać. Jeśli nie masz tej roli, skonfiguruj ją.

Należy zauważyć, że ta rola różni się od...
* Imię i nazwisko dla tej roli w trakcie okresu zapoznawczego — *właściciel Digital bliźniaczych reprezentacji systemu Azure (wersja zapoznawcza)* (rola jest taka sama, ale zmieniono nazwę).
* rola *właściciela* w całej subskrypcji platformy Azure. *Właściciel danych Digital bliźniaczych reprezentacji systemu Azure* jest rolą w ramach usługi Azure Digital bliźniaczych reprezentacji i jest objęty zakresem tego indywidualnego wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
* rola *właściciela* w usłudze Azure Digital bliźniaczych reprezentacji. Są to dwie odrębne role usługi Azure Digital bliźniaczych reprezentacji Management, a *właściciel danych Digital bliźniaczych reprezentacji platformy Azure* to rola, która powinna być używana do zarządzania w ramach wersji zapoznawczej.

#### <a name="check-current-setup"></a>Sprawdź bieżącą konfigurację

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Rozwiązywanie problemów 

Jeśli nie masz tego przypisania roli, ktoś mający rolę właściciela w **subskrypcji platformy Azure** powinien uruchomić następujące polecenie, aby udzielić użytkownikowi platformy Azure roli *właściciela danych Digital bliźniaczych reprezentacji* w **wystąpieniu usługi Azure Digital bliźniaczych reprezentacji**. 

Jeśli jesteś właścicielem subskrypcji, możesz uruchomić to polecenie samodzielnie. Jeśli nie, skontaktuj się z właścicielem, aby uruchomić to polecenie w Twoim imieniu.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Aby uzyskać więcej informacji na temat tego wymagania roli i procesu przypisywania, zobacz [sekcję *Konfigurowanie uprawnień dostępu użytkownika* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) w temacie *How to: Konfigurowanie wystąpienia i uwierzytelniania (CLI lub portal)*.

Jeśli masz już to przypisanie roli *i* używasz rejestracji aplikacji usługi Azure AD do uwierzytelniania aplikacji klienckiej, możesz przejść do następnego rozwiązania, jeśli to rozwiązanie nie rozwiązało problemu 403.

### <a name="solution-2"></a>#2 rozwiązania

W przypadku korzystania z rejestracji aplikacji usługi Azure AD w celu uwierzytelnienia aplikacji klienckiej drugie możliwe rozwiązanie polega na sprawdzeniu, czy rejestracja aplikacji ma uprawnienia skonfigurowane dla usługi Digital bliźniaczych reprezentacji systemu Azure. Jeśli nie są skonfigurowane, należy je skonfigurować.

#### <a name="check-current-setup"></a>Sprawdź bieżącą konfigurację

Aby sprawdzić, czy uprawnienia zostały skonfigurowane prawidłowo, przejdź do [strony Przegląd rejestracji aplikacji usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) w Azure Portal. Możesz uzyskać dostęp do tej strony, wyszukując *rejestracje aplikacji* na pasku wyszukiwania portalu.

Przejdź do karty *wszystkie aplikacje* , aby wyświetlić wszystkie rejestracje aplikacji, które zostały utworzone w ramach subskrypcji.

Powinna zostać wyświetlona już utworzona Rejestracja aplikacji na liście. Wybierz go, aby otworzyć jego szczegóły.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Strona Rejestracje aplikacji w Azure Portal":::

Najpierw sprawdź, czy ustawienia uprawnień Digital bliźniaczych reprezentacji systemu Azure zostały prawidłowo ustawione podczas rejestracji. W tym celu wybierz pozycję *manifest* z paska menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj tych pól w obszarze `requiredResourceAccess` . Wartości powinny być zgodne z poniższymi zrzutu ekranu:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Strona Rejestracje aplikacji w Azure Portal":::

Następnie wybierz pozycję *uprawnienia interfejsu API* na pasku menu, aby sprawdzić, czy ta rejestracja aplikacji zawiera uprawnienia do odczytu/zapisu w usłudze Azure Digital bliźniaczych reprezentacji. Powinien zostać wyświetlony wpis podobny do tego:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Strona Rejestracje aplikacji w Azure Portal":::

#### <a name="fix-issues"></a>Rozwiązywanie problemów

Jeśli którykolwiek z tych elementów występuje inaczej niż opisano, postępuj zgodnie z instrukcjami dotyczącymi sposobu konfigurowania rejestracji aplikacji w temacie [*How to: Create a App Registration*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Następne kroki

Przeczytaj czynności konfiguracyjne dotyczące tworzenia i uwierzytelniania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji:
* [*Instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI)*](how-to-set-up-instance-cli.md)

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)