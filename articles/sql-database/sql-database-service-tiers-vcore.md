---
title: Omówienie modelu rdzeni wirtualnych
description: Model zakupów rdzeń wirtualny umożliwia niezależne skalowanie zasobów obliczeniowych i magazynu, dopasowanie wydajności lokalnej i optymalizację cen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481086"
---
# <a name="vcore-model-overview"></a>Omówienie modelu rdzeni wirtualnych

Model Virtual Core (rdzeń wirtualny) zapewnia kilka korzyści:

- Wyższe limity obliczeń, pamięci, operacji we/wy i magazynu.
- Kontrola nad generowaniem sprzętu w celu lepszego dopasowania do wymagań obliczeniowych i pamięci dla obciążenia.
- Rabaty cenowe dla [korzyść użycia hybrydowego platformy Azure (AHB)](sql-database-azure-hybrid-benefit.md) i [wystąpienia zarezerwowanego (ri)](sql-database-reserved-capacity.md).
- Większa przejrzystość szczegółów sprzętowych, które zwiększają moc obliczeniową; ułatwia planowanie migracji z wdrożeń lokalnych.

## <a name="service-tiers"></a>Warstwy usług

Opcje warstwy usług w modelu rdzeń wirtualny obejmują Ogólnego przeznaczenia, Krytyczne dla działania firmy i skalowanie. Warstwa usługi zwykle definiuje architekturę magazynu, limity przestrzeni i operacji we/wy oraz opcje ciągłości działania związane z dostępnością i odzyskiwaniem po awarii.

