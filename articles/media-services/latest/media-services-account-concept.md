---
title: Zarządzanie kontami Azure Media Services v3 | Microsoft Docs
description: Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. W tym artykule wyjaśniono, jak zarządzać kontami Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980385"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Zarządzaj kontami Azure Media Services v3

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [konta magazynu](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Przeniesienie konta Media Services między subskrypcjami 

Jeśli musisz przenieść konto Media Services do nowej subskrypcji, musisz najpierw przenieść całą grupę zasobów, która zawiera konto Media Services do nowej subskrypcji. Należy przenieść wszystkie dołączone zasoby: konta usługi Azure Storage, profile Azure CDN itd. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Podobnie jak w przypadku wszystkich zasobów platformy Azure przenoszenie grup zasobów może zająć trochę czasu.

> [!NOTE]
> Media Services v3 obsługuje model z obsługą wielu dzierżawców.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Utwórz kopie zapasowe wszystkich danych na koncie przed migracją do innej subskrypcji.
* Należy zatrzymać wszystkie punkty końcowe przesyłania strumieniowego i zasoby przesyłania strumieniowego na żywo. Użytkownicy nie będą mogli uzyskać dostępu do zawartości na czas trwania przenoszenia grupy zasobów. 

> [!IMPORTANT]
> Nie uruchamiaj punktu końcowego przesyłania strumieniowego, dopóki przeniesienie nie zakończy się pomyślnie.

### <a name="troubleshoot"></a>Rozwiązywanie problemów 

Jeśli konto Media Services lub skojarzone konto usługi Azure Storage stanie się "Rozłączono" po przeniesieniu grupy zasobów, spróbuj obrócić klucze konta magazynu. Jeśli rotacja kluczy konta magazynu nie rozwiązuje stanu "odłączono" konta Media Services, należy wysłać nowe żądanie pomocy technicznej z menu "Pomoc techniczna i rozwiązywanie problemów" na koncie Media Services.  

## <a name="next-steps"></a>Następne kroki

[Tworzenie konta](create-account-cli-quickstart.md)
