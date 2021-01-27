---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920476"
---
### <a name="provider-limits--quota"></a>Limity liczby dostawców &

Usługa Quantum platformy Azure obsługuje dostawców usług zarówno pierwszej, jak i innych firm. Dostawcy innych firm mają własne ograniczenia i limity przydziału. Użytkownicy mogą wyświetlać oferty i limity w Azure Portal podczas konfigurowania dostawców innych firm w bloku dostawcy. 

Opublikowane limity przydziału można znaleźć dla dostawcy rozwiązań optymalizacji pierwszej firmy firmy Microsoft poniżej. 

#### <a name="learn--develop-sku"></a>Informacje & opracowywania jednostki SKU

| Zasób | Limit |
| --- | --- |
| Współbieżne zadania oparte na procesorach CPU | do 5 współbieżnych zadań |
| Współbieżne zadania oparte na FPGA | maksymalnie 2 współbieżne zadania |
| Godziny korzystania z funkcji Solver na podstawie procesora | 20 godzin miesięcznie  |
| FPGA godziny korzystania z funkcji Solver | 1 godzina miesięcznie  |

Jeśli używasz uczenia & tworzenia jednostki SKU, **nie** możesz zażądać zwiększenia limitów przydziału. Zamiast tego należy przełączyć się na wydajność w skali SKU.

#### <a name="performance-at-scale-sku"></a>Wydajność w skali SKU

| Zasób | Limit |
| --- | --- |
| Współbieżne zadania oparte na procesorach CPU | do 100 zadań współbieżnych |
| Współbieżne zadania oparte na FPGA | do 10 współbieżnych zadań |
| Godziny korzystania z funkcji Solver | 50 000 godzin miesięcznie  |

Jeśli musisz zażądać zwiększenia limitu, skontaktuj się z pomocą techniczną platformy Azure. 

Więcej informacji można znaleźć na [stronie cennika usługi Azure Quantum](https://aka.ms/AQ/Pricing).
Aby uzyskać informacje na temat ofert innych firm, zapoznaj się z odpowiednią stroną dostawcy w Azure Portal.
