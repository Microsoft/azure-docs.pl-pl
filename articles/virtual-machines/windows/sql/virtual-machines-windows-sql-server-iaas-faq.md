---
title: SQL Server na Windows Virtual Machines na platformie Azure — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 0d6d69b82e80ff9bc33e49302cf59766b9c2e8d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81270829"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Często zadawane pytania dotyczące programu SQL Server uruchomionego na maszynach wirtualnych z systemem Windows na platformie Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [SQL Server na Windows Virtual Machines na platformie Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> W tym artykule omówiono problemy dotyczące SQL Server na maszynach wirtualnych z systemem Windows. Jeśli używasz maszyn wirtualnych SQL Server on Linux, zobacz [często zadawane pytania dotyczące systemu Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Rastrow

1. **Jakie są dostępne obrazy z galerii maszyn wirtualnych SQL Server?** 

   Platforma Azure przechowuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji SQL Server we wszystkich wersjach systemu Windows i Linux. Aby uzyskać więcej informacji, zobacz kompletna lista [obrazów maszyn wirtualnych z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) i [obrazów maszyn wirtualnych](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)z systemem Linux.

1. **Czy istnieją zaktualizowane obrazy galerii maszyn wirtualnych SQL Server?**

   Co dwa miesiące SQL Server obrazy w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych aktualizacji systemu Windows i Linux. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje oznaczone jako ważne w Windows Update, w tym ważne SQL Server aktualizacje zabezpieczeń i dodatki Service Pack. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemowe. SQL Server aktualizacje zbiorcze są obsługiwane inaczej dla systemów Linux i Windows. W przypadku systemu Linux w odświeżeniu są również uwzględniane SQL Server aktualizacje zbiorcze. Jednak w tej chwili maszyny wirtualne z systemem Windows nie są aktualizowane przy użyciu aktualizacji zbiorczych SQL Server lub systemu Windows Server.

1. **Czy SQL Server obrazy maszyn wirtualnych zostaną usunięte z galerii?**

   Tak. System Azure obsługuje tylko jeden obraz na wersję główną i wydanie. Na przykład po wydaniu nowego dodatku Service Pack SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz SQL Server dla poprzedniego dodatku Service Pack zostanie natychmiast usunięty z Azure Portal. Jest on jednak nadal dostępny do aprowizacji z programu PowerShell w ciągu następnych trzech miesięcy. Po trzech miesiącach Poprzedni obraz dodatku Service Pack nie jest już dostępny. Te zasady usuwania również będą stosowane, jeśli wersja SQL Server nie będzie obsługiwana, gdy osiągnie koniec cyklu życia.


