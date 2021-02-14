---
title: Zabezpieczanie kont usług opartych na użytkownikach | Azure Active Directory
description: Przewodnik dotyczący zabezpieczania lokalnych kont użytkowników.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417466"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Zabezpieczanie kont usług opartych na użytkownikach w Active Directory

Lokalne konta użytkowników to tradycyjne podejście do zabezpieczania usług działających w systemie Windows. Te konta mogą być używane jako ostatnie w przypadku, gdy usługa zarządza konta usług (kont gMSA) i Autonomiczne zarządzane konta usług (sMSAs). Zobacz Omówienie kont lokalnych usług, aby uzyskać informacje na temat wybierania najlepszego typu konta do użycia. Sprawdź również, czy możesz przenieść usługę do korzystania z konta usługi platformy Azure, takiego jak tożsamość zarządzana lub zasada usługi. 

Lokalne konta użytkowników można utworzyć w celu zapewnienia kontekstu zabezpieczeń usług i udzielania uprawnień wymaganych do uzyskiwania dostępu do zasobów lokalnych i sieciowych przez usługi. Wymagają one ręcznego zarządzania hasłami podobnie jak każde inne konto użytkownika Active Directory (AD). Administratorzy usług i domen muszą przestrzegać procesów zarządzania silnymi hasłami, aby zachować bezpieczeństwo tych kont.

Jeśli używasz konta użytkownika jako konta usługi, użyj go tylko dla jednej usługi. Nadaj jej nazwę w sposób, który oznacza, że jest to konto usługi i dla którego usługi. 

## <a name="benefits-and-challenges"></a>Zalety i wyzwania

Korzyści

Lokalne konta użytkowników to najbardziej uniwersalny typ konta do użycia z usługami. Konta użytkowników używane jako konta usług mogą być kontrolowane przez wszystkie zasady z normalnymi kontami użytkowników. Te dane są używane tylko wtedy, gdy nie można użyć elementu MSA. Należy również oszacować, czy konto komputera jest lepszym rozwiązaniem. 

Wyzwania z lokalnymi kontami użytkowników

W przypadku korzystania z lokalnych kont użytkowników są związane następujące wyzwania.

| Wyzwania| Środki zaradcze |
| - | - |
| Zarządzanie hasłami jest procesem ręcznym, który może prowadzić do słabego poziomu zabezpieczeń i usług.| Upewnij się, że złożoność hasła i zmiana hasła podlegają niezawodnemu procesowi, który zapewnia regularne aktualizacje przy użyciu silnego hasła. <br> Koordynuj zmianę hasła z aktualizacją haseł w usłudze, co spowoduje przestoje usługi. |
| Identyfikowanie lokalnych kont użytkowników, które działają jako konta usług, może być trudne.| Dokumenty i obsługa rekordów kont usług wdrożonych w danym środowisku. <br> Śledź nazwę konta i zasoby, do których są przypisane dostęp. <br> Rozważ dodanie prefiksu svc_ do wszystkich kont użytkowników używanych jako konta usług. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Znajdowanie lokalnych kont użytkowników używanych jako konta usług

Lokalne konta użytkowników są podobne do wszystkich innych kont użytkowników usługi AD. W związku z tym trudno znaleźć te konta, ponieważ nie istnieje pojedynczy atrybut konta użytkownika, które identyfikuje go jako konto usługi. 

Zalecamy utworzenie łatwej do zidentyfikowania konwencji nazewnictwa dla dowolnego konta użytkownika, które jest używane jako konto usługi.

Na przykład Dodaj "Service-" jako prefiks i nazwij usługę: "Service-HRDataConnector".

Możesz użyć niektórych spośród poniższych wskaźników, aby znaleźć te konta usług, ale może to nie znaleźć wszystkich kont.

* Konta zaufane na potrzeby delegowania.

* Konta z nazwami główną usługi.

* Konta, których hasło jest ustawione na nigdy nie wygasa.

Aby znaleźć lokalne konta użytkowników utworzone dla usług, można uruchomić następujące polecenia programu PowerShell.

### <a name="find-accounts-trusted-for-delegation"></a>Znajdź konta zaufane do delegowania

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Znajdowanie kont z nazwami zasad usługi

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Znajdź konta z hasłami ustawione na nigdy nie wygasają

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Możesz także przeprowadzić inspekcję dostępu do poufnych zasobów i zarchiwizować dzienniki inspekcji do systemu informacji o zabezpieczeniach i zarządzania zdarzeniami (SIEM). Korzystając z systemów takich jak Azure Log Analytics lub Azure, można wyszukiwać i analizować konta oraz usługi.

## <a name="assess-security-of-on-premises-user-accounts"></a>Ocena zabezpieczeń lokalnych kont użytkowników

Oceń zabezpieczenia lokalnych kont użytkowników używanych jako konta usług, korzystając z następujących kryteriów:

* Co to są zasady zarządzania hasłami?

* Czy konto jest członkiem wszystkich uprzywilejowanych grup?

* Czy konto ma dostęp do odczytu i zapisu do ważnych zasobów?

### <a name="mitigate-potential-security-issues"></a>Eliminowanie potencjalnych problemów z zabezpieczeniami

W poniższej tabeli przedstawiono potencjalne problemy z zabezpieczeniami oraz odpowiednie ograniczenia dotyczące kont użytkowników lokalnych.

| Problemy z zabezpieczeniami| Środki zaradcze |
| - | - |
| Zarządzanie hasłami|* Upewnij się, że złożoność hasła i zmiana hasła podlegają niezawodnemu procesowi, który zapewnia regularne aktualizacje z silnymi wymaganiami dotyczącymi hasła. <br> * Koordynuj zmianę hasła przy użyciu aktualizacji hasła, aby zminimalizować przestoje usługi. |
| Konto jest członkiem uprzywilejowanych grup.| Zapoznaj się z członkostwem w grupie. Usuń konto z uprzywilejowanych grup. Przyznaj kontu prawa i uprawnienia wymagane do uruchomienia usługi (skontaktuj się z dostawcą usług). Na przykład może być możliwe Odmowa logowania lokalnego lub Odmowa logowania interaktywnego. |
| Konto ma dostęp do odczytu i zapisu do poufnych zasobów.| Inspekcja dostępu do poufnych zasobów. Archiwizuj dzienniki inspekcji w usłudze SIEM (Azure Log Analytics lub na platformie Azure wskaźnikowej) na potrzeby analizy. Koryguj uprawnienia zasobów w przypadku wykrycia niepożądanego poziomu dostępu. |


## <a name="move-to-more-secure-account-types"></a>Przenieś do bardziej bezpiecznych typów kont

Firma Microsoft nie zaleca klientom używania lokalnych kont użytkowników jako kont usług. W przypadku każdej usługi korzystającej z tego typu konta należy ocenić, czy można zamiast tego skonfigurować użycie gMSA lub autonomiczne.

Ponadto należy oszacować, czy sama usługa może zostać przeniesiona na platformę Azure, aby można było używać bardziej bezpiecznych typów kont usługi. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)

 
