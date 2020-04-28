---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75460498"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów usługi Azure BLOB i kolejek, rozpoczynając od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie przypisanie roli dotyczy wszystkich obiektów BLOB w kontenerze, a także właściwości kontenera i metadanych.
- **Poszczególne kolejki.** W tym zakresie przypisanie roli ma zastosowanie do komunikatów w kolejce, a także do właściwości i metadanych kolejki.
- **Konto magazynu.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów i ich obiektów blob, a także do wszystkich kolejek i ich komunikatów.
- **grupa zasobów.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów w subskrypcji.

> [!IMPORTANT]
> Jeśli subskrypcja obejmuje przestrzeń nazw usługi Azure datakostki, role należące do zakresu subskrypcji nie będą udzielać dostępu do danych obiektów blob i kolejek. Zamiast nich role zakresu z grupą zasobów, kontem magazynu lub kontenerem lub kolejką.     
