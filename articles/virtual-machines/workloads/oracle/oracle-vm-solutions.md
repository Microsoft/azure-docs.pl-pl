---
title: Rozwiązania firmy Oracle na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Informacje o obsługiwanych konfiguracjach i ograniczeniach obrazów maszyn wirtualnych Oracle na Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 8bcd45ab1270d478b05b3929d7b8914976612294
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645306"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure

Ten artykuł zawiera informacje na temat rozwiązań firmy Oracle opartych na obrazach maszyn wirtualnych opublikowanych przez program Oracle w portalu Azure Marketplace. Jeśli interesujesz się rozwiązaniami aplikacji w chmurze za pomocą infrastruktury chmurowej Oracle, zobacz [rozwiązania aplikacji Oracle integrujące Microsoft Azure i infrastrukturę chmurową firmy Oracle](oracle-oci-overview.md).

Aby uzyskać listę aktualnie dostępnych obrazów, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od czerwca 2020 dostępne są następujące obrazy:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Te obrazy są uważane za "dostarczenie własnej licencji", a w związku z tym opłaty są naliczane tylko za zasoby obliczeniowe, magazynowe i sieciowe związane z uruchamianiem maszyny wirtualnej.  Przyjęto założenie, że masz licencję na korzystanie z oprogramowania Oracle i że masz bieżącą umowę pomocy technicznej na platformie Oracle. Firma Oracle gwarantuje mobilność licencji ze środowiska lokalnego na platformę Azure. Aby uzyskać szczegółowe informacje na temat mobilności licencji, zobacz opublikowane [oprogramowanie Oracle i Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) uwagi.

Użytkownicy mogą również zdecydować się na oparcie rozwiązań w obrazie niestandardowym utworzonym od podstaw na platformie Azure lub przekazaniem obrazu niestandardowego ze środowiska lokalnego.

## <a name="oracle-database-vm-images"></a>Obrazy maszyn wirtualnych bazy danych Oracle

