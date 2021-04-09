---
title: Migruj profil Azure CDN ze standardu Verizon do Verizon Premium
description: Zapoznaj się ze szczegółami dotyczącymi migracji profilu ze standardu Verizon do Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778382"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrowanie profilu Azure CDN ze standardowego Verizon do warstwy Premium Verizon

Po utworzeniu profilu usługi Azure Content Delivery Network (CDN) w celu zarządzania punktami końcowymi Azure CDN oferuje cztery różne produkty, spośród których można wybierać. Aby uzyskać informacje o różnych produktach i ich dostępnych funkcjach, zobacz [porównanie Azure CDN funkcji produktu](cdn-features.md).

Jeśli utworzono **Standard Azure CDN z poziomu profilu Verizon** i korzystasz z niego do zarządzania punktami końcowymi usługi CDN, możesz uaktualnić go do **Azure CDN Premium z profilu Verizon** . Po uaktualnieniu punkty końcowe usługi CDN i wszystkie Twoje dane zostaną zachowane. 

> [!IMPORTANT]
> Po uaktualnieniu do **Azure CDN Premium z poziomu profilu Verizon** nie można później przekonwertować go z powrotem do **Azure CDN standardowego z profilu Verizon** .
> 

Aby uaktualnić **Standard Azure CDN z profilu Verizon** , skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porównanie profilu
**Azure CDN Premium from profile Verizon** mają następujące kluczowe różnice w stosunku do **Azure CDN standardowych z profilów Verizon** :
- W przypadku niektórych funkcji Azure CDN takich jak [kompresja](cdn-improve-performance.md), [reguły buforowania](cdn-caching-rules.md)i [filtrowanie geograficzne](cdn-restrict-access-by-country.md)nie można użyć interfejsu Azure CDN, należy użyć portalu Verizon za pomocą przycisku **Zarządzaj** .
- Interfejs API: w przeciwieństwie do standardu Verizon, nie można używać interfejsu API do kontrolowania tych funkcji, które są dostępne z poziomu portalu Premium Verizon. Można jednak użyć interfejsu API do sterowania innymi typowymi funkcjami, takimi jak tworzenie/usuwanie punktu końcowego, przeczyszczanie/ładowanie buforowanych zasobów oraz włączanie/wyłączanie domeny niestandardowej.
- Cennik: Verizon Premium ma inną strukturę cenową dla transferów danych niż standardowa Verizon. Aby uzyskać więcej informacji, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium from profile Verizon** mają następujące dodatkowe funkcje:
- [Uwierzytelnianie tokenu](cdn-token-auth.md): umożliwia użytkownikom uzyskiwanie i używanie tokenu w celu pobierania bezpiecznych zasobów.
- [Aparat reguł](./cdn-verizon-premium-rules-engine.md): umożliwia dostosowywanie sposobu obsługi żądań HTTP.
- Zaawansowane narzędzia analityczne:
   - [Szczegółowa analiza HTTP](cdn-advanced-http-reports.md)
   - [Analiza wydajności Edge](cdn-edge-performance.md)
   - [Analiza w czasie rzeczywistym](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o aparacie reguł, zobacz [Informacje o aparacie Azure CDN reguł](./cdn-verizon-premium-rules-engine-reference.md).