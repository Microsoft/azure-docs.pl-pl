---
title: Szyfrowanie usługi do obsługi danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule omówiono szyfrowanie danych w spoczynku i sposób włączania CMK oraz zarządzania nimi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650817"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi do obsługi danych w spoczynku

Usługa twarzy automatycznie szyfruje dane po utrwaleniu w chmurze. Szyfrowanie usługi programu Front Data chroni dane i pomaga sprostać wymaganiom dotyczącym zabezpieczeń i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [Customer-Managed formularz żądania klucza usługi](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą czołową należy utworzyć nowy zasób usługi i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu platformy z użyciem warstwy cenowej E0 można skonfigurować swoją tożsamość zarządzaną za pomocą Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę usług, które obsługują CMK, zobacz [klucze zarządzane przez klienta dla Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Co to jest Azure Key Vault](../../key-vault/general/overview.md)?
* [Formularz żądania klucza Customer-Managed Cognitive Services](https://aka.ms/cogsvc-cmk)
