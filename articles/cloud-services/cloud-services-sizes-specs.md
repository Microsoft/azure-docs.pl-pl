---
title: Rozmiary maszyn wirtualnych dla usług Azure Cloud Services (klasyczny) | Microsoft Docs
description: Wyświetla różne rozmiary maszyn wirtualnych (i identyfikatory) dla ról Sieć Web i proces roboczy usługi w chmurze platformy Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a31c9c4747ccc72f0552ebe2f8daf85ef5fba8a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015305"
---
# <a name="sizes-for-cloud-services-classic"></a>Rozmiary dla Cloud Services (klasyczne)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym temacie opisano dostępne rozmiary i opcje dla wystąpień ról usługi w chmurze (ról sieci Web i procesów roboczych). Zawiera również zagadnienia dotyczące wdrażania, które należy wziąć pod uwagę podczas planowania użycia tych zasobów. Każdy rozmiar ma identyfikator, który został umieszczony w [pliku definicji usługi](cloud-services-model-and-package.md#csdef). Ceny za każdy rozmiar są dostępne na stronie [cennika Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) .

> [!NOTE]
> Aby sprawdzić powiązane limity platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Rozmiary dla wystąpień roli sieć Web i proces roboczy
Na platformie Azure do wyboru jest wiele standardowych rozmiarów maszyn wirtualnych. Uwagi dotyczące niektórych z tych rozmiarów:

* Maszyny wirtualne serii D są zaprojektowane do uruchamiania aplikacji wymagających większej mocy obliczeniowej i wydajności dysków tymczasowych. Maszyny wirtualne serii D zapewniają szybsze procesory, większą ilość pamięci na rdzeń i dyski półprzewodnikowe (SSD) dla dysków tymczasowych. Szczegółowe informacje zawiera ogłoszenie [New D-Series Virtual Machine Sizes](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Nowe rozmiary maszyn wirtualnych serii D) w blogu platformy Azure.
* Seria Dv3, Seria Dv2, kolejne z serii D, oferuje bardziej wydajny procesor CPU. Procesor CPU serii Dv2 jest o około 35% szybszy niż procesor CPU serii D. Seria Dv2 jest oparta na procesorze najnowszej generacji Intel Xeon® E5-2673 v3 (Haswell) z zegarem 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć częstotliwość 3,1 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.
* Maszyny wirtualne z serii G oferują największą ilość pamięci i są uruchamiane na hostach z procesorami z rodziny Intel Xeon E5 V3.
* Maszyny wirtualne z serii A można wdrażać na różnych typach sprzętu i procesorach. Rozmiar jest ograniczany na podstawie sprzętu, aby zaoferować spójną wydajność procesora dla uruchomionego wystąpienia, niezależnie od sprzętu, na którym jest wdrożony. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej.
* Rozmiar A0 jest nadmiernie subskrybowany na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

