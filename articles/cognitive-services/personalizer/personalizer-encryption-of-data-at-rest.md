---
title: Szyfrowanie usługi dla danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Usługa personalizacji — szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 10eb627a340b45c93b2cfb2973e294d8d5d7c7e5
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307830"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi dla danych w spoczynku

Usługa personalizacji automatycznie szyfruje dane po utrwaleniu jej w chmurze. Szyfrowanie usługi personalizacji chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi personalizacji](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Personalizujer należy utworzyć nowy zasób narzędzia Personalizacja i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu personalizacji z warstwą cenową E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Klient usługi personalizacji — formularz żądania klucza zarządzanego](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
