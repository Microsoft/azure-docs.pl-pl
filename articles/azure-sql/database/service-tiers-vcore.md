---
title: Omówienie modelu zakupów rdzeni wirtualnych
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Model zakupów rdzeni wirtualnych umożliwia niezależne skalowanie zasobów obliczeniowych i magazynowych, dopasowanie wydajności lokalnej oraz optymalizację cen dla zasobów Azure SQL Database i Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779187"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Omówienie modelu rdzeni wirtualnych — Azure SQL Database i Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Model zakupów rdzeni wirtualnych (vCore) używany przez Azure SQL Database i Azure SQL Managed Instance zapewnia kilka korzyści:

- Wyższe limity mocy obliczeniowej, pamięci, operacji we/wy i magazynu.
- Kontroluj generację sprzętu, aby lepiej dopasować obciążenie do wymagań dotyczących mocy obliczeniowej i pamięci.
- Rabaty cenowe [dla Korzyść użycia hybrydowego platformy Azure (AHB)](../azure-hybrid-benefit.md) i wystąpienia [zarezerwowanego (RI).](reserved-capacity-overview.md)
- Większa przejrzystość szczegółów sprzętu, które są zasilane z mocy obliczeniowej, co ułatwia planowanie migracji z wdrożeń lokalnych.

## <a name="service-tiers"></a>Warstwy usług

Opcje warstw usług w modelu z rdzeniami Ogólnego przeznaczenia, Krytyczne dla działania firmy i Hiperskala. Warstwa usługi ogólnie definiuje architekturę magazynu, limity miejsca i we/wy oraz opcje ciągłości działania związane z dostępnością i odzyskiwaniem po awarii.

