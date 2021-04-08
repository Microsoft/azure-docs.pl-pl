---
title: Opublikuj anons w laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak opublikować niestandardowy anons w istniejącym laboratorium, aby poinformować użytkowników o najnowszych zmianach lub dodatkach do laboratorium w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270771"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Opublikuj anons w laboratorium w Azure DevTest Labs

Jako administrator laboratorium możesz ogłosić niestandardowy anons w istniejącym laboratorium, aby poinformować użytkowników o najnowszych zmianach lub dodatkach do laboratorium. Na przykład możesz chcieć poinformować użytkowników o:

- Nowe dostępne rozmiary maszyn wirtualnych
- Obrazy, które są obecnie bezużyteczne
- Aktualizacje zasad laboratorium

Po opublikowaniu anons zostanie wyświetlony na stronie Przegląd laboratorium, a użytkownik może wybrać go, aby uzyskać więcej szczegółów.

Funkcja anonsu jest przeznaczona do użycia na potrzeby tymczasowych powiadomień.  Możesz łatwo wyłączyć anons, gdy nie jest już potrzebne.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Kroki umożliwiające opublikowanie anonsu w istniejącym laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** . (Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz ogłosić ogłoszenie.
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. W obszarze **Ustawienia** wybierz pozycję **ogłoszenie laboratorium**.

    ![Przycisk anons Lab](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Aby utworzyć komunikat dla użytkowników w tym laboratorium, ustaw wartość **włączone** na **tak**.

1. Możesz wprowadzić **datę wygaśnięcia** , aby określić datę i godzinę, po upływie których anons nie jest już pokazywany użytkownikom. Jeśli nie wprowadzisz daty wygaśnięcia, ogłoszenie pozostanie do momentu jej wyłączenia.

   > [!NOTE]
   > Po wygaśnięciu anonsu nie jest on już pokazywany użytkownikom, ale nadal istnieje w okienku **anons laboratorium** . Można wprowadzić w nim zmiany i ponownie je włączyć, aby ponownie aktywować.
   >
   >

1. Wprowadź **tytuł anonsu** i **tekst anonsu**.

   Tytuł może składać się z maksymalnie 100 znaków i jest widoczny dla użytkownika na stronie przeglądu laboratorium. Jeśli użytkownik wybierze tytuł, zostanie wyświetlony tekst anonsu.

   W tekście anonsu jest akceptowana wartość promocji. Po wprowadzeniu tekstu powiadomienia możesz wyświetlić komunikat w obszarze podglądu u dołu ekranu.

    ![Ekran anonsowania laboratorium do tworzenia wiadomości.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Wybierz pozycję **Zapisz** , gdy ogłoszenie jest gotowe do opublikowania.

Gdy nie chcesz już wyświetlać tego anonsu dla użytkowników laboratorium, Wróć do strony **anons Lab** **i ustaw wartość** **nie**. W przypadku określenia daty wygaśnięcia anons zostanie automatycznie wyłączony w tym dniu i o tej godzinie.

## <a name="steps-for-users-to-view-an-announcement"></a>Kroki dla użytkowników dotyczące wyświetlania anonsu

1. Z [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium.

1. Jeśli laboratorium ma ogłoszone powiadomienie, w górnej części strony Przegląd laboratorium zostanie wyświetlone powiadomienie z informacjami. Ten komunikat informacyjny jest tytułem anonsu, który został określony podczas tworzenia anonsu.

    ![Powiadomienie laboratorium na stronie przeglądu](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Użytkownik może wybrać komunikat, aby wyświetlić cały anons.

    ![Więcej informacji na temat anonsu laboratorium](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Możesz określić anons jako część szablonu Azure Resource Manager, jak pokazano w następującym przykładzie:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Szablon Azure Resource Manager można wdrożyć przy użyciu jednego z następujących sposobów:

- [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Interfejs API REST](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Następne kroki
* Jeśli zmienisz lub ustawisz zasady laboratorium, możesz chcieć ogłosić anons w celu informowania użytkowników. [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md) zawiera informacje dotyczące stosowania ograniczeń i Konwencji w ramach subskrypcji przy użyciu zasad niestandardowych.
* Zapoznaj się z [galerią szablonów DevTest Labs Azure Resource Manager — szybki start](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
