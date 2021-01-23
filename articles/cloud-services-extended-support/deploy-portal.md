---
title: Wdrażanie usługi w chmurze platformy Azure (obsługa rozszerzona) — Azure Portal
description: Wdrażanie usługi w chmurze platformy Azure (obsługa rozszerzona) przy użyciu Azure Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9ca0a3852c6be04332369e57ce1916e5f589516f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745150"
---
# <a name="deploy-azure-cloud-services-extended-support-using-the-azure-portal"></a>Wdrażanie usługi Azure Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
W tym artykule wyjaśniono, jak za pomocą Azure Portal utworzyć wdrożenie usługi w chmurze (obsługa rozszerzona). 

> [!IMPORTANT]
> Cloud Services (obsługa rozszerzona) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona) i Utwórz skojarzone zasoby. 

2. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

3.  Korzystając z paska wyszukiwania znajdującego się w górnej części Azure Portal, Wyszukaj i wybierz pozycję **Cloud Services (obsługa rozszerzona)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Obraz przedstawia blok wszystkie zasoby w Azure Portal.":::
 
4.  W okienku Cloud Services (obsługa rozszerzona) wybierz pozycję **Utwórz**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Obraz przedstawia kupowanie usługi w chmurze w portalu Marketplace.":::

5. Okno tworzenia Cloud Services (obsługa rozszerzona) zostanie otwarte na karcie **podstawy** . 
    - Wybierz subskrypcję.
    - Wybierz grupę zasobów lub Utwórz nową.
    - Wprowadź odpowiednią nazwę wdrożenia usługi w chmurze (obsługa rozszerzona).
        - Nazwa DNS usługi w chmurze jest oddzielona i określona przez etykietę nazwy DNS publicznego adresu IP i można ją zmodyfikować w sekcji Public IP na karcie Konfiguracja.
    -  Wybierz region do wdrożenia.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Obraz przedstawia główny blok Cloud Services (obsługa rozszerzona).":::

6. Dodaj konfigurację usługi w chmurze, pliki pakietów i definicje. Możesz dodać istniejące pliki z usługi BLOB Storage lub przekazać je z komputera lokalnego. W przypadku przekazywania z komputera lokalnego zostaną one zapisane na koncie magazynu. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Obraz przedstawia sekcję przekazywanie na karcie podstawy podczas tworzenia.":::

7. Po zakończeniu wszystkich pól przejdź do i Wypełnij kartę **Konfiguracja** . 
    - Wybierz sieć wirtualną, która ma zostać skojarzona z usługą w chmurze, lub Utwórz nową. 
        - Wdrożenia usługi w chmurze (obsługa rozszerzona) **muszą** znajdować się w sieci wirtualnej. Do sieci wirtualnej **należy** również odwołać się w pliku konfiguracji usługi (. cscfg), który znajduje się w `NetworkConfiguration` sekcji.
    - Wybierz istniejący publiczny adres IP, który ma zostać skojarzony z usługą w chmurze, lub Utwórz nowy.
        - Jeśli w pliku definicji usługi (. csdef) masz zdefiniowane **punkty końcowe IP** , należy utworzyć publiczny adres IP dla usługi w chmurze. 
        - Cloud Services (obsługa rozszerzona) obsługuje tylko podstawową jednostkę SKU adresów IP.
        - Jeśli konfiguracja usługi (. cscfg) zawiera zastrzeżony adres IP, typ alokacji dla publicznego adresu IP musi być ustawiony jako **statyczny**. 
        - Opcjonalnie można przypisać nazwę DNS punktu końcowego usługi w chmurze, aktualizując Właściwość etykieta DNS publicznego adresu IP skojarzonego z usługą w chmurze.  
    - Obowiązkowe Uruchom usługę w chmurze. Wybierz Uruchom lub nie uruchamiaj usługi natychmiast po utworzeniu.
    - Wybierz Key Vault 
        - Key Vault jest wymagane w przypadku określenia co najmniej jednego certyfikatu w pliku konfiguracji usługi (. cscfg). Po wybraniu magazynu kluczy będziemy próbować znaleźć wybrane certyfikaty z pliku konfiguracji usługi (. cscfg) na podstawie ich odcisków palców. Jeśli w magazynie kluczy brakuje żadnych certyfikatów, możesz je teraz przekazać, a następnie kliknąć przycisk **Odśwież**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Obraz przedstawia blok konfiguracja w Azure Portal podczas tworzenia Cloud Services (obsługa rozszerzona).":::

8. Po zakończeniu wszystkich pól przejdź na kartę **Przegląd i tworzenie** , aby sprawdzić poprawność konfiguracji wdrożenia i utworzyć usługę w chmurze (pomoc rozszerzona).

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).