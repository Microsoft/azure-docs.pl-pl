---
title: Kontrola dostępu oparta na rolach na platformie Azure — Custom Vision
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób konfigurowania kontroli dostępu opartej na rolach platformy Azure dla projektów Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 096946a1a63c0826381875cd3ce4eaf0129a85df
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146365"
---
# <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Custom Vision obsługuje kontrolę dostępu opartą na rolach (Azure RBAC) na platformie Azure, system autoryzacji służący do zarządzania indywidualnym dostępem do zasobów platformy Azure. Korzystając z funkcji RBAC platformy Azure, można przypisać różnym członkom zespołu różne poziomy uprawnień dla projektów Custom Vision. Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [dokumentację usługi Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Dodawanie przypisania roli do zasobu Custom Vision

Usługę Azure RBAC można przypisać do zasobu Custom Vision. Aby udzielić dostępu do zasobu platformy Azure, Dodaj przypisanie roli.
1. W witrynie [Azure Portal](https://ms.portal.azure.com/) wybierz pozycję **Wszystkie usługi**. 
1. Następnie wybierz **Cognitive Services**i przejdź do określonego zasobu szkoleniowego Custom Vision.
   > [!NOTE]
   > Możesz również skonfigurować usługę Azure RBAC dla całej grupy zasobów, subskrypcji lub grup zarządzania. W tym celu należy wybrać żądany poziom zakresu, a następnie przejść do żądanego elementu (na przykład wybierając pozycję **grupy zasobów** , a następnie klikając pozycję do odpowiedniej grupy zasobów).
1. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku nawigacji po lewej stronie.
1. Wybierz kartę **przypisania ról** , aby wyświetlić przypisania ról dla tego zakresu.
1. Wybierz pozycję **Dodaj**  ->  **Dodaj przypisanie roli**.
1. Z listy rozwijanej **rola** wybierz rolę, którą chcesz dodać.
1. Z listy **Wybierz** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać pole wyboru, aby przeszukać katalog pod kątem nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.
1. Wybierz przycisk **Zapisz**, aby przypisać rolę.

W ciągu kilku minut obiekt docelowy zostanie przypisany do wybranej roli w wybranym zakresie.

## <a name="custom-vision-role-types"></a>Custom Vision typy ról

Skorzystaj z poniższej tabeli, aby określić potrzeby dostępu do zasobów Custom Vision.

|Rola  |Uprawnienia  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Pełny dostęp do projektów, w tym możliwość tworzenia, edytowania lub usuwania projektu.        |
|`Cognitive Services Custom Vision Trainer`     | Pełny dostęp z wyjątkiem możliwości tworzenia lub usuwania projektu. Instruktorzy mogą wyświetlać i edytować projekty oraz uczenie, publikować, cofać Publikowanie lub eksportować modele.        |
|`Cognitive Services Custom Vision Labeler`     | Możliwość przekazywania, edytowania lub usuwania obrazów szkoleniowych oraz tworzenia, dodawania, usuwania lub usuwania tagów. Etykiety mogą wyświetlać projekty, ale nie mogą aktualizować niczego innego niż obrazy szkoleniowe i Tagi.         |
|`Cognitive Services Custom Vision Deployment`     | Możliwość publikowania, cofania publikacji lub eksportowania modeli. Narzędzia wdrażania mogą wyświetlać projekty, ale nie mogą aktualizować projektu, obrazów szkoleniowych ani tagów.        |
|`Cognitive Services Custom Vision Reader`     | Możliwość wyświetlania projektów. Czytelnicy nie mogą wprowadzać żadnych zmian.        |
