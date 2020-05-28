---
title: Przegląd modelu zakupu rdzeń wirtualny
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Model zakupów rdzeń wirtualny umożliwia niezależne skalowanie zasobów obliczeniowych i magazynu, dopasowanie wydajności lokalnej oraz optymalizację cen dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 1a6546ad587fa308ab5559d04814191c503ecdc3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044094"
---
# <a name="vcore-model-overview---azure-sql-database--sql-managed-instance"></a>Przegląd modelu rdzeń wirtualny — Azure SQL Database & wystąpienie zarządzane SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Model zakupu wirtualnego rdzenia (rdzeń wirtualny) używany przez Azure SQL Database i wystąpienie zarządzane usługi Azure SQL zapewnia kilka korzyści:

- Wyższe limity obliczeń, pamięci, operacji we/wy i magazynu.
- Kontrola nad generowaniem sprzętu w celu lepszego dopasowania do wymagań obliczeniowych i pamięci dla obciążenia.
- Rabaty cenowe dla [korzyść użycia hybrydowego platformy Azure (AHB)](../azure-hybrid-benefit.md) i [wystąpienia zarezerwowanego (ri)](reserved-capacity-overview.md).
- Większa przejrzystość szczegółów sprzętowych, które zwiększają moc obliczeniową; ułatwia planowanie migracji z wdrożeń lokalnych.

## <a name="service-tiers"></a>Warstwy usług

Opcje warstwy usług w modelu rdzeń wirtualny obejmują Ogólnego przeznaczenia, Krytyczne dla działania firmy i skalowanie. Warstwa usługi zwykle definiuje architekturę magazynu, limity przestrzeni i operacji we/wy oraz opcje ciągłości działania związane z dostępnością i odzyskiwaniem po awarii.

