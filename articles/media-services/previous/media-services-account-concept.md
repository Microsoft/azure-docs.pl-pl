---
title: Zarządzanie kontami Azure Media Services V2 | Microsoft Docs
description: Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. W tym artykule wyjaśniono, jak zarządzać kontami Azure Media Services V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 167028cda6962445373b22c5f3d0a9e56c3e608a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255414"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Zarządzanie kontami Azure Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Przeniesienie konta Media Services między subskrypcjami

Jeśli musisz przenieść konto Media Services do nowej subskrypcji, musisz najpierw przenieść całą grupę zasobów, która zawiera konto Media Services do nowej subskrypcji. Należy przenieść wszystkie dołączone zasoby: konta usługi Azure Storage, profile Azure CDN itd. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Podobnie jak w przypadku wszystkich zasobów platformy Azure przenoszenie grup zasobów może zająć trochę czasu.

Media Services V2 nie obsługuje modelu z obsługą wielu dzierżawców. Jeśli musisz przenieść konto Media Services do subskrypcji w nowej dzierżawie, Utwórz nową aplikację Azure Active Directory (Azure AD) w nowej dzierżawie. Następnie Przenieś konto do subskrypcji w nowej dzierżawie. Po zakończeniu przenoszenia dzierżawy możesz zacząć korzystać z aplikacji usługi Azure AD od nowej dzierżawy, aby uzyskać dostęp do konta Media Services przy użyciu interfejsów API v2.

> [!IMPORTANT]
> Należy zresetować informacje [uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md) w celu uzyskania dostępu do interfejsu API Media Services V2.
  
### <a name="considerations"></a>Zagadnienia do rozważenia

* Utwórz kopie zapasowe wszystkich danych na koncie przed migracją do innej subskrypcji.
* Należy zatrzymać wszystkie punkty końcowe przesyłania strumieniowego i zasoby przesyłania strumieniowego na żywo. Użytkownicy nie będą mogli uzyskać dostępu do zawartości na czas trwania przenoszenia grupy zasobów.

> [!IMPORTANT]
> Nie uruchamiaj punktu końcowego przesyłania strumieniowego, dopóki przeniesienie nie zakończy się pomyślnie.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli konto Media Services lub skojarzone konto usługi Azure Storage stanie się "Rozłączono" po przeniesieniu grupy zasobów, spróbuj obrócić klucze konta magazynu. Jeśli rotacja kluczy konta magazynu nie rozwiązuje stanu "odłączono" konta Media Services, należy wysłać nowe żądanie pomocy technicznej z menu "Pomoc techniczna i rozwiązywanie problemów" na koncie Media Services.  

## <a name="next-steps"></a>Następne kroki

[Tworzenie konta](media-services-portal-create-account.md)
