---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu Azure Portal
description: Wdróż maszyny wirtualne na dedykowanych hostach przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 068f6e85a0b2a56bb20c8581ec4fa59d50009088
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080322"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu portalu

W tym artykule opisano sposób tworzenia [dedykowanego hosta](dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Ubuntu Server 16.04 LTS by Canonical**, wybierz odpowiednią pozycję i kliknij **Utwórz**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja **.
1. W obszarze **Opcje dostępności** wybierz pozycję **strefa dostępności**, a następnie z listy rozwijanej wybierz pozycję *1* .
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jedną z serii Esv3, na przykład **standardowa E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. W obszarze **Konto administratora** wybierz opcję **klucz publiczny SSH**, wpisz nazwę użytkownika, a następnie wklej swój klucz publiczny w polu tekstowym. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze publiczne porty przychodzące dla **reguł portów ruchu**przychodzącego  >  **Public inbound ports**wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **SSH (22)** z listy rozwijanej. 
1. W górnej części strony wybierz kartę **Zaawansowane** i w sekcji **host** wybierz pozycję Moja *host* dla **grupy hostów** *i hosta* dla **hosta**. 
    ![Wybierz grupę hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Gdy zobaczysz komunikat, że Walidacja zakończyła się pomyślnie, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut.

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Możesz dodać maszynę wirtualną z wykończeniami do dedykowanego hosta, ale najpierw należy Stop\Deallocated. maszynę wirtualną Przed przeniesieniem maszyny wirtualnej do dedykowanego hosta upewnij się, że konfiguracja maszyny wirtualnej jest obsługiwana:

- Rozmiar maszyny wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host to DSv3, rozmiar maszyny wirtualnej może być Standard_D4s_v3, ale nie Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy umieszczania sąsiedztwa. Usuń maszynę wirtualną z grupy położenia zbliżeniowe przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny wirtualnej z grupy umieszczania sąsiedztwa](../windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
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

- Możesz również wdrożyć dedykowanego hosta za pomocą [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md).
