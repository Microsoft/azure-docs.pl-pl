---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016138"
---
1. Zainstaluj dapl, rdmacm, ibverbs i mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. W programie/etc/waagent.conf Włącz funkcję RDMA, usuwając komentarz w poniższych wierszach konfiguracji. Aby edytować ten plik, potrzebny jest dostęp do katalogu głównego.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Dodaj lub Zmień następujące ustawienia pamięci w KB w pliku/etc/security/limits.conf. Aby edytować ten plik, potrzebny jest dostęp do katalogu głównego. W celach testowych można ustawić memlock na nieograniczoną. Na przykład: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Zainstaluj bibliotekę Intel MPI. [Kup i Pobierz](https://software.intel.com/intel-mpi-library/) bibliotekę z firmy Intel lub Pobierz [bezpłatną wersję ewaluacyjną](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Obsługiwane są tylko środowiska uruchomieniowe Intel MPI 5. x.
 
   Kroki instalacji można znaleźć w [podręczniku instalacji biblioteki MPI firmy Intel](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Włącz ptrace dla niegłównych procesów niezwiązanych z debugerem (wymaganych przez najnowsze wersje technologii Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
