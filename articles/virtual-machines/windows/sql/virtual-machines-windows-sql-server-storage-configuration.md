---
title: Konfiguracja magazynu dla maszyn wirtualnych programu SQL Server | Dokumenty firmy Microsoft
description: W tym temacie opisano, jak platforma Azure konfiguruje magazyn dla maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej (model wdrażania Menedżera zasobów). Wyjaśniono również, jak można skonfigurować magazyn dla istniejących maszyn wirtualnych programu SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482151"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Konfiguracja usługi Storage dla maszyn wirtualnych programu SQL Server

Podczas konfigurowania obrazu maszyny wirtualnej programu SQL Server na platformie Azure portal pomaga zautomatyzować konfigurację magazynu. Obejmuje to dołączanie magazynu do maszyny Wirtualnej, uczynienie tego magazynu dostępnym dla programu SQL Server i konfigurowanie go w celu optymalizacji pod kątem określonych wymagań dotyczących wydajności.

W tym temacie wyjaśniono, jak platforma Azure konfiguruje magazyn dla maszyn wirtualnych programu SQL Server zarówno podczas inicjowania obsługi administracyjnej, jak i dla istniejących maszyn wirtualnych. Ta konfiguracja jest oparta na [najlepszych praktykach dotyczących wydajności](virtual-machines-windows-sql-performance.md) dla maszyn wirtualnych platformy Azure z systemem SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć ustawień konfiguracji magazynu automatycznego, maszyna wirtualna wymaga następujących cech:

