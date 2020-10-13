---
title: Często zadawane pytania dotyczące wydajności protokołu SMB dla Azure NetApp Files | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące wydajności protokołu SMB dla Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 6a7bf07359344e26280021a6a55eecc5b96b7a86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653693"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Często zadawane pytania dotyczące wydajności protokołu SMB dla Azure NetApp Files

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące najlepszych rozwiązań dotyczących wydajności protokołu SMB dla Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Czy włączono Wielokanałowość protokołu SMB w udziałach SMB? 

Tak, funkcja bezobsługowego protokołu SMB jest domyślnie włączona — zmiany wprowadzane są na początku stycznia 2020. Wszystkie udziały SMB Datowanie istniejące woluminy SMB mają włączoną funkcję, a wszystkie nowo utworzone woluminy również będą miały włączoną funkcję w czasie tworzenia. 

Wszystkie połączenia SMB ustanowione przed włączeniem funkcji należy zresetować, aby korzystać z funkcji wielokanałowości SMB. Aby zresetować, można odłączyć i ponownie połączyć udział SMB.

## <a name="is-rss-supported"></a>Czy funkcja RSS jest obsługiwana?

Tak, Azure NetApp Files obsługuje skalowanie po stronie odbierającej (RSS).

W przypadku włączenia obsługi wielokanałowego protokołu SMB klient protokołu SMB3 nawiązuje wiele połączeń TCP z serwerem SMB Azure NetApp Files za pośrednictwem karty interfejsu sieciowego (NIC) z obsługą pojedynczej funkcji RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Które wersje systemu Windows obsługują Wielokanałowość SMB?

