---
title: Ciągła integracja i wdrażanie dla Azure Stream Analytics
description: Ten artykuł zawiera omówienie potoku ciągłej integracji i wdrażania (CI/CD) dla Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: b5057eb8c84e839f504060228986ea759c8bdc3d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123171"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Ciągła integracja i wdrażanie (CI/CD) dla Azure Stream Analytics

Zadanie Azure Stream Analytics można wdrożyć w sposób ciągły przy użyciu integracji kontroli źródła. Integracja kontroli źródła umożliwia przepływ pracy, w którym aktualizacja kodu wyzwala wdrożenie zasobów na platformie Azure. W tym artykule przedstawiono podstawowe kroki tworzenia potoku ciągłej integracji i wdrażania (CI/CD).

Jeśli dopiero zaczynasz Azure Stream Analytics, Rozpocznij pracę z [Azure Stream Analytics przewodnika Szybki Start](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania

Wykonaj kroki opisane w tym przewodniku, aby utworzyć potok ciągłej integracji/ciągłego wdrażania dla Stream Analytics.

1. Utwórz zapytanie Azure Stream Analytics.

   Używanie Azure Stream Analytics narzędzi dla [Visual Studio Code](./quick-create-visual-studio-code.md) lub [Visual Studio](stream-analytics-quick-create-vs.md) do [lokalnego tworzenia i testowania zapytań](develop-locally.md). Możesz także [wyeksportować istniejące zadanie](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) do projektu lokalnego.

2. Zatwierdź projekty Azure Stream Analytics do systemu kontroli źródła, takich jak repozytorium git.

3. Użyj [Azure Stream Analytics narzędzia Ci/CD](cicd-tools.md) do kompilowania projektów i generowania szablonów zarządzania zasobami platformy Azure dla wdrożenia.

4. Uruchamianie [zautomatyzowanych testów skryptów](cicd-tools.md#automated-test) do regresji jakościowej.

5. [Wdróż zadanie](cicd-tools.md#deploy-to-azure) automatycznie na platformie Azure.

## <a name="auto-build-test-and-deploy"></a>Autokompilowanie, testowanie i wdrażanie

Można użyć wiersza polecenia i [Azure Stream Analytics narzędzia](cicd-tools.md) ciągłej integracji, testowania i wdrażania. Możesz również skonfigurować potok ciągłej integracji/ciągłego wdrażania w [Azure Pipelines](set-up-cicd-pipeline.md). Azure Pipelines, aby włączyć bardziej zaawansowane możliwości, takie jak zarządzanie potokiem, Wizualizacja i wyzwalacze.

## <a name="next-steps"></a>Następne kroki

* [Automatyzowanie kompilacji, testów i wdrożeń zadania Azure Stream Analytics przy użyciu narzędzi CI/CD](cicd-tools.md)
* [Skonfiguruj potok ciągłej integracji/ciągłego wdrażania dla Stream Analytics zadania przy użyciu Azure Pipelines](set-up-cicd-pipeline.md)