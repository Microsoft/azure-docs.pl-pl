---
title: Content Moderator szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Content Moderator szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 38fc21ee45db25f015a6b8b534b0d922efa636f2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310550"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator szyfrowanie danych magazynowanych

Content Moderator automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta Content moderator](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Content Moderator należy utworzyć nowy zasób Content Moderator i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu Content Moderator z użyciem warstwy cenowej E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Włącz szyfrowanie danych dla zespołu Content Moderatorowego

Aby włączyć szyfrowanie danych dla zespołu przeglądów Content Moderator, zobacz [Przewodnik Szybki Start: Try Content moderator w sieci Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Musisz podać _Identyfikator zasobu_ za pomocą warstwy cenowej Content moderator E0.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę usług, które obsługują CMK, zobacz [klucze zarządzane przez klienta dla Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formularz żądania klucza zarządzanego przez klienta Cognitive Services](https://aka.ms/cogsvc-cmk)

