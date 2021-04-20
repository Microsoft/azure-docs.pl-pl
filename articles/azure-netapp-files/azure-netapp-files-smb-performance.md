---
title: Często zadawane pytania dotyczące wydajności SMB dla Azure NetApp Files| Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące wydajności SMB dla Azure NetApp Files.
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
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726722"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Często zadawane pytania dotyczące wydajności SMB dla Azure NetApp Files

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące najlepszych rozwiązań dotyczących wydajności SMB dla Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Czy funkcja SMB Multichannel jest włączona w udziałach SMB? 

Tak, funkcja SMB Multichannel jest domyślnie włączona. Zmiana ta zostanie włączona na początku stycznia 2020 r. Wszystkie udziały SMB mają włączoną funkcję wcześniej istniejących woluminów SMB, a wszystkie nowo utworzone woluminy również będą miały włączoną funkcję w czasie tworzenia. 

Każde połączenie SMB nawiązane przed włączeniem funkcji należy zresetować, aby skorzystać z funkcji SMB Multichannel. Aby zresetować, możesz rozłączyć i ponownie połączyć udział SMB.

## <a name="is-rss-supported"></a>Czy funkcja RSS jest obsługiwana?

Tak, Azure NetApp Files obsługuje skalowanie po stronie odbierających (RSS).

Po włączeniu funkcji SMB Multichannel klient SMB3 ustanawia wiele połączeń TCP z serwerem SMB Azure NetApp Files za pośrednictwem karty sieciowej (NIC), która jest w stanie obsługi jednego źródła danych RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Które wersje systemu Windows obsługują usługę SMB Multichannel?

