---
title: Określ obrazy z witryny Marketplace dla laboratorium w Azure Lab Services
description: W tym artykule opisano sposób określania obrazów z portalu Marketplace, których twórca laboratorium może użyć do tworzenia laboratoriów w ramach konta laboratorium w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434740"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

## <a name="select-images-available-for-labs"></a>Wybierz obrazy dostępne dla laboratoriów
W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Można filtrować listę, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając opcję tylko  / **wyłączone** z listy rozwijanej w górnej części strony. 
    
![Strona Obrazy w portalu Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
    
- Tworzą jedną maszynę wirtualną
- Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
- Nie wymagają zakupu dodatkowego planu licencjonowania

## <a name="disable-images-for-a-lab"></a>Wyłącz obrazy dla laboratorium 
Aby wyłączyć pojedynczy obraz dla laboratorium, wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Wyłącz obraz**. 

![Wyłączanie jednego obrazu](./media/tutorial-setup-lab-account/disable-one-image.png) 

Alternatywnie możesz zaznaczyć pole wyboru przed nazwą obrazu, a następnie wybrać pozycję **Wyłącz wybrane obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów w tym samym czasie, zaznacz pola wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy** na pasku narzędzi. 

![Wyłączanie wielu obrazów](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Włącz obrazy dla laboratorium
Aby włączyć wyłączony obraz, wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie wybierz pozycję **Włącz obraz**. Alternatywnie możesz zaznaczyć pole wyboru przed nazwą obrazu, a następnie wybrać pozycję **Włącz wybrane obrazy** na pasku narzędzi. 

Aby wyłączyć wiele obrazów w tym samym czasie, zaznacz pola wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy** na pasku narzędzi. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Włącz obrazy podczas tworzenia laboratorium
Możesz włączyć więcej obrazów w czasie tworzenia laboratorium: 

1. Zaloguj się do [witryny sieci web Azure Lab Services](https://labs.azure.com) przy użyciu poświadczeń **właściciela konta laboratorium**
2. Wybierz domyślny obraz maszyny wirtualnej lub strzałkę w dół. 
3. Wybierz pozycję **Włącz więcej opcji obrazu**. 

    ![Włącz więcej opcji obrazu](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Postępuj zgodnie z instrukcjami z poprzedniej sekcji, aby włączyć wybrane obrazy. 
5. Być może trzeba będzie zamknąć **nowe okno Lab** i otworzyć je ponownie, aby wyświetlić obrazy wybrane w poprzednim kroku. 



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, dostęp do laboratoriów](how-to-use-classroom-lab.md)
