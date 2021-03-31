---
title: Zaktualizuj skrypt wdrażania szablonów programu Visual Studio, aby użyć polecenia AZ PowerShell
description: Aktualizuj skrypt wdrożenia szablonu programu Visual Studio z AzureRM do AZ PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963873"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aktualizuj skrypt wdrożenia szablonu programu Visual Studio, aby użyć polecenia AZ PowerShell module

Program Visual Studio 16,4 obsługuje używanie modułu AZ PowerShell w skrypcie wdrażania szablonu. Jednak program Visual Studio nie instaluje automatycznie tego modułu. Aby użyć AZ module, należy wykonać cztery kroki:

1. [Odinstaluj moduł AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Zainstaluj AZ module](/powershell/azure/install-az-ps)
1. Aktualizowanie programu Visual Studio do 16,4
1. Zaktualizuj skrypt wdrożenia w projekcie.

## <a name="update-visual-studio-to-164"></a>Aktualizowanie programu Visual Studio do 16,4

Zaktualizuj instalację programu Visual Studio do wersji 16,4 lub nowszej. Podczas uaktualniania upewnij się, że składnik Azure PowerShell nie jest zaznaczony. Ponieważ zainstalowano AZ module za pomocą galerii, nie chcesz ponownie instalować modułu AzureRM.

Jeśli uaktualniono już do 16,4, a składnik Azure PowerShell został sprawdzony, można go odinstalować, uruchamiając Instalator programu Visual Studio. Nie wybieraj składnika Azure PowerShell w obciążeniu platformy Azure ani na stronie poszczególnych składników.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizowanie skryptu wdrożenia w projekcie

Zamień wszystkie wystąpienia ciągu "AzureRm" na "AZ" w skrypcie wdrożenia. Możesz odwołać się do poprawek w tym [rejestrze](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) , aby zobaczyć zmiany. Aby uzyskać więcej informacji na temat uaktualniania skryptów do modułu AZ module, zobacz [migrowanie Azure PowerShell z AzureRM do AZ](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z projektu programu Visual Studio, zobacz [Tworzenie i wdrażanie projektów grupy zasobów platformy Azure za pośrednictwem programu Visual Studio](create-visual-studio-deployment-project.md).
