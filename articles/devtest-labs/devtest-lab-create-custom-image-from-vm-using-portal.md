---
title: Tworzenie niestandardowego obrazu Azure DevTest Labs na podstawie maszyny wirtualnej | Microsoft Docs
description: Dowiedz się, jak utworzyć niestandardowy obraz w Azure DevTest Labs z zainicjowanej maszyny wirtualnej przy użyciu Azure Portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2020
ms.author: spelluru
ms.openlocfilehash: 3c11c8a4076f868e3f8896dac80147a485f166d3
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896235"
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

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Utwórz stronę obrazu niestandardowego":::
1. Wybierz, czy program Sysprep został uruchomiony na maszynie wirtualnej. Jeśli program Sysprep nie został uruchomiony na maszynie wirtualnej, określ, czy program Sysprep ma być uruchamiany na maszynie wirtualnej po utworzeniu obrazu niestandardowego.
1. Po zakończeniu tworzenia obrazu niestandardowego wybierz **przycisk OK** .

    Po kilku minutach obraz niestandardowy zostanie utworzony i będzie przechowywany w ramach konta magazynu laboratorium. Gdy użytkownik laboratorium chce utworzyć nową maszynę wirtualną, obraz jest dostępny na liście obrazów podstawowych.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="niestandardowy obraz dostępny na liście obrazów podstawowych":::

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu

- [Obrazy niestandardowe lub formuły?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