Oprogramowanie Oracle obsługuje uruchamianie Oracle Database 12,1 i nowszych wersji Standard i Enterprise na platformie Azure w obrazach maszyn wirtualnych na podstawie Oracle Linux.  Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych Oracle Database na platformie Azure, należy pamiętać o prawidłowym rozmiarze obrazu maszyny wirtualnej i użyć SSD w warstwie Premium lub SSD w warstwie Ultra Managed Disks. Aby uzyskać instrukcje na temat szybkiego uzyskiwania Oracle Database na platformie Azure przy użyciu opublikowanego obrazu maszyny wirtualnej Oracle, [wypróbuj Oracle Database przewodnika Szybki Start](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opcje konfiguracji dołączonego dysku

Dołączone dyski korzystają z usługi Azure Blob Storage. Każdy dysk w warstwie Standardowa może teoretycznie maksymalnie wynosić około 500 operacji wejścia/wyjścia na sekundę (IOPS). Nasza oferta dysku Premium jest preferowana w przypadku obciążeń baz danych o wysokiej wydajności i może osiągać 5000 operacji we/wy na dysku. Można użyć jednego dysku, jeśli spełnia wymagania dotyczące wydajności. Można jednak zwiększyć wydajność wydajności operacji we/wy, jeśli używasz wielu dołączonych dysków, rozłożyć dane bazy danych między nimi, a następnie użyj funkcji automatycznego zarządzania magazynem (ASM) firmy Oracle. Zobacz [Omówienie automatycznego magazynu Oracle](https://www.oracle.com/technetwork/database/index-100339.html) , aby uzyskać więcej informacji dotyczących programu Oracle ASM. Przykład instalacji i konfiguracji programu Oracle ASM na maszynie wirtualnej platformy Azure z systemem Linux można znaleźć w temacie [Instalowanie i Konfigurowanie zautomatyzowanego zarządzania magazynem programu Oracle](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Opcje konfiguracji magazynu udostępnionego

Azure NetApp Files został zaprojektowany tak, aby spełniał podstawowe wymagania dotyczące uruchamiania obciążeń o wysokiej wydajności, takich jak bazy danych w chmurze, i zapewnia;

- Usługa Azure Native Shared Storage NFS do uruchamiania obciążeń Oracle przy użyciu natywnego klienta systemu plików NFS lub Oracle dNFS
- Skalowalne warstwy wydajności odzwierciedlające rzeczywisty zakres żądań IOPS
- Małe opóźnienia
- Wysoka dostępność, wysoka trwałość i łatwość zarządzania na dużą skalę, zazwyczaj zapotrzebowanie na zadania o znaczeniu krytycznym dla przedsiębiorstw (na przykład SAP i Oracle)
- Szybka i wydajna kopia zapasowa i odzyskiwanie, aby osiągnąć najbardziej agresywne umowy SLA RTO i RPO

Te możliwości są możliwe, ponieważ Azure NetApp Files jest oparty na NetApp® ONTAP® wszystkie systemy Flash działające w środowisku centrum danych platformy Azure — jako usługa natywna platformy Azure. Wynikiem jest idealna technologia magazynu bazy danych, która może zostać zainicjowana i zużyta podobnie jak w przypadku innych opcji usługi Azure Storage. Więcej informacji na temat sposobu wdrażania i uzyskiwania dostępu do Azure NetApp Files woluminów NFS znajduje się w [dokumentacji Azure NetApp Files](../../../azure-netapp-files/index.yml) . Zapoznaj się z tematem zalecenia dotyczące [najlepszych rozwiązań w programie Oracle na platformie Azure, korzystając z Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) z najlepszymi rozwiązaniami dotyczącymi obsługi bazy Azure NetApp Files danych Oracle.

## <a name="licensing-oracle-database--software-on-azure"></a>Licencjonowanie Oracle Database & oprogramowania na platformie Azure

Microsoft Azure to autoryzowane środowisko chmury do uruchamiania Oracle Database. Tabela czynników podstawowych firmy Oracle nie ma zastosowania w przypadku licencjonowania baz danych Oracle w chmurze. Zamiast tego w przypadku używania maszyn wirtualnych z włączoną technologią Hyper-Threading dla baz danych w wersji Enterprise należy liczyć dwie procesorów wirtualnych vCPU jako równoważne jednej licencji procesora Oracle, jeśli jest włączona funkcja wielowątkowości (zgodnie z opisem w dokumencie zasad). Szczegóły zasad można znaleźć [tutaj](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Bazy danych Oracle zazwyczaj wymagają wyższych ilości pamięci i operacji we/wy. Z tego powodu zaleca się używanie [maszyn wirtualnych zoptymalizowanych pod kątem pamięci](../../sizes-memory.md) dla tych obciążeń. Aby jeszcze bardziej zoptymalizować obciążenia, Oracle Database zaleca się użycie [procesorów wirtualnych vcpuych rdzeni](../../constrained-vcpu.md) , które wymagają dużej ilości pamięci, magazynu I przepustowości we/wy, ale nie dużej liczby rdzeni.

W przypadku migrowania oprogramowania i obciążeń programu Oracle ze środowiska lokalnego do Microsoft Azure firma Oracle zapewnia mobilność licencji zgodnie z opisem w artykule [Oracle na platformie Azure — często zadawane pytania](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia dotyczące wysokiej dostępności i odzyskiwania po awarii

W przypadku korzystania z baz danych Oracle na platformie Azure użytkownik jest odpowiedzialny za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii w celu uniknięcia wszelkich przestojów.

Wysoką dostępność i odzyskiwanie po awarii dla Oracle Database Enterprise Edition (bez polegania na programie Oracle RAC) można uzyskać na platformie Azure przy użyciu funkcji [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)lub [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)z dwiema bazami danych na dwóch oddzielnych maszynach wirtualnych. Obie maszyny wirtualne powinny znajdować się w tej samej [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) , aby zapewnić, że będą mogły uzyskać do nich dostęp za pośrednictwem prywatnego trwałego adresu IP.  Ponadto Zalecamy umieszczenie maszyn wirtualnych w tym samym zestawie dostępności, aby umożliwić platformie Azure umieszczenie ich w osobnych domenach błędów i domenach uaktualnienia. Jeśli chcesz mieć nadmiarowość geograficzną, skonfiguruj dwie bazy danych do replikowania między dwoma różnymi regionami i Połącz te dwa wystąpienia z VPN Gateway.

Samouczek [implementujący funkcję Oracle Data Guard na platformie Azure](configure-oracle-dataguard.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.  

Przy użyciu funkcji Oracle Data Guard można uzyskać wysoką dostępność przy użyciu podstawowej bazy danych na jednej maszynie wirtualnej, dodatkowej (w stanie gotowości) w innej maszynie wirtualnej i replikacji jednokierunkowej. Wynikiem jest dostęp do odczytu do kopii bazy danych programu. Za pomocą programu Oracle GoldenGate można skonfigurować dwukierunkową replikację między dwiema bazami danych. Aby dowiedzieć się, jak skonfigurować rozwiązanie wysokiej dostępności dla baz danych przy użyciu tych narzędzi, zobacz artykuł [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) i dokumentacja [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) w witrynie Oracle. Jeśli potrzebujesz dostępu do odczytu i zapisu do kopii bazy danych programu, możesz użyć usługi [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Samouczek [implementacji programu Oracle GoldenGate na platformie Azure](configure-oracle-golden-gate.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.

Oprócz posiadania rozwiązań o wysokiej dostępności i rozwiązaniu DR na platformie Azure należy utworzyć strategię tworzenia kopii zapasowych w celu przywrócenia bazy danych. W samouczku [Tworzenie kopii zapasowej i odzyskiwanie Oracle Database](./oracle-overview.md) przeprowadzi Cię przez podstawową procedurę tworzenia spójnej kopii zapasowej.

## <a name="support-for-jd-edwards"></a>Obsługa JD Edwards

Zgodnie z pomocą techniczną firmy Oracle [Identyfikator Doc 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne wersje 9,2 i nowsze są obsługiwane we **wszystkich ofertach chmury publicznej** , które spełniają ich specyficzne `Minimum Technical Requirements` (MTR).  Należy utworzyć niestandardowe obrazy, które spełniają specyfikacje MTR dla zgodności aplikacji systemu operacyjnego i oprogramowania.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oferty maszyn wirtualnych z programem Oracle WebLogic Server

Firmy Oracle i Microsoft współpracują w celu przełączenia serwera WebLogic do portalu Azure Marketplace w postaci kolekcji ofert aplikacji platformy Azure.  Te oferty są opisane w artykule [Oracle WebWebLogicc Server aplikacje platformy Azure](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Obrazy maszyn wirtualnych z programem Oracle WebLogic Server

- **Klastrowanie jest obsługiwane tylko w wersji Enterprise.** Masz licencję na korzystanie z klastrowania WebLogic tylko w przypadku korzystania z serwera Oracle WebLogic Server w wersji Enterprise. Nie należy używać klastrowania z Oracle WebLogic Server Standard Edition.
- **Multiemisja UDP nie jest obsługiwana.** Platforma Azure obsługuje emisję pojedynczą UDP, ale nie multiemisję ani rozgłaszanie. Serwer Oracle WebLogic Server może polegać na możliwościach emisji pojedynczej protokołu UDP platformy Azure. W celu uzyskania najlepszych wyników polegających na emisji pojedynczej UDP zaleca się, aby rozmiar klastra WebLogic był przechowywany statyczny lub przechowywany bez więcej niż 10 serwerów zarządzanych.
- **Serwer Oracle WebLogic oczekuje, że porty publiczne i prywatne mają być takie same dla dostępu T3 (na przykład w przypadku korzystania z usługi Enterprise JavaBeans).** Rozważmy scenariusz wielowarstwowy, w którym aplikacja warstwy usług (EJB) jest uruchomiona w klastrze programu Oracle WebLogic Server składającym się z co najmniej dwóch maszyn wirtualnych w sieci wirtualnej o nazwie *SLWLS*. Warstwa klienta znajduje się w innej podsieci w tej samej sieci wirtualnej, uruchamiając prosty program języka Java próbujący wywołać EJB w warstwie usług. Ponieważ konieczne jest równoważenie obciążenia warstwy usług, należy utworzyć publiczny punkt końcowy ze zrównoważonym obciążeniem dla maszyn wirtualnych w klastrze serwera Oracle WebLogic. Jeśli określony port prywatny różni się od portu publicznego (na przykład 7006:7008), wystąpi błąd, taki jak następujące:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Jest to spowodowane tym, że dla każdego zdalnego dostępu T3 serwer Oracle WebLogic oczekuje, że port usługi równoważenia obciążenia i port serwera zarządzanego WebLogic są takie same. W poprzednim przypadku klient uzyskuje dostęp do portu 7006 (Port usługi równoważenia obciążenia), a serwer zarządzany nasłuchuje na 7008 (port prywatny). To ograniczenie dotyczy tylko dostępu T3, a nie protokołu HTTP.

   Aby uniknąć tego problemu, użyj jednego z następujących obejść:

- Użyj tych samych numerów portów prywatnych i publicznych dla punktów końcowych z równoważeniem obciążenia, które są przeznaczone dla dostępu T3.
- Podczas uruchamiania serwera Oracle WebLogic należy uwzględnić następujący parametr JVM:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Aby uzyskać powiązane informacje, zobacz artykuł **860340,1** w bazie wiedzy o <https://support.oracle.com> .

- **Ograniczenia dynamicznego klastrowania i równoważenia obciążenia.** Załóżmy, że chcesz użyć dynamicznego klastra na serwerze Oracle WebLogic i uwidocznić go za pomocą jednego publicznego punktu końcowego ze zrównoważonym obciążeniem na platformie Azure. Można to zrobić tak długo, jak używasz stałego numeru portu dla każdego z serwerów zarządzanych (nie jest to przypisywany dynamicznie z zakresu) i nie uruchamiaj więcej zarządzanych serwerów niż maszyny, które są śledzone przez administratora. Oznacza to, że nie ma więcej niż jednego serwera zarządzanego na maszynę wirtualną. Jeśli konfiguracja powoduje, że są uruchamiane więcej serwerów Oracle WebLogic niż maszyny wirtualne (w przypadku wielu wystąpień serwera Oracle WebLogic, które współużytkują tę samą maszynę wirtualną), nie jest możliwe, aby więcej niż jedno wystąpienie serwerów Oracle WebLogic było powiązane z danym numerem portu. Inne osoby na tej maszynie wirtualnej zakończą się niepowodzeniem.

   Jeśli skonfigurujesz serwer administracyjny do automatycznego przypisywania unikatowych numerów portów do serwerów zarządzanych, równoważenie obciążenia nie jest możliwe, ponieważ platforma Azure nie obsługuje mapowania z jednego portu publicznego na wiele portów prywatnych, co byłoby wymagane dla tej konfiguracji.
- **Wiele wystąpień serwera Oracle WebLogic na maszynie wirtualnej.** W zależności od wymagań wdrożenia można rozważyć uruchomienie wielu wystąpień serwera Oracle WebLogic na tej samej maszynie wirtualnej, jeśli maszyna wirtualna jest wystarczająco duża. Na przykład w przypadku maszyny wirtualnej o średnim średnim, która zawiera dwa rdzenie, można uruchomić dwa wystąpienia programu Oracle webWebLogicc Server. Jednak nadal zalecamy uniknięcie wprowadzenia pojedynczych punktów awarii do architektury, w przypadku gdy używana jest tylko jedna maszyna wirtualna z wieloma wystąpieniami programu Oracle webWebLogicc Server. Korzystanie z co najmniej dwóch maszyn wirtualnych może być lepszym rozwiązaniem, a każda maszyna wirtualna może uruchamiać wiele wystąpień serwera Oracle webWebLogicc Server. Każde wystąpienie serwera Oracle WebLogic może być częścią tego samego klastra. Obecnie nie jest możliwe używanie platformy Azure do równoważenia obciążenia punktów końcowych, które są udostępniane przez takie wdrożenia serwera Oracle WebLogic w ramach tej samej maszyny wirtualnej, ponieważ moduł równoważenia obciążenia platformy Azure wymaga dystrybuowania serwerów z równoważeniem obciążenia między unikatowymi maszynami wirtualnymi.

## <a name="oracle-jdk-virtual-machine-images"></a>Obrazy maszyn wirtualnych Oracle JDK

- **JDK 6 i 7 najnowszych aktualizacji.** Chociaż zalecamy korzystanie z najnowszej publicznej, obsługiwanej wersji języka Java (obecnie Java 8), platforma Azure udostępnia również obrazy z JDK 6 i 7. Jest to przeznaczone dla starszych aplikacji, które nie są jeszcze gotowe do uaktualnienia do JDK 8. Mimo że aktualizacje wcześniejszych JDK obrazów mogą nie być już dostępne publicznie, biorąc pod partnerstwo firmy Microsoft z firmą Oracle, obrazy JDK 6 i 7 udostępniane przez platformę Azure mają zawierać nowszą, niepubliczną aktualizację, która zwykle jest oferowana przez firmę Oracle tylko do wybranej grupy obsługiwanych klientów firmy Oracle. Nowe wersje obrazów JDK będą udostępniane w czasie z zaktualizowanymi wersjami JDK 6 i 7.

   JDK dostępne w obrazach JDK 6 i 7, a maszyny wirtualne i obrazy pochodzące od nich mogą być używane tylko na platformie Azure.
- **64-bitowy JDK.** Obrazy maszyn wirtualnych programu Oracle WebLogic Server i obrazy maszyn wirtualnych Oracle JDK udostępniane przez platformę Azure zawierają 64-bitowe wersje systemów Windows Server i JDK.

## <a name="next-steps"></a>Następne kroki

Teraz masz przegląd bieżących rozwiązań firmy Oracle opartych na obrazach maszyn wirtualnych w Microsoft Azure. Następnym krokiem jest wdrożenie pierwszej bazy danych Oracle Database na platformie Azure.

> [!div class="nextstepaction"]
> [Tworzenie bazy danych Oracle na platformie Azure](oracle-database-quick-create.md)
