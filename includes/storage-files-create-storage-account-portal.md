---
title: storage-files-create-storage-account-portal
description: Utwórz konto magazynu dla usługi Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: ad9f3a115ed226b492a08dae29db61029d9098eb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558995"
---
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

Aby utworzyć konto magazynu:

1. W menu po lewej stronie wybierz pozycję, **+** Aby utworzyć zasób.
1. W polu wyszukiwania wprowadź ciąg **konto magazynu**, wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**, a następnie wybierz pozycję **Utwórz**.
    ![Zrzut ekranu przedstawiający pozycję konta magazynu w oknie dialogowym wyszukiwania zasobów](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. W polu **Nazwa** wprowadź ciąg *mystorageacct*, a po nim kilka losowo wybranych liczb, dopóki nie zostanie wyświetlony zielony znacznik wyboru wskazujący, że nazwa jest unikatowa. Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zanotuj nazwę konta magazynu. Użyjesz jej później. 
1. W obszarze **model wdrażania** pozostaw wartość domyślną **Menedżer zasobów**. Aby dowiedzieć się więcej na temat różnic między usługą Azure Resource Manager a klasycznym modelem wdrożenia, zobacz [Understand deployment models and the state of your resources (Omówienie modeli wdrażania i stanu zasobów)](../articles/azure-resource-manager/management/deployment-models.md).
1. W obszarze **wydajność** Zachowaj domyślną wartość **Standard**.
    
    > [!NOTE]
    > Ten przewodnik Szybki Start tworzy standardowy udział plików, ale jeśli chciałeś użyć udziałów plików w warstwie Premium, zamiast tego wybierz opcję **Premium** .

1. W polu **Rodzaj konta** wybierz wartość **StorageV2**. Aby dowiedzieć się więcej na temat różnych rodzajów kont magazynu, zobacz [Understand Azure storage accounts (Omówienie kont usługi Azure Storage)](../articles/storage/common/storage-account-overview.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).

    > [!NOTE]
    > Ten przewodnik Szybki Start tworzy konto ogólnego przeznaczenia w wersji 2. Jeśli chcesz użyć udziałów plików w warstwie Premium, zamiast tego wybierz pozycję **FileStorage** .

1. W polu **Replikacja** wybierz wartość **Magazyn lokalnie nadmiarowy (LRS)**. 
1. Zawsze zalecanym, aby w obszarze **Wymagany bezpieczny transfer** wybierać wartość **Włączono**. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Understand encryption in-transit (Omówienie szyfrowania podczas przesyłania danych)](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
1. W obszarze **Subskrypcja** wybierz subskrypcję, której użyto do utworzenia konta magazynu. Jeśli masz tylko jedną subskrypcję, powinna być ona wartością domyślną.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę *myResourceGroup*.
1. W polu **Lokalizacja** wybierz pozycję **Wschodnie stany USA 2**.
1. W polu **Sieci wirtualne** pozostaw opcję domyślną **Wyłączone**. 
1. Aby ułatwić znajdowanie konta magazynu, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
1. Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć wdrażanie.