System Windows obsługuje funkcję SMB Multichannel od czasu systemu Windows 2012 w celu umożliwienia najlepszej wydajności.  Szczegółowe informacje można znaleźć w tematach Deploy SMB Multichannel (Wdrażanie funkcji [SMB Multichannel)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) i [The basics of SMB Multichannel (Podstawy funkcji SMB Multichannel).](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Czy moja maszyna wirtualna platformy Azure obsługuje kanał RSS?

Aby sprawdzić, czy karty sieciowe maszyny wirtualnej platformy Azure obsługują kanał RSS, uruchom polecenie w następujący `Get-SmbClientNetworkInterface` sposób i zaznacz pole wyboru `RSS Capable` : 

![Zrzut ekranu przedstawiający dane wyjściowe RSS dla maszyny wirtualnej platformy Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Czy Azure NetApp Files SMB Direct?

Nie, Azure NetApp Files nie obsługuje SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Jakie są korzyści z korzystania z wielokanałowego kanału SMB? 

Funkcja SMB Multichannel umożliwia klientowi SMB3 ustanawianie puli połączeń za pośrednictwem jednej karty sieciowej lub wielu kart sieciowych oraz używanie ich do wysyłania żądań dla jednej sesji SMB. Z kolei zgodnie z projektem SMB1 i SMB2 wymagają od klienta nawiązania jednego połączenia i wysłania całego ruchu SMB dla danej sesji za pośrednictwem tego połączenia. To pojedyncze połączenie ogranicza ogólną wydajność protokołu, która może zostać osiągnięta z jednego klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Czy należy skonfigurować wiele karty sieciowe na kliencie dla SMB?

Nie. Klient SMB będzie odpowiadać licznikowi kart sieciowych zwracanych przez serwer SMB.  Każdy wolumin magazynu jest dostępny z jednego punktu końcowego magazynu.  Oznacza to, że dla każdej relacji SMB będzie używana tylko jedna karta sieciowa.  

Jak pokazują `Get-SmbClientNetworkInterace` poniższe dane wyjściowe, maszyna wirtualna ma 2 interfejsy sieciowe — 15 i 12.  Jak pokazano w poniższym poleceniu , mimo że istnieją dwie karty sieciowe z możliwością rss, tylko interfejs 12 jest używany w połączeniu z udziałem SMB; interfejs 15 nie jest `Get-SmbMultichannelConnection` używany.

![Zrzut ekranu przedstawia dane wyjściowe dla kart sieciowych z możliwością rss.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Czy na platformie Azure jest obsługiwane kart sieciowych?

Kart sieciowych nie jest obsługiwana na platformie Azure. Mimo że wiele interfejsów sieciowych jest obsługiwanych na maszynach wirtualnych platformy Azure, reprezentują one konstrukcję logiczną, a nie fizyczną. W związku z tym nie zapewniają one odporności na uszkodzenia.  Ponadto przepustowość dostępna dla maszyny wirtualnej platformy Azure jest obliczana dla samej maszyny, a nie dla żadnego pojedynczego interfejsu sieciowego.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaka jest wydajność systemu SMB Multichannel?

Poniższe testy i wykresy pokazują możliwości usługi SMB Multichannel w obciążeniach z jednym wystąpieniem.

### <a name="random-io"></a>Losowe we/wy  

Po wyłączeniu funkcji SMB Multichannel na kliencie przeprowadzono czyste 4 testy odczytu i zapisu KiB przy użyciu fio i zestawu roboczego 40 GiB.  Udział SMB został odłączony od każdego testu z przyrostami liczby połączeń klienta SMB na ustawienia interfejsu sieciowego RSS `1` , , , , `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Testy pokazują, że domyślne ustawienie jest wystarczające w przypadku obciążeń intensywnie większych niż `4` we/wy; zwiększanie do i miało niewielki `8` `16` wpływ. 

Polecenie okazało `netstat -na | findstr 445` się, że nawiązono dodatkowe połączenia z przyrostami od `1` do i do `4` `8` `16` .  Cztery rdzenie procesora CPU zostały w pełni wykorzystane dla SMB podczas każdego testu, co potwierdza statystyka wydajności (nieujmowana `Per Processor Network Activity Cycles` w tym artykule).

![Wykres, który przedstawia losowe porównanie we/wy SMB Multichannel.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Maszyna wirtualna platformy Azure nie ma wpływu na limity we/wy magazynu SMB (ani NFS).  Jak pokazano na poniższym wykresie, typ wystąpienia D32ds ma ograniczoną szybkość 308 000 dla buforowanej liczby we/wy na magazynowanie i 51 200 dla niecachowanej liczby IOPS magazynu.  Powyższy wykres pokazuje jednak znacznie więcej we/wy za pośrednictwem SMB.

![Wykres, który pokazuje losowy test porównawczy we/wy.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekwencyjne we/wy 

Testy podobne do opisanych wcześniej losowych testów we/wy zostały wykonane przy użyciu 64-KiB sekwencyjnych operacji we/wy. Chociaż wzrost liczby połączeń klienta na interfejs sieciowy RSS powyżej 4" nie miał zauważalnego wpływu na losowe we/wy, to samo nie dotyczy sekwencyjnych we/wy. Jak pokazano na poniższym wykresie, każdy wzrost jest związany z odpowiednim wzrostem przepływności odczytu. Przepływność zapisu pozostała płaska z powodu ograniczeń przepustowości sieci na platformie Azure dla każdego typu/rozmiaru wystąpienia. 

![Wykres, na który przedstawiono porównanie testu przepływności.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Platforma Azure umieszcza limity szybkości sieci dla każdego typu/rozmiaru maszyny wirtualnej. Limit szybkości jest nakładany tylko na ruch wychodzący. Liczba karty sieciowe obecne na maszynie wirtualnej nie ma wpływu na całkowitą przepustowość dostępną dla maszyny.  Na przykład typ wystąpienia D32ds ma narzucony limit sieci 16 000 Mb/s (2000 MiB/s).  Jak pokazano na powyższym wykresie sekwencyjnym, limit ma wpływ na ruch wychodzący (zapis), ale nie na odczyty wielokanałowe.

![Wykres, który pokazuje sekwencyjny test porównania we/wy.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Jaka wydajność jest oczekiwana w przypadku pojedynczego wystąpienia z zestawem danych o rozmiarze 1 TB?

Aby zapewnić bardziej szczegółowy wgląd w obciążenia z mieszanym odczytem/zapisem, na poniższych dwóch wykresach przedstawiono wydajność pojedynczego woluminu chmury na poziomie usługi Ultra o pojemności 50 TB z zestawem danych o rozmiarze 1 TB i wielokanałowym przetwarzaniem SMB o rozmiarze 4. Wykorzystano optymalne we/wy z 16, a parametry elastycznego we/wy (FIO) zostały użyte w celu zapewnienia pełnego wykorzystania przepustowości sieci ( `numjobs=16` ).

Na poniższym wykresie przedstawiono wyniki 4 tys. losowych we/wy z pojedynczym wystąpieniem maszyny wirtualnej i mieszanym odczytem/zapisem w 10% interwałach:

![Wykres, który pokazuje, że system Windows 2019 w standardowych _D32ds_v4 losowych testów we/wy 4K.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Na poniższym wykresie przedstawiono wyniki sekwencyjnego we/wy:

![Wykres, który przedstawia przepływność sekwencyjną 64K w standardzie systemu Windows 2019 _D32ds_v4 64K.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Jaka wydajność jest oczekiwana podczas skalowania w zewnątrz przy użyciu 5 maszyn wirtualnych z zestawem danych o rozmiarze 1 TB?

Te testy z 5 maszynami wirtualnmi używają tego samego środowiska testowego co pojedyncza maszyna wirtualna, a każdy proces jest pisany do własnego pliku.

Na poniższym wykresie przedstawiono wyniki losowych we/wy:

![Wykres, który przedstawia standardową 5-wystąpienia 5-wystąpienia _D32ds_v4 Windows 2019 i 4K i 5 wystąpień we/wy.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Na poniższym wykresie przedstawiono wyniki sekwencyjnego we/wy:

![Wykres, który przedstawia przepływność sekwencyjną systemu Windows 2019 _D32ds_v4 64K z 5 wystąpieniami.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Jak monitorować karty Ethernet funkcji Hyper-V i zapewnić zmaksymalizowanie pojemności sieci?  

Jedną ze strategii używanych do testowania za pomocą fio (fio) jest `numjobs=16` ustawienie . W ten sposób każde zadanie jest 16 określonych wystąpień w celu zmaksymalizowania Microsoft Hyper-V karty sieciowej.

Działania na każdej karcie w systemie Windows monitor wydajności można sprawdzić, wybierając pozycję Monitor wydajności > Dodaj liczniki > interfejsu sieciowego > Microsoft Hyper-V **karty sieciowej.**

![Zrzut ekranu przedstawiający monitor wydajności Dodaj licznik.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Po uruchomieniu ruchu danych w woluminach można monitorować karty w systemie Windows monitor wydajności. Jeśli nie używasz wszystkich tych 16 kart wirtualnych, możesz nie maksymalizować pojemności przepustowości sieci.

![Zrzut ekranu przedstawiający monitor wydajności wyjściowe.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Czy zaleca się przyspieszoną sieć?

Aby uzyskać maksymalną wydajność, zaleca się skonfigurowanie [przyspieszonej sieci tam,](../virtual-network/create-vm-accelerated-networking-powershell.md) gdzie jest to możliwe. Należy pamiętać o następujących kwestiach:  

* Funkcja Azure Portal domyślnie włącza przyspieszoną sieć dla maszyn wirtualnych, które obsługują tę funkcję.  Jednak inne metody wdrażania, takie jak ansible i podobne narzędzia do konfiguracji, mogą nie być dostępne.  Nie można włączyć przyspieszonej sieci może bazować na wydajności maszyny.  
* Jeśli przyspieszona sieć nie jest włączona w interfejsie sieciowym maszyny wirtualnej ze względu na brak obsługi typu lub rozmiaru wystąpienia, pozostanie ona wyłączona w przypadku większych typów wystąpień. W takich przypadkach będzie potrzebna ręczna interwencja.

## <a name="are-jumbo-frames-supported"></a>Czy duże ramki są obsługiwane?

Duże ramki nie są obsługiwane w przypadku maszyn wirtualnych platformy Azure.

## <a name="is-smb-signing-supported"></a>Czy podpisywanie SMB jest obsługiwane? 

Protokół SMB stanowi podstawę udostępniania plików i wydruku oraz innych operacji sieciowych, takich jak zdalne administrowanie systemem Windows. Aby zapobiegać atakom typu man-in-the-middle, które modyfikują pakiety SMB podczas przesyłania, protokół SMB obsługuje cyfrowe podpisywanie pakietów SMB. 

Podpisywanie SMB jest obsługiwane we wszystkich wersjach protokołu SMB obsługiwanych przez Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaki jest wpływ podpisywania SMB na wydajność?  

Podpisywanie SMB ma niesłychany wpływ na wydajność SMB. Między innymi potencjalnymi przyczynami spadku wydajności cyfrowe podpisywanie każdego pakietu zużywa dodatkowy procesor CPU po stronie klienta, jak pokazano na poniższych danych wyjściowych wydajności. W tym przypadku core 0 jest odpowiedzialny za SMB, w tym za podpisywanie SMB.  Porównanie z liczbami przepływności odczytu sekwencyjnego innych niż wielokanałowe w poprzedniej sekcji pokazuje, że podpisywanie SMB zmniejsza ogólną przepływność z 875MiB/s do około 250MiB/s. 

![Wykres, który pokazuje wpływ na wydajność podpisywania SMB.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>Jaki jest przewidywany wpływ szyfrowania SMB na obciążenia klientów?

Zobacz Często zadawane pytania na temat [szyfrowania SMB.](azure-netapp-files-faqs.md#smb_encryption_impact)

## <a name="next-steps"></a>Następne kroki  

- [Często zadawane pytania dotyczące Azure NetApp Files](azure-netapp-files-faqs.md)
- Zobacz temat [Azure NetApp Files: Zarządzane udziały plików](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) przedsiębiorstwa dla obciążeń SMB dotyczące korzystania z udziałów plików SMB z Azure NetApp Files.