---
title: Importowanie i eksportowanie planów za pomocą programu PowerShell
description: Dowiedz się, jak pracować z definicjami planu jako kodem. Udostępniaj, kontroluj źródła i zarządzaj nimi za pomocą poleceń eksportu i importu.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686836"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importowanie i eksportowanie definicji planu za pomocą programu PowerShell

Plany platformy Azure można w pełni zarządzać za pośrednictwem witryny Azure portal. W miarę postępów organizacji w korzystaniu z planów platformy Azure, powinny zacząć myśleć o definicjach planu jako kod zarządzany. Ta koncepcja jest często określana jako infrastruktura jako kod (IaC). Traktowanie definicji planu jako kodu oferuje dodatkowe korzyści wykraczające poza to, co oferuje witryna Azure portal. Korzyści te obejmują:

- Udostępnianie definicji planu
- Tworzenie kopii zapasowej definicji planu
- Ponowne czesanie definicji planu w różnych dzierżawach lub subskrypcjach
- Umieszczanie definicji planu w kontroli źródła
  - Automatyczne testowanie definicji planu w środowiskach testowych
  - Obsługa ciągłej integracji i ciągłego wdrażania potoków (CI/CD)

Niezależnie od powodów, zarządzanie definicjami planu jako kod ma korzyści. W tym artykule `Import-AzBlueprintWithArtifact` pokazano, jak używać i `Export-AzBlueprintWithArtifact` poleceń w module [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że umiarkowana wiedza robocza na temat planów platformy Azure. Jeśli jeszcze tego nie zrobiono, przejmij następujące artykuły:

- [Tworzenie strategii w portalu](../create-blueprint-portal.md)
- Przeczytaj o [etapach wdrażania](../concepts/deployment-stages.md) i [cyklu życia planu](../concepts/lifecycle.md)
- [Tworzenie](../create-blueprint-powershell.md) definicji i przydziałów planu i [zarządzanie nimi](./manage-assignments-ps.md) za pomocą programu PowerShell

Jeśli nie jest jeszcze zainstalowany, postępuj zgodnie z instrukcjami w [Dodaj moduł Az.Blueprint,](./manage-assignments-ps.md#add-the-azblueprint-module) aby zainstalować i sprawdzić poprawność modułu **Az.Blueprint** z Galerii programu PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Struktura folderów definicji planu

Zanim przyjrzymy się eksportowaniu i importowaniu planów, przyjrzyjmy się, jak mają strukturę plików tworzących definicję planu. Definicja planu powinna być przechowywana we własnym folderze.

> [!IMPORTANT]
> Jeśli żadna wartość nie jest `Import-AzBlueprintWithArtifact` przekazywana do **parametru Nazwa** polecenia cmdlet, używana jest nazwa folderu, w którego jest przechowywana definicja planu.

Wraz z definicją planu, `blueprint.json`który musi być nazwany, są artefakty, które składa się z definicji planu. Każdy artefakt musi znajdować się `artifacts`w podfolderze o nazwie .
Zebrano strukturę definicji planu jako plików JSON w folderach powinna wyglądać następująco:

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

Kroki eksportowania definicji planu są proste. Eksportowanie definicji planu może być przydatne do udostępniania, tworzenia kopii zapasowych lub umieszczania w formancie źródłowym.

- **Plan** [wymagany]
  - Określa definicję planu
  - Użyj, `Get-AzBlueprint` aby uzyskać obiekt odniesienia
- **Ścieżka wyjściowa** [wymagane]
  - Określa ścieżkę zapisywania plików JSON definicji planu
  - Pliki wyjściowe znajdują się w podfolderze z nazwą definicji planu
- **Wersja** (opcjonalnie)
  - Określa wersję do wytłkować, jeśli obiekt odniesienia **blueprint** zawiera odwołania do więcej niż jednej wersji.

1. Uzyskaj odwołanie do definicji planu do wyeksportowania z subskrypcji reprezentowane jako: `{subId}`

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Użyj `Export-AzBlueprintWithArtifact` polecenia cmdlet, aby wyeksportować określoną definicję planu:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importowanie definicji planu

Po [wyeksportowaniu definicji planu](#export-your-blueprint-definition) lub ręcznie utworzonej definicji planu w [wymaganej strukturze folderów](#folder-structure-of-a-blueprint-definition)można zaimportować tę definicję planu do innej grupy zarządzania lub subskrypcji.

Aby zapoznać się z przykładami wbudowanych definicji planu, zobacz [repozytorium Usługi Azure Blueprint GitHub.](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)

- **Nazwa** [wymagane]
  - Określa nazwę nowej definicji planu
- **InputPath** [wymagane]
  - Określa ścieżkę do utworzenia definicji planu z
  - Musi być zgodna z [wymaganą strukturą folderów](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (opcjonalnie)
  - Identyfikator grupy zarządzania, aby zapisać definicję planu, jeśli nie domyślny bieżący kontekst
  - Należy określić **identyfikator Grupy Zarządzania** lub **identyfikator subskrypcji**
- **Identyfikator subskrypcji** (opcjonalnie)
  - Identyfikator subskrypcji, aby zapisać definicję planu, jeśli nie domyślny bieżący kontekst
  - Należy określić **identyfikator Grupy Zarządzania** lub **identyfikator subskrypcji**

1. Użyj `Import-AzBlueprintWithArtifact` polecenia cmdlet, aby zaimportować określoną definicję planu:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Po zaimportowaniu definicji planu [przypisz ją za pomocą programu PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Aby uzyskać informacje na temat tworzenia zaawansowanych definicji planu, zobacz następujące artykuły:

- Użyj [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dostosuj [kolejność sekwencjonowania planu](../concepts/sequencing-order.md).
- Chroń wdrożenia za pomocą [blokowania zasobów planu](../concepts/resource-locking.md).
- [Zarządzanie planami jako kodem](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).