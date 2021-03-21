---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu Azure Portal
description: Wdróż maszyny wirtualne i zestawy skalowania na dedykowanych hostach przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667328"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych i zestawów skalowania na dedykowanych hostach przy użyciu portalu 

W tym artykule opisano sposób tworzenia [dedykowanego hosta](dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 


## <a name="limitations"></a>Ograniczenia

- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Więcej informacji można znaleźć na [stronie cennika](https://aka.ms/ADHPricing) hosta.

## <a name="create-a-host-group"></a>Tworzenie grupy hostów

**Grupa hostów** jest zasobem, który reprezentuje kolekcję dedykowanych hostów. Można utworzyć grupę hostów w regionie i strefie dostępności, a następnie dodać do niej hosty. W przypadku planowania wysokiej dostępności dostępne są dodatkowe opcje. W przypadku dedykowanych hostów można użyć jednej lub obu następujących opcji: 
- Obejmuje wiele stref dostępności. W takim przypadku konieczne jest posiadanie grupy hostów w każdej ze stref, które mają być używane.
- Obejmuje wiele domen błędów, które są mapowane na stojaki fizyczne. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hostów. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów wynoszącej 1. 

Możesz również zdecydować się na korzystanie ze stref dostępności i domen błędów. 

W tym przykładzie utworzymy grupę hostów przy użyciu jednej strefy dostępności i dwóch domen błędów. 


1. Otwórz [Portal](https://portal.azure.com)Azure. 
1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **grupę hostów** , a następnie wybierz z wyników pozycję **grupy hostów** .
1. Na stronie **grupy hostów** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów.
1. Wpisz *myDedicatedHostsRG* jako **nazwę** , a następnie wybierz przycisk **OK**.
1. W obszarze **Nazwa grupy hostów** wpisz element *webhost*.
1. W obszarze **Lokalizacja** wybierz pozycję **Wschodnie stany USA**.
1. W **obszarze strefa dostępności** wybierz pozycję **1**.
1. W obszarze **liczba domen błędów** wybierz pozycję **2**.
1. Wybierz pozycję **automatyczne umieszczanie** , aby automatycznie przypisywać maszyny wirtualne i wystąpienia zestawów skalowania do dostępnego hosta w tej grupie.
1. Wybierz pozycję **Przegląd + Utwórz** , a następnie zaczekaj na weryfikację.
1. Po wyświetleniu komunikatu **zweryfikowana weryfikacja** wybierz pozycję **Utwórz** , aby utworzyć grupę hostów.

Utworzenie grupy hostów może trwać zaledwie kilka minut.


## <a name="create-a-dedicated-host"></a>Tworzenie dedykowanego hosta

Teraz Utwórz dedykowanego hosta w grupie hostów. Oprócz nazwy hosta, należy podać jednostkę SKU dla hosta. Jednostka SKU hosta przechwytuje obsługiwaną serię maszyn wirtualnych oraz generowanie sprzętu dla dedykowanego hosta.

Aby uzyskać więcej informacji o jednostkach SKU i cenach hosta, zobacz [Cennik dedykowanego hosta platformy Azure](https://aka.ms/ADHPricing).

Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostanie wyświetlony monit o określenie domeny błędów dla hosta.  

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **dedykowanego hosta** , a następnie wybierz pozycję **dedykowane hosty** na podstawie wyników.
1. Na stronie **hosty dedykowane** wybierz pozycję **Utwórz**.
1. Wybierz subskrypcję, której chcesz użyć.
1. Wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**.
1. W obszarze **szczegóły wystąpienia** wpisz wartość *webhost* dla **nazwy** i wybierz pozycję *Wschodnie stany USA* dla lokalizacji.
1. W **obszarze Profil sprzętu** wybierz pozycję *rodzina Es3* , a następnie w polu **rodzina rozmiarów** wybierz pozycję Moja **Grupa** , a następnie wybierz pozycję *1* dla **domeny błędów**.  Pozostaw wartości domyślne pozostałych pól.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Przegląd + Utwórz** i poczekaj na weryfikację.
1. Po wyświetleniu komunikatu **zweryfikowana weryfikacja** wybierz pozycję **Utwórz** , aby utworzyć hosta.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. W polu wyszukiwania powyżej listy zasobów portalu Azure Marketplace Wyszukaj i wybierz obraz, którego chcesz użyć, a następnie wybierz pozycję **Utwórz**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu** upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja**.
1. W obszarze **Opcje dostępności** wybierz pozycję **strefa dostępności**, a następnie z listy rozwijanej wybierz pozycję *1* .
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jedną z serii Esv3, na przykład **standardowa E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. Wypełnij pozostałe pola na karcie **podstawowe** , zgodnie z wymaganiami.
1. Jeśli chcesz określić hosta, który ma być używany przez maszynę wirtualną, w górnej części strony wybierz kartę **Zaawansowane** i w sekcji **host** wybierz pozycję Moja *host* dla **grupy hostów** *i hosta* dla **hosta**. W przeciwnym razie maszyna wirtualna zostanie automatycznie umieszczona na hoście z pojemnością.
    ![Wybierz grupę hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Gdy zobaczysz komunikat, że Walidacja zakończyła się pomyślnie, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut.

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania 

Podczas wdrażania zestawu skalowania należy określić grupę hostów.

1. Wyszukaj *zestaw skalowania* i wybierz z listy pozycję **zestawy skalowania maszyn wirtualnych** .
1. Wybierz pozycję **Dodaj** , aby utworzyć nowy zestaw skalowania.
1. Wypełnij pola na karcie **podstawowe** , jak zwykle, ale pamiętaj o wybraniu rozmiaru maszyny wirtualnej z serii wybranej dla dedykowanego hosta, takiego jak **standardowa E2s v3**.
1. Na karcie **Zaawansowane** dla **algorytmu rozpraszania** wybierz pozycję **maksymalne rozmieszczanie**.
1. W obszarze **Grupa hostów** wybierz grupę hostów z listy rozwijanej. Jeśli ostatnio utworzono grupę, dodanie jej do listy może potrwać minutę.

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Możesz dodać maszynę wirtualną z wykończeniami do dedykowanego hosta, ale najpierw należy Stop\Deallocated. maszynę wirtualną Przed przeniesieniem maszyny wirtualnej do dedykowanego hosta upewnij się, że konfiguracja maszyny wirtualnej jest obsługiwana:

- Rozmiar maszyny wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host to DSv3, rozmiar maszyny wirtualnej może być Standard_D4s_v3, ale nie Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy umieszczania sąsiedztwa. Usuń maszynę wirtualną z grupy położenia zbliżeniowe przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny wirtualnej z grupy umieszczania sąsiedztwa](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może znajdować się w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi to być ta sama strefa dostępności co grupa hostów. Ustawienia strefy dostępności dla maszyny wirtualnej i grupy hostów muszą być zgodne.

Przenieś maszynę wirtualną na dedykowanego hosta przy użyciu [portalu](https://portal.azure.com).

1. Otwórz stronę dla maszyny wirtualnej.
1. Wybierz pozycję **Zatrzymaj** , aby stop\deallocate maszynę wirtualną.
1. Z menu po lewej stronie wybierz pozycję **Konfiguracja** .
1. Wybierz grupę hostów i hosta z menu rozwijanego.
1. Gdy skończysz, wybierz pozycję **Zapisz** w górnej części strony.
1. Po dodaniu maszyny wirtualnej do hosta z menu po lewej stronie wybierz pozycję **Przegląd** .
1. W górnej części strony wybierz pozycję **Rozpocznij** , aby ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) .

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Możesz również wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](./linux/dedicated-hosts-cli.md) lub [programu PowerShell](./windows/dedicated-hosts-powershell.md).
