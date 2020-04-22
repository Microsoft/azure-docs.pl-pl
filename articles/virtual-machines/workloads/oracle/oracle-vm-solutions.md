---
title: Rozwiązania Oracle na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsługiwanych konfiguracjach i ograniczeniach obrazów maszyn wirtualnych Oracle na platformie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: borisb
ms.openlocfilehash: 0cee7c25960d567c75a14d8ad9ef95b3e7221862
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683417"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Obrazy maszyn wirtualnych Oracle i ich wdrożenie na platformie Microsoft Azure

W tym artykule omówiono informacje o rozwiązaniach Oracle opartych na obrazach maszyn wirtualnych opublikowanych przez oracle w portalu Azure Marketplace. Jeśli interesują Cię rozwiązania w zakresie aplikacji między chmurami z oracle cloud infrastructure, zobacz [rozwiązania aplikacji Oracle integrujące microsoft azure i oracle cloud infrastructure](oracle-oci-overview.md).

Aby uzyskać listę aktualnie dostępnych obrazów, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od maja 2019 r. dostępne są następujące obrazy:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Te obrazy są uważane za "Bring Your Own License" i jako takie będą naliczane tylko za koszty obliczeniowe, magazynowe i sieciowe poniesione w związku z uruchomieniem maszyny Wirtualnej.  Zakłada się, że masz odpowiednie licencje na korzystanie z oprogramowania Oracle i że masz aktualną umowę pomocy technicznej z Oracle. Oracle ma zagwarantowaną mobilność licencji z lokalnego na platformę Azure. Szczegółowe informacje na temat mobilności licencji można znaleźć w opublikowanej notatce [oracle i microsoft.](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 

Osoby fizyczne mogą również oprzeć swoje rozwiązania na obrazie niestandardowym, który tworzą od podstaw na platformie Azure lub przekazać obraz niestandardowy ze środowiska lokalnego.

## <a name="oracle-database-vm-images"></a>Obrazy maszyn wirtualnych bazy danych Oracle
Oracle obsługuje uruchamianie oracle database 12.1 i wyższych wersji Standard i Enterprise na platformie Azure na obrazach maszyn wirtualnych opartych na systemie Oracle Linux.  Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych bazy danych Oracle Database na platformie Azure, należy odpowiednio powymitować obraz maszyny Wirtualnej i używać dysków zarządzanych dysków SSD premium lub ultra SSD. Aby uzyskać instrukcje dotyczące szybkiego uruchamiania bazy danych Oracle Database na platformie Azure przy użyciu opublikowanego obrazu maszyny [Wirtualnej](oracle-database-quick-create.md)Oracle, wypróbuj przewodnik Szybki start bazy danych Oracle Database .

### <a name="attached-disk-configuration-options"></a>Dołączone opcje konfiguracji dysku

Dołączone dyski są zależne od usługi magazynu obiektów Blob platformy Azure. Każdy dysk standardowy jest w stanie maksymalnie około 500 operacji wejścia/wyjścia na sekundę (IOPS). Nasza oferta dysków premium jest preferowana w przypadku obciążeń bazy danych o wysokiej wydajności i może osiągnąć do 5000 IOps na dysk. Można użyć jednego dysku, jeśli spełnia twoje potrzeby w zakresie wydajności. Można jednak poprawić efektywną wydajność usług We/Wy, jeśli używasz wielu dołączonych dysków, rozmieszczasz dane bazy danych na nich, a następnie użyj automatycznego zarządzania pamięcią masową Oracle (ASM). Więcej informacji na temat oracle ASM można [znaleźć w omówienie funkcji Oracle Automatic Storage.](https://www.oracle.com/technetwork/database/index-100339.html) Na przykład jak zainstalować i skonfigurować Oracle ASM na maszynie wirtualnej platformy Azure z systemem Linux, zobacz [instalowanie i konfigurowanie Oracle Automated Storage Management](configure-oracle-asm.md) samouczek.

### <a name="shared-storage-configuration-options"></a>Opcje konfiguracji magazynu udostępnionego

Usługa Azure NetApp Files została zaprojektowana w celu spełnienia podstawowych wymagań uruchamiania obciążeń o wysokiej wydajności, takich jak bazy danych w chmurze, i zapewnia;
- Natywna usługa magazynowania nfs platformy Azure do uruchamiania obciążeń Oracle za pośrednictwem natywnego klienta NFS maszyny wirtualnej lub oracle dNFS
- Skalowalne warstwy wydajności, które odzwierciedlają rzeczywisty zakres wymagań we/wy
- Małe opóźnienia
- Wysoka dostępność, wysoka trwałość i łatwość zarządzania na dużą skalę, zwykle wymagane przez krytyczne obciążenia przedsiębiorstwa (takie jak SAP i Oracle)
- Szybkie i wydajne tworzenie kopii zapasowych i odzyskiwanie, aby osiągnąć najbardziej agresywne RTO i RPO SLA

Te możliwości są możliwe, ponieważ usługa Azure NetApp Files jest oparta na netapp® ONTAP® systemach wszystkich flash działających w środowisku centrum danych platformy Azure — jako usługa Azure Native. Rezultatem jest technologia magazynu bazy danych idealne, które mogą być aprowizowane i używane, podobnie jak inne opcje magazynu platformy Azure. Zobacz [dokumentację usługi Azure NetApp Files,](https://docs.microsoft.com/azure/azure-netapp-files/) aby uzyskać więcej informacji na temat wdrażania woluminów NFS plików plików NetApp i uzyskiwania do nich dostępu. Zobacz [Przewodnik najlepszych rozwiązań oracle na platformie Azure Deployment Using Azure NetApp Files,](https://www.netapp.com/us/media/tr-4780.pdf) aby uzyskać zalecenia dotyczące najlepszych rozwiązań dotyczących obsługi bazy danych Oracle w plikach NetApp platformy Azure.


## <a name="licensing-oracle-database--software-on-azure"></a>Licencjonowanie oprogramowania oracle database & na platformie Azure
Platforma Microsoft Azure jest autoryzowanym środowiskiem chmury do uruchamiania bazy danych Oracle Database. Tabela Oracle Core Factor nie ma zastosowania podczas licencjonowania baz danych Oracle w chmurze. Zamiast tego podczas korzystania z maszyn wirtualnych z technologią hyper-threading włączoną dla baz danych wersji Enterprise Edition należy zliczyć dwa procesory wirtualne jako równoważne jednej licencji procesora Oracle, jeśli hiperwątkowość jest włączona (jak określono w dokumencie zasad). Szczegóły zasad można znaleźć [tutaj](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Bazy danych Oracle zazwyczaj wymagają większej ilości pamięci i we/wy. Z tego powodu maszyny [wirtualne zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) są zalecane dla tych obciążeń. Aby jeszcze bardziej zoptymalizować obciążenia, [procesory wirtualne o ograniczonej liczbie rdzeni](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) są zalecane dla obciążeń bazy danych Oracle Database, które wymagają dużej ilości pamięci, pamięci masowej i przepustowości we/wy, ale nie są to wysokie liczby rdzeni.

Podczas migracji oprogramowania i obciążeń Oracle z lokalnego środowiska do platformy Microsoft Azure oracle zapewnia mobilność licencji zgodnie z często zadawanymi [pytaniami dotyczącymi oracle na platformie Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Cluster (Oracle RAC) został zaprojektowany w celu ograniczenia awarii pojedynczego węzła w lokalnej konfiguracji klastra z wieloma węzłami. Opiera się na dwóch technologiach lokalnych, które nie są rodzime dla środowisk chmury publicznej na dużą skalę: sieci multi-cast i współużytkowany dysk. Jeśli rozwiązanie bazy danych wymaga oracle rac na platformie Azure, potrzebujesz oprogramowania innej firmy, aby włączyć te technologie. Więcej informacji na temat programu Oracle RAC można znaleźć na [stronie FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Wysoka dostępność i zagadnienia związane z odzyskiwaniem po awarii
Podczas korzystania z baz danych Oracle na platformie Azure, jesteś odpowiedzialny za wdrożenie wysokiej dostępności i odzyskiwania po awarii rozwiązanie, aby uniknąć przestojów. 

Wysoką dostępność i odzyskiwanie po awarii dla Oracle Database Enterprise Edition (bez polegania na oracle RAC) można osiągnąć na platformie Azure za pomocą [data guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)lub Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), z dwiema bazami danych na dwóch oddzielnych maszynach wirtualnych. Obie maszyny wirtualne powinny znajdować się w tej samej [sieci wirtualnej,](https://azure.microsoft.com/documentation/services/virtual-network/) aby zapewnić sobie dostęp do siebie za pośrednictwem prywatnego trwałego adresu IP.  Ponadto zaleca się umieszczenie maszyn wirtualnych w tym samym zestawie dostępności, aby umożliwić platformie Azure umieszczanie ich w oddzielnych domenach błędów i domenach uaktualniania. Jeśli chcesz mieć nadmiarowość geograficzną, skonfiguruj dwie bazy danych do replikacji między dwoma różnymi regionami i połącz dwa wystąpienia z bramą sieci VPN.

Samouczek [Implementuj oracle data guard na platformie Azure](configure-oracle-dataguard.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.  

Dzięki oracle data guard można osiągnąć wysoką dostępność dzięki podstawowej bazie danych w jednej maszynie wirtualnej, pomocniczej (rezerwowej) bazie danych na innej maszynie wirtualnej i jednokierunkowej replikacji skonfigurowanej między nimi. Wynikiem jest dostęp do odczytu do kopii bazy danych. Za pomocą oracle GoldenGate można skonfigurować replikację dwukierunkową między dwiema bazami danych. Aby dowiedzieć się, jak skonfigurować rozwiązanie o wysokiej dostępności dla baz danych przy użyciu tych narzędzi, zobacz [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) i [Dokumentacja GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) w witrynie Oracle. Jeśli potrzebujesz dostępu do odczytu i zapisu do kopii bazy danych, możesz użyć [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Samouczek [Implementuj Oracle GoldenGate na platformie Azure](configure-oracle-golden-gate.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.

Oprócz posiadania wysokiej bazy danych i odzyskiwania po awarii rozwiązanie zaprojektowany na platformie Azure, należy mieć strategii tworzenia kopii zapasowych w celu przywrócenia bazy danych. Samouczek [Kopia zapasowa i odzyskiwanie bazy danych Oracle Database](oracle-backup-recovery.md) przeprowadzi Cię przez podstawową procedurę ustanawiania spójnej kopii zapasowej.


## <a name="support-for-jd-edwards"></a>Wsparcie dla JD Edwards
Zgodnie z Oracle Support note [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne w wersji 9.2 i nowszych są obsługiwane w **dowolnej ofercie chmury publicznej,** która spełnia ich specyficzne `Minimum Technical Requirements` (MTR).  Należy utworzyć niestandardowe obrazy, które spełniają ich specyfikacje MTR dla zgodności systemu operacyjnego i aplikacji. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Obrazy maszyn wirtualnych oracle WebLogic Server

* **Klastrowanie jest obsługiwane tylko w wersji Enterprise Edition.** Korzystanie z klastrów WebLogic jest licencjonowane tylko w przypadku korzystania z programu Enterprise Edition programu Oracle WebLogic Server. Nie należy używać klastrowania z oracle weblogic server standard edition.
* **Multiemisji UDP nie jest obsługiwany.** Platforma Azure obsługuje emisję pojedynczą UDP, ale nie multiemisji lub nadawania. Oracle WebLogic Server może polegać na możliwościach emisji pojedynczej platformy Azure UDP. Aby uzyskać najlepsze wyniki opierając się na emisji pojedynczej UDP, zaleca się, że rozmiar klastra WebLogic jest statyczny lub przechowywane z nie więcej niż 10 serwerów zarządzanych.
* **Oracle WebLogic Server oczekuje, że porty publiczne i prywatne będą takie same dla dostępu T3 (na przykład podczas korzystania z oprogramowania JavaBeans).** Rozważmy scenariusz wielowarstwowy, w którym aplikacja warstwy usług (EJB) jest uruchomiona w klastrze Oracle WebLogic Server składającym się z dwóch lub więcej maszyn wirtualnych w sieci wirtualnej o nazwie *SLWLS*. Warstwa klienta znajduje się w innej podsieci w tej samej sieci wirtualnej, z uruchomionym prostym programem Java, który próbuje wywołać EJB w warstwie usługi. Ponieważ konieczne jest zrównoważenie obciążenia warstwy usługi, należy utworzyć publiczny punkt końcowy z równoważenia obciążenia dla maszyn wirtualnych w klastrze serwera Oracle WebLogic Server. Jeśli określony port prywatny różni się od portu publicznego (na przykład 7006:7008), wystąpi następujący błąd:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Dzieje się tak, ponieważ w przypadku każdego zdalnego dostępu T3 serwer Oracle WebLogic Server oczekuje, że port modułu równoważenia obciążenia i port serwera zarządzanego WebLogic będą takie same. W poprzednim przypadku klient uzyskuje dostęp do portu 7006 (port modułu równoważenia obciążenia), a serwer zarządzany nasłuchuje na 7008 (port prywatny). To ograniczenie ma zastosowanie tylko do dostępu T3, a nie http.

   Aby uniknąć tego problemu, należy użyć jednego z następujących rozwiązań:

  * Użyj tych samych numerów portów prywatnych i publicznych dla punktów końcowych z równoważenia obciążenia dedykowanych dostępowi T3.
  * Podczas uruchamiania programu Oracle WebLogic Server należy uwzględnić następujący parametr JVM:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Informacje te można znaleźć w artykule **860340.1** bazy wiedzy o . <https://support.oracle.com>

* **Dynamiczne klastrowanie i równoważenie obciążenia.** Załóżmy, że chcesz użyć klastra dynamicznego w programie Oracle WebLogic Server i udostępnić go za pośrednictwem jednego, publicznego punktu końcowego z równoważenia obciążenia na platformie Azure. Można to zrobić tak długo, jak można użyć stałego numeru portu dla każdego z serwerów zarządzanych (nie dynamicznie przypisane z zakresu) i nie uruchomić więcej serwerów zarządzanych niż istnieją maszyny administrator jest śledzenie. Oznacza to, że nie ma więcej niż jeden serwer zarządzany na maszynę wirtualną). Jeśli konfiguracja powoduje, że uruchamia się więcej serwerów Oracle WebLogic niż maszyny wirtualne (oznacza to, że wiele wystąpień serwera Oracle WebLogic Server współużytkuje tę samą maszynę wirtualną), nie jest możliwe, aby więcej niż jedno z tych wystąpień serwerów Oracle WebLogic powiązać z danym numerem portu. Pozostałe na tej maszynie wirtualnej nie powiedzie się.

   Jeśli serwer administracyjny zostanie skonfigurowany do automatycznego przypisywania unikatowych numerów portów do serwerów zarządzanych, równoważenie obciążenia nie jest możliwe, ponieważ platforma Azure nie obsługuje mapowania z jednego portu publicznego do wielu portów prywatnych, co byłoby wymagane w tej konfiguracji.
* **Wiele wystąpień serwera Oracle WebLogic server na maszynie wirtualnej.** W zależności od wymagań wdrożenia można rozważyć uruchomienie wielu wystąpień programu Oracle WebLogic Server na tej samej maszynie wirtualnej, jeśli maszyna wirtualna jest wystarczająco duża. Na przykład na średniej wielkości maszynie wirtualnej, która zawiera dwa rdzenie, można uruchomić dwa wystąpienia Oracle WebLogic Server. Jednak nadal zaleca się, aby uniknąć wprowadzania pojedynczych punktów awarii do architektury, co byłoby w przypadku, gdy używasz tylko jednej maszyny wirtualnej, która jest uruchomiona wiele wystąpień Oracle WebLogic Server. Przy użyciu co najmniej dwóch maszyn wirtualnych może być lepsze podejście, a każda maszyna wirtualna może następnie uruchomić wiele wystąpień Oracle WebLogic Server. Każde wystąpienie programu Oracle WebLogic Server może nadal być częścią tego samego klastra. Jednak obecnie nie jest możliwe użycie platformy Azure do równoważenia obciążenia punktów końcowych, które są udostępniane przez takie wdrożenia Oracle WebLogic Server w ramach tej samej maszyny wirtualnej, ponieważ moduł równoważenia obciążenia platformy Azure wymaga, aby serwery z równoważeniem obciążenia były dystrybuowane między unikatowymi maszynami wirtualnymi.

## <a name="oracle-jdk-virtual-machine-images"></a>Obrazy maszyn wirtualnych Oracle JDK
* **JDK 6 i 7 najnowsze aktualizacje.** Chociaż zalecamy korzystanie z najnowszej publicznej, obsługiwanej wersji oprogramowania Java (obecnie Java 8), platforma Azure udostępnia również obrazy JDK 6 i 7. Jest to przeznaczone dla starszych aplikacji, które nie są jeszcze gotowe do uaktualnienia do JDK 8. Chociaż aktualizacje poprzednich obrazów JDK mogą nie być już dostępne dla ogółu społeczeństwa, biorąc pod uwagę partnerstwo firmy Microsoft z oracle, obrazy JDK 6 i 7 dostarczone przez platformę Azure mają zawierać nowszą niepubliczną aktualizację, która jest zwykle oferowana przez Oracle tylko wybranej grupie obsługiwanych klientów Oracle. Nowe wersje obrazów JDK będą udostępniane w czasie ze zaktualizowanymi wersjami JDK 6 i 7.

   Zestaw JDK dostępny w obrazach JDK 6 i 7 oraz maszyny wirtualne i obrazy z nich pochodzące mogą być używane tylko na platformie Azure.
* **64-bitowy JDK.** Obrazy maszyn wirtualnych serwera Oracle WebLogic Server i obrazy maszyn wirtualnych Oracle JDK dostarczane przez platformę Azure zawierają 64-bitowe wersje zarówno systemu Windows Server, jak i JDK.

## <a name="next-steps"></a>Następne kroki
Masz teraz przegląd aktualnych rozwiązań Oracle opartych na obrazach maszyn wirtualnych na platformie Microsoft Azure. Następnym krokiem jest wdrożenie pierwszej bazy danych Oracle na platformie Azure.

> [!div class="nextstepaction"]
> [Tworzenie bazy danych Oracle na platformie Azure](oracle-database-quick-create.md)
