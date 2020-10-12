---
title: plik dołączany
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534140"
---
Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów usługi Azure BLOB i kolejek, rozpoczynając od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie przypisanie roli dotyczy wszystkich obiektów BLOB w kontenerze, a także właściwości kontenera i metadanych.
- **Poszczególne kolejki.** W tym zakresie przypisanie roli ma zastosowanie do komunikatów w kolejce, a także do właściwości i metadanych kolejki.
- **Konto magazynu.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów i ich obiektów blob, a także do wszystkich kolejek i ich komunikatów.
- **grupa zasobów.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów w subskrypcji.
- **Grupa zarządzania.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów we wszystkich subskrypcjach w grupie zarządzania.

Aby uzyskać więcej informacji na temat przypisań i zakresu ról platformy Azure, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../articles/role-based-access-control/overview.md).