System Windows obsługuje Wielokanałowość protokołu SMB od systemu Windows 2012 w celu zapewnienia najlepszej wydajności.  Aby uzyskać szczegółowe informacje, zobacz temat [wdrażanie wielokanałowości SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) i [podstawy protokołu SMB](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Czy moja maszyna wirtualna platformy Azure obsługuje funkcję RSS?

Aby sprawdzić, czy karty sieciowe maszyn wirtualnych platformy Azure obsługują funkcję RSS, uruchom polecenie `Get-SmbClientNetworkInterface` w następujący sposób i zaznacz pole `RSS Capable` : 

![Zrzut ekranu przedstawiający dane wyjściowe RSS dla maszyny wirtualnej platformy Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Czy Azure NetApp Files obsługuje protokół SMB Direct?

Nie, Azure NetApp Files nie obsługuje protokołu SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Co to jest korzyść protokołu SMB wielokanałowe? 

Funkcja wielokanałowego protokołu SMB umożliwia klientowi protokołu SMB3 ustanawianie puli połączeń za pośrednictwem jednej karty interfejsu sieciowego (NIC) lub wielu kart sieciowych i używanie ich do wysyłania żądań dla jednej sesji SMB. W przeciwieństwie do projektu, protokół SMB1 i SMB2 wymagają, aby klient ustanowił jedno połączenie i wysłał cały ruch SMB dla danej sesji za pośrednictwem tego połączenia. To pojedyncze połączenie ogranicza ogólną wydajność protokołu, którą można osiągnąć z jednego klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Czy należy skonfigurować wiele kart sieciowych na moim kliencie dla protokołu SMB?

Nie. Klient SMB będzie pasował do liczby kart sieciowych zwracanych przez serwer SMB.  Każdy wolumin magazynu jest dostępny z jednego i tylko jednego punktu końcowego magazynu.  Oznacza to, że tylko jedna karta sieciowa będzie używana dla danej relacji protokołu SMB.  

Jak `Get-SmbClientNetworkInterace` widać na poniższej ilustracji, maszyna wirtualna ma 2 interfejsy sieciowe — 15 i 12.  Jak pokazano w poniższym poleceniu `Get-SmbMultichannelConnection` , chociaż istnieją dwie karty sieciowe z obsługą funkcji RSS, w połączeniu z udziałem SMB jest używany tylko interfejs 12, a interfejs 15 nie jest używany.

![Screeshot pokazujący dane wyjściowe dla kart sieciowych z obsługą funkcji RSS.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Czy tworzenie zespołu kart interfejsu sieciowego jest obsługiwane na platformie Azure?

Tworzenie zespołu kart interfejsu sieciowego nie jest obsługiwane na platformie Azure. Chociaż wiele interfejsów sieciowych jest obsługiwanych w usłudze Azure Virtual Machines, reprezentują one logiczną, a nie fizyczną konstrukcję. W związku z tym nie zapewniają odporności na uszkodzenia.  Ponadto przepustowość dostępna dla maszyny wirtualnej platformy Azure jest obliczana dla samego komputera, a nie dla poszczególnych interfejsów sieciowych.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaka jest wydajność dla wielokanałowego protokołu SMB?

Poniższe testy i wykresy przedstawiają moc wielokanałowe protokołu SMB w przypadku obciążeń z jednym wystąpieniem.

### <a name="random-io"></a>Losowe we/wy  

W przypadku korzystania z funkcji wielokanałowości SMB wyłączonej na kliencie czyste KiB testy odczytu i zapisu zostały wykonane przy użyciu FIO i zestawu roboczego GiB 40.  Udział SMB został odłączony między każdym testem, z przyrostami liczby połączeń klienta SMB na ustawienia interfejsu sieciowego RSS,,, `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Testy pokazują, że ustawienie domyślne `4` jest wystarczające dla obciążeń intensywnie korzystających z operacji we/wy, zwiększając do `8` i `16` ma znaczący efekt. 

Polecenie `netstat -na | findstr 445` udowodniono, że nastąpiło ustanowienie dodatkowych połączeń z przyrostami z `1` do `4` `8` i do `16` .  Cztery rdzenie procesora CPU zostały w pełni wykorzystane do SMB podczas każdego testu, zgodnie z potwierdzeniem przez `Per Processor Network Activity Cycles` statystykę monitora wydajności (nieuwzględnioną w tym artykule).

![Wykres przedstawiający losowe porównanie operacji we/wy protokołu SMB.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Maszyna wirtualna platformy Azure nie ma wpływu na limity operacji we/wy magazynu SMB (ani NFS).  Jak pokazano na poniższym wykresie, typ wystąpienia D32ds ma ograniczoną 308 000 dla operacji wejścia/wyjścia magazynu w pamięci podręcznej i 51 200 dla operacji we/wy na sekundę.  Jednak wykres powyżej pokazuje znacznie więcej operacji we/wy za pośrednictwem protokołu SMB.

![Wykres pokazujący losowy test porównawczy we/wy.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekwencyjne operacje we/wy 

Testy podobne do losowych testów operacji we/wy opisanych wcześniej zostały wykonane z użyciem sekwencyjnych operacji we/wy 64 KiB. Mimo że zwiększenie liczby połączeń klientów na interfejs sieciowy RSS poza 4 "nie miało zauważalnego wpływu na losowe we/wy, to samo nie dotyczy sekwencyjnych operacji we/wy. Jak widać na poniższym wykresie, każdy wzrost jest związany z odpowiednim wzrostem przepływności odczytu. Przepustowość zapisu była płaska ze względu na ograniczenia przepustowości sieci na platformie Azure dla każdego typu/rozmiaru wystąpienia. 

![Wykres przedstawiający porównanie testu przepływności.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Platforma Azure umieszcza limity szybkości sieci na każdym typie/rozmiarze maszyny wirtualnej. Limit szybkości jest nakładany tylko na ruch wychodzący. Liczba kart sieciowych znajdujących się na maszynie wirtualnej nie ma wpływu na łączną przepustowość dostępną dla maszyny.  Na przykład typ wystąpienia D32ds ma nałożoną granicę sieci wynoszącą 16 000 MB/s (2 000 MiB na sekundę).  Jak pokazano na wykresie sekwencyjnym, limit ma wpływ na ruch wychodzący (zapis), ale nie do odczytu wielokanałowego.

![Wykres pokazujący sekwencyjny test operacji wejścia/wyjścia.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Jaka wydajność jest oczekiwana z pojedynczym wystąpieniem z zestawem danych o pojemności 1 TB?

Aby zapewnić bardziej szczegółowy wgląd w obciążenia przy użyciu miksów odczytu i zapisu, następujące dwa wykresy przedstawiają wydajność jednego, Ultra-poziomego woluminu w chmurze wynoszącego 50 TB z zestawem danych 1 TB i ze wielokanałowością SMB 4. Użyto optymalnych IODepth 16 i elastycznych parametrów we/wy (FIO) w celu zapewnienia pełnego użycia przepustowości sieci ( `numjobs=16` ).

Na poniższym wykresie przedstawiono wyniki dla losowych operacji we/wy z jednym wystąpieniem maszyny wirtualnej i mieszanie odczytu/zapisu w 10% interwałach:

![Wykres przedstawiający losowy test operacji we/wy w systemie Windows 2019 w warstwie _D32ds_v4 standardowa.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Poniższy wykres przedstawia wyniki dla sekwencyjnych operacji we/wy:

![Wykres przedstawiający standardową _D32ds_v4 systemu Windows 2019 64 KB przepływności.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Jaka wydajność jest oczekiwana podczas skalowania w poziomie przy użyciu 5 maszyn wirtualnych z zestawem danych o pojemności 1 TB?

Te testy z 5 maszyn wirtualnych używają tego samego środowiska testowego co pojedyncze maszyny wirtualne, a każdy proces zapisuje je w osobnym pliku.

Poniższy wykres przedstawia wyniki losowych operacji we/wy:

![Wykres przedstawiający system Windows 2019 w warstwie Standardowa _D32ds_v4 4 k wystąpienia randio test we/wy.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Poniższy wykres przedstawia wyniki dla sekwencyjnych operacji we/wy:

![Wykres przedstawiający system Windows 2019 w warstwie _D32ds_v4 standardowa 64 KB 5-wystąpienia sekwencyjnej przepływności.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Jak monitorować karty Ethernet funkcji Hyper-V i upewnić się, że pojemność sieci jest maksymalizuje?  

Należy ustawić jedną strategię używaną w testowaniu z FIO `numjobs=16` . Dzięki temu każde zadanie jest wykonywane w 16 określonych wystąpieniach w celu zmaksymalizowania Microsoft Hyper-V karcie sieciowej.

Aby sprawdzić działanie na poszczególnych kartach w Monitorze wydajności systemu Windows, wybierz pozycję **Monitor wydajności > Dodaj liczniki > interfejs sieciowy > Microsoft Hyper-V karcie sieciowej**.

![Zrzut ekranu przedstawiający interfejs Add Counter monitora wydajności.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Po uruchomieniu ruchu danych w woluminach można monitorować karty w Monitorze wydajności systemu Windows. Jeśli nie korzystasz ze wszystkich tych 16 kart wirtualnych, możesz nie zmaksymalizować wydajności przepustowości sieci.

![Zrzut ekranu przedstawiający dane wyjściowe monitora wydajności.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Czy zalecane jest przyspieszenie sieci?

W celu uzyskania maksymalnej wydajności zaleca się skonfigurowanie [przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) , jeśli jest to możliwe. Należy pamiętać o następujących kwestiach:  

* Azure Portal domyślnie włącza przyspieszone sieci dla maszyn wirtualnych obsługujących tę funkcję.  Jednak inne metody wdrażania, takie jak rozwiązania ansible i podobne narzędzia do konfiguracji, mogą nie być obsługiwane.  Niepowodzenie włączenia przyspieszonej sieci może hobble wydajność maszyny.  
* Jeśli przyspieszona sieć nie jest włączona w interfejsie sieciowym maszyny wirtualnej z powodu braku obsługi dla typu lub rozmiaru wystąpienia, pozostanie wyłączone z większymi typami wystąpień. W tych przypadkach będzie potrzebna ręczna interwencja.

## <a name="are-jumbo-frames-supported"></a>Czy są obsługiwane duże ramki?

Duże ramki nie są obsługiwane w przypadku maszyn wirtualnych platformy Azure.

## <a name="is-smb-signing-supported"></a>Czy podpisywanie SMB jest obsługiwane? 

Protokół SMB stanowi podstawę udostępniania plików i drukarek oraz innych operacji sieciowych, takich jak Zdalna administracja systemu Windows. Aby zapobiegać atakom typu man-in-the-middle, które modyfikują pakiety SMB podczas przesyłania, protokół SMB obsługuje cyfrowe podpisywanie pakietów SMB. 

Podpisywanie SMB jest obsługiwane przez wszystkie wersje protokołu SMB obsługiwane przez Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaki jest wpływ podpisywania SMB na wydajność?  

Podpisywanie SMB ma szkodliwy wpływ na wydajność protokołu SMB. Między innymi potencjalnymi przyczynami obniżenia wydajności, podpisywanie cyfrowe każdego pakietu zużywa dodatkowy procesor CPU po stronie klienta, jak pokazano poniżej. W takim przypadku w przypadku protokołu SMB jest wyświetlana wartość podstawowa 0, w tym podpisywanie SMB.  Porównanie z wielokanałowymi numerami przepływności odczytu w poprzedniej sekcji pokazuje, że podpisywanie SMB zmniejsza ogólną przepływność od 875MiB do około 250MiB/s. 

![Wykres pokazujący wpływ na wydajność podpisywania protokołu SMB.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Następne kroki  

- [Często zadawane pytania dotyczące Azure NetApp Files](azure-netapp-files-faqs.md)
- Zobacz [Azure NetApp Files: zarządzane udziały plików w przedsiębiorstwie dla obciążeń SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) o korzystaniu z udziałów plików smb z Azure NetApp Files.
