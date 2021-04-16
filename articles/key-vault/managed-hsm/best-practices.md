---
title: Najlepsze rozwiązania dotyczące korzystania z zarządzanego Azure Key Vault HSM
description: W tym dokumencie wyjaśniono niektóre najlepsze rozwiązania dotyczące korzystania z Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482521"
---
# <a name="best-practices-when-using-managed-hsm"></a>Najlepsze rozwiązania dotyczące korzystania z zarządzanego modułu HSM

## <a name="control-access-to-your-managed-hsm"></a>Kontrola dostępu do zarządzanego modułu HSM

Zarządzany moduł HSM to usługa w chmurze, która zabezpiecza klucze szyfrowania. Ponieważ klucze te są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do zarządzanych modułów HSM, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten [artykuł](access-control.md) zawiera omówienie modelu dostępu. Wyjaśniono w nim uwierzytelnianie i autoryzację oraz kontrolę dostępu opartą na rolach.
- Utwórz [nową Azure Active Directory zabezpieczeń](../../active-directory/fundamentals/active-directory-manage-groups.md) dla administratorów modułu HSM (zamiast przypisywać rolę administratorów do poszczególnych osób). Zapobiegnie to zablokowaniu administracji w przypadku usunięcia poszczególnych kont.
- Blokowanie dostępu do grup zarządzania, subskrypcji, grup zasobów i zarządzanych modułów HSM — kontrola dostępu do grup zarządzania, subskrypcji i grup zasobów przy użyciu kontroli dostępu na platformie Azure
- Tworzenie przypisań ról kluczy przy użyciu lokalnej kontroli [RBAC zarządzanego modułu HSM.](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Aby zachować separację obowiązków, należy unikać przypisywania wielu ról do tych samych podmiotów zabezpieczeń. 
- Przypisz role przy użyciu jednostki dostępu z najmniejszymi uprawnieniami.
- Utwórz niestandardową definicję roli z dokładnym zestawem uprawnień.

## <a name="choose-regions-that-support-availability-zones"></a>Wybieranie regionów, które obsługują strefy dostępności

- Aby zapewnić najlepszą wysoką dostępność i odporność strefową, wybierz regiony platformy Azure, [Strefy dostępności](../../availability-zones/az-overview.md) są obsługiwane. Te regiony są wyświetlane jako "Zalecane regiony" w Azure Portal.

## <a name="backup"></a>Backup

- Upewnij się, że regularnie tworzyć kopie zapasowe modułu HSM. Kopie zapasowe można tworzyć na poziomie modułu HSM i dla określonych kluczy. 

## <a name="turn-on-logging"></a>Włączanie rejestrowania

- [Włącz rejestrowanie dla](logging.md) modułu HSM. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włączanie opcji odzyskiwania

- [Usuwanie nie softne](../general/soft-delete-overview.md) jest domyślnie włączone.
- Włącz ochronę przed przeczyszczaniem, jeśli chcesz chronić przed wymuszaniem usunięcia modułu HSM nawet po włączeniu usuwania nie softowego.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać [informacje o pełnej kopii zapasowej/przywracaniu](backup-restore.md) modułu HSM, zobacz Pełna kopia zapasowa/przywracanie.
- Zobacz [Rejestrowanie zarządzanego modułu HSM,](logging.md) aby dowiedzieć się, jak skonfigurować Azure Monitor modułu HSM
- Zobacz [Zarządzanie zarządzanymi kluczami HSM w](key-management.md) celu zarządzania kluczami.
- Zobacz [Zarządzanie rolami zarządzanego modułu HSM](role-management.md) w celu zarządzania przypisaniami ról.
