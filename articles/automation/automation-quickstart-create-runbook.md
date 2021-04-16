---
title: Przewodnik Szybki start platformy Azure — Tworzenie elementu runbook usługi Azure Automation | Microsoft Docs
description: Ten artykuł ułatwia wprowadzenie do tworzenia Azure Automation Runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: 151199d8389130b85e917a09fd8e8d7b5f56d90c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536550"
---
# <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu runbook usługi Azure Automation

Elementy runbook usługi Azure Automation można tworzyć za pośrednictwem platformy Azure. Ta metoda zapewnia oparty na przeglądarce interfejs użytkownika przeznaczony do tworzenia elementów runbook usługi Automation. W tym przewodniku Szybki start opisano tworzenie, edytowanie, testowanie i publikowanie elementu runbook programu PowerShell usługi Automation.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure na stronie https://portal.azure.com .

## <a name="create-the-runbook"></a>Tworzenie runbook

Najpierw utwórz element runbook. Przykładowy element runbook tworzony w tym przewodniku Szybki start domyślnie wyświetla tekst `Hello World`.

1. Otwórz konto usługi Automation.

1. Kliknij pozycję **Runbook w** obszarze **Automatyzacja procesów.** Zostanie wyświetlona lista elementów runbook.

1. Kliknij **pozycję Utwórz runbook** w górnej części listy.

1. Wprowadź wartość jako nazwę runbook w polu Nazwa, a następnie wybierz `Hello-World` pozycję **PowerShell** w polu **Typ runbook.**  

   ![Wprowadzanie na stronie informacji dotyczących elementu runbook usługi Automation](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kliknij pozycję **Utwórz**. Element runbook zostanie utworzony i zostanie otworzona strona Edytuj element runbook programu PowerShell.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie runbook programu PowerShell.":::

1. Wpisz lub skopiuj i wklej następujący kod do okienka edycji. Tworzy opcjonalny parametr wejściowy o nazwie z domyślną wartością i generuje ciąg, który `Name` `World` używa tej wartości wejściowej:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kliknij **przycisk Zapisz,** aby zapisać kopię roboczą runbook.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie programu PowerShell Runbook z przykładowym kodem w oknie po prawej stronie.":::

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Po utworzeniu tego runbooka należy go przetestować, aby sprawdzić, czy działa.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

1. Wprowadź wartość w polu **Nazwa** i kliknij pozycję **Uruchom**. Zadanie testowania zostanie uruchomione oraz zostaną wyświetlone stan zadania i danej wyjściowe.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Zrzut ekranu przedstawiający okienko Test z przykładową wartością w polu nazwy.":::

1. Zamknij okienko Test, klikając ikonę **X** w prawym górnym rogu. Wybierz przycisk **OK** w wyświetlonym oknie podręcznym.

1. Na stronie Edytuj element runbook programu PowerShell kliknij pozycję **Publikuj**, aby opublikować element runbook jako oficjalną wersję elementu runboook na koncie.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie element runbook programu PowerShell z wybranym przyciskiem Publikuj.":::

## <a name="run-the-runbook"></a>Uruchamianie elementu runbook

Po opublikowaniu elementu runbook zostanie wyświetlona strona przeglądu.

1. Na stronie przeglądu elementu runbook kliknij pozycję **Uruchom**, aby otworzyć stronę konfiguracji Uruchamianie elementu Runbook dla tego elementu runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Zrzut ekranu przedstawiający stronę uruchamiania konfiguracji runbook.":::

1. Zostaw pole **Nazwa** puste, aby została użyta wartość domyślna, i kliknij przycisk **OK**. Zadanie runbook zostanie przesłane i zostanie wyświetlona strona Zadanie.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Zrzut ekranu przedstawiający stronę Zadania z wybranym przyciskiem Dane wyjściowe.":::

1. Gdy stan zadania to `Running` lub , kliknij pozycję Dane wyjściowe, aby otworzyć okienko Dane wyjściowe i wyświetlić dane wyjściowe `Completed` runbook. 

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Zrzut ekranu przedstawiający okienko Dane wyjściowe z danych wyjściowych runbook.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, edytowano, przetestowano i opublikowano element runbook oraz uruchomiono zadanie elementu runbook. Aby dowiedzieć się więcej o elementach runbook usługi Automation, przejdź do artykułu opisującego różne typy elementów runbook, które można tworzyć i z nich korzystać w usłudze Automation.

> [!div class="nextstepaction"]
> [Azure Automation typów elementów Runbook](./automation-runbook-types.md)
