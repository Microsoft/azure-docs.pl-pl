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
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166330"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Wdrażanie Cloud Services platformy Azure (obsługa rozszerzona) przy użyciu Azure Portal
W tym artykule wyjaśniono, jak za pomocą Azure Portal utworzyć wdrożenie usługi w chmurze (obsługa rozszerzona). 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona) i Utwórz skojarzone zasoby. 

## <a name="deploy-a-cloud-services-extended-support"></a>Wdrażanie Cloud Services (obsługa rozszerzona) 
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2.  Korzystając z paska wyszukiwania znajdującego się w górnej części Azure Portal, Wyszukaj i wybierz pozycję **Cloud Services (obsługa rozszerzona)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Obraz przedstawia blok wszystkie zasoby w Azure Portal.":::
 
3.  W okienku Cloud Services (obsługa rozszerzona) wybierz pozycję **Utwórz**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Obraz przedstawia kupowanie usługi w chmurze w portalu Marketplace.":::

4. Okno tworzenia Cloud Services (obsługa rozszerzona) zostanie otwarte na karcie **podstawy** . 
    - Wybierz subskrypcję.
    - Wybierz grupę zasobów lub Utwórz nową.
    - Wprowadź odpowiednią nazwę wdrożenia usługi w chmurze (obsługa rozszerzona).
        - Nazwa DNS usługi w chmurze jest oddzielona i określona przez etykietę nazwy DNS publicznego adresu IP i można ją zmodyfikować w sekcji Public IP na karcie Konfiguracja.
    -  Wybierz region do wdrożenia.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Obraz przedstawia główny blok Cloud Services (obsługa rozszerzona).":::

5. Dodaj konfigurację usługi w chmurze, pliki pakietów i definicje. Możesz dodać istniejące pliki z usługi BLOB Storage lub przekazać je z komputera lokalnego. W przypadku przekazywania z komputera lokalnego zostaną one zapisane na koncie magazynu. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Obraz przedstawia sekcję przekazywanie na karcie podstawy podczas tworzenia.":::

6. Po zakończeniu wszystkich pól przejdź do i Wypełnij kartę **Konfiguracja** . 
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

7. Po zakończeniu wszystkich pól przejdź na kartę **Przegląd i tworzenie** , aby sprawdzić poprawność konfiguracji wdrożenia i utworzyć usługę w chmurze (pomoc rozszerzona).

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
- Odwiedź [repozytorium przykładów Cloud Services (obsługa rozszerzona)](https://github.com/Azure-Samples/cloud-services-extended-support)