Rozmiar maszyny wirtualnej ma wpływ na ceny. Rozmiar wpływa również na wydajność przetwarzania oraz pojemność pamięci i magazynu maszyny wirtualnej. Koszty magazynowania są obliczane osobno na podstawie wykorzystanych stron na koncie magazynu. Aby uzyskać szczegółowe informacje, zobacz [Cloud Services szczegóły cennika](https://azure.microsoft.com/pricing/details/cloud-services/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

W podjęciu decyzji o rozmiarze mogą pomóc następujące informacje:

* Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz. Aby uzyskać szczegółowe informacje i zagadnienia dotyczące korzystania z tych rozmiarów, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Seria Dv3, Seria Dv2, Seria D serii G, są idealne dla aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych lub większych potrzeb związanych z pamięcią. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.
* Niektóre hosty fizyczne w centrach danych platformy Azure mogą nie obsługiwać większych rozmiarów maszyn wirtualnych, takich jak A5–A11. W efekcie może zostać wyświetlony komunikat o błędzie **nie można skonfigurować maszyny wirtualnej {Name}** lub **nie można utworzyć maszyny wirtualnej {Machine Name}** podczas zmiany rozmiaru istniejącej maszyny wirtualnej na nowy rozmiar; Tworzenie nowej maszyny wirtualnej w sieci wirtualnej utworzonej przed 16 kwietnia 2013; lub dodanie nowej maszyny wirtualnej do istniejącej usługi w chmurze. Zobacz [błąd: "nie można skonfigurować maszyny wirtualnej"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) na forum pomocy technicznej w celu obejścia tego problemu w każdym scenariuszu wdrażania.
* Subskrypcja może również ograniczać liczbę rdzeni, które można wdrożyć w rodzinach o określonym rozmiarze. Aby zwiększyć limit przydziału, skontaktuj się z pomocą techniczną platformy Azure.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Utworzyliśmy koncepcję jednostki obliczeniowej platformy Azure (ACU), aby zapewnić porównanie wydajności obliczeniowej (CPU) w jednostkach SKU platformy Azure i ustalić, która jednostka SKU najprawdopodobniej spełnia Twoje wymagania dotyczące wydajności.  Jednostka ACU jest obecnie standaryzowana na małej maszynie wirtualnej (Standardowa_A1) jako równa 100, a wszystkie pozostałe jednostki SKU reprezentują w przybliżeniu, o ile szybciej dana jednostka SKU może uruchomić standardowy test porównawczy.

> [!IMPORTANT]
> Wartość ACU jest tylko wskazówką. Wyniki dla konkretnego obciążenia mogą się różnić.
>
>

<br>

| Rodzina SKU | ACU/rdzeń |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Małe ExtraLarge](#a-series) |100 |
| [A5 — 7](#a-series) |100 |
| [A8–A11](#a-series) |225* |
| [A wersja 2](#av2-series) |100 |
| [Wykres](#d-series) |160 |
| [D v2](#dv2-series) |210 - 250* |
| [D v3](#dv3-series) |160 – 190 * |
| [E v3](#ev3-series) |160 – 190 * |
| [G](#g-series) |180 - 240* |
| [C](#h-series) |290 - 300* |

Jednostki ACU oznaczone gwiazdką (*) wykorzystują technologię Intel® Turbo w celu zwiększenia częstotliwości zegara procesora CPU i zapewniania większej wydajności. Skala zwiększenia wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

## <a name="size-tables"></a>Tabele rozmiarów
W poniższych tabelach przedstawiono rozmiary maszyn wirtualnych i możliwości, jakie oferują.

* Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Podczas porównywania dysków mierzonych w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3 bajtów) należy pamiętać, że pojemność podawana w GiB może wydawać się mniejsza. Na przykład 1023 GiB = 1098,4 GB.
* Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
* Dyski danych mogą działać w trybie buforowanym lub niebuforowanym. Dla pracy dysku danych w trybie buforowanym tryb pamięci podręcznej hosta jest ustawiony na wartość **ReadOnly** lub **ReadWrite**. Dla pracy dysku danych bez buforowania tryb pamięci podręcznej hosta jest ustawiony na wartość **None**.
* Maksymalna przepustowość sieci jest maksymalną zagregowaną przepustowością przydzieloną i przypisaną dla typu maszyny wirtualnej. Maksymalna przepustowość stanowi wskazówkę umożliwiającą wybranie odpowiedniego typu maszyny wirtualnej, który zapewni dostępność odpowiedniej pojemności sieci. W przypadku przechodzenia między niską, umiarkowaną i wysoką przepływność zostanie odpowiednio zwiększona. Rzeczywista wydajność sieci będzie zależeć od wielu czynników, takich jak obciążenia sieciowe i obciążenia aplikacji oraz ustawienia sieciowe aplikacji.

## <a name="a-series"></a>Seria A
| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy: GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20                   | 1 / niska |
| Mały           | 1         | 1,75         | 225                  | 1 / średnia |
| Śred.          | 2         | 3,5          | 490                  | 1 / średnia |
| Duży           | 4         | 7            | 1000                 | 2 / wysoka |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / wysoka |
| A5              | 2         | 14           | 490                  | 1 / średnia |
| A6              | 4         | 28           | 1000                 | 2 / wysoka |
| A7              | 8         | 56           | 2040                 | 4 / wysoka |

## <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej
Aby uzyskać informacje i zagadnienia dotyczące korzystania z tych rozmiarów, zobacz [rozmiary maszyn wirtualnych o wysokiej wydajności](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy: GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8             |8          | 56           | 1817                 | 2 / wysoka |
| A9             |16         | 112          | 1817                 | 4 / bardzo wysoka |
| A10             |8          | 56           | 1817                 | 2 / wysoka |
| A11             |16         | 112          | 1817                 | 4 / bardzo wysoka |

\*Obsługa funkcji RDMA

## <a name="av2-series"></a>Seria Av2

| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standardowa_A1_v2  | 1         | 2            | 10                   | 1 / średnia                 |
| Standardowa_A2_v2  | 2         | 4            | 20                   | 2 / średnia                 |
| Standardowa_A4_v2  | 4         | 8            | 40                   | 4 / wysoka                     |
| Standardowa_A8_v2  | 8         | 16           | 80                   | 8 / wysoka                     |
| Standardowa_A2m_v2 | 2         | 16           | 20                   | 2 / średnia                 |
| Standardowa_A4m_v2 | 4         | 32           | 40                   | 4 / wysoka                     |
| Standardowa_A8m_v2 | 8         | 64           | 80                   | 8 / wysoka                     |


## <a name="d-series"></a>Seria D
| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standardowa_D1     | 1         | 3,5          | 50                   | 1 / średnia |
| Standardowa_D2     | 2         | 7            | 100                  | 2 / wysoka |
| Standardowa_D3     | 4         | 14           | 200                  | 4 / wysoka |
| Standardowa_D4     | 8         | 28           | 400                  | 8 / wysoka |
| Standardowa_D11    | 2         | 14           | 100                  | 2 / wysoka |
| Standardowa_D12    | 4         | 28           | 200                  | 4 / wysoka |
| Standardowa_D13    | 8         | 56           | 400                  | 8 / wysoka |
| Standardowa_D14    | 16        | 112          | 800                  | 8 / bardzo wysoka |

## <a name="dv2-series"></a>Seria Dv2
| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standardowa_D1_v2  | 1         | 3,5          | 50                   | 1 / średnia |
| Standardowa_D2_v2  | 2         | 7            | 100                  | 2 / wysoka |
| Standardowa_D3_v2  | 4         | 14           | 200                  | 4 / wysoka |
| Standardowa_D4_v2  | 8         | 28           | 400                  | 8 / wysoka |
| Standardowa_D5_v2  | 16        | 56           | 800                  | 8 / ekstremalnie wysoka |
| Standardowa_D11_v2 | 2         | 14           | 100                  | 2 / wysoka |
| Standardowa_D12_v2 | 4         | 28           | 200                  | 4 / wysoka |
| Standardowa_D13_v2 | 8         | 56           | 400                  | 8 / wysoka |
| Standardowa_D14_v2 | 16        | 112          | 800                  | 8 / ekstremalnie wysoka |
| Standard_D15_v2 | 20        | 140          | 1000                | 8 / ekstremalnie wysoka |

## <a name="dv3-series"></a>Seria Dv3

| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB   | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standardowa_D2_v3  | 2         | 8             | 50                   | 2 / średnia |
| Standardowa_D4_v3  | 4         | 16            | 100                  | 2 / wysoka |
| Standardowa_D8_v3  | 8         | 32            | 200                  | 4 / wysoka |
| Standardowa_D16_v3 | 16        | 64            | 400                  | 8 / ekstremalnie wysoka |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / ekstremalnie wysoka |
| Standard_D48_v3 | 48        | 192           | 1200                 | 8 / ekstremalnie wysoka |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / ekstremalnie wysoka |

## <a name="ev3-series"></a>Seria Ev3

| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB   | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standardowa_E2_v3  | 2         | 16            | 50                   | 2 / średnia |
| Standardowa_E4_v3  | 4         | 32            | 100                  | 2 / wysoka |
| Standardowa_E8_v3  | 8         | 64            | 200                  | 4 / wysoka |
| Standardowa_E16_v3 | 16        | 128           | 400                  | 8 / ekstremalnie wysoka |
| Standardowa_E32_v3 | 32        | 256           | 800                  | 8 / ekstremalnie wysoka |
| Standard_E48_v3 | 48        | 384           | 1200                 | 8 / ekstremalnie wysoka |
| Standardowa_E64_v3 | 64        | 432           | 1600                 | 8 / ekstremalnie wysoka |


## <a name="g-series"></a>Seria G
| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standardowa_G1     | 2         | 28           | 384                  |1 / wysoka |
| Standardowa_G2     | 4         | 56           | 768                  |2 / wysoka |
| Standardowa_G3     | 8         | 112          | 1536                |4 / bardzo wysoka |
| Standardowa_G4     | 16        | 224          | 3072                |8 / ekstremalnie wysoka |
| Standard_G5     | 32        | 448          | 6144                |8 / ekstremalnie wysoka |

## <a name="h-series"></a>Seria H
Maszyny wirtualne serii H platformy Azure to następna generacja maszyn wirtualnych o wysokiej wydajności obliczeniowej, które idealnie sprawdzają się w przypadku najwyższych potrzeb obliczeniowych, na przykład w modelowaniu molekularnym i analizach obliczeniowych dynamiki płynów. Te 8 i 16 podstawowych maszyn wirtualnych są oparte na technologii procesora Intel Haswell E5-2667 v3, która ma DDR4 pamięć i lokalny magazyn oparty na dyskach SSD.

Seria H oferuje, obok znacznej mocy procesora CPU, różnorodne opcje dla sieci obsługujących technologię RDMA i niskie opóźnienia, korzystając z sieci InfiniBand o przepustowości FDR wraz z kilkoma konfiguracjami pamięci do obsługi obliczeń wymagających znacznego wykorzystania pamięci.

| Rozmiar            | Rdzenie procesora CPU | Pamięć: GiB  | Magazyn tymczasowy (SSD): GiB       | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standardowa_H8     | 8         | 56           | 1000                 | 8 / wysoka |
| Standardowa_H16    | 16        | 112          | 2000                 | 8 / bardzo wysoka |
| Standardowa_H8m    | 8         | 112          | 1000                 | 8 / wysoka |
| Standardowa_H16m   | 16        | 224          | 2000                 | 8 / bardzo wysoka |
| Standardowa_H16r*  | 16        | 112          | 2000                 | 8 / bardzo wysoka |
| Standardowa_H16mr* | 16        | 224          | 2000                 | 8 / bardzo wysoka |

\*Obsługa funkcji RDMA

## <a name="configure-sizes-for-cloud-services"></a>Skonfiguruj rozmiary dla Cloud Services
Rozmiar maszyny wirtualnej wystąpienia roli można określić jako część modelu usług opisanego przez [plik definicji usługi](cloud-services-model-and-package.md#csdef). Rozmiar roli określa liczbę rdzeni procesora CPU, pojemność pamięci oraz rozmiar lokalnego systemu plików przydzielony do uruchomionego wystąpienia. Wybierz rozmiar roli na podstawie wymagania dotyczącego zasobów aplikacji.

Oto przykład ustawiania rozmiaru roli Standard_D2 dla wystąpienia roli sieci Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Zmiana rozmiaru istniejącej roli

Ze względu na to, że zmiany obciążeń lub nowe rozmiary maszyn wirtualnych staną się dostępne, można zmienić rozmiar roli. W tym celu należy zmienić rozmiar maszyny wirtualnej w pliku definicji usługi (jak pokazano powyżej), ponownie spakować usługę w chmurze i wdrożyć ją.

>[!TIP]
> Możesz chcieć użyć różnych rozmiarów maszyn wirtualnych dla roli w różnych środowiskach (np. Test a produkcja). Jednym ze sposobów jest utworzenie w projekcie wielu plików definicji usługi (. csdef), a następnie utworzenie różnych pakietów usług w chmurze na środowisko podczas zautomatyzowanej kompilacji za pomocą narzędzia CSPack. Aby dowiedzieć się więcej o elementach pakietu usług w chmurze i sposobach ich tworzenia, zobacz [co to jest model usług Cloud Services i jak go spakować?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Pobierz listę rozmiarów
Aby uzyskać listę rozmiarów, można użyć programu PowerShell lub interfejsu API REST. Interfejs API REST jest udokumentowany w [tym miejscu](/previous-versions/azure/reference/dn469422(v=azure.100)). Poniższy kod jest poleceniem programu PowerShell, które wyświetla listę wszystkich rozmiarów dostępnych dla Cloud Services. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [limitów, przydziałów i ograniczeń usługi i subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
* Dowiedz się więcej [o rozmiarach maszyn wirtualnych obliczeniowych o wysokiej wydajności](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) dla obciążeń HPC.
