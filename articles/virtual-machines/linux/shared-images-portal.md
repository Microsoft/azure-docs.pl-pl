---
title: Tworzenie udostępnionych obrazów maszyn wirtualnych z systemem Azure z systemem Linux przy użyciu portalu
description: Dowiedz się, jak używać witryny Azure portal do tworzenia i udostępniania obrazów maszyn wirtualnych systemu Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 74c87d589f1c50551ac5685fe0fa126a82bffbde
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758434"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Tworzenie galerii obrazów udostępnionych platformy Azure przy użyciu portalu

[Udostępniona galeria obrazów](shared-image-galleries.md) upraszcza niestandardowe udostępnianie obrazów w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą być używane do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub między regionami, w dzierżawie usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony, w których chcesz je udostępnić i komu chcesz je udostępnić. Można utworzyć wiele galerii, aby logicznie grupować udostępnione obrazy. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być wersjona i można wybrać do replikacji każdej wersji obrazu do innego zestawu regionów platformy Azure. Galeria działa tylko z zarządzanymi obrazami.

Funkcja Galeria obrazów udostępnionych ma wiele typów zasobów. Będziemy używać lub budować te w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Podstawowy obraz, który może być używany samodzielnie lub używany do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na [uogólnionych](shared-image-galleries.md#generalized-and-specialized-images) maszynach wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do tworzenia wersji obrazów udostępnionych. |
| **Migawka** | Kopia dysku VHD, który może być użyty do tworzenia **wersji obrazu**. Migawki mogą być pobierane ze [specjalistycznej](shared-image-galleries.md#generalized-and-specialized-images) maszyny Wirtualnej (która nie została uogólniona), a następnie używane samodzielnie lub z migawkami dysków z danymi, aby utworzyć wyspecjalizowaną wersję obrazu.
| **Galeria zdjęć** | Podobnie jak w portalu Azure **Marketplace, galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale można kontrolować, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są definiowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go w organizacji. Można dołączyć informacje, takie jak to, czy obraz jest uogólniony lub wyspecjalizowany, system operacyjny, minimalne i maksymalne wymagania dotyczące pamięci oraz informacje o wersji. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest to, czego używasz do tworzenia maszyny Wirtualnej podczas korzystania z galerii. W zależności od środowiska może być dostępnych wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, podczas tworzenia maszyny Wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów mogą być używane wiele razy. |

<br>

> [!IMPORTANT]
> Wyspecjalizowane obrazy są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Znane ograniczenia podglądu** Maszyny wirtualne można tworzyć tylko z wyspecjalizowanych obrazów przy użyciu portalu lub interfejsu API. Obsługa interfejsu wiersza polecenia lub programu PowerShell dla wersji zapoznawczej nie jest obsługiwana przez program CLI.


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionej maszyny Wirtualnej lub migawkę wyspecjalizowanej maszyny Wirtualnej. Możesz wykonać [samouczek: Utwórz niestandardowy obraz maszyny Wirtualnej platformy Azure za pomocą programu Azure PowerShell,](tutorial-custom-images.md) aby utworzyć obraz zarządzany, lub [utwórz migawkę](../windows/snapshot-copy-managed-disk.md) dla wyspecjalizowanej maszyny Wirtualnej. W przypadku obrazów zarządzanych i migawek rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy nad tym artykułem w razie potrzeby zastąp nazwy grupy zasobów i maszyn wirtualnych.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Tworzenie maszyn wirtualnych 

Teraz można utworzyć jedną lub więcej nowych maszyn wirtualnych. W tym przykładzie utworzy maszynę wirtualną o nazwie *myVMfromImage*, w *myResourceGroup* w centrum danych *wschodnich stanów USA.*

1. Przejdź do definicji obrazu. Za pomocą filtru zasobów można wyświetlić wszystkie dostępne definicje obrazów.
1. Na stronie definicji obrazu wybierz pozycję **Utwórz maszynę wirtualną** z menu u góry strony.
1. W przypadku **grupy zasobów**wybierz pozycję **Utwórz nowy** i wpisz *myResourceGroup* dla nazwy.
1. W **nazwie maszyny wirtualnej**wpisz *myVM*.
1. W obszarze **Region**wybierz pozycję *Wschodnie stany USA*.
1. W przypadku **opcji dostępności**pozostaw domyślną opcję *Brak nadmiarowości infrastruktury*.
1. Wartość **obrazu** jest automatycznie wypełniana `latest` wersją obrazu, jeśli rozpoczęto od strony dla definicji obrazu.
1. W obszarze **Rozmiar**wybierz rozmiar maszyny Wirtualnej z listy dostępnych rozmiarów, a następnie wybierz pozycję **Wybierz**.
1. W obszarze **Konto administratora**, jeśli źródłowa maszyna wirtualna została uogólniona, wprowadź **nazwę użytkownika** i klucz **publiczny SSH**. Jeśli źródło maszyny Wirtualnej był wyspecjalizowany, te opcje będą wyszarzone, ponieważ używane są informacje ze źródłowej maszyny Wirtualnej.
1. Jeśli chcesz zezwolić na zdalny dostęp do maszyny Wirtualnej, w obszarze **Publiczne porty przychodzące**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz pozycję **SSH (22)** z listy rozwijanej. Jeśli nie chcesz zezwalać na zdalny dostęp do maszyny Wirtualnej, pozostaw **opcję Brak** dla **publicznych portów przychodzących**.
1. Po zakończeniu wybierz przycisk **Przejrzyj + utwórz** u dołu strony.
1. Po przejściu weryfikacji maszyny Wirtualnej wybierz **pozycję Utwórz** u dołu strony, aby rozpocząć wdrażanie.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

Jeśli chcesz usunąć poszczególne zasoby, musisz je usunąć w odwrotnej kolejności. Na przykład, aby usunąć definicję obrazu, należy usunąć wszystkie wersje obrazów utworzone z tego obrazu.

## <a name="next-steps"></a>Następne kroki

Zasób Shared Image Gallery można również utworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat galerii obrazów udostępnionych, zobacz [Omówienie](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).

