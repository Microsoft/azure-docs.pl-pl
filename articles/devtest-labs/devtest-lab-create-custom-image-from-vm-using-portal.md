---
title: Tworzenie niestandardowego obrazu Azure DevTest Labs na podstawie maszyny wirtualnej | Microsoft Docs
description: Dowiedz się, jak utworzyć niestandardowy obraz w Azure DevTest Labs z zainicjowanej maszyny wirtualnej przy użyciu Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87277031"
---
# <a name="create-a-custom-image-from-a-vm"></a>Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Można utworzyć obraz niestandardowy na podstawie zainicjowanej maszyny wirtualnej, a następnie użyć tego obrazu niestandardowego do utworzenia identycznych maszyn wirtualnych. Poniższe kroki ilustrują sposób tworzenia obrazu niestandardowego z poziomu maszyny wirtualnej:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.  

1. W okienku głównym laboratorium wybierz pozycję **moje maszyny wirtualne**.
 
1. W okienku **moje maszyny wirtualne** wybierz maszynę wirtualną, z której chcesz utworzyć niestandardowy obraz.

1. W okienku zarządzanie maszyną wirtualną wybierz pozycję **Utwórz obraz niestandardowy** w obszarze **operacje**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Utwórz niestandardowy element menu obrazu":::
1. W okienku **obraz niestandardowy** wprowadź nazwę i opis obrazu niestandardowego. Te informacje są wyświetlane na liście baz danych podczas tworzenia maszyny wirtualnej. Obraz niestandardowy będzie zawierać dysk systemu operacyjnego i wszystkie dyski z danymi dołączone do maszyny wirtualnej.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Utwórz niestandardowy element menu obrazu":::
1. Wybierz, czy program Sysprep został uruchomiony na maszynie wirtualnej. Jeśli program Sysprep nie został uruchomiony na maszynie wirtualnej, określ, czy program Sysprep ma być uruchamiany na maszynie wirtualnej po utworzeniu obrazu niestandardowego.
1. Po zakończeniu tworzenia obrazu niestandardowego wybierz **przycisk OK** .

    Po kilku minutach obraz niestandardowy zostanie utworzony i będzie przechowywany w ramach konta magazynu laboratorium. Gdy użytkownik laboratorium chce utworzyć nową maszynę wirtualną, obraz jest dostępny na liście obrazów podstawowych.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="Utwórz niestandardowy element menu obrazu":::

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu

- [Obrazy niestandardowe lub formuły?](./devtest-lab-faq.md#blog-post)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
