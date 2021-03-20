---
title: Tworzenie niestandardowego obrazu Azure DevTest Labs z pliku VHD | Microsoft Docs
description: Dowiedz się, jak utworzyć niestandardowy obraz w Azure DevTest Labs z pliku VHD przy użyciu Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289411"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Tworzenie obrazu niestandardowego na podstawie pliku VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przeprowadzą Cię przez proces tworzenia niestandardowego obrazu z pliku VHD przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.  

1. W okienku głównym laboratorium wybierz pozycję **Konfiguracja i zasady**. 

1. W okienku **Konfiguracja i zasady** wybierz pozycję **obrazy niestandardowe**.

1. W okienku **obrazy niestandardowe** wybierz pozycję **+ Dodaj**.

    ![Dodawanie obrazu niestandardowego](./media/devtest-lab-create-template/add-custom-image.png)

1. Wprowadź nazwę obrazu niestandardowego. Ta nazwa jest wyświetlana na liście obrazów podstawowych podczas tworzenia maszyny wirtualnej.

1. Wprowadź opis obrazu niestandardowego. Ten opis jest wyświetlany na liście obrazów podstawowych podczas tworzenia maszyny wirtualnej.

1. W obszarze **Typ systemu operacyjnego** wybierz opcję **Windows** lub **Linux**.

    - W przypadku wybrania opcji **Windows** należy określić za pomocą pola wyboru, czy program *Sysprep* został uruchomiony na maszynie. 
    - W przypadku wybrania systemu **Linux** należy określić za pomocą pola wyboru *, czy cofanie aprowizacji zostało uruchomione* na komputerze. 

1. Wybierz **dysk VHD** z menu rozwijanego. Jest to wirtualny dysk twardy, który zostanie użyty do utworzenia nowego obrazu niestandardowego. W razie potrzeby wybierz opcję **przekazania wirtualnego dysku twardego przy użyciu programu PowerShell**.

1. Możesz również wprowadzić nazwę planu, ofertę planu i zaplanować wydawcę, jeśli obraz użyty do utworzenia obrazu niestandardowego nie jest obrazem licencjonowanym (opublikowanym przez firmę Microsoft).

   - **Nazwa planu:** Wprowadź nazwę obrazu portalu Marketplace, z którego jest tworzony obraz niestandardowy 
   - **Oferta planu:** Wprowadź produkt (ofertę) obrazu portalu Marketplace, z którego jest tworzony obraz niestandardowy 
   - **Wydawca planu:** Wprowadź wydawcę obrazu z witryny Marketplace, z którego jest tworzony obraz niestandardowy

   > [!NOTE]
   > Jeśli obraz, którego używasz do tworzenia obrazu niestandardowego, **nie** jest obrazem licencjonowanym, te pola są puste i mogą być wypełnione w przypadku wybrania opcji. Jeśli obraz **jest** licencjonowanym obrazem, pola są wypełniane automatycznie przy użyciu informacji o planie. Jeśli spróbujesz zmienić je w tym przypadku, zostanie wyświetlony komunikat ostrzegawczy.
   >
   >

1. Wybierz **przycisk OK** , aby utworzyć obraz niestandardowy.

Po kilku minutach obraz niestandardowy zostanie utworzony i będzie przechowywany w ramach konta magazynu laboratorium. Gdy użytkownik laboratorium chce utworzyć nową maszynę wirtualną, obraz jest dostępny na liście obrazów podstawowych.

![Niestandardowy obraz dostępny na liście obrazów podstawowych](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu

- [Obrazy niestandardowe lub formuły?](./devtest-lab-faq.md#blog-post)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie maszyny wirtualnej do laboratorium](./devtest-lab-add-vm.md)