|-|**Ogólnego przeznaczenia**|**Krytyczne dla działania firmy**|**Hiperskala**|
|---|---|---|---|
|Optymalne zastosowanie|Większość obciążeń biznesowych. Oferuje zorientowane na budżet, zrównoważone i skalowalne opcje zasobów obliczeniowych i magazynowych. |Oferuje aplikacjom biznesowym najwyższą odporność na awarie dzięki użyciu kilku izolowanych replik i zapewnia najwyższą wydajność we/wy na replikę bazy danych.|Większość obciążeń biznesowych z wysoce skalowalnymi wymaganiami w zakresie magazynu i skali odczytu.  Oferuje wyższą odporność na awarie, umożliwiając konfigurację więcej niż jednej izolowanej repliki bazy danych. |
|Storage|Używa magazynu zdalnego.<br/>**SQL Database aprowizowanych zasobów obliczeniowych:**<br/>5 GB–4 TB<br/>**Obliczenia bez serwera:**<br/>5 GB–3 TB<br/>**SQL Managed Instance:** 32 GB–8 TB |Używa lokalnego magazynu SSD.<br/>**SQL Database aprowizowanych zasobów obliczeniowych:**<br/>5 GB–4 TB<br/>**SQL Managed Instance:**<br/>32 GB–4 TB |Elastyczne automatyczne podrastanie magazynu zgodnie z potrzebami. Obsługuje do 100 TB magazynu. Używa lokalnego magazynu SSD dla lokalnej pamięci podręcznej puli buforów i lokalnego magazynu danych. Używa magazynu zdalnego platformy Azure jako końcowego długoterminowego magazynu danych. |
|Liczba IOPS i przepływność (przybliżona)|**SQL Database:** Zobacz limity zasobów dla [pojedynczych baz danych i](resource-limits-vcore-single-databases.md) [pul elastycznych.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Managed Instance:** zobacz [Overview Azure SQL Managed Instance resource limits (Omówienie limitów zasobów).](../managed-instance/resource-limits.md#service-tier-characteristics)|Zobacz limity zasobów dla [pojedynczych baz danych](resource-limits-vcore-single-databases.md) i [pul elastycznych.](resource-limits-vcore-elastic-pools.md)|Hiperskala to architektura wielowarstwowa z buforowaniem na wielu poziomach. Efektywna IOPS i przepływność będą zależeć od obciążenia.|
|Dostępność|1 replika, brak replik w skali odczytu|3 repliki, 1 [replika w skali odczytu,](read-scale-out.md)<br/>strefowo nadmiarowa wysoka dostępność|1 replika do odczytu i zapisu oraz 0–4 [repliki w skali odczytu](read-scale-out.md)|
|Tworzenie kopii zapasowych|Magazyn geograficznie nadmiarowy z dostępem do odczytu [(RA-GRS),](../../storage/common/geo-redundant-design.md)1–35 dni (domyślnie 7 dni)|[RA-GRS,](../..//storage/common/geo-redundant-design.md)1–35 dni (domyślnie 7 dni)|Kopie zapasowe oparte na migawkach w zdalnym magazynie platformy Azure. Przywracanie używa tych migawek w celu szybkiego odzyskiwania. Kopie zapasowe są natychmiastowe i nie mają wpływu na wydajność operacji we/wy obliczeń. Przywracanie jest szybkie i nie jest operacją rozmiaru danych (trwa kilka minut, a nie godzin czy dni).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||


### <a name="choosing-a-service-tier"></a>Wybieranie warstwy usługi

Aby uzyskać informacje na temat wybierania warstwy usługi dla określonego obciążenia, zobacz następujące artykuły:

- [Kiedy należy wybrać Ogólnego przeznaczenia usługi](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać Krytyczne dla działania firmy usługi](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Kiedy należy wybrać warstwę usługi Hiperskala](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Warstwy obliczeniowe

Opcje warstw obliczeniowych w modelu rdzeni wirtualnych obejmują warstwy zasobów obliczeniowych aprowizowanych i bez serwera.


### <a name="provisioned-compute"></a>Aprowowane zasoby obliczeniowe

Aprowizowana warstwa obliczeniowa zapewnia określoną ilość zasobów obliczeniowych, które są stale aprowowane niezależnie od aktywności obciążeń, oraz nalicza opłaty za ilość zasobów obliczeniowych aprowizowanych za stałą cenę za godzinę.


### <a name="serverless-compute"></a>Bezserwerowe usługi obliczeniowe

Warstwa [zasobów obliczeniowych bez serwera](serverless-tier-overview.md) automatycznie skaluje zasoby obliczeniowe na podstawie aktywności obciążenia i nalicza opłaty za ilość używanych zasobów obliczeniowych na sekundę.



## <a name="hardware-generations"></a>Generacje sprzętu

Opcje generowania sprzętu w modelu z rdzeniami wirtualnych obejmują generację Gen 4/5, serię M, serię Fsv2 i serię DC. Generacja sprzętu zazwyczaj definiuje limity mocy obliczeniowej i pamięci oraz inne cechy, które mają wpływ na wydajność obciążenia.

### <a name="gen4gen5"></a>Gen4/Gen5

- Sprzęt Gen4/Gen5 zapewnia zrównoważone zasoby obliczeniowe i pamięci i jest odpowiedni dla większości obciążeń baz danych, które nie mają większej ilości pamięci, wyższych rdzeni wirtualnych ani szybszych wymagań dotyczących pojedynczych rdzeni wirtualnych zgodnie z wymaganiami z serii Fsv2 lub M.

W przypadku regionów, w których jest dostępna gen4/Gen5, zobacz [Dostępność gen4/Gen5.](#gen4gen5-1)

### <a name="fsv2-series"></a>Seria Fsv2

- Fsv2 to zoptymalizowana pod kątem obliczeń opcja sprzętowa zapewniająca małe opóźnienie procesora CPU i wysoką szybkość zegara dla najbardziej wymagających obciążeń procesora CPU.
- W zależności od obciążenia seria Fsv2 może zapewnić większą wydajność procesora CPU na rdzeń wirtualnych niż w przypadku 5. generacji, a rozmiar 72 rdzeni wirtualnych może zapewnić większą wydajność procesora w przypadku mniejszej wydajności niż 80 rdzeni wirtualnych w przypadku usługi Gen5. 
- Fsv2 zapewnia mniej pamięci i bazy danych tempdb na rdzeń wirtualnych niż inne urządzenia, więc obciążenia wrażliwe na te limity mogą chcieć rozważyć użycie serii Gen5 lub M.  

Seria Fsv2 w programie jest obsługiwana tylko w Ogólnego przeznaczenia warstwie. W przypadku regionów, w których dostępna jest seria Fsv2, zobacz Dostępność serii [Fsv2.](#fsv2-series-1)

### <a name="m-series"></a>Seria M

- Seria M to zoptymalizowana pod kątem pamięci opcja sprzętowa dla obciążeń wymagających większej ilości pamięci i wyższych limitów obliczeniowych niż zapewniana przez usługę Gen5.
- Seria M zapewnia 29 GB na rdzeń wirtualnych i do 128 rdzeni wirtualnych, co zwiększa limit pamięci względem gen5 o 8x do niemal 4 TB.

Seria M jest obsługiwana tylko w Krytyczne dla działania firmy i nie obsługuje nadmiarowości stref.  W przypadku regionów, w których seria M jest dostępna, zobacz [Dostępność serii M.](#m-series-1)

#### <a name="azure-offer-types-supported-by-m-series"></a>Typy ofert platformy Azure obsługiwane przez serię M

Aby uzyskać dostęp do serii M, subskrypcja musi być ofertą płatną, w tym z płatnością zgodnie z Enterprise Agreement (EA).  Aby uzyskać pełną listę typów ofert platformy Azure obsługiwanych przez serię M, zobacz bieżące [oferty bez limitów wydatków.](https://azure.microsoft.com/support/legal/offer-details)

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Seria DC

> [!NOTE]
> Seria DC jest obecnie dostępna w publicznej **wersji zapoznawczej**.

- Sprzęt serii DC korzysta z procesorów Firmy Intel Software Guard Extensions (Intel SGX).
- Seria DC jest wymagana w [przypadku Always Encrypted z bezpiecznymi enklawami,](/sql/relational-databases/security/encryption/always-encrypted-enclaves)które nie są obsługiwane w przypadku innych konfiguracji sprzętu.
- Seria DC jest przeznaczona dla obciążeń, które przetwarzają poufne dane i wymagają poufnych możliwości przetwarzania zapytań, dostarczanych przez Always Encrypted z bezpiecznymi enklawami.
- Sprzęt serii DC zapewnia zrównoważone zasoby obliczeniowe i pamięci.

Seria DC jest obsługiwana tylko dla aprowowanych zasobów obliczeniowych (bez serwera nie jest obsługiwana) i nie obsługuje nadmiarowości stref. W przypadku regionów, w których dostępna jest seria [DC, zobacz DC-series availability (Dostępność serii DC).](#dc-series-1)

#### <a name="azure-offer-types-supported-by-dc-series"></a>Typy ofert platformy Azure obsługiwane przez serię DC

Aby uzyskać dostęp do serii DC, subskrypcja musi być ofertą płatną, w tym z płatnością zgodnie z Enterprise Agreement (EA).  Aby uzyskać pełną listę typów ofert platformy Azure obsługiwanych przez serię DC, zobacz [bieżące oferty bez limitów wydatków.](https://azure.microsoft.com/support/legal/offer-details)

### <a name="compute-and-memory-specifications"></a>Specyfikacje obliczeń i pamięci


|Generacja sprzętu  |Compute  |Pamięć  |
|:---------|:---------|:---------|
|4. generacja     |- Procesory Intel® E5-2673 v3 (Haswell) 2,4 GHz<br>— Aprowizuj do 24 rdzeni wirtualnych (1 rdzeń fizyczny = 1 rdzeń fizyczny)  |— 7 GB na rdzeń wirtualnych<br>— Aprowizować do 168 GB|
|5. generacja     |**Aprowowane zasoby obliczeniowe**<br>— Procesory Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) i \* Intel® 8272CL (Cascade Lake) 2,5 GHz \*<br>- Aprowizować do 80 rdzeni wirtualnych (1 rdzeń vCore = 1 hiperwątkowy)<br><br>**Bezserwerowe usługi obliczeniowe**<br>— procesory Intel® E5-2673 v4 (Broadwell) 2,3 GHz i Intel® SP-8160 (Skylake)*<br>— Automatyczne skalowanie w górę do 40 rdzeni wirtualnych (1 rdzeń wirtualnych = 1 hiperwątkowy)|**Aprowowane zasoby obliczeniowe**<br>— 5,1 GB na rdzeń wirtualnych<br>— Aprowizować do 408 GB<br><br>**Bezserwerowe usługi obliczeniowe**<br>— Automatyczne skalowanie w górę do 24 GB na rdzeń wirtualnych<br>— Automatyczne skalowanie w górę do maksymalnej pojemności 120 GB|
|Seria Fsv2     |- Procesory Intel® 8168 (Skylake)<br>- Cechowanie trwałą całą szybkość zegara core turbo 3,4 GHz i maksymalna szybkość zegara z jednym rdzeniem turbo 3,7 GHz.<br>- Aprowizować do 72 rdzeni wirtualnych (1 rdzeń wirtualnych = 1 hiperwątkowy)|— 1,9 GB na rdzeń wirtualnych<br>— Aprowizować do 136 GB|
|Seria M     |— Procesory Intel® E7-8890 v3 2,5 GHz i Intel® 8280M 2,7 GHz (Cascade Lake)<br>— Aprowizuj do 128 rdzeni wirtualnych (1 rdzeń wirtualnych = 1 hiperwątkowy)|— 29 GB na rdzeń wirtualnych<br>— Aprowizuj do 3,7 TB|
|Seria DC     | - Procesory Intel XEON E-2288G<br>- Cechowanie rozszerzenia Intel Software Guard (Intel SGX))<br>— Aprowizuj do 8 rdzeni wirtualnych (1 rdzeń fizyczny = 1 rdzeń fizyczny) | 4,5 GB na rdzeń wirtualnych |

\* W widoku [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamicznego zarządzania generacja sprzętu dla baz danych korzystających z procesorów Intel® SP-8160 (Skylake) jest wyświetlana jako Gen6, natomiast generacja sprzętu dla baz danych korzystających z technologii Intel® 8272CL (Cascade Lake) jest wyświetlana jako Gen7. Limity zasobów dla wszystkich baz danych 5. generacji są takie same niezależnie od typu procesora (Broadwell, Skylake lub Cascade Lake).

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz Limity zasobów dla pojedynczych baz danych [(rdzeni wirtualnych)](resource-limits-vcore-single-databases.md)lub Limity zasobów dla [pul elastycznych (rdzeni wirtualnych).](resource-limits-vcore-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Wybieranie generacji sprzętu

W Azure Portal można wybrać generację sprzętu dla bazy danych lub puli w programie SQL Database w czasie tworzenia lub zmienić generację sprzętu istniejącej bazy danych lub puli.

**Aby wybrać generację sprzętu podczas tworzenia SQL Database lub puli**

Aby uzyskać szczegółowe informacje, [zobacz Tworzenie SQL Database](single-database-create-quickstart.md).

Na karcie **Podstawowe wybierz**  link Konfiguruj bazę danych w sekcji **Obliczenia i magazyn,** a następnie wybierz link **Zmień** konfigurację:

  ![Konfigurowanie bazy danych](./media/service-tiers-vcore/configure-sql-database.png)

Wybierz odpowiednią generację sprzętu:

  ![wybieranie sprzętu](./media/service-tiers-vcore/select-hardware.png)


**Aby zmienić generację sprzętu istniejącej SQL Database puli**

W przypadku bazy danych na stronie Przegląd wybierz link **Warstwa cenowa:**

  ![zmiana sprzętu](./media/service-tiers-vcore/change-hardware.png)

W przypadku puli na stronie Przegląd wybierz pozycję **Konfiguruj.**

Wykonaj kroki zmiany konfiguracji i wybierz generację sprzętu zgodnie z opisem w poprzednich krokach.

**Aby wybrać generację sprzętu podczas tworzenia SQL Managed Instance**

Aby uzyskać szczegółowe informacje, [zobacz Tworzenie SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

Na karcie **Podstawowe wybierz**  link Konfiguruj bazę danych w sekcji **Obliczenia i** magazyn, a następnie wybierz odpowiednią generację sprzętu:

  ![konfigurowanie SQL Managed Instance](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Aby zmienić generację sprzętu istniejącego SQL Managed Instance**

# <a name="the-azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

Na stronie SQL Managed Instance wybierz link **Warstwa cenowa** umieszczony w sekcji Ustawienia

![zmiana SQL Managed Instance sprzętowego](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na stronie Warstwa cenowa będzie można zmienić generację sprzętu zgodnie z opisem w poprzednich krokach.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj poniższego skryptu programu PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Aby uzyskać więcej informacji, zobacz polecenie [Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance)

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia interfejsu wiersza polecenia:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Aby uzyskać więcej informacji, zobacz [polecenie az sql mi update.](/cli/azure/sql/mi#az_sql_mi_update)

---

### <a name="hardware-availability"></a>Dostępność sprzętu

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Sprzęt 4. [generacji jest wywłaszowany](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe bazy danych muszą zostać wdrożone na sprzęcie Gen5.

Generacja 5 jest dostępna we wszystkich regionach publicznych na całym świecie.

#### <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest dostępna w następujących regionach: Australia Środkowa, Australia Środkowa 2, Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Azja Wschodnia Wschodnie stany USA, Francja Środkowa, Indie Środkowe, Korea Środkowa, Korea Południowa, Europa Północna, Północna Republika Południowej Afryki, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Europa Zachodnia, Zachodnie stany USA 2.


#### <a name="m-series"></a>Seria M

Seria M jest dostępna w następujących regionach: Wschodnie stany USA, Europa Północna, Europa Zachodnia, Zachodnie stany USA 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Seria DC

> [!NOTE]
> Seria DC jest obecnie dostępna w publicznej **wersji zapoznawczej**.

Seria DC jest dostępna w następujących regionach: Kanada Środkowa, Kanada Wschodnia, Wschodnie stany USA, Europa Północna, Południowe Zjednoczone Królestwo, Europa Zachodnia, Zachodnie stany USA.

Jeśli potrzebujesz serii DC w obecnie nieobsługiwanym [regionie,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) prześlij bilet pomocy technicznej, zgodnie z instrukcjami w te tematu Request quota increases for Azure SQL Database and SQL Managed Instance (Żądanie zwiększenia limitu przydziału dla Azure SQL Database [i SQL Managed Instance](quota-increase-request.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę, zobacz: 
- [Tworzenie SQL Database przy użyciu Azure Portal](single-database-create-quickstart.md)
- [Tworzenie SQL Managed Instance przy użyciu Azure Portal](../managed-instance/instance-create-quickstart.md)

Aby uzyskać szczegółowe informacje o cenach, zobacz [Azure SQL Database cennika.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Aby uzyskać szczegółowe informacje na temat określonych rozmiarów zasobów obliczeniowych i magazynowych dostępnych w warstwach usług ogólnego przeznaczenia i krytycznych dla działania firmy, zobacz:

- [Limity zasobów oparte na rdzeniu Azure SQL Database](resource-limits-vcore-single-databases.md).
- [Limity zasobów oparte na rdzeniu rdzeni wirtualnych dla puli Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [Limity zasobów oparte na rdzeniu Azure SQL Managed Instance](../managed-instance/resource-limits.md).
