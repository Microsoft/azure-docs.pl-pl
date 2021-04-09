---
title: Wdrażanie modeli w środowisku produkcyjnym — zespołowe przetwarzanie danych
description: Jak wdrażać modele w środowisku produkcyjnym, dzięki czemu można odgrywać aktywną rolę w podejmowaniu decyzji biznesowe.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 89ea1e991df46b4e4d23305b6118980b80c2f917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321186"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Wdrażaj modele w środowisku produkcyjnym, aby odgrywać aktywną rolę w podejmowaniu decyzji handlowych

Wdrożenie produkcyjne umożliwia modelowi odtworzenie aktywnej roli w firmie. Przewidywania ze wdrożonego modelu mogą być używane na potrzeby podejmowania decyzji dla firmy.

## <a name="production-platforms"></a>Platformy produkcyjne

Istnieją różne podejścia i platformy umożliwiające umieszczanie modeli w środowisku produkcyjnym. Oto kilka opcji:

- [Gdzie można wdrażać modele przy użyciu Azure Machine Learning](../how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Serwer Microsoft Machine Learning](/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Przed wdrożeniem należy upewnić się, że opóźnienie oceny modelu jest wystarczająco małe, aby było używane w środowisku produkcyjnym.
>

>[!NOTE]
>Aby uzyskać wdrożenie przy użyciu Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Testowanie A/B:

Gdy wiele modeli jest w środowisku produkcyjnym, [testy A/B](https://en.wikipedia.org/wiki/A/B_testing) mogą służyć do porównywania wydajności modelu. 
 
## <a name="next-steps"></a>Następne kroki

Dostępne są również instruktaże pokazujące wszystkie kroki procesu dla **konkretnych scenariuszy** . Są one wyświetlane i połączone z opisami miniatur w artykule [przykładowe instruktaże](walkthroughs.md) . Ilustrują one sposób łączenia chmur, narzędzi lokalnych i usług w przepływ pracy lub potoku w celu utworzenia inteligentnej aplikacji.