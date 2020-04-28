---
title: Optymalizacja siatki programu Visual Studio dla usługi Azure Service Fabric
description: W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75497980"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optymalizacja wydajności programu Visual Studio dla projektów usługi Service Fabric Mesh

W tym artykule przedstawiono, jak zoptymalizować wydajność programu Visual Studio pod kątem projektów usługi Service Fabric Mesh, tak aby uruchomienie pierwszego debugowania (F5) trwało znacznie krócej.  

## <a name="change-visual-studio-settings"></a>Zmienianie ustawień programu Visual Studio
 
W programie Visual Studio w obszarze **Narzędzia** > **Opcje**  > **Service Fabric siatka narzędzia** > **Ogólne**można dostosować następujące ustawienia:

- Ustawienie **Ściągaj wymagane obrazy platformy Docker przy ładowaniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu pobierania obrazu podczas ładowania projektu.  
- Ustawienie **Wdróż aplikację przy otwieraniu projektu** umożliwia szybsze rozpoczęcie pierwszego debugowania (F5) przez uruchomienie procesu wdrażania przy otwieraniu projektu.  
- Opcja **Usuń aplikację przy zamykaniu projektu** umożliwia odzyskanie zasobów (procesora, pamięci RAM) przydzielonych do aplikacji przez usunięcie aplikacji usługi Mesh przy zamykaniu projektu.  

Gdy w oknie danych wyjściowych narzędzi Service Fabric Tools są wyświetlane komunikaty odnoszące się do „ściągania obrazów”, „przygotowywania” i „usuwania aplikacji” przez program Visual Studio, dotyczy to powyższych ustawień. Te ustawienia można wyłączyć.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Debugowanie aplikacji usługi Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)