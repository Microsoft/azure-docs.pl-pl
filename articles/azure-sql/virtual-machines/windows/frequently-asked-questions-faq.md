---
title: SQL Server na Windows Virtual Machines w często zadawanych pytaniach na temat platformy Azure | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania SQL Server na maszyn wirtualnych platformy Azure.
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
ms.openlocfilehash: 02bb1f539369cf72a5d5b6503a3584069589b19e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727352"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Często zadawane pytania dotyczące SQL Server maszyn wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre najczęściej zadawane pytania dotyczące uruchamiania aplikacji [SQL Server usłudze Windows Azure Virtual Machines (maszyn wirtualnych).](https://azure.microsoft.com/services/virtual-machines/sql-server/)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Obrazów

1. **Jakie SQL Server z galerii maszyn wirtualnych są dostępne?** 

   Platforma Azure obsługuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych wersji SQL Server we wszystkich wersjach dla systemów Windows i Linux. Aby uzyskać więcej informacji, zobacz pełną listę obrazów maszyn wirtualnych z [systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) i [obrazów maszyn wirtualnych z systemem Linux.](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)

1. **Czy istniejące obrazy SQL Server wirtualnej galerii są aktualizowane?**

   Co dwa miesiące obrazy SQL Server w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych aktualizacji systemów Windows i Linux. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje, które są oznaczone jako ważne Windows Update, w tym ważne SQL Server aktualizacji zabezpieczeń i dodatków Service Pack. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemu. SQL Server aktualizacji zbiorczych są obsługiwane inaczej w systemach Linux i Windows. W przypadku systemu Linux SQL Server aktualizacje zbiorcze są również uwzględniane podczas odświeżania. Jednak w tej chwili maszyny wirtualne z systemem Windows nie są aktualizowane przy użyciu SQL Server ani aktualizacji zbiorczych systemu Windows Server.

1. **Czy SQL Server maszyn wirtualnych można usunąć z galerii?**

   Tak. Platforma Azure utrzymuje tylko jeden obraz na wersję główną i edycję. Na przykład po zwolnieniu nowego SQL Server Service Pack platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz SQL Server dla poprzedniego dodatku Service Pack jest natychmiast usuwany z Azure Portal. Jednak nadal jest ona dostępna do aprowowania z programu PowerShell przez następne trzy miesiące. Po upływie trzech miesięcy poprzedni obraz dodatku Service Pack nie będzie już dostępny. Te zasady usuwania będą również stosowane, jeśli SQL Server wersji nie będzie obsługiwana po zakończeniu cyklu życia.


