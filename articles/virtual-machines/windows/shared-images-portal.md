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
ms.openlocfilehash: 1560a67d73b712ba1f295992fce4f7f1a2ae75bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100706"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Tworzenie galerii udostępnionych obrazów platformy Azure przy użyciu portalu

[Galeria obrazów udostępnionych](shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Niestandardowe obrazy mogą służyć do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracje aplikacji i inne konfiguracje systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. Firma Microsoft będzie używać tych aplikacji lub tworzyć je w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Podstawowy obraz, który może być używany samodzielnie lub do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie [uogólnionych](shared-image-galleries.md#generalized-and-specialized-images) maszyn wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do utworzenia wersji obrazu udostępnionego. |
| **Zdjęcie** | Kopia dysku VHD, której można użyć do udostępnienia **wersji obrazu**. Migawki mogą być pobierane z [wyspecjalizowanej](shared-image-galleries.md#generalized-and-specialized-images) maszyny wirtualnej (która nie została uogólniona), a następnie używana samodzielnie lub z migawkami dysków danych, aby utworzyć wyspecjalizowaną wersję obrazu.
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są zdefiniowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go w organizacji. Można dołączyć informacje takie jak uogólnione lub wyspecjalizowane obrazy, system operacyjny, minimalne i maksymalne wymagania dotyczące pamięci oraz informacje o wersji. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |

<br>


> [!IMPORTANT]
> Wyspecjalizowane obrazy są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Ograniczenia znanej wersji zapoznawczej** Maszyny wirtualne można tworzyć tylko z wyspecjalizowanych obrazów przy użyciu portalu lub interfejsu API. Program nie obsługuje interfejsu wiersza polecenia lub programu PowerShell dla wersji zapoznawczej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionej maszyny wirtualnej lub migawkę wyspecjalizowanej maszyny wirtualnej. Możesz skorzystać z [samouczka: Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure za pomocą Azure PowerShell](tutorial-custom-images.md) w celu utworzenia obrazu zarządzanego lub [utworzenia migawki](snapshot-copy-managed-disk.md) dla wyspecjalizowanej maszyny wirtualnej. W przypadku obrazów zarządzanych i migawek rozmiar dysku danych nie może być większy niż 1 TB.

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
1. Wartość **obrazu** jest automatycznie wypełniana przy użyciu wersji `latest` obrazu, jeśli rozpoczęto od strony dla definicji obrazu.
1. W polu **rozmiar**wybierz rozmiar maszyny wirtualnej z listy Dostępne rozmiary, a następnie wybierz **pozycję Wybierz**.
1. Jeśli obraz został uogólniony przy użyciu **konta administratora**, należy podać nazwę użytkownika, taką jak *azureuser* i hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Jeśli obraz był wyspecjalizowany, pola Nazwa użytkownika i hasło zostaną wyszarzone, ponieważ używane są nazwy użytkownika i hasło dla źródłowej maszyny wirtualnej.
1. Jeśli chcesz zezwolić na dostęp zdalny do maszyny wirtualnej w obszarze **publiczne porty przychodzące**, wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. Jeśli nie chcesz zezwalać na dostęp zdalny do maszyny wirtualnej, pozostaw **nie** wybrane dla **publicznych portów przychodzących**.
1. Po zakończeniu wybierz przycisk **Recenzja + tworzenie** w dolnej części strony.
1. Po zakończeniu walidacji maszyny wirtualnej wybierz pozycję **Utwórz** w dolnej części strony, aby rozpocząć wdrażanie.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

Jeśli chcesz usunąć poszczególne zasoby, musisz je usunąć w odwrotnej kolejności. Na przykład aby usunąć definicję obrazu, należy usunąć wszystkie wersje obrazu utworzone na podstawie tego obrazu.

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).

