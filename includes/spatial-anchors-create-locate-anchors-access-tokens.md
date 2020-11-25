---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993024"
---
### <a name="access-tokens"></a>Tokeny dostępu

Tokeny dostępu są bardziej niezawodną metodą uwierzytelniania przy użyciu kotwic Azure. Szczególnie podczas przygotowywania aplikacji do wdrożenia produkcyjnego. Podsumowanie tego podejścia polega na skonfigurowaniu usługi zaplecza, z którą aplikacja kliencka może bezpiecznie uwierzytelniać. Usługa zaplecza z usługą AAD w czasie wykonywania oraz z usługą bezpiecznego tokenu zakotwiczenia platformy Azure, aby zażądać tokenu dostępu. Token ten jest następnie dostarczany do aplikacji klienckiej i używany w zestawie SDK do uwierzytelniania za pomocą zakotwiczeń przestrzennych platformy Azure.
