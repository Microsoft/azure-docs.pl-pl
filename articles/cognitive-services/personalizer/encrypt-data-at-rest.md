---
title: Szyfrowanie usługi dla danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane dla narzędzia Personalizacja oraz sposób włączania CMK i zarządzania nim.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524500"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Szyfrowanie usługi dla danych w spoczynku

Usługa personalizacji automatycznie szyfruje dane po utrwaleniu jej w chmurze. Szyfrowanie usługi personalizacji chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [Customer-Managed formularz żądania klucza](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Personalizujer należy utworzyć nowy zasób narzędzia Personalizacja i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu personalizacji z warstwą cenową E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza Customer-Managed usługi personalizacji](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](../../key-vault/general/overview.md)