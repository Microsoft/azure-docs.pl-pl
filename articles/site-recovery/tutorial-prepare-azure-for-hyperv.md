---
title: Przygotuj platformę Azure do odzyskiwania po awarii funkcji Hyper-V za pomocą Azure Site Recovery
description: Dowiedz się, jak przygotować platformę Azure do odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V za pomocą Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ca03336fc60abbb458615e922a2556dd21a79a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739682"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Przygotowywanie zasobów platformy Azure na potrzeby odzyskiwania po awarii funkcji Hyper-V

 [Azure Site Recovery](site-recovery-overview.md) zapewnia ciągłość biznesową i odzyskiwanie po awarii (BCDR), dzięki czemu aplikacje biznesowe działają w trakcie planowanych i nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

Ten samouczek jest pierwszą częścią serii, która opisuje sposób konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych funkcji Hyper-V.

> [!NOTE]
> Zalecamy projektowanie samouczków, aby pokazać najprostszą ścieżkę wdrożenia dla scenariusza. Te samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać więcej informacji, zapoznaj się z sekcją "How to" w przypadku każdego odpowiadającego scenariusza.

W tym samouczku przedstawiono sposób przygotowania składników platformy Azure w celu replikowania lokalnych maszyn wirtualnych (Hyper-V) na platformę Azure. Omawiane tematy:

> [!div class="checklist"]
> * Sprawdzanie, czy konto platformy Azure ma uprawnienia do replikacji.
> * Utwórz konto usługi Azure Storage, które przechowuje obrazy replikowanych maszyn.
> * Utwórz magazyn Recovery Services, który przechowuje metadane i informacje o konfiguracji dla maszyn wirtualnych i innych składników replikacji.
> * Skonfiguruj sieć platformy Azure. Gdy maszyny wirtualne platformy Azure są tworzone po przejściu w tryb failover, są one przyłączone do tej sieci.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in"></a>Zaloguj

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Sprawdzanie uprawnień konta

Jeśli utworzono bezpłatne konto platformy Azure, jesteś administratorem dla tej subskrypcji. Jeśli nie jesteś administratorem, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla nowej maszyny wirtualnej, konieczne jest posiadanie następujących uprawnień:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na wybranym koncie magazynu.

Aby wykonać te zadania, Twoje konto powinno mieć przypisaną wbudowaną rolę współautor maszyny wirtualnej. Aby zarządzać operacjami Site Recovery w magazynie, należy przypisać do konta rolę wbudowaną Site Recovery współautor.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Obrazy replikowanych maszyn są przechowywane w usłudze Azure Storage. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services.

1. W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**  >  **Magazyn**  >  **konto magazynu — obiekt BLOB, plik, tabela, kolejka**.
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta.  Wybrana nazwa musi być unikatowa w obrębie platformy Azure, mieć długość od 3 do 24 znaków i zawierać tylko małe litery i cyfry. Na potrzeby tego samouczka Użyj **contosovmsacct1910171607**.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W obszarze **rodzaj konta** wybierz pozycję **Magazyn (ogólnego przeznaczenia w wersji 1)**. Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu.
6. W obszarze **Wydajność** wybierz opcję **Standardowa**. Następnie w obszarze **Warstwa dostępu** wybierz domyślną opcję **gorąca**.
7. W obszarze **subskrypcja** wybierz subskrypcję, w której chcesz utworzyć nowe konto magazynu.
8. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure. Na potrzeby tego samouczka Użyj **ContosoRG**.
9. W obszarze **Lokalizacja** wybierz lokalizację geograficzną dla swojego konta magazynu. W tym samouczku należy użyć **Europa Zachodnia**.
10. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

   ![Zrzut ekranu przedstawiający opcje Utwórz konto magazynu.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W Azure Portal wybierz pozycję **+ Utwórz zasób**, a następnie wyszukaj w witrynie Azure Marketplace pozycję Recovery Services.
2. Wybierz **Backup and Site Recovery (OMS)**. Następnie na stronie **kopia zapasowa i Site Recovery** wybierz pozycję **Utwórz**.
1. W obszarze **Magazyn usługi Recovery services > nazwa** wpisz przyjazną nazwę, aby zidentyfikować magazyn. Na potrzeby tego samouczka użyj nazwy **ContosoVMVault**.
2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Na potrzeby tego samouczka Użyj **contosoRG**.
3. W obszarze **Lokalizacja** wybierz region, w którym powinien się znajdować magazyn. W tym samouczku należy użyć **Europa Zachodnia**.
4. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**  >  **Utwórz**.

![Zrzut ekranu przedstawiający stronę tworzenie magazynu Recovery Services.](./media/tutorial-prepare-azure/new-vault-settings.png)

Nowy magazyn będzie wyświetlany na **pulpicie nawigacyjnym**  >  **wszystkie zasoby** i na stronie **magazyny główne Recovery Services** .

## <a name="set-up-an-azure-network"></a>Konfiguracja sieci platformy Azure

Gdy maszyny wirtualne platformy Azure są tworzone na podstawie magazynu po przejściu w tryb failover, dołączają one do tej sieci.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Sieć** > **Sieć wirtualna**. Jako model wdrażania pozostaw wybraną pozycję Resource Manager.
2. W obszarze **Nazwa** wprowadź nazwę sieci. Nazwa musi być unikatowa w obrębie grupy zasobów platformy Azure. Na potrzeby tego samouczka Użyj **ContosoASRnet**.
3. Określ grupę zasobów, w której chcesz utworzyć sieć. Na potrzeby tego samouczka Użyj istniejącej grupy zasobów **contosoRG**.
4. W obszarze **zakres adresów** wprowadź wartość **10.0.0.0/24** jako zakres sieci. Brak podsieci dla tej sieci.
5. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć sieć.
6. W obszarze **Lokalizacja** wybierz pozycję **Europa Zachodnia**. Sieć musi znajdować się w tym samym regionie co magazyn Usług odzyskiwania.
7. Pozostaw domyślne opcje podstawowej ochrony DDoS bez punktu końcowego usługi w sieci.
8. Wybierz przycisk **Utwórz**.

![Zrzut ekranu przedstawiający opcje tworzenia sieci wirtualnej.](media/tutorial-prepare-azure/create-network.png)

Utworzenie sieci wirtualnej zajmuje kilka sekund. Po jego utworzeniu zobaczysz go na pulpicie nawigacyjnym Azure Portal.

## <a name="useful-links"></a>Przydatne łącza

Dowiedz się więcej:
- [Sieci platformy Azure](../virtual-network/virtual-networks-overview.md)
- [Dyski zarządzane](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przygotowywanie lokalnej infrastruktury funkcji Hyper-V na potrzeby odzyskiwania po awarii na platformie Azure](hyper-v-prepare-on-premises-tutorial.md)
