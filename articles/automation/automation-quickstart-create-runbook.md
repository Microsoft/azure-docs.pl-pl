---
title: Przewodnik Szybki start platformy Azure — Tworzenie elementu runbook usługi Azure Automation | Microsoft Docs
description: Ten artykuł ułatwia rozpoczęcie tworzenia Azure Automation elementu Runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 0717a7ac3cc663ff68ba96864aa5d37732337ca5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836740"
---
# <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu runbook usługi Azure Automation

Elementy runbook usługi Azure Automation można tworzyć za pośrednictwem platformy Azure. Ta metoda zapewnia oparty na przeglądarce interfejs użytkownika przeznaczony do tworzenia elementów runbook usługi Automation. W tym przewodniku Szybki start opisano tworzenie, edytowanie, testowanie i publikowanie elementu runbook programu PowerShell usługi Automation.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="create-the-runbook"></a>Tworzenie elementu Runbook

Najpierw utwórz element runbook. Przykładowy element runbook tworzony w tym przewodniku Szybki start domyślnie wyświetla tekst `Hello World`.

1. Otwórz konto usługi Automation.

1. Kliknij pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**. Zostanie wyświetlona lista elementów runbook.

1. Kliknij pozycję **Utwórz element Runbook** w górnej części listy.

1. Wprowadź `Hello-World` nazwę elementu Runbook w polu **Nazwa** , a następnie wybierz opcję **PowerShell** dla pola **Typ elementu Runbook** . 

   ![Wprowadzanie na stronie informacji dotyczących elementu runbook usługi Automation](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kliknij przycisk **Utwórz**. Element runbook zostanie utworzony i zostanie otworzona strona Edytuj element runbook programu PowerShell.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Wpisz lub skopiuj i wklej następujący kod do okienka edycji. Powoduje utworzenie opcjonalnego parametru wejściowego o nazwie `Name` domyślnej wartości `World` i wyjście ciągu, który używa tej wartości wejściowej:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kliknij przycisk **Zapisz** , aby zapisać kopię roboczą elementu Runbook.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Po utworzeniu elementu Runbook należy przetestować element Runbook, aby sprawdzić, czy działa.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

1. Wprowadź wartość w polu **Nazwa** i kliknij pozycję **Uruchom**. Zadanie testowania zostanie uruchomione oraz zostaną wyświetlone stan zadania i danej wyjściowe.

    ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Zamknij okienko testowania, klikając **symbol X** w prawym górnym rogu. Wybierz przycisk **OK** w wyświetlonym oknie podręcznym.

1. Na stronie Edytuj element runbook programu PowerShell kliknij pozycję **Publikuj**, aby opublikować element runbook jako oficjalną wersję elementu runboook na koncie.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Uruchamianie elementu runbook

Po opublikowaniu elementu runbook zostanie wyświetlona strona przeglądu.

1. Na stronie przeglądu elementu runbook kliknij pozycję **Uruchom**, aby otworzyć stronę konfiguracji Uruchamianie elementu Runbook dla tego elementu runbook.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Zostaw pole **Nazwa** puste, aby została użyta wartość domyślna, i kliknij przycisk **OK**. Zadanie elementu Runbook zostało przesłane i zostanie wyświetlona strona zadanie.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Gdy zadanie ma stan `Running` lub `Completed` , kliknij pozycję **dane wyjściowe** , aby otworzyć okienko dane wyjściowe i wyświetlić dane wyjściowe elementu Runbook.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, edytowano, przetestowano i opublikowano element runbook oraz uruchomiono zadanie elementu runbook. Aby dowiedzieć się więcej o elementach runbook usługi Automation, przejdź do artykułu opisującego różne typy elementów runbook, które można tworzyć i z nich korzystać w usłudze Automation.

> [!div class="nextstepaction"]
> [Azure Automation typy elementów Runbook](./automation-runbook-types.md)
