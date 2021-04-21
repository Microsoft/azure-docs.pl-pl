---
title: Co zmiana limitu przydziału na wolumin twardy oznacza dla Azure NetApp Files usługi | Microsoft Docs
description: Opisuje zmianę limitu przydziału woluminów, sposób planowania zmiany oraz sposób monitorowania pojemności i zarządzania nimi.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764283"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Co zmiana limitu przydziału twardego woluminu oznacza dla twojej Azure NetApp Files usługi

Od początku usługi usługa Azure NetApp Files aprowizowania puli pojemności i mechanizmu automatycznego wzrostu. Azure NetApp Files woluminy są aprowizowane zuniowo w puli pojemności zaaprowizowanych przez klienta w wybranej warstwie i o wybranym rozmiarze. Rozmiary woluminów (limity przydziału) są używane do zapewnienia wydajności i pojemności, a limity przydziału można dostosowywać na bieżąco w dowolnym momencie. To zachowanie oznacza, że obecnie limit przydziału woluminu jest używana do sterowania przepustowością woluminu. Obecnie pule nienadmiernych pojemności automatycznie rosną po zapełnieniu pojemności.   

> [!IMPORTANT] 
> Zachowanie Azure NetApp Files aprowizowania woluminów i puli pojemności zmieni się na mechanizm *ręczny* i *z możliwością* sterowania. **Od 30 kwietnia 2021 r. (aktualizacja) rozmiary woluminów (limity przydziału) będą zarządzać wydajnością przepustowości, a także aprowizowana pojemność, a bazowe pule pojemności nie będą już automatycznie rosnąć.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Przyczyny zmiany przydziału twardego woluminu

Wielu klientów wskazuje trzy główne wyzwania związane z *początkowym zachowaniem:*
* Klienci maszyny wirtualnej widzą pojemność aprowizowana zubogo (100 TiB) dowolnego woluminu w przypadku korzystania z narzędzi do monitorowania przestrzeni systemu operacyjnego lub pojemności, zapewniając niedokładną widoczność pojemności po stronie klienta lub aplikacji.
* Właściciele aplikacji nie mieliby kontroli nad aprowizowana przestrzenią puli pojemności (i powiązanymi kosztami) ze względu na zachowanie automatycznego wzrostu puli pojemności. Ta sytuacja jest kłopotliwa w środowiskach, w których "procesy wybiegowe" mogą szybko zapełniać się i powiększać aprowizowana pojemność i koszty.
* Klienci chcą widzieć i utrzymywać bezpośrednią korelację między rozmiarem woluminu (limitem przydziału) a wydajnością. W przypadku bieżącego zachowania (niejawnego) nadsubskrybowania woluminu (w zależności od pojemności) i automatycznego wzrostu puli klienci nie mają bezpośredniej korelacji, dopóki przydział woluminu nie zostanie aktywnie ustawiony lub zresetowany. 

Wielu klientów zażądało bezpośredniej kontroli nad aprowizowana pojemnością. Chce kontrolować i równoważyć pojemność i wykorzystanie magazynu. Chcą również kontrolować koszty wraz z widocznością dostępnej, używanej i aprowizowanych pojemności oraz wydajności woluminów aplikacji po stronie aplikacji i po stronie klienta. 

## <a name="what-is-the-volume-hard-quota-change"></a>Jaka jest zmiana limitu przydziału twardego woluminu   

Po zmianie limitu przydziału przydziału Azure NetApp Files woluminy nie będą już aprowizowane z wartością maksymalną (maksymalną) 100 TiB. Woluminy będą aprowowane przy rzeczywistym skonfigurowanym rozmiarze (limitze przydziału). Ponadto pule pojemności nie będą już automatycznie rosnąć po osiągnięciu pełnego zużycia pojemności. Ta zmiana będzie odzwierciedlać zachowanie, takie jak dyski zarządzane platformy Azure, które również są aprowowane bez automatycznego zwiększania pojemności.

Rozważmy na przykład wolumin Azure NetApp Files skonfigurowany przy rozmiarze 1 TiB (limit przydziału) w puli pojemności na poziomie usługi 4 TiB Ultra. Aplikacja stale zapisuje dane na woluminie.

