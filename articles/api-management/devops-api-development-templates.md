---
title: Ciągłej integracji/ciągłego wdrażania API Management platformy Azure przy użyciu szablonów ARM
description: Wprowadzenie do interfejsu API DevOps za pomocą usługi Azure API Management przy użyciu szablonów Azure Resource Manager do zarządzania wdrożeniami interfejsów API w potoku ciągłej integracji/ciągłego dostarczania
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183162"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Ciągłej integracji/ciągłego dostarczania API Management przy użyciu szablonów Azure Resource Manager

W tym artykule pokazano, jak używać interfejsu API DevOps z platformą Azure API Management za pomocą szablonów Azure Resource Manager. Dzięki strategicznej wartości interfejsów API potok ciągłej integracji i ciągłego wdrażania (CI/CD) stał się ważnym aspektem programowania interfejsu API. Umożliwia ona organizacjom Automatyzowanie wdrażania zmian interfejsu API bez konieczności ręcznego wykonywania czynności, wykrywania wcześniejszych problemów i ostatecznego dostarczania wartości użytkownikom. 

Aby uzyskać szczegółowe informacje, narzędzia i przykłady kodu w celu zaimplementowania podejścia DevOps opisanego w tym artykule, zobacz [zestaw Resource Kit dla platformy Azure API Management DevOps w usłudze](https://github.com/Azure/azure-api-management-devops-resource-kit) GitHub. Ponieważ klienci przedstawiają szeroką gamę kultur inżynieryjnych i istniejących rozwiązań do automatyzacji, podejście nie jest rozwiązaniem o jednym rozmiarze.

## <a name="the-problem"></a>Problem

Organizacje dzisiaj zwykle mają wiele środowisk wdrażania (takich jak programowanie, testowanie i produkcja) i używają osobnych wystąpień API Management dla każdego środowiska. Niektóre wystąpienia są współużytkowane przez wiele zespołów programistycznych, którzy są odpowiedzialni za różne interfejsy API z różnymi wersjami cadences.

W związku z tym klienci napotykają następujące wyzwania:

* Jak zautomatyzować wdrażanie interfejsów API w API Management
* Jak migrować konfiguracje z jednego środowiska do innego
* Jak uniknąć zakłóceń między różnymi zespołami programistycznymi, które współużytkują to samo wystąpienie API Management

## <a name="manage-configurations-in-resource-manager-templates"></a>Zarządzanie konfiguracjami w szablonach Menedżer zasobów

Na poniższej ilustracji przedstawiono proponowane podejście. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagram, który ilustruje DevOps z API Management.":::

W tym przykładzie istnieją dwa środowiska wdrażania: *programowanie* i *produkcja*. Każdy z nich ma własne wystąpienie API Management. 

* Deweloperzy interfejsu API mają dostęp do wystąpienia programowania i mogą go używać do tworzenia i testowania interfejsów API. 
* Wyznaczeni zespołu o nazwie *wydawcy interfejsu API* zarządza wystąpieniem produkcyjnym.

Najważniejszym podejściem jest zachowanie wszystkich konfiguracji API Management w [szablonach Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Organizacja powinna zachować te szablony w systemie kontroli źródła, takim jak Git. Jak pokazano na obrazie, repozytorium wydawcy zawiera wszystkie konfiguracje wystąpienia API Management produkcyjnego w kolekcji szablonów:

|Template  |Opis  |
|---------|---------|
|Szablon usługi     | Konfiguracje na poziomie usług API Management wystąpienia, takie jak warstwa cenowa i domeny niestandardowe.         |
|Szablony udostępnione     |  Udostępnione zasoby w ramach wystąpienia API Management, takie jak grupy, produkty i rejestratory.    |
|Szablony interfejsu API     |  Konfiguracje interfejsów API i ich zasobów: operacje, zasady, ustawienia diagnostyczne.        |
|Główny szablon (główny)     |   Łączy wszystko, [łącząc](../azure-resource-manager/templates/linked-templates.md) się ze wszystkimi szablonami i wdrażając je w określonej kolejności. Aby wdrożyć wszystkie konfiguracje w wystąpieniu API Management, wdróż szablon główny. Każdy szablon można również wdrożyć osobno.       |

Deweloperzy interfejsu API będą rozwidlenia repozytorium wydawcy do repozytorium deweloperów i pracować nad zmianami dotyczącymi ich interfejsów API. W większości przypadków koncentrują się na szablonach interfejsu API dla swoich interfejsów API i nie trzeba zmieniać szablonów udostępnionych lub usług.

## <a name="migrate-configurations-to-templates"></a>Migrowanie konfiguracji do szablonów
Deweloperzy interfejsu API czołowe wyzwania podczas pracy z szablonami Menedżer zasobów:

* Deweloperzy interfejsu API często pracują ze [specyfikacją openapi](https://github.com/OAI/OpenAPI-Specification) i mogą nie być zaznajomieni ze schematami Menedżer zasobów. Ręczne tworzenie szablonów może być podatne na błędy. 

   Narzędzie o nazwie [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) w zestawie Resource Kit może pomóc zautomatyzować tworzenie szablonów interfejsu API na podstawie otwartego pliku specyfikacji interfejsu API. Ponadto deweloperzy mogą podawać API Management zasady dla interfejsu API w formacie XML. 

* W przypadku klientów, którzy już używają API Management, inne wyzwanie polega na wyodrębnieniu istniejących konfiguracji do Menedżer zasobów szablonów. W przypadku tych klientów narzędzie o nazwie [Ekstraktor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) w zestawie Resource Kit może pomóc w generowaniu szablonów przez wyodrębnienie konfiguracji z ich wystąpień API Management.  

## <a name="workflow"></a>Przepływ pracy

* Po zakończeniu opracowywania i testowania interfejsu API przez deweloperów interfejsu API i wygenerowania szablonów interfejsu API można przesłać żądanie ściągnięcia w celu scalenia zmian w repozytorium wydawcy. 

* Wydawcy interfejsu API mogą sprawdzić poprawność żądania ściągnięcia i upewnić się, że zmiany są bezpieczne i zgodne. Można na przykład sprawdzić, czy tylko protokół HTTPS może komunikować się z interfejsem API. Większość walidacji może być zautomatyzowana jako krok w potoku ciągłej integracji/ciągłego wdrażania.

* Gdy zmiany zostaną zatwierdzone i scalone, wydawcy interfejsu API mogą zdecydować się na wdrożenie ich w wystąpieniu produkcyjnym zgodnie z harmonogramem lub na żądanie. Wdrażanie szablonów można zautomatyzować za pomocą [akcji usługi GitHub](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)lub innych narzędzi.


Dzięki temu organizacja może zautomatyzować wdrażanie zmian interfejsu API w wystąpieniach API Management, a także łatwo wspierać zmiany z jednego środowiska do innego. Ponieważ różne zespoły programistyczne interfejsu API będą działać na różnych zestawach szablonów i plikach interfejsu API, uniemożliwiają zakłócenia między różnymi zespołami.

## <a name="video"></a>Wideo

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Następne kroki

- Więcej informacji, narzędzi i przykładowych szablonów można znaleźć w [zestawie Resource Kit dla platformy Azure API Management DevOps](https://github.com/Azure/azure-api-management-devops-resource-kit) .