---
title: Zarządzanie formułami w laboratoriach devtest azure w celu tworzenia maszyn wirtualnych | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak utworzyć formułę z podstawy (obraz niestandardowy, obraz w portalu Marketplace lub innej formuły) lub istniejącej maszyny Wirtualnej.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: ce980167cd88adfada1aa294aafa885184565799
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641161"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Zarządzanie formułami laboratoriów devtest azure

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

W tym artykule pokazano, jak utworzyć formułę z podstawy (obraz niestandardowy, obraz w portalu Marketplace lub innej formuły) lub istniejącej maszyny Wirtualnej. W tym artykule poprowadzą Cię również zarządzanie istniejącymi formułami.

## <a name="create-a-formula"></a>Tworzenie formuły
Każdy, kto ma uprawnienia *Użytkowników* DevTest Labs, może tworzyć maszyny wirtualne przy użyciu formuły jako podstawy. Formuły można tworzyć na dwa sposoby: 

* Od podstawy — użyj, gdy chcesz zdefiniować wszystkie cechy formuły.
* Z istniejącej maszyny Wirtualnej laboratorium — użyj, gdy chcesz utworzyć formułę na podstawie ustawień istniejącej maszyny Wirtualnej.

Aby uzyskać więcej informacji na temat dodawania użytkowników i uprawnień, zobacz [Dodawanie właścicieli i użytkowników w laboratoriach DevTest Platformy Azure](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Tworzenie formuły z bazy
Poniższe kroki prowadzą użytkownika przez proces tworzenia formuły na podstawie obrazu niestandardowego, obrazu w portalu Marketplace lub innej formuły.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

3. Z listy laboratoriów wybierz żądane laboratorium.  

4. Na stronie laboratorium wybierz **formuły (bazy wielokrotnegoużynia)** w menu po lewej stronie.
5. Na stronie **Formuły** wybierz pozycję **+ Dodaj**.
   
    ![Dodawanie formuły](./media/devtest-lab-create-formulas/add-formula.png)

6. Na stronie **Wybierz stronę bazową** wybierz bazę (obraz niestandardowy lub obraz w portalu Marketplace), z której chcesz utworzyć formułę.
7. Na karcie **Ustawienia podstawowe** na stronie **Tworzenie formuły** określ następujące wartości:
   
    * **Nazwa formuły** — wprowadź nazwę formuły. Ta wartość jest wyświetlana na liście obrazów podstawowych podczas tworzenia maszyny Wirtualnej. Nazwa jest sprawdzana podczas jej wpisywania, a jeśli jest nieprawidłowa, komunikat wskazuje wymagania dotyczące prawidłowej nazwy.
    - Wprowadź opcjonalny **opis** formuły. 
    * **Nazwa użytkownika** — umożliwia wprowadzenie nazwy użytkownika, któremu przyznano uprawnienia administratora.
    * **Hasło** — wprowadź — lub wybierz z listy rozwijanej — wartość skojarzoną z kluczem tajnym (hasłem), którego chcesz użyć dla określonego użytkownika. Aby dowiedzieć się więcej o zapisywaniu wpisów tajnych w magazynie kluczy i używaniu ich podczas tworzenia zasobów laboratoryjnych, zobacz [Przechowywanie wpisów tajnych w usłudze Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).

        Wybierz **opcję Użyj zapisanego klucza tajnego,** jeśli chcesz użyć klucza tajnego z usługi Azure Key Vault zamiast hasła. 
    * **Rozmiar maszyny wirtualnej** — wybierz **zmień rozmiar,** aby zmienić rozmiar maszyny Wirtualnej. 
    - **Typ dysku systemu operacyjnego** - wybierz typ dysku, którego chcesz użyć (Standardowy dysk twardy, standardowy dysk SSD lub dysk SSD premium).
    * **Artefakty** — wybierz **pozycję Dodaj lub usuń artefakty,** na której należy wybrać i skonfigurować artefakty, które chcesz dodać do obrazu podstawowego. Aby uzyskać więcej informacji na temat artefaktów, zobacz [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej Azure DevTest Labs.](devtest-lab-artifact-author.md)

        ![Strona ustawień podstawowych](./media/devtest-lab-create-formulas/basic-settings.png)
8. Przełącz się na kartę **Ustawienia zaawansowane** i określ następujące wartości:
    - **Sieć wirtualna** — aby zmienić sieć wirtualną, wybierz pozycję **Zmień sieć wirtualną**. 
    - **Podsieć** - Aby zmienić podsieć, wybierz pozycję **Zmień podsieć**. 
    - **Konfiguracja adresu IP** — określ, czy mają być publiczne, prywatne czy udostępnione adresy IP. Aby uzyskać więcej informacji na temat udostępnionych adresów IP, zobacz [Opis udostępnionych adresów IP w laboratorium devtest platformy Azure](./devtest-lab-shared-ip.md).
    - **Data i godzina wygaśnięcia** — nie można edytować tego pola. 
    - **Spraw, aby ta maszyna była niona** - uczynienie maszyny "podlegalną z roszczeniem" oznacza, że nie zostanie ona przypisana własności w momencie tworzenia. Zamiast tego użytkownicy laboratorium będą mogli przejąć na własność ("roszczenie") komputer na stronie laboratorium.  

        ![Strona ustawień podstawowych](./media/devtest-lab-create-formulas/advanced-settings.png)
8. Wybierz **pozycję Prześlij,** aby utworzyć formułę.

9. Po utworzeniu formuły jest ona wyświetlana na liście na stronie **Formuły.**

### <a name="create-a-formula-from-a-vm"></a>Tworzenie formuły z maszyny Wirtualnej
Poniższe kroki prowadzą użytkownika przez proces tworzenia formuły na podstawie istniejącej maszyny Wirtualnej. 

> [!NOTE]
> Aby utworzyć formułę z maszyny Wirtualnej, maszyna wirtualna musi zostać utworzona po 30 marca 2016 r. 
> 
> 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.  
4. Na stronie **Przegląd** laboratorium wybierz maszynę wirtualną, z której chcesz utworzyć formułę.
   
    ![Maszyny wirtualne laboratoriów](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stronie maszyny Wirtualnej wybierz pozycję **Utwórz formułę (bazę wielokrotnego wyboru).**
   
    ![Tworzenie formuły](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na stronie **Tworzenie formuły** wprowadź **nazwę** i **opis** nowej formuły.
   
    ![Tworzenie strony formuły](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wybierz **przycisk OK,** aby utworzyć formułę.

## <a name="modify-a-formula"></a>Modyfikowanie formuły
Aby zmodyfikować formułę, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.  
4. Na stronie laboratorium wybierz **pozycję Formuły (bazy wielokrotnego pożycie).**
   
    ![Menu Formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stronie **Formuły laboratorium** wybierz formułę, którą chcesz zmodyfikować.
6. Na stronie **Aktualizowanie formuły** wykonuj żądane zmiany i wybierz pozycję **Aktualizuj**.

## <a name="delete-a-formula"></a>Usuwanie formuły
Aby usunąć formułę, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.  
4. Na stronie **Ustawienia** laboratorium wybierz pozycję **Formuły**.
   
    ![Menu Formuły](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stronie **Formuły laboratorium** wybierz wielokropek po prawej stronie formuły, którą chcesz usunąć.
   
    ![Menu Formuły](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. W menu kontekstowym formuły wybierz polecenie **Usuń**.
   
    ![Menu kontekstowe Formuły](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wybierz **pozycję Tak** w oknie dialogowym potwierdzenia usunięcia.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Podobne posty na blogu
* [Niestandardowe obrazy lub formuły?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Następne kroki
Po utworzeniu formuły do użycia podczas tworzenia maszyny Wirtualnej następnym krokiem jest [dodanie maszyny Wirtualnej do laboratorium.](devtest-lab-add-vm.md)

