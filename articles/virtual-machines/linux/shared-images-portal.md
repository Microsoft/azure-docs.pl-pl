---
title: Tworzenie udostępnionych obrazów maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu portalu
description: Dowiedz się, jak używać Azure Portal do tworzenia i udostępniania obrazów maszyn wirtualnych z systemem Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 01d095a5506ec0886cdee875b57edbbbeca6fc86
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878832"
---
# <a name="create-a-shared-image-gallery-using-the-portal"></a>Tworzenie udostępnionej galerii obrazów przy użyciu portalu

[Galeria obrazów udostępnionych](../shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Niestandardowe obrazy mogą służyć do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracje aplikacji i inne konfiguracje systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi Azure AD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (Azure RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. Firma Microsoft będzie używać tych aplikacji lub tworzyć je w tym artykule:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionej maszyny wirtualnej lub migawkę wyspecjalizowanej maszyny wirtualnej. Możesz skorzystać z [samouczka: Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure za pomocą Azure PowerShell](tutorial-custom-images.md) w celu utworzenia obrazu zarządzanego lub [utworzenia migawki](../windows/snapshot-copy-managed-disk.md) dla wyspecjalizowanej maszyny wirtualnej. W przypadku obrazów zarządzanych i migawek rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, jeśli są one odpowiednie.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Tworzenie maszyn wirtualnych 

Teraz można utworzyć co najmniej jedną nową maszynę wirtualną. Ten przykład tworzy maszynę wirtualną o nazwie *myVMfromImage*, w liście *zasobów* w *regionie Wschodnie stany USA* .

1. Przejdź do definicji obrazu. Możesz użyć filtru zasobów, aby wyświetlić wszystkie dostępne definicje obrazu.
1. Na stronie definicji obrazu wybierz pozycję **Utwórz maszynę wirtualną** z menu w górnej części strony.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wpisz nazwę *zasobu* .
1. W polu **Nazwa maszyny wirtualnej** wpisz *myVM*.
1. W **obszarze region** wybierz pozycję *Wschodnie stany USA*.
1. W przypadku **opcji dostępności** pozostaw wartość domyślną *Brak wymaganej nadmiarowości infrastruktury*.
1. Wartość **obrazu** jest automatycznie wypełniana przy użyciu `latest` wersji obrazu, jeśli rozpoczęto od strony dla definicji obrazu.
1. W polu **rozmiar** wybierz rozmiar maszyny wirtualnej z listy Dostępne rozmiary, a następnie wybierz **pozycję Wybierz**.
1. W obszarze **konto administratora**, jeśli źródłowa maszyna wirtualna została uogólniona, wprowadź **nazwę użytkownika** i **klucz publiczny SSH**. Jeśli źródłowa maszyna wirtualna była wyspecjalizowana, Opcje te zostaną wyszarzone, ponieważ są używane informacje ze źródłowej maszyny wirtualnej.
1. Jeśli chcesz zezwolić na dostęp zdalny do maszyny wirtualnej w obszarze **publiczne porty przychodzące**, wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **SSH (22)** z listy rozwijanej. Jeśli nie chcesz zezwalać na dostęp zdalny do maszyny wirtualnej, pozostaw **nie** wybrane dla **publicznych portów przychodzących**.
1. Po zakończeniu wybierz przycisk **Recenzja + tworzenie** w dolnej części strony.
1. Po zakończeniu walidacji maszyny wirtualnej wybierz pozycję **Utwórz** w dolnej części strony, aby rozpocząć wdrażanie.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

Jeśli chcesz usunąć poszczególne zasoby, musisz je usunąć w odwrotnej kolejności. Na przykład aby usunąć definicję obrazu, należy usunąć wszystkie wersje obrazu utworzone na podstawie tego obrazu.

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](../shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](../troubleshooting-shared-images.md).