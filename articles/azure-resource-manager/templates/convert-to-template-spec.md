---
title: Konwertuj szablon portalu na specyfikację szablonu
description: Opisuje sposób konwersji istniejącego szablonu w Galerii Azure Portal na specyfikacje szablonu.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555940"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Konwertuj galerię szablonów w portalu na specyfikacje szablonu

Azure Portal zapewnia sposób przechowywania szablonów Azure Resource Manager (szablony ARM) na Twoim koncie. Jednak [specyfikacje szablonów](template-specs.md) oferują łatwiejszy sposób udostępniania szablonów użytkownikom w organizacji oraz łączenia z innymi szablonami. W tym artykule przedstawiono sposób konwertowania istniejących szablonów w galerii szablonów na specyfikacje szablonu.

Aby sprawdzić, czy masz jakieś szablony do przekonwertowania, Wyświetl [galerię szablonów w portalu](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Te szablony mają typ zasobu `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Konwertuj przy użyciu skryptu programu PowerShell

Aby uprościć konwertowanie szablonów w galerii szablonów, Użyj skryptu programu PowerShell z repozytorium szablonów szybkiego startu platformy Azure. Po uruchomieniu skryptu można utworzyć nową specyfikację szablonu dla każdego szablonu lub pobrać szablon, który tworzy specyfikację szablonu. Skrypt nie usuwa szablonu z galerii szablonów.

1. Skopiuj [skrypt migracji](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Zapisz kopię lokalną o nazwie *Migrate-GalleryItems.ps1*.
1. Aby utworzyć nowe specyfikacje szablonu, podaj wartości `-ResourceGroupName` `-Location` parametrów i. 

   Ustaw `ItemsToExport` , aby `MyGalleryItems` wyeksportować szablony. Ustaw tę wartość, aby `AllGalleryItems` wyeksportować wszystkie szablony, do których masz dostęp.

   Poniższy przykład tworzy nowe specyfikacje szablonu dla każdego szablonu w grupie zasobów o nazwie **migratedRG**. Skrypt tworzy grupę zasobów, jeśli nie istnieje.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Aby pobrać szablony, których można użyć do tworzenia specyfikacji szablonu, nie należy podawać wartości dla grupy zasobów lub lokalizacji. Zamiast tego należy określić `-ExportToFile` . Szablon nie jest taki sam jak szablon w galerii. Zamiast tego zawiera zasób specyfikacji szablonu, który tworzy specyfikację szablonu dla szablonu.

   Poniższy przykład pobiera szablony bez tworzenia specyfikacji szablonu.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Aby dowiedzieć się, jak wdrożyć szablon, który tworzy specyfikację szablonu, zobacz [Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu (wersja zapoznawcza)](quickstart-create-template-specs.md).

Aby uzyskać więcej informacji na temat skryptu i jego parametrów, zobacz [Tworzenie TemplateSpecs z szablonu galerii szablonów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Ręczna konwersja za poorednictwem portalu

Możesz ręcznie kopiować szablony z galerii do nowej specyfikacji szablonu.

1. Otwórz [Szablony (wersja zapoznawcza)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) w portalu.
1. Wybierz szablon do migracji.
1. Wybierz pozycję **Wyświetl szablon**.
1. Skopiuj zawartość szablonu.
1. Na pasku wyszukiwania portalu Wyszukaj **specyfikacje szablonu**. Wybierz tę opcję.
1. Wybierz pozycję **Utwórz specyfikację szablonu**.
1. Podaj wartości dla nazwy, subskrypcji, grupy zasobów, lokalizacji i wersji.
1. Wybierz pozycję **Dalej: Edytuj szablon**.
1. W przypadku zawartości szablonu wklej szablon skopiowany z galerii szablonów.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Po pomyślnym zakończeniu walidacji wybierz pozycję **Utwórz**.

Jeśli trzeba udostępnić specyfikację szablonu innym użytkownikom w organizacji, należy [ustawić kontrolę dostępu opartą na rolach](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) dla grupy lub użytkowników, którzy potrzebują dostępu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat specyfikacji szablonów, zobacz [Tworzenie i wdrażanie specyfikacji szablonu](template-specs.md).
