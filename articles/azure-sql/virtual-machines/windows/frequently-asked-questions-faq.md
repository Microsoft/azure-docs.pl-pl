---
title: SQL Server na Windows Virtual Machines na platformie Azure — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 014bbe4421bf00f35b2d80505cea288e75f8ca94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224677"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Często zadawane pytania dotyczące SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [SQL Server na platformie Microsoft Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Rastrow

1. **Jakie są dostępne obrazy z galerii maszyn wirtualnych SQL Server?** 

   Platforma Azure przechowuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji SQL Server we wszystkich wersjach systemu Windows i Linux. Aby uzyskać więcej informacji, zobacz kompletna lista [obrazów maszyn wirtualnych z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) i [obrazów maszyn wirtualnych](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)z systemem Linux.

1. **Czy istnieją zaktualizowane obrazy galerii maszyn wirtualnych SQL Server?**

   Co dwa miesiące SQL Server obrazy w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych aktualizacji systemu Windows i Linux. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje oznaczone jako ważne w Windows Update, w tym ważne SQL Server aktualizacje zabezpieczeń i dodatki Service Pack. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemowe. SQL Server aktualizacje zbiorcze są obsługiwane inaczej dla systemów Linux i Windows. W przypadku systemu Linux w odświeżeniu są również uwzględniane SQL Server aktualizacje zbiorcze. Jednak w tej chwili maszyny wirtualne z systemem Windows nie są aktualizowane przy użyciu aktualizacji zbiorczych SQL Server lub systemu Windows Server.

1. **Czy SQL Server obrazy maszyn wirtualnych zostaną usunięte z galerii?**

   Tak. System Azure obsługuje tylko jeden obraz na wersję główną i wydanie. Na przykład po wydaniu nowego dodatku Service Pack SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz SQL Server dla poprzedniego dodatku Service Pack zostanie natychmiast usunięty z Azure Portal. Jest on jednak nadal dostępny do aprowizacji z programu PowerShell w ciągu następnych trzech miesięcy. Po trzech miesiącach Poprzedni obraz dodatku Service Pack nie jest już dostępny. Te zasady usuwania również będą stosowane, jeśli wersja SQL Server nie będzie obsługiwana, gdy osiągnie koniec cyklu życia.


