---
title: Skalowanie aplikacji HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak skalować aplikacje HPC na maszyny wirtualne platformy Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600968"
---
# <a name="scaling-hpc-applications"></a>Skalowanie aplikacji HPC

Optymalna wydajność skalowania w górę i w poziomie aplikacji HPC na platformie Azure wymaga eksperymentów dostrajania i optymalizacji wydajności dla określonego obciążenia. Ta sekcja i strony dotyczące serii maszyn wirtualnych oferują ogólne wskazówki dotyczące skalowania aplikacji.

## <a name="application-setup"></a>Konfiguracja aplikacji
Repo [azurehpc zawiera](https://github.com/Azure/azurehpc) wiele przykładów:
- Optymalne konfigurowanie i [uruchamianie](https://github.com/Azure/azurehpc/tree/master/apps) aplikacji.
- Konfiguracja [systemów plików i klastrów](https://github.com/Azure/azurehpc/tree/master/examples).
- [Samouczki](https://github.com/Azure/azurehpc/tree/master/tutorials) dotyczące łatwego rozpoczynania pracy z niektórymi najczęściej spotykane przepływy pracy aplikacji.

## <a name="optimally-scaling-mpi"></a>Optymalne skalowanie mpi 

Poniższe sugestie dotyczą optymalnej wydajności, wydajności i spójności skalowania aplikacji:

- W przypadku zadań o mniejszej skali (tj. < 256 000 połączeń) użyj opcji:
   ```bash
   UCX_TLS=rc,sm
   ```

- W przypadku zadań o większej skali (tj. > 256 000 połączeń) użyj opcji:
   ```bash
   UCX_TLS=dc,sm
   ```

- W powyższym przykładzie, aby obliczyć liczbę połączeń dla zadania MPI, użyj:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Routing adaptacyjny
Routing adaptacyjny (AR) umożliwia usłudze Azure Virtual Machines (VMs) z uruchomionymi technologiami EDR iJĄCĄ InfiniBand automatyczne wykrywanie przeciążenia sieci i unikanie go przez dynamiczne wybieranie bardziej optymalnych ścieżek sieciowych. W związku z tym ar oferuje ulepszone opóźnienia i przepustowość w sieci InfiniBand, co z kolei zwiększa wydajność i wydajność skalowania. Aby uzyskać więcej informacji, zapoznaj się z [artykułem TechCo w witrynie TechCo w witrynie](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Przypinanie procesów

- Przypinanie procesów do rdzeni przy użyciu sekwencyjnego przypinania (w przeciwieństwie do podejścia automatycznego równoważenia). 
- Powiązanie przez Numa/Core/HwThread jest lepsze niż powiązanie domyślne.
- W przypadku hybrydowych aplikacji równoległych (OpenMP+MPI) użyj 4 wątków i 1 rangi MPI na ccx w przypadku rozmiarów maszyn wirtualnych HB i HBv2.
- W przypadku czystych aplikacji MPI poeksperymentuj z 1–4 rangami MPI na ccx w celu uzyskania optymalnej wydajności na maszynach wirtualnych o rozmiarach HB i HBv2.
- W przypadku niektórych aplikacji z bardzo dużą podatnością na przepustowość pamięci użycie większej liczby rdzeni na ccx może być korzystne. W przypadku tych aplikacji użycie 3 lub 2 rdzeni na ccx może zmniejszyć poziom zadowolenia z przepustowości pamięci i zapewnić wyższą wydajność w świecie rzeczywistym lub bardziej spójną skalowalność. W szczególności rozwiązanie MPI Allreduce może skorzystać z tego podejścia.
- W przypadku znacznie większych przebiegów skalowania zaleca się używanie transportu UD lub hybrydowego transportu RC+UD. Wiele bibliotek MPI/bibliotek środowiska uruchomieniowego robi to wewnętrznie (na przykład UCX lub MVAPICH2). Sprawdź konfiguracje transportu dla przebiegów na dużą skalę.

## <a name="compiling-applications"></a>Kompilowanie aplikacji
<br>
<details>
<summary>Kliknij, aby rozwinąć</summary>

Chociaż nie jest to konieczne, kompilowanie aplikacji z odpowiednimi flagami optymalizacji zapewnia najlepszą wydajność skalowania w górę na komputerach wirtualnych z serii HB i HC.

### <a name="amd-optimizing-cc-compiler"></a>Optymalizacja kompilatora C/C++ firmy AMD

System kompilatora kompilatora C/C++ (AOCC) firmy AMD oferuje wysoki poziom zaawansowanych optymalizacji, wielowątkowych i obsługi procesorów, w tym optymalizację globalną, wektoryzację, analizy między proceduralne, przekształcenia pętli i generowanie kodu. Pliki binarne kompilatora AOCC są odpowiednie dla systemów Linux mających bibliotekę GNU C Library (glibc) w wersji 2.17 lub nowszą. Pakiet kompilatora składa się z kompilatora C/C++ (clang), kompilatora Jirana (FLANG) i frontony z Clang (Dragon Dragon).

### <a name="clang"></a>Clang

Clang to kompilator języków C, C++ i Objective-C, który przetwarza wstępnie, analizuje, optymalizuje, generuje kod, zestaw i łączenie. Rozwiązanie Clang obsługuje flagę , aby umożliwić najlepsze generowanie i dostrajanie kodu dla architektury  `-march=znver1` x86 firmy AMD opartej na architekturze Zen.

### <a name="flang"></a>Język FLANG

Kompilator języka FLANG jest ostatnim dodatkiem do pakietu AOCC (dodanego w kwietniu 2018 r.) i jest obecnie w wersji wstępnej do pobrania i testowania przez deweloperów. W oparciu o oprogramowanie z 2008 r. Firma AMD rozszerza wersję języka FLANG w serwisie GitHub ( https://github.com/flang-compiler/flang) . Kompilator języka FLANG obsługuje wszystkie opcje kompilatora Clang oraz dodatkową liczbę opcji kompilatora specyficznych dla języka FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg to wtyczka gcc, która zastępuje optymalizatory i generatory kodu GCC tymi z projektu LLVM. DragonEgg dostarczany z AOCC współpracuje z gcc-4.8.x, został przetestowany pod kątem obiektów docelowych x86-32/x86-64 i został pomyślnie użyty na różnych platformach Linux.

GProcesran jest rzeczywistym frontonem dla programów w Czajce odpowiedzialnym za przetwarzanie wstępne, analizowanie i analizę semantyczną generującą pośrednią reprezentację GIMPLE (IR) GCC. DragonEgg to wtyczka GNU podłączana do przepływu kompilacji G Pluginran. Implementuje interfejs API wtyczek GNU. Wraz z architekturą wtyczek dragonegg staje się sterownikiem kompilatora, co pozwala na prowadzenie różnych faz kompilacji.  Po pobraniu i zainstalowaniu instrukcji można wywołać aplikację Dragon Dragon przy użyciu następujących poleceń: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Kompilator PGI
PGI Community Edition ver. Potwierdza się, że 17 pracuje z procesorem AMD EPYC. Skompilowana wersja PGI strumienia zapewnia pełną przepustowość pamięci platformy. Nowsza wersja Community Edition 18.10 (listopad 2018) powinna również działać dobrze. Poniżej przedstawiono przykładowy interfejs wiersza polecenia do optymalnego kompilatora z kompilatorem Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilator Intel
Intel Compiler ver. Potwierdza się, że 18 działa z procesorem AMD EPYC. Poniżej przedstawiono przykładowy interfejs wiersza polecenia do optymalnego kompilatora z kompilatorem Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilator GCC 
W przypadku hpc firma AMD zaleca kompilator GCC w wersjach 7.3 lub nowszej. Starsze wersje, takie jak 4.8.5 dołączone do systemu RHEL/CentOS 7.4, nie są zalecane. GCC 7.3 i nowsze zapewniają znacznie wyższą wydajność testów HPL, HPCG i DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Następne kroki

- Przetestuj swoją [wiedzę, modułu szkoleniowego na temat optymalizacji aplikacji HPC na platformie Azure.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Zapoznaj się [z omówieniem serii HBv3](hbv3-series-overview.md) i [omówieniem serii HC.](hc-series-overview.md)
- Zapoznaj się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności na Azure Compute [Tech Community Blogi](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Dowiedz się więcej o [hpc na](/azure/architecture/topics/high-performance-computing/) platformie Azure.
