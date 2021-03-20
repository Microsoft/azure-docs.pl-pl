---
title: Eksportuj szablon w Azure Portal
description: Użyj Azure Portal, aby wyeksportować szablon Azure Resource Manager z zasobów w ramach subskrypcji.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: ee97953a337bbb7cc9a8d1f042a3beae7bccdcae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185695"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Eksport jednego i kilku zasobów do szablonu w Azure Portal

Aby pomóc w tworzeniu szablonów Azure Resource Manager, można wyeksportować szablon z istniejących zasobów. Wyeksportowany szablon pomaga zrozumieć składnię i właściwości JSON, które wdrażają zasoby. Aby zautomatyzować przyszłe wdrożenia, Zacznij od wyeksportowanego szablonu i zmodyfikuj go dla danego scenariusza.

Menedżer zasobów umożliwia wybranie jednego lub większej liczby zasobów do wyeksportowania do szablonu. Możesz skupić się na dokładnie żądanych zasobach w szablonie.

W tym artykule przedstawiono sposób eksportowania szablonów za pomocą portalu. Można również użyć interfejsu [wiersza polecenia platformy Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [API REST](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Wybierz opcję eksportu z prawej strony

Istnieją dwa sposoby eksportowania szablonu:

* **Eksportuj z grupy zasobów lub zasobu**. Ta opcja generuje nowy szablon z istniejących zasobów. Wyeksportowany szablon jest "migawką" bieżącego stanu grupy zasobów. Można wyeksportować całą grupę zasobów lub konkretne zasoby w ramach tej grupy zasobów.

* **Eksportuj przed wdrożeniem lub z historii**. Ta opcja pobiera dokładną kopię szablonu użytego do wdrożenia.

W zależności od wybranej opcji, eksportowane szablony mają różne jakości.

| Z grupy zasobów lub zasobu | Przed wdrożeniem lub historią |
| --------------------- | ----------------- |
| Szablon jest migawką bieżącego stanu zasobów. Obejmuje ona wszystkie zmiany ręczne dokonane po wdrożeniu. | Szablon pokazuje tylko stan zasobów w czasie wdrażania. Wszystkie zmiany ręczne wprowadzone po wdrożeniu nie są uwzględniane. |
| Możesz wybrać zasoby z grupy zasobów do wyeksportowania. | Uwzględniono wszystkie zasoby dla określonego wdrożenia. Nie można wybrać podzestawu tych zasobów ani dodać zasobów, które zostały dodane w innym czasie. |
| Szablon zawiera wszystkie właściwości zasobów, w tym niektóre właściwości, które normalnie nie zostały ustawione podczas wdrażania. Przed ponownym użyciem szablonu warto usunąć lub wyczyścić te właściwości. | Szablon zawiera tylko właściwości, które są związane z wdrożeniem. Szablon jest gotowy do użycia. |
| Szablon prawdopodobnie nie zawiera wszystkich parametrów, które są potrzebne do ponownego użycia. Większość wartości właściwości jest zakodowana w szablonie. Aby ponownie wdrożyć szablon w innych środowiskach, należy dodać parametry, które zwiększają możliwości konfiguracji zasobów.  Można usunąć zaznaczenie opcji **Dołącz parametry** , aby można było tworzyć własne parametry. | Szablon zawiera parametry, które ułatwiają ponowne wdrażanie w różnych środowiskach. |

Wyeksportuj szablon z grupy zasobów lub zasobu, gdy:

* Musisz przechwycić zmiany w zasobach, które zostały wprowadzone po pierwotnym wdrożeniu.
* Chcesz wybrać zasoby, które mają zostać wyeksportowane.

Wyeksportuj szablon przed wdrożeniem lub z historii, gdy:

* Chcesz, aby szablon był łatwy do ponownego użycia.
* Nie trzeba dołączać zmian wprowadzonych po oryginalnym wdrożeniu.

## <a name="limitations"></a>Ograniczenia

Podczas eksportowania z grupy zasobów lub zasobu wyeksportowany szablon jest generowany na podstawie [opublikowanych schematów](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) dla każdego typu zasobu. Czasami schemat nie ma najnowszej wersji dla typu zasobu. Sprawdź wyeksportowany szablon, aby upewnić się, że zawiera on potrzebne właściwości. W razie potrzeby edytuj wyeksportowany szablon, aby użyć potrzebnej wersji interfejsu API.

Funkcja eksportowania szablonu nie obsługuje eksportowania zasobów Azure Data Factory. Aby dowiedzieć się, jak wyeksportować zasoby Data Factory, zobacz [kopiowanie lub klonowanie fabryki danych w Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Aby wyeksportować zasoby utworzone za pomocą klasycznego modelu wdrażania, należy [je zmigrować do modelu wdrażania Menedżer zasobów](../../virtual-machines/migration-classic-resource-manager-overview.md).

Jeśli zostanie wyświetlone ostrzeżenie podczas eksportowania szablonu wskazującego, że typ zasobu nie został wyeksportowany, nadal można odnaleźć właściwości dla tego zasobu. Aby dowiedzieć się więcej o różnych opcjach wyświetlania właściwości zasobów, zobacz [odnajdywanie właściwości zasobów](view-resources.md). Możesz również przyjrzeć się [interfejsowi API REST platformy Azure](/rest/api/azure/) dla typu zasobu.

Istnieje limit 200 zasobów w grupie zasobów, dla której zostanie utworzony wyeksportowany szablon. Jeśli spróbujesz wyeksportować grupę zasobów, która ma więcej niż 200 zasobów, zostanie wyświetlony komunikat o błędzie `Export template is not supported for resource groups more than 200 resources` .

## <a name="export-template-from-a-resource-group"></a>Eksportowanie szablonu z grupy zasobów

Aby wyeksportować jeden lub więcej zasobów z grupy zasobów:

1. Wybierz grupę zasobów zawierającą zasoby, które chcesz wyeksportować.

1. Wybierz co najmniej jeden zasób, zaznaczając pola wyboru.  Aby zaznaczyć wszystkie, zaznacz pole wyboru po lewej stronie **nazwy**. Element menu **Eksportuj szablon** zostanie włączony dopiero po wybraniu co najmniej jednego zasobu.

   ![Eksportuj wszystkie zasoby](./media/export-template-portal/select-all-resources.png)

    Na zrzucie ekranu jest zaznaczone tylko konto magazynu.
1. Wybierz pozycję **Eksportuj szablon**.

1. Zostanie wyświetlony wyeksportowany szablon, który jest dostępny do pobrania i wdrożenia.

   ![Pokaż szablon](./media/export-template-portal/show-template.png)

   Domyślnie wybrane są **Parametry include** .  Po wybraniu tego szablonu zostaną uwzględnione wszystkie parametry szablonu, gdy szablon zostanie wygenerowany. Jeśli chcesz utworzyć własne parametry, przełącz to pole wyboru, aby je nie uwzględnić.

## <a name="export-template-from-a-resource"></a>Eksportowanie szablonu z zasobu

Aby wyeksportować jeden zasób:

1. Wybierz grupę zasobów zawierającą zasób, który chcesz wyeksportować.

1. Wybierz zasób, który chcesz wyeksportować, aby otworzyć zasób.

1. W przypadku tego zasobu wybierz pozycję **Eksportuj szablon** w lewym okienku.

   ![Eksportuj zasób](./media/export-template-portal/export-single-resource.png)

1. Zostanie wyświetlony wyeksportowany szablon, który jest dostępny do pobrania i wdrożenia. Szablon zawiera tylko jeden zasób. Domyślnie wybrane są **Parametry include** .  Po wybraniu tego szablonu zostaną uwzględnione wszystkie parametry szablonu, gdy szablon zostanie wygenerowany. Jeśli chcesz utworzyć własne parametry, przełącz to pole wyboru, aby je nie uwzględnić.

## <a name="export-template-before-deployment"></a>Eksportuj szablon przed wdrożeniem

1. Wybierz usługę platformy Azure, którą chcesz wdrożyć.

1. Wprowadź wartości dla nowej usługi.

1. Po przekazaniu walidacji, ale przed rozpoczęciem wdrażania, wybierz pozycję **Pobierz szablon do automatyzacji**.

   ![Pobieranie szablonu](./media/export-template-portal/download-before-deployment.png)

1. Szablon jest wyświetlany i jest dostępny do pobrania i wdrożenia.


## <a name="export-template-after-deployment"></a>Eksportuj szablon po wdrożeniu

Możesz wyeksportować szablon, który został użyty do wdrożenia istniejących zasobów. Pobrany szablon jest dokładnie taki, który został użyty do wdrożenia.

1. Wybierz grupę zasobów, którą chcesz wyeksportować.

1. Wybierz link w obszarze **wdrożenia**.

   ![Wybierz historię wdrożenia](./media/export-template-portal/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybieranie wdrożenia](./media/export-template-portal/select-details.png)

1. Wybierz **szablon**. Zostanie wyświetlony szablon użyty dla tego wdrożenia, który jest dostępny do pobrania.

   ![Wybieranie szablonu](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak eksportować szablony przy użyciu interfejsu [wiersza polecenia platformy Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](template-syntax.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](../index.yml).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).