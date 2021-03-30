---
title: Najlepsze rozwiązania przy użyciu Azure Key Vault zarządzanego modułu HSM
description: W tym dokumencie wyjaśniono niektóre najlepsze rozwiązania dotyczące korzystania z Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90996853"
---
# <a name="best-practices-when-using-managed-hsm"></a>Najlepsze rozwiązania dotyczące korzystania z zarządzanego modułu HSM

## <a name="control-access-to-your-managed-hsm"></a>Kontrola dostępu do zarządzanego modułu HSM

Zarządzany moduł HSM to usługa w chmurze, która chroni klucze szyfrowania. Ponieważ te klucze są wrażliwe i krytyczne dla działania firmy, pamiętaj, aby zabezpieczyć dostęp do zarządzanego sprzętowych modułów zabezpieczeń, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten [artykuł](access-control.md) zawiera omówienie modelu dostępu. Objaśniono uwierzytelnianie i autoryzację oraz kontrolę dostępu opartą na rolach.
- Utwórz [Azure Active Directory grupę zabezpieczeń](../../active-directory/fundamentals/active-directory-manage-groups.md) dla administratorów modułu HSM (zamiast przypisywania roli administratora do poszczególnych użytkowników). Zapobiega to występowaniu "blokady administracyjnej" w przypadku usunięcia indywidualnego konta.
- Zablokuj dostęp do grup zarządzania, subskrypcji, grup zasobów i zarządzanych sprzętowych modułów zabezpieczeń — Użyj usługi Azure RBAC, aby kontrolować dostęp do grup zarządzania, subskrypcji i grup zasobów
- Tworzenie przypisań ról na klucz za pomocą [zarządzanego modułu HSM Local RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Przypisywanie ról przy użyciu najniższych uprawnień dostępu

## <a name="choose-regions-that-support-availability-zones"></a>Wybierz regiony obsługujące strefy dostępności

- Aby zapewnić najlepszą wysoką dostępność i odporność strefy, wybierz regiony platformy Azure, w których [strefy dostępności](../../availability-zones/az-overview.md) są obsługiwane. Te regiony są wyświetlane jako "zalecane regiony" w Azure Portal.

## <a name="backup"></a>Backup

- Upewnij się, że wykonywane są regularne kopie zapasowe modułu HSM. Kopie zapasowe można wykonywać na poziomie modułu HSM i dla określonych kluczy. 

## <a name="turn-on-logging"></a>Włącz rejestrowanie

- [Włącz rejestrowanie](logging.md) dla modułu HSM. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

- [Usuwanie nietrwałe](../general/soft-delete-overview.md) jest domyślnie włączone.
- Włącz ochronę przed przeczyszczeniem, jeśli chcesz chronić przed wymuszeniem usuwania modułu HSM nawet po włączeniu usuwania nietrwałego.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat pełnej kopii zapasowej/przywracania modułu HSM, zobacz [pełna kopia zapasowa/przywracanie](backup-restore.md) .
- Zobacz [Rejestrowanie zarządzanego modułu HSM](logging.md) , aby dowiedzieć się, jak skonfigurować rejestrowanie przy użyciu Azure monitor
- Zobacz [Zarządzanie](key-management.md) kluczami modułu HSM dla zarządzania kluczami.
- Zobacz [zarządzane zarządzanie rolami modułu HSM](role-management.md) do zarządzania przypisaniami ról.
