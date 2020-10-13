---
title: Optymalizacja siatki programu Visual Studio dla usługi Azure Service Fabric
description: W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: aa7a959128d3bcdfcce67d3abeac245975339a9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840306"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optymalizacja wydajności programu Visual Studio dla projektów usługi Service Fabric Mesh

W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.  

## <a name="change-visual-studio-settings"></a>Zmienianie ustawień programu Visual Studio
 
W programie Visual Studio w obszarze **Narzędzia**  >  **Opcje**   >  **Service Fabric siatka narzędzia**  >  **Ogólne**można dostosować następujące ustawienia:

- Ustawienie **Ściągaj wymagane obrazy platformy Docker przy ładowaniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu pobierania obrazu podczas ładowania projektu.  
- Ustawienie **Wdróż aplikację przy otwieraniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu wdrażania przy otwieraniu projektu.  
- Opcja **Usuń aplikację przy zamykaniu projektu** umożliwia odzyskanie zasobów (procesora, pamięci RAM) przydzielonych do aplikacji przez usunięcie aplikacji usługi Mesh przy zamykaniu projektu.  

Gdy w oknie danych wyjściowych narzędzi Service Fabric Tools są wyświetlane komunikaty odnoszące się do „ściągania obrazów”, „przygotowywania” i „usuwania aplikacji” przez program Visual Studio, dotyczy to powyższych ustawień. Te ustawienia można wyłączyć.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Debugowanie aplikacji usługi Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)