* Aprowizowana z [obrazem galerii programu SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Używa [modelu wdrażania Menedżera zasobów](../../../azure-resource-manager/management/deployment-models.md).
* Używa [najwyższej jakości SSD](../disks-types.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne

W poniższych sekcjach opisano sposób konfigurowania magazynu dla nowych maszyn wirtualnych programu SQL Server.

### <a name="azure-portal"></a>Azure Portal

Podczas inicjowania obsługi administracyjnej maszyny Wirtualnej platformy Azure przy użyciu obrazu galerii programu SQL Server wybierz pozycję **Zmień konfigurację** na karcie **Ustawienia programu SQL Server,** aby otworzyć stronę Konfiguracja zoptymalizowanego pod kątem wydajności magazynu. Można pozostawić wartości domyślnie lub zmodyfikować typ konfiguracji dysku, który najlepiej odpowiada Twoim potrzebom w zależności od obciążenia. 

![Konfiguracja magazynu maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Wybierz typ obciążenia, dla którego wdrażasz program SQL Server w obszarze **Optymalizacja magazynu.** Dzięki opcji **Optymalizacja ogólna** domyślnie będziesz mieć jeden dysk danych z 5000 max IOPS i będzie używany ten sam dysk dla danych, dziennika transakcji i magazynu TempDB. Wybranie **przetwarzania transakcyjnego** (OLTP) lub **hurtowni danych** spowoduje utworzenie osobnego dysku dla danych, osobnego dysku dla dziennika transakcji i użycie lokalnego dysku SSD dla bazy danych TempDB. Nie ma różnic w pamięci masowej między **przetwarzaniem transakcyjnym** a **magazynowaniem danych,** ale zmienia [konfigurację paska i flagi śledzenia.](#workload-optimization-settings) Wybranie magazynu w wersji premium ustawia buforowanie na *Odczyt tylko* dla dysku danych, a *brak* dla dysku dziennika zgodnie z [najlepszymi praktykami wydajności programu SQL Server VM.](virtual-machines-windows-sql-performance.md) 

![Konfiguracja magazynu maszyn wirtualnych programu SQL Server podczas inicjowania obsługi administracyjnej](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Konfiguracja dysku jest w pełni konfigurowalna, dzięki czemu można skonfigurować topologię magazynu, typ dysku i we/wy potrzebne dla obciążenia maszyny wirtualnej programu SQL Server. Masz również możliwość korzystania z UltraSSD (preview) jako opcji dla **typu dysku,** jeśli maszyna wirtualna programu SQL Server znajduje się w jednym z obsługiwanych regionów (Wschodnia us2, Południowo-Południowa Azja i Europa Północna) i włączono [dyski ultra dla subskrypcji](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Ponadto masz możliwość ustawiania buforowania dysków. Maszyny wirtualne platformy Azure mają wielowarstwową technologię buforowania o nazwie [Pamięć podręczna obiektów Blob,](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) gdy jest używana z [dyskami premium.](/azure/virtual-machines/windows/disks-types#premium-ssd) Pamięć podręczna obiektów Blob używa kombinacji pamięci RAM maszyny wirtualnej i lokalnego identyfikatora SSD do buforowania. 

Buforowanie dysków dla dysków SSD premium można *odczytać tylko*, *ReadWrite* lub *None*. 

- *ReadOnly* buforowanie jest bardzo korzystne dla plików danych programu SQL Server, które są przechowywane w magazynie w jakości. *Odczyt tylko* buforowanie przynosi małe opóźnienie odczytu, wysoki odczyt We/Wy i przepływność jako odczyty są wykonywane z pamięci podręcznej, która znajduje się w pamięci maszyny Wirtualnej i lokalnego SSD. Te odczyty są znacznie szybsze niż odczyty z dysku danych, który jest z magazynu obiektów blob platformy Azure. Magazyn w warstwie Premium nie zlicza odczytów obsługiwanych z pamięci podręcznej w kierunku iOPS dysku i przepływności. W związku z tym odpowiednie jest w stanie osiągnąć wyższą całkowitą liczbę we/wy i przepływność. 
- *Żadna* konfiguracja pamięci podręcznej nie powinna być używana dla dysków hostujących plik dziennika programu SQL Server, ponieważ plik dziennika jest zapisywany sekwencyjnie i nie korzysta z buforowania *tylko* do odczytu. 
- *Buforowanie ReadWrite* nie powinno być używane do hostowania plików programu SQL Server, ponieważ program SQL Server nie obsługuje spójności danych z pamięcią podręczną *ReadWrite.* Zapisuje pojemność odpadów pamięci podręcznej obiektów blob *tylko do odczytu* i opóźnienia nieznacznie zwiększyć, jeśli zapisy przejść przez *ReadOnly* warstw pamięci podręcznej obiektów blob. 


   > [!TIP]
   > Upewnij się, że konfiguracja magazynu jest zgodna z ograniczeniami nałożonymi przez wybrany rozmiar maszyny Wirtualnej. Wybranie parametrów magazynu, które przekraczają limit wydajności rozmiaru `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`maszyny Wirtualnej spowoduje błąd: . Zmniejsz IOP, zmieniając typ dysku lub zwiększ ograniczenie limitu wydajności, zwiększając rozmiar maszyny Wirtualnej. 


Na podstawie twoich wyborów platforma Azure wykonuje następujące zadania konfiguracji magazynu po utworzeniu maszyny Wirtualnej:

* Tworzy i dołącza dyski SSD premium do maszyny wirtualnej.
* Konfiguruje dyski danych, które mają być dostępne dla programu SQL Server.
* Konfiguruje dyski danych w puli magazynu na podstawie określonych wymagań dotyczących rozmiaru i wydajności (We/Wy i przepływności).
* Kojarzy pulę magazynu z nowym dyskiem na maszynie wirtualnej.
* Optymalizuje ten nowy dysk na podstawie określonego typu obciążenia (hurtownia danych, przetwarzanie transakcyjne lub ogólne).

Aby uzyskać więcej informacji na temat konfigurowania ustawień magazynu przez platformę Azure, zobacz [sekcję Konfiguracja magazynu](#storage-configuration). Aby uzyskać pełny instruktaż sposobu tworzenia maszyny Wirtualnej programu SQL Server w witrynie Azure portal, zobacz [samouczek inicjowania obsługi administracyjnej](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Szablony zarządzania zasobami

Jeśli używasz następujących szablonów Menedżera zasobów, dwa dyski danych w warstwie Premium są domyślnie dołączone, bez konfiguracji puli magazynu. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych premium, które są dołączone do maszyny wirtualnej.

* [Tworzenie maszyny wirtualnej za pomocą automatycznej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny wirtualnej za pomocą automatycznego łatanie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny wirtualnej z integracją AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Szablon szybkiego startu

Do wdrożenia maszyny Wirtualnej programu SQL Server przy użyciu optymalizacji magazynu można użyć następującego szablonu szybkiego startu. 

* [Tworzenie maszyny Wirtualnej z optymalizacją magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Tworzenie maszyny wirtualnej przy użyciu ultrassd](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych programu SQL Server można zmodyfikować niektóre ustawienia magazynu w witrynie Azure portal. Otwórz [zasób maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)i wybierz **pozycję Przegląd**. Strona Omówienie programu SQL Server pokazuje bieżące użycie magazynu maszyny Wirtualnej. Wszystkie dyski, które istnieją na maszynie wirtualnej są wyświetlane na tym wykresie. Dla każdego dysku miejsce do magazynowania jest wyświetlane w czterech sekcjach:

* Dane SQL
* Dziennik SQL
* Inne (magazyn inny niż SQL)
* Dostępne

Aby zmodyfikować ustawienia magazynu, wybierz pozycję **Konfiguruj** w obszarze **Ustawienia**. 

![Konfigurowanie magazynu dla istniejącej maszyny Wirtualnej programu SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Można zmodyfikować ustawienia dysku dla dysków, które zostały skonfigurowane podczas procesu tworzenia maszyny Wirtualnej programu SQL Server. Wybranie **opcji Rozszerz dysk** otwiera stronę modyfikacji dysku, umożliwiając zmianę typu dysku oraz dodanie dodatkowych dysków. 

![Konfigurowanie magazynu dla istniejącej maszyny Wirtualnej programu SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Ta sekcja zawiera odwołanie do zmian konfiguracji magazynu, które platforma Azure automatycznie wykonuje podczas inicjowania obsługi administracyjnej lub konfiguracji maszyny Wirtualnej SQL w witrynie Azure portal.

* Platforma Azure konfiguruje pulę magazynu z magazynu wybranego z maszyny Wirtualnej. Następna sekcja tego tematu zawiera szczegółowe informacje na temat konfiguracji puli magazynu.
* Automatyczna konfiguracja pamięci masowej zawsze używa dysków danych [SSD](../disks-types.md) P30 premium. W związku z tym istnieje mapowanie 1:1 między wybraną liczbą terabajtów a liczbą dysków danych dołączonych do maszyny Wirtualnej.

Aby uzyskać informacje o cenach, zobacz stronę [Cennik magazynu](https://azure.microsoft.com/pricing/details/storage) na karcie **Magazyn dysku.**

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynów

Platforma Azure używa następujących ustawień do utworzenia puli magazynu na maszynach wirtualnych programu SQL Server.

| Ustawienie | Wartość |
| --- | --- |
| Rozmiar paska |256 KB (Magazynowanie danych); 64 KB (transakcyjne) |
| Rozmiary dysków |1 TB każda |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |64 KB rozmiar jednostki alokacji NTFS |
| Odzyskiwanie | Proste odzyskiwanie (brak odporności) |
| Liczba kolumn |Liczba dysków z danymi do 8<sup>1</sup> |


<sup>1</sup> Po utworzeniu puli magazynu nie można zmienić liczby kolumn w puli magazynu.


## <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia

W poniższej tabeli opisano trzy dostępne opcje typu obciążenia i odpowiadające im optymalizacje:

| Typ obciążenia | Opis | Optymalizacje |
| --- | --- | --- |
| **Ogólne** |Domyślne ustawienie obsługujące większość obciążeń |Brak |
| **Przetwarzanie transakcyjne** |Optymalizuje pamięć masową dla tradycyjnych obciążeń OLTP bazy danych |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowanie danych** |Optymalizuje pamięć masową pod kątem obciążeń analitycznych i raportowania |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko podczas inicjowania obsługi administracyjnej maszyny wirtualnej SQL, wybierając go w kroku konfiguracji magazynu.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z innymi tematami związanymi z uruchamianiem programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)