1. **Czy można wdrożyć starszy obraz SQL Server, który nie jest widoczny w Azure Portal?**

   Tak, przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat wdrażania maszyn wirtualnych SQL Server przy użyciu programu PowerShell, zobacz Jak aprowizować maszyny wirtualne SQL Server maszyn wirtualnych za pomocą [programu Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Czy można utworzyć uogólniony obraz Azure Marketplace SQL Server maszyny wirtualnej SQL Server wirtualnej i używać go do wdrażania maszyn wirtualnych?**

   Tak, ale musisz zarejestrować każdą maszynę wirtualną usługi SQL Server za pomocą rozszerzenia agenta [SQL IaaS,](sql-agent-extension-manually-register-single-vm.md) aby zarządzać maszyną wirtualną usługi SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne kopie zapasowe. Podczas rejestrowania za pomocą rozszerzenia należy również określić typ licencji dla każdej maszyny SQL Server wirtualnej.

1. **Jak mogę uogólniać SQL Server maszynie wirtualnej platformy Azure i używać jej do wdrażania nowych maszyn wirtualnych?**

   Możesz wdrożyć maszynę wirtualną z systemem Windows Server (bez zainstalowanego na nim programu SQL Server) i użyć procesu [sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) sql do uogólnienia programu SQL Server na maszynie wirtualnej platformy Azure (Windows) z nośnikiem instalacyjnym programu SQL Server. Klienci, którzy biorą udział w programie [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3), mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają konta pakiet Software Assurance mogą używać nośnika instalacyjnego z Azure Marketplace SQL Server maszyny wirtualnej, która ma odpowiednią edycję.

   Alternatywnie możesz użyć jednego z obrazów SQL Server z Azure Marketplace, aby uogólnić SQL Server na maszynie wirtualnej platformy Azure. Należy pamiętać, że przed utworzeniem własnego obrazu należy usunąć następujący klucz rejestru z obrazu źródłowego. Niewykonanie tej instrukcji może spowodować błąd folderu bootstrap SQL Server konfiguracji i/lub rozszerzenia SQL IaaS w stanie niepowodzenia.

   Ścieżka klucza rejestru:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server maszyn wirtualnych platformy Azure, w tym wdrożonych na podstawie niestandardowych uogólnionych obrazów, należy zarejestrować za pomocą rozszerzenia agenta [SQL IaaS](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) w celu spełnienia wymagań dotyczących zgodności i korzystania z opcjonalnych funkcji, takich jak automatyczne stosowanie poprawek i automatyczne kopie zapasowe. Rozszerzenie umożliwia również określenie [typu licencji dla każdej](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) maszyny SQL Server wirtualnej.

1. **Czy mogę użyć własnego wirtualnego dysku twardego do wdrożenia maszyny SQL Server wirtualnej?**

   Tak, ale należy zarejestrować każdą maszynę wirtualną z usługą SQL Server przy użyciu rozszerzenia agenta [SQL IaaS,](sql-agent-extension-manually-register-single-vm.md) aby zarządzać maszyną wirtualną z usługą SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne kopie zapasowe.

1. **Czy można skonfigurować konfiguracje, które nie są wyświetlane w galerii maszyn wirtualnych (na przykład Windows 2008 R2 + SQL Server 2012)?**

   Nie. W przypadku obrazów galerii maszyn wirtualnych, które zawierają SQL Server, należy wybrać jeden z obrazów dostarczonych za pośrednictwem Azure Portal lub za pomocą programu [PowerShell](create-sql-vm-powershell.md). Można jednak wdrożyć maszynę wirtualną z systemem Windows i samodzielnie zainstalować SQL Server do tej maszyny wirtualnej. Następnie należy zarejestrować maszynę wirtualną z programem SQL Server przy użyciu rozszerzenia agenta [SQL IaaS,](sql-agent-extension-manually-register-single-vm.md) aby zarządzać maszyną wirtualną z usługą SQL Server na serwerze Azure Portal, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne kopie zapasowe. 


## <a name="creation"></a>Tworzenie

1. **Jak mogę maszynę wirtualną platformy Azure za pomocą SQL Server?**

   Najprostszą metodą jest utworzenie maszyny wirtualnej, która zawiera SQL Server. Aby uzyskać samouczek dotyczący rejestracji na platformie Azure i tworzenia maszyny wirtualnej usługi SQL Server z portalu, zobacz Provision a SQL Server virtual machine (Aprowizowanie maszyny wirtualnej SQL Server [wirtualnej) w Azure Portal](create-sql-vm-portal.md). Możesz wybrać obraz maszyny wirtualnej, który korzysta z licencjonowania za SQL Server płatności za sekundę, lub użyć obrazu, który umożliwia korzystanie z własnej licencji SQL Server licencji. Możesz również ręcznie zainstalować program SQL Server maszynie wirtualnej z bezpłatną licencją (Developer lub Express) lub ponownie użyć licencji lokalnej. Pamiętaj, aby zarejestrować maszynę wirtualną usługi SQL Server przy użyciu rozszerzenia agenta [SQL IaaS,](sql-agent-extension-manually-register-single-vm.md) aby zarządzać maszyną wirtualną z usługą SQL Server w portalu, a także korzystać z funkcji, takich jak automatyczne stosowanie poprawek i automatyczne tworzenie kopii zapasowych. Jeśli masz własną licencję, musisz mieć licencję przenośność licencji w ramach programu Software Assurance [na platformie Azure.](https://azure.microsoft.com/pricing/license-mobility/) Aby uzyskać więcej informacji, zobacz [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. **Jak przeprowadzić migrację lokalnej bazy danych SQL Server do chmury?**

   Najpierw utwórz maszynę wirtualną platformy Azure z SQL Server wystąpieniem. Następnie przemigruj lokalne bazy danych do tego wystąpienia. Aby uzyskać informacje na temat strategii migracji danych, zobacz Migrate a SQL Server database to SQL Server in an Azure VM (Migrowanie bazy danych SQL Server [na maszynie wirtualnej platformy Azure).](migrate-to-vm-from-sql-server.md)

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Istnieją trzy sposoby, aby to zrobić. Jeśli jesteś klientem z Enterprise Agreement (EA), możesz aprowizować jeden z obrazów maszyn wirtualnych, który obsługuje licencje [,](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)co jest również znane jako bring-your-own-license (BYOL). Jeśli masz [pakiet Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), możesz włączyć [](licensing-model-azure-hybrid-benefit-ahb-change.md) Korzyść użycia hybrydowego platformy Azure na istniejącym obrazie z płatnością zgodnie z płatnością( PAYG). Możesz także skopiować nośniki instalacyjne programu SQL Server na maszynę wirtualną z systemem Windows Server, a następnie zainstalować program SQL Server na maszynie wirtualnej. Pamiętaj, aby zarejestrować maszynę wirtualną [](sql-agent-extension-manually-register-single-vm.md) SQL Server z rozszerzeniem, aby uzyskać funkcje, takie jak zarządzanie portalem, automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek. 


1. **Czy klient musi SQL Server licencji dostępu klienta (CAL), aby nawiązać połączenie z obrazem z płatnością zgodnie z SQL Server, który jest uruchomiony w usłudze Azure Virtual Machines?**

   Nie. Klienci potrzebują licencji CAL, gdy korzystają z licencji bring-your-own-license i przeniosą swoje maszyny wirtualne SQL Server sa/cal na maszyny wirtualne platformy Azure. 

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Tak. Możesz łatwo przełączyć obraz galerii z płatnością zgodnie z potrzebami (PAYG) na bring-your-own-license (BYOL), włączając Korzyść użycia hybrydowego platformy Azure [.](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)  Aby uzyskać więcej informacji, zobacz Jak zmienić model licencjonowania [dla maszyny SQL Server wirtualnej.](licensing-model-azure-hybrid-benefit-ahb-change.md) Obecnie ta usługa jest dostępna tylko dla klientów publicznych i Azure Government chmury.


1. **Czy przełączanie modeli licencjonowania wymaga jakiegokolwiek przestoju w pracy programu SQL Server?**

   Nie. [Zmiana modelu licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md) nie wymaga żadnych przestojów SQL Server, ponieważ zmiana obowiązuje natychmiast i nie wymaga ponownego uruchomienia maszyny wirtualnej. Jednak aby zarejestrować maszynę wirtualną usługi SQL Server przy użyciu rozszerzenia agenta SQL IaaS, rozszerzenie SQL IaaS  jest wymagane i zainstalowanie rozszerzenia SQL [IaaS](sql-server-iaas-agent-extension-automate-management.md) w trybie pełnym powoduje ponowne uruchomienie usługi SQL Server. W związku z tym, jeśli konieczne jest zainstalowanie rozszerzenia SQL  IaaS, zainstaluj je  w trybie lekkim w celu ograniczenia funkcjonalności lub zainstaluj je w trybie pełnym podczas okna obsługi. Rozszerzenie SQL IaaS  zainstalowane w trybie  lekkim można uaktualnić do trybu pełnego w dowolnym momencie, ale wymaga ponownego uruchomienia SQL Server usługi. 
   
1. **Czy istnieje możliwość przełączenia modeli licencjonowania na maszynie wirtualnej SQL Server wdrożonej przy użyciu modelu klasycznego?**

   Nie. Zmiana modeli licencjonowania nie jest obsługiwana na klasycznej maszynie wirtualnej. Możesz zmigrować maszynę wirtualną do Azure Resource Manager i zarejestrować się przy użyciu rozszerzenia agenta SQL IaaS. Po zarejestrowaniu maszyny wirtualnej przy użyciu rozszerzenia agenta SQL IaaS zmiany modelu licencjonowania będą dostępne na maszynie wirtualnej.

1. **Czy mogę używać witryny Azure Portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie w portalu jest funkcją zapewnianą przez rozszerzenie agenta SQL IaaS, które opiera się na SQL Server agenta IaaS. W związku z tym te same ograniczenia dotyczą rozszerzenia. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym wystąpieniem nazwanym, o ile został prawidłowo skonfigurowany. Aby uzyskać więcej informacji na temat tych ograniczeń, [zobacz SQL Server IaaS agent extension (Rozszerzenie agenta IaaS).](sql-server-iaas-agent-extension-automate-management.md) 

1. **Czy subskrypcje CSP mogą aktywować Korzyść użycia hybrydowego platformy Azure?**

   Tak, korzyść użycia hybrydowego platformy Azure jest dostępny dla subskrypcji CSP. Klienci programu CSP powinni najpierw wdrożyć obraz z płatnością zgodnie z potrzebami, a następnie zmienić [model](licensing-model-azure-hybrid-benefit-ahb-change.md) licencjonowania na bring-your-own-license.
   
 
1. **Czy muszę płacić za licencję programu SQL Server na maszynie wirtualnej platformy Azure, jeśli używam jej tylko w trybie wstrzymania/trybie failover?**

   Aby mieć bezpłatną licencję pasywną dla rezerwowej pomocniczej grupy dostępności lub wystąpienia klastra trybu failover, musisz spełnić wszystkie następujące kryteria zgodnie z postanowieniami licencyjnymi [produktu:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Masz licencję [na mobilność za](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) [pośrednictwem pakiet Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 
   1. Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server dla klientów ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest ono używane tylko do synchronizacji z serwerem podstawowym i utrzymywania pasywnej bazy danych w stanie rezerwy aktywnej. Jeśli usługa obsługuje dane, takie jak raporty dla klientów z uruchomionymi aktywnymi obciążeniami usługi SQL Server, lub wykonuje dowolną pracę inną niż określona w warunkach produktu, musi być płatną licencjonowaną SQL Server wystąpieniem. Na wystąpieniu pomocniczym dozwolone jest następujące działanie: sprawdzanie spójności bazy danych lub polecenie CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również na krótkie okresy uruchamiać jednocześnie wystąpienie podstawowe i odpowiadające mu wystąpienie odzyskiwania po awarii w celu testowania odzyskiwania po awarii co 90 dni. 
   1. Aktywna licencja SQL Server jest objęta usługą pakiet Software Assurance  i umożliwia korzystanie tylko z jednego pasywnego pomocniczego wystąpienia SQL Server, z maksymalnie taką samą ilością zasobów obliczeniowych, jak w przypadku licencjonowanego aktywnego serwera. 
   1. Pomocnicza maszyna SQL Server wirtualna korzysta z licencji [odzyskiwania po](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) awarii w Azure Portal.
   
1. **Co jest uznawane za wystąpienie pasywne?**

   Pasywne wystąpienie programu SQL Server nie obsługuje danych programu SQL Server dla klientów ani nie uruchamia aktywnych obciążeń programu SQL Server. Jest ono używane tylko do synchronizacji z serwerem podstawowym i utrzymywania pasywnej bazy danych w stanie rezerwy aktywnej. Jeśli obsługuje ono dane, takie jak raporty dla klientów z uruchomionymi aktywnymi obciążeniami programu SQL Server, lub wykonuje jakąkolwiek inną pracę, niż określona w warunkach produktu, musi to być płatne, licencjonowane wystąpienie programu SQL Server. Na wystąpieniu pomocniczym dozwolone jest następujące działanie: sprawdzanie spójności bazy danych lub polecenie CheckDB, pełne kopie zapasowe, kopie zapasowe dziennika transakcji i monitorowanie danych użycia zasobów. Można również na krótkie okresy uruchamiać jednocześnie wystąpienie podstawowe i odpowiadające mu wystąpienie odzyskiwania po awarii w celu testowania odzyskiwania po awarii co 90 dni.
   

1. **W jakich scenariuszach można zastosować korzyść odzyskiwania po awarii?**

   Przewodnik [licencjonowania zawiera](https://aka.ms/sql2019licenseguide) scenariusze, w których można wykorzystać korzyść odzyskiwania po awarii. Aby uzyskać więcej informacji, zapoznaj się z warunkami użytkowania produktu lub skontaktuj się z osobami odpowiedzialnymi za licencjonowanie bądź z menedżerem konta.

1. **Które subskrypcje obsługują korzyść odzyskiwania po awarii?**

   Kompleksowe programy oferujące w ramach stałych korzyści prawa subskrypcyjne równoważne z programem Software Assurance obsługują korzyść odzyskiwania po awarii. Obejmuje to, , ale nie jest ograniczony do programu Open Value (OV), open value subscription (OVS), Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS) oraz serwera i rejestracji w chmurze (SCE). Zapoznaj się z warunkami [produktu i porozmawiaj](https://www.microsoft.com/licensing/product-licensing/products) z kontaktami w zakresie licencjonowania lub menedżerem konta, aby uzyskać więcej informacji. 

   
 ## <a name="extension"></a>Wewnętrzny

1. **Czy zarejestrowanie mojej maszyny wirtualnej przy użyciu nowego rozszerzenia agenta SQL IaaS spowoduje dodatkowe koszty?**

   Nie. Rozszerzenie agenta SQL IaaS zapewnia po prostu dodatkową możliwość zarządzania SQL Server na maszynie wirtualnej platformy Azure bez dodatkowych opłat. 

1. **Czy rozszerzenie agenta SQL IaaS jest dostępne dla wszystkich klientów?**
 
   Tak, o ile maszyna wirtualna SQL Server została wdrożona w chmurze publicznej przy użyciu modelu Resource Manager, a nie modelu klasycznego. Wszyscy inni klienci mogą zarejestrować się przy użyciu nowego rozszerzenia agenta SQL IaaS. Jednak tylko klienci z korzyścią [pakiet Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) mogą korzystać z własnej licencji, aktywując Korzyść użycia hybrydowego platformy Azure [(AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na SQL Server wirtualnej. 

1. **Co się stanie z zasobem rozszerzenia _(Microsoft.SqlVirtualMachine),_ jeśli zasób maszyny wirtualnej zostanie przeniesiony lub porzucony?** 

   Gdy zasób Microsoft.Compute/VirtualMachine zostanie porzucony lub przeniesiony, skojarzony zasób Microsoft.SqlVirtualMachine zostanie powiadomiony o asynchronicznej replikacji operacji.

1. **Co się stanie z maszyną wirtualną w przypadku porzucania zasobu rozszerzenia _(Microsoft.SqlVirtualMachine)?_**

    Usunięcie zasobu Microsoft.SqlVirtualMachine nie ma wpływu na zasób Microsoft.Compute/VirtualMachine. Jednak zmiany licencjonowania powrócą domyślnie do oryginalnego źródła obrazu. 

1. **Czy jest możliwe zarejestrowanie samodzielnie wdrożonych maszyn wirtualnych SQL Server z rozszerzeniem agenta SQL IaaS?**

    Tak. Jeśli wdrożono usługę SQL Server z własnego nośnika i zainstalowano rozszerzenie SQL IaaS, możesz zarejestrować maszynę wirtualną z rozszerzeniem SQL Server, aby uzyskać korzyści z możliwości zarządzania zapewniane przez rozszerzenie SQL IaaS.    


## <a name="administration"></a>Administracja

1. **Czy mogę zainstalować drugie wystąpienie usługi SQL Server na tej samej maszynie wirtualnej? Czy mogę zmienić zainstalowane funkcje wystąpienia domyślnego?**

   Tak. Nośnik SQL Server znajduje się w folderze na dysku **C.** Uruchom **Setup.exe** z tej lokalizacji, aby dodać nowe SQL Server wystąpień lub zmienić inne zainstalowane funkcje SQL Server na maszynie. Należy pamiętać, że niektóre funkcje, takie jak automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i integracja Azure Key Vault, działają tylko w przypadku wystąpienia domyślnego lub nazwanego wystąpienia, które zostało prawidłowo skonfigurowane (zobacz pytanie 3). Klienci korzystający pakiet Software Assurance za pośrednictwem modelu [licencjonowania usługi Korzyść użycia hybrydowego platformy Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) lub modelu licencjonowania z płatnością zgodnie z użyciem mogą zainstalować wiele wystąpień usługi SQL Server na maszynie wirtualnej bez ponoszenia dodatkowych kosztów licencjonowania.  Dodatkowe SQL Server mogą powodować obciążenie zasobów systemowych, chyba że zostaną prawidłowo skonfigurowane. 

1. **Jaka jest maksymalna liczba wystąpień na maszynie wirtualnej?**
   SQL Server 2012 do SQL Server 2019 może obsługiwać [50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) wystąpień na serwerze autonomicznym. Jest to ten sam limit niezależnie od tego, czy platforma Azure jest lokalna. Zobacz [najlepsze rozwiązania, aby](performance-guidelines-best-practices.md#multiple-instances) dowiedzieć się, jak lepiej przygotować środowisko. 

1. **Czy mogę odinstalować domyślne wystąpienie programu SQL Server?**

   Tak, ale należy wziąć pod uwagę pewne kwestie. Po pierwsze SQL Server mogą nadal występować w zależności od modelu licencji dla maszyny wirtualnej. Po drugie, jak dano w poprzedniej odpowiedzi, istnieją funkcje, które polegają na SQL Server [agenta IaaS](sql-server-iaas-agent-extension-automate-management.md). Jeśli odinstalujesz również wystąpienie domyślne bez usuwania rozszerzenia IaaS, rozszerzenie będzie nadal szukać wystąpienia domyślnego i może generować błędy dziennika zdarzeń. Te błędy są z następujących dwóch źródeł: **zarządzania poświadczeniami Microsoft SQL Server** i Microsoft SQL Server **agenta IaaS.** Jeden z błędów może być podobny do następującego:

      Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny.

   Jeśli zdecydujesz się odinstalować wystąpienie domyślne, odinstaluj również [SQL Server agenta IaaS.](sql-server-iaas-agent-extension-automate-management.md) 

1. **Czy mogę użyć nazwanego wystąpienia klasy SQL Server z rozszerzeniem IaaS?**
   
   Tak, jeśli nazwane wystąpienie jest jedynym wystąpieniem na SQL Server, a oryginalne wystąpienie domyślne [zostało poprawnie odinstalowane.](sql-server-iaas-agent-extension-automate-management.md#named-instance-support) Jeśli nie ma wystąpienia domyślnego i istnieje wiele nazwanych wystąpień na jednej maszynie wirtualnej SQL Server wirtualnej, instalacja SQL Server agenta IaaS nie powiedzie się.  

1. **Czy mogę usunąć SQL Server i skojarzone rozliczenia licencji z maszyny SQL Server wirtualnej?**

   Tak, ale należy wykonać dodatkowe czynności, aby uniknąć nalinia opłat za wystąpienie SQL Server zgodnie ze wskazówkami [w zakresie cen.](pricing-guidance.md) Jeśli chcesz całkowicie usunąć wystąpienie usługi SQL Server, możesz przeprowadzić migrację do innej maszyny wirtualnej platformy Azure bez wstępnej instalacji programu SQL Server na maszynie wirtualnej i usunąć bieżącą maszynę wirtualną SQL Server wirtualnej. Jeśli chcesz zachować maszynę wirtualną, ale zatrzymać SQL Server, wykonaj następujące kroki: 

   1. W razie potrzeby należy wrócić do kopii zapasowej wszystkich danych, w tym systemowych baz danych. 
   1. Odinstaluj SQL Server, w tym rozszerzenie SQL IaaS (jeśli jest obecne).
   1. Zainstaluj bezpłatną [wersję programu SQL Express.](https://www.microsoft.com/sql-server/sql-server-downloads)
   1. Zarejestruj się przy użyciu rozszerzenia agenta SQL IaaS w [trybie lekkim.](sql-agent-extension-manually-register-single-vm.md)
   1. [Zmień wersję SQL Server](change-sql-server-edition.md#change-edition-in-portal) w Azure Portal [na](https://portal.azure.com) Express, aby zatrzymać rozliczanie.  
   1. (opcjonalnie) Wyłącz usługę Express SQL Server, wyłączając uruchamianie usługi. 

1. **Czy mogę używać witryny Azure Portal do zarządzania wieloma wystąpieniami na tej samej maszynie wirtualnej?**

   Nie. Zarządzanie portalem jest zapewniane przez rozszerzenie agenta SQL IaaS, które opiera się na SQL Server agenta IaaS. W związku z tym te same ograniczenia mają zastosowanie do rozszerzenia jako rozszerzenie. Portal może zarządzać tylko jednym wystąpieniem domyślnym lub jednym nazwane wystąpieniem, o ile jest poprawnie skonfigurowane. Aby uzyskać więcej informacji, [zobacz SQL Server IaaS Agent extension (Rozszerzenie agenta IaaS)](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak mogę zmienić wersję/edycję usługi SQL Server na maszynie wirtualnej platformy Azure?**

   Klienci mogą zmienić wersję/edycję programu SQL Server przy użyciu nośnika instalacyjnego zawierającego odpowiednią wersję/edycję programu SQL Server. Po zmianie edycji za pomocą witryny Azure Portal zmodyfikuj właściwość edycji maszyny wirtualnej w celu dokładnego odzwierciedlenia rozliczeń za tę maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [Zmienianie wersji maszyny wirtualnej SQL Server wirtualnej.](change-sql-server-edition.md) Rozliczenia dla różnych wersji programu SQL Server są takie same, dlatego po zmianie wersji programu SQL Server nie są wymagane żadne dodatkowe akcje.

1. **Gdzie mogę uzyskać nośnik instalacyjny, aby zmienić edycję lub wersję programu SQL Server?**

   Klienci, którzy biorą udział w programie [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają pakiet Software Assurance mogą używać nośnika instalacyjnego z obrazu Azure Marketplace SQL Server maszyny wirtualnej, który ma odpowiednią edycję.
   
1. **Jak wygląda zastosowanie aktualizacji i dodatków Service Pack na maszynie wirtualnej z programem SQL Server?**

   maszyny wirtualne zapewniają kontrolę nad maszyną hosta, w tym nad czasem i sposobem stosowania aktualizacji. Dla systemu operacyjnego możesz ręcznie zastosować aktualizacje systemu Windows lub włączyć usługę harmonogramu o nazwie [Automatyczne stosowanie poprawek](automated-patching.md). Usługa Automatyczne stosowanie poprawek instaluje wszelkie aktualizacje, które są oznaczone jako ważne, w tym aktualizacje programu SQL Server w tej kategorii. Inne, opcjonalne aktualizacje dla programu SQL Server należy zainstalować ręcznie.

1. **Czy mogę uaktualnić SQL Server 2008/2008 R2 po zarejestrowaniu go za pomocą rozszerzenia agenta SQL IaaS?**

   Jeśli system operacyjny to Windows Server 2008 R2 lub nowszy, tak. Możesz użyć dowolnego nośnika instalacyjnego, aby uaktualnić wersję i wydanie usługi SQL Server, a następnie uaktualnić tryb rozszerzenia [SQL IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes)z agenta do _pełnego._  Zapewni to dostęp do wszystkich zalet rozszerzenia SQL IaaS, takich jak możliwości zarządzania portalem, automatyczne kopie zapasowe i automatyczne stosowanie poprawek. Jeśli wersja systemu operacyjnego to Windows Server 2008, obsługiwany jest tylko tryb NoAgent. 

1. **Jak mogę uzyskać bezpłatne rozszerzone aktualizacje zabezpieczeń dla moich wystąpień programu SQL Server 2008 i SQL Server 2008 R2, dla których kończy się wsparcie?**

   Bezpłatne rozszerzone [aktualizacje zabezpieczeń można uzyskać,](sql-server-2008-extend-end-of-support.md) przenosząc SQL Server w stanie, w jaki jest, na maszynę wirtualną platformy Azure. Aby uzyskać więcej informacji, zobacz temat dotyczący [opcji zakończenia pomocy technicznej](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Ogólne

1. **Czy SQL Server klastra trybu failover są obsługiwane na maszynach wirtualnych platformy Azure?**

   Tak. Wystąpienie klastra trybu failover można zainstalować przy użyciu udziałów plików [Premium (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) lub bezpośrednich miejsc do magazynowania [(S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) dla podsystemu magazynowania. Udziały plików w wersji Premium zapewniają pojemność IOPS i przepływność, które spełnią potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z we/wy rozważ użycie bezpośrednich miejsc do magazynowania opartych na manged premium lub ultra dyskach. Alternatywnie możesz użyć rozwiązań do klastrowania lub magazynowania innych firm, zgodnie z opisem w tesłudze: Wysoka dostępność i odzyskiwanie po awarii na SQL Server [platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > Obecnie pełne rozszerzenie _agenta_ [IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md) nie jest obsługiwane w przypadku SQL Server FCI na platformie Azure. Zaleca się odinstalowanie _pełnego_ rozszerzenia z maszyn wirtualnych, które uczestniczą w sieci FCI, a zamiast tego zainstalowanie rozszerzenia w _trybie lekkim._ To rozszerzenie obsługuje funkcje, takie jak automatyczne tworzenie kopii zapasowych i stosowanie poprawek, oraz niektóre funkcje portalu dla SQL Server. Te funkcje nie będą działać w SQL Server wirtualnych  po odinstalowaniu pełnego agenta.

1. **Jaka jest różnica między maszynami SQL Server i SQL Database wirtualnej?**

   Koncepcyjnie uruchamianie SQL Server na maszynie wirtualnej platformy Azure nie różni się tak bardzo SQL Server w zdalnym centrum danych. Z kolei [Azure SQL Database](../../database/sql-database-paas-overview.md) oferuje bazę danych jako usługę. W SQL Database nie masz dostępu do maszyn hostujących bazy danych. Aby uzyskać pełne porównanie, zobacz Choose [a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Wybieranie](../../azure-sql-iaas-vs-paas-what-is-overview.md)opcji chmury: baza danych PaaS lub usługa SQL Server na platformie Azure ).

1. **Jak mogę zainstalować narzędzia SQL Data Tools na mojej maszynie wirtualnej platformy Azure?**

    Pobierz i zainstaluj narzędzia SQL Data tools z usługi [Microsoft SQL Server Data Tools — Business Intelligence for Visual Studio 2013.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Czy transakcje rozproszone z MSDTC są obsługiwane SQL Server wirtualnych?**
   
    Tak. Lokalny kod DTC jest obsługiwany SQL Server 2016 SP2 i większych. Jednak aplikacje muszą być testowane podczas korzystania z zawsze wł. grup dostępności, ponieważ transakcje w locie podczas pracy w trybie failover nie powiodą się i należy podjąć ponowną próbę. Klastrowany kod DTC jest dostępny od systemu Windows Server 2019. 
    
1. **Czy Azure SQL wirtualna przenosi lub przechowuje dane klientów poza region?**

   Nie. W rzeczywistości Azure SQL wirtualna i rozszerzenie agenta SQL IaaS nie przechowują żadnych danych klienta.

## <a name="sql-server-iaas-agent-extension"></a>Rozszerzenie agenta IaaS w programie SQL Server

1. **Czy należy zarejestrować maszynę wirtualną SQL Server aprowizowana na SQL Server obrazie w Azure Marketplace?**

   Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne aprowowane na SQL Server obrazów w Azure Marketplace. Rejestracja przy użyciu rozszerzenia jest wymagana  tylko wtedy, gdy maszyna wirtualna nie została aprowizowana z obrazów SQL Server w programie Azure Marketplace i SQL Server została zainstalowana samodzielnie.

1. **Czy rozszerzenie agenta SQL IaaS jest dostępne dla wszystkich klientów?** 

   Tak. Klienci powinni zarejestrować swoje maszyny wirtualne SQL Server z rozszerzeniem, jeśli nie używali obrazu SQL Server z usługi Azure Marketplace, a zamiast tego samodzielnie SQL Server lub jeśli przywiązali niestandardowy dysk VHD. Maszyny wirtualne należące do wszystkich typów subskrypcji (direct, Enterprise Agreement i Dostawca rozwiązań w chmurze) mogą rejestrować się za pomocą rozszerzenia agenta SQL IaaS.

1. **Jaki jest domyślny tryb zarządzania podczas rejestrowania przy użyciu rozszerzenia agenta SQL IaaS?**

   Domyślny tryb zarządzania podczas rejestrowania przy użyciu rozszerzenia agenta SQL IaaS jest *lekki.* Jeśli właściwość SQL Server management nie zostanie ustawiona podczas rejestrowania w rozszerzeniu, tryb zostanie ustawiony jako lekki, a usługa SQL Server nie zostanie ponownie uruchomiona. Zaleca się zarejestrowanie się przy użyciu rozszerzenia agenta SQL IaaS w trybie lekkim, a następnie uaktualnienie do pełnej wersji w oknie obsługi. Podobnie domyślne zarządzanie jest również uproszczone w przypadku korzystania z [funkcji automatycznej rejestracji](sql-agent-extension-automatic-registration-all-vms.md).

1. **Jakie są wymagania wstępne dotyczące rejestrowania przy użyciu rozszerzenia agenta SQL IaaS?**

   Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS nie jest wymagane poza zainstalowaniem SQL Server na maszynie wirtualnej. Należy pamiętać, że jeśli rozszerzenie agenta SQL IaaS jest zainstalowane w trybie pełnym, usługa SQL Server zostanie ponownie uruchomiona, dlatego zaleca się wykonanie tej czynności w oknie obsługi.

1. **Czy zarejestrowanie przy użyciu rozszerzenia agenta SQL IaaS spowoduje zainstalowanie agenta na mojej maszynie wirtualnej?**

   Tak, zarejestrowanie przy użyciu rozszerzenia agenta SQL IaaS w trybie pełnego zarządzania instaluje agenta na maszynie wirtualnej. Rejestrowanie w trybie lekkim lub NoAgent nie jest. 

   Zarejestrowanie przy użyciu rozszerzenia agenta SQL IaaS w trybie lekkim powoduje tylko skopiowanie plików binarnych rozszerzenia agenta SQL IaaS na maszynę wirtualną, ale nie powoduje zainstalowania agenta.  Te pliki binarne są następnie używane do instalowania agenta po uaktualnieniu trybu zarządzania do pełnej wersji.


1. **Czy rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS będzie SQL Server na mojej maszynie wirtualnej?**

   Zależy to od trybu określonego podczas rejestracji. Jeśli określono tryb lightweight lub NoAgent, usługa SQL Server nie zostanie ponownie uruchomiona. Jednak określenie trybu zarządzania jako pełnego spowoduje ponowne SQL Server usługi. Funkcja automatycznej rejestracji rejestruje maszyny wirtualne SQL Server w trybie lekkim, chyba że wersja systemu Windows Server to 2008. W takim przypadku maszyna wirtualna SQL Server zostanie zarejestrowana w trybie NoAgent. 

1. **Jaka jest różnica między trybami zarządzania lightweight i NoAgent podczas rejestrowania przy użyciu rozszerzenia agenta SQL IaaS?** 

   Tryb zarządzania NoAgent jest jedynym dostępnym trybem zarządzania dla systemów SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. W przypadku wszystkich nowszych wersji systemu Windows Server dwa dostępne tryby zarządzania są uproszczone i pełne. 

   Tryb NoAgent SQL Server właściwości wersji i wersji muszą być ustawiane przez klienta. Tryb lekki wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i SQL Server wystąpienia.

1. **Czy mogę zarejestrować się przy użyciu rozszerzenia agenta SQL IaaS bez określania SQL Server typu licencji?**

   Nie. Typ SQL Server licencji nie jest opcjonalną właściwością podczas rejestrowania przy użyciu rozszerzenia agenta SQL IaaS. Podczas rejestrowania za pomocą rozszerzenia agenta SQL IaaS we wszystkich trybach zarządzania (NoAgent, lightweight i full) należy ustawić typ licencji usługi SQL Server jako płatność zgodnie z Korzyść użycia hybrydowego platformy Azure. Jeśli masz zainstalowaną dowolną z bezpłatnych wersji SQL Server, taką jak Developer lub Evaluation, musisz zarejestrować się przy użyciu licencjonowania z płatnością zgodnie z użytkownikiem. Korzyść użycia hybrydowego platformy Azure jest dostępna tylko w przypadku płatnych wersji SQL Server, takich jak wersje Enterprise i Standard.

1. **Czy mogę uaktualnić SQL Server IaaS z trybu NoAgent do trybu pełnego?**

   Nie. Uaktualnianie trybu zarządzania do pełnego lub uproszczonego nie jest dostępne dla trybu NoAgent. Jest to ograniczenie techniczne systemu Windows Server 2008. Najpierw należy uaktualnić system operacyjny do systemu Windows Server 2008 R2 lub większego, a następnie będzie można uaktualnić do trybu pełnego zarządzania. 

1. **Czy mogę uaktualnić SQL Server IaaS z trybu uproszczonego do pełnego?**

   Tak. Uaktualnianie trybu zarządzania z uproszczonego do pełnego jest obsługiwane za pośrednictwem Azure PowerShell lub Azure Portal. Spowoduje to ponowne uruchomienie SQL Server usługi.

1. **Czy mogę obniżyć poziom SQL Server IaaS z trybu pełnego na NoAgent lub uproszczonego?**

   Nie. Nie jest obsługiwane SQL Server zarządzania rozszerzeniami IaaS. Nie można obniżyć trybu zarządzania z trybu pełnego na lekki lub NoAgent i nie można go obniżyć z trybu uproszczonego na tryb NoAgent. 

   Aby zmienić tryb zarządzania z pełnego [zarządzania,](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) wyrejestruj maszynę wirtualną programu SQL Server z rozszerzenia  agenta SQL IaaS, porzucając zasób maszyny wirtualnej SQL i ponownie zarejestruj maszynę wirtualną programu SQL Server przy użyciu rozszerzenia agenta SQL IaaS w innym trybie zarządzania.

1. **Czy mogę zarejestrować się przy użyciu rozszerzenia agenta SQL IaaS z Azure Portal?**

   Nie. Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS nie jest dostępne w Azure Portal. Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS jest obsługiwane tylko za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. 

1. **Czy mogę zarejestrować maszynę wirtualną z rozszerzeniem agenta SQL IaaS przed SQL Server instalacji?**

   Nie. Maszyna wirtualna musi mieć co najmniej jedno SQL Server (aparat bazy danych), aby pomyślnie zarejestrować się przy użyciu rozszerzenia agenta SQL IaaS. Jeśli na maszynie SQL Server nie ma żadnego wystąpienia, nowy zasób Microsoft.SqlVirtualMachine będzie w stanie awarii.

1. **Czy mogę zarejestrować maszynę wirtualną przy użyciu rozszerzenia agenta SQL IaaS, jeśli istnieje SQL Server wystąpień?**

   Tak, pod warunkiem, że na maszynie wirtualnej istnieje wystąpienie domyślne. Rozszerzenie agenta SQL IaaS zarejestruje tylko jedno SQL Server (aparat bazy danych). Rozszerzenie agenta SQL IaaS zarejestruje domyślne SQL Server wystąpienia w przypadku wielu wystąpień.

1. **Czy mogę zarejestrować wystąpienie SQL Server trybu failover z rozszerzeniem agenta SQL IaaS?**

   Tak. SQL Server klastra trybu failover na maszynie wirtualnej platformy Azure można zarejestrować za pomocą rozszerzenia agenta SQL IaaS w trybie lekkim. Jednak SQL Server klastra trybu failover nie można uaktualnić do trybu pełnego zarządzania.

1. **Czy mogę zarejestrować maszynę wirtualną przy użyciu rozszerzenia agenta SQL IaaS, jeśli skonfigurowano zawsze wł. grupę dostępności?**

   Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia usługi SQL Server na maszynie wirtualnej platformy Azure z rozszerzeniem agenta SQL IaaS, jeśli uczestniczysz w konfiguracji zawsze wł. grupy dostępności.

1. **Jaki jest koszt rejestracji za pomocą rozszerzenia agenta SQL IaaS lub uaktualnienia do pełnego trybu zarządzania?**

   Brak. Rejestracja w rozszerzeniu agenta SQL IaaS lub korzystanie z dowolnego z trzech trybów zarządzania nie wiąże się z opłatami. Zarządzanie maszyną SQL Server wirtualną za pomocą rozszerzenia jest całkowicie bezpłatne. 

1. **Jaki jest wpływ używania różnych trybów zarządzania na wydajność?**

   Korzystanie z trybów *NoAgent*  i uproszczonego zarządzania nie ma żadnego wpływu. Korzystanie z trybu pełnego zarządzania z dwóch usług zainstalowanych w tym systemu operacyjnego ma minimalny wpływ.  Można je monitorować za pośrednictwem Menedżera zadań i widoczne w wbudowanej konsoli usług systemu Windows. 

   Te dwie nazwy usług to:
   - `SqlIaaSExtensionQuery` (Nazwa wyświetlana — `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Nazwa wyświetlana — `Microsoft SQL Server IaaS Agent` )

1. **Jak mogę usunąć rozszerzenie?**

   Usuń rozszerzenie, [wyrejestrując](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) maszynę wirtualną SQL Server z rozszerzenia agenta SQL IaaS. 

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Windows:**

* [Omówienie SQL Server maszyny wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Aprowiz SQL Server na maszynie wirtualnej z systemem Windows](create-sql-vm-portal.md)
* [Migrowanie bazy danych do usługi SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Najlepsze rozwiązania dotyczące wydajności dla SQL Server na platformie Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Wzorce aplikacji i strategie tworzenia aplikacji dla SQL Server na platformie Azure Virtual Machines](application-patterns-development-strategies.md)

**Maszyny wirtualne z systemem Linux:**

* [Omówienie usługi SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Aprowiz SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Często zadawane pytania (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux dokumentacji](/sql/linux/sql-server-linux-overview)
