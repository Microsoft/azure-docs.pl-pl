---
title: Uaktualnij wersję klastra usługi Azure Service Fabric
description: Dowiedz się więcej o wersjach klastra w usłudze Azure Service Fabric, łącznie z linkiem do najnowszych wydań z blogu zespołu Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 3e859a04ffb0b885aab0f31e83afad8380cbcc95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010205"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Uaktualnij wersję klastra usługi Azure Service Fabric

Upewnij się, że w klastrze jest zawsze uruchomiona obsługiwana wersja usługi Azure Service Fabric. Co najmniej 60 dni po opublikowaniu nowej wersji Service Fabric, obsługa poprzedniej wersji zostanie zakończona. Anonse nowych wydań znajdziesz na [blogu zespołu Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Dla każdej wersji środowiska uruchomieniowego Service Fabric można użyć określonych lub starszych wersji pakietów SDK/NuGet. Nowsze wersje pakietów mogą nie być ukierunkowane na starsze klastry. Starsze klastry mogą mieć zmiany funkcji lub protokołu, które nie są obsługiwane przez nowsze środowiska pakietu.

Zapoznaj się z następującymi artykułami, aby uzyskać szczegółowe informacje o tym, jak zapewnić, że w klastrze działa obsługiwana wersja Service Fabric:

- [Uaktualnianie klastra usługi Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Uaktualnij wersję Service Fabric działającą w autonomicznym klastrze systemu Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Nieobsługiwane wersje

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Zgłoś alert dla wersji z zakresu od 5,7 do 6.3.63. *

Aby zwiększyć bezpieczeństwo i dostępność, infrastruktura platformy Azure wprowadziła zmianę, która może mieć wpływ na Service Fabric klientów. Ta zmiana dotyczy wszystkich klastrów Service Fabric z systemami 5,7 do 6,3.

Aktualizacja środowiska uruchomieniowego Service Fabric jest dostępna dla wszystkich obsługiwanych Service Fabric wersji we wszystkich regionach. Uaktualnij do jednej z najnowszych obsługiwanych wersji do 19 stycznia 2021, aby uniknąć przerw w działaniu usługi.

Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej, skontaktuj się z pośrednictwem kanałów pomocy technicznej systemu Azure. Otwórz żądanie pomocy technicznej dla usługi Azure Service Fabric i podaj ten kontekst w ramach biletu pomocy technicznej.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Jeśli nie możesz uaktualnić do obsługiwanej wersji

Klastry usługi Azure Service Fabric uruchomione w wersjach od 5,7 do 6.3.63. * będą niedostępne, jeśli nie uaktualniono do 19 stycznia 2021.

#### <a name="required-action"></a>Wymagana akcja

Uaktualnij program do obsługiwanej Service Fabric wersji, aby zapobiec przestojom lub utracie funkcjonalności związanej z tą zmianą. Upewnij się, że w klastrach działa co najmniej następujące wersje, aby zapobiec problemom w danym środowisku.

> [!Note]
> **Wszystkie wydane wersje 7,2 obejmują niezbędne zmiany**.
  
  | System operacyjny | Bieżące środowisko uruchomieniowe Service Fabric w klastrze | Wydanie w wersji CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Zgłoś alert dla wersji nowszych niż 6,3

Aby zwiększyć bezpieczeństwo i dostępność, infrastruktura platformy Azure wprowadziła zmianę, która może mieć wpływ na Service Fabric klientów. Ta zmiana wpłynie na wszystkie klastry Service Fabric używające [trybu otwierania sieci dla kontenerów](./service-fabric-networking-modes.md#set-up-open-networking-mode) i uruchamiania wersji 6,3 do 7,0 lub niezgodnych wersji obsługiwanych później niż 7,0. Aktualizacja środowiska uruchomieniowego Service Fabric jest dostępna dla wszystkich obsługiwanych Service Fabric wersji we wszystkich regionach.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Jeśli nie możesz uaktualnić do obsługiwanej wersji

W klastrach usługi Azure Service Fabric, które działają w niezmienionych wersjach nowszych niż 6,3, zostaną utracone przerwy w działaniu funkcji lub usług, jeśli nie zostały one uaktualnione do obsługiwanej wersji do 19 stycznia 2021.
  
  - **W przypadku klastrów z uruchomioną wersją Service Fabric większą niż 6,3 nie korzystających z funkcji sieci otwartych** klaster pozostanie nieaktualny.

 - **W przypadku klastrów z uruchomioną wersją Service Fabric większą niż 6,3 i używania [funkcji Otwórz sieć dla kontenerów](./service-fabric-networking-modes.md#set-up-open-networking-mode)** klaster może stać się niedostępny i przestanie działać, co może spowodować przerwanie działania usługi dla obciążeń.
 
 -   **W przypadku klastrów z systemami [Windows między 7.0.457 i 7.0.466 (w obu wersjach uwzględnionych)](#supported-version-names) oraz systemu operacyjnego Windows jest włączona funkcja kontenerów systemu Windows. Uwaga: nie ma to wpływu na wersje systemu Linux 7.0.457, 7.0.464 i 7.0.465**.
    - **Wpływ**: klaster przestanie działać, co może spowodować przerwanie działania usługi dla obciążeń.
    
#### <a name="required-action"></a>Wymagana akcja

Aby zapobiec przestojom lub utracie funkcjonalności, należy się upewnić, że w klastrach działa jedna z następujących wersji.

Wersje Service Fabric w tabeli zawierają niezbędne zmiany, aby zapobiec utracie funkcjonalności. Upewnij się, że używasz jednej z tych wersji.  

> [!Note]
> **Klastry usługi Azure Service Fabric działające w wersji 6,5 muszą wykonywać wiele uaktualnień w tym samym czasie przed zmianą infrastucuture, aby uniknąć utraty funkcjonalności klastra**. 
>   -   1. Uaktualnij do 7.0.466. **W tej wersji pośredniej nie mogą znajdować się klastry z systemem operacyjnym Windows, w którym włączono funkcję kontenerów systemu Windows. Muszą oni wykonać następny krok (II) poniżej. oznacza to, że  Uaktualnij, aby uzyskać bezpieczniejsze i zgodne wersja, aby uniknąć przerw w działaniu usługi**
>   -   2. Uaktualnij do najnowszej wersji programu skargi w wersji 7,0 * Release (7.0.478) lub dowolnej z wyższych wersji wymienionych poniżej.


> [!Note]
> **Wszystkie wersje wydań 7,2 obejmują niezbędne zmiany**.

 | System operacyjny | Bieżące środowisko uruchomieniowe Service Fabric w klastrze | Wydanie w wersji CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16,04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

## <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli wymieniono wersje Service Fabric i ich daty końcowe pomocy technicznej.

| Service Fabric środowisko uruchomieniowe w klastrze | Można uaktualnić bezpośrednio z wersji klastra |Zgodna wersja zestawu SDK lub pakietu NuGet | Koniec wsparcia |
| --- | --- |--- | --- |
| Wszystkie wersje klastra przed 5.3.121 | 5.1.158.* |Mniejsze niż lub równe wersji 2,3 |20 stycznia 2017 |
| 5,3. * | 5.1.158.* |Mniejsze niż lub równe wersji 2,3 |24 lutego 2017 |
| 5,4. * | 5.1.158.* |Mniejsze niż lub równe wersji 2,4 |10 maja 2017       |
| 5,5. * | 5.4.164.* |Mniejsze niż lub równe wersji 2,5 |10 sierpnia 2017 r.    |
| 5,6. * | 5.4.164.* |Mniejsze niż lub równe wersji 2,6 |13 października 2017 r.   |
| 5,7. * | 5.4.164.* |Mniejsze niż lub równe wersji 2,7 |15 grudnia 2017 r.  |
| 6,0. * | 5.6.205.* |Mniejsze niż lub równe wersji 2,8 |30 marca 2018     |
| 6,1. * | 5.7.221.* |Mniejsze niż lub równe wersji 3,0 |15 lipca 2018      |
| 6,2. * | 6.0.232.* |Mniejsze niż lub równe wersji 3,1 |26 października 2018   |
| 6,3. * | 6.1.480.* |Mniejsze niż lub równe wersji 3,2 |31 marca 2019  |
| 6,4. * | 6.2.301.* |Mniejsze niż lub równe wersji 3,3 |15 września 2019 |
| 6,5. * | 6.4.617.* |Mniejsze niż lub równe wersji 3,4 |1 sierpnia 2020 |
| 7.0.466.* | 6.4.664.* |Mniejsze niż lub równe wersji 4,0|31 stycznia 2021  |
| 7.0.466.* | 6,5. * |Mniejsze niż lub równe wersji 4,0|31 stycznia 2021 |
| 7.0.470.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,0 |31 stycznia 2021  |
| 7.0.472.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,0 |31 stycznia 2021  |
| 7.0.478.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,0 |31 stycznia 2021  |
| 7.1.409.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.417.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.428.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.456.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.458.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.459.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.503.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.1.510.* | 7.0.466.* |Mniejsze niż lub równe wersji 4,1 |31 lipca 2021 |
| 7.2.413.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.432.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.433.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.445.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.452.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.457.* | 7.0.470.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |
| 7.2.477.* | 7.0.478.* |Mniejsze niż lub równe wersji 4,2 |Bieżąca wersja, dlatego bez daty zakończenia |

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne obsługiwane przez obsługiwane wersje Service Fabric.

| System operacyjny | Najwcześniejsza obsługiwana wersja Service Fabric |
| --- | --- |
| Windows Server 2012 z dodatkiem R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| System Windows Server 1709 | 6.0 |
| System Windows Server 1803 | 6.4 |
| System Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Obsługiwane nazwy wersji

W poniższej tabeli wymieniono nazwy wersji Service Fabric i odpowiadające im numery wersji.

| Nazwa wersji | Numer wersji systemu Windows | Numer wersji systemu Linux |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Nie dotyczy|
| 5,3 CU1 | 5.3.204.9494 | Nie dotyczy|
| 5,3 ZASTOSUJESZ PAKIETU CU2 | 5.3.301.9590 | Nie dotyczy|
| 5,3 CU3 | 5.3.311.9590 | Nie dotyczy|
| 5,4 ZASTOSUJESZ PAKIETU CU2 | 5.4.164.9494 | Nie dotyczy|
| 5,5 CU1 | 5.5.216.0    | Nie dotyczy|
| 5,5 ZASTOSUJESZ PAKIETU CU2 | 5.5.219.0 | Nie dotyczy|
| 5,5 CU3 | 5.5.227.0 | Nie dotyczy|
| 5,5 CU4 | 5.5.232.0 | Nie dotyczy|
| 5,6 RTO | 5.6.204.9494 | Nie dotyczy|
| 5,6 ZASTOSUJESZ PAKIETU CU2 | 5.6.210.9494 | Nie dotyczy|
| 5,6 CU3 | 5.6.220.9494 | Nie dotyczy|
| 5,7 RTO | 5.7.198.9494 | Nie dotyczy|
| 5,7 CU4 | 5.7.221.9494 | Nie dotyczy|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 ZASTOSUJESZ PAKIETU CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 ZASTOSUJESZ PAKIETU CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Nie dotyczy|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 ZASTOSUJESZ PAKIETU CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 ZASTOSUJESZ PAKIETU CU2 | 6.4.622.9590 | Nie dotyczy|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Nie dotyczy|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Nie dotyczy|
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 ZASTOSUJESZ PAKIETU CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 ZASTOSUJESZ PAKIETU CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 ZASTOSUJESZ PAKIETU CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,1 CU10 | 7.1.510.9590 | NA |
| 7,2 RTO | 7.2.413.9590 | NA |
| 7,2 ZASTOSUJESZ PAKIETU CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | NA |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7,2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7,2 CU7 | 7.2.477.9590 | 7.2.476.1 |