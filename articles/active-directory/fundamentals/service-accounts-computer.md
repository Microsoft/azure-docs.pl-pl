---
title: Zabezpieczanie kont komputerów | Azure Active Directory
description: Przewodnik dotyczący zabezpieczania lokalnych kont komputerów.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417513"
---
# <a name="securing-computer-accounts"></a>Zabezpieczanie kont komputerów

Konto komputera lub LocalSystem to wbudowane, wysoce uprzywilejowane konto z dostępem do praktycznie wszystkich zasobów na komputerze lokalnym. To konto nie jest skojarzone z żadnym kontem zalogowanego użytkownika. Usługi działające jako LocalSystem uzyskują dostęp do zasobów sieciowych przez przesłanie poświadczeń komputera do serwerów zdalnych. Przedstawia poświadczenia w formularzu <domain_name>\<computer_name> $. Wstępnie zdefiniowana nazwa konta komputera to NT AUTHORITY\SYSTEM. Może służyć do uruchomienia usługi i zapewnienia kontekstu zabezpieczeń dla tej usługi.

![[Obraz 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Zalety korzystania z konta komputera

Konto komputera zapewnia następujące korzyści.

* **Nieograniczony dostęp lokalny**: konto komputera zapewnia pełny dostęp do zasobów lokalnych komputera.

* **Automatyczne zarządzanie hasłami**: konto komputera eliminuje konieczność ręcznego zmieniania haseł. Zamiast tego to konto jest członkiem Active Directory, a hasło konta zostanie zmienione automatycznie. Eliminuje to również konieczność zarejestrowania głównej nazwy usługi dla usługi.

* **Ograniczone prawa dostępu do komputera**: domyślna lista Access Control (ACL) w Active Directory Domain Services umożliwia minimalny dostęp do kont komputerów. Jeśli ta usługa miała być zaatakowana, może mieć ograniczony dostęp do zasobów w sieci.

## <a name="assess-security-posture-of-computer-accounts"></a>Ocena zabezpieczeń stan kont komputerów

Potencjalne wyzwania i powiązane środki zaradcze podczas korzystania z kont komputerów. 

| Problemy| Środki zaradcze |
| - | - |
| Konta komputerów podlegają usunięciu i wykorzystaniu, gdy komputer opuszcza i ponownie przystępuje do domeny.| Sprawdź, czy konieczne jest dodanie komputera do grupy usługi AD i sprawdź, które konto komputera zostało dodane do grupy przy użyciu przykładowych skryptów znajdujących się na tej stronie.| 
| Po dodaniu konta komputera do grupy wszystkie usługi działające jako LocalSystem na tym komputerze uzyskują prawa dostępu do grupy.| Być selektywne dla członkostwa w grupie konta komputera. Należy unikać tworzenia kont komputerów jako członków dowolnych grup administratorów domeny, ponieważ skojarzona usługa ma pełny dostęp do Active Directory Domain Services. |
| Nieprawidłowe wartości domyślne sieci dla systemu lokalnego| Nie należy zakładać, że konto komputera ma domyślny ograniczony dostęp do zasobów sieciowych. Zamiast tego należy uważnie sprawdzić członkostwa w grupach dla tego konta. |
| Nieznane usługi działające jako system lokalny| Upewnij się, że wszystkie usługi działające na koncie LocalSystem to usługi firmy Microsoft lub zaufane usługi pochodzące od stron trzecich. |


## <a name="find-services-running-under-the-computer-account"></a>Znajdź usługi działające w ramach konta komputera

Użyj następującego polecenia cmdlet programu PowerShell, aby znaleźć usługi działające w kontekście LocalSystem

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Znajdź konta komputerów, które są członkami określonej grupy**

Użyj następującego polecenia cmdlet programu PowerShell, aby znaleźć konta komputerów, które są członkami określonej grupy.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Znajdowanie komputerów należących do grup uprzywilejowanych**

Użyj następującego polecenia cmdlet programu PowerShell, aby znaleźć konta komputerów, które są członkami grup administratorów tożsamości (Administratorzy domeny, Administratorzy przedsiębiorstwa, Administratorzy)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Przenoszenie z kont komputerów

> [!IMPORTANT]
> Konta komputerów są kontami o wysokim poziomie uprawnień i powinny być używane tylko wtedy, gdy usługa wymaga nieograniczonego dostępu do zasobów lokalnych na komputerze i nie można użyć zarządzanego konta usługi (MSA).

* Skontaktuj się z właścicielem usługi, jeśli usługa może być uruchamiana przy użyciu konta MSA, a następnie użyj konto usługi zarządzane przez grupę (gMSA) lub Autonomiczne zarządzane konto usługi (autonomiczne), jeśli usługa obsługuje tę usługę.

* Użyj konta użytkownika domeny z tylko uprawnieniami potrzebnymi do uruchomienia usługi.

## <a name="next-steps"></a>Następne kroki 

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)

 

 