||**Zastosowania ogólne**|**Krytyczne dla działania firmy**|**Hiperskala**|
|---|---|---|---|
|Najlepsze dla|Większość obciążeń firmowych. Oferuje zorientowane na budżety, zrównoważone i skalowalne Opcje obliczeniowe i magazynowe. |Oferuje aplikacjom biznesowym największą odporność na błędy przy użyciu kilku izolowanych replik i zapewnia największą wydajność operacji we/wy na replikę bazy danych.|Większość obciążeń firmowych z wysoce skalowalnym magazynem i wymaganiami dotyczącymi skali odczytu.  Zapewnia wyższą odporność na błędy, umożliwiając konfigurację wielu izolowanych replik baz danych. |
|Magazyn|Używa magazynu zdalnego.<br/>**SQL Database Obliczanie zainicjowane**:<br/>5 GB – 4 TB<br/>**Obliczenia bezserwerowe**:<br/>5 GB — 3 TB<br/>**Wystąpienie zarządzane SQL**: 32 GB – 8 TB |Używa lokalnego magazynu SSD.<br/>**SQL Database Obliczanie zainicjowane**:<br/>5 GB – 4 TB<br/>**Wystąpienie zarządzane SQL**:<br/>32 GB — 4 TB |Elastyczna automatyczne zwiększanie magazynu zgodnie z wymaganiami. Obsługuje do 100 TB pamięci masowej. Używa lokalnego magazynu SSD dla lokalnej pamięci podręcznej puli buforów i lokalnego magazynu danych. Używa magazynu zdalnego platformy Azure jako końcowego długoterminowego magazynu danych. |
|Operacje we/wy i przepływność (przybliżona)|**SQL Database**: Zobacz limity zasobów dla [pojedynczych baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych](resource-limits-vcore-elastic-pools.md).<br/>**Wystąpienie zarządzane SQL**: zobacz [Omówienie limitów zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md#service-tier-characteristics).|Zobacz limity zasobów dla [pojedynczych baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych](resource-limits-vcore-elastic-pools.md).|Skalowanie jest architekturą wielowarstwową z buforowaniem na wielu poziomach. Efektywne operacje we/wy i przepływność będą zależeć od obciążenia.|
|Dostępność|1 replika, brak replik w skali odczytu|3 repliki, 1 [replika w skali odczytu](read-scale-out.md),<br/>Strefa — nadmiarowa wysoka dostępność (HA)|1 replika odczytu i zapisu oraz 0-4 [replik w skali odczytu](read-scale-out.md)|
|Tworzenie kopii zapasowych|[Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 dni (domyślnie 7 dni)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35 dni (domyślnie 7 dni)|Tworzenie kopii zapasowych opartych na migawce w magazynie zdalnym platformy Azure. Przywraca używanie tych migawek do szybkiego odzyskiwania. Kopie zapasowe są natychmiast i nie wpływają na wydajność obliczeń we/wy. Przywracanie odbywa się szybko i nie jest operacją o rozmiarze danych (w minutach, a nie w godzinach lub dniach).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||


### <a name="choosing-a-service-tier"></a>Wybieranie warstwy usługi

Aby uzyskać informacje na temat wybierania warstwy usług dla konkretnego obciążenia, zobacz następujące artykuły:

- [Kiedy należy wybrać warstwę usług ogólnego przeznaczenia](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać warstwę usługi Krytyczne dla działania firmy](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać warstwę usługi do skalowania](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Warstwy obliczeniowe

Opcje warstwy obliczeniowej w modelu rdzeń wirtualny obejmują warstwy obliczeniowe z obsługą administracyjną i bezserwerową.


### <a name="provisioned-compute"></a>Zainicjowane obliczenie

Wstępnie zainicjowana warstwa obliczeniowa zapewnia określoną ilość zasobów obliczeniowych, które są stale inicjowane niezależnie od aktywności obciążenia, oraz opłaty za ilość obliczeń przywidzianych przy stałej cenie za godzinę.


### <a name="serverless-compute"></a>Bezserwerowe usługi obliczeniowe

[Warstwa obliczeniowa bezserwerowa](serverless-tier-overview.md) automatycznie skaluje zasoby obliczeniowe na podstawie aktywności obciążeń i rachunków dla ilości użytych obliczeń na sekundę.



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
|Seria M     |— Procesory Intel Xeon E7-8890 v3 2,5 GHz i Intel Xeon Platinum 8280M 2,7 GHz (Kaskada Lake)<br>-Inicjowanie obsługi administracyjnej 128 rdzeni wirtualnych (1 rdzeń wirtualny = 1 Hyper-Thread)|-29 GB na rdzeń wirtualny<br>— Inicjowanie obsługi administracyjnej 3,7 TB|

\*W widoku dynamicznego zarządzania [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) generowanie sprzętu dla baz danych 5 rdzeń przy użyciu procesorów Intel SP-8160 (Skylake) pojawia się jako Gen6. Limity zasobów dla wszystkich baz danych 5 rdzeń są takie same, niezależnie od typu procesora (Broadwell lub Skylake).

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [limity zasobów dla pojedynczych baz danych (rdzeń wirtualny)](resource-limits-vcore-single-databases.md)lub [limity zasobów dla pul elastycznych (rdzeń wirtualny)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Wybieranie generowania sprzętu

W Azure Portal można wybrać generowanie sprzętu dla SQL Database lub puli w momencie tworzenia lub można zmienić generowanie sprzętu istniejącej bazy danych lub puli SQL.

**Aby wybrać generowanie sprzętu podczas tworzenia SQL Database lub puli**

Aby uzyskać szczegółowe informacje, zobacz [tworzenie SQL Database](single-database-create-quickstart.md).

Na karcie **podstawowe** wybierz łącze **Konfiguruj bazę danych** w sekcji **obliczenia + magazyn** , a następnie wybierz łącze **Zmień konfigurację** :

  ![Konfigurowanie bazy danych](./media/service-tiers-vcore/configure-sql-database.png)

Wybierz żądaną generację sprzętu:

  ![Wybierz sprzęt](./media/service-tiers-vcore/select-hardware.png)


**Aby zmienić generowanie sprzętu istniejącej SQL Database lub puli**

W przypadku bazy danych na stronie Przegląd wybierz łącze **warstwa cenowa** :

  ![Zmień sprzęt](./media/service-tiers-vcore/change-hardware.png)

W przypadku puli na stronie Przegląd wybierz pozycję **Konfiguruj**.

Postępuj zgodnie z instrukcjami, aby zmienić konfigurację, i wybierz Generowanie sprzętu zgodnie z opisem w poprzednich krokach.

**Aby wybrać generowanie sprzętu podczas tworzenia wystąpienia zarządzanego SQL**

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie wystąpienia zarządzanego SQL](../managed-instance/instance-create-quickstart.md).

Na karcie **podstawowe** wybierz łącze **Konfiguruj bazę danych** w sekcji **obliczenia + magazyn** , a następnie wybierz żądaną generację sprzętu:

  ![Skonfiguruj wystąpienie zarządzane SQL](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Aby zmienić generowanie sprzętu istniejącego wystąpienia zarządzanego SQL**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na stronie wystąpienie zarządzane SQL Wybierz pozycję **warstwa cenowa** link do sekcji Ustawienia.

![Zmień sprzęt wystąpienia zarządzanego SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na stronie **warstwa cenowa** będzie można zmienić generowanie sprzętu zgodnie z opisem w poprzednich krokach.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

Obliczenia sprzęt jest [wycofywany i nie](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) jest już dostępny dla nowych wdrożeń. Wszystkie nowe bazy danych należy wdrożyć na sprzęcie 5 rdzeń.

5 rdzeń jest dostępna w większości regionów na całym świecie.

#### <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest dostępna w następujących regionach: Australia Środkowa, Australia Środkowa 2, Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Azja Wschodnia, Wschodnie stany USA, Francja środkowa, Indie Środkowe, Indie Zachodnie, Korea środkowa, Korea Południowa, Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Północna Republika Południowej Afryki Europa Północna


#### <a name="m-series"></a>Seria M

Seria M jest dostępna w następujących regionach: Wschodnie stany USA, Europa Północna, Europa Zachodnia, zachodnie stany USA 2.
Seria M może również mieć ograniczoną dostępność w dodatkowych regionach. Możesz zażądać innego regionu niż wymienione tutaj, ale realizacja w innym regionie może nie być możliwa.

Aby włączyć dostępność serii M w ramach subskrypcji, musisz uzyskać dostęp do żądania, podając [nowe żądanie pomocy technicznej](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Utwórz żądanie obsługi, aby włączyć serię M: 

1. Wybierz pozycję **Pomoc i obsługa techniczna** w portalu.
2. Wybierz pozycję **Nowe żądanie obsługi**.

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

Aby rozpocząć, zobacz: 
- [Tworzenie SQL Database przy użyciu Azure Portal](single-database-create-quickstart.md)
- [Tworzenie wystąpienia zarządzanego SQL przy użyciu Azure Portal](../managed-instance/instance-create-quickstart.md)

Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i magazynowych dostępnych w warstwach usług ogólnych i krytycznych dla działania firmy, zobacz: 

- [limity zasobów opartych na rdzeń wirtualny Azure SQL Database](resource-limits-vcore-single-databases.md).
- [limity zasobów opartych na rdzeń wirtualny dla Azure SQL Database w puli](resource-limits-vcore-elastic-pools.md).
- [limity zasobów opartych na rdzeń wirtualny dla wystąpienia zarządzanego Azure SQL](../managed-instance/resource-limits.md). 

