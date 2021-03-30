---
title: Rozwiązywanie problemów z awariami maszyn wirtualnych i środowiska Azure DevTest Labs
description: Dowiedz się, jak rozwiązywać problemy dotyczące maszyn wirtualnych i błędów tworzenia środowiska w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476482"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Rozwiązywanie problemów dotyczących maszyn wirtualnych i błędów tworzenia środowiska w Azure DevTest Labs
DevTest Labs zawiera ostrzeżenia, jeśli nazwa komputera jest nieprawidłowa lub jeśli zamierzasz naruszać zasady laboratorium. Czasami zobaczysz czerwoną pozycję `X` na maszynie wirtualnej laboratorium lub w stanie środowiska, która informuje o niepowodzeniu.  Ten artykuł zawiera kilka lew, których można użyć w celu znalezienia podstawowego problemu i miejmy nadzieję, aby uniknąć problemu w przyszłości.

## <a name="portal-notifications"></a>Powiadomienia portalu
Jeśli używasz Azure Portal, pierwsze miejsce, do którego chcesz się zapoznać, jest **panel powiadomień**.  Panel powiadomień, dostępny na głównym pasku poleceń, klikając **ikonę dzwonka**, poinformuje użytkownika o tym, czy maszyna wirtualna lub Tworzenie środowiska laboratorium zakończyło się powodzeniem.  Jeśli wystąpił błąd, zostanie wyświetlony komunikat o błędzie skojarzony z błędem tworzenia. Szczegóły często zawierają dodatkowe informacje pomocne w rozwiązaniu problemu. W poniższym przykładzie Tworzenie maszyny wirtualnej nie powiodło się z powodu braku rdzeni. Szczegółowy komunikat informuje, jak rozwiązać problem i zażądać wzrostu przydziału rdzeni.

![Powiadomienie Azure Portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Maszyna wirtualna w stanie uszkodzenia
Jeśli stan maszyny wirtualnej w laboratorium jest **uszkodzony**, źródłowa maszyna wirtualna mogła zostać usunięta ze strony **maszyny wirtualnej** , do której użytkownik może przejść ze strony **Virtual Machines** (nie ze strony DevTest Labs). Wyczyść laboratorium w DevTest Labs, usuwając maszynę wirtualną z laboratorium. Następnie utwórz ponownie maszynę wirtualną w środowisku laboratoryjnym. 

![Maszyna wirtualna w stanie uszkodzona](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Dzienniki aktywności
Sprawdź dzienniki aktywności, jeśli badasz awarię jakiś czas po próbie utworzenia maszyny wirtualnej lub środowiska. W tej sekcji pokazano, jak znaleźć dzienniki dla maszyn wirtualnych i środowisk.

## <a name="activity-logs-for-virtual-machines"></a>Dzienniki aktywności dla maszyn wirtualnych

1. Na stronie głównej laboratorium wybierz MASZYNę wirtualną, aby uruchomić stronę **maszyny wirtualnej** .
2. Na stronie **maszyna wirtualna** w sekcji **monitorowanie** w menu po lewej stronie wybierz pozycję **Dziennik aktywności** , aby wyświetlić wszystkie dzienniki skojarzone z maszyną wirtualną.
3. W obszarze elementy dziennika aktywności wybierz operację, która nie powiodła się. Zazwyczaj operacja zakończona niepowodzeniem jest wywoływana `Write Virtualmachines` .
4. W okienku po prawej stronie przejdź do karty JSON. Szczegóły znajdują się w widoku JSON dziennika.

    ![Dziennik aktywności dla maszyny wirtualnej](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Zapoznaj się z dziennikiem JSON, dopóki nie znajdziesz `statusMessage` właściwości. Zawiera on główny komunikat o błędzie i szczegółowe informacje, jeśli ma to zastosowanie. Poniższy kod JSON jest przykładem błędu, który został wyświetlony we wcześniejszej części tego artykułu.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Dziennik aktywności dla środowiska

Aby wyświetlić dziennik aktywności dla tworzenia środowiska, wykonaj następujące kroki:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady** w menu po lewej stronie.
2. na stronie **Konfiguracja i zasady** wybierz pozycję **dzienniki aktywności** w menu.
3. Poszukaj błędu na liście działań w dzienniku i wybierz ją.
4. W okienku po prawej stronie przejdź do karty JSON i Wyszukaj **statusMessage**.

    ![Dziennik aktywności środowiska](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Dzienniki wdrożenia szablonu Menedżer zasobów
Jeśli środowisko lub maszyna wirtualna została utworzona za poorednictwem usługi Automation, w ostatnim miejscu można znaleźć informacje o błędzie. To jest dziennik wdrożenia szablonu Azure Resource Manager. Gdy zasób laboratoryjny zostanie utworzony za pomocą automatyzacji, często odbywa się to za pomocą wdrożenia szablonu Azure Resource Manager. Zapoznaj [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) się z przykładowymi szablonami Azure Resource Manager, które tworzą zasoby usługi DevTest Labs.

Aby wyświetlić dzienniki wdrożenia szablonu laboratorium, wykonaj następujące kroki:

1. Uruchom stronę dla grupy zasobów, w której istnieje laboratorium.
2. Wybierz pozycję **wdrożenia** w menu po lewej stronie w obszarze **Ustawienia**.
3. Wyszukaj wdrożenia z nieprawidłowym stanem i wybierz je.
4. Na stronie **wdrażanie** wybierz łącze **szczegóły operacji** dla operacji, która się nie powiodła.
5. W oknie **szczegóły operacji** są wyświetlane szczegółowe informacje o operacji zakończonej niepowodzeniem.

## <a name="next-steps"></a>Następne kroki
Zobacz [Rozwiązywanie problemów z błędami artefaktów](devtest-lab-troubleshoot-artifact-failure.md)
