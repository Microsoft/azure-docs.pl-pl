---
title: Usługa aparat rozpoznawania formularzy szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane przez aparat rozpoznawania formularzy oraz sposób włączania CMK i zarządzania nim.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 86b15b0059c2e3466ef65daeb53780798b3882d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079255"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Szyfruj aparat rozpoznawania danych w czasie spoczynku

Aparat rozpoznawania formularzy platformy Azure automatycznie szyfruje dane po utrwaleniu ich w chmurze. Szyfrowanie aparatu rozpoznawania formularzy chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 11 maja, 2020. Aby używać CMK z aparatem rozpoznawania formularzy, należy utworzyć nowy zasób aparatu rozpoznawania formularza. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Następne kroki

* [Aparat rozpoznawania formularzy formularz żądania klucza zarządzanego przez klienta](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)