---
title: Kopiowanie lub tworzenie kopii zapasowych zadań Azure Stream Analytics
description: W tym artykule opisano sposób kopiowania lub tworzenia kopii zapasowej zadania Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: e0214cb9515a5974f1de9a27760dc037f7593f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892312"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopiowanie lub tworzenie kopii zapasowych zadań Azure Stream Analytics

Możesz kopiować i tworzyć kopie zapasowe wdrożonych zadań Azure Stream Analytics przy użyciu Visual Studio Code lub programu Visual Studio. Kopiowanie zadania do innego regionu nie powoduje skopiowania ostatniego czasu wyjściowego. W związku z tym nie można użyć opcji [**Ostatnia zatrzymana**](https://docs.microsoft.com/azure/stream-analytics/start-job#start-options) podczas uruchamiania skopiowanego zadania.

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Zainstaluj [rozszerzenie Azure Stream Analytics dla narzędzi Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) i [Azure Stream Analytics dla programu Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Kliknij ikonę **platformy Azure** na pasku działania Visual Studio Code a następnie rozwiń węzeł **Stream Analytics** . Zadania powinny być wyświetlane w ramach subskrypcji.

   ![Otwórz Eksploratora Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Aby wyeksportować zadanie do projektu lokalnego, zlokalizuj zadanie, które chcesz wyeksportować w **eksploratorze Stream Analytics** w Visual Studio Code. Następnie wybierz folder dla projektu.

    ![Znajdź zadanie ASA w Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Projekt zostanie wyeksportowany do folderu, który został wybrany i dodany do bieżącego obszaru roboczego.

    ![Eksportowanie zadania ASA w Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Aby opublikować zadanie w innym regionie lub utworzyć kopię zapasową przy użyciu innej nazwy, wybierz pozycję **Wybierz z subskrypcji do opublikowania** w edytorze zapytań ( \* . asaql) i postępuj zgodnie z instrukcjami.

    ![Publikowanie na platformie Azure w Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Wykonaj [zadanie Eksportuj wdrożone Azure Stream Analytics do instrukcji projektu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project).

2. Otwórz \* plik. asaql w edytorze zapytań, wybierz pozycję **Prześlij do platformy Azure** w Edytorze skryptów i postępuj zgodnie z instrukcjami, aby opublikować zadanie w innym regionie lub kopii zapasowej przy użyciu nowej nazwy.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Visual Studio Code](quick-create-visual-studio-code.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
