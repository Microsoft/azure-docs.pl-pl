---
title: Konfiguracja magazynu dla maszyn wirtualnych SQL Server | Microsoft Docs
description: W tym temacie opisano, jak platforma Azure konfiguruje magazyn dla SQL Server maszyn wirtualnych podczas aprowizacji (Azure Resource Manager model wdrażania). Wyjaśniono również, jak można skonfigurować magazyn dla istniejących maszyn wirtualnych SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 3a4b7d68d7cd21ccb4b7eb8b97e0d331fb236e96
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146726"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguracja usługi Storage dla maszyn wirtualnych programu SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W przypadku skonfigurowania SQL Server obrazu maszyny wirtualnej (VM) na platformie Azure Azure Portal pomaga zautomatyzować konfigurację magazynu. Obejmuje to dołączenie magazynu do maszyny wirtualnej, dzięki czemu można uzyskać dostęp do tego magazynu SQL Server i skonfigurować go do optymalizacji pod kątem konkretnych wymagań dotyczących wydajności.

W tym temacie wyjaśniono, jak platforma Azure konfiguruje magazyn dla maszyn wirtualnych SQL Server zarówno podczas aprowizacji, jak i dla istniejących maszyn wirtualnych. Ta konfiguracja jest oparta na [najlepszych rozwiązaniach dotyczących wydajności](performance-guidelines-best-practices.md) dla maszyn wirtualnych platformy Azure z systemem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z ustawień konfiguracji automatycznego magazynu, maszyna wirtualna wymaga następujących cech:

