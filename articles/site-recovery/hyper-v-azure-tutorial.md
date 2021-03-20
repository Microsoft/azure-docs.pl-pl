---
title: Konfigurowanie odzyskiwania po awarii funkcji Hyper-V przy użyciu Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V (bez programu VMM) na platformę Azure przy użyciu Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ce98c785700301bba92926d7d5a243b614eca7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87504232"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez zarządzanie replikacją, trybem failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure.

Jest to trzeci samouczek z tej serii. Pokazano, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure. Ten samouczek dotyczy maszyn wirtualnych funkcji Hyper-V, które nie są zarządzane przez program Microsoft System Center Virtual Machine Manager (VMM).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Skonfiguruj środowisko replikacji źródłowej, w tym lokalne składniki Site Recovery i środowisko docelowej replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami w sekcji **przewodniki** z instrukcjami w [dokumentacji Site Recovery](./index.yml).



## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jest to trzeci samouczek z tej serii. Przyjęto założenie, że zadania zostały już wykonane w poprzednich samouczkach:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W Azure Portal przejdź do obszaru **magazyny Recovery Services** i wybierz magazyn. **ContosoVMVault** magazynu został przygotowany w poprzednim samouczku.
2. W **wprowadzenie** wybierz pozycję **Site Recovery**, a następnie wybierz pozycję **Przygotuj infrastrukturę**.
3. W obszarze **cel ochrony**, gdzie znajdują się  >  **maszyny?** wybierz pozycję **lokalna**.
4. W obszarze **gdzie chcesz replikować maszyny?** wybierz pozycję **na platformie Azure**.
5. W obszarze **czy maszyny są zwirtualizowane?** wybierz pozycję **tak, używając funkcji Hyper-V**.
6. Czy w programie **System Center VMM jest używany do zarządzania hostami funkcji Hyper-V?** wybierz pozycję **nie**.
7. Wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający opcje celu ochrony w temacie Przygotowanie infrastruktury.](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

1. W przypadku **planowania wdrożenia**, jeśli planujesz duże wdrożenie, Pobierz planista wdrażania dla funkcji Hyper-V z linku na stronie. [Dowiedz się więcej](hyper-v-deployment-planner-overview.md) o planowaniu wdrożenia funkcji Hyper-V.
2. W tym samouczku nie są potrzebne Planista wdrażania. Czy w programie **wykonano Planowanie wdrożenia?** zaznacz opcję **chcę zrobić to później**, a następnie wybierz **przycisk OK**.

    ![Zrzut ekranu przedstawiający Opcje planowania wdrożenia w temacie Przygotowanie infrastruktury.](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, należy utworzyć lokację funkcji Hyper-V i dodać do tej lokacji hosty funkcji Hyper-V zawierające maszyny wirtualne, które mają być replikowane. Następnie należy pobrać i zainstalować dostawcę Azure Site Recovery oraz agenta Recovery Services platformy Azure na każdym hoście i zarejestrować lokację funkcji Hyper-V w magazynie.

1. W obszarze **Przygotowanie infrastruktury** wybierz pozycję **Źródło**.
2. W obszarze **przygotowanie źródła** wybierz pozycję **+ Lokacja funkcji Hyper-V**.
3. W obszarze **Utwórz lokację funkcji Hyper-V** Określ nazwę lokacji. Korzystamy z usługi **ContosoHyperVSite**.

    ![Zrzut ekranu przedstawiający wybór witryny funkcji Hyper-V w obszarze Przygotowanie infrastruktury.](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po utworzeniu lokacji w obszarze **Przygotuj Źródło**  >  **krok 1: Wybierz lokację funkcji Hyper-V**, wybierz utworzoną witrynę.
5. Wybierz pozycję **+ serwer funkcji Hyper-V**.

    ![Zrzut ekranu przedstawiający wybór serwera funkcji Hyper-V w obszarze Przygotowanie infrastruktury.](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Pobierz Instalatora dla dostawcy Site Recovery Microsoft Azure.
7. Pobierz klucz rejestracji magazynu. Ten klucz jest wymagany do zainstalowania dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Zrzut ekranu przedstawiający opcje pobierania dostawcy i klucza rejestracji.](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Zainstaluj pobrany plik instalacyjny (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V, który ma zostać dodany do lokacji funkcji Hyper-V. Instalator instaluje dostawcę Azure Site Recovery i agenta Recovery Services na każdym hoście funkcji Hyper-V.

1. Uruchom plik instalacyjny.
2. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
3. W obszarze **Instalacja** zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie wybierz pozycję **Zainstaluj**.
4. Po zakończeniu instalacji w Kreatorze Microsoft Azure rejestracji Site Recovery > **ustawienia magazynu** wybierz pozycję **Przeglądaj**, a następnie w polu **plik klucza** wybierz pobrany plik klucza magazynu.
5. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
6. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
7. W obszarze **rejestracja** po zarejestrowaniu serwera w magazynie wybierz pozycję **Zakończ**.

Metadane z serwera funkcji Hyper-v są pobierane przez Azure Site Recovery, a serwer jest wyświetlany na   >  **hostach funkcji Hyper-v** Site Recovery Infrastructure. Ten proces może potrwać do 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalowanie dostawcy na serwerze podstawowym funkcji Hyper-V

Jeśli używasz serwera podstawowego funkcji Hyper-V, Pobierz plik Instalatora i wykonaj następujące kroki:

1. Wyodrębnij pliki z AzureSiteRecoveryProvider.exe do katalogu lokalnego, uruchamiając następujące polecenie:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Uruchom polecenie `.\setupdr.exe /i`. Wyniki są rejestrowane w%Programdata%\ASRLogs\DRASetupWizard.log.

3. Zarejestruj serwer, uruchamiając następujące polecenie:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i sprawdź zasoby docelowe:

1. Wybierz pozycję **Przygotuj**  >  **cel** infrastruktury.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG** , w której zostaną utworzone maszyny wirtualne platformy Azure po przejściu do trybu failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Wybierz kolejno pozycje **Przygotowanie infrastruktury**  >  **Ustawienia replikacji**  >  **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** Określ nazwę zasad. Korzystamy z usługi **ContosoReplicationPolicy**.
3. W tym samouczku pozostawimy ustawienia domyślne:
    - **Częstotliwość kopiowania** wskazuje, jak często będą replikowane dane różnicowe (po replikacji początkowej). Częstotliwość domyślna to co pięć minut.
    - **Przechowywanie punktów odzyskiwania** wskazuje, że punkty odzyskiwania będą przechowywane przez dwie godziny. Maksymalna dozwolona wartość przechowywania w przypadku ochrony maszyn wirtualnych hostowanych na hostach funkcji Hyper-V wynosi 24 godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasad wybierz **przycisk OK**. Nowo utworzone zasady są automatycznie kojarzone z określoną lokacją funkcji Hyper-V. W naszym samouczku **ContosoHyperVSite**.

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Włączanie replikacji

1. W obszarze **replikacja aplikacji** wybierz pozycję **Źródło**.
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie wybierz przycisk **OK**.
3. W obszarze **cel** Sprawdź, czy element docelowy (Azure), subskrypcję magazynu i model wdrażania **Menedżer zasobów** .
4. Jeśli używasz ustawień samouczka, wybierz konto magazynu **contosovmsacct1910171607** utworzone w poprzednim samouczku dla replikowanych danych. Wybierz również sieć **ContosoASRnet** , w której maszyny wirtualne platformy Azure będą znajdować się po przejściu do trybu failover.
5. Na stronie wybierz **maszynę wirtualną**  >  wybierz maszynę wirtualną, którą chcesz zreplikować. Następnie wybierz przycisk **OK**.

   Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **finalizowania ochrony** Replikacja początkowa zostanie zakończona, a maszyna wirtualna jest gotowa do przejścia w tryb failover.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
