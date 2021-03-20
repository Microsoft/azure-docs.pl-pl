---
title: Konfigurowanie funkcji Hyper-V (z programem VMM) odzyskiwanie awaryjne przy użyciu Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center VMM do platformy Azure przy użyciu Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89442824"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach VMM na platformie Azure

W tym samouczku opisano sposób włączania replikacji lokalnych maszyn wirtualnych funkcji Hyper-V zarządzanych przez System Center Virtual Machine Manager (VMM) do platformy Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md). Jeśli nie korzystasz z programu VMM, [postępuj zgodnie z tym samouczkiem](hyper-v-azure-tutorial.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Skonfiguruj środowisko replikacji źródłowej, w tym lokalne składniki Site Recovery i środowisko docelowej replikacji.
> * Skonfiguruj mapowanie sieci między sieciami maszyn wirtualnych programu VMM i sieciami wirtualnymi platformy Azure.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w sekcji **przewodniki** z instrukcjami w [dokumentacji Site Recovery](./index.yml).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że zostały już wykonane następujące samouczki:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
1. [Przygotowywanie lokalnych serwerów funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W Azure Portal przejdź do obszaru **magazyny Recovery Services** i wybierz magazyn **ContosoVMVault** , który został utworzony w samouczku [Przygotowywanie platformy Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) .
1. W obszarze **wprowadzenie** wybierz pozycję **Site Recovery**  >  **Przygotuj infrastrukturę** i skonfiguruj następujące ustawienia:
    1. **Cel ochrony**  >  **Gdzie znajdują się maszyny?** wybierz pozycję **lokalnie**.
    1. **Gdzie chcesz replikować maszyny?** wybierz pozycję **na platformie Azure**.
    1. **Czy maszyny są zwirtualizowane?** wybierz pozycję **tak, używając funkcji Hyper-V**.
    1. **Czy używasz programu System Center VMM do zarządzania hostami funkcji Hyper-V?**, wybierz opcję **tak**.
1. Wybierz przycisk **OK**.

   ![Cel replikacji](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W przypadku **planowania wdrożenia**, jeśli planujesz duże wdrożenie, Pobierz planista wdrażania dla funkcji Hyper-V z linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
1. W tym samouczku nie są potrzebne Planista wdrażania. Czy w programie **wykonano Planowanie wdrożenia?** zaznacz opcję **chcę zrobić to później**, a następnie wybierz **przycisk OK**.

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Podczas konfigurowania środowiska źródłowego należy zainstalować dostawcę Azure Site Recovery na serwerze programu VMM i zarejestrować serwer w magazynie. Należy zainstalować agenta Recovery Services platformy Azure na każdym hoście funkcji Hyper-V.

1. **Przygotuj infrastrukturę**. Wybierz pozycję **Źródło**.
1. **Przygotuj Źródło**. Wybierz pozycję **+ VMM** , aby dodać serwer programu VMM. W obszarze **Dodaj serwer** sprawdź, czy pozycja **Serwer System Center VMM** pojawia się jako **Typ serwera**.
1. Pobierz Instalatora dla dostawcy Site Recovery Microsoft Azure.
1. Pobierz klucz rejestracji magazynu. Ten klucz będzie potrzebny podczas uruchamiania Instalatora dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.
1. Pobierz Instalatora dla agenta Microsoft Azure Recovery Services.

   ![Pobierz dostawcę, klucz rejestracji i agenta](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalowanie dostawcy na serwerze VMM

1. W **Microsoft Update** Kreatora instalacji dostawcy Azure Site Recovery. Wybierz, aby użyć Microsoft Update do sprawdzenia dostępności aktualizacji dostawcy.
1. **Instalacja**. Zaakceptuj domyślną lokalizację instalacji dostawcy i wybierz pozycję **Zainstaluj**.
1. Po zakończeniu instalacji w Kreatorze Microsoft Azure rejestracji Site Recovery wybierz pozycję **ustawienia magazynu**, **Przeglądaj** i w polu **plik klucza** wybierz pobrany plik klucza magazynu.
1. Określ subskrypcję Azure Site Recovery i nazwę magazynu (**ContosoVMVault**). Określ przyjazną nazwę serwera programu VMM, aby zidentyfikować ją w magazynie.
1. **Ustawienia serwera proxy**. Wybierz pozycję **Połącz bezpośrednio, aby Azure Site Recovery bez serwera proxy**.
1. Zaakceptuj domyślną lokalizację certyfikatu używanego do szyfrowania danych. Zaszyfrowane dane zostaną odszyfrowane po przełączeniu w tryb failover.
1. **Zsynchronizuj metadane chmury**. Wybierz pozycję **Synchronizuj metadane chmury do portalu Site Recovery**. Ta akcja musi wystąpić tylko raz na każdym serwerze. Następnie wybierz pozycję **zarejestruj**.
1. Po zarejestrowaniu serwera w magazynie wybierz pozycję **Zakończ**.

Po zakończeniu rejestracji metadane z serwera są pobierane przez Azure Site Recovery, a serwer VMM jest wyświetlany w **infrastrukturze Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalowanie agenta usług odzyskiwania na hostach funkcji Hyper-V

Zainstaluj agenta na każdym hoście funkcji Hyper-V zawierającym maszyny wirtualne, które chcesz replikować.

W Kreatorze instalacji agenta Microsoft Azure Recovery Services skonfiguruj następujące ustawienia:

1. **Sprawdzanie wymagań wstępnych**. Wybierz opcję **Dalej**. Wszystkie brakujące wymagania wstępne zostaną zainstalowane automatycznie.
1. **Ustawienia instalacji**. Zaakceptuj lokalizację instalacji. Następnie wybierz pozycję **Zainstaluj**.

    >[!NOTE]
    >W przypadku Azure Site Recovery **Lokalizacja pamięci podręcznej** nie jest wymagana.

1. **Instalacja**. Po zakończeniu instalacji wybierz pozycję **Zamknij** , aby zakończyć pracę kreatora.

   ![Zainstaluj agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

1. Wybierz pozycję **Przygotuj**  >  **cel** infrastruktury.
1. Wybierz subskrypcję i grupę zasobów (**ContosoRG**), w której zostaną utworzone maszyny wirtualne platformy Azure po przejściu do trybu failover.
1. Wybierz model wdrażania **Menedżer zasobów** .

Site Recovery sprawdza, czy istnieje co najmniej jedno zgodne konto usługi Azure Storage i sieci.

## <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. **Infrastruktura Site Recovery**  >  **Mapowania sieci**  >  **Mapowanie sieci**. Wybierz ikonę **+ mapowanie sieci** .
1. **Dodaj mapowanie sieci**. Wybierz **źródłowy serwer programu System Center VMM** . Dla **elementu docelowego** wybierz pozycję Azure.
1. Sprawdź subskrypcję i model wdrożenia po przejściu do trybu failover.
1. **Sieć źródłowa**. Wybierz źródłową lokalną maszynę wirtualną.
1. **Sieć docelowa**. Wybierz sieć platformy Azure, w której będą znajdować się repliki maszyn wirtualnych platformy Azure po ich utworzeniu po przejściu do trybu failover. Następnie wybierz przycisk **OK**.

   ![Mapowanie sieci](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz kolejno pozycje **Przygotowanie infrastruktury**  >  **Ustawienia replikacji**  >  **+ Utwórz i skojarz**.
1. W obszarze **Utwórz i skojarz zasady** Określ nazwę zasad. Korzystamy z usługi **ContosoReplicationPolicy**.
1. Zaakceptuj ustawienia domyślne i wybierz **przycisk OK**:
   - **Częstotliwość kopiowania** wskazuje, że po replikacji początkowej dane różnicowe będą replikowane co pięć minut.
   - **Przechowywanie punktów odzyskiwania** wskazuje, że każdy punkt odzyskiwania będzie przechowywany przez dwie godziny.
   - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
   - **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
   - **Szyfrowanie danych przechowywanych na platformie Azure** jest ustawione na wartość domyślne (**wyłączone**) i wskazuje, że dane w spoczynku na platformie Azure nie są szyfrowane.
1. Po utworzeniu zasad wybierz **przycisk OK**. Nowo utworzone zasady są automatycznie kojarzone z chmurą programu VMM.

## <a name="enable-replication"></a>Włączanie replikacji

1. **Replikowanie aplikacji**. Wybierz pozycję **Źródło**.
1. **Źródło**. Wybierz chmurę programu VMM. Następnie wybierz przycisk **OK**.
1. **Element docelowy**. Sprawdź element docelowy (Azure), subskrypcję magazynu i wybierz model **Menedżer zasobów** .
1. Wybierz konto magazynu **contosovmsacct1910171607** i Sieć **ContosoASRnet** Azure.
1. **Maszyny wirtualne**  >  **Wybierz opcję**. Wybierz maszynę wirtualną, którą chcesz replikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **finalizowania ochrony** Replikacja początkowa zostanie zakończona, a maszyna wirtualna jest gotowa do przejścia w tryb failover.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
