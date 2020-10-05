---
title: Udostępnianie poza organizacji (szablon ARM) — Przewodnik Szybki Start dotyczący usługi Azure Data Share
description: Dowiedz się, jak udostępniać dane klientom i partnerom za pomocą usługi Azure Data Share i szablonu Menedżer zasobów w tym przewodniku Szybki Start.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 76ef44d438b9af7ada6c1c464705a22ee10f4c58
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88654110"
---
# <a name="quickstart-share-data-using-azure-data-share-and-resource-manager-templates"></a>Szybki Start: udostępnianie danych przy użyciu usługi Azure Data Share i szablonów Menedżer zasobów

Dowiedz się, jak skonfigurować nowy udział danych platformy Azure na podstawie konta usługi Azure Storage przy użyciu szablonu Azure Resource Manager i rozpocząć udostępnianie danych klientom i partnerom spoza organizacji platformy Azure. Listę obsługiwanych magazynów danych można znaleźć [w temacie obsługiwane magazyny danych w udziale danych platformy Azure](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft. Storage/storageAccounts/blobServices/kontenery](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft. Storage/storageAccounts/dostawcy/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft. dataudział/konta](/rest/api/datashare/accounts/create)
* [Microsoft. dataudział/konta/udziały](/rest/api/datashare/shares/create)
* [Microsoft. dataudział/konta/udziały/zestawy danych](/rest/api/datashare/datasets/create)
* [Microsoft. dataudział/konta/udziały/zaproszenia](/rest/api/datashare/invitations/create)
* [Microsoft. datashare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

Szablon wykonuje następujące zadania:

* Utwórz konto magazynu i kontener używany jako źródło danych udziału danych.
* Utwórz konto udostępniania danych i udział danych.
* Utwórz przypisanie roli w celu przyznania roli czytnika danych obiektów blob magazynu do zasobu udziału danych źródłowych. Zobacz [role i wymagania dotyczące udziału danych platformy Azure](./concepts-roles-permissions.md).
* Dodaj zestaw danych do udziału daty.
* Dodaj odbiorców do udziału danych.
* Włącz harmonogram migawek dla udziału danych.

Ten szablon jest tworzony na potrzeby celów edukacyjnych. W ramach tej działalności zazwyczaj masz pewne dane na istniejącym koncie magazynu. Przed uruchomieniem szablonu lub skryptu w celu utworzenia zestawu danych należy utworzyć przypisanie roli. Czasami podczas wdrażania szablonu może zostać wyświetlony następujący komunikat o błędzie:

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Wynika to z faktu, że wdrożenie próbuje utworzyć zestaw danych, zanim przypisanie RBAC zostanie ukończone. Mimo komunikatu o błędzie wdrożenie może się powieść.  Nadal będzie można zapoznać się z [przeglądem wdrożonych zasobów](#review-deployed-resources).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Wybierz lub wprowadź następujące wartości:

    * **Subskrypcja**: wybierz subskrypcję platformy Azure używaną do tworzenia udziału danych i innych zasobów.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, lub wybierz istniejącą grupę zasobów.
    * **Lokalizacja**: wybierz lokalizację grupy zasobów.
    * **Nazwa projektu**: Wprowadź nazwę projektu.  Nazwa projektu służy do generowania nazw zasobów.  Zobacz definicje zmiennych w poprzednim szablonie.
    * **Lokalizacja**: Wybierz lokalizację dla zasobów.  Możesz użyć tej samej lokalizacji dla grupy zasobów.
    * **Wiadomość e-mail z zaproszeniem**: wprowadź adres e-mail logowania odbiorcy platformy Azure.  Alias e-mail nie działa.

    Użyj wartości domyślnej dla pozostałej części ustawień.
1. Wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**, a następnie wybierz pozycję **Kup**.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz utworzone konto udostępniania danych.
1. Z menu po lewej stronie wybierz pozycję **Wyślij udziały**.  Zobaczysz konto magazynu wymienione na liście.
1. Wybierz konto magazynu.  W obszarze **szczegóły**zobaczysz ustawienie synchronizacji skonfigurowane w szablonie.

    ![Ustawienia synchronizacji konta magazynu udziałów danych platformy Azure](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Wybierz pozycję **zaproszenia** z góry. Zobaczysz adres e-mail określony podczas wdrażania szablonu. **Stan** jest **oczekiwany**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobów z grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dowiesz się, jak utworzyć udział danych platformy Azure i zaprosić adresatów. Aby dowiedzieć się więcej o tym, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka [Zaakceptuj i Odbierz dane](subscribe-to-data-share.md) .
