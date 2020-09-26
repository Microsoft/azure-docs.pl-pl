---
title: 'ML Studio (klasyczny): moduły programu PowerShell — platforma Azure'
description: Za pomocą programu PowerShell Twórz i Zarządzaj Azure Machine Learning Studio (klasyczne) obszary robocze, eksperymenty, usługi sieci Web i nie tylko.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 59a0989157a87c7fb951c8b6d9c7b70474626f42
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361878"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduły programu PowerShell dla Azure Machine Learning Studio (klasyczne)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Korzystając z modułów programu PowerShell, można programowo zarządzać zasobami programu Studio (klasycznymi), takimi jak obszary robocze, zestawy danych i usługi sieci Web.

Można korzystać z zasobów Studio (klasycznych) przy użyciu trzech modułów programu PowerShell:

* [Azure PowerShell AZ](#az-rm) wydana w 2018, obejmuje wszystkie funkcje AzureRM, chociaż z różnymi nazwami poleceń cmdlet
* [AzureRM](#az-rm) wydane w 2016, zastąpione przez PowerShell AZ
* [Azure Machine Learning PowerShell Classic](#classic) wydanej w 2016

Chociaż te moduły programu PowerShell mają różne podobieństwa, każdy z nich jest przeznaczony dla konkretnych scenariuszy. W tym artykule opisano różnice między modułami programu PowerShell i można zdecydować, które z nich wybrać.  

Zapoznaj się z poniższą [tabelą pomocy technicznej](#support-table) , aby zobaczyć, które zasoby są obsługiwane przez poszczególne moduły. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell AZ i AzureRM

AZ jest teraz zamierzonym modułem programu PowerShell na potrzeby współdziałania z platformą Azure i zawiera wszystkie poprzednie funkcje programu AzureRM. AzureRM będzie nadal otrzymywać poprawki błędów, ale nie otrzyma żadnych nowych poleceń cmdlet ani funkcji.  AZ i AzureRM obu rozwiązań zarządzania wdrożonych przy użyciu modelu wdrażania **Azure Resource Manager** . Te zasoby obejmują obszary robocze Studio (klasyczne) i Studio (klasyczne) "nowe" usług sieci Web. 

Środowisko klasyczne programu PowerShell można zainstalować obok opcji AZ lub AzureRM w celu pokrycia typów zasobów "New" i "klasyczny". Jednak nie zaleca się instalowania w tym samym czasie AZ i AzureRM. Aby podjąć decyzję między AZ i AzureRM, firma Microsoft zaleca AZ dla wszystkich przyszłych wdrożeń.  Dowiedz się więcej na temat AZ a AzureRM i ścieżki migracji w temacie [wprowadzenie do Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Aby rozpocząć pracę z AZ, postępuj zgodnie z instrukcjami dotyczącymi [instalacji platformy Azure AZ](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Środowisko klasyczne programu PowerShell

Program Studio (klasyczny) [środowisko klasyczne programu PowerShell](https://aka.ms/amlps) umożliwia zarządzanie zasobami wdrożonymi przy użyciu **klasycznego modelu wdrażania**. Te zasoby obejmują zasoby użytkownika programu Studio (klasycznego), "klasyczne" usługi sieci Web i "klasyczne" punkty końcowe usługi sieci Web.

Firma Microsoft zaleca jednak użycie modelu wdrażania Menedżer zasobów dla wszystkich przyszłych zasobów w celu uproszczenia wdrażania zasobów i zarządzania nimi. Jeśli chcesz dowiedzieć się więcej na temat modeli wdrażania, zobacz artykuł [Azure Resource Manager a klasyczna Deployment](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) .

Aby rozpocząć pracę z klasycznym programem PowerShell, Pobierz [pakiet Release](https://github.com/hning86/azuremlps/releases) z usługi GitHub i postępuj zgodnie z [instrukcjami dotyczącymi instalacji](https://github.com/hning86/azuremlps/blob/master/README.md). Instrukcje wyjaśniają, jak odblokować pobraną/niespakowaną bibliotekę DLL, a następnie zaimportować ją do środowiska programu PowerShell.

Środowisko klasyczne programu PowerShell można zainstalować obok opcji AZ lub AzureRM w celu pokrycia typów zasobów "New" i "klasyczny".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabela obsługi programu PowerShell


|  **AZ** |  **Środowisko klasyczne programu PowerShell** |
| --- | --- | --- |
| Tworzenie/usuwanie obszarów roboczych | [Szablony usługi Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Zarządzanie planami zobowiązań obszaru roboczego | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Zarządzanie użytkownikami obszaru roboczego |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Zarządzanie usługami sieci Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nowe" usługi sieci Web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasyczne" usługi sieci Web) |
| Zarządzanie punktami końcowymi/kluczami usługi sieci Web |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Zarządzanie zestawami danych użytkownika/modelami przeszkolonymi| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Zarządzanie eksperymentami użytkowników |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Zarządzanie modułami niestandardowymi | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z pełną dokumentacją tego modułu programu PowerShell:
* [Środowisko klasyczne programu PowerShell](https://aka.ms/amlps)
* [Moduł Az programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
