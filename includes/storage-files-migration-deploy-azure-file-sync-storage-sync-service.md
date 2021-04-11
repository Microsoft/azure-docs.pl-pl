---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075599"
---
W tym kroku wymagane są poświadczenia subskrypcji platformy Azure.

Podstawowym zasobem do skonfigurowania dla Azure File Sync jest nazywana *usługa synchronizacji magazynu*. Zalecamy wdrożenie tylko jednego serwera dla wszystkich serwerów, które synchronizują ten sam zestaw plików teraz lub w przyszłości. Tworzenie wielu usług synchronizacji magazynu jest możliwe tylko wtedy, gdy istnieją różne zestawy serwerów, które nigdy nie muszą wymieniać danych. Na przykład może być serwerów, które nigdy nie synchronizują tego samego udziału plików platformy Azure. W przeciwnym razie najlepszym rozwiązaniem jest usługa synchronizacji magazynu.

Wybierz region platformy Azure dla usługi synchronizacji magazynu znajdującej się blisko lokalizacji. Wszystkie inne zasoby w chmurze muszą zostać wdrożone w tym samym regionie. Aby uprościć zarządzanie, należy utworzyć nową grupę zasobów w subskrypcji, w której zawarto zasoby synchronizacji i magazynu.

Aby uzyskać więcej informacji, zobacz [sekcję dotyczącą wdrażania usługi synchronizacji magazynu](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) w artykule na temat wdrażania Azure File Sync. Wykonaj tylko tę część artykułu. W dalszych krokach znajdziesz linki do innych sekcji artykułu.