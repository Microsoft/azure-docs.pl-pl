---
title: Wdrażanie usługi synchronizacji magazynu
description: Wdrożenie zasobu chmury Azure File Sync, usługi synchronizacji magazynu. Wspólny blok tekstu współużytkowany w dokumentach migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143595"
---
W tym kroku wymagane są poświadczenia subskrypcji platformy Azure.

Podstawowym zasobem do skonfigurowania dla Azure File Sync jest nazywana *usługa synchronizacji magazynu*. Zalecamy wdrożenie tylko jednego serwera dla wszystkich serwerów, które synchronizują ten sam zestaw plików teraz lub w przyszłości. Utwórz wiele usług synchronizacji magazynu tylko wtedy, gdy masz różne zestawy serwerów, które nigdy nie muszą wymieniać danych (na przykład: Synchronizuj ten sam udział plików platformy Azure). W przeciwnym razie najlepszym rozwiązaniem jest usługa synchronizacji magazynu.

Wybierz region platformy Azure dla usługi synchronizacji magazynu znajdującej się blisko lokalizacji. Wszystkie inne zasoby w chmurze muszą zostać wdrożone w tym samym regionie.
Aby uprościć zarządzanie, należy utworzyć nową grupę zasobów w subskrypcji, w której zawarto zasoby synchronizacji i magazynu.

Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą wdrażania usługi synchronizacji magazynu](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) w artykule na temat wdrażania Azure File Sync. Wykonaj tylko tę część artykułu. W dalszych krokach znajdziesz linki do innych sekcji artykułu.