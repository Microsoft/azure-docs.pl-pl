---
title: plik dołączania
description: plik dołączania
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947614"
---
## <a name="object-model"></a>Model obiektów

Tokeny dostępu użytkowników umożliwiają Autoryzowanie użytkowników aplikacji do bezpośredniego połączenia z zasobem usług Azure Communications Services. Te tokeny są generowane na serwerze, przekazywane z powrotem do aplikacji klienckiej, a następnie używane do inicjowania bibliotek klienckich usług komunikacyjnych. Aby to osiągnąć, należy utworzyć zaufany punkt końcowy usługi, który może uwierzytelniać użytkowników, i wystawiać tokeny dostępu użytkowników. Ta usługa powinna zachować trwałe mapowanie między tożsamościami użytkowników aplikacji i użytkownikami usług Azure Communications Services.

Klasa CommunicationIdentityClient zapewnia wszystkie funkcje związane z tokenem użytkownika i dostępu. Można utworzyć wystąpienie go przy użyciu parametrów połączenia, a następnie użyć go do zarządzania użytkownikami i wystawiania tokenów dostępu.

> [!WARNING]
> Tokeny są poufnymi danymi, ponieważ udzielają one dostępu do zasobów użytkownika. W związku z tym krytyczne jest zabezpieczenie tokenów przed naruszeniem zabezpieczeń. Należy utworzyć zaufany punkt końcowy usługi, który wystawia tylko tokeny dostępu autoryzowanym użytkownikom aplikacji. W przypadku buforowania tokenów dostępu użytkowników do magazynu zapasowego zdecydowanie zaleca się korzystanie z szyfrowania.

### <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Zakresy umożliwiają określenie dokładnej funkcjonalności usług Azure Communications Services, którą token dostępu użytkownika będzie mógł autoryzować. Zakresy są stosowane do tokenów dostępu poszczególnych użytkowników. Usługi komunikacyjne platformy Azure obsługują następujące zakresy tokenów dostępu użytkowników:

| Nazwa   | Opis                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Umożliwia uczestnictwo w rozmowie                                         |
| `VoIP` | Przyznaje możliwość wykonywania i odbierania wywołań VOIP przy użyciu biblioteki klienta wywołującego * |
| `Pstn` | Przyznaje możliwość nawiązywania połączeń PSTN przy użyciu biblioteki klienta wywołującego *           |

\* Funkcja nie jest jeszcze obsługiwana i będzie dostępna wkrótce

Jeśli chcesz usunąć dostęp użytkownika do określonych funkcji, należy najpierw [odwołać wszelkie istniejące tokeny dostępu](#revoke-user-access-tokens) , które mogą zawierać niepożądane zakresy przed wystawieniem nowego tokenu z bardziej ograniczonym zestawem zakresów.
