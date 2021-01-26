---
title: Usługa Azure Defender dla systemu DNS — zalety i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla systemu DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 957e39f7629337182c3e19a1a514c42883666301
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796998"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Wprowadzenie do usługi Azure Defender dla systemu DNS

[Azure DNS](../dns/dns-overview.md) to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Usługa Azure Defender dla systemu DNS zapewnia dodatkową warstwę ochrony zasobów w chmurze, wykonując następujące działania:

- ciągłe monitorowanie wszystkich zapytań DNS z zasobów platformy Azure
- Uruchamianie zaawansowanej analizy zabezpieczeń w celu powiadomienia o podejrzanych działaniach

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Wpisaną|**Usługa Azure Defender dla systemu DNS** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Jakie są korzyści z usługi Azure Defender dla systemu DNS?

Usługa Azure Defender dla systemu DNS chroni przed problemami, w tym:

- Eksfiltracji danych z zasobów platformy Azure przy użyciu tunelowania DNS
- Złośliwe oprogramowanie komunikujące się z serwerem C&C
- Komunikacja ze złośliwymi domenami jako phishing i wydobycie kryptograficzne
- Ataki DNS — komunikacja ze złośliwymi rozwiązaniami DNS 

Pełna lista alertów dostarczonych przez usługę Azure Defender dla systemu DNS znajduje się na [stronie odniesienia dotyczącej alertów](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Zależności

Usługa Azure Defender dla systemu DNS nie używa żadnych agentów. 

Aby chronić warstwę DNS, Włącz usługę Azure Defender dla systemu DNS dla każdej subskrypcji zgodnie z opisem w temacie [Włączanie usługi Azure Defender](security-center-pricing.md#enable-azure-defender).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla systemu DNS. W przypadku pokrewnego materiału zapoznaj się z następującym artykułem: 

- Alerty zabezpieczeń mogą być generowane przez Security Center lub odebrane przez Security Center z różnych produktów zabezpieczeń. Aby wyeksportować wszystkie te alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub innych zewnętrznych narzędzi, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Włączanie usługi Azure Defender](security-center-pricing.md#enable-azure-defender)