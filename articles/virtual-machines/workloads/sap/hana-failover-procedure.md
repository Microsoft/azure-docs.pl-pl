---
title: Procedura trybu failover platformy HANA w witrynie odzyskiwania po awarii dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jak przeprowadzić pracę w trybie failover w lokacji odzyskiwania po awarii dla SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6454903a7c37da30e317e29c126109b39b14efbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83660581"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura przechodzenia w tryb failover odzyskiwania po awarii


>[!IMPORTANT]
>Ten artykuł nie zastępuje dokumentacji dotyczącej administracji SAP HANA ani informacji o oprogramowaniu SAP. Oczekujemy, że masz pełne zrozumienie i doświadczenie w SAP HANA administrowania i działania, szczególnie w przypadku kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tym artykule przedstawiono zrzuty ekranu z programu SAP HANA Studio. Zawartość, struktura i charakter ekranów narzędzi administracyjnych SAP i samych narzędzi mogą ulec zmianie z wersji SAP HANA na Release.

Istnieją dwa przypadki, które należy wziąć pod uwagę w przypadku przełączenia w tryb failover do lokacji DR:

- Do uzyskania najnowszego stanu danych potrzebna jest baza danych SAP HANA. W takim przypadku istnieje skrypt samoobsługowy, w którym można przeprowadzić pracę w trybie failover bez konieczności kontaktowania się z firmą Microsoft. W przypadku powrotu po awarii należy współpracować z firmą Microsoft.
- Chcesz przywrócić migawkę magazynu, która nie jest najnowszą replikowaną migawką. W takim przypadku należy współpracować z firmą Microsoft. 

