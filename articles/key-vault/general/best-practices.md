---
title: Najlepsze rozwiązania dotyczące korzystania z Key Vault Azure Key Vault | Microsoft Docs
description: W tym dokumencie wyjaśniono niektóre najlepsze rozwiązania dotyczące korzystania z Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2a81d28998c531339a1b719e37e4b90004157edf
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005846"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania w zakresie używania Key Vault

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten [artykuł](secure-your-key-vault.md) zawiera omówienie modelu dostępu Key Vault. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie dotyczące kontroli dostępu do magazynu są następujące:
1. Zablokuj dostęp do subskrypcji, grupy zasobów i magazynów kluczy (RBAC)
2. Utwórz zasady dostępu dla każdego magazynu
3. Użyj najmniejszej nazwy podmiotu zabezpieczeń dostępu, aby udzielić dostępu
4. Włącz funkcję Zapora i [punkty końcowe usługi sieci wirtualnej](overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Użyj oddzielnego Key Vault

Nasze zalecenie polega na użyciu magazynu dla każdej aplikacji na środowisko (projektowanie, przedprodukcyjne i produkcyjne). Ułatwia to nie udostępnianie wpisów tajnych w różnych środowiskach i zmniejsza zagrożenie w przypadku naruszenia.

## <a name="backup"></a>Backup

Zadbaj o to, aby regularnie korzystać z magazynu w celu aktualizowania/usuwania/tworzenia obiektów w magazynie.

## <a name="turn-on-logging"></a>Włącz rejestrowanie

[Włącz rejestrowanie](logging.md) dla swojego magazynu. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

1. Włącz [usuwanie nietrwałe](overview-soft-delete.md).
2. Włącz ochronę przed przeczyszczeniem, jeśli chcesz chronić przed usunięciem wpisu tajnego lub magazynu, nawet po włączeniu usuwania nietrwałego.
