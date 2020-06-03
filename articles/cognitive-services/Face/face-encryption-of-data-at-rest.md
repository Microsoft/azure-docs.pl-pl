---
title: Szyfrowanie usługi do obsługi danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Szyfrowanie usługi z danymi magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309037"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi do obsługi danych w spoczynku

Usługa twarzy automatycznie szyfruje dane po utrwaleniu jej w chmurze. Szyfrowanie usługi programu Front Data chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą czołową należy utworzyć nowy zasób usługi i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu platformy z użyciem warstwy cenowej E0 można skonfigurować swoją tożsamość zarządzaną za pomocą Azure Key Vault.

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są obecnie dostępne w następujących regionach:

* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* Wschodnie stany USA
* US Gov Wirginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę usług, które obsługują CMK, zobacz [klucze zarządzane przez klienta dla Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formularz żądania klucza zarządzanego przez klienta Cognitive Services](https://aka.ms/cogsvc-cmk)
