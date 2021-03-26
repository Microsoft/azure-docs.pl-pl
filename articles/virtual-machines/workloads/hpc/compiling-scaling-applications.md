---
title: Skalowanie aplikacji HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak skalować aplikacje HPC na maszynach wirtualnych platformy Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 4ab2c599bea4b2e3e682755a80a2ee348e4de7ef
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606780"
---
# <a name="scaling-hpc-applications"></a>Skalowanie aplikacji HPC

Optymalna wydajność skalowania i skalowalności aplikacji HPC na platformie Azure wymaga dostrajania wydajności i optymalizacji dla określonego obciążenia. Ta sekcja i strony specyficzne dla serii maszyn wirtualnych oferują ogólne wskazówki dotyczące skalowania aplikacji.

## <a name="application-setup"></a>Konfiguracja aplikacji
[Repozytorium azurehpc](https://github.com/Azure/azurehpc) zawiera wiele przykładów:
- Optymalne Konfigurowanie i uruchamianie [aplikacji](https://github.com/Azure/azurehpc/tree/master/apps) .
- Konfiguracja [systemów plików i klastrów](https://github.com/Azure/azurehpc/tree/master/examples).
- [Samouczki](https://github.com/Azure/azurehpc/tree/master/tutorials) ułatwiające szybkie rozpoczęcie pracy z niektórymi typowymi przepływami.

## <a name="optimally-scaling-mpi"></a>Optymalne skalowanie MPI 

Poniższe sugestie dotyczą optymalnej wydajności, wydajności i spójności skalowania aplikacji:

- W przypadku zadań o mniejszych skalach (tj. < połączeń 256 K) Użyj opcji:
   ```bash
   UCX_TLS=rc,sm
   ```

- W przypadku większych zadań skalowania (tj. > połączeń 256 K) Użyj opcji:
   ```bash
   UCX_TLS=dc,sm
   ```

- W powyższej części, aby obliczyć liczbę połączeń dla zadania MPI, użyj:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Routing adaptacyjny
Routing adaptacyjny (AR) umożliwia korzystanie z platformy Azure Virtual Machines (maszyny wirtualne) z systemem EDR i HDR InfiniBand w celu automatycznego wykrywania i zapobiegania przeciążenia sieci przez dynamiczne Wybieranie optymalnych ścieżek sieciowych. W rezultacie AR oferuje ulepszone opóźnienia i przepustowość w sieci InfiniBand, co z kolei umożliwia zwiększenie wydajności i skalowalności. Aby uzyskać więcej informacji, zapoznaj się z [artykułem TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Przypinanie procesów

- Przypnij procesy do rdzeni przy użyciu podejścia sekwencyjnego (w przeciwieństwie do podejścia autorównowaga). 
- Powiązanie według architektury NUMA/Core/HwThread jest lepsze niż domyślne powiązanie.
- W przypadku hybrydowych aplikacji równoległych (OpenMP + MPI) Użyj 4 wątków i 1 MPI rangi na CCX przy HB i HBv2 rozmiarach maszyn wirtualnych.
- W przypadku czystych aplikacji MPI eksperyment z rangą 1-4 MPI na CCX w celu uzyskania optymalnej wydajności w przypadku HB i HBv2 rozmiarów maszyn wirtualnych.
- Niektóre aplikacje o najwyższej czułości przepustowości pamięci mogą korzystać z mniejszej liczby rdzeni na CCX. W przypadku tych aplikacji użycie rdzeni 3 lub 2 na CCX może zmniejszyć rywalizację o przepustowość pamięci i zapewnić wyższą wydajność rzeczywistą lub większą spójność. W szczególności MPI Allreduce może korzystać z tego podejścia.
- W przypadku znacznie większych przebiegów skalowania zaleca się użycie UD lub transportów hybrydowych RC + UD. Wiele bibliotek MPI/bibliotek środowiska uruchomieniowego to wewnętrznie (na przykład UCX lub MVAPICH2). Sprawdź konfiguracje transportu w przypadku uruchamiania na dużą skalę.

## <a name="compiling-applications"></a>Kompilowanie aplikacji
<br>
<details>
<summary>Kliknij, aby rozwinąć</summary>

Chociaż nie jest to konieczne, Kompilowanie aplikacji z odpowiednimi flagami optymalizacji zapewnia najlepszą wydajność skalowania na maszynach wirtualnych z serii HB i HC.

### <a name="amd-optimizing-cc-compiler"></a>Kompilator AMD optymalizujący język C/C++

Kompilator AMD optymalizujący kompilator C/C++ (AOCC) oferuje wysoki poziom zaawansowanych optymalizacji, wielowątkowości i obsługi procesora, które obejmują optymalizację globalną, wektoryzacji, analizy międzyprocesowe, przekształcenia pętli i generowanie kodu. Pliki binarne kompilatora AOCC są odpowiednie dla systemów Linux z biblioteką GNU C library (glibc) w wersji 2,17 lub nowszej. Pakiet kompilatora składa się z kompilatora C/C++ (Clang), kompilatora Pascal (FLANG) oraz Pascal frontonu do Clang (sceny Dragon jaja).

### <a name="clang"></a>Clang

Clang to język C, C++ i zamierzenie obsługujący przetwarzanie wstępne, analizowanie, optymalizacja, generowanie kodu, zestaw i łączenie. Clang obsługuje  `-march=znver1` flagę, aby umożliwić optymalne generowanie kodu i dostrajanie dla architektury x86 opartych na architekturze AMD Zen.

### <a name="flang"></a>FLANG

Kompilator FLANG jest ostatnim dodatkiem do pakietu AOCC (dodano 2018 kwietnia) i jest obecnie w wersji wstępnej dla deweloperów do pobrania i przetestowania. W oparciu o Pascal 2008, AMD rozszerza wersję usługi GitHub FLANG ( https://github.com/flang-compiler/flang) . Kompilator FLANG obsługuje wszystkie opcje kompilatora Clang i dodatkową liczbę opcji kompilatora specyficznych dla FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg to wtyczka do usługi w zatoce, która zastępuje optymalizacje i generatory kodu z programu w ramach projektu LLVM. DragonEgg, który jest dostarczany z AOCC współpracuje z usługą współpracy w zatoce — 4.8. x, został przetestowany dla celów x86-32/x86-64 i został pomyślnie użyty na różnych platformach systemu Linux.

GFortran jest rzeczywistym frontonem dla programów Pascal odpowiedzialnych za przetwarzanie, analizowanie i analizę semantyczną generującą reprezentację pośrednią (IR) GIMPLE. DragonEgg jest wtyczką GNU, podłączając do przepływu kompilacji GFortran. Implementuje interfejs API wtyczki GNU. W przypadku architektury wtyczki DragonEgg przechodzi do sterownika kompilatora, co prowadzi do różnych faz kompilacji.  Po wykonaniu instrukcji dotyczących pobierania i instalacji sceny Dragon jaja można wywołać przy użyciu: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Kompilator PGI
PGI Community Edition. 17 został potwierdzony do pracy z procesorem AMD EPYC. Skompilowana przez PGI wersja strumienia zapewnia pełną przepustowość pamięci na platformie. Nowsza wersja Community 18,10 (lis 2018) powinna również być odpowiednia. Poniżej znajduje się przykładowy interfejs wiersza polecenia do kompilatora optymalnie z kompilatorem Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilator Intel
Intel Compiler Ver. 18 został potwierdzony do pracy z procesorem AMD EPYC. Poniżej znajduje się przykładowy interfejs wiersza polecenia do kompilatora optymalnie z kompilatorem Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilator w zatoce 
W przypadku platformy HPC procesor AMD zaleca kompilatory w wersji 7,3 lub nowszej. Starsze wersje, takie jak 4.8.5 dołączone do RHEL/CentOS 7,4, nie są zalecane. W wersji 7,3 i nowszej program zapewni znacznie wyższą wydajność testów HPL, HPCG i DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](/azure/architecture/topics/high-performance-computing/) na platformie Azure.
