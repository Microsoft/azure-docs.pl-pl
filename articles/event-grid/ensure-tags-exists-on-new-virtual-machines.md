---
title: Integracja usługi Azure Automation z usługą Event Grid | Microsoft Docs
description: Dowiedz się, jak automatycznie dodać tag po utworzeniu nowej maszyny wirtualnej i wysłać powiadomienie do usługi Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87462007"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Samouczek: integracja Azure Automation z usługami Event Grid i Microsoft Teams

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie przykładowego elementu runbook usługi Event Grid.
> * Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams.
> * Tworzenie elementu webhook dla elementu runbook.
> * Tworzy subskrypcję usługi Event Grid.
> * Tworzenie maszyny wirtualnej, która wyzwala element runbook.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Do ukończenia tego samouczka potrzebujesz [konta usługi Azure Automation](../automation/index.yml), na którym będzie przechowywany element runbook wyzwolony z subskrypcji usługi Azure Event Grid.

* Na koncie usługi Automation musi zostać załadowany moduł `AzureRM.Tags`. Zobacz [How to import modules in Azure Automation (Jak importować moduły w usłudze Azure Automation) ](../automation/automation-update-azure-modules.md), aby dowiedzieć się, jak importować moduły do usługi Azure Automation.

## <a name="import-an-event-grid-sample-runbook"></a>Importowanie przykładowego elementu runbook usługi Event Grid

1. Wybierz konto usługi Automation, a następnie wybierz stronę **Elementy runbook**.

   ![Wybieranie elementów runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Wybierz przycisk **Przeglądaj galerię**.

3. Wyszukaj pozycję **Event Grid**, a następnie wybierz pozycję **Integrowanie usługi Azure Automation z usługą Event Grid**.

    ![Importowanie elementu runbook z galerii](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Wybierz polecenie **Importuj** i wprowadź nazwę **Watch-VMWrite**.

5. Po zaimportowaniu elementu runbook wybierz polecenie **Edytuj**, aby wyświetlić jego źródło. 
6. Zaktualizuj wiersz 74 w skrypcie, który ma zostać użyty `Tag` zamiast `Tags` .

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Wybierz przycisk **Publikuj**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams

1. W usłudze Microsoft Teams wybierz pozycję **Więcej opcji** obok nazwy kanału, a następnie wybierz pozycję **Łączniki**.

    ![Połączenia usługi Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Przewiń listę łączników do pozycji **Przychodzący element webhook** i wybierz pozycję **Dodaj**.

3. Wprowadź nazwę **AzureAutomationIntegration**, a następnie wybierz pozycję **Utwórz**.

4. Skopiuj adres URL elementu webhook do schowka i Zapisz go. Adres URL elementu webhook jest używany do wysyłania informacji do usługi Microsoft Teams.

5. Wybierz pozycję **Gotowe**, aby zapisać element webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Tworzenie elementu webhook dla elementu runbook

1. Otwórz element runbook Watch-VMWrite.

2. Wybierz pozycję **Elementy webhook**, a następnie **Dodaj element webhook**.

3. Wprowadź nazwę **WatchVMEventGrid**. Skopiuj adres URL do schowka, a następnie zapisz go.

    ![Konfigurowanie nazwy elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Wybierz pozycję **Skonfiguruj parametry i parametry uruchomieniowe**, a następnie wprowadź adres URL elementu webhook usługi Microsoft Teams w polu **CHANNELURL**. Pozostaw pole **WEBHOOKDATA** puste.

    ![Konfigurowanie parametrów elementu webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Wybierz pozycję **Utwórz**, aby utworzyć element webhook dla elementu runbook usługi Automation.

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

1. Na stronie omówienia **konta usługi Automation** wybierz pozycję **Event Grid**.

    ![Wybieranie usługi Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Kliknij pozycję **+ Subskrypcja zdarzeń**.

3. Skonfiguruj subskrypcję przy użyciu następujących informacji:
    1. W polu **Typ tematu** wybierz pozycję **Subskrypcje platformy Azure**.
    2. Usuń zaznaczenie pola wyboru **Subskrybuj wszystkie typy zdarzeń**.
    3. Wprowadź nazwę **AzureAutomation**.
    4. Na liście rozwijanej **Zdefiniowane typy zdarzeń** wyczyść wszystkie opcje z wyjątkiem **Powodzenie zapisu zasobów**.

        > [!NOTE] 
        > Azure Resource Manager obecnie nie różni się od tworzenia i aktualizowania, więc implementacja tego samouczka dla wszystkich zdarzeń Microsoft. resources. ResourceWriteSuccess w ramach subskrypcji platformy Azure może spowodować powstanie dużej liczby wywołań.
    1. W polu **Typ punktu końcowego** wybierz pozycję **Element webhook**.
    2. Kliknij pozycję **Wybierz punkt końcowy**. Na stronie **Wybieranie elementu webhook**, która zostanie otwarta, wklej adres URL elementu webhook utworzonego dla elementu runbook Watch-VMWrite.
    3. W obszarze **FILTRY** wprowadź subskrypcję i grupę zasobów, w której chcesz wyszukać nowo utworzone maszyny wirtualne. Powinno to wyglądać następująco: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję usługi Event Grid.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Tworzenie maszyny wirtualnej, która wyzwala element runbook

1. Utwórz nową maszynę wirtualną w grupie zasobów określonej w filtrze prefiksu subskrypcji usługi Event Grid.

2. Element runbook Watch-VMWrite powinien zostać wywołany, a nowy tag dodany do maszyny wirtualnej.

    ![Tag maszyny wirtualnej](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nowy komunikat zostanie wysłany do kanału usługi Microsoft Teams.

    ![Powiadomienie usługi Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano integrację usług Event Grid i Automation. W tym samouczku omówiono:

> [!div class="checklist"]
> * Importowanie przykładowego elementu runbook usługi Event Grid.
> * Tworzenie opcjonalnego elementu webhook usługi Microsoft Teams.
> * Tworzenie elementu webhook dla elementu runbook.
> * Tworzy subskrypcję usługi Event Grid.
> * Tworzenie maszyny wirtualnej, która wyzwala element runbook.

> [!div class="nextstepaction"]
> [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Event Grid](../event-grid/custom-event-quickstart.md)
