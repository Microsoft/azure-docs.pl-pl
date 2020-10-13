---
title: Tworzenie galerii udostępnionych obrazów platformy Azure przy użyciu portalu
description: Dowiedz się, jak tworzyć i udostępniać obrazy maszyn wirtualnych przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e10b1955d50450e43d1dbb180f4d533b6b6ae8b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978058"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Tworzenie galerii udostępnionych obrazów platformy Azure przy użyciu portalu

[Galeria obrazów udostępnionych](shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Niestandardowe obrazy mogą służyć do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracje aplikacji i inne konfiguracje systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. Firma Microsoft będzie używać tych aplikacji lub tworzyć je w tym artykule:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


Podczas pracy z tym artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, jeśli są one odpowiednie.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Tworzenie maszyn wirtualnych

Teraz można utworzyć co najmniej jedną nową maszynę wirtualną. Ten przykład tworzy maszynę wirtualną o nazwie *myVM*w liście *zasobów*w *regionie Wschodnie stany USA* .

1. Przejdź do definicji obrazu. Możesz użyć filtru zasobów, aby wyświetlić wszystkie dostępne definicje obrazu.
1. Na stronie definicji obrazu wybierz pozycję **Utwórz maszynę wirtualną** z menu w górnej części strony.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową** , a następnie wpisz nazwę *zasobu* .
1. W polu **Nazwa maszyny wirtualnej**wpisz *myVM*.
1. W **obszarze region**wybierz pozycję *Wschodnie stany USA*.
1. W przypadku **opcji dostępności**pozostaw wartość domyślną *Brak wymaganej nadmiarowości infrastruktury*.
1. Wartość **obrazu** jest automatycznie wypełniana przy użyciu `latest` wersji obrazu, jeśli rozpoczęto od strony dla definicji obrazu.
1. W polu **rozmiar**wybierz rozmiar maszyny wirtualnej z listy Dostępne rozmiary, a następnie wybierz **pozycję Wybierz**.
1. Jeśli obraz został uogólniony przy użyciu **konta administratora**, należy podać nazwę użytkownika, taką jak *azureuser* i hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Jeśli obraz był wyspecjalizowany, pola Nazwa użytkownika i hasło zostaną wyszarzone, ponieważ używane są nazwy użytkownika i hasło dla źródłowej maszyny wirtualnej.
1. Jeśli chcesz zezwolić na dostęp zdalny do maszyny wirtualnej w obszarze **publiczne porty przychodzące**, wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. Jeśli nie chcesz zezwalać na dostęp zdalny do maszyny wirtualnej, pozostaw **nie** wybrane dla **publicznych portów przychodzących**.
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

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](../troubleshooting-shared-images.md).