---
title: Limity usługi
titleSuffix: Azure Digital Twins
description: Wykres przedstawiający limity Azure Digital Twins usługi.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728040"
---
# <a name="azure-digital-twins-service-limits"></a>Azure Digital Twins limity usługi

Są to limity usług Azure Digital Twins.

> [!NOTE]
> Niektóre obszary tej usługi mają dostosowywane limity. Jest on reprezentowany w poniższych tabelach z *kolumną Można* dostosowywać?. Gdy można dostosować limit, wartość *Dostosowywane?* ma wartość *Tak.*
>
> Jeśli Twoja firma wymaga podniesienia dostosowywanego limitu lub limitu przydziału powyżej domyślnego limitu, możesz zażądać dodatkowych zasobów, [otwierając bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Limity według typu

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Praca z limitami

Po osiągnięciu limitu usługa ogranicza dodatkowe żądania. Spowoduje to odpowiedź błędu 429 z tych żądań.

Aby nimi zarządzać, poniżej znajdują się zalecenia dotyczące pracy z limitami.
* **Użyj logiki ponawiania prób.** Zestawy [AZURE DIGITAL TWINS implementują](how-to-use-apis-sdks.md) logikę ponawiania dla żądań, które zakończyły się niepowodzeniem, więc jeśli pracujesz z dostarczonym zestawem SDK, jest to już wbudowane. W przeciwnym razie rozważ zaimplementowanie logiki ponawiania prób we własnej aplikacji. Usługa wysyła z powrotem nagłówek w odpowiedzi na błąd, którego można użyć do określenia, jak długo należy `Retry-After` czekać przed ponowieniem próby.
* **Użyj progów i powiadomień, aby ostrzec o zbliżaniu się do limitów.** Niektóre limity usługi dla Azure Digital Twins mają odpowiednie [metryki,](troubleshoot-metrics.md) których można użyć do śledzenia użycia w tych obszarach. Aby skonfigurować progi i skonfigurować alert dla dowolnej metryki w przypadku zbliżania się progu, zobacz instrukcje w tece [*Rozwiązywanie problemów: Konfigurowanie alertów*](troubleshoot-alerts.md). Aby skonfigurować powiadomienia dla innych limitów, w przypadku których metryki nie są dostarczane, rozważ zaimplementowanie tej logiki we własnym kodzie aplikacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat bieżącej wersji Azure Digital Twins w przeglądzie usługi:
* [*Omówienie: Co to jest Azure Digital Twins?*](overview.md)