Początkowe *zachowanie:*  
* Oczekiwana przepustowość: 128 MiB/s
* Całkowita pojemność do wykorzystania (i widoczna dla klienta): 100 TiB   
    Nie będzie można zapisać więcej danych na woluminie poza tym rozmiarem.
* Pula pojemności: gdy pula jest pełna, zwiększa się automatycznie o 1 TiB.
* Zmiana limitu przydziału woluminu: zmienia tylko wydajność (przepustowość) woluminu. Nie zmienia to widocznej ani użytecznej pojemności klienta.

Zmienione *zachowanie:*  
* Oczekiwana przepustowość: 128 MiB/s
* Całkowita pojemność do wykorzystania (i widoczna dla klienta): 1 TiB Nie będzie można zapisywać większej ilości danych na woluminie poza tym rozmiarem.
* Pula pojemności: pozostaje o rozmiarze 4 TiB i nie rośnie automatycznie. 
* Zmiana limitu przydziału woluminu: zmienia wydajność (przepustowość) i widoczną dla klienta pojemność woluminu lub pojemność do wykorzystania.

Należy aktywnie monitorować wykorzystanie woluminów Azure NetApp Files pul pojemności. Należy celowo zmienić wykorzystanie woluminu i puli w celu całkowitego użycia. Azure NetApp Files będzie nadal zezwalać na operacje rozmiaru woluminu i puli pojemności na [bieżąco.](azure-netapp-files-resize-capacity-pools-or-volumes.md)

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Jak zoperacyjnie zmienić przydział woluminu

Ta sekcja zawiera wskazówki dotyczące sposobu zarządzania zmianą przydziału twardego woluminu w celu bezproblemowego przejścia. Udostępnia również szczegółowe informacje dotyczące obsługi aktualnie aprowowanych woluminów i pul pojemności, monitorowania w trakcie działania oraz opcji alertów i zarządzania pojemnością.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Obecnie aprowizowane woluminy i pule pojemności

Ze względu na zmianę przydziału twardego woluminu należy zmienić model operacyjny. Aprowizowane woluminy i pule pojemności będą wymagały ciągłego zarządzania pojemnością.  Ponieważ zmienione zachowanie nastąpi natychmiast, zespół Azure NetApp Files zaleca szereg środków naprawczych dla istniejących, wcześniej aprowowanych woluminów i pul pojemności, zgodnie z opisem w tej sekcji.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Zalecenia dotyczące środków naprawczych lub prewencyjnych  

Zmiana przydziału twardego woluminu spowoduje zmiany w zaaprowizowanych i dostępnych pojemności dla wcześniej aprowowanych woluminów i pul. W związku z tym mogą wystąpić pewne wyzwania związane z alokacją pojemności. Aby uniknąć krótkoterminowych sytuacji, w których nie ma miejsca dla klientów, zespół Azure NetApp Files zaleca stosowanie następujących środków naprawczych/prewencyjnych: 

