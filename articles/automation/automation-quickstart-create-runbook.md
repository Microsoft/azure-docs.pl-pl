---
title: Przewodnik Szybki start platformy Azure — Tworzenie elementu runbook usługi Azure Automation | Microsoft Docs
description: Dowiedz się, jak utworzyć element runbook usługi Azure Automation
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 250f51c9f028dd55d8327259e35b82b0c392c1f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537002"
---
# <a name="create-an-azure-automation-runbook"></a>Tworzenie elementu runbook usługi Azure Automation

Elementy runbook usługi Azure Automation można tworzyć za pośrednictwem platformy Azure. Ta metoda zapewnia oparty na przeglądarce interfejs użytkownika przeznaczony do tworzenia elementów runbook usługi Automation. W tym przewodniku Szybki start opisano tworzenie, edytowanie, testowanie i publikowanie elementu runbook programu PowerShell usługi Automation.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się na https://portal.azure.complatformie Azure na poziomie .

## <a name="create-the-runbook"></a>Tworzenie śmiętu

Najpierw utwórz element runbook. Przykładowy element runbook tworzony w tym przewodniku Szybki start domyślnie wyświetla tekst `Hello World`.

1. Otwórz konto usługi Automation.

1. Kliknij **pozycję Elementy runbook w** obszarze **Automatyzacja procesów**. Zostanie wyświetlona lista elementów runbook.

1. Kliknij **pozycję Utwórz projekt runbook** u góry listy.

1. Wprowadź `Hello-World` nazwę uruchomieniu w polu **Nazwa** i wybierz pozycję **PowerShell** dla pola **Typ yjka.** 

   ![Wprowadzanie na stronie informacji dotyczących elementu runbook usługi Automation](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kliknij przycisk **Utwórz**. Element runbook zostanie utworzony i zostanie otworzona strona Edytuj element runbook programu PowerShell.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Wpisz lub skopiuj i wklej następujący kod do okienka edycji. Tworzy opcjonalny parametr `Name` wejściowy wywoływany `World`z domyślną wartością programu , i wyprowadza ciąg, który używa tej wartości wejściowej:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kliknij **przycisk Zapisz,** aby zapisać kopię roboczą likwiatu.

    ![Tworzenie skryptu programu PowerShell w edytorze elementów runbook](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Po utworzeniu śmigięcie należy przetestować projekt runbook, aby sprawdzić, czy działa.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

1. Wprowadź wartość w polu **Nazwa** i kliknij pozycję **Uruchom**. Zadanie testowania zostanie uruchomione oraz zostaną wyświetlone stan zadania i danej wyjściowe.

    ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Zamknij okienko testu, klikając x **w** prawym górnym rogu. Wybierz przycisk **OK** w wyświetlonym oknie podręcznym.

1. Na stronie Edytuj element runbook programu PowerShell kliknij pozycję **Publikuj**, aby opublikować element runbook jako oficjalną wersję elementu runboook na koncie.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Uruchamianie elementu runbook

Po opublikowaniu elementu runbook zostanie wyświetlona strona przeglądu.

1. Na stronie przeglądu elementu runbook kliknij pozycję **Uruchom**, aby otworzyć stronę konfiguracji Uruchamianie elementu Runbook dla tego elementu runbook.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Zostaw pole **Nazwa** puste, aby została użyta wartość domyślna, i kliknij przycisk **OK**. Zadanie runbooka zostanie przesłane, a na stronie Zadanie zostanie wyświetlona strona Zadanie.

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Gdy stan zadania `Running` `Completed`jest lub , kliknij przycisk **Dane wyjściowe,** aby otworzyć okienko Dane wyjściowe i wyświetlić dane wyjściowe uruchomieniu. 105/

   ![Zadanie testowania elementu runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, edytowano, przetestowano i opublikowano element runbook oraz uruchomiono zadanie elementu runbook. Aby dowiedzieć się więcej o elementach runbook usługi Automation, przejdź do artykułu opisującego różne typy elementów runbook, które można tworzyć i z nich korzystać w usłudze Automation.

> [!div class="nextstepaction"]
> [Instrukcje dotyczące usługi Automation — typy elementów runbook](./automation-runbook-types.md)
