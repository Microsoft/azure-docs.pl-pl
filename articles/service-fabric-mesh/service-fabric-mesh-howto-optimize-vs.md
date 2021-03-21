---
title: Optymalizacja siatki programu Visual Studio dla usługi Azure Service Fabric
description: W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625756"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optymalizacja wydajności programu Visual Studio dla projektów usługi Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.  

## <a name="change-visual-studio-settings"></a>Zmienianie ustawień programu Visual Studio
 
W programie Visual Studio w obszarze **Narzędzia**  >  **Opcje**   >  **Service Fabric siatka narzędzia**  >  **Ogólne** można dostosować następujące ustawienia:

- Ustawienie **Ściągaj wymagane obrazy platformy Docker przy ładowaniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu pobierania obrazu podczas ładowania projektu.  
- Ustawienie **Wdróż aplikację przy otwieraniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu wdrażania przy otwieraniu projektu.  
- Opcja **Usuń aplikację przy zamykaniu projektu** umożliwia odzyskanie zasobów (procesora, pamięci RAM) przydzielonych do aplikacji przez usunięcie aplikacji usługi Mesh przy zamykaniu projektu.  

Gdy w oknie danych wyjściowych narzędzi Service Fabric Tools są wyświetlane komunikaty odnoszące się do „ściągania obrazów”, „przygotowywania” i „usuwania aplikacji” przez program Visual Studio, dotyczy to powyższych ustawień. Te ustawienia można wyłączyć.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Debugowanie aplikacji usługi Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)