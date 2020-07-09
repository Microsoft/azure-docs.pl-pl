---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024903"
---
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów usługi Azure BLOB i kolejek, rozpoczynając od najwęższego zakresu:

- **Pojedynczy kontener.** W tym zakresie przypisanie roli dotyczy wszystkich obiektów BLOB w kontenerze, a także właściwości kontenera i metadanych.
- **Poszczególne kolejki.** W tym zakresie przypisanie roli ma zastosowanie do komunikatów w kolejce, a także do właściwości i metadanych kolejki.
- **Konto magazynu.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów i ich obiektów blob, a także do wszystkich kolejek i ich komunikatów.
- **grupa zasobów.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu w grupie zasobów.
- **Subskrypcja.** W tym zakresie przypisanie roli dotyczy wszystkich kontenerów lub kolejek we wszystkich kontach magazynu we wszystkich grupach zasobów w subskrypcji.
