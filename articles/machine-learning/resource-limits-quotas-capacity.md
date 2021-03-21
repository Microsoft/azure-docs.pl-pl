---
title: Limity usługi
titleSuffix: Azure Machine Learning
description: Limity usługi używane do planowania pojemności oraz maksymalne limity żądań i odpowiedzi dla Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930807"
---
# <a name="service-limits-in-azure-machine-learning"></a>Limity usługi w Azure Machine Learning

W tej sekcji przedstawiono podstawowe limity przydziału i progi ograniczania w Azure Machine Learning. Aby dowiedzieć się, jak zwiększyć limity przydziału zasobów, zobacz ["Zarządzanie i zwiększanie limitów przydziału zasobów"](how-to-manage-quotas.md) .

## <a name="workspaces"></a>Obszary robocze
| Limit | Wartość |
| --- | --- |
| Nazwa obszaru roboczego | 2-32 znaków |

## <a name="runs"></a>Uruchamianie
| Limit | Wartość |
| --- | --- |
| Uruchomienia na obszar roboczy | 10 mln |
| RunId/ParentRunId | 256 znaków |
| DataContainerId | 261 znaków |
| Nazwa wyświetlana |256 znaków|
| Opis |5 000 znaków|
| Liczba właściwości |50 |
| Długość klucza właściwości |100 znaków |
| Długość wartości właściwości |1 000 znaków |
| Liczba tagów |50 |
| Długość klucza tagu |100 |
| Długość wartości tagu |1 000 znaków |
| CancelUri / CompleteUri / DiagnosticsUri |1 000 znaków |
| Długość komunikatu o błędzie |3 000 znaków |
| Długość komunikatu ostrzegawczego |300 znaków |
| Liczba wejściowych zestawów danych |200 |
| Liczba wyjściowych zestawów danych |20 |


## <a name="metrics"></a>Metryki
| Limit | Wartość |
| --- | --- |
| Nazwy metryk na przebieg |50|
| Wiersze metryk na nazwę metryki |10 mln|
| Kolumny na wiersz metryki |15|
| Długość nazwy kolumny metryk |255 znaków |
| Długość wartości kolumny metryk |255 znaków |
| Liczba wierszy metryki na partię przekazana | 250 |

> [!NOTE]
> Jeśli nastąpi przekroczenie limitu nazw metryk dla uruchomienia, ponieważ zmienne są formatowane na nazwę metryki, rozważ zamiast tego użyć metryki wiersza, gdzie jedna kolumna jest wartością zmiennej, a druga kolumna to wartość metryki.

## <a name="artifacts"></a>Artifacts

| Limit | Wartość |
| --- | --- |
| Liczba artefaktów na przebieg |10 mln|
| Maksymalna długość ścieżki artefaktu |5 000 znaków |

## <a name="limit-increases"></a>Limit wzrostu
Niektóre limity można zwiększyć dla poszczególnych obszarów roboczych, [kontaktując się z pomocą techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj środowisko Azure Machine Learning](how-to-configure-environment.md)
- Dowiedz się, jak zwiększyć limity przydziału zasobów w temacie ["Zarządzanie i zwiększanie limitów przydziału zasobów"](how-to-manage-quotas.md).

