---
title: Wprowadzenie do Active Directory kont usług | Azure Active Directory
description: Wprowadzenie do typów kont usług w Active Directory oraz sposób ich zabezpieczania.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645620"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Wprowadzenie do Active Directory kont usług

Usługa ma podstawową tożsamość zabezpieczeń, która określa prawa dostępu do zasobów lokalnych i sieciowych. Kontekst zabezpieczeń dla usługi Microsoft Win32 jest określany na podstawie konta usługi, które jest używane do uruchamiania usługi. Konto usługi służy do:
* Identyfikowanie i uwierzytelnianie usługi
* pomyślnie uruchomiono usługę
* dostęp lub wykonywanie kodu lub aplikacji
* Rozpocznij proces. 

## <a name="types-of-on-premises-service-accounts"></a>Typy lokalnych kont usług

W oparciu o przypadek użycia możesz użyć zarządzanego konta usługi (MSA), konta komputera lub konta użytkownika, aby uruchomić usługę. Usługi muszą zostać przetestowane, aby upewnić się, że mogą korzystać z zarządzanego konta usługi. Jeśli to możliwe, należy użyć jednego z nich.

### <a name="group-msa-accounts"></a>Grupowanie kont MSA

Używaj [kont usług zarządzanych przez grupę](service-accounts-group-managed.md) (kont gMSA) zawsze, gdy jest to możliwe dla usług działających w środowisku lokalnym. Kont gMSA zapewniają pojedyncze rozwiązanie tożsamości dla usługi działającej w farmie serwerów lub za modułem równoważenia obciążenia sieciowego. Mogą być również używane w przypadku usługi uruchomionej na jednym serwerze. [Kont gMSA mają określone wymagania, które muszą zostać spełnione](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Autonomiczne konta MSA

Jeśli nie możesz użyć gMSA, użyj [autonomicznych zarządzanych kont usług](service-accounts-standalone-managed.md)(autonomiczne). sMSAs wymaga co najmniej systemu Windows Server 2008R2. W przeciwieństwie do kont gMSA, sMSAs działa tylko na jednym serwerze. Mogą one być używane dla wielu usług na tym serwerze.

### <a name="computer-account"></a>Konto komputera

Jeśli nie możesz użyć elementu MSA, zbadaj użycie [kont komputerów](service-accounts-computer.md). Konto LocalSystem jest wstępnie zdefiniowanym kontem lokalnym, które ma rozległe uprawnienia na komputerze lokalnym i działa jako tożsamość komputera w sieci.   
Usługi, które są uruchamiane jako konto LocalSystem, uzyskują dostęp do zasobu sieciowego przy użyciu poświadczeń konta komputera w formacie <domain_name>\<computer_name> .

NT NT\SYSTEM to wstępnie zdefiniowana nazwa konta LocalSystem. Może służyć do uruchomienia usługi i zapewnienia kontekstu zabezpieczeń dla tej usługi.

> [!NOTE]
> Gdy używane jest konto komputera, nie można określić, która usługa na komputerze używa tego konta i dlatego nie może przeprowadzić inspekcji usługi, która wprowadza zmiany. 

### <a name="user-account"></a>Konto użytkownika

Jeśli nie możesz użyć elementu MSA, zbadaj przy użyciu [kont użytkowników](service-accounts-user-on-premises.md). Konta użytkowników mogą być kontem użytkownika domeny lub kontem użytkownika lokalnego.

Konto użytkownika domeny umożliwia usłudze pełne korzystanie z funkcji zabezpieczeń usług systemu Windows i Microsoft Active Directory Domain Services. Usługa będzie mieć dostęp lokalny i sieciowy przyznany do konta. Będzie także mieć uprawnienia do wszystkich grup, których konto jest członkiem. Konta usług domeny obsługują wzajemne uwierzytelnianie Kerberos.

Konto użytkownika lokalnego (format nazwy: ".\UserName") istnieje tylko w bazie danych SAM komputera hosta; nie ma obiektu użytkownika w Active Directory Domain Services. Domena nie może uwierzytelnić konta lokalnego. W związku z tym Usługa uruchomiona w kontekście zabezpieczeń konta użytkownika lokalnego nie ma dostępu do zasobów sieciowych (z wyjątkiem użytkowników anonimowych). Usługi działające w kontekście użytkownika lokalnego nie mogą obsługiwać wzajemnego uwierzytelniania Kerberos, w którym usługa jest uwierzytelniana przez jej klientów. Z tego względu konta użytkowników lokalnych są zwykle nieodpowiedni dla usług z obsługą katalogów.

> [!IMPORTANT]
> Konta usług nie powinny należeć do żadnych grup uprzywilejowanych, ponieważ członkostwo w grupie uprzywilejowanych przyznaje uprawnienia, które mogą stanowić zagrożenie dla bezpieczeństwa. Każda usługa powinna dysponować własnym kontem usługi na potrzeby inspekcji i zabezpieczeń.

## <a name="choose-the-right-type-of-service-account"></a>Wybierz odpowiedni typ konta usługi


| Kryteria| gMSA| Autonomiczne| Konto komputera| Konto użytkownika |
| - | - | - | - | - |
| Aplikacja jest uruchamiana na pojedynczym serwerze| Tak| Tak. Użyj gMSA, jeśli to możliwe| Tak. Użyj elementu MSA, jeśli to możliwe| Tak. Jeśli to możliwe, użyj konta MSA. |
| Aplikacja jest uruchamiana na wielu serwerach| Tak| Nie| Nie. Konto jest powiązane z serwerem| Tak. Jeśli to możliwe, użyj konta MSA. |
| Aplikacje działające za modułami równoważenia obciążenia| Tak| Nie| Nie| Tak. Używaj tylko wtedy, gdy nie można użyć elementu gMSA |
| Aplikacja działa w systemie Windows Server 2008 R2| Nie| Tak| Tak. Jeśli to możliwe, użyj konta MSA.| Tak. Jeśli to możliwe, użyj konta MSA. |
| Działa w systemie Windows Server 2012| Tak| Tak. Użyj gMSA, jeśli to możliwe| Tak. Użyj MSA, jeśli to możliwe| Tak. Jeśli to możliwe, użyj konta MSA. |
| Wymagania dotyczące ograniczania konta usługi do jednego serwera| Nie| Tak| Tak. Użyj autonomiczne, jeśli to możliwe| Nie. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Korzystanie z dzienników serwera i programu PowerShell w celu zbadania

Za pomocą dzienników serwera można określić, które serwery i jak wiele serwerów ma działać aplikacja.

Można uruchomić następujące polecenie programu PowerShell, aby uzyskać listę wersji systemu Windows Server dla wszystkich serwerów w sieci. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Znajdowanie lokalnych kont usług

Zalecamy dodanie prefiksu, takiego jak "svc". Do wszystkich kont używanych jako konta usług. Ta konwencja nazewnictwa ułatwi ich znalezienie i zarządzanie. Należy również rozważyć użycie atrybutu Description dla konta usługi i właściciela konta usługi, może to być alias zespołu lub właściciel zespołu zabezpieczeń.

Znajdowanie lokalnych kont usług jest kluczem do zapewnienia bezpieczeństwa. I może być trudne dla kont innych niż MSA. Zalecamy przejrzenie wszystkich kont, które mają dostęp do ważnych zasobów lokalnych, i określenie, które komputery lub konta użytkowników mogą działać jako konta usług. Aby znaleźć konta, można również użyć poniższych metod.

* Artykuły dla każdego typu konta zawierają szczegółowe instrukcje dotyczące znajdowania tego typu konta. Aby uzyskać linki do tych artykułów, zobacz sekcję następne kroki w tym artykule.

## <a name="document-service-accounts"></a>Konta usług dokumentów

Po znalezieniu kont usług w środowisku lokalnym należy udokumentować następujące informacje dotyczące każdego konta. 

* Właściciel. Osoba odpowiedzialna za obsługę konta.

* Cel. Aplikacja, która reprezentuje konto, lub w innym celu. 

* Zakresy uprawnień. Jakie uprawnienia ma i czy ma? Co zrobić, jeśli dowolna Grupa jest członkiem?

* Profil ryzyka. Jakie jest ryzyko dla Twojej firmy w przypadku naruszenia zabezpieczeń tego konta? W przypadku wysokiego ryzyka Użyj elementu MSA.

* Przewidywany okres istnienia i okresowe zaświadczanie. Jak długo przewiduje się, że to konto jest aktywne? Jak często musi być przeglądany właściciel i zaświadczać o bieżąco?

* Zabezpieczenia haseł. W przypadku kont użytkowników i komputerów lokalnych, gdzie jest przechowywane hasło. Upewnij się, że hasła są bezpieczne i dokumenty, które mają dostęp. Rozważ użycie [Privileged Identity Management](../privileged-identity-management/pim-configure.md) do zabezpieczania przechowywanych haseł. 

  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania kont usługi

* [Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

* [Bezpieczne konta usług zarządzane przez grupę](service-accounts-group-managed.md)

* [Zabezpieczone autonomicznie zarządzane konta usług](service-accounts-standalone-managed.md)

* [Bezpieczne konta komputerów](service-accounts-computer.md)

* [Zabezpieczanie kont użytkowników](service-accounts-user-on-premises.md)

* [Zarządzanie kontami lokalnych usług](service-accounts-govern-on-premises.md)