||**Zastosowania ogólne**|**Krytyczne dla działania firmy**|**Hiperskala**|
|---|---|---|---|
|Najlepsze dla|Większość obciążeń firmowych. Oferuje zorientowane na budżety, zrównoważone i skalowalne Opcje obliczeniowe i magazynowe. |Oferuje aplikacjom biznesowym największą odporność na błędy przy użyciu kilku izolowanych replik i zapewnia największą wydajność operacji we/wy na replikę bazy danych.|Większość obciążeń firmowych z wysoce skalowalnym magazynem i wymaganiami dotyczącymi skali odczytu.  Zapewnia wyższą odporność na błędy, umożliwiając konfigurację wielu izolowanych replik baz danych. |
|Magazyn|Używa magazynu zdalnego.<br/>**Pojedyncze bazy danych i alokowane pule elastyczne**:<br/>5 GB – 4 TB<br/>**Obliczenia bezserwerowe**:<br/>5 GB — 3 TB<br/>**Wystąpienie zarządzane**: 32 GB – 8 TB |Używa lokalnego magazynu SSD.<br/>**Pojedyncze bazy danych i alokowane pule elastyczne**:<br/>5 GB – 4 TB<br/>**Wystąpienie zarządzane**:<br/>32 GB — 4 TB |Elastyczna automatyczne zwiększanie magazynu zgodnie z wymaganiami. Obsługuje do 100 TB pamięci masowej. Używa lokalnego magazynu SSD dla lokalnej pamięci podręcznej puli buforów i lokalnego magazynu danych. Używa magazynu zdalnego platformy Azure jako końcowego długoterminowego magazynu danych. |
|Operacje we/wy i przepływność (przybliżona)|**Pojedyncze bazy danych i pule elastyczne**: Zobacz limity zasobów dla [pojedynczych baz danych](../sql-database/sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Wystąpienie zarządzane**: zobacz [Przegląd Azure SQL Database limity zasobów wystąpienia zarządzanego](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Zobacz limity zasobów dla [pojedynczych baz danych](../sql-database/sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Skalowanie jest architekturą wielowarstwową z buforowaniem na wielu poziomach. Efektywne operacje we/wy i przepływność będą zależeć od obciążenia.|
|Dostępność|1 replika, brak replik w skali odczytu|3 repliki, 1 [replika w skali odczytu](sql-database-read-scale-out.md),<br/>Strefa — nadmiarowa wysoka dostępność (HA)|1 replika odczytu i zapisu oraz 0-4 [replik w skali odczytu](sql-database-read-scale-out.md)|
|Tworzenie kopii zapasowych|[Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|Tworzenie kopii zapasowych opartych na migawce w magazynie zdalnym platformy Azure. Przywraca używanie tych migawek do szybkiego odzyskiwania. Kopie zapasowe są natychmiast i nie wpływają na wydajność obliczeń we/wy. Przywracanie odbywa się szybko i nie jest operacją o rozmiarze danych (w minutach, a nie w godzinach lub dniach).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||


### <a name="choosing-a-service-tier"></a>Wybieranie warstwy usługi

Aby uzyskać informacje na temat wybierania warstwy usług dla konkretnego obciążenia, zobacz następujące artykuły:

- [Kiedy należy wybrać warstwę usług ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać warstwę usługi Krytyczne dla działania firmy](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać warstwę usługi do skalowania](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Warstwy obliczeniowe

Opcje warstwy obliczeniowej w modelu rdzeń wirtualny obejmują warstwy obliczeniowe z obsługą administracyjną i bezserwerową.


### <a name="provisioned-compute"></a>Zainicjowane obliczenie

Wstępnie zainicjowana warstwa obliczeniowa zapewnia określoną ilość zasobów obliczeniowych, które są stale inicjowane niezależnie od aktywności obciążenia, oraz opłaty za ilość obliczeń przywidzianych przy stałej cenie za godzinę.


### <a name="serverless-compute"></a>Bezserwerowe usługi obliczeniowe

[Warstwa obliczeniowa bezserwerowa](sql-database-serverless.md) automatycznie skaluje zasoby obliczeniowe na podstawie aktywności obciążeń i rachunków dla ilości użytych obliczeń na sekundę.



## <a name="hardware-generations"></a>Generacja sprzętu

Opcje generowania sprzętu w modelu rdzeń wirtualny obejmują 4/5 generacji serii M (wersja zapoznawcza) i Fsv2 (wersja zapoznawcza). Generowanie sprzętu zwykle definiuje limity obliczeń i pamięci oraz inne właściwości, które wpływają na wydajność obciążenia.

### <a name="gen4gen5"></a>Obliczenia/5 rdzeń

- Sprzęt obliczenia/5 rdzeń zapewnia zrównoważone zasoby obliczeniowe i pamięci oraz jest odpowiedni dla większości obciążeń związanych z bazą danych, które nie mają wyższej ilości pamięci, wyższych rdzeń wirtualny lub szybszych wymagań rdzeń wirtualny, które są dostarczane przez serie Fsv2 lub serii M.

W przypadku regionów, w których jest dostępny obliczenia/5 rdzeń, zobacz [dostępność obliczenia/5 rdzeń](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2 — seria (wersja zapoznawcza)

- Fsv2-Series to zoptymalizowana pod kątem wydajności opcja sprzętowa zapewniająca niskie opóźnienie procesora CPU i dużą szybkość zegara w przypadku większości obciążeń wymagających procesora CPU.
- W zależności od obciążenia seria Fsv2 może zapewnić większą wydajność procesora CPU na rdzeń wirtualny niż 5 rdzeń, a rozmiar rdzeń wirtualny 72 może zapewnić większą wydajność procesora CPU mniejszą niż 80 rdzeni wirtualnych w 5 rdzeń. 
- Fsv2 zapewnia mniejszą ilość pamięci i tempdb na rdzeń wirtualny niż inny sprzęt, dlatego obciążenia te mogą chcieć uwzględnić 5 rdzeń lub M serii.  

Seria Fsv2 jest obsługiwana tylko w warstwie Ogólnego przeznaczenia.  W przypadku regionów, w których jest dostępna seria Fsv2, zobacz [dostępność z serii Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Seria M (wersja zapoznawcza)

- Seria M to zoptymalizowana pod kątem pamięci opcja sprzętowa dla obciążeń wymagających większej ilości pamięci i wyższych limitów obliczeń niż zapewniana przez 5 rdzeń.
- Seria M zapewnia 29 GB na rdzeń wirtualny i 128 rdzeni wirtualnych, co zwiększa limit pamięci względem 5 rdzeń przez 8x do niemal 4 TB.

Seria M jest obsługiwana tylko w warstwie Krytyczne dla działania firmy i nie obsługuje nadmiarowości strefy.

Aby włączyć sprzęt serii M dla subskrypcji i regionu, należy otworzyć żądanie obsługi. Subskrypcja musi być płatnym typem oferty, w tym płatności zgodnie z rzeczywistym użyciem lub Umowa Enterprise (EA).  Jeśli żądanie pomocy technicznej zostanie zatwierdzone, wybór i środowisko aprowizacji serii M są zgodne z tym samym wzorcem, co w przypadku innych generacji sprzętowych. W przypadku regionów, w których jest dostępna Seria M, zobacz [dostępność serii m](#m-series).


### <a name="compute-and-memory-specifications"></a>Specyfikacje obliczeniowe i pamięci


|Generowanie sprzętu  |Wystąpienia obliczeniowe  |Memory (Pamięć)  |
|:---------|:---------|:---------|
|Obliczenia     |-Procesory Intel E5-2673 v3 (Haswell) 2,4 GHz<br>— Zapewnij do 24 rdzeni wirtualnych (1 rdzeń wirtualny = 1 rdzeń fizyczny)  |-7 GB na rdzeń wirtualny<br>— Zapewnij do 168 GB|
|5 rdzeń     |**Zainicjowane obliczenie**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz i Intel SP-8160 (Skylake) * procesory<br>— Inicjowanie obsługi administracyjnej do 80 rdzeni wirtualnych (1 rdzeń wirtualny = 1 Hyper-Thread)<br><br>**Bezserwerowe usługi obliczeniowe**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz i Intel SP-8160 (Skylake) * procesory<br>-Automatyczne skalowanie do 16 rdzeni wirtualnych (1 rdzeń wirtualny = 1 Hyper-Thread)|**Zainicjowane obliczenie**<br>-5,1 GB na rdzeń wirtualny<br>— Zapewnij do 408 GB<br><br>**Bezserwerowe usługi obliczeniowe**<br>-Automatyczne skalowanie do 24 GB na rdzeń wirtualny<br>— Automatyczne skalowanie do maksymalnie 48 GB|
|Seria Fsv2     |— Procesory Intel Xeon Platinum 8168 (SkyLake)<br>— Dzięki stałej szybkości taktu Turbo o częstotliwości 3,4 GHz i maksymalnej pojedynczej podstawowej prędkości zegarka Turbo o godz. 3,7 GHz.<br>-Inicjowanie obsługi administracyjnej 72 rdzeni wirtualnych (1 rdzeń wirtualny = 1 Hyper-Thread)|-1,9 GB na rdzeń wirtualny<br>-Inicjowanie obsługi administracyjnej 136 GB|
|Seria M     |-Procesory Intel Xeon E7-8890 v3 2,5 GHz<br>-Inicjowanie obsługi administracyjnej 128 rdzeni wirtualnych (1 rdzeń wirtualny = 1 Hyper-Thread)|-29 GB na rdzeń wirtualny<br>— Inicjowanie obsługi administracyjnej 3,7 TB|

\*W widoku dynamicznego zarządzania [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) generowanie sprzętu dla baz danych 5 rdzeń przy użyciu procesorów Intel SP-8160 (Skylake) pojawia się jako Gen6. Limity zasobów dla wszystkich baz danych 5 rdzeń są takie same, niezależnie od typu procesora (Broadwell lub Skylake).

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [limity zasobów dla pojedynczych baz danych (rdzeń wirtualny)](sql-database-vcore-resource-limits-single-databases.md)lub [limity zasobów dla pul elastycznych (rdzeń wirtualny)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Wybieranie generowania sprzętu

W Azure Portal można wybrać generowanie sprzętu dla bazy danych SQL lub puli w momencie tworzenia lub można zmienić generowanie sprzętu istniejącej bazy danych lub puli SQL.

**Aby wybrać generowanie sprzętu podczas tworzenia bazy danych lub puli SQL**

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie bazy danych SQL](sql-database-single-database-get-started.md).

Na karcie **podstawowe** wybierz łącze **Konfiguruj bazę danych** w sekcji **obliczenia + magazyn** , a następnie wybierz łącze **Zmień konfigurację** :

  ![Konfigurowanie bazy danych](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Wybierz żądaną generację sprzętu:

  ![Wybierz sprzęt](media/sql-database-service-tiers-vcore/select-hardware.png)


**Aby zmienić generowanie sprzętu istniejącej bazy danych lub puli SQL**

W przypadku bazy danych na stronie Przegląd wybierz łącze **warstwa cenowa** :

  ![Zmień sprzęt](media/sql-database-service-tiers-vcore/change-hardware.png)

W przypadku puli na stronie Przegląd wybierz pozycję **Konfiguruj**.

Postępuj zgodnie z instrukcjami, aby zmienić konfigurację, i wybierz Generowanie sprzętu zgodnie z opisem w poprzednich krokach.

**Aby wybrać generowanie sprzętu podczas tworzenia wystąpienia zarządzanego**

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

Na karcie **podstawowe** wybierz łącze **Konfiguruj bazę danych** w sekcji **obliczenia + magazyn** , a następnie wybierz żądaną generację sprzętu:

  ![Skonfiguruj wystąpienie zarządzane](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Aby zmienić generowanie sprzętu istniejącego wystąpienia zarządzanego**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na stronie wystąpienie zarządzane wybierz pozycję **warstwa cenowa** link do sekcji Ustawienia.

![Zmień sprzęt wystąpienia zarządzanego](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Na stronie **warstwa cenowa** będzie można zmienić generowanie sprzętu zgodnie z opisem w poprzednich krokach.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Użyj poniższego skryptu programu PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Aby uzyskać więcej szczegółów, zobacz polecenie [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Aby uzyskać więcej szczegółów, sprawdź polecenie [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) .

---

### <a name="hardware-availability"></a>Dostępność sprzętu

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Obliczenia/5 rdzeń

Sprzęt obliczenia jest [stopniowo wycofywany](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe bazy danych należy wdrożyć na sprzęcie 5 rdzeń.

5 rdzeń jest dostępna w większości regionów na całym świecie.

#### <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest dostępna w następujących regionach: Australia Środkowa, Australia Środkowa 2, Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Azja Wschodnia, Wschodnie stany USA, Francja środkowa, Indie Środkowe, Indie Zachodnie, Korea środkowa, Korea Południowa, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Północna Republika Południowej Afryki Europa Północna


#### <a name="m-series"></a>Seria M

Seria M jest dostępna w następujących regionach: Wschodnie stany USA, Europa Północna, Europa Zachodnia, zachodnie stany USA 2.
Seria M może również mieć ograniczoną dostępność w dodatkowych regionach. Możesz zażądać innego regionu niż wymienione tutaj, ale realizacja w innym regionie może nie być możliwa.

Aby włączyć dostępność serii M w ramach subskrypcji, musisz uzyskać dostęp do żądania, podając [nowe żądanie pomocy technicznej](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Utwórz żądanie obsługi, aby włączyć serię M: 

1. Wybierz pozycję **Pomoc i obsługa techniczna** w portalu.
2. Wybierz pozycję **nowe żądanie obsługi**.

Na stronie **podstawowe** podaj następujące informacje:

1. W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)**.
2. W przypadku **subskrypcji** = wybierz subskrypcję, aby włączyć serie M.
3. W obszarze **Typ limitu przydziału**wybierz pozycję **baza danych SQL**.
4. Wybierz pozycję **dalej** , aby przejść do strony **szczegółów** .

Na stronie **szczegóły** podaj następujące informacje:

1. W sekcji **Szczegóły problemu** wybierz łącze **Podaj szczegóły** . 
2. W obszarze **typ przydziału SQL Database** wybierz pozycję **Seria M**.
3. W polu **region**wybierz region, w którym ma zostać włączona Seria M.
    W przypadku regionów, w których jest dostępna Seria M, zobacz [dostępność serii m](#m-series).

Zatwierdzone żądania pomocy technicznej są zwykle spełnione w ciągu 5 dni roboczych.


## <a name="next-steps"></a>Następne kroki

- Aby utworzyć bazę danych SQL, zobacz [Tworzenie bazy danych SQL przy użyciu Azure Portal](sql-database-single-database-get-started.md).
- Dla określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- W przypadku określonych rozmiarów obliczeń i opcji rozmiaru magazynu dla pul elastycznych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
- Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).
