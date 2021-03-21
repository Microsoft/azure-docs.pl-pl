---
title: Co zmienia się w przydziały twarde dla usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano zmianę sposobu korzystania z przydziałów twardych woluminu, planowanie zmiany i monitorowanie pojemności oraz zarządzanie nimi.
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
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 12807e83f7841bc67999ce385d0cb82bf15f4c71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175995"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Co zmienia się w przydziały twarde dla usługi Azure NetApp Files

Na początku usługi Azure NetApp Files użyto funkcji aprowizacji puli pojemności i mechanizmu automatycznego wzrostu. Woluminy Azure NetApp Files są alokowane elastycznie, w przypadku podwyższenia poziomu puli pojemności obsługiwanej przez klienta wybranej warstwy i rozmiaru. Rozmiary woluminów (przydziały) są używane w celu zapewnienia wydajności i pojemności, a limity przydziału można w dowolnym momencie dostosować do siebie. To zachowanie oznacza, że obecnie przydział woluminu jest dźwignią wydajności służącą do kontrolowania przepustowości do woluminu. Obecnie w przypadku wypełniania pojemności pule pojemności są automatycznie zwiększane.   

> [!IMPORTANT] 
> Azure NetApp Files zachowanie obsługi puli woluminów i pojemności zmieni się na mechanizm *ręczny* i *kontrolowany* . **Począwszy od 1 kwietnia 2021 (zaktualizowane), rozmiary woluminów (przydział) będą zarządzać wydajnością przepustowości, a także pojemnością zasobów, a podstawowe pule pojemności nie będą już automatycznie zwiększane.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Przyczyny zmiany limitu przydziału twardego

Wielu klientów wykazało trzy główne wyzwania z *początkowym* zachowaniem:
* W przypadku korzystania z przestrzeni OS lub narzędzi do monitorowania pojemności klienci maszyn wirtualnych będą widzieli pojemność alokowanej elastycznie (100 TiB) dla danego woluminu.
* Właściciele aplikacji nie będą mieć kontroli nad miejscem puli zainicjowanej pojemności (i powiązanym kosztem) ze względu na zachowanie autowzrostu puli pojemności. Ta sytuacja jest nieskomplikowana w środowiskach, w których "procesy uruchamiania" mogą szybko zapełniać i zwiększać zainicjowaną pojemność i koszt.
* Klienci chcą zobaczyć i zachować bezpośrednią korelację między rozmiarem woluminu (przydziałem) i wydajnością. W przypadku bieżącego zachowania (niejawnego) przy zasubskrybowaniu woluminu (z możliwością) i rozszerzania puli klienci nie mają bezpośredniej korelacji, dopóki przydział woluminu nie zostanie aktywnie ustawiony lub zresetowany. 

Wielu klientów zażądał bezpośredniej kontroli nad zainicjowaną pojemnością. Chcą kontrolować i zrównoważyć pojemność i wykorzystanie magazynu. Chcą również kontrolować koszty wraz z widocznością i wydajnością po stronie klienta oraz udostępnianą, wykorzystywaną i wydajną pojemnością woluminów aplikacji. 

## <a name="what-is-the-volume-hard-quota-change"></a>Co to jest zmiana przydziału twardego woluminu   

W przypadku zmiany przydziałów twardych woluminów Azure NetApp Files woluminy nie będą już alokowane elastycznie przy maksymalnym obsłudze 100 TiB. Woluminy będą obsługiwane w rzeczywistym skonfigurowanym rozmiarze (przydziały). Ponadto pule wydajności podnieśki nie będą już automatycznie zwiększane po osiągnięciu użycia pełnej pojemności. Ta zmiana będzie odzwierciedlała zachowanie, takie jak dyski zarządzane platformy Azure, które są również obsługiwane w niezmienionej postaci.

Rozważmy na przykład wolumin Azure NetApp Files skonfigurowany o rozmiarze 1 TiB (przydział) w puli pojemności na poziomie 4-TiB Ultra Service. Aplikacja ciągle zapisuje dane na woluminie.

