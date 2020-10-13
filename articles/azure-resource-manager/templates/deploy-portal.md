---
title: Wdrażanie zasobów za pomocą Azure Portal
description: Użyj Azure Portal i Zarządzaj zasobami platformy Azure, aby wdrożyć swoje zasoby w grupie zasobów w ramach subskrypcji.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 31f80eb617820def871633dac1541c7dc3bed691
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255266"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal

Dowiedz się, jak wdrażać zasoby platformy Azure przy użyciu szablonów [Azure Portal](https://portal.azure.com) z [Azure Resource Manager (ARM)](overview.md) . Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure Portal](../management/manage-resources-portal.md).

Wdrażanie zasobów platformy Azure przy użyciu Azure Portal zwykle obejmuje dwa kroki:

- Utwórz grupę zasobów.
- Wdróż zasoby w grupie zasobów.

Ponadto można również wdrożyć szablon ARM w celu utworzenia zasobów platformy Azure.

W tym artykule przedstawiono obie metody.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Aby utworzyć nową grupę zasobów, wybierz pozycję **grupy zasobów** z [Azure Portal](https://portal.azure.com).

   ![Wybierz grupy zasobów](./media/deploy-portal/select-resource-groups.png)

1. W obszarze grupy zasobów wybierz pozycję **Dodaj**.

   ![Dodaj grupę zasobów](./media/deploy-portal/add-resource-group.png)

1. Wybierz lub wprowadź następujące wartości właściwości:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: nadaj nazwę grupie zasobów.
    - **Region**: Określ lokalizację platformy Azure. Jest to miejsce, w którym Grupa zasobów przechowuje metadane dotyczące zasobów. Ze względów związanych ze zgodnością warto określić miejsce przechowywania metadanych. Ogólnie rzecz biorąc, zalecamy określenie lokalizacji, w której będą znajdować się większość zasobów. Korzystanie z tej samej lokalizacji może uprościć szablon.

   ![Ustawianie wartości grupy](./media/deploy-portal/set-group-properties.png)

1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Przejrzyj wartości, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Odśwież** , aby wyświetlić nową grupę zasobów na liście.

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu grupy zasobów można wdrożyć zasoby w grupie z portalu Marketplace. Portal Marketplace zawiera wstępnie zdefiniowane rozwiązania dla typowych scenariuszy.

1. Aby rozpocząć wdrożenie, wybierz pozycję **Utwórz zasób** na podstawie [Azure Portal](https://portal.azure.com).

   ![Nowy zasób](./media/deploy-portal/new-resources.png)

1. Znajdź Typ zasobu, który chcesz wdrożyć. Zasoby są zorganizowane w kategorii. Jeśli nie widzisz konkretnego rozwiązania, które chcesz wdrożyć, możesz wyszukać je w portalu Marketplace. Poniższy zrzut ekranu pokazuje, że wybrano serwer Ubuntu.

   ![Wybierz typ zasobu](./media/deploy-portal/select-resource-type.png)

1. W zależności od typu wybranego zasobu masz kolekcję odpowiednich właściwości do ustawienia przed wdrożeniem. Dla wszystkich typów należy wybrać docelową grupę zasobów. Na poniższej ilustracji pokazano, jak utworzyć maszynę wirtualną z systemem Linux i wdrożyć ją w utworzonej grupie zasobów.

   ![Tworzenie grupy zasobów](./media/deploy-portal/select-existing-group.png)

   Alternatywnie można utworzyć grupę zasobów podczas wdrażania zasobów. Wybierz pozycję **Utwórz nową** i nadaj grupie zasobów nazwę.

1. Wdrożenie rozpocznie się. Wdrożenie może potrwać kilka minut. Niektóre zasoby będą trwać dłużej niż w przypadku innych zasobów. Po zakończeniu wdrażania zostanie wyświetlone powiadomienie. Wybierz pozycję **Przejdź do zasobu** , aby otworzyć

   ![Wyświetl powiadomienie](./media/deploy-portal/view-notification.png)

1. Po wdrożeniu zasobów możesz dodać więcej zasobów do grupy zasobów, wybierając pozycję **Dodaj**.

   ![Dodaj zasób](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Wdróż zasoby z szablonu niestandardowego

Jeśli chcesz wykonać wdrożenie, ale nie używasz żadnego z szablonów w portalu Marketplace, możesz utworzyć dostosowany szablon, który definiuje infrastrukturę rozwiązania. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz [Omówienie struktury i składni szablonów ARM](template-syntax.md).

> [!NOTE]
> Interfejs portalu nie obsługuje odwoływania się do [wpisu tajnego z Key Vault](key-vault-parameter.md). Zamiast tego należy użyć [programu PowerShell](deploy-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](deploy-cli.md) do wdrożenia szablonu lokalnie lub z zewnętrznego identyfikatora URI.

1. Aby wdrożyć dostosowany szablon za pomocą portalu, wybierz pozycję **Utwórz zasób**, Wyszukaj **szablon**. a następnie wybierz pozycję **Template Deployment**.

   ![Wyszukaj wdrożenie szablonu](./media/deploy-portal/search-template.png)

1. Wybierz przycisk **Utwórz**.
1. Zobaczysz kilka opcji tworzenia szablonu:

    - **Kompiluj własny szablon w edytorze**: Utwórz szablon przy użyciu edytora szablonów portalu.  Edytor może dodać schemat szablonu zasobu.
    - **Szablony wspólne**: Istnieją cztery popularne szablony służące do tworzenia maszyny wirtualnej z systemem Linux, maszyny wirtualnej systemu Windows, aplikacji sieci Web i bazy danych w Azure SQL Database.
    - **Załaduj szablon szybkiego startu usługi GitHub**: Użyj istniejących [szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/).

   ![Opcje widoku](./media/deploy-portal/see-options.png)

    Ten samouczek zawiera instrukcje dotyczące ładowania szablonu szybkiego startu.

1. W obszarze **ładowanie szablonu szybkiego startu usługi GitHub**wpisz lub wybierz pozycję **101-Storage-account-Create**.

    Dostępne są dwie opcje:

    - **Wybierz szablon**: Wdróż szablon.
    - **Edytuj szablon**: Edytuj szablon szybkiego startu przed jego wdrożeniem.

1. Wybierz pozycję **Edytuj szablon** , aby poznać Edytor szablonów portalu. Szablon jest ładowany w edytorze. Należy zauważyć, że istnieją dwa parametry: **storageAccountType** i **Location**.

   ![Tworzenie szablonu](./media/deploy-portal/show-json.png)

1. Wprowadź drobne zmiany w szablonie. Na przykład zaktualizuj zmienną **storageAccountName** do:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Wybierz pozycję **Zapisz**. Teraz zostanie wyświetlony interfejs wdrożenia szablonu portalu. Zwróć uwagę na dwa parametry zdefiniowane w szablonie.
1. Wprowadź lub wybierz wartości właściwości:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj jej nazwę.
    - **Lokalizacja**: Wybierz lokalizację platformy Azure.
    - **Typ konta magazynu**: Użyj wartości domyślnej.
    - **Lokalizacja**: Użyj wartości domyślnej.
    - **Wyrażam zgodę na powyższe warunki i postanowienia**: (wybierz)

1. Wybierz pozycję **Kup**.

## <a name="next-steps"></a>Następne kroki

- Aby wyświetlić dzienniki inspekcji, zobacz [operacje inspekcji z Menedżer zasobów](../management/view-activity-logs.md).
- Aby rozwiązać problemy z błędami wdrażania, zobacz [Wyświetlanie operacji wdrażania](deployment-history.md).
- Aby wyeksportować szablon z wdrożenia lub grupy zasobów, zobacz [Eksportowanie szablonów ARM](export-template-portal.md).
- Aby bezpiecznie wdrożyć usługę w wielu regionach, zobacz [Azure Menedżer wdrażania](deployment-manager-overview.md).
