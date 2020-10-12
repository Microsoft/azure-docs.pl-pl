---
title: Przenoszenie maszyny wirtualnej do innego regionu (Azure Site Recovery)
description: Dowiedz się, jak migrować SQL Server maszynę wirtualną z jednego regionu do innego na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4252528020dde731dd7bf14ae8f7a03467ba953a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298603"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Przenoszenie SQL Server maszyny wirtualnej do innego regionu na platformie Azure z Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule przedstawiono sposób użycia Azure Site Recovery do migrowania SQL Server maszyny wirtualnej z jednego regionu do drugiego na platformie Azure. 

Przeniesienie maszyny wirtualnej SQL Server do innego regionu wymaga wykonania następujących czynności:
1. [Przygotowywanie](#prepare-to-move): Upewnij się, że źródłowa maszyna wirtualna SQL Server i region docelowy są odpowiednio przygotowane do przeniesienia. 
1. [Konfigurowanie](#configure-azure-site-recovery-vault): przeniesienie maszyny wirtualnej SQL Server wymaga, aby była ona replikowanym obiektem w magazynie Azure Site Recovery. Musisz dodać maszynę wirtualną SQL Server do magazynu Azure Site Recovery. 
1. [Testowanie](#test-move-process): MIGROWANIE maszyny wirtualnej SQL Server wymaga jej przełączenia w tryb failover z regionu źródłowego do zreplikowanego regionu docelowego. Aby upewnić się, że proces przenoszenia zakończy się pomyślnie, musisz najpierw sprawdzić, czy SQL Server maszyna wirtualna może przejść w tryb failover w regionie docelowym. Ułatwi to ujawnienie wszelkich problemów i uniknięcie ich podczas rzeczywistego przenoszenia. 
1. [Przenoszenie](#move-the-sql-server-vm): po przejściu testowej pracy w trybie failover, Jeśli wiesz, że możesz bezpiecznie migrować maszynę wirtualną SQL Server, możesz przeprowadzić przeniesienie maszyny wirtualnej do regionu docelowego. 
1. [Czyszczenie](#clean-up-source-resources): aby uniknąć naliczania opłat, Usuń SQL Server maszynę wirtualną z magazynu i wszelkie niepotrzebne zasoby, które są pozostawione w grupie zasobów. 

## <a name="verify-prerequisites"></a>Weryfikacja wymagań wstępnych 

- Upewnij się, że przeniesienie z regionu źródłowego do regionu docelowego [jest obsługiwane](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Przejrzyj [architekturę i składniki scenariusza](../../../site-recovery/azure-to-azure-architecture.md) , a także [ograniczenia i wymagania dotyczące obsługi](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Sprawdź uprawnienia konta. Jeśli utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla maszyny wirtualnej i skopiować dane przy użyciu Azure Site Recovery, musisz dysponować: 
    - Uprawnienia do tworzenia maszyny wirtualnej. Wbudowana rola *współautor maszyny wirtualnej* ma następujące uprawnienia: 
        - Uprawnienia do tworzenia maszyny wirtualnej w wybranej grupie zasobów. 
        - Uprawnienia do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej. 
        - Uprawnienia do zapisu na wybranym koncie magazynu. 
      - Uprawnienia do zarządzania operacjami Azure Site Recovery. Rola *współautor Site Recovery* ma wszystkie uprawnienia wymagane do zarządzania operacjami Site Recovery w magazynie Recovery Services.  

## <a name="prepare-to-move"></a>Przygotuj do przeniesienia
Przygotuj zarówno źródłową, SQL Server maszynę wirtualną, jak i region docelowy dla przeniesienia. 

### <a name="prepare-the-source-sql-server-vm"></a>Przygotuj źródłową maszynę wirtualną SQL Server

- Upewnij się, że wszystkie najnowsze certyfikaty główne znajdują się na maszynie wirtualnej SQL Server, która ma zostać przeniesiona. Jeśli nie ma tam najnowszych certyfikatów głównych, ograniczenia zabezpieczeń uniemożliwią kopiowanie danych do regionu docelowego. 
- Na maszynach wirtualnych z systemem Windows zainstaluj wszystkie najnowsze aktualizacje systemu Windows na maszynie wirtualnej, aby wszystkie zaufane certyfikaty główne były na tym komputerze. W środowisku odłączonym postępuj zgodnie ze standardową Windows Update i procesem aktualizacji certyfikatu dla swojej organizacji. 
- W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora systemu Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i listy odwołania certyfikatów na maszynie wirtualnej. 
- Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść. 
- Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu lub używa serwera proxy zapory do kontrolowania dostępu wychodzącego, Sprawdź wymagania. 
- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Obejmuje to między innymi usługi równoważenia obciążenia, sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i publiczne adresy IP. 

### <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału. 
- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów do obsługi maszyn wirtualnych o rozmiarze pasującym do Twoich źródłowych maszyn wirtualnych. Jeśli używasz Site Recovery do kopiowania danych do obiektu docelowego, Site Recovery wybierze ten sam rozmiar lub najbliższy możliwy rozmiar docelowej maszyny wirtualnej. 
- Upewnij się, że tworzysz zasób docelowy dla każdego składnika, który jest identyfikowany w układzie sieci źródłowej. Ten krok jest ważne, aby upewnić się, że maszyny wirtualne mają wszystkie funkcje i funkcje w regionie docelowym, który istniał w regionie źródłowym. 
    - Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny wirtualnej. Możesz również wstępnie utworzyć sieć i przypisać ją do maszyny wirtualnej w przepływie użytkownika w celu włączenia replikacji. Należy ręcznie utworzyć wszystkie inne zasoby w regionie docelowym.
- Aby utworzyć najczęściej używane zasoby sieciowe, które są odpowiednie dla Ciebie na podstawie konfiguracji źródłowej maszyny wirtualnej, zapoznaj się z następującą dokumentacją: 
    - [Sieciowe grupy zabezpieczeń](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Moduł równoważenia obciążenia](../../../load-balancer/tutorial-load-balancer-standard-internal-portal.md)
    - [Publiczny adres IP](../../../virtual-network/virtual-network-public-ip-address.md)
    - Dodatkowe składniki sieci można znaleźć w dokumentacji dotyczącej [sieci](../../../virtual-network/virtual-networks-overview.md).
- Ręcznie Utwórz sieć nieprodukcyjną w regionie docelowym, jeśli chcesz przetestować konfigurację przed przeprowadzeniem ostatniego przejścia do regionu docelowego. Zalecamy wykonanie tej czynności, ponieważ zapewnia ona minimalne zakłócenia w sieci produkcyjnej. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurowanie magazynu Azure Site Recovery

Poniższe kroki pokazują, jak używać Azure Site Recovery do kopiowania danych do regionu docelowego. Utwórz magazyn Recovery Services w dowolnym regionie innym niż region źródłowy. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okienka nawigacji. 
1. Wybierz **ją & narzędzia do zarządzania** , a następnie wybierz pozycję **kopia zapasowa i Site Recovery**. 
1. Na karcie **podstawowe** w obszarze **szczegóły projektu**Utwórz nową grupę zasobów w regionie docelowym lub wybierz istniejącą grupę zasobów w regionie docelowym. 
1. W obszarze **szczegóły wystąpienia**Określ nazwę magazynu, a następnie wybierz **region** docelowy z listy rozwijanej. 
1. Wybierz pozycję **Recenzja + Utwórz** , aby utworzyć magazyn Recovery Services. 
1. W lewym górnym rogu okienka nawigacji wybierz pozycję **wszystkie usługi** , a następnie w polu wyszukiwania wpisz `recovery services` . 
1. Zdefiniować Wybierz gwiazdkę obok **Recovery Services magazynów** , aby dodać ją do paska szybkiej nawigacji. 
1. Wybierz pozycję **magazyny usługi Recovery Services** , a następnie wybierz utworzony magazyn Recovery Services. 
1. W okienku **Przegląd** wybierz pozycję **replikacja**. 

   ![Konfigurowanie replikacji](./media/move-sql-vm-different-region/configure-replication.png)

1. Wybierz pozycję **Źródło** , a następnie wybierz pozycję **Azure** jako źródło. Wybierz odpowiednie wartości dla innych pól listy rozwijanej, takich jak lokalizacja dla źródłowych maszyn wirtualnych. Tylko grupy zasobów znajdujące się w regionie **lokalizacji źródłowej** będą widoczne w polu **źródłowa Grupa zasobów** . 
1. Wybierz pozycję **maszyny wirtualne** , a następnie wybierz maszyny wirtualne, które chcesz zmigrować. Wybierz **przycisk OK** , aby zapisać WYBRANą maszynę wirtualną. 
1. Wybierz pozycję **Ustawienia**, a następnie wybierz **lokalizację docelową** z listy rozwijanej. Powinna to być przygotowana wcześniej Grupa zasobów. 
1. Po dostosowaniu replikacji wybierz pozycję **Utwórz zasoby docelowe** , aby utworzyć zasoby w nowej lokalizacji. 
1. Po zakończeniu tworzenia zasobów wybierz pozycję **Włącz replikację** , aby rozpocząć REPLIKACJĘ maszyny wirtualnej SQL Server z poziomu źródła do regionu docelowego.
1. Stan replikacji można sprawdzić, przechodząc do magazynu odzyskiwania, wybierając pozycję **zreplikowane elementy** i wyświetlając **stan** maszyny wirtualnej SQL Server. Stan **chronione** wskazuje, że replikacja została ukończona. 

   ![Sprawdzanie stanu replikacji](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testuj proces przenoszenia
Poniższe kroki pokazują, jak używać Azure Site Recovery do testowania procesu przenoszenia. 

1. Przejdź do **magazynu Recovery Services** w [Azure Portal](https://portal.azure.com) i wybierz pozycję **zreplikowane elementy**. 
1. Wybierz maszynę wirtualną SQL Server, którą chcesz przenieść, sprawdź, czy **kondycja replikacji** jest wyświetlana jako **dobra kondycja** , a następnie wybierz pozycję **Testuj tryb failover**. 

   ![Testowanie pracy w trybie failover dla maszyny wirtualnej](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Na stronie **test pracy w trybie failover** wybierz **najnowszy punkt odzyskiwania spójny na poziomie aplikacji** , który ma być używany w trybie failover, ponieważ jest to jedyny typ migawki, który może zagwarantować, że SQL Server spójności danych. 
1. Wybierz sieć wirtualną w obszarze **Azure Virtual Network** , a następnie wybierz przycisk **OK** , aby przetestować tryb failover. 
   
   >[!IMPORTANT]
   > Zalecamy używanie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania pracy w trybie failover. Nie należy używać sieci produkcyjnej skonfigurowanej po włączeniu replikacji i przeniesieniu maszyn wirtualnych na ostatecznie. 

1. Aby monitorować postęp, przejdź do magazynu, wybierz pozycję **Site Recovery zadania** w obszarze **monitorowanie**, a następnie wybierz zadanie **test pracy w trybie failover** , które jest w toku.

   ![Monitorowanie postępu testu pracy w trybie failover](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Po zakończeniu testu przejdź do **maszyn wirtualnych** w portalu i przejrzyj nowo utworzoną maszynę wirtualną. Upewnij się, że maszyna wirtualna SQL Server jest uruchomiona, ma odpowiednio rozmiar i jest połączona z odpowiednią siecią. 
1. Usuń maszynę wirtualną, która została utworzona w ramach testu, ponieważ opcja **trybu failover** będzie wyszarzona do momentu oczyszczenia zasobów testowych trybu failover. Przejdź z powrotem do magazynu, wybierz pozycję **zreplikowane elementy**, wybierz maszynę wirtualną SQL Server, a następnie wybierz pozycję **Oczyść test tryb failover**. Zarejestruj i Zapisz wszelkie obserwacje związane z testem w sekcji **notatki** i zaznacz pole wyboru obok pozycji **testowanie zostało zakończone. Usuń maszyny wirtualne z testowym trybem failover**. Wybierz **przycisk OK** , aby wyczyścić zasoby po teście. 

   ![Wyczyść elementy po teście trybu failover](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Przenoszenie SQL Server maszyny wirtualnej 
Poniższe kroki pokazują, jak przenieść maszynę wirtualną SQL Server z regionu źródłowego do regionu docelowego. 

1. Przejdź do magazynu **Recovery Services** , wybierz pozycję **zreplikowane elementy**, wybierz maszynę wirtualną, a następnie wybierz pozycję **tryb failover**. 

   ![Inicjowanie trybu failover](./media/move-sql-vm-different-region/initiate-failover.png)

1. Wybierz najnowszy punkt odzyskiwania **spójny** na poziomie aplikacji w **punkcie odzyskiwania**. 
1. Zaznacz pole wyboru obok pozycji **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Site Recovery spróbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Tryb failover będzie kontynuowany nawet w przypadku niepowodzenia zamknięcia. 
1. Wybierz **przycisk OK** , aby uruchomić tryb failover.
1. Proces trybu failover można monitorować na stronie **zadania Site Recovery** , która była wyświetlana podczas monitorowania testu trybu failover w poprzedniej sekcji. 
1. Po zakończeniu zadania Sprawdź, czy SQL Server maszyna wirtualna jest wyświetlana w regionie docelowym zgodnie z oczekiwaniami. 
1. Przejdź z powrotem do magazynu, wybierz pozycję **zreplikowane elementy**, wybierz maszynę wirtualną SQL Server i wybierz pozycję **Zatwierdź** , aby zakończyć proces przenoszenia w regionie docelowym. Poczekaj na zakończenie zadania zatwierdzania. 
1. Zarejestruj maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, aby umożliwić zarządzanie **maszynami wirtualnymi SQL** w ramach Azure Portal i funkcji skojarzonych z dostawcą zasobów. Aby uzyskać więcej informacji, zobacz [rejestrowanie SQL Server VM przy użyciu dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md). 

  > [!WARNING]
  > SQL Server spójność danych jest gwarantowana tylko za pomocą migawek spójnych na poziomie aplikacji. Nie można użyć **najnowszej przetworzonej** migawki dla SQL Server trybu failover, ponieważ migawka odzyskiwania po awarii nie może zagwarantować SQL Server spójności danych. 

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych
Aby uniknąć naliczania opłat, Usuń SQL Server maszynę wirtualną z magazynu, a następnie usuń wszelkie niepotrzebne zasoby. 

1. Przejdź z powrotem do magazynu **Site Recovery** , wybierz pozycję **zreplikowane elementy**i wybierz maszynę wirtualną SQL Server. 
1. Wybierz pozycję **Wyłącz replikację**. Wybierz przyczynę wyłączenia ochrony, a następnie wybierz przycisk **OK** , aby wyłączyć replikację. 

   >[!IMPORTANT]
   > Należy wykonać ten krok, aby uniknąć naliczania opłat za replikację Azure Site Recovery. 

1. Jeśli nie masz planu, aby ponownie użyć żadnego z zasobów w regionie źródłowym, Usuń wszystkie odpowiednie zasoby sieciowe i odpowiednie konta magazynu. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server na maszynie wirtualnej z systemem Windows — często zadawane pytania](frequently-asked-questions-faq.md)
* [SQL Server wskazówki dotyczące cennika maszyn wirtualnych z systemem Windows](pricing-guidance.md)
* [SQL Server w informacjach o wersji maszyny wirtualnej z systemem Windows](doc-changes-updates-release-notes.md)


