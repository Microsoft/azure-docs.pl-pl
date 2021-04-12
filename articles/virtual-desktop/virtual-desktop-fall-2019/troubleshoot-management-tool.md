---
title: Narzędzie do zarządzania pulpitem wirtualnym systemu Windows (klasycznego) — Azure
description: Jak rozwiązywać problemy z narzędziem do zarządzania pulpitem wirtualnym systemu Windows (klasycznym).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 430ab72a3eb0cd41d707ff0eb5d10d8b97162d53
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444245"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Rozwiązywanie problemów z narzędziem do zarządzania pulpitem wirtualnym systemu Windows (klasycznym)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

W tym artykule opisano problemy, które mogą wystąpić podczas wdrażania narzędzia do zarządzania pulpitami wirtualnymi systemu Windows i sposoby ich naprawiania.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Błąd: usługi narzędzia do zarządzania skonfigurowane, ale instalacja automatyczna kończy się niepowodzeniem

Po pomyślnym skonfigurowaniu usług dla narzędzia do zarządzania, ale instalacja automatyczna nie powiedzie się, zostanie wyświetlony następujący komunikat o błędzie:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Zazwyczaj oznacza to jedną z następujących dwóch rzeczy:

- Użytkownik ma uprawnienia właściciela do subskrypcji i administratora globalnego na poziomie dzierżawy, ale nie może zalogować się do platformy Azure.
- Ustawienia konta użytkownika mają włączone uwierzytelnianie wieloskładnikowe.

Aby rozwiązać ten problem:

1. Upewnij się, że użytkownik utworzony dla nazwy głównej użytkownika Azure Active Directory ma poziom subskrypcji "Współautor".
2. Zaloguj się do <portal.azure.com> przy użyciu konta UPN, aby sprawdzić ustawienia konta i upewnić się, że uwierzytelnianie wieloskładnikowe nie jest włączone. Jeśli jest włączona, wyłącz ją.
3. Odwiedź stronę zgody na pulpit wirtualny systemu Windows i upewnij się, że aplikacje serwera i klienta mają zgodę.
4. Zapoznaj się z samouczkiem [wdrażanie narzędzia do zarządzania](manage-resources-using-ui.md) , jeśli problem będzie się powtarzał i ponownie Wdróż narzędzie.

## <a name="error-job-with-specified-id-already-exists"></a>Błąd: zadanie o określonym IDENTYFIKATORze już istnieje

Jeśli użytkownik widzi komunikat o błędzie "zadanie o określonym IDENTYFIKATORze już istnieje", jest to spowodowane faktem, że nie podano unikatowej nazwy w parametrze "Nazwa aplikacji" podczas wdrażania szablonu.

Aby rozwiązać ten problem, należy ponownie wdrożyć narzędzie do zarządzania z wypełnionym parametrem "Nazwa aplikacji".

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Monit o opóźnioną zgodę podczas otwierania narzędzia do zarządzania

Po wdrożeniu narzędzia do zarządzania monit o zgodę może nie zostać otwarty od razu. Oznacza to, że ładowanie usługi Azure Web App trwa dłużej niż zwykle. Monit powinien zostać wyświetlony po zakończeniu ładowania sieci Web platformy Azure.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Użytkownik nie może wdrożyć narzędzia do zarządzania w regionie Wschodnie stany USA

Jeśli klient ustawi region na Wschodnie stany USA, nie może wdrożyć narzędzia do zarządzania.

Aby rozwiązać ten problem, wdróż narzędzie do zarządzania w innym regionie. Ponowne wdrożenie narzędzia w innym regionie nie ma wpływu na środowisko użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat śledzenia eskalacji w [temacie Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview-2019.md).
- Dowiedz się, jak zgłosić problemy z narzędziami pulpitu wirtualnego systemu Windows w [szablonach ARM dla usługi pulpitu zdalnego](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Aby dowiedzieć się, jak wdrożyć narzędzie do zarządzania, zobacz [wdrażanie narzędzia do zarządzania](manage-resources-using-ui.md).