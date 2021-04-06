---
title: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373758"
---
Przed przypisaniem roli RBAC platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres. Role RBAC platformy Azure zdefiniowane w szerszym zakresie są dziedziczone przez zasoby znajdujące się poniżej.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów usługi Azure BLOB i kolejek, rozpoczynając od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie przypisanie roli dotyczy wszystkich obiektów BLOB w kontenerze, a także właściwości kontenera i metadanych.
- **Poszczególne kolejki.** W tym zakresie przypisanie roli ma zastosowanie do komunikatów w kolejce, a także do właściwości i metadanych kolejki.
- **Konto magazynu.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów i ich obiektów blob, a także do wszystkich kolejek i ich komunikatów.
- **grupa zasobów.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów w subskrypcji.
- **Grupa zarządzania.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów we wszystkich subskrypcjach w grupie zarządzania.

Aby uzyskać więcej informacji na temat przypisań i zakresu ról platformy Azure, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../articles/role-based-access-control/overview.md).
