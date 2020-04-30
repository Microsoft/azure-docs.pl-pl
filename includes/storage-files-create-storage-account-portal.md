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
ms.openlocfilehash: d4054760c77a7a70b7ed84a9f95b88a3bcf2bda3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76020819"
---
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

Aby utworzyć konto magazynu:

1. W menu po lewej stronie wybierz **+** pozycję, aby utworzyć zasób.
2. W polu wyszukiwania wprowadź ciąg **konto magazynu**, wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka**, a następnie wybierz pozycję **Utwórz**.
    ![Zrzut ekranu przedstawiający pozycję konta magazynu w oknie dialogowym wyszukiwania zasobów](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. W polu **Nazwa** wprowadź ciąg *mystorageacct*, a po nim kilka losowo wybranych liczb, dopóki nie zostanie wyświetlony zielony znacznik wyboru wskazujący, że nazwa jest unikatowa. Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zanotuj nazwę konta magazynu. Użyjesz jej później. 
4. W obszarze **model wdrażania**pozostaw wartość domyślną **Menedżer zasobów**. Aby dowiedzieć się więcej na temat różnic między usługą Azure Resource Manager a klasycznym modelem wdrożenia, zobacz [Understand deployment models and the state of your resources (Omówienie modeli wdrażania i stanu zasobów)](../articles/azure-resource-manager/management/deployment-models.md).
5. W polu **Rodzaj konta** wybierz wartość **StorageV2**. Aby dowiedzieć się więcej na temat różnych rodzajów kont magazynu, zobacz [Understand Azure storage accounts (Omówienie kont usługi Azure Storage)](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. W polu **Wydajność** zachowaj wartość domyślną **Standard Storage**. Usługa Azure Files aktualnie obsługuje tylko magazyn w wersji Standardowa. Nawet w przypadku wybrania magazynu Azure Premium Storage udział plików będzie przechowywany w magazynie Standard Storage.
7. W polu **Replikacja** wybierz wartość **Magazyn lokalnie nadmiarowy (LRS)**. 
8. Zawsze zalecanym, aby w obszarze **Wymagany bezpieczny transfer** wybierać wartość **Włączono**. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Understand encryption in-transit (Omówienie szyfrowania podczas przesyłania danych)](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. W obszarze **Subskrypcja** wybierz subskrypcję, której użyto do utworzenia konta magazynu. Jeśli masz tylko jedną subskrypcję, powinna być ona wartością domyślną.
10. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę *myResourceGroup*.
11. W polu **Lokalizacja** wybierz pozycję **Wschodnie stany USA 2**.
12. W polu **Sieci wirtualne** pozostaw opcję domyślną **Wyłączone**. 
13. Aby ułatwić znajdowanie konta magazynu, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.
14. Po zakończeniu kliknij pozycję **Utwórz**, aby rozpocząć wdrażanie.