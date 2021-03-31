---
title: Konfigurowanie ustawień obrazu portalu Azure Marketplace w Azure DevTest Labs
description: Skonfiguruj, które obrazy w portalu Azure Marketplace mogą być używane podczas tworzenia maszyny wirtualnej w Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86512439"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurowanie ustawień obrazu portalu Azure Marketplace w Azure DevTest Labs
DevTest Labs obsługuje tworzenie maszyn wirtualnych opartych na obrazach portalu Azure Marketplace w zależności od tego, jak zostały skonfigurowane obrazy w portalu Azure Marketplace, które mają być używane w środowisku laboratoryjnym. W tym artykule opisano, jak określić, w jaki sposób można używać obrazów portalu Azure Marketplace podczas tworzenia maszyn wirtualnych w środowisku laboratoryjnym. Gwarantuje to, że Twój zespół ma dostęp tylko do potrzebnych obrazów portalu Marketplace. 

## <a name="specify-allowed-images-for-creating-vms"></a>Określ dozwolone obrazy do tworzenia maszyn wirtualnych
Wykonaj następujące kroki, aby określić, które obrazy w portalu Azure Marketplace są dozwolone podczas tworzenia maszyny wirtualnej. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz laboratorium. 
4. Na stronie głównej strony laboratorium wybierz pozycję **Konfiguracja i zasady**.
5. Na stronie **Konfiguracja i zasady** laboratorium w obszarze **bazy maszyn wirtualnych** wybierz pozycję **obrazy z witryny Marketplace**.
6. Określ, czy chcesz, aby wszystkie kwalifikowane obrazy portalu Azure Marketplace były dostępne do użycia jako podstawa nowej maszyny wirtualnej. Jeśli wybierzesz opcję **tak**, wszystkie obrazy w portalu Azure Marketplace, które spełniają wszystkie następujące kryteria, są dozwolone w laboratorium:
   
   * Obraz tworzy pojedynczą maszynę wirtualną **i**
   * Obraz używa Azure Resource Manager do inicjowania obsługi maszyn wirtualnych, **a**
   * Obraz nie wymaga zakupu dodatkowego planu licencjonowania
     
     Jeśli chcesz, aby żadne obrazy nie były dozwolone lub chcesz określić, które obrazy mogą być używane, wybierz pozycję **nie**.
     
     ![Opcja zezwalania na używanie wszystkich obrazów z portalu Marketplace jako obrazów podstawowych dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Jeśli wybierzesz pozycję **nie** do poprzedniego kroku, pole wyboru **dozwolone obrazy/Zaznacz wszystko** jest włączone. 
   Tej opcji można użyć razem z polem wyszukiwania, aby szybko wybrać lub usunąć zaznaczenie wszystkich elementów wyświetlanych na liście.
   * Wybierz obrazy w portalu Azure Marketplace, które mają być dozwolone na potrzeby tworzenia maszyn wirtualnych, sprawdzając odpowiednie pola wyboru każdego obrazu.
   * Nie zaznaczaj niczego z listy, jeśli nie chcesz zezwalać na używanie jakichkolwiek obrazów portalu Azure Marketplace w środowisku laboratoryjnym.
   
     ![Możesz określić, które obrazy w portalu Azure Marketplace mogą być używane jako obrazy podstawowe dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie możesz znaleźć określonego obrazu do włączenia dla laboratorium, wykonaj następujące czynności: 

- Sprawdź, czy można wyświetlić obraz podczas tworzenia maszyny wirtualnej obliczeniowej.
- Obraz może nie być dostępny w typie używanej subskrypcji. Skontaktuj się z administratorem subskrypcji dla typu subskrypcji (na przykład: MSDN, Free, płatność zgodnie z rzeczywistym użyciem itp.). 
- Obsługa obrazów generacji 2 w programie DevTest Labs jest ograniczona. Jeśli dla obrazu są dostępne wersje generacji 1 i Gen 2, DevTest Labs wyświetla tylko wersję Gen 1 obrazu podczas tworzenia maszyny wirtualnej. Obejście polega na utworzeniu niestandardowego obrazu generacji 2 poza laboratorium i użyciu go do utworzenia maszyny wirtualnej. Jeśli dostępna jest tylko wersja 2 generacji obrazu, jest ona obsługiwana i widoczna na liście przez DevTest Labs. 
      


## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu sposobu, w jaki obrazy portalu Azure Marketplace są dozwolone podczas tworzenia maszyny wirtualnej, następnym krokiem jest [dodanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md).