*Początkowe* zachowanie:  
* Oczekiwana przepustowość: 128 MiB/s
* Łączny użyteczny (i widoczny dla klienta) pojemność: 100 TiB   
    Nie będzie można zapisać większej ilości danych w woluminie poza tym rozmiarem.
* Pula pojemności: automatycznie rośnie z 1 przyrost TiB, gdy jest pełny.
* Zmiana limitu przydziału woluminu: tylko zmiany wydajności (przepustowości) woluminu. Nie zmienia ona widocznej ani użytecznej pojemności klienta.

*Zmienione* zachowanie:  
* Oczekiwana przepustowość: 128 MiB/s
* Łączna liczba przydatnych (i widocznych dla klienta) pojemności: 1 TiB nie będzie można zapisać większej ilości danych na woluminie poza tym rozmiarem.
* Pula pojemności: pozostanie 4 TiB w rozmiarze i nie rośnie automatycznie. 
* Zmiana limitu przydziału woluminu: zmiany wydajności (przepustowości) i widoczne dla klienta pojemności woluminu.

Należy aktywnie monitorować wykorzystanie woluminów Azure NetApp Files i pul pojemności. Należy do tego celu zmienić wykorzystanie woluminu i puli na potrzeby zamykania do pełnego zużycia. Azure NetApp Files będzie nadal zezwalać [na operacje zmiany rozmiaru puli pojemności i woluminu na bieżąco](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Jak operacjonalizować zmianę limitu przydziału twardego

Ta sekcja zawiera wskazówki dotyczące operacjonalizować zmiany limitu przydziału twardego na potrzeby płynnego przejścia. Zawiera on również szczegółowe informacje na potrzeby obsługi aktualnie zainicjowanych woluminów oraz pul pojemności, monitorowania i generowania alertów oraz opcji zarządzania wydajnością.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Aktualnie inicjowane woluminy i pule pojemności

Ze względu na zmianę przydziału twardego woluminu należy zmienić model operacyjny. Zarezerwowane woluminy i pule pojemności będą wymagały ciągłego zarządzania pojemnością.  Ze względu na to, że zmienione zachowanie będzie wykonywane natychmiast, zespół Azure NetApp Files zaleca szereg jednorazowych miar naprawczych dla istniejących, wcześniej zainicjowanych woluminów i pul pojemności, zgodnie z opisem w tej sekcji.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Zalecenia dotyczące działań naprawczych i zapobiegawczych  

Zmiana przydziałów twardych woluminu spowoduje zmiany zainicjowanej i dostępnej pojemności dla wstępnie zainicjowanych woluminów i pul. W związku z tym mogą wystąpić pewne problemy związane z alokacją pojemności. Aby uniknąć krótkoterminowych sytuacji poza miejscem dla klientów, zespół Azure NetApp Files zaleca następujące miary naprawcze i zapobiegawcze: 

* **Obsługiwane rozmiary woluminów**:   
    Zmień rozmiar każdego z zainicjowanych woluminów, aby dysponować odpowiednim buforem na podstawie szybkości zmian i alertów, lub zmienić rozmiar szybkością oferowaną (na przykład 20% w oparciu o typowe zagadnienia dotyczące obciążeń), maksymalnie 100 TiB (czyli [limit rozmiaru woluminu](azure-netapp-files-resource-limits.md#resource-limits)). Ten nowy rozmiar woluminu, w tym pojemność bufora, powinien opierać się na następujących czynnikach:
    * Pojemność woluminu **zainicjowanego** , na wypadek gdy używana pojemność jest mniejsza niż przydział woluminu zaalokowanego.
    * **Używana** pojemność woluminu, na wypadek gdy użyta pojemność jest większa niż przydział woluminu zaalokowanego.  
    Nie ma dodatkowej opłaty za zwiększenie pojemności na poziomie woluminu, jeśli nie ma potrzeby wzrostu puli wydajności. W wyniku tej zmiany można obsłużyć *zwiększenie* limitu przepustowości dla woluminu (w przypadku używania [typu puli wydajności autoqos](azure-netapp-files-understand-storage-hierarchy.md#qos_types) ).

* **Obsługiwane rozmiary puli pojemności**:   
    Po dostosowaniu rozmiaru woluminu, jeśli suma rozmiarów woluminów będzie większa niż rozmiar puli pojemności hostingu, Pula pojemności będzie musiała zostać zwiększona do rozmiaru równego lub większego od sumy woluminów, a maksymalna 500 TiB (czyli [limit rozmiaru puli pojemności](azure-netapp-files-resource-limits.md#resource-limits)). Dodatkowa pojemność puli pojemności będzie podlegać opłatom za ACR.

Należy skontaktować się z specjalistami ds. Azure NetApp Files, aby sprawdzić poprawność środowiska, jeśli potrzebujesz pomocy przy konfigurowaniu monitorowania lub zgłaszaniu alertów zgodnie z opisem w poniższych sekcjach.

### <a name="ongoing-capacity-management"></a>Ciągłe zarządzanie pojemnością  

Po przeprowadzeniu jednorazowych środków naprawczych należy wdrożyć bieżące procesy do monitorowania pojemności i zarządzania nią. Poniższe sekcje zawierają sugestie i alternatywy dotyczące monitorowania pojemności i zarządzania nimi.

### <a name="monitor-capacity-utilization"></a>Monitorowanie wykorzystania pojemności

Można monitorować wykorzystanie pojemności na różnych poziomach. 

#### <a name="vm-level-monitoring"></a>Monitorowanie na poziomie maszyny wirtualnej 

Najwyższy poziom monitorowania (najbliżej aplikacji) jest z poziomu maszyny wirtualnej aplikacji. W systemie operacyjnym klienta maszyny wirtualnej zaobserwujesz zmianę w działaniu raportowania pojemności.

W poniższych dwóch scenariuszach należy wziąć pod uwagę Azure NetApp Files wolumin skonfigurowany o rozmiarze 1-TiB (przydział) w puli pojemności w warstwach 4-TiB i Ultra. 

##### <a name="windows"></a>Windows

Klienci systemu Windows mogą sprawdzić zajętą i dostępną pojemność woluminu przy użyciu właściwości dysku zamapowanego przez sieć. Możesz użyć   ->    ->  opcji **Właściwości** stacji Eksploratora.  

Poniższe przykłady przedstawiają raportowanie pojemności woluminu w systemie Windows *przed* zmianą zachowania:

![Zrzuty ekranu pokazujące przykładową pojemność magazynu woluminu przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Możesz również użyć `dir` polecenia w wierszu polecenia, jak pokazano poniżej:

![Zrzut ekranu, który pokazuje użycie polecenia, aby wyświetlić pojemność magazynu dla woluminu przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Poniższe przykłady przedstawiają raportowanie pojemności woluminu w systemie Windows *po* zmienionym zachowaniu:

![Zrzuty ekranu pokazujące przykładową pojemność magazynu woluminu po zmianie zachowania.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Poniższy przykład przedstawia `dir` dane wyjściowe polecenia:  

![Zrzut ekranu przedstawiający użycie polecenia do wyświetlania pojemności magazynu dla woluminu po zmianie zachowania.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Klienci z systemem Linux mogą sprawdzić użytą i dostępną pojemność woluminu za pomocą [ `df` polecenia](https://linux.die.net/man/1/df). `-h`Opcja spowoduje wyświetlenie rozmiaru, używanego miejsca i dostępnego miejsca w formacie do odczytu przez człowieka przy użyciu rozmiarów jednostek M, G i T.

Poniższy przykład przedstawia raportowanie pojemności woluminu w systemie Linux *przed* zmienionym zachowaniem:  

![Zrzut ekranu przedstawiający użycie systemu Linux do wyświetlania pojemności magazynu dla woluminu przed zmianą zachowania.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Poniższy przykład przedstawia raportowanie pojemności woluminu w systemie Linux *po* zmienionym zachowaniu:  

![Zrzut ekranu przedstawiający użycie systemu Linux do wyświetlania pojemności magazynu dla woluminu po zmianie zachowania.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurowanie alertów za pomocą ANFCapacityManager

Można użyć Logic Apps narzędzia ANFCapacityManager obsługiwanego przez społeczność, aby monitorować Azure NetApp Files pojemności i odbierać dopasowane alerty. Narzędzie ANFCapacityManager jest dostępne na stronie usługi [ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager)w serwisie GitHub.

ANFCapacityManager to aplikacja logiki platformy Azure, która zarządza regułami alertów opartych na wydajności. Automatycznie zwiększa rozmiary woluminów, aby zapobiec braku miejsca na Azure NetApp Files woluminów. Wdrażanie i zapewnia następujące możliwości Alert Management:

* Po utworzeniu puli pojemności Azure NetApp Files lub woluminu ANFCapacityManager tworzy regułę alertu metryki na podstawie określonego procentu progowego wykorzystania.
* Po zmianie rozmiaru puli pojemności Azure NetApp Files lub woluminu ANFCapacityManager modyfikuje regułę alertu metryki na podstawie określonego progu zużytej pojemności (%). Jeśli reguła alertu nie istnieje, zostanie utworzona.
* Po usunięciu puli pojemności Azure NetApp Files lub woluminu zostanie usunięta odpowiednia reguła alertu dotyczącego metryki.

Można skonfigurować następujące ustawienia alertów:  

* **Pełny próg puli pojemności (%)** — to ustawienie określa wykorzystany próg wyzwalający alert dla pul pojemności. Wartość 90 spowoduje, że alert zostanie wyzwolony, gdy pula pojemności osiągnie 90% zużyte.
* **Pełny próg woluminu (%)** — to ustawienie określa wykorzystany próg wyzwalający alert dla woluminów. Wartość 80 spowoduje, że alert zostanie wyzwolony, gdy wolumin osiągnie 80% czasu zużyte.
* **Istniejąca grupa akcji dla powiadomień o pojemności** — to ustawienie jest grupą akcji, która będzie wyzwalana dla alertów opartych na wydajności. To ustawienie powinno być wstępnie utworzone przez użytkownika. Grupa akcji może wysyłać wiadomości e-mail, wiadomości SMS lub inne formaty.

Na poniższej ilustracji przedstawiono konfigurację alertu:  

![Ilustracja przedstawiająca konfigurację alertów za pomocą ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Po zainstalowaniu ANFCapacityManager można oczekiwać następujących zachowań: w przypadku utworzenia, zmodyfikowania lub usunięcia puli pojemności Azure NetApp Files lub woluminu, aplikacja logiki automatycznie tworzy, modyfikuje lub usuwa regułę alertu metryki opartej na dyspozycyjności o nazwie `ANF_Pool_poolname` lub `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Zarządzanie pojemnością

Oprócz monitorowania i zgłaszania alertów należy również uwzględnić praktyczne zarządzanie pojemnością aplikacji, aby zarządzać zużyciem Azure NetApp Files (zwiększone). Po wypełnieniu woluminu Azure NetApp Files lub puli pojemności [dodatkowa pojemność może być dostępna na bieżąco bez zakłóceń aplikacji](azure-netapp-files-resize-capacity-pools-or-volumes.md). W tej sekcji opisano różne ręczne i zautomatyzowane sposoby zwiększania ilości miejsca zajmowanego przez pulę woluminów i pojemności odpowiednio do potrzeb.
 
#### <a name="manual"></a>Ręcznie 

Możesz użyć portalu lub interfejsu wiersza polecenia, aby ręcznie zwiększyć rozmiar woluminu lub puli pojemności. 

##### <a name="portal"></a>Portal 

W razie potrzeby można [zmienić rozmiar woluminu](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) . Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.

1. W bloku Zarządzanie kontem usługi NetApp kliknij pozycję **woluminy**.  
2. Kliknij prawym przyciskiem myszy nazwę woluminu, którego rozmiar chcesz zmienić, lub kliknij `…` ikonę na końcu wiersza woluminu, aby wyświetlić menu kontekstowe. 
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć wolumin.   

   ![Zrzut ekranu pokazujący opcje menu kontekstowego dla woluminu.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Zrzut ekranu przedstawiający okno aktualizowanie przydziału woluminu.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

W niektórych przypadkach Pula pojemności hostingu nie ma wystarczającej wydajności, aby zmienić rozmiar woluminów. Można jednak [zmienić rozmiar puli pojemności](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) w 1-TIB przyrostach lub zmniejszeniu. Rozmiar puli pojemności nie może być mniejszy niż 4 TiB. *Zmiana rozmiarów puli pojemności zmienia zakupione Azure NetApp Files pojemności.*

1. W bloku Zarządzanie kontem usługi NetApp kliknij pulę pojemności, której rozmiar chcesz zmienić.
2. Kliknij prawym przyciskiem myszy nazwę puli pojemności lub kliknij `…` ikonę na końcu wiersza puli pojemności, aby wyświetlić menu kontekstowe.
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć pulę pojemności.    

   ![Zrzut ekranu przedstawiający opcje menu kontekstowego dla puli pojemności.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Zrzut ekranu pokazujący okno puli rozmiarów.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>Interfejs wiersza polecenia lub program PowerShell

Aby ręcznie zmienić rozmiar woluminu lub puli pojemności, można użyć [narzędzi interfejsu wiersza polecenia Azure NetApp Files](azure-netapp-files-sdk-cli.md#cli-tools), w tym interfejsu wiersza polecenia platformy Azure i Azure PowerShell.  Poniższe dwa polecenia mogą służyć do zarządzania zasobami woluminu Azure NetApp Files i puli:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Aby zarządzać zasobami Azure NetApp Files przy użyciu interfejsu wiersza polecenia platformy Azure, możesz otworzyć Azure Portal i wybrać łącze Azure **Cloud Shell** w górnej części paska menu: 

[![Zrzut ekranu pokazujący, jak uzyskać dostęp do Cloud Shell linku. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Ta akcja spowoduje otwarcie Azure Cloud Shell:

[![Zrzut ekranu przedstawiający okno Cloud Shell. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Poniższe przykłady używają poleceń do [wyświetlania](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) i [aktualizowania](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) rozmiaru woluminu:
 
[![Zrzut ekranu przedstawiający użycie programu PowerShell w celu wyświetlenia rozmiaru woluminu. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Zrzut ekranu przedstawiający użycie programu PowerShell w celu zaktualizowania rozmiaru woluminu. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Poniższe przykłady używają poleceń do [wyświetlania](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) i [aktualizowania](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) rozmiaru puli pojemności:

[![Zrzut ekranu przedstawiający Wyświetlanie rozmiaru puli pojemności przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Zrzut ekranu, który pokazuje, jak zaktualizować rozmiar puli pojemności przy użyciu programu PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatyczny  

Można utworzyć zautomatyzowany proces zarządzania zmienionym zachowaniem.

##### <a name="rest-api"></a>Interfejs API REST   

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP na podstawie zasobów, takich jak konto NetApp, Pula pojemności, woluminy i migawki. Specyfikacja interfejsu API REST dla Azure NetApp Files jest publikowana za pomocą [strony Azure NetApp Files Menedżer zasobów GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)). [Przykładowy kod do użycia z interfejsami API REST w serwisie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) GitHub.

Zobacz temat [Programowanie dla Azure NetApp Files za pomocą interfejsu API REST](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>Interfejs API REST korzystający z programu PowerShell  

Interfejs API REST dla usługi Azure NetApp Files definiuje operacje HTTP na podstawie zasobów, takich jak konto NetApp, Pula pojemności, woluminy i migawki. [Specyfikacja interfejsu API REST dla Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) jest publikowana w serwisie GitHub.

Zobacz temat [programowanie do Azure NetApp Files za pomocą interfejsu API REST przy użyciu programu PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Zarządzanie pojemnością przy użyciu ANFCapacityManager

ANFCapacityManager to aplikacja logiki platformy Azure, która zarządza regułami alertów opartych na wydajności. Automatycznie zwiększa rozmiary woluminów, aby zapobiec braku miejsca na Azure NetApp Files woluminów. Oprócz wysyłania alertów można umożliwić automatyczne zwiększenie rozmiaru puli woluminów i pojemności, aby zapobiec braku miejsca na Azure NetApp Files woluminów: 

* Opcjonalnie, gdy wolumin Azure NetApp Files osiągnie próg wykorzystanego procentu, przydział woluminu (rozmiar) zostanie zwiększony o procent określony w zakresie 10-100.  
* Jeśli zwiększenie rozmiaru woluminu przekracza pojemność puli zawierającej pojemność, rozmiar puli pojemności zostanie również zwiększony w celu uwzględnienia nowego rozmiaru woluminu.

Można skonfigurować następujące podstawowe ustawienia zarządzania pojemnością:  

* **Automatyczne zwiększanie procent zwiększa** — procent istniejącego rozmiaru woluminu, aby automatycznie rosnąć wolumin, jeśli osiągnie określony **procent pełnego progu**. Wartość 0 (zero) spowoduje wyłączenie funkcji automatyczne zwiększanie. Zalecana jest wartość z zakresu od 10 do 100.

    ![Zrzut ekranu przedstawiający okno Ustawianie procentu automatycznego przyrostu woluminu.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Często zadawane pytania 

W tej sekcji znajdują się odpowiedzi na pytania dotyczące zmiany przydziału twardego woluminu. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Czy liczba przestrzeni migawek jest przygotowana do możliwej pojemności woluminu?

Tak, wydajność używanej migawki jest uwzględniana w rozmieszczeniu zainicjowanym w woluminie. Na wypadek, gdy wolumin jest pełny, weź pod uwagę dwie opcje korygowania:

* Zmień rozmiar woluminu zgodnie z opisem w tym artykule.
* Usuń starsze migawki, aby zwolnić miejsce w woluminie hostingu.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Czy ta zmiana oznacza, że zachowanie dotyczące autodostrajania woluminu zniknie z Azure NetApp Files?

Typowym koncepcją jest to, że Azure NetApp Files *woluminy* zostaną automatycznie powiększone podczas wypełniania. Woluminy były elastycznie alokowane o rozmiarze 100 TiB, niezależnie od rzeczywistego limitu przydziału, podczas gdy *Pula wydajności* podnieśki zostanie automatycznie powiększona o 1-TIB przyrosty. Ta zmiana spowoduje rozwiązanie rozmiaru *woluminu* (widocznego i możliwego do użycia) w określonym limicie przydziału, a *Pule pojemności* nie będą już automatycznie zwiększane. Ta zmiana powoduje często odpowiednie dokładne i precyzyjne raportowanie pojemności po stronie klienta. Pozwala to uniknąć użycia pojemności "przemijające".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Czy ta zmiana ma wpływ na woluminy zreplikowane z replikacją między regionami (wersja zapoznawcza)? 

Przydział woluminu twardego nie jest wymuszany na woluminach docelowych replikacji.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Czy ta zmiana ma wpływ na metryki obecnie dostępne w Azure Monitor?

Metryki portalu i statystyki Azure Monitor będą dokładnie odzwierciedlać nowy model alokacji i użycia.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Czy ta zmiana ma wpływ na limity zasobów dla Azure NetApp Files?

Nie wprowadzono zmian w limitach zasobów dla Azure NetApp Files poza zmianami przydziału opisanymi w tym artykule.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Czy istnieje przykładowy przepływ pracy ANFCapacityManager?  

Tak. Zobacz [przykład automatyczne zwiększanie przepływu pracy](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)w witrynie GitHub.

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Czy ANFCapacityManager jest obsługiwana przez firmę Microsoft?  

[Aplikacja logiki ANFCapacityManager jest udostępniana jako-is i nie jest obsługiwana przez NetApp lub firmę Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Zachęcamy do modyfikacji w celu dopasowania do określonego środowiska lub wymagań. Należy przetestować funkcje przed wdrożeniem jej w środowiskach krytycznych lub produkcyjnych w firmie.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Jak zgłosić błąd lub przesłać żądanie funkcji dla ANFCapacityManger?
Błędy i żądania funkcji można przesłać, klikając pozycję **nowy problem** na [stronie usługi ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager/issues)w serwisie GitHub.

## <a name="next-steps"></a>Następne kroki
* [Zmienianie rozmiaru puli pojemności lub woluminu](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metryki dla usługi Azure NetApp Files](azure-netapp-files-metrics.md)