* **Rozmiary aprowizowanych woluminów:**   
    Zmień rozmiar każdego aprowizowanych woluminów tak, aby miał odpowiedni bufor na podstawie szybkości zmian i alertów lub zmiany rozmiaru czasu przetwarzania (na przykład 20% w oparciu o typowe zagadnienia dotyczące obciążenia) z maksymalnym rozmiarem 100 TiB (czyli [limitem](azure-netapp-files-resource-limits.md#resource-limits)rozmiaru woluminu). Ten nowy rozmiar woluminu, w tym pojemność buforu, powinien opierać się na następujących czynnikach:
    * **Aprowizowana** pojemność woluminu, jeśli używana pojemność jest mniejsza niż aprowizowany limit przydziału woluminu.
    * **Używana** pojemność woluminu, jeśli używana pojemność jest większa niż aprowizowany limit przydziału woluminu.  
    Nie ma dodatkowych opłat za zwiększenie pojemności na poziomie woluminu, jeśli pula pojemności nienadmiernych nie musi być zwiększana. W związku z tą zmianą można  zaobserwować wzrost limitu przepustowości dla woluminu (w przypadku użycia typu puli [pojemności automatycznego QoS).](azure-netapp-files-understand-storage-hierarchy.md#qos_types)

* **Rozmiary aprowizowanych pul pojemności:**   
    Po dopasowaniu rozmiarów woluminów, jeśli suma rozmiarów woluminów będzie większa niż rozmiar puli pojemności hostingu, pula pojemności będzie trzeba zwiększyć do rozmiaru równego lub większego niż suma woluminów, przy czym maksymalna wartość to 500 TiB (czyli limit rozmiaru puli [pojemności).](azure-netapp-files-resource-limits.md#resource-limits) Dodatkowa pojemność puli będzie normalnie podlegać opłatom za usługę ACR.

Jeśli potrzebujesz pomocy przy konfigurowaniu monitorowania lub alertów zgodnie z opisem w poniższych sekcjach, należy współpracować ze specjalistami ds. Azure NetApp Files w celu zweryfikowania środowiska.

### <a name="ongoing-capacity-management"></a>Bieżące zarządzanie pojemnością  

Po wykonaniu środków naprawczych należy ująć w sobie ciągłe procesy monitorowania pojemności i zarządzania nimi. Poniższe sekcje zawierają sugestie i alternatywy dotyczące monitorowania pojemności i zarządzania nimi.

### <a name="monitor-capacity-utilization"></a>Monitorowanie wykorzystania pojemności

Wykorzystanie pojemności można monitorować na różnych poziomach. 

#### <a name="vm-level-monitoring"></a>Monitorowanie na poziomie maszyny wirtualnej 

Najwyższy poziom monitorowania (najbliższy aplikacji) jest z poziomu maszyny wirtualnej aplikacji. Zaobserwowasz zmianę zachowania raportowania pojemności z poziomu systemu operacyjnego klienta maszyny wirtualnej.

W następujących dwóch scenariuszach rozważmy wolumin Azure NetApp Files skonfigurowany w rozmiarze 1 TiB (limit przydziału) w puli pojemności na poziomie usługi 4 TiB w chmurze Ultra. 

##### <a name="windows"></a>Windows

Klienci systemu Windows mogą sprawdzać używaną i dostępną pojemność woluminu przy użyciu właściwości dysku zmapowanych sieci. Możesz użyć opcji **Właściwości**  ->  **dysku**  ->  **Eksploratora.**  

W poniższych przykładach przedstawiono raportowanie pojemności woluminu w systemie Windows *przed* zmianą zachowania:

![Zrzuty ekranu, na których pokazano przykładową pojemność magazynu woluminu przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Możesz również użyć polecenia `dir` w wierszu polecenia, jak pokazano poniżej:

![Zrzut ekranu przedstawiający użycie polecenia w celu wyświetlenia pojemności magazynu dla woluminu przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

W poniższych przykładach podano raportowanie pojemności woluminu w systemie Windows *po* zmianie zachowania:

![Zrzuty ekranu, które pokazują przykładową pojemność magazynu woluminu po zmianie zachowania.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

W poniższym przykładzie pokazano `dir` dane wyjściowe polecenia:  

![Zrzut ekranu przedstawiający użycie polecenia w celu wyświetlenia pojemności magazynu dla woluminu po zmianie zachowania.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Klienci systemu Linux mogą sprawdzić używaną i dostępną pojemność woluminu za pomocą [ `df` polecenia](https://linux.die.net/man/1/df). Opcja będzie wyświetlać rozmiar, używane miejsce i dostępne miejsce w formacie czytelnym dla człowieka przy użyciu `-h` rozmiarów jednostek M, G i T.

W poniższym przykładzie przedstawiono raportowanie pojemności woluminów w systemie Linux *przed* zmianą zachowania:  

![Zrzut ekranu przedstawiający wyświetlanie pojemności magazynu dla woluminu przy użyciu systemu Linux przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

W poniższym przykładzie przedstawiono raportowanie pojemności woluminów w systemie Linux *po* zmianie zachowania:  

![Zrzut ekranu przedstawiający wyświetlanie pojemności magazynu dla woluminu przy użyciu systemu Linux po zmianie zachowania.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurowanie alertów przy użyciu klasy ANFCapacityManager

Za pomocą obsługiwanego przez społeczność narzędzia ANFCapacityManager można Logic Apps monitorowania pojemności Azure NetApp Files i otrzymywania dostosowanych alertów. Narzędzie ANFCapacityManager jest dostępne na stronie [ANFCapacityManager w witrynie GitHub.](https://github.com/ANFTechTeam/ANFCapacityManager)

ANFCapacityManager to aplikacja logiki platformy Azure, która zarządza regułami alertów opartymi na pojemnościach. Powoduje to automatyczne zwiększenie rozmiarów woluminów, aby Azure NetApp Files woluminów nie będzie się zakańczyć. Jest łatwe do wdrożenia i zapewnia następujące Alert Management możliwości:

* Po utworzeniu Azure NetApp Files pojemności lub woluminu program ANFCapacityManager tworzy regułę alertu metryki na podstawie określonego progu procentowego użycia.
* Gdy rozmiar Azure NetApp Files pojemności lub woluminu zostanie zmieniony, moduł ANFCapacityManager modyfikuje regułę alertu metryki na podstawie określonego progu użycia pojemności procentowej. Jeśli reguła alertu nie istnieje, zostanie utworzona.
* Po usunięciu Azure NetApp Files pojemności lub woluminu odpowiednia reguła alertu metryki zostanie usunięta.

Można skonfigurować następujące kluczowe ustawienia alertów:  

* **Procent pełnego progu puli pojemności** — to ustawienie określa próg, który wyzwala alert dla pul pojemności. Wartość 90 spowodowałaby wyzwolenie alertu, gdy pula pojemności osiągnie 90% zużytych zasobów.
* **Procent pełnego progu woluminu** — to ustawienie określa próg, który wyzwala alert dla woluminów. Wartość 80 spowodowałaby wyzwolenie alertu, gdy ilość zużytego woluminu osiągnie 80%.
* **Istniejąca grupa akcji dla powiadomień o** pojemności — to ustawienie jest grupą akcji, która zostanie wyzwolona na podstawie alertów opartych na pojemności. To ustawienie powinno zostać wstępnie utworzone przez Ciebie. Grupa akcji może wysyłać wiadomości e-mail, wiadomości SMS lub inne formaty.

Na poniższej ilustracji przedstawiono konfigurację alertu:  

![Ilustracja przedstawiająca konfigurację alertów przy użyciu klasy ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Po zainstalowaniu klasy ANFCapacityManager można oczekiwać następującego zachowania: Gdy pula pojemności lub wolumin pojemności usługi Azure NetApp Files zostanie utworzona, zmodyfikowana lub usunięta, aplikacja logiki automatycznie utworzy, zmodyfikuje lub usunie regułę alertu metryki opartą na pojemności o nazwie `ANF_Pool_poolname` lub `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Zarządzanie pojemnością

Oprócz monitorowania i alertów należy również uwzględnić praktykę zarządzania pojemnością aplikacji w celu zarządzania Azure NetApp Files (zwiększone) pojemności. Po zapełnieniu Azure NetApp Files pojemności można na bieżąco dostarczać dodatkową pojemność bez [zakłóceń w działaniu aplikacji.](azure-netapp-files-resize-capacity-pools-or-volumes.md) W tej sekcji opisano różne sposoby ręcznego i zautomatyzowanego zwiększania ilości i pojemności aprowizowanych przestrzeni w razie potrzeby.
 
#### <a name="manual"></a>Ręcznie 

Możesz użyć portalu lub interfejsu wiersza polecenia, aby ręcznie zwiększyć rozmiar woluminu lub puli pojemności. 

##### <a name="portal"></a>Portal 

W razie [potrzeby można zmienić rozmiar](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) woluminu. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.

1. W bloku Zarządzanie kontem netApp kliknij pozycję **Woluminy.**  
2. Kliknij prawym przyciskiem myszy nazwę woluminu, którego rozmiar chcesz zmienić, lub kliknij ikonę na końcu wiersza woluminu, aby wyświetlić `…` menu kontekstowe. 
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar woluminu lub go usunąć.   

   ![Zrzut ekranu przedstawiający opcje menu kontekstowego dla woluminu.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Zrzut ekranu przedstawiający okno Aktualizowanie limitu przydziału woluminów.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

W niektórych przypadkach pula pojemności hostingu nie ma wystarczającej pojemności do zmiany rozmiaru woluminów. Można jednak zmienić [rozmiar puli pojemności w](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) przyrostach lub dekrementacjach o 1 TiB. Rozmiar puli pojemności nie może być mniejszy niż 4 TiB. *Zmiana rozmiaru puli pojemności zmienia zakupioną pojemność Azure NetApp Files pojemności.*

1. W bloku Zarządzanie kontem netApp kliknij pulę pojemności, której rozmiar chcesz zmienić.
2. Kliknij prawym przyciskiem myszy nazwę puli pojemności lub kliknij ikonę na końcu wiersza puli pojemności, aby `…` wyświetlić menu kontekstowe.
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar puli pojemności lub ją usunąć.    

   ![Zrzut ekranu przedstawiający opcje menu kontekstowego dla puli pojemności.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Zrzut ekranu przedstawiający okno Zmienianie rozmiaru puli.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>Interfejs wiersza polecenia lub program PowerShell

Możesz użyć narzędzi interfejsu [wiersza Azure NetApp Files](azure-netapp-files-sdk-cli.md#cli-tools)wiersza polecenia, w tym interfejsu wiersza polecenia platformy Azure i interfejsu Azure PowerShell, aby ręcznie zmienić rozmiar woluminu lub puli pojemności.  Następujące dwa polecenia mogą służyć do zarządzania woluminami Azure NetApp Files zasobów puli:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Aby zarządzać Azure NetApp Files zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, możesz otworzyć Azure Portal i wybrać link Azure **Cloud Shell** u góry paska menu: 

[![Zrzut ekranu przedstawiający sposób uzyskiwania dostępu Cloud Shell linku. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Ta akcja spowoduje otwarcie Azure Cloud Shell:

[![Zrzut ekranu przedstawiający Cloud Shell ekranu. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

W poniższych przykładach za pomocą poleceń [można wyświetlić](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) [i](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) zaktualizować rozmiar woluminu:
 
[![Zrzut ekranu przedstawiający wyświetlanie rozmiaru woluminu przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Zrzut ekranu przedstawiający aktualizowanie rozmiaru woluminu przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

W poniższych przykładach za pomocą poleceń [można wyświetlić](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) [i](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) zaktualizować rozmiar puli pojemności:

[![Zrzut ekranu przedstawiający wyświetlanie rozmiaru puli pojemności przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[Zrzut ekranu przedstawiający aktualizowanie rozmiaru puli pojemności ![ przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatyczny  

Możesz utworzyć zautomatyzowany proces zarządzania zmienionym zachowaniem.

##### <a name="rest-api"></a>Interfejs API REST   

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP względem zasobów, takich jak konto netApp, pula pojemności, woluminy i migawki. Specyfikacja interfejsu API REST dla Azure NetApp Files jest publikowana za [pośrednictwem Azure NetApp Files Resource Manager w witrynie GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. Przykładowy [kod do użycia z interfejsami API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) można znaleźć w usłudze GitHub.

Zobacz Develop for Azure NetApp Files with REST API (Tworzenie aplikacji pod [Azure NetApp Files przy użyciu interfejsu API REST).](azure-netapp-files-develop-with-rest-api.md) 

##### <a name="rest-api-using-powershell"></a>Interfejs API REST korzystający z programu PowerShell  

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP względem zasobów, takich jak konto netApp, pula pojemności, woluminy i migawki. Specyfikacja [interfejsu API REST dla Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) jest publikowana za pośrednictwem usługi GitHub.

Zobacz Develop for Azure NetApp Files with REST API using PowerShell (Tworzenie [aplikacji pod Azure NetApp Files interfejsem API REST przy użyciu programu PowerShell).](develop-rest-api-powershell.md)

##### <a name="capacity-management-using-anfcapacitymanager"></a>Zarządzanie pojemnością przy użyciu klasy ANFCapacityManager

ANFCapacityManager to aplikacja logiki platformy Azure, która zarządza regułami alertów opartymi na pojemnościach. Automatycznie zwiększa rozmiary woluminów, aby Azure NetApp Files woluminów nie będzie za dużo miejsca. Oprócz wysyłania alertów można włączyć automatyczne zwiększanie rozmiaru woluminów i puli pojemności, aby zapobiec Azure NetApp Files woluminów z zakańcem miejsca: 

* Opcjonalnie, gdy wolumin Azure NetApp Files osiągnie określony próg procentu zużytego woluminu, limit przydziału woluminu (rozmiar) zostanie zwiększony o wartość procentową określoną w zakresie od 10 do 100.  
* Jeśli zwiększenie rozmiaru woluminu przekroczy pojemność puli pojemności, rozmiar puli pojemności również zostanie zwiększony w celu uwzględnienia nowego rozmiaru woluminu.

Można skonfigurować następujące ustawienie zarządzania pojemnością klucza:  

* **Automatyczne zwiększanie procentu —** procent istniejącego rozmiaru woluminu w celu automatycznego zwiększenia woluminu, jeśli osiągnie określony **procent pełnego progu.** Wartość 0 (zero) spowoduje wyłączenie funkcji Automatycznego podrastania. Zalecana jest wartość z 10 do 100.

    ![Zrzut ekranu przedstawiający okno Ustaw wartość procentową automatycznego wzrostu woluminu.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Często zadawane pytania 

W tej sekcji znajdują się odpowiedzi na niektóre pytania dotyczące zmiany limitu przydziału woluminu. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Czy miejsce migawki jest wliczane do użytecznej lub aprowizowana pojemności woluminu?

Tak, zużyta pojemność migawki jest wliczane do aprowizowanych miejsc w woluminie. Jeśli wolumin jest pełny, rozważ dwie opcje korygowania:

* Zmień rozmiar woluminu zgodnie z opisem w tym artykule.
* Usuń starsze migawki, aby wolnego miejsca w woluminie hostingu.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Czy ta zmiana oznacza, że zachowanie automatycznego wzrostu woluminu zniknie z Azure NetApp Files?

Powszechnym błędem jest to, że Azure NetApp Files *woluminy* będą automatycznie rosnąć po zapełnieniu. Woluminy były aprowizowane zustanie o rozmiarze 100 TiB, niezależnie  od rzeczywistego ustawionego limitu przydziału, podczas gdy pula pojemności nienadmiernych automatycznie rośnie z przyrostami o 1 TiB. Ta zmiana będzie dotyczyć rozmiaru woluminu (widocznego i  użytecznego)  do ustawionego limitu przydziału, a pule pojemności nie będą już automatycznie powiększane. Ta zmiana skutkuje często żądanym dokładnym obszarem po stronie klienta i raportowaniem pojemności. Pozwala to uniknąć "niepotrzebnych" zużycia pojemności.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Czy ta zmiana ma jakikolwiek wpływ na woluminy replikowane za pomocą replikacji między regionami (wersja zapoznawcza)? 

Przydział woluminów twardych nie jest wymuszany na woluminach docelowych replikacji.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Czy ta zmiana ma jakikolwiek wpływ na metryki obecnie dostępne w Azure Monitor?

Metryki i statystyki Azure Monitor portalu dokładnie odzwierciedlają nowy model alokacji i wykorzystania.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Czy ta zmiana ma jakikolwiek wpływ na limity zasobów dla Azure NetApp Files?

Nie ma żadnych zmian limitów zasobów dla Azure NetApp Files poza zmiany limitu przydziału opisane w tym artykule.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Czy istnieje przykładowy przepływ pracy ANFCapacityManager?  

Tak. Zobacz [przykładowy przepływ pracy automatycznego podrastania woluminów w witrynie GitHub.](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Czy firma Microsoft obsługuje program ANFCapacityManager?  

[Aplikacja logiki ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)jest dostarczana w stanie, w ile jest, i nie jest obsługiwana przez firmę NetApp ani Microsoft. Zachęcamy do modyfikowania, aby dopasować je do określonego środowiska lub wymagań. Funkcje należy przetestować przed wdrożeniem ich w środowiskach krytycznych dla działania firmy lub produkcyjnych.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Jak zgłosić usterkę lub przesłać żądanie funkcji dla anfcapacityManger?
Możesz przesyłać usterki i żądania funkcji, klikając pozycję **Nowy problem** na [stronie ANFCapacityManager w witrynie GitHub.](https://github.com/ANFTechTeam/ANFCapacityManager/issues)

## <a name="next-steps"></a>Następne kroki
* [Zmienianie rozmiaru puli pojemności lub woluminu](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metryki dla usługi Azure NetApp Files](azure-netapp-files-metrics.md)
