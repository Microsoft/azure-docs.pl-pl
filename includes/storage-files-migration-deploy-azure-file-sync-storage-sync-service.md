---
title: Wdrażanie usługi synchronizacji magazynu
description: Wdróż zasób w chmurze Azure File Sync, usługę synchronizacji magazynu. Wspólny blok tekstu współużytkowany w dokumentach migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043169"
---
W tym kroku wymagane są poświadczenia subskrypcji platformy Azure.

Podstawowym zasobem do skonfigurowania dla Azure File Sync jest nazywana *usługa synchronizacji magazynu*. Zalecamy wdrożenie tylko jednego serwera dla wszystkich serwerów, które synchronizują ten sam zestaw plików teraz lub w przyszłości. Tworzenie wielu usług synchronizacji magazynu jest możliwe tylko wtedy, gdy istnieją różne zestawy serwerów, które nigdy nie muszą wymieniać danych. Na przykład może być serwerów, które nigdy nie synchronizują tego samego udziału plików platformy Azure. W przeciwnym razie najlepszym rozwiązaniem jest usługa synchronizacji magazynu.

Wybierz region platformy Azure dla usługi synchronizacji magazynu znajdującej się blisko lokalizacji. Wszystkie inne zasoby w chmurze muszą zostać wdrożone w tym samym regionie. Aby uprościć zarządzanie, należy utworzyć nową grupę zasobów w subskrypcji, w której zawarto zasoby synchronizacji i magazynu.

Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą wdrażania usługi synchronizacji magazynu](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) w artykule na temat wdrażania Azure File Sync. Wykonaj tylko tę część artykułu. W dalszych krokach znajdziesz linki do innych sekcji artykułu.