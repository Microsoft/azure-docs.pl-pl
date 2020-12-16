---
title: Role wbudowane Windows Virtual Desktop — Azure
description: Omówienie wbudowanych ról dla pulpitu wirtualnego systemu Windows dostępnego dla usługi Azure RBAC.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577701"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Wbudowane role dla pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows używa kontroli dostępu opartej na rolach (RBAC) na platformie Azure do przypisywania ról użytkownikom i administratorom. Role te dają administratorom uprawnienia do wykonywania określonych zadań. Aby dowiedzieć się więcej na temat ról wbudowanych dla usługi Azure RBAC, zobacz [role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md).

Standardowe role wbudowane dla platformy Azure to właściciel, współautor i czytelnik. Jednak pulpit wirtualny systemu Windows ma dodatkowe role umożliwiające oddzielenie ról zarządzania dla pul hostów, grup aplikacji i obszarów roboczych. Ta separacja umożliwia bardziej szczegółową kontrolę nad zadaniami administracyjnymi. Role te są zgodne ze standardowymi rolami platformy Azure i metodologią najniższych uprawnień.

Pulpit wirtualny systemu Windows nie ma określonej roli właściciela. Można jednak użyć standardowej roli właściciela dla obiektów usługi.

## <a name="desktop-virtualization-contributor"></a>Współautor wirtualizacji pulpitu

Rola współautor wirtualizacji pulpitu umożliwia zarządzanie wszystkimi aspektami wdrożenia. Nie ma jednak dostępu do zasobów obliczeniowych. Do publikowania grup aplikacji dla użytkowników lub grup użytkowników potrzebna będzie także rola Administrator dostępu użytkowników.


- Microsoft. DesktopVirtualization/\* 
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="desktop-virtualization-reader"></a>Czytnik wirtualizacji pulpitu

Rola czytelnika wirtualizacji pulpitu umożliwia wyświetlenie wszystkich elementów we wdrożeniu, ale nie pozwala na wprowadzanie żadnych zmian.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="host-pool-contributor"></a>Współautor puli hostów

Rola współautor puli hostów umożliwia zarządzanie wszystkimi aspektami pul hostów, w tym dostęp do zasobów. Do tworzenia maszyn wirtualnych potrzebna jest dodatkowa rola współautor, współautor maszyny wirtualnej. Aby utworzyć pulę hostów przy użyciu portalu, musisz mieć role AppGroup i współautor obszaru roboczego, a także użyć roli współautora wirtualizacji pulpitu.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="host-pool-reader"></a>Czytnik puli hostów

Rola czytelnik puli hostów umożliwia wyświetlenie wszystkiego w puli hostów, ale nie pozwala na wprowadzanie żadnych zmian.

- Microsoft. DesktopVirtualization/hostpools/ \* /Read
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="application-group-contributor"></a>Współautor grupy aplikacji

Rola współautor grupy aplikacji umożliwia zarządzanie wszystkimi aspektami grup aplikacji. Jeśli chcesz opublikować grupy aplikacji dla użytkowników lub grup użytkowników, musisz mieć rolę administratora dostępu użytkownika.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/odczyt
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="application-group-reader"></a>Czytelnik grupy aplikacji

Rola czytelnik grupy aplikacji umożliwia wyświetlenie wszystkich elementów w grupie aplikacji i nie pozwala na wprowadzanie żadnych zmian.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/odczyt
- Microsoft. DesktopVirtualization/hostpools/odczyt
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="workspace-contributor"></a>Współautor obszaru roboczego

Rola współautor obszaru roboczego umożliwia zarządzanie wszystkimi aspektami obszarów roboczych. Aby uzyskać informacje o aplikacjach dodanych do grup aplikacji, należy również przypisać rolę czytelnik grupy aplikacji.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/obszary robocze/\*
- Microsoft. DesktopVirtualization/applicationgroups/odczyt
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="workspace-reader"></a>Czytnik obszarów roboczych

Rola czytelnik obszaru roboczego umożliwia wyświetlenie wszystkich elementów w obszarze roboczym, ale nie pozwala na wprowadzanie żadnych zmian.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/obszary robocze/odczyt
- Microsoft. DesktopVirtualization/applicationgroups/odczyt
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="user-session-operator"></a>Operator sesji użytkownika

Rola operatora sesji użytkownika umożliwia wysyłanie komunikatów, Rozłączanie sesji i używanie funkcji wylogowywania do podpisywania sesji z hosta sesji. Jednak ta rola nie pozwala na wykonywanie zarządzania hostem sesji, takich jak usuwanie hosta sesji, zmiana trybu opróżniania i tak dalej. Ta rola może wyświetlać przypisania, ale nie może modyfikować administratorów. Zalecamy przypisanie tej roli do określonych pul hostów. W przypadku przyznania tego uprawnienia na poziomie grupy zasobów administrator będzie miał uprawnienie do odczytu wszystkich pul hostów w grupie zasobów.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/hostpools/odczyt
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*

## <a name="session-host-operator"></a>Operator hosta sesji

Rola współautor hosta sesji umożliwia wyświetlanie i usuwanie hostów sesji oraz tryb opróżniania. Nie mogą oni dodawać hostów sesji przy użyciu Azure Portal, ponieważ nie mają uprawnień do zapisu dla obiektów puli hostów. Jeśli token rejestracji jest prawidłowy (wygenerowany i niewygasły), można użyć tej roli do dodawania hostów sesji do puli hostów poza Azure Portal, jeśli administrator ma uprawnienia obliczeniowe za pomocą roli współautor maszyny wirtualnej.

Na poniższej liście opisano uprawnienia, do których ta rola ma dostęp:

- Microsoft. DesktopVirtualization/hostpools/odczyt
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft. resources/subscriptions/resourceGroups/Read
- Microsoft. resources/Deployments/Read
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. Support/\*
