---
title: Konwertuj szablon portalu na specyfikację szablonu
description: Opisuje sposób konwersji istniejącego szablonu w Galerii Azure Portal na specyfikacje szablonu.
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739110"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Konwertuj galerię szablonów w portalu na specyfikacje szablonu

Azure Portal zapewnia sposób przechowywania szablonów Azure Resource Manager (szablony ARM) na Twoim koncie. **Ta funkcja jest przestarzała.** Aby nadal korzystać z szablonów w tej galerii, przekonwertuj je na [specyfikacje szablonu](template-specs.md).

W tym artykule przedstawiono sposób konwertowania istniejących szablonów w galerii szablonów na specyfikacje szablonu.

W portalu przestarzała funkcja jest nazywana **szablonami (wersja zapoznawcza)**. Aby sprawdzić, czy masz jakieś szablony do przekonwertowania, Wyświetl [galerię szablonów w portalu](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Te szablony mają typ zasobu `Microsoft.Gallery/myareas/galleryitems` .

## <a name="deprecation-of-portal-feature"></a>Wycofanie funkcji portalu

Galeria szablonów w portalu jest przestarzała 21 stycznia 2021. Możesz nadal z niego korzystać do 21 lutego. Od 22 lutego nie można tworzyć nowych szablonów w galerii portalu, ale nadal można wyświetlać i wdrażać istniejące szablony.

22 czerwca funkcja zostanie usunięta z portalu, a wszystkie operacje interfejsu API zostaną zablokowane. Nie będziesz w stanie wyświetlać ani wdrażać żadnych szablonów z galerii.

Przed 22 czerwca należy przeprowadzić migrację wszystkich szablonów, które mają być nadal używane. Aby przeprowadzić migrację szablonów, można użyć jednej z metod przedstawionych w tym artykule. Po usunięciu funkcji należy otworzyć sprawę pomocy technicznej, aby uzyskać wszystkie szablony, które nie zostały poddane migracji.

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
