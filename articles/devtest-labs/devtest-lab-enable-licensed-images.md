---
title: Włącz licencjonowany obraz w laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak włączyć licencjonowany obraz w Azure DevTest Labs przy użyciu Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91276360"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Włącz licencjonowany obraz w laboratorium w Azure DevTest Labs

W Azure DevTest Labs licencjonowany obraz to taki, który zawiera warunki i postanowienia — zwykle od innej firmy — która musi zostać zaakceptowana, zanim obraz będzie dostępny dla użytkowników w laboratorium. W poniższych sekcjach opisano sposób pracy z licencjonowanymi obrazami, dzięki czemu są one dostępne do tworzenia maszyn wirtualnych.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Określanie, czy licencjonowany obraz jest dostępny dla użytkowników
Pierwszym krokiem, aby umożliwić użytkownikom tworzenie maszyn wirtualnych przy użyciu licencjonowanego obrazu, jest upewnienie się, że warunki i postanowienia zostały zaakceptowane dla licencjonowanego obrazu. Poniższe kroki pokazują, jak wyświetlić stan oferty dla licencjonowanego obrazu oraz, w razie potrzeby, zaakceptować warunki i postanowienia.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.  

1. W lewym panelu w obszarze **Ustawienia** wybierz pozycję **Konfiguracja i zasady**.

1. W lewym panelu w obszarze **bazy maszyn wirtualnych** wybierz pozycję **obrazy z witryny Marketplace**. 

    ![Element menu obrazów witryny Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Zostanie wyświetlona lista wszystkich dostępnych obrazów w portalu Marketplace, w tym **stanu oferty** dla każdego obrazu.

    ![Lista obrazów portalu Marketplace pokazująca stan oferty dla każdego obrazu](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Obraz licencjonowany pokazuje stan oferty 
    
    - **Warunki zaakceptowane:** obraz licencjonowany jest dostępny dla użytkowników, którzy mogą tworzyć maszyny wirtualne. 
    - **Wymagane przegląd warunków:** licencjonowany obraz nie jest obecnie dostępny dla użytkowników. Warunki i postanowienia licencji muszą zostać zaakceptowane przed użyciem jej do tworzenia maszyn wirtualnych. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Udostępnienie licencjonowanego obrazu użytkownikom laboratorium
Aby upewnić się, że dla użytkowników laboratorium jest dostępny licencjonowany obraz, właściciel laboratorium z uprawnieniami administratora musi najpierw zaakceptować warunki i postanowienia dotyczące tego licencjonowanego obrazu. Włączenie wdrażania programistycznego dla subskrypcji skojarzonej z licencjonowanym obrazem automatycznie akceptuje warunki prawne i zasady zachowania poufności informacji dla tego obrazu. [Praca z obrazami w portalu Marketplace na Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) zawiera dodatkowe informacje na temat programistycznego wdrażania obrazów z portalu Marketplace.

Można włączyć wdrażanie programistyczne dla licencjonowanego obrazu, wykonując następujące czynności:

1. W [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)przejdź do listy **obrazów w portalu Marketplace**.

1. Zidentyfikuj obraz licencjonowany, do którego użytkownicy mają mieć dostęp, ale którego warunki nie zostały zaakceptowane. Na przykład może zostać wyświetlony Data Science Virtual Machine, który pokazuje stan **zaakceptowanych warunków** lub **przegląd warunków**.

    ![Zrzut ekranu przedstawiający Data Science Virtual Machine obrazy z statusem oferty "" warunków zaakceptowanych "i" wymaganym przeglądem warunków "](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Maszyny wirtualne do nauki o danych to obrazy maszyn wirtualnych platformy Azure, wstępnie zainstalowane, skonfigurowane i przetestowane przy użyciu kilku popularnych narzędzi, które są często używane do analizy danych, uczenia maszynowego i szkolenia AI. [Wprowadzenie do usługi Azure Data Science Virtual Machine dla systemów Linux i Windows](../machine-learning/data-science-virtual-machine/overview.md) to świetna okazja do uzyskania informacji na temat DSVMs.
   >
   >

1. W kolumnie **stan oferty** dla obrazu wybierz opcję **warunki przeglądu**.

1. W oknie konfigurowanie wdrożenia programistycznego wybierz pozycję **Włącz**.

    ![Konfigurowanie okna wdrożenia programowego](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > W oknie konfigurowanie wdrożenia programowego może pojawić się wiele subskrypcji. Upewnij się, że włączasz wdrażanie programistyczne tylko dla zamierzonej subskrypcji.
   >
   >


1. Wybierz pozycję **Zapisz**. 

    Na liście obrazów portalu Marketplace ten obraz zawiera teraz **zaakceptowane warunki** i jest dostępny dla użytkowników, którzy mogą tworzyć maszyny wirtualne.

> [!NOTE]
> Użytkownicy mogą tworzyć obraz niestandardowy na podstawie licencjonowanego obrazu. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowego obrazu z pliku VHD](devtest-lab-create-template.md) .
>
>


## <a name="related-blog-posts"></a>Powiązane wpisy w blogu

- [Obrazy niestandardowe lub formuły?](./devtest-lab-faq.md#blog-post)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obrazu niestandardowego z poziomu maszyny wirtualnej](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Tworzenie obrazu niestandardowego na podstawie pliku VHD](devtest-lab-create-template.md)
- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