1. **Czy jest możliwe wdrożenie starszego obrazu SQL Server, który nie jest widoczny w Azure Portal?**

   Tak, przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat wdrażania maszyn wirtualnych SQL Server przy użyciu programu PowerShell, zobacz [jak zainicjować obsługę administracyjną SQL Server maszyny wirtualne w Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Czy jest możliwe utworzenie uogólnionego SQL Server obrazu SQL Server portalu Azure Marketplace i użycie go do wdrożenia maszyn wirtualnych?**

   Tak, ale musisz [zarejestrować każdą SQL Server maszynę wirtualną przy użyciu rozszerzenia SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. Podczas rejestrowania z rozszerzeniem należy również określić typ licencji dla każdej maszyny wirtualnej SQL Server.

1. **Jak mogę generalize SQL Server na maszynie wirtualnej platformy Azure i używać jej do wdrażania nowych maszyn wirtualnych?**

   Możesz wdrożyć maszynę wirtualną z systemem Windows Server (bez zainstalowanego SQL Server) i użyć procesu [Sysprep programu SQL](/sql/database-engine/install-windows/install-sql-server-using-sysprep) do uogólnienia SQL Server na maszynie wirtualnej platformy Azure (Windows) przy użyciu nośnika instalacyjnego programu SQL Server. Klienci, którzy biorą udział w programie [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3), mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z witryny Azure Marketplace SQL Server obrazu maszyny wirtualnej z odpowiednią wersją.

   Alternatywnie możesz użyć jednego z SQL Server obrazów z witryny Azure Marketplace, aby uogólnić SQL Server na maszynie wirtualnej platformy Azure. Należy pamiętać, że przed utworzeniem własnego obrazu należy usunąć następujący klucz rejestru z obrazu źródłowego. Niewykonanie tej czynności może skutkować SQL Server przeładowania folderu Bootstrap Instalatora i/lub rozszerzenia IaaS SQL w stanie Niepowodzenie.

   Ścieżka klucza rejestru:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server na maszynach wirtualnych platformy Azure, w tym tych wdrożonych z niestandardowych obrazów uogólnionych, powinny być [zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) , aby spełnić wymagania dotyczące zgodności i korzystać z funkcji opcjonalnych, takich jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. Rozszerzenie pozwala także [określić typ licencji](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) dla każdej maszyny wirtualnej SQL Server.

1. **Czy mogę użyć własnego wirtualnego dysku twardego do wdrożenia maszyny wirtualnej SQL Server?**

   Tak, ale musisz [zarejestrować każdą SQL Server maszynę wirtualną przy użyciu rozszerzenia SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych.

1. **Czy można skonfigurować konfiguracje, które nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 + SQL Server 2012)?**

   Nie. W przypadku obrazów galerii maszyn wirtualnych, które zawierają SQL Server, należy wybrać jeden z podanych obrazów za pośrednictwem Azure Portal lub za pośrednictwem [programu PowerShell](create-sql-vm-powershell.md). Istnieje jednak możliwość wdrożenia maszyny wirtualnej z systemem Windows i samodzielnego zainstalowania jej SQL Server. Następnie należy [zarejestrować maszynę wirtualną SQL Server za pomocą rozszerzenia programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) , aby zarządzać SQL Server maszyną wirtualną w Azure Portal, a także korzystać z takich funkcji, jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. 


## <a name="creation"></a>Tworzenie

1. **Jak mogę utworzyć maszynę wirtualną platformy Azure z SQL Server?**

   Najłatwiej jest utworzyć maszynę wirtualną, która zawiera SQL Server. Aby zapoznać się z samouczkiem dotyczącym rejestracji w usłudze Azure i tworzenia SQL Server maszyny wirtualnej z poziomu portalu, zobacz temat [inicjowanie SQL Server maszyny wirtualnej w Azure Portal](create-sql-vm-portal.md). Możesz wybrać obraz maszyny wirtualnej, który korzysta z licencjonowania SQL Server z opcją płatność za sekundę, lub możesz użyć obrazu, który pozwala na korzystanie z własnej licencji SQL Server. Istnieje również możliwość ręcznej instalacji SQL Server na maszynie wirtualnej z bezpłatną licencjonowaną wersją (Developer lub Express) lub przez ponowne użycie licencji lokalnej. Pamiętaj, aby [zarejestrować maszynę wirtualną SQL Server za pomocą rozszerzenia programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z takich funkcji, jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. W przypadku posiadania własnej licencji musisz mieć [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak można migrować lokalną bazę danych SQL Server do chmury?**

   Najpierw utwórz maszynę wirtualną platformy Azure z wystąpieniem SQL Server. Następnie Przeprowadź migrację lokalnych baz danych do tego wystąpienia. Aby uzyskać strategie migracji danych, zobacz [Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją trzy sposoby, aby to zrobić. Jeśli jesteś klientem z Enterprise Agreement (EA), możesz udostępnić jeden z [obrazów maszyn wirtualnych, które obsługują licencje](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), które są również znane jako BYOL. Jeśli masz program [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), możesz włączyć [korzyść użycia hybrydowego platformy Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) na istniejącym obrazie z opcją płatność zgodnie z rzeczywistym użyciem (PAYG). Możesz także skopiować nośniki instalacyjne programu SQL Server na maszynę wirtualną z systemem Windows Server, a następnie zainstalować program SQL Server na maszynie wirtualnej. Pamiętaj, aby zarejestrować SQL Server maszynę wirtualną z [rozszerzeniem](sql-agent-extension-manually-register-single-vm.md) dla funkcji takich jak Zarządzanie portalem, automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek. 


1. **Czy klient potrzebuje SQL Server licencji dostępu klienta (CAL), aby połączyć się z obrazem SQL Server z opcją płatność zgodnie z rzeczywistym użyciem działającym na platformie Azure Virtual Machines?**

   Nie. Klienci potrzebują licencji CAL, gdy korzystają z opcji "Przenieś własną licencję" i przenoszą swoją maszynę wirtualną SQL Server serwera SA/CAL na maszyny wirtualne platformy Azure. 

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Możesz łatwo przełączyć obraz galerii płatność zgodnie z rzeczywistym użyciem, aby przenieść własną licencję (BYOL), włączając [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). Obecnie ta funkcja jest dostępna tylko dla klientów korzystających z chmury publicznej i Azure Government.


1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md) nie wymaga żadnych przestojów dla SQL Server, ponieważ zmiana zacznie obowiązywać natychmiast i nie wymaga ponownego uruchomienia maszyny wirtualnej. Jednak, aby zarejestrować maszynę wirtualną SQL Server przy użyciu rozszerzenia agenta SQL IaaS, [rozszerzenie SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) jest wymaganiem wstępnym i zainstalowanie rozszerzenia SQL IaaS w trybie _pełnym_ powoduje ponowne uruchomienie usługi SQL Server. W związku z tym, jeśli rozszerzenie SQL IaaS musi zostać zainstalowane, zainstaluj je w trybie _uproszczonym_ w celu uzyskania ograniczonej funkcjonalności lub zainstaluj ją w trybie _pełnym_ w oknie obsługi. Rozszerzenie SQL IaaS zainstalowane w trybie _uproszczonym_ można uaktualnić do trybu _pełnego_ w dowolnym momencie, ale wymaga ponownego uruchomienia usługi SQL Server. 
   
1. **Czy istnieje możliwość przełączania modeli licencjonowania na SQL Server maszynę wirtualną wdrożoną przy użyciu modelu klasycznego?**

   Nie. Zmiana modeli licencjonowania nie jest obsługiwana na klasycznej maszynie wirtualnej. Możesz migrować maszynę wirtualną do modelu Azure Resource Manager i zarejestrować się w rozszerzeniu programu SQL IaaS Agent. Po zarejestrowaniu maszyny wirtualnej przy użyciu rozszerzenia programu SQL IaaS Agent zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej.

1. **Czy mogę używać witryny Azure Portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie portalem to funkcja udostępniona przez rozszerzenie programu SQL IaaS Agent, które opiera się na rozszerzeniu agenta SQL Server IaaS. W związku z tym te same ograniczenia mają zastosowanie do rozszerzenia jako rozszerzenie. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym wystąpieniem nazwanym, o ile został prawidłowo skonfigurowany. Aby uzyskać więcej informacji o tych ograniczeniach, zobacz [SQL Server rozszerzenia agenta IaaS](sql-server-iaas-agent-extension-automate-management.md). 

1. **Czy subskrypcje CSP mogą aktywować Korzyść użycia hybrydowego platformy Azure?**

   Tak, korzyść użycia hybrydowego platformy Azure jest dostępny dla subskrypcji CSP. Klienci programu CSP powinni najpierw wdrożyć obraz z opcją płatność zgodnie z rzeczywistym użyciem, a następnie [zmienić model licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md) na własny.
   
 
1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Aby uzyskać bezpłatną licencję pasywną dla aktywnej pomocniczej grupy dostępności lub wystąpienia klastra trybu failover, należy spełnić wszystkie następujące kryteria, zgodnie z opisem w [postanowień licencyjnych dotyczących produktu](https://www.microsoft.com/licensing/product-licensing/products):

   1. Masz możliwość przenoszenia [licencji](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) w ramach programu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server dla klientów ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest ono używane tylko do synchronizacji z serwerem podstawowym i utrzymywania pasywnej bazy danych w stanie rezerwy aktywnej. Jeśli obsługuje dane, takie jak raporty na klientach korzystających z aktywnych obciążeń SQL Server lub wykonywanie jakichkolwiek prac innych niż określone w warunkach produktu, musi to być płatne wystąpienie SQL Server licencjonowane. Na wystąpieniu pomocniczym dozwolone jest następujące działanie: sprawdzanie spójności bazy danych lub polecenie CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również na krótkie okresy uruchamiać jednocześnie wystąpienie podstawowe i odpowiadające mu wystąpienie odzyskiwania po awarii w celu testowania odzyskiwania po awarii co 90 dni. 
   1. Licencja na aktywną SQL Server jest objęta programem Software Assurance i umożliwia obsługę **jednego** pasywnego wystąpienia SQL Server awaryjnego, z maksymalnie taką samą ilością obliczeń jak licencjonowany aktywny serwer. 
   1. Pomocnicza maszyna wirtualna SQL Server wykorzystuje licencję [odzyskiwania po awarii](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) w Azure Portal.
   
1. **Co jest uznawane za wystąpienie pasywne?**

   Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server dla klientów ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest ono używane tylko do synchronizacji z serwerem podstawowym i utrzymywania pasywnej bazy danych w stanie rezerwy aktywnej. Jeśli obsługuje ono dane, takie jak raporty dla klientów z uruchomionymi aktywnymi obciążeniami programu SQL Server, lub wykonuje jakąkolwiek inną pracę, niż określona w warunkach produktu, musi to być płatne, licencjonowane wystąpienie programu SQL Server. Na wystąpieniu pomocniczym dozwolone jest następujące działanie: sprawdzanie spójności bazy danych lub polecenie CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również na krótkie okresy uruchamiać jednocześnie wystąpienie podstawowe i odpowiadające mu wystąpienie odzyskiwania po awarii w celu testowania odzyskiwania po awarii co 90 dni.
   

1. **W jakich scenariuszach można zastosować korzyść odzyskiwania po awarii?**

   [Przewodnik licencjonowania](https://aka.ms/sql2019licenseguide) zawiera scenariusze, w których można wykorzystać korzyść odzyskiwania po awarii. Aby uzyskać więcej informacji, zapoznaj się z warunkami użytkowania produktu lub skontaktuj się z osobami odpowiedzialnymi za licencjonowanie bądź z menedżerem konta.

1. **Które subskrypcje obsługują korzyść odzyskiwania po awarii?**

   Kompleksowe programy oferujące w ramach stałych korzyści prawa subskrypcyjne równoważne z programem Software Assurance obsługują korzyść odzyskiwania po awarii. Obejmuje to, ale nie jest ograniczony do, wartość Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS) i serwer i rejestracja w chmurze (SCE). Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi [produktów](https://www.microsoft.com/licensing/product-licensing/products) i skontaktuj się z kontaktami licencjonowania lub menedżerem kont. 

   
 ## <a name="extension"></a>Wewnętrzny

1. **Czy program zarejestruje moją maszynę wirtualną przy użyciu nowego rozszerzenia agenta IaaS SQL, aby uzyskać dodatkowe koszty?**

   Nie. Rozszerzenie programu SQL IaaS Agent umożliwia po prostu dodatkową łatwość zarządzania SQL Server na maszynie wirtualnej platformy Azure bez dodatkowych opłat. 

1. **Czy rozszerzenie usługi SQL IaaS Agent jest dostępne dla wszystkich klientów?**
 
   Tak, pod warunkiem, że maszyna wirtualna SQL Server została wdrożona w chmurze publicznej przy użyciu modelu Menedżer zasobów, a nie modelu klasycznego. Wszyscy inni klienci mogą zarejestrować się w nowym rozszerzeniu programu SQL IaaS Agent. Jednak tylko klienci z korzyścią z pakietu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) mogą korzystać z własnej licencji, aktywując [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na maszynie wirtualnej SQL Server. 

1. **Co się stanie z zasobem rozszerzenia (_Microsoft. SqlVirtualMachine_), jeśli zasób maszyny wirtualnej zostanie przeniesiony lub porzucony?** 

   Gdy zasób Microsoft. COMPUTE/VirtualMachine zostanie usunięty lub przeniesiony, skojarzony z nim zasób Microsoft. SqlVirtualMachine zostanie powiadomiony o asynchronicznym replikacji operacji.

1. **Co się stanie z maszyną wirtualną, jeśli zasób rozszerzenia (_Microsoft. SqlVirtualMachine_) zostanie porzucony?**

    Nie ma to wpływu na zasób Microsoft. COMPUTE/VirtualMachine, gdy zostanie usunięty zasób Microsoft. SqlVirtualMachine. Zmiany licencjonowania zostaną jednak domyślnie przywrócone do oryginalnego źródła obrazu. 

1. **Czy istnieje możliwość zarejestrowania samoczynnych maszyn wirtualnych SQL Server z rozszerzeniem agenta SQL IaaS?**

    Tak. Jeśli wdrożono SQL Server z własnego nośnika i zainstalowano rozszerzenie SQL IaaS, możesz zarejestrować maszynę wirtualną SQL Server z rozszerzeniem, aby uzyskać korzyści z możliwości zarządzania zapewniane przez rozszerzenie SQL IaaS.    


## <a name="administration"></a>Administracja

1. **Czy mogę zainstalować drugie wystąpienie SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik instalacyjny SQL Server znajduje się w folderze na dysku **C** . Uruchom **Setup.exe** z tej lokalizacji, aby dodać nowe wystąpienia SQL Server lub zmienić inne zainstalowane funkcje SQL Server na komputerze. Należy zauważyć, że niektóre funkcje, takie jak automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integracja Azure Key Vault, działają tylko względem wystąpienia domyślnego lub nazwanego wystąpienia, które zostało prawidłowo skonfigurowane (zobacz pytanie 3). Klienci korzystający [z programu Software Assurance w ramach korzyść użycia hybrydowego platformy Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) lub modelu licencjonowania z **płatność zgodnie z rzeczywistym** użyciem mogą instalować wiele wystąpień SQL Server na maszynie wirtualnej bez ponoszenia dodatkowych kosztów licencjonowania. Dodatkowe wystąpienia SQL Server mogą ograniczać zasoby systemowe, chyba że są poprawnie skonfigurowane. 

1. **Jaka jest maksymalna liczba wystąpień na maszynie wirtualnej?**
   SQL Server 2012 do SQL Server 2019 może obsługiwać [50 wystąpienia](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) na serwerze autonomicznym. Jest to ten sam limit, niezależnie od platformy Azure w środowisku lokalnym. Zapoznaj się z [najlepszymi rozwiązaniami](performance-guidelines-best-practices.md#multiple-instances) , aby dowiedzieć się, jak lepiej przygotować swoje środowisko. 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Po pierwsze rozliczenia skojarzone z SQL Server mogą być nadal wykonywane w zależności od modelu licencji dla maszyny wirtualnej. Po drugie, zgodnie z opisem w poprzedniej odpowiedzi, istnieją funkcje, które opierają się na [rozszerzeniu SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). W przypadku odinstalowania wystąpienia domyślnego bez usuwania rozszerzenia IaaS, rozszerzenie nadal szuka wystąpienia domyślnego i może generować błędy dziennika zdarzeń. Te błędy pochodzą z następujących dwóch źródeł: **Microsoft SQL Server zarządzania poświadczeniami** i **Microsoft SQL Server agenta IaaS**. Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   W przypadku podjęcia decyzji o odinstalowaniu wystąpienia domyślnego należy również odinstalować także [rozszerzenie Agent SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) . 

1. **Czy można użyć nazwanego wystąpienia SQL Server z rozszerzeniem IaaS?**
   
   Tak, jeśli nazwane wystąpienie jest jedynym wystąpieniem w SQL Server i jeśli oryginalne wystąpienie domyślne zostało [odinstalowane prawidłowo](sql-server-iaas-agent-extension-automate-management.md#named-instance-support). Jeśli nie ma wystąpienia domyślnego i istnieje wiele wystąpień nazwanych na jednej maszynie wirtualnej SQL Server, instalacja rozszerzenia agenta SQL Server IaaS nie powiedzie się.  

1. **Czy mogę usunąć SQL Server i powiązane z nimi rozliczanie licencji z poziomu maszyny wirtualnej SQL Server?**

   Tak, ale musisz podjąć dodatkowe kroki, aby uniknąć naliczania opłat za wystąpienie SQL Server zgodnie z opisem w temacie [wskazówki dotyczące cen](pricing-guidance.md). Jeśli chcesz całkowicie usunąć wystąpienie SQL Server, możesz przeprowadzić migrację do innej maszyny wirtualnej platformy Azure bez SQL Server wstępnie zainstalowanej na maszynie wirtualnej i usunąć bieżącą SQL Server maszynę wirtualną. Jeśli chcesz zachować maszynę wirtualną, ale zatrzymać SQL Server rozliczeń, wykonaj następujące kroki: 

   1. Wykonaj kopię zapasową wszystkich danych, w tym systemowych baz danych, w razie potrzeby. 
   1. Odinstaluj SQL Server całkowicie, łącznie z rozszerzeniem SQL IaaS (jeśli istnieje).
   1. Zainstaluj bezpłatną [wersję programu SQL Express](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Zarejestruj się w rozszerzeniu SQL IaaS Agent w [trybie uproszczonym](sql-agent-extension-manually-register-single-vm.md).
   1. obowiązkowe Wyłącz usługę Express SQL Server, wyłączając uruchamianie usługi. 

1. **Czy mogę używać witryny Azure Portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie portalem jest udostępniane przez rozszerzenie Agent IaaS SQL, które opiera się na rozszerzeniu agenta SQL Server IaaS. W związku z tym te same ograniczenia mają zastosowanie do rozszerzenia jako rozszerzenie. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym wystąpieniem nazwanym, dopóki jego konfiguracja jest prawidłowa. Aby uzyskać więcej informacji, zobacz [SQL Server rozszerzenia agenta IaaS](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak mogę zmienić na inną wersję/wydanie SQL Server na maszynie wirtualnej platformy Azure?**

   Klienci mogą zmienić wersję/edycję programu SQL Server przy użyciu nośnika instalacyjnego zawierającego odpowiednią wersję/edycję programu SQL Server. Po zmianie edycji za pomocą witryny Azure Portal zmodyfikuj właściwość edycji maszyny wirtualnej w celu dokładnego odzwierciedlenia rozliczeń za tę maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Zmiana wersji maszyny wirtualnej SQL Server](change-sql-server-edition.md). Rozliczenia dla różnych wersji programu SQL Server są takie same, dlatego po zmianie wersji programu SQL Server nie są wymagane żadne dodatkowe akcje.

1. **Gdzie mogę uzyskać nośnik instalacyjny, aby zmienić edycję lub wersję programu SQL Server?**

   Klienci, którzy biorą udział w programie [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z witryny Azure Marketplace SQL Server obrazu maszyny wirtualnej z odpowiednią wersją.
   
1. **Jak wygląda zastosowanie aktualizacji i dodatków Service Pack na maszynie wirtualnej z programem SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. Dla systemu operacyjnego możesz ręcznie zastosować aktualizacje systemu Windows lub włączyć usługę harmonogramu o nazwie [Automatyczne stosowanie poprawek](automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

1. **Czy mogę uaktualnić wystąpienie SQL Server 2008/2008 R2 po zarejestrowaniu go przy użyciu rozszerzenia programu SQL IaaS Agent?**

   Jeśli system operacyjny to Windows Server 2008 R2 lub nowszy, tak. Możesz użyć dowolnego nośnika instalacyjnego, aby uaktualnić wersję i wydanie SQL Server, a następnie uaktualnić [tryb rozszerzenia SQL IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes)) _bez agenta_ do _pełnego_. Dzięki temu będzie można uzyskać dostęp do wszystkich korzyści z rozszerzenia IaaS języka SQL, takich jak Zarządzanie portalem, zautomatyzowane kopie zapasowe i automatyczne stosowanie poprawek. Jeśli wersja systemu operacyjnego to Windows Server 2008, obsługiwany jest tylko tryb noagent. 

1. **Jak mogę uzyskać bezpłatne rozszerzone aktualizacje zabezpieczeń dla moich wystąpień programu SQL Server 2008 i SQL Server 2008 R2, dla których kończy się wsparcie?**

   Możesz uzyskać [bezpłatne rozszerzone aktualizacje zabezpieczeń](sql-server-2008-extend-end-of-support.md) , przenosząc SQL Server na maszynę wirtualną platformy Azure. Aby uzyskać więcej informacji, zobacz temat dotyczący [opcji zakończenia pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Ogólne

1. **Czy SQL Server wystąpienia klastra trybu failover (FCI) są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Wystąpienie klastra trybu failover można zainstalować przy użyciu [udziałów plików w warstwie Premium (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) lub [bezpośrednich miejsc do magazynowania (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) dla podsystemu magazynowania. Udziały plików w warstwie Premium zapewniają możliwości IOPS i przepływności, które będą spełniały potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z operacji we/wy należy rozważyć użycie funkcji bezpośrednie miejsca do magazynowania w oparciu o zarządzane lub niezwykle duże dyski. Alternatywnie możesz użyć rozwiązań do obsługi klastrów lub magazynu innych firm, jak opisano w temacie [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > _W tej_ chwili [SQL Server rozszerzenie agenta IaaS](sql-server-iaas-agent-extension-automate-management.md) nie jest obsługiwane dla SQL Server FCI na platformie Azure. Zalecamy odinstalowanie _pełnego_ rozszerzenia z maszyn wirtualnych, które uczestniczą w FCI, i zamiast tego zainstaluj rozszerzenie w trybie _uproszczonym_ . To rozszerzenie obsługuje funkcje, takie jak automatyczne tworzenie kopii zapasowych i stosowanie poprawek oraz niektóre funkcje portalu dla SQL Server. Te funkcje nie będą działały dla SQL Server maszyn wirtualnych po odinstalowaniu _pełnego_ agenta.

1. **Jaka jest różnica między maszynami wirtualnymi SQL Server a usługą SQL Database?**

   Ze względu na to, że Uruchamianie SQL Server na maszynie wirtualnej platformy Azure nie różni się od uruchamiania SQL Server w zdalnym centrum danych. W przeciwieństwie [Azure SQL Database](../../database/sql-database-paas-overview.md) oferuje bazę danych jako usługę. W przypadku SQL Database nie masz dostępu do maszyn, które obsługują bazy danych. Aby zapoznać się z pełnym porównaniem, zobacz [Wybieranie opcji SQL Server w chmurze: baza danych Azure SQL (PaaS) lub SQL Server na maszynach wirtualnych platformy Azure (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Jak mogę zainstalować narzędzia SQL Data Tools na mojej maszynie wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia SQL Data Tools z [Microsoft SQL Server Data Tools — analiza biznesowa dla Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Czy transakcje rozproszone z usługą MSDTC są obsługiwane na maszynach wirtualnych SQL Server?**
   
    Tak. Lokalna usługa DTC jest obsługiwana w przypadku SQL Server 2016 z dodatkiem SP2 i nowszych. Jednak aplikacje muszą być testowane w przypadku używania zawsze włączonych grup dostępności, ponieważ transakcje w trakcie pracy w trybie failover będą kończyć się niepowodzeniem i muszą zostać ponowione. Klastrowane usługi DTC są dostępne począwszy od systemu Windows Server 2019. 
    
1. **Czy maszyna wirtualna usługi Azure SQL przenosi lub zapisuje dane klienta poza regionem?**

   Nie. W rzeczywistości maszyna wirtualna Azure SQL i rozszerzenie SQL IaaS Agent nie przechowują żadnych danych klienta.

## <a name="sql-server-iaas-agent-extension"></a>Rozszerzenie agenta IaaS w programie SQL Server

1. **Czy należy zarejestrować moją SQL Serverą maszynę wirtualną z poziomu obrazu SQL Server w witrynie Azure Marketplace?**

   Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne obsługiwane przez obrazy SQL Server w portalu Azure Marketplace. Rejestracja przy użyciu rozszerzenia jest wymagana tylko wtedy, gdy maszyna wirtualna *nie* została zainicjowana z obrazów SQL Server w portalu Azure Marketplace i SQL Server została zainstalowana samoobsługowo.

1. **Czy rozszerzenie usługi SQL IaaS Agent jest dostępne dla wszystkich klientów?** 

   Tak. Klienci powinni rejestrować SQL Server maszyny wirtualne przy użyciu rozszerzenia, jeśli nie korzystają z obrazu SQL Server z witryny Azure Marketplace, a zamiast tego są samoinstalujące się SQL Server lub mają swój niestandardowy dysk VHD. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, Enterprise Agreement i dostawca rozwiązań w chmurze) mogą być rejestrowane przy użyciu rozszerzenia programu SQL IaaS Agent.

1. **Jaki jest domyślny tryb zarządzania podczas rejestrowania w rozszerzeniu programu SQL IaaS Agent?**

   Domyślny tryb zarządzania podczas rejestrowania w rozszerzeniu programu SQL IaaS Agent jest *lekki*. Jeśli właściwość Zarządzanie SQL Server nie jest ustawiona podczas rejestrowania z rozszerzeniem, tryb zostanie ustawiony jako uproszczony, a usługa SQL Server nie zostanie uruchomiona ponownie. Zaleca się, aby najpierw zarejestrować się w rozszerzeniu programu SQL IaaS Agent w trybie uproszczonym, a następnie przeprowadzić uaktualnienie do pełnej wersji w oknie obsługi. Analogicznie, domyślne zarządzanie jest również lekkie w przypadku korzystania z [funkcji automatycznej rejestracji](sql-agent-extension-automatic-registration-all-vms.md).

1. **Jakie są wymagania wstępne, które należy zarejestrować w rozszerzeniu programu SQL IaaS Agent?**

   Nie ma wymagań wstępnych do zarejestrowania się w rozszerzeniu programu SQL IaaS Agent innym niż SQL Server zainstalowane na maszynie wirtualnej. Należy pamiętać, że jeśli rozszerzenie programu SQL IaaS Agent jest zainstalowane w trybie pełnym, usługa SQL Server zostanie uruchomiona ponownie, Dlatego zalecane jest wykonanie okna obsługi.

1. **Czy zostanie zarejestrowana przy użyciu rozszerzenia agenta SQL IaaS, Zainstaluj agenta na mojej maszynie wirtualnej?**

   Tak, Rejestracja przy użyciu rozszerzenia agenta SQL IaaS w trybie pełnego zarządzania powoduje zainstalowanie agenta na maszynie wirtualnej. Zarejestrowanie w trybie uproszczonym lub brak agenta nie jest dozwolone. 

   Rejestracja przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym powoduje jedynie skopiowanie *plików binarnych* rozszerzenia agenta SQL IaaS do maszyny wirtualnej, a agent nie jest instalowany. Te pliki binarne są następnie używane do instalowania agenta, gdy tryb zarządzania zostanie uaktualniony do wersji pełnej.


1. **Czy program zostanie zarejestrowany przy użyciu SQL Server ponownego uruchomienia rozszerzenia agenta SQL na mojej maszynie wirtualnej?**

   Jest to zależne od trybu określonego podczas rejestracji. W przypadku określenia trybu uproszczonego lub noagent usługa SQL Server nie zostanie uruchomiona ponownie. Jednak określenie trybu zarządzania jako pełny spowoduje ponowne uruchomienie usługi SQL Server. Funkcja automatycznego rejestrowania rejestruje maszyny wirtualne SQL Server w trybie uproszczonym, chyba że wersja systemu Windows Server to 2008, w takim przypadku SQL Server maszyna wirtualna zostanie zarejestrowana w trybie noagent. 

1. **Jaka jest różnica między trybami zarządzania Lightweight i noagent podczas rejestrowania się w rozszerzeniu programu SQL IaaS Agent?** 

   Tryb zarządzania bez agenta jest jedynym dostępnym trybem zarządzania dla SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. W przypadku wszystkich nowszych wersji systemu Windows Server dwa dostępne tryby zarządzania są lekkie i pełne. 

   Tryb noagent wymaga, aby właściwości SQL Server wersji i wydania zostały ustawione przez klienta. Tryb uproszczony wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i wydania wystąpienia SQL Server.

1. **Czy można zarejestrować się przy użyciu rozszerzenia SQL IaaS Agent bez określania typu licencji SQL Server?**

   Nie. Typ licencji SQL Server nie jest właściwością opcjonalną podczas rejestrowania się w rozszerzeniu programu SQL IaaS Agent. Musisz ustawić typ licencji SQL Server na płatność zgodnie z rzeczywistym użyciem lub Korzyść użycia hybrydowego platformy Azure podczas rejestrowania się w rozszerzeniu SQL IaaS Agent we wszystkich trybach zarządzania (noagent, Lightweight i Full). Jeśli masz dowolną z bezpłatnych wersji SQL Server zainstalowanych, takich jak deweloper lub wersja ewaluacyjna, musisz zarejestrować się, korzystając z licencjonowania z opcją płatność zgodnie z rzeczywistym użyciem. Korzyść użycia hybrydowego platformy Azure jest dostępna tylko dla płatnych wersji SQL Server, takich jak wersje Enterprise i Standard.

1. **Czy mogę uaktualnić rozszerzenie SQL Server IaaS z trybu noagent do trybu pełnego?**

   Nie. Uaktualnianie trybu zarządzania do wersji pełnej lub lekkiej nie jest dostępne dla trybu noagent. Jest to ograniczenie techniczne systemu Windows Server 2008. Musisz najpierw uaktualnić system operacyjny do wersji Windows Server 2008 R2 lub nowszej, a następnie można przeprowadzić uaktualnienie do trybu pełnego zarządzania. 

1. **Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu uproszczonego do trybu pełnego?**

   Tak. Uaktualnianie trybu zarządzania z lekkich do pełnych jest obsługiwane za pośrednictwem Azure PowerShell lub Azure Portal. Spowoduje to wyzwolenie ponownego uruchomienia usługi SQL Server.

1. **Czy można obniżyć SQL Server rozszerzenie IaaS z trybu pełnego do noagent lub Lightweight Management Mode?**

   Nie. Obniżenie poziomu trybu zarządzania rozszerzeniami SQL Server IaaS nie jest obsługiwane. Trybu zarządzania nie można zmienić z trybu pełnego na Lightweight lub noagent i nie można go zmienić z trybu uproszczonego na tryb noagent. 

   Aby zmienić tryb zarządzania z pełnego zarządzania, należy [wyrejestrować](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server maszynę wirtualną z rozszerzenia programu SQL IaaS Agent przez usunięcie _zasobu_ maszyny wirtualnej SQL i ponowne zarejestrowanie SQL Server maszynie wirtualnej z rozszerzeniem agenta SQL IaaS ponownie w innym trybie zarządzania.

1. **Czy można zarejestrować się przy użyciu rozszerzenia SQL IaaS Agent w Azure Portal?**

   Nie. Rejestracja przy użyciu rozszerzenia agenta SQL IaaS nie jest dostępna w Azure Portal. Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS jest obsługiwane tylko w interfejsie wiersza polecenia platformy Azure lub w Azure PowerShell. 

1. **Czy można zarejestrować maszynę wirtualną za pomocą rozszerzenia programu SQL IaaS Agent przed zainstalowaniem SQL Server?**

   Nie. Maszyna wirtualna musi mieć co najmniej jedno wystąpienie SQL Server (aparat bazy danych), aby można było pomyślnie zarejestrować się w rozszerzeniu agenta SQL IaaS. Jeśli na maszynie wirtualnej nie ma SQL Server wystąpienia, nowy zasób Microsoft. SqlVirtualMachine będzie w stanie niepowodzenia.

1. **Czy można zarejestrować maszynę wirtualną za pomocą rozszerzenia programu SQL IaaS Agent, jeśli istnieje wiele wystąpień SQL Server?**

   Tak, pod warunkiem, że na maszynie wirtualnej jest domyślne wystąpienie. Rozszerzenie agenta SQL IaaS zarejestruje tylko jedno wystąpienie SQL Server (aparatu bazy danych). Rozszerzenie programu SQL IaaS Agent zarejestruje domyślne wystąpienie SQL Server w przypadku wielu wystąpień.

1. **Czy można zarejestrować SQL Server wystąpienie klastra trybu failover z rozszerzeniem agenta SQL IaaS?**

   Tak. SQL Server wystąpienia klastra trybu failover na maszynie wirtualnej platformy Azure można zarejestrować przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym. Niemniej jednak SQL Server wystąpienia klastra trybu failover nie można uaktualnić do trybu pełnego zarządzania.

1. **Czy można zarejestrować moją maszynę wirtualną przy użyciu rozszerzenia agenta SQL IaaS, jeśli jest skonfigurowana zawsze włączona Grupa dostępności?**

   Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia SQL Server na maszynie wirtualnej platformy Azure przy użyciu rozszerzenia agenta SQL IaaS w przypadku uczestnictwa w konfiguracji grupy dostępności zawsze włączone.

1. **Jaki jest koszt rejestrowania w rozszerzeniu SQL IaaS agent lub z uaktualnieniem do trybu pełnej możliwości zarządzania?**

   Brak. Nie istnieje opłata skojarzona z rejestracją przy użyciu rozszerzenia agenta SQL IaaS lub z użyciem jednego z trzech trybów zarządzania. Zarządzanie maszyną wirtualną SQL Server przy użyciu rozszerzenia jest całkowicie bezpłatne. 

1. **Jaki jest wpływ na wydajność korzystania z różnych trybów zarządzania?**

   Nie ma to wpływu na użycie trybu *noagent* i *uproszczonego* zarządzania. W przypadku korzystania z trybu *pełnego* zarządzania z dwóch usług, które są zainstalowane w systemie operacyjnym, ma minimalny wpływ. Mogą one być monitorowane za pośrednictwem Menedżera zadań i widoczne w wbudowanej konsoli usług systemu Windows. 

   Nazwy następujących usług:
   - `SqlIaaSExtensionQuery` (Nazwa wyświetlana `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Nazwa wyświetlana `Microsoft SQL Server IaaS Agent` )

1. **Jak mogę usunąć rozszerzenie?**

   Usuń rozszerzenie, [wyrejestrując](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) maszynę wirtualną SQL Server z rozszerzenia programu SQL IaaS Agent. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Windows](create-sql-vm-portal.md)
* [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Najlepsze rozwiązania w zakresie wydajności SQL Server na platformie Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines](application-patterns-development-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Często zadawane pytania (Linux)](../linux/frequently-asked-questions-faq.md)
* [Dokumentacja SQL Server on Linux](/sql/linux/sql-server-linux-overview)