1. **Czy jest możliwe wdrożenie starszego obrazu SQL Server, który nie jest widoczny w Azure Portal?**

   Tak, przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat wdrażania maszyn wirtualnych SQL Server przy użyciu programu PowerShell, zobacz [jak zainicjować obsługę administracyjną SQL Server maszyny wirtualne w Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Jak mogę uogólnić SQL Server na maszynie wirtualnej platformy Azure i używać jej do wdrażania nowych maszyn wirtualnych?**

   Możesz wdrożyć maszynę wirtualną z systemem Windows Server (bez zainstalowanych na niej SQL Server) i użyć procesu [Sysprep programu SQL](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) do uogólnienia SQL Server na maszynie wirtualnej platformy Azure (Windows) przy użyciu nośnika instalacyjnego programu SQL Server. Klienci posiadający [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) mogą uzyskać nośnik instalacyjny z [centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z poziomu obrazu maszyny wirtualnej SQL Server Marketplace z odpowiednią wersją.

   Alternatywnie możesz użyć jednego z SQL Server obrazów w portalu Azure Marketplace, aby uogólnić SQL Server na maszynie wirtualnej platformy Azure. Należy pamiętać, że przed utworzeniem własnego obrazu należy usunąć następujący klucz rejestru z obrazu źródłowego. Niewykonanie tej czynności może skutkować SQL Server przeładowania folderu Bootstrap Instalatora i/lub rozszerzenia IaaS SQL w stanie Niepowodzenie.

   Ścieżka klucza rejestru:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Zaleca się, aby wszystkie SQL Server maszyny wirtualne platformy Azure, w tym te wdrożone z niestandardowymi obrazami uogólnionymi, były [zarejestrowane za pomocą dostawcy odkursu maszyny wirtualnej SQL](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash) w celu spełnienia wymagań dotyczących zgodności oraz do korzystania z funkcji opcjonalnych, takich jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. Pozwala także [określić typ licencji](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal) dla każdej maszyny wirtualnej SQL Server.

1. **Czy mogę użyć własnego wirtualnego dysku twardego do wdrożenia maszyny wirtualnej SQL Server?**

   Tak, ale musisz [zarejestrować każdą SQL Server maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z takich funkcji, jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych.

1. **Czy można skonfigurować konfiguracje, które nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 + SQL Server 2012)?**

   Nie. W przypadku obrazów galerii maszyn wirtualnych, które zawierają SQL Server, należy wybrać jeden z podanych obrazów za pośrednictwem Azure Portal lub za pośrednictwem [programu PowerShell](virtual-machines-windows-ps-sql-create.md). Istnieje jednak możliwość wdrożenia maszyny wirtualnej z systemem Windows i samodzielnego zainstalowania jej SQL Server. Następnie należy [zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z takich funkcji, jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. 


## <a name="creation"></a>Tworzenie

1. **Jak mogę utworzyć maszynę wirtualną platformy Azure z SQL Server?**

   Najłatwiej jest utworzyć maszynę wirtualną, która zawiera SQL Server. Aby zapoznać się z samouczkiem dotyczącym rejestracji w usłudze Azure i tworzenia SQL Server maszyny wirtualnej z poziomu portalu, zobacz temat [inicjowanie SQL Server maszyny wirtualnej w Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Możesz wybrać obraz maszyny wirtualnej, który korzysta z licencjonowania SQL Server z opcją płatność za sekundę, lub możesz użyć obrazu, który pozwala na korzystanie z własnej licencji SQL Server. Istnieje również możliwość ręcznej instalacji SQL Server na maszynie wirtualnej z bezpłatną licencjonowaną wersją (Developer lub Express) lub przez ponowne użycie licencji lokalnej. Pamiętaj, aby [zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) , aby zarządzać SQL Server maszyną wirtualną w portalu, a także korzystać z takich funkcji, jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. W przypadku posiadania własnej licencji musisz mieć [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/). Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak można migrować lokalną bazę danych SQL Server do chmury?**

   Najpierw utwórz maszynę wirtualną platformy Azure z wystąpieniem SQL Server. Następnie Przeprowadź migrację lokalnych baz danych do tego wystąpienia. Aby uzyskać strategie migracji danych, zobacz [Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją trzy sposoby, aby to zrobić. Jeśli jesteś klientem z umową Enterprise Agreement (EA), możesz udostępnić jeden z [obrazów maszyn wirtualnych, które obsługują licencje](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), które są również znane jako BYOL. Jeśli masz program [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), możesz włączyć [korzyść użycia hybrydowego platformy Azure](virtual-machines-windows-sql-ahb.md) na istniejącym obrazie z opcją płatność zgodnie z rzeczywistym użyciem (PAYG). Można też skopiować nośnik instalacyjny SQL Server na maszynę wirtualną z systemem Windows Server, a następnie zainstalować SQL Server na maszynie wirtualnej. Należy pamiętać o zarejestrowaniu maszyny wirtualnej SQL Server przy użyciu [dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md) dla funkcji takich jak Zarządzanie portalem, automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek. 

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Możesz łatwo przełączyć obraz galerii płatność zgodnie z rzeczywistym użyciem, aby przenieść własną licencję (BYOL), włączając [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](virtual-machines-windows-sql-ahb.md). Obecnie ta funkcja jest dostępna tylko dla klientów chmury publicznej.

1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](virtual-machines-windows-sql-ahb.md) nie wymaga żadnych przestojów dla SQL Server, ponieważ zmiana zacznie obowiązywać natychmiast i nie wymaga ponownego uruchomienia maszyny wirtualnej. Jednak, aby zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL Server, [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) jest wymaganiem wstępnym i zainstalowanie rozszerzenia SQL IaaS w trybie _pełnym_ powoduje ponowne uruchomienie usługi SQL Server. W związku z tym, jeśli rozszerzenie SQL IaaS musi zostać zainstalowane, zainstaluj je w trybie _uproszczonym_ w celu uzyskania ograniczonej funkcjonalności lub zainstaluj ją w trybie _pełnym_ w oknie obsługi. Rozszerzenie SQL IaaS zainstalowane w trybie _uproszczonym_ można uaktualnić do trybu _pełnego_ w dowolnym momencie, ale wymaga ponownego uruchomienia usługi SQL Server. 
   
1. **Czy jest możliwe przełączenie modelu licencjonowania na SQL Server maszyny wirtualnej wdrożonej przy użyciu modelu klasycznego?**

   Nie. Zmiana modelu licencjonowania nie jest obsługiwana na klasycznej maszynie wirtualnej. Możesz migrować maszynę wirtualną do modelu Azure Resource Manager i zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL Server. Po zarejestrowaniu maszyny wirtualnej przy użyciu dostawcy zasobów maszyny wirtualnej SQL Server zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej.

1. **Czy mogę używać Azure Portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie portalem to funkcja udostępniana przez SQL Server dostawcę zasobów maszyny wirtualnej, która opiera się na rozszerzeniu SQL Server agenta IaaS. W związku z tym te same ograniczenia dotyczą dostawcy zasobów jako rozszerzenie. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym wystąpieniem nazwanym, o ile został prawidłowo skonfigurowany. Aby uzyskać więcej informacji o tych ograniczeniach, zobacz [SQL Server rozszerzenia agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Czy subskrypcje CSP mogą aktywować Korzyść użycia hybrydowego platformy Azure?**

   Tak, Korzyść użycia hybrydowego platformy Azure jest dostępny dla subskrypcji dostawcy CSP. Klienci programu CSP powinni najpierw wdrożyć obraz z opcją płatność zgodnie z rzeczywistym użyciem, a następnie [zmienić model licencjonowania](virtual-machines-windows-sql-ahb.md) na własny.
   
 
1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Aby uzyskać bezpłatną licencję pasywną dla aktywnej pomocniczej grupy dostępności lub wystąpienia klastra trybu failover, należy spełnić wszystkie następujące kryteria, zgodnie z opisem w [postanowień licencyjnych dotyczących produktu](https://www.microsoft.com/licensing/product-licensing/products):

   1. Masz możliwość przenoszenia [licencji](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) w ramach programu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Wystąpienie SQL Server pasywne nie obsługuje SQL Server danych do klientów ani nie uruchamia aktywnych obciążeń SQL Server. Jest on używany tylko do synchronizacji z serwerem podstawowym i w inny sposób utrzymują pasywną bazę danych w stanie rezerwy aktywnej. Jeśli obsługuje dane, takie jak raporty na klientach korzystających z aktywnych obciążeń SQL Server lub wykonywanie jakichkolwiek prac innych niż określone w warunkach produktu, musi to być płatne wystąpienie SQL Server licencjonowane. Następujące działanie jest dozwolone w wystąpieniu pomocniczym: sprawdzanie spójności bazy danych lub CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również uruchomić podstawowe i odpowiednie wystąpienie odzyskiwania po awarii dla krótkich okresów testowania odzyskiwania po awarii co 90 dni. 
   1. Licencja na aktywną SQL Server jest objęta programem Software Assurance i umożliwia obsługę **jednego** pasywnego wystąpienia SQL Server awaryjnego, z maksymalnie taką samą ilością obliczeń jak licencjonowany aktywny serwer. 
   1. Pomocnicza maszyna wirtualna SQL Server wykorzystuje licencję [odzyskiwania po awarii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) w Azure Portal.
   
1. **Co jest uznawane za wystąpienie pasywne?**

   Wystąpienie SQL Server pasywne nie obsługuje SQL Server danych do klientów ani nie uruchamia aktywnych obciążeń SQL Server. Jest on używany tylko do synchronizacji z serwerem podstawowym i w inny sposób utrzymują pasywną bazę danych w stanie rezerwy aktywnej. Jeśli obsługuje dane, takie jak raporty na klientach korzystających z aktywnych obciążeń SQL Server lub wykonywanie jakichkolwiek prac innych niż określone w warunkach produktu, musi to być płatne wystąpienie SQL Server licencjonowane. Następujące działanie jest dozwolone w wystąpieniu pomocniczym: sprawdzanie spójności bazy danych lub CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również uruchomić podstawowe i odpowiednie wystąpienie odzyskiwania po awarii dla krótkich okresów testowania odzyskiwania po awarii co 90 dni.
   

1. **Jakie scenariusze mogą korzystać z korzyści z odzyskiwania po awarii?**

   [Przewodnik licencjonowania](https://aka.ms/sql2019licenseguide) zawiera scenariusze, w których można wykorzystać korzyść odzyskiwania po awarii. Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi produktów i skontaktuj się z kontaktami licencjonowania lub z menedżerem konta.

1. **Które subskrypcje obsługują korzyść odzyskiwania po awarii (DR)?**

   Kompleksowe programy oferujące równoważne prawa subskrypcyjne programu Software Assurance jako stałe korzyści wspierają korzyść odzyskiwania po awarii. Obejmuje to. ale nie jest ograniczony do, wartość Open Value (OV), Open Value Subscription (OVS), Umowa Enterprise (EA), Enterprise Subscription Agreement (EAS) i serwer i rejestracja w chmurze (SCE). Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi [produktów](https://www.microsoft.com/licensing/product-licensing/products) i skontaktuj się z kontaktami licencjonowania lub menedżerem kont. 

   
 ## <a name="resource-provider"></a>Dostawca zasobów

1. **Czy program zarejestruje moją maszynę wirtualną za pomocą nowego dostawcy zasobów maszyny wirtualnej SQL Server uzyskać dodatkowych kosztów?**

   Nie. Dostawca zasobów SQL Server VM umożliwia jedynie dodatkową możliwość zarządzania SQL Server na maszynie wirtualnej platformy Azure bez dodatkowych opłat. 

1. **Czy SQL Server dostawcy zasobów maszyny wirtualnej jest dostępny dla wszystkich klientów?**
 
   Tak, pod warunkiem, że maszyna wirtualna SQL Server została wdrożona w chmurze publicznej przy użyciu modelu Menedżer zasobów, a nie modelu klasycznego. Wszyscy inni klienci mogą zarejestrować się przy użyciu nowego dostawcy zasobów maszyny wirtualnej SQL Server. Jednak tylko klienci z korzyścią z pakietu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) mogą korzystać z własnej licencji, aktywując [korzyść użycia hybrydowego platformy Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na maszynie wirtualnej SQL Server. 

1. **Co się stanie z zasobem dostawcy zasobów (_Microsoft. SqlVirtualMachine_), jeśli zasób maszyny wirtualnej zostanie przeniesiony lub porzucony?** 

   Gdy zasób Microsoft. COMPUTE/VirtualMachine zostanie usunięty lub przeniesiony, skojarzony z nim zasób Microsoft. SqlVirtualMachine zostanie powiadomiony o asynchronicznym replikacji operacji.

1. **Co się stanie z maszyną wirtualną, jeśli zasób dostawcy zasobów (_Microsoft. SqlVirtualMachine_) zostanie porzucony?**

    Nie ma to wpływu na zasób Microsoft. COMPUTE/VirtualMachine, gdy zostanie usunięty zasób Microsoft. SqlVirtualMachine. Zmiany licencjonowania zostaną jednak domyślnie przywrócone do oryginalnego źródła obrazu. 

1. **Czy istnieje możliwość zarejestrowania samoczynnych maszyn wirtualnych SQL Server z dostawcą zasobów maszyny wirtualnej SQL Server?**

    Tak. Jeśli wdrożono SQL Server z własnego nośnika i zainstalowano rozszerzenie SQL IaaS, można zarejestrować SQL Serverą maszynę wirtualną przy użyciu dostawcy zasobów, aby uzyskać korzyści z zarządzania udostępniane przez rozszerzenie SQL IaaS. Nie można jednak skonwertować samodzielnie wdrożonej maszyny wirtualnej SQL Server na płatność zgodnie z rzeczywistym użyciem.


   


## <a name="administration"></a>Administracja

1. **Czy mogę zainstalować drugie wystąpienie SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik instalacyjny SQL Server znajduje się w folderze na dysku **C** . Uruchom **plik Setup. exe** z tej lokalizacji, aby dodać nowe wystąpienia SQL Server lub zmienić inne zainstalowane funkcje SQL Server na komputerze. Należy zauważyć, że niektóre funkcje, takie jak automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integracja Azure Key Vault, działają tylko względem wystąpienia domyślnego lub nazwanego wystąpienia, które zostało prawidłowo skonfigurowane (zobacz pytanie 3). 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Po pierwsze rozliczenia skojarzone z SQL Server mogą być nadal wykonywane w zależności od modelu licencji dla maszyny wirtualnej. Po drugie, zgodnie z opisem w poprzedniej odpowiedzi, istnieją funkcje, które opierają się na [rozszerzeniu SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md). W przypadku odinstalowania wystąpienia domyślnego bez usuwania rozszerzenia IaaS, rozszerzenie nadal szuka wystąpienia domyślnego i może generować błędy dziennika zdarzeń. Te błędy pochodzą z następujących dwóch źródeł: **Microsoft SQL Server zarządzania poświadczeniami** i **Microsoft SQL Server agenta IaaS**. Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   W przypadku podjęcia decyzji o odinstalowaniu wystąpienia domyślnego należy również odinstalować także [rozszerzenie Agent SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) . 

1. **Czy można użyć nazwanego wystąpienia SQL Server z rozszerzeniem IaaS**?
   
   Tak, jeśli nazwane wystąpienie jest jedynym wystąpieniem w SQL Server i jeśli oryginalne wystąpienie domyślne zostało [odinstalowane prawidłowo](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Jeśli nie ma wystąpienia domyślnego i istnieje wiele wystąpień nazwanych na jednej maszynie wirtualnej SQL Server, instalacja rozszerzenia agenta SQL Server IaaS nie powiedzie się. 

1. **Czy mogę usunąć SQL Server całkowicie z maszyny wirtualnej SQL Server?**

   Tak, ale nadal będzie naliczana opłata za maszynę wirtualną SQL Server, zgodnie z opisem w temacie [wskazówki dotyczące cen dla SQL Server maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.
   
## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak zmienić wersję/edycję programu SQL Server na inną na maszynie wirtualnej platformy Azure?**

   Klienci mogą zmienić wersję/edycję programu SQL Server przy użyciu nośnika instalacyjnego zawierającego odpowiednią wersję/edycję programu SQL Server. Po zmianie edycji za pomocą witryny Azure Portal zmodyfikuj właściwość edycji maszyny wirtualnej w celu dokładnego odzwierciedlenia rozliczeń za tę maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Zmiana wersji maszyny wirtualnej SQL Server](virtual-machines-windows-sql-change-edition.md). Nie ma różnicy rozliczeń dla różnych wersji SQL Server, więc gdy wersja SQL Server została zmieniona, nie jest wymagana żadna dodatkowa akcja.

1. **Gdzie mogę uzyskać nośnik instalacyjny, aby zmienić wersję lub wersję SQL Server?**

   Klienci posiadający [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mogą uzyskać nośnik instalacyjny z [centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z poziomu obrazu maszyny wirtualnej SQL Server Marketplace z odpowiednią wersją.
   
1. **Jak są stosowane aktualizacje i dodatki Service Pack na maszynie wirtualnej SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. W przypadku systemu operacyjnego można ręcznie zastosować aktualizacje systemu Windows lub włączyć usługę planowania o nazwie [zautomatyzowane stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

1. **Czy mogę uaktualnić wystąpienie SQL Server 2008/2008 R2 po zarejestrowaniu go u dostawcy zasobów maszyny wirtualnej SQL Server?**

   Tak. Możesz użyć dowolnego nośnika instalacyjnego, aby uaktualnić wersję i wydanie SQL Server, a następnie uaktualnić [tryb rozszerzenia SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) _bez agenta_ do _pełnego_. Dzięki temu będzie można uzyskać dostęp do wszystkich korzyści z rozszerzenia IaaS języka SQL, takich jak Zarządzanie portalem, zautomatyzowane kopie zapasowe i automatyczne stosowanie poprawek. 

1. **Jak mogę uzyskać bezpłatne rozszerzone aktualizacje zabezpieczeń dla mojego końca wsparcia SQL Server 2008 i SQL Server 2008 R2?**

   Możesz uzyskać [bezpłatne rozszerzone aktualizacje zabezpieczeń](virtual-machines-windows-sql-server-2008-eos-extend-support.md) , przenosząc SQL Server na maszynę wirtualną usługi Azure SQL. Aby uzyskać więcej informacji, zobacz [koniec opcji pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Ogólne

1. **Czy SQL Server wystąpienia klastra trybu failover (FCI) są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Wystąpienie klastra trybu failover można zainstalować przy użyciu [udziałów plików w warstwie Premium (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) lub [bezpośrednich miejsc do magazynowania (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) dla podsystemu magazynowania. Udziały plików w warstwie Premium zapewniają możliwości IOPS i przepływności, które będą spełniały potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z operacji we/wy należy rozważyć użycie funkcji bezpośrednie miejsca do magazynowania w oparciu o zarządzane lub niezwykle duże dyski. Alternatywnie możesz użyć rozwiązań do obsługi klastrów lub magazynu innych firm zgodnie z opisem w temacie [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > _W tej_ chwili [SQL Server rozszerzenie agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md) nie jest obsługiwane dla SQL Server FCI na platformie Azure. Zalecamy odinstalowanie _pełnego_ rozszerzenia z maszyn wirtualnych, które uczestniczą w FCI, i zamiast tego zainstaluj rozszerzenie w trybie _uproszczonym_ . To rozszerzenie obsługuje funkcje, takie jak automatyczne tworzenie kopii zapasowych i stosowanie poprawek oraz niektóre funkcje portalu dla SQL Server. Te funkcje nie będą działały dla SQL Server maszyn wirtualnych po odinstalowaniu _pełnego_ agenta.

1. **Jaka jest różnica między maszynami wirtualnymi SQL Server a usługą SQL Database?**

   Ze względu na to, że Uruchamianie SQL Server na maszynie wirtualnej platformy Azure nie różni się od uruchamiania SQL Server w zdalnym centrum danych. W przeciwieństwie [SQL Database](../../../sql-database/sql-database-technical-overview.md) oferuje bazę danych jako usługę. W przypadku SQL Database nie masz dostępu do maszyn, które obsługują bazy danych. Aby zapoznać się z pełnym porównaniem, zobacz [Wybieranie opcji SQL Server w chmurze: baza danych Azure SQL (PaaS) lub SQL Server na maszynach wirtualnych platformy Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Jak mogę zainstalować narzędzia SQL Data Tools na mojej maszynie wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia SQL Data Tools z [Microsoft SQL Server Data Tools — analiza biznesowa dla Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Czy transakcje rozproszone z usługą MSDTC są obsługiwane na maszynach wirtualnych SQL Server?**
   
    Tak. Lokalna usługa DTC jest obsługiwana w przypadku SQL Server 2016 z dodatkiem SP2 i nowszych. Jednak aplikacje muszą być testowane w przypadku używania zawsze włączonych grup dostępności, ponieważ transakcje w trakcie pracy w trybie failover będą kończyć się niepowodzeniem i muszą zostać ponowione. Klastrowane usługi DTC są dostępne począwszy od systemu Windows Server 2019. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
