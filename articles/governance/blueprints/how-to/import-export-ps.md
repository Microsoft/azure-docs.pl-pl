---
title: Importowanie i eksportowanie planów przy użyciu programu PowerShell
description: Dowiedz się, jak korzystać z definicji planu jako kodu. Udostępnianie, kontrola źródła i zarządzanie nimi za pomocą poleceń eksportu i importu.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919990"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importowanie i eksportowanie definicji planów przy użyciu programu PowerShell

Plany platformy Azure mogą być w pełni zarządzane za pomocą Azure Portal. W miarę jak organizacje przestają korzystać z planów platformy Azure, powinny zacząć myśleć o definicjach planów jako kodu zarządzanego. Pojęcie to jest często określane jako infrastruktura jako kod (IaC). Traktowanie definicji planu jako kodu oferuje dodatkowe korzyści poza ofertą Azure Portal. Te korzyści obejmują między innymi:

- Udostępnianie definicji planów
- Tworzenie kopii zapasowej definicji planów
- Używanie definicji planu w różnych dzierżawach lub subskrypcjach
- Umieszczanie definicji planów w kontroli źródła
  - Zautomatyzowane testowanie definicji planów w środowiskach testowych
  - Obsługa potoków ciągłej integracji i ciągłego wdrażania (CI/CD)

Bez względu na to, że Zarządzanie definicjami planów w postaci kodu ma zalety. W tym artykule pokazano, jak używać `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` poleceń i w module [AZ. plan](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono umiarkowaną praktyczną wiedzę na temat planów platformy Azure. Jeśli jeszcze tego nie zrobiono, należy wykonać czynności opisane w następujących artykułach:

- [Tworzenie strategii w portalu](../create-blueprint-portal.md)
- Przeczytaj informacje o [etapach wdrażania](../concepts/deployment-stages.md) i [cyklu życia](../concepts/lifecycle.md) planu
- [Tworzenie](../create-blueprint-powershell.md) i [Zarządzanie](./manage-assignments-ps.md) definicjami planów i przypisaniami przy użyciu programu PowerShell

Jeśli nie jest jeszcze zainstalowana, postępuj zgodnie z instrukcjami w temacie [Dodawanie modułu AZ. plan](./manage-assignments-ps.md#add-the-azblueprint-module) w celu zainstalowania i sprawdzenia poprawności modułu **AZ. plan** z poziomu Galeria programu PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Struktura folderów definicji planu

Przed rozpoczęciem eksportowania i importowania planów Przyjrzyjmy się, jak pliki wchodzące w skład definicji strategii są strukturalne. Definicja planu powinna być przechowywana w jego własnym folderze.

> [!IMPORTANT]
> Jeśli żadna wartość nie jest przenoszona do parametru **name** `Import-AzBlueprintWithArtifact` polecenia cmdlet, używana jest nazwa folderu, w którym jest przechowywana definicja planu.

Wraz z definicją strategii, która musi mieć nazwę `blueprint.json` , są artefakty, które składają się z definicji planu. Każdy artefakt musi znajdować się w podfolderze o nazwie `artifacts` .
Ze sobą struktura definicji planu jako plików JSON w folderach powinna wyglądać następująco:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Eksportowanie definicji planu

Procedura eksportowania definicji planu jest prosta. Eksportowanie definicji strategii może być przydatne w przypadku udostępniania, wykonywania kopii zapasowej lub umieszczania w kontroli źródła.

- **Plan** [wymagany]
  - Określa definicję planu
  - Użyj, `Get-AzBlueprint` Aby uzyskać obiekt odwołania
- **OutputPath** [wymagane]
  - Określa ścieżkę, w której mają zostać zapisane pliki JSON definicji planu
  - Pliki wyjściowe znajdują się w podfolderze o nazwie definicji strategii
- **Wersja** (opcjonalnie)
  - Określa wersję do wyprowadzenia **w przypadku, gdy** obiekt odwołania do strategii zawiera odwołania do więcej niż jednej wersji.

1. Pobierz odwołanie do definicji planu w celu wyeksportowania z subskrypcji reprezentowanej jako `{subId}` :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Użyj `Export-AzBlueprintWithArtifact` polecenia cmdlet, aby wyeksportować określoną definicję strategii:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importowanie definicji planu

Po [wyeksportowaniu definicji](#export-your-blueprint-definition) planu lub zdefiniowaniu ręcznie utworzonej definicji planu w [wymaganej strukturze folderów](#folder-structure-of-a-blueprint-definition)można zaimportować tę definicję planu do innej grupy zarządzania lub subskrypcji.

Aby zapoznać się z przykładami wbudowanych definicji planów, zobacz [Azure Blueprint repozytorium GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins).

- **Nazwa** [wymagane]
  - Określa nazwę nowej definicji planu
- **InputPath** [wymagane]
  - Określa ścieżkę, z której ma zostać utworzona definicja planu
  - Musi być zgodna z [wymaganą strukturą folderu](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcjonalnie)
  - Identyfikator grupy zarządzania, w której ma zostać zapisana definicja planu, jeśli nie jest to ustawienie domyślne bieżącego kontekstu
  - Należy określić wartość **ManagementGroupId** lub identyfikator **subskrypcji**
- Identyfikator **subskrypcji** (opcjonalnie)
  - Identyfikator subskrypcji, w której ma zostać zapisana definicja planu, jeśli nie jest to bieżące ustawienie domyślne kontekstu
  - Należy określić wartość **ManagementGroupId** lub identyfikator **subskrypcji**

1. Użyj `Import-AzBlueprintWithArtifact` polecenia cmdlet, aby zaimportować określoną definicję strategii:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Po zaimportowaniu definicji planu [Przypisz ją przy użyciu programu PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Aby uzyskać informacje na temat tworzenia zaawansowanych definicji planów, zobacz następujące artykuły:

- Użyj [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dostosuj [kolejność sekwencjonowania planów](../concepts/sequencing-order.md).
- Chroń wdrożenia za pomocą funkcji [blokowania zasobów](../concepts/resource-locking.md)planu.
- [Zarządzaj planami jako kodem](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).