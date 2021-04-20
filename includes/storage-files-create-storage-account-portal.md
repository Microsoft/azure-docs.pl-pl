---
title: storage-files-create-storage-account-portal
description: Utwórz konto magazynu dla usługi Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717839"
---
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

Aby utworzyć konto magazynu:

1. W menu po lewej stronie **+** wybierz pozycję , aby utworzyć zasób.
1. Wybierz **pozycję Konto magazynu,** aby utworzyć konto magazynu.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Zrzut ekranu przedstawiający opcję konta magazynu w bloku tworzenia zasobu." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. W polu **Nazwa** wprowadź ciąg *mystorageacct*, a po nim kilka losowo wybranych liczb, dopóki nie zostanie wyświetlony zielony znacznik wyboru wskazujący, że nazwa jest unikatowa. Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zanotuj nazwę konta magazynu. Użyjesz jej później. 
1. W **jęz.** Wydajności zachowaj wartość domyślną **Standardowa**.
1. W polu **Replikacja** wybierz wartość **Magazyn lokalnie nadmiarowy (LRS)**.
1. W obszarze **Subskrypcja** wybierz subskrypcję, której użyto do utworzenia konta magazynu. Jeśli masz tylko jedną subskrypcję, powinna być ona wartością domyślną.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę *myResourceGroup*.
1. W polu **Lokalizacja** wybierz pozycję **Wschodnie stany USA 2**.
1. Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć wdrażanie.