>[!NOTE]
>Poniższe kroki należy wykonać w jednostce dużego wystąpienia HANA, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze zreplikowane migawki magazynu, wykonaj kroki opisane w sekcji "wykonywanie pełnego trybu failover odzyskiwania po awarii — azure_hana_dr_failover" w [narzędziu Microsoft Snapshot Tools dla SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Jeśli chcesz, aby wiele wystąpień SAP HANA przekroczyć do trybu failover, uruchom polecenie azure_hana_dr_failover kilka razy. Gdy jest to wymagane, wprowadź SAP HANA identyfikator SID, który chcesz przełączyć i przywrócić. 


Można testować tryb failover odzyskiwania po awarii bez wywierania wpływu na rzeczywistą relację replikacji. Aby przeprowadzić test pracy w trybie failover, wykonaj kroki opisane w sekcji "Przeprowadź test pracy w trybie failover DR azure_hana_test_dr_failover" w [narzędziu Microsoft Snapshot Tools dla SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>*Nie* należy uruchamiać żadnych transakcji produkcyjnych w wystąpieniu, które zostało utworzone w witrynie odzyskiwania po awarii, za pomocą procesu **testowania trybu failover**. Polecenie azure_hana_test_dr_failover tworzy zestaw woluminów, które nie mają relacji z lokacją główną. W związku z tym synchronizacja z powrotem do lokacji głównej *nie* jest możliwa. 

Jeśli chcesz mieć wiele wystąpień SAP HANA do przetestowania, uruchom skrypt kilka razy. Gdy jest to wymagane, wprowadź SAP HANA identyfikator SID wystąpienia, które ma zostać przetestowane w celu przełączenia w tryb failover. 

>[!NOTE]
>Jeśli konieczne jest przełączenie w tryb failover do lokacji odzyskiwania po awarii w celu zajęcia pewnych danych, które zostały usunięte godz. 

1. Zamknij wystąpienie nieprodukcyjne platformy HANA w jednostce odzyskiwania po awarii w dużych wystąpieniach platformy HANA, które są uruchomione. Jest preinstalowane wystąpienie produkcyjne uśpione HANA.
1. Upewnij się, że żadne procesy SAP HANA nie są uruchomione. Dla tej kontroli użyj następującego polecenia:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      W danych wyjściowych powinien być widoczny proces **hdbdaemon** w stanie zatrzymania i nie ma żadnych innych procesów Hana w stanie uruchomionym lub uruchomionym.
1. Określ nazwę migawki lub SAP HANA identyfikator kopii zapasowej, do której ma zostać przywrócona lokacja odzyskiwania po awarii. W przypadku rzeczywistych przypadków odzyskiwania po awarii ta migawka jest zwykle najnowszą migawką. Jeśli musisz odzyskać utracone dane, wybierz wcześniejszą migawkę.
1. Skontaktuj się z pomocą techniczną platformy Azure w ramach żądania wsparcia o wysokim priorytecie. Zażądaj przywrócenia tej migawki przy użyciu nazwy i daty migawki lub identyfikatora kopii zapasowej HANA w witrynie odzyskiwania po awarii. Domyślnie strona operacje przywraca tylko wolumin/Hana/Data. Jeśli chcesz, aby/Hana/logbackups woluminy, należy zastanowić się, że. *Nie przywracaj woluminu/Hana/Shared.* Zamiast tego należy wybrać określone pliki, takie jak global.ini poza katalogiem **. snapshot** i jego podkatalogach, po ponownym zainstalowaniu woluminu/Hana/Shared dla PRD. 

   Na stronie operacje wykonywane są następujące czynności:

   a. Replikacja migawek z woluminu produkcyjnego do woluminów odzyskiwania po awarii jest zatrzymana. Takie zakłócenia mogły już wystąpić, jeśli awaria w lokacji produkcyjnej to powód, dla którego należy wykonać procedurę odzyskiwania po awarii.
   
   b. Nazwa migawki magazynu lub migawka z wybranym IDENTYFIKATORem kopii zapasowej zostanie przywrócona na woluminach odzyskiwania po awarii.
   
   c. Po przywróceniu woluminy odzyskiwania po awarii są dostępne do zamontowania w jednostkach dużego wystąpienia HANA w regionie odzyskiwania po awarii.
      
1. Zainstaluj woluminy odzyskiwania po awarii w jednostce dużego wystąpienia HANA w lokacji odzyskiwania po awarii. 
1. Uruchom wystąpienie produkcyjne SAP HANA uśpione.
1. W przypadku wybrania opcji kopiowania dzienników kopii zapasowych dziennika transakcji w celu skrócenia czasu RPO należy scalić kopie zapasowe dziennika transakcji z nowo zainstalowanym katalogiem DR/Hana/logbackups. Nie zastępuj istniejących kopii zapasowych. Skopiuj nowsze kopie zapasowe, które nie zostały zreplikowane z najnowszą replikacją migawki magazynu.
1. Można również przywrócić pojedyncze pliki z migawek, które nie zostały zreplikowane do woluminu/hana/shared/PRD w regionie odzyskiwania po awarii.

Poniższe kroki pokazują, jak odzyskać wystąpienie produkcyjne SAP HANA na podstawie przywróconej migawki magazynu i kopii zapasowych dziennika transakcji, które są dostępne.

1. Zmień lokalizację kopii zapasowej na **/Hana/logbackups** za pomocą programu SAP HANA Studio.

   ![Zmień lokalizację kopii zapasowej odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skanów przez lokalizacje plików kopii zapasowej i sugeruje ostatnią kopię zapasową dziennika transakcji do przywrócenia. Skanowanie może potrwać kilka minut, aż zostanie wyświetlony ekran podobny do następującego:

   ![Lista kopii zapasowych dziennika transakcji na potrzeby odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Dostosuj niektóre ustawienia domyślne:

      - Wyczyść pole wyboru **Użyj różnicowych kopii zapasowych**.
      - Wybierz pozycję **Inicjuj obszar dziennika**.

   ![Ustaw obszar dziennika inicjowania](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wybierz pozycję **Zakończ**.

   ![Kończenie przywracania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinien pojawić się okno postępu, jak pokazano tutaj. Należy pamiętać, że przykładem jest przywracanie odzyskiwania po awarii w konfiguracji SAP HANA skalowalnego w poziomie z trzech węzłów.

![Postęp przywracania](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Jeśli przywracanie przestanie odpowiadać na ekranie **zakończenia** i nie pokazuje ekranu postępu, upewnij się, że wszystkie wystąpienia SAP HANA w węzłach procesu roboczego są uruchomione. W razie potrzeby Uruchom wystąpienia SAP HANA ręcznie.


## <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z programu DR do lokacji produkcyjnej
Powrót po awarii z programu DR do lokacji produkcyjnej. Przyjrzyjmy się scenariuszowi, w którym praca w trybie failover w lokacji odzyskiwania po awarii została spowodowana przez problemy w regionie produkcyjnej platformy Azure, a nie przez potrzeby odzyskiwania utraconych danych. 

Używasz obciążenia produkcyjnego SAP przez pewien czas w lokacji odzyskiwania po awarii. W miarę rozwiązywania problemów z witryną produkcyjną należy powrócić po awarii do lokacji produkcyjnej. Ze względu na to, że nie można utracić danych, krok z powrotem do lokacji produkcyjnej obejmuje kilka kroków i blisko współpracy z SAP HANA w zespole operacji platformy Azure. Istnieje możliwość wyzwolenia zespołu operacji w celu rozpoczęcia synchronizacji z powrotem do lokacji produkcyjnej po rozwiązaniu problemów.

Wykonaj następujące kroki:

1. SAP HANA w zespole operacji platformy Azure pobiera wyzwalacz, aby synchronizować woluminy magazynów produkcyjnych z woluminów magazynu odzyskiwania po awarii, co teraz reprezentuje stan produkcji. W tym stanie jednostka dużej instancji HANA w lokacji produkcyjnej jest wyłączona.
1. SAP HANA w usłudze Azure Operations Manager monitoruje replikację i upewni się, że jest ona przechwycona przed zainformowaniem użytkownika.
1. Należy zamknąć aplikacje, które korzystają z wystąpienia produkcyjnego HANA w lokacji odzyskiwania po awarii. Następnie należy wykonać kopię zapasową dziennika transakcji platformy HANA. Następnie Zatrzymaj wystąpienie HANA uruchomione w jednostkach dużych wystąpień platformy HANA w lokacji odzyskiwania po awarii.
1. Po wyłączeniu wystąpienia programu HANA działającego w jednostce dużego wystąpienia HANA w lokacji odzyskiwania po awarii zespół operacyjny ręcznie zsynchronizuje woluminy dysków ponownie.
1. SAP HANA w zespole operacji platformy Azure ponownie uruchamia jednostkę dużego wystąpienia HANA w lokacji produkcyjnej. Odstają się one do Ciebie. Upewnij się, że wystąpienie SAP HANA jest w stanie zamknięcia w czasie uruchamiania jednostki dużego wystąpienia platformy HANA.
1. Wykonujesz te same kroki przywracania bazy danych, które zakończyły się wcześniej w trybie failover w lokacji odzyskiwania po awarii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Aby monitorować stan postępu replikacji magazynu, uruchom skrypt `azure_hana_replication_status` . To polecenie musi zostać uruchomione z jednostki, która działa w lokalizacji odzyskiwania po awarii, aby działać zgodnie z oczekiwaniami. Polecenie działa niezależnie od tego, czy replikacja jest aktywna. Polecenie można uruchomić dla każdej jednostki usługi HANA o dużej instancji w ramach dzierżawy w lokalizacji odzyskiwania po awarii. Nie można jej użyć do uzyskania szczegółowych informacji o woluminie rozruchowym. 

Aby uzyskać więcej informacji na temat polecenia i jego danych wyjściowych, zobacz "Pobierz stan replikacji DR-azure_hana_replication_status" w [narzędziu Microsoft Snapshot Tools for SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Następne kroki
- Zobacz [monitorowanie i rozwiązywanie problemów po stronie platformy Hana](hana-monitor-troubleshoot.md).