* Udostępnione za pomocą [obrazu galerii SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).
* Używa [modelu wdrażania Menedżer zasobów](../../../azure-resource-manager/management/deployment-models.md).
* Używa warstwy [Premium dysków SSD](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne

W poniższych sekcjach opisano sposób konfigurowania magazynu dla nowych maszyn wirtualnych SQL Server.

### <a name="azure-portal"></a>Azure Portal

Podczas aprowizacji maszyny wirtualnej platformy Azure przy użyciu obrazu galerii SQL Server wybierz pozycję **Zmień konfigurację** na karcie **Ustawienia SQL Server** , aby otworzyć stronę Konfiguracja magazynu zoptymalizowanego pod kątem wydajności. Możesz pozostawić wartości domyślne lub zmodyfikować typ konfiguracji dysku, który najlepiej odpowiada Twoim potrzebom, na podstawie obciążenia. 

![Zrzut ekranu, który podświetla kartę Ustawienia SQL Server i opcję Zmień konfigurację.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Wybierz typ obciążenia, które wdrażasz SQL Server na potrzeby **optymalizacji magazynu** . W przypadku opcji **ogólnej** optymalizacji domyślnie istnieje jeden dysk z danymi o pojemności 5000 maks. liczba IOPS i ten sam dysk jest używany do przechowywania danych, dzienników transakcji i magazynu tempdb. Wybranie opcji **przetwarzanie transakcyjne** (OLTP) lub **Magazyn danych** spowoduje utworzenie oddzielnego dysku dla danych, oddzielnego dysku dla dziennika transakcji i użycie lokalnego SSD dla bazy tempdb. Nie istnieją różnice między **przetwarzaniem transakcyjnym** i **magazynem danych** , ale zmieniają [konfigurację rozłożenia i flagi śledzenia](#workload-optimization-settings). Wybranie usługi Premium Storage ustawia buforowanie do *odczytu* dla dysku danych i *Brak* dla dysku dziennika jako SQL Server najlepszych rozwiązań dotyczących [wydajności maszyn wirtualnych](performance-guidelines-best-practices.md). 

![SQL Server konfigurację magazynu maszyny wirtualnej podczas aprowizacji](./media/storage-configuration/sql-vm-storage-configuration.png)

Konfiguracja dysku jest w pełni dostosowywana, co pozwala na skonfigurowanie topologii magazynu, typu dysku i liczby IOPs potrzebnych dla SQL Server obciążenia maszyny wirtualnej. Istnieje również możliwość korzystania z UltraSSD (wersja zapoznawcza) jako opcji dla **typu dysku** , jeśli maszyna wirtualna SQL Server znajduje się w jednym z obsługiwanych regionów (Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Północna) i włączono [dla niej Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Ponadto można ustawić buforowanie dla dysków. Maszyny wirtualne platformy Azure mają wielowarstwową technologię buforowania o nazwie [BLOB cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) , gdy jest używana z [dyskami w warstwie Premium](../../../virtual-machines/disks-types.md#premium-ssd). Pamięć podręczna obiektów BLOB używa kombinacji pamięci RAM maszyny wirtualnej i lokalnego dysku SSD na potrzeby buforowania. 

Buforowanie dysków dla SSD w warstwie Premium może być *tylko do odczytu* , *ReadWrite* lub *none* . 

- Buforowanie *tylko do odczytu* jest wysoce korzystne dla SQL Server plików danych przechowywanych w Premium Storage. Buforowanie w trybie *tylko* do odczytu umożliwia opóźnienie w przypadku małych odczytów, dużą liczbę operacji we/wy odczytu i przepływność, co oznacza, że operacje odczytu są wykonywane z pamięci podręcznej, która jest w pamięci Te odczyty są znacznie szybsze niż odczyt z dysku danych, który pochodzi z usługi Azure Blob Storage. Usługa Premium Storage nie zlicza odczytów obsługiwanych z pamięci podręcznej do operacji we/wy na dysku. W związku z tym, Twoje zastosowanie ma możliwość osiągnięcia wyższej całkowitej liczby operacji we/wy na sekundę. 
- *Nie należy* używać konfiguracji pamięci podręcznej dla dysków hostującym SQL Server pliku dziennika, ponieważ plik dziennika jest pisany sekwencyjnie i nie korzysta z buforowania *tylko do odczytu* . 
- Buforowanie *ReadWrite* nie powinno być używane do hostowania plików SQL Server, ponieważ SQL Server nie obsługuje spójności danych z pamięcią podręczną *ReadWrite* . Zapisy pojemności pamięci podręcznej obiektów BLOB *tylko do odczytu* i opóźnień nieco zwiększają się, jeśli zapisy przechodzą *przez warstwy* pamięci podręcznej obiektów BLOB. 


   > [!TIP]
   > Upewnij się, że konfiguracja magazynu jest zgodna z ograniczeniami narzuconymi przez wybrany rozmiar maszyny wirtualnej. Wybranie parametrów magazynu, które przekraczają limit wydajności dla rozmiaru maszyny wirtualnej, spowoduje błąd: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.` . Zmniejsz liczbę operacji we/wy przez zmianę typu dysku lub zwiększenie ograniczenia wydajności przez zwiększenie rozmiaru maszyny wirtualnej. 


Na podstawie wybranych opcji platforma Azure wykonuje następujące zadania konfiguracji magazynu po utworzeniu maszyny wirtualnej:

* Tworzy i dołącza dysków SSD Premium do maszyny wirtualnej.
* Konfiguruje dyski danych, które mają być dostępne dla SQL Server.
* Konfiguruje dyski danych w puli magazynów na podstawie określonych wymagań dotyczących rozmiaru i wydajności (IOPS i przepływności).
* Kojarzy pulę magazynów z nowym dyskiem na maszynie wirtualnej.
* Optymalizuje ten nowy dysk w oparciu o określony typ obciążenia (magazynowanie danych, przetwarzanie transakcyjne lub ogólne).

Aby uzyskać więcej informacji na temat konfigurowania ustawień magazynu przez platformę Azure, zobacz [sekcję Konfiguracja magazynu](#storage-configuration). Aby zapoznać się z pełnym przewodnikiem dotyczącym tworzenia SQL Server maszyny wirtualnej w Azure Portal, zobacz [samouczek aprowizacji](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

W przypadku używania następujących szablonów Menedżer zasobów są domyślnie dołączone dwa dyski z danymi Premium bez konfiguracji puli magazynu. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych w warstwie Premium dołączonych do maszyny wirtualnej.

* [Tworzenie maszyny wirtualnej przy użyciu zautomatyzowanej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny wirtualnej z automatyczną poprawką](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny wirtualnej z integracją AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Szablon szybkiego startu

Poniższy szablon szybkiego startu umożliwia wdrożenie maszyny wirtualnej SQL Server przy użyciu funkcji optymalizacji magazynu. 

* [Tworzenie maszyny wirtualnej przy użyciu optymalizacji magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Tworzenie maszyny wirtualnej przy użyciu UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych SQL Server można zmodyfikować niektóre ustawienia magazynu w Azure Portal. Otwórz [zasób usługi SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)i wybierz pozycję **Przegląd** . Na stronie Przegląd SQL Server jest wyświetlane bieżące użycie magazynu dla maszyny wirtualnej. Na tym wykresie są wyświetlane wszystkie dyski, które znajdują się na maszynie wirtualnej. Dla każdego dysku jest wyświetlana w czterech sekcjach:

* Dane SQL
* Dziennik SQL
* Inne (magazyn inny niż SQL)
* Dostępne

Aby zmodyfikować ustawienia magazynu, wybierz pozycję **Konfiguruj** w obszarze **Ustawienia** . 

![Zrzut ekranu, który podświetla opcję Konfiguruj i sekcję użycie magazynu.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Można zmodyfikować ustawienia dysku dla dysków, które zostały skonfigurowane podczas procesu tworzenia maszyny wirtualnej SQL Server. Wybranie pozycji **rozszerzająca dysk** otwiera stronę modyfikacja dysku, umożliwiając zmianę typu dysku, a także dodanie dodatkowych dysków. 

![Konfigurowanie magazynu dla istniejącej maszyny wirtualnej SQL Server](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Ta sekcja zawiera informacje o zmianach konfiguracji magazynu, które usługa Azure automatycznie wykonuje podczas SQL Server aprowizacji lub konfiguracji maszyny wirtualnej w Azure Portal.

* Platforma Azure konfiguruje pulę magazynów z magazynu wybranego z maszyny wirtualnej. Następna sekcja tego tematu zawiera szczegółowe informacje o konfiguracji puli magazynu.
* Funkcja automatycznej konfiguracji magazynu zawsze używa dysków danych w [warstwie Premium dysków SSD](../../../virtual-machines/disks-types.md) P30. W związku z tym istnieje 1:1 mapowanie między wybraną liczbą terabajtów a liczbą dysków danych podłączonych do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz stronę z [cennikiem usługi Storage](https://azure.microsoft.com/pricing/details/storage) na karcie **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynów

Platforma Azure używa następujących ustawień do utworzenia puli magazynów na SQL Server maszynach wirtualnych.

| Ustawienie | Wartość |
| --- | --- |
| Rozmiar paska |256 KB (magazynowanie danych); 64 KB (transakcyjna) |
| Rozmiary dysków |1 TB każdego |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |rozmiar jednostki alokacji systemu plików NTFS 64 KB |
| Odzyskiwania | Odzyskiwanie proste (bez odporności) |
| Liczba kolumn |Liczba dysków z danymi do 8<sup>1</sup> |


<sup>1</sup> po utworzeniu puli magazynów nie można zmienić liczby kolumn w puli magazynów.


## <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia

W poniższej tabeli opisano trzy dostępne opcje typu obciążenia oraz ich odpowiednie optymalizacje:

| Typ obciążenia | Opis | Optymalizacje |
| --- | --- | --- |
| **Ogólne** |Ustawienie domyślne obsługujące większość obciążeń |Brak |
| **Przetwarzanie transakcyjne** |Optymalizuje magazyn dla tradycyjnych obciążeń OLTP bazy danych |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowanie danych** |Optymalizuje magazyn do obciążeń analitycznych i sprawozdawczych |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko podczas inicjowania obsługi administracyjnej maszyny wirtualnej SQL Server, wybierając ją w kroku konfiguracji magazynu.

## <a name="next-steps"></a>Następne kroki

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).