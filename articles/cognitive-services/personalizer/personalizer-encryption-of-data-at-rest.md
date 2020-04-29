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
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071090"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi dla danych w spoczynku

Usługa personalizacji automatycznie szyfruje dane po utrwaleniu jej w chmurze. Szyfrowanie usługi personalizacji chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta usługi personalizacji](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Personalizujer należy utworzyć nowy zasób narzędzia Personalizacja i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu personalizacji z warstwą cenową E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Następne kroki

* [Klient usługi personalizacji — formularz żądania klucza zarządzanego](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
