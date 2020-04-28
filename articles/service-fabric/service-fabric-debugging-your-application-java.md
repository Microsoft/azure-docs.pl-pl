---
title: Debugowanie aplikacji w programie zaćmienie
description: Zwiększ niezawodność i wydajność usług, opracowując i debugując je w programie w ramach programu w przypadku lokalnego klastra projektowego.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614489"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debugowanie aplikacji Java Service Fabric przy użyciu programu zaćmienie
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zaćmienie/Java](service-fabric-debugging-your-application-java.md)
> 

1. Uruchom lokalny klaster programistyczny, wykonując czynności opisane w sekcji [Konfigurowanie środowiska deweloperskiego Service Fabric](service-fabric-get-started-linux.md).

2. Zaktualizuj entryPoint.sh usługi, którą chcesz debugować, aby uruchomić proces języka Java z parametrami zdalnego debugowania. Ten plik można znaleźć w następującej lokalizacji: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. W tym przykładzie na potrzeby debugowania ustawiono port 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Zaktualizuj manifest aplikacji przez ustawienie liczby wystąpień lub liczby replik dla usługi, która jest debugowana na 1. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw `InstanceCount="1"`, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Wdrażanie aplikacji.

5. W środowisku IDE: zaćmienie wybierz kolejno opcje **Run-> debugowanie konfiguracje-> zdalna aplikacja Java i właściwości połączenia wejściowego** i ustaw właściwości w następujący sposób:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Ustaw punkty przerwania w żądanych punktach i Debuguj aplikację.

Jeśli aplikacja uległa awarii, możesz również włączyć coredumps. Wykonaj `ulimit -c` w powłoce i jeśli zwraca wartość 0, coredumps nie są włączone. Aby włączyć nieograniczony coredumps, wykonaj następujące polecenie: `ulimit -c unlimited`. Możesz również sprawdzić stan przy użyciu polecenia `ulimit -a`.  Jeśli chcesz zaktualizować ścieżkę generacji CoreDump, wykonaj `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`polecenie. 

### <a name="next-steps"></a>Następne kroki

* [Zbierz dzienniki przy użyciu Diagnostyka Azure systemu Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
