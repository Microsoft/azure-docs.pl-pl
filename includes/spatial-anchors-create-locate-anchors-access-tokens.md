---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182926"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodną metodą uwierzytelniania przy użyciu kotwic Azure. Szczególnie podczas przygotowywania aplikacji do wdrożenia produkcyjnego. Podsumowanie tego podejścia polega na skonfigurowaniu usługi zaplecza, z którą aplikacja kliencka może bezpiecznie uwierzytelniać. Usługa zaplecza z usługą AAD w czasie wykonywania oraz z usługą bezpiecznego tokenu zakotwiczenia platformy Azure, aby zażądać tokenu dostępu. Token ten jest następnie dostarczany do aplikacji klienckiej i używany w zestawie SDK do uwierzytelniania za pomocą zakotwiczeń przestrzennych platformy Azure.
