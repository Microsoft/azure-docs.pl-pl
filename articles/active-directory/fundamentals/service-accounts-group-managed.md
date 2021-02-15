---
title: Zabezpieczanie kont usług zarządzanych przez grupę | Azure Active Directory
description: Przewodnik dotyczący zabezpieczania kont komputerów usług zarządzanych przez grupę.
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
ms.openlocfilehash: 5c73bcd8fb4c6b594633abd1ac268bd8dfd78202
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417486"
---
# <a name="securing-group-managed-service-accounts"></a>Zabezpieczanie kont usług zarządzanych przez grupę

Konta usług zarządzane przez grupę (kont gMSA) są kontami domeny zarządzanej, które są używane na potrzeby zabezpieczania usług. Program kont gMSA może działać na jednym serwerze lub w farmie serwerów, na przykład w systemach za pomocą usługi Network Load Balancer (NLB) lub serwera Internet Information Services (IIS). Po skonfigurowaniu usług do korzystania z podmiotu zabezpieczeń gMSA zarządzanie hasłami dla tego konta jest obsługiwane przez system Windows.

## <a name="benefits-of-using-gmsas"></a>Zalety korzystania z usługi kont gMSA

Kont gMSA oferują jedno rozwiązanie do tworzenia tożsamości z większymi zabezpieczeniami, jednocześnie zmniejszając koszty administracyjne, wykonując następujące czynności:

* **Ustawianie silnych haseł**. Kont gMSA używają losowo generowanych haseł złożonych o 240 bajtów. Złożoność i długość haseł gMSA minimalizują prawdopodobieństwo naruszenia przez atak z wykorzystaniem rozdzielania lub słowników.

* **Regularne cykliczne hasła**. Kont gMSA zarządzanie hasłami w systemie Windows, co spowoduje zmianę hasła co 30 dni. Administratorzy usługi i domeny nie muszą już planować zmian haseł ani zarządzać awarią usługi, aby zachować bezpieczeństwo kont usług. 

* **Obsługa wdrażania w farmach serwerów**. Możliwość wdrażania kont gMSA na wielu serwerach umożliwia obsługę rozwiązań o zrównoważonym obciążeniu, w których wiele hostów działa w tej samej usłudze. 

* **Obsługa uproszczonego zarządzania główną nazwą serwera (SPN)**. Nazwę SPN można skonfigurować przy użyciu programu PowerShell w momencie tworzenia konta. Ponadto usługi obsługujące automatyczne rejestracje nazw SPN mogą to zrobić w odniesieniu do gMSA, pod warunkiem, że uprawnienia gMSA zostały prawidłowo ustawione. 

## <a name="when-to-use-gmsas"></a>Kiedy używać kont gMSA

Użyj kont gMSA jako preferowanego typu konta dla usług lokalnych, chyba że usługa, taka jak klaster trybu failover, nie obsługuje tej funkcji.

> [!IMPORTANT]
> Przed wdrożeniem w środowisku produkcyjnym należy przetestować usługę za pomocą usługi kont gMSA. W tym celu należy skonfigurować środowisko testowe i upewnić się, że aplikacja może korzystać z gMSA i uzyskiwać dostęp do zasobów potrzebnych do uzyskania dostępu. Aby uzyskać więcej informacji, zobacz [Obsługa kont usług zarządzanych przez grupę](https://docs.microsoft.com/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019).


Jeśli usługa nie obsługuje korzystania z programu kont gMSA, następnym najlepszym rozwiązaniem jest użycie autonomicznego zarządzanego konta usługi (autonomiczne). sMSAs zapewnia taką samą funkcjonalność jak gMSA, ale są przeznaczone do wdrożenia tylko na jednym serwerze.

Jeśli nie można użyć elementu gMSA lub autonomiczne obsługiwanego przez usługę, usługa musi być skonfigurowana do uruchamiania jako konto użytkownika standardowego. Administratorzy usług i domen muszą przestrzegać procesów zarządzania silnymi hasłami, aby zachować bezpieczeństwo konta.

## <a name="assess-the-security-posture-of-gmsas"></a>Oceń stan zabezpieczeń kont gMSA

Kont gMSA są z natury bezpieczniejsze niż konta użytkowników standardowych, które wymagają stałego zarządzania hasłami. Należy jednak wziąć pod uwagę kont gMSA "zasięg dostępu podczas przeszukiwania ogólnego stan zabezpieczeń.

W poniższej tabeli przedstawiono potencjalne problemy z zabezpieczeniami i środki zaradcze dotyczące korzystania z kont gMSA.

| Problemy z zabezpieczeniami| Środki zaradcze |
| - | - |
| gMSA jest członkiem uprzywilejowanych grup. | Przejrzyj członkostwo w grupach. W tym celu można utworzyć skrypt programu PowerShell umożliwiający Wyliczenie wszystkich członkostw w grupie, a następnie filtrowanie wynikowego pliku CSV według nazw plików gMSA. <br>Usuń gMSA z uprzywilejowanych grup.<br> Przyznaj gMSA tylko prawa i uprawnienia wymagane do uruchomienia usługi (skontaktuj się z dostawcą usługi). 
| gMSA ma dostęp do odczytu i zapisu do poufnych zasobów. | Inspekcja dostępu do poufnych zasobów. Archiwizuj dzienniki inspekcji w usłudze SIEM, na przykład platformę Azure Log Analytics lub platformę Azure, na potrzeby analizy. Usuń niepotrzebne uprawnienia do zasobów w przypadku wykrycia niepożądanego poziomu dostępu. |


## <a name="find-gmsas"></a>Znajdź kont gMSA

Twoja organizacja mogła już utworzyć kont gMSA. Uruchom następujące polecenie cmdlet programu PowerShell, aby pobrać te konta:

Aby efektywnie działać, kont gMSA musi znajdować się w jednostce organizacyjnej zarządzane konta usług (OU).

  
![Zrzut ekranu przedstawiający jednostkę organizacyjną zarządzanego konta usługi.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Aby znaleźć kont MSA usługi, która może nie istnieć, zobacz następujące polecenia.

**Aby znaleźć wszystkie konta usług, w tym kont gMSA i sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Zarządzanie kont gMSA

Do zarządzania kont gMSA można użyć Active Directory następujących poleceń cmdlet programu PowerShell:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Począwszy od systemu Windows Server 2012, polecenia cmdlet *-ADServiceAccount działają domyślnie z kont gMSA. Aby uzyskać więcej informacji na temat użycia powyższych poleceń cmdlet, zobacz [**wprowadzenie z kontami usług zarządzanymi przez grupę**](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Przenieś do gMSA
Kont gMSA to najbardziej bezpieczny typ konta usługi dla potrzeb lokalnych. Jeśli można przenieść się do jednego, należy. Dodatkowo Rozważ przeniesienie usług na platformę Azure i konta usług do usługi Azure Active Directory.

1.  Upewnij się, że [klucz główny KDS został wdrożony w lesie](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Jest to jednorazowa operacja.

2. [Utwórz nowy gMSA](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Zainstaluj nowy gMSA na każdym hoście z uruchomioną usługą.
   > [!NOTE] 
   > Aby uzyskać więcej informacji na temat tworzenia i instalowania gMSA na hoście przed skonfigurowaniem usługi do korzystania z gMSA, zobacz [wprowadzenie z kontami usług zarządzanymi przez grupę](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Zmień tożsamość usługi na gMSA i podaj puste hasło.

5. Sprawdź, czy usługa działa w ramach nowej tożsamości gMSA.

6. Usuń starą tożsamość konta usługi.

 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)
