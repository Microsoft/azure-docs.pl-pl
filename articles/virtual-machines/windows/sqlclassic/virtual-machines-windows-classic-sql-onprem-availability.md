---
title: Rozszerzone zawsze włączone grupy dostępności na platformę Azure | Microsoft Docs
description: W tym samouczku użyto zasobów utworzonych przy użyciu klasycznego modelu wdrażania i opisano sposób użycia Kreatora dodawania repliki w SQL Server Management Studio (SSMS), aby dodać replikę zawsze włączonej repliki grupy dostępności na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 928162282ca973811315728a07b4fbe40100324e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017583"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Rozszerzanie lokalnych, zawsze dostępnych grup dostępności na platformę Azure
Zawsze włączone grupy dostępności zapewniają wysoką dostępność dla grup baz danych, dodając repliki pomocnicze. Te repliki umożliwiają przechodzenie w tryb failover baz danych w przypadku awarii. Ponadto mogą służyć do odciążania obciążeń odczytu lub zadań tworzenia kopii zapasowych.

Lokalne grupy dostępności można rozłożyć na Microsoft Azure, udostępniając co najmniej jedną maszynę wirtualną platformy Azure z SQL Server, a następnie dodając je jako repliki do lokalnych grup dostępności.

W tym samouczku założono, że masz następujące elementy:

* Aktywna subskrypcja platformy Azure. Możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Istniejąca zawsze włączona Grupa dostępności. Aby uzyskać więcej informacji na temat grup dostępności, zobacz [Always On Availability Groups](https://msdn.microsoft.com/library/hh510230.aspx).
* Łączność między siecią lokalną a siecią wirtualną platformy Azure. Aby uzyskać więcej informacji na temat tworzenia tej sieci wirtualnej, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu Azure Portal (klasyczne)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

## <a name="add-azure-replica-wizard"></a>Kreator dodawania repliki platformy Azure
W tej sekcji pokazano, jak za pomocą **Kreatora dodawania repliki systemu Azure** rozłożyć rozwiązanie zawsze włączone grupy dostępności w celu uwzględnienia replik platformy Azure.

> [!IMPORTANT]
> **Kreator dodawania repliki platformy Azure** obsługuje tylko maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania. Nowe wdrożenia maszyn wirtualnych powinny używać nowszego modelu Menedżer zasobów. Jeśli używasz maszyn wirtualnych z Menedżer zasobów, musisz ręcznie dodać pomocniczą replikę platformy Azure przy użyciu poleceń języka Transact-SQL (nie pokazano tutaj). Ten Kreator nie będzie działał w Menedżer zasobów scenariuszu.

1. W obszarze SQL Server Management Studio rozwiń węzeł **zawsze włączone grupy dostępności o wysokiej dostępności**  >  **Availability Groups**  >  **[nazwa grupy dostępności]**.
2. Kliknij prawym przyciskiem myszy pozycję **repliki dostępności**, a następnie kliknij polecenie **Dodaj replikę**.
3. Domyślnie zostanie wyświetlony **Kreator dodawania repliki do grupy dostępności** . Kliknij przycisk **Dalej**.  Jeśli wybrano opcję **nie pokazuj tej strony ponownie** w dolnej części strony podczas poprzedniego uruchomienia tego kreatora, ten ekran nie zostanie wyświetlony.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Konieczne będzie połączenie ze wszystkimi istniejącymi replikami pomocniczymi. Możesz kliknąć przycisk **Połącz...** obok każdej repliki lub kliknij pozycję **Połącz wszystko...** w dolnej części ekranu. Po uwierzytelnieniu kliknij przycisk **dalej** , aby przejść do następnego ekranu.
5. Na stronie **Określanie replik** w górnej części ekranu znajduje się wiele kart: **repliki**, **punkty końcowe**, **Preferencje tworzenia kopii zapasowych**i **odbiornik**. Na karcie **repliki** kliknij pozycję **Dodaj replikę Azure..** . Aby uruchomić Kreatora dodawania repliki platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Jeśli wcześniej zainstalowano program, wybierz istniejący certyfikat zarządzania Azure z lokalnego magazynu certyfikatów systemu Windows. Wybierz lub wprowadź identyfikator subskrypcji platformy Azure, jeśli został on użyty wcześniej. Możesz kliknąć pozycję Pobierz, aby pobrać i zainstalować certyfikat zarządzania platformy Azure i pobrać listę subskrypcji przy użyciu konta platformy Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Każde pole na stronie zostanie wypełnione wartościami, które będą używane do tworzenia maszyny wirtualnej platformy Azure, która będzie hostować replikę.
   
   | Ustawienie | Opis |
   | --- | --- |
   | **Image (Obraz)** |Wybierz żądaną kombinację systemów operacyjnych i SQL Server |
   | **Rozmiar maszyny wirtualnej** |Wybierz rozmiar maszyny wirtualnej, który najlepiej odpowiada potrzebom biznesowym |
   | **Nazwa maszyny wirtualnej** |Określ unikatową nazwę nowej maszyny wirtualnej. Nazwa musi zawierać od 3 do 15 znaków, może zawierać tylko litery, cyfry i łączniki, a także musi zaczynać się literą i kończyć literą lub cyfrą. |
   | **Nazwa użytkownika maszyny wirtualnej** |Określ nazwę użytkownika, która stanie się kontem administratora na maszynie wirtualnej |
   | **Hasło administratora maszyny wirtualnej** |Określ hasło dla nowego konta |
   | **Potwierdź hasło** |Potwierdź hasło nowego konta |
   | **Virtual Network** |Określ sieć wirtualną platformy Azure, która ma być używana przez nową MASZYNę wirtualną. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Virtual Network Omówienie](../../../virtual-network/virtual-networks-overview.md). |
   | **Podsieć Virtual Network** |Określ podsieć sieci wirtualnej, której ma używać Nowa maszyna wirtualna |
   | **Domain** |Potwierdź, że wstępnie wypełniona wartość dla domeny jest poprawna |
   | **Nazwa użytkownika domeny** |Określ konto należące do lokalnej grupy administratorów w lokalnych węzłach klastra |
   | **Hasło** |Określ hasło dla nazwy użytkownika domeny |
8. Kliknij przycisk **OK** , aby sprawdzić poprawność ustawień wdrożenia.
9. W dalszej kolejności są wyświetlane postanowienia prawne. Przeczytaj i kliknij przycisk **OK** , jeśli akceptujesz te warunki.
10. Zostanie wyświetlona strona **Określanie replik** . Sprawdź ustawienia nowej repliki Azure na kartach **repliki**, **punkty końcowe**i **Preferencje tworzenia kopii zapasowej** . Zmodyfikuj ustawienia, aby spełniały wymagania biznesowe.  Aby uzyskać więcej informacji na temat parametrów zawartych w tych kartach, zobacz [strona Określanie replik (Kreator nowej grupy dostępności/Kreator dodawania repliki)](https://msdn.microsoft.com/library/hh213088.aspx). Należy zauważyć, że odbiorniki nie mogą być tworzone za pomocą karty odbiornik dla grup dostępności zawierających repliki platformy Azure. Ponadto, jeśli odbiornik został już utworzony przed uruchomieniem kreatora, zostanie wyświetlony komunikat z informacją, że nie jest on obsługiwany na platformie Azure. Zawarto informacje na temat tworzenia odbiorników w sekcji **Tworzenie odbiornika grupy dostępności** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Kliknij przycisk **Dalej**.
12. Na stronie **Wybierz początkową synchronizację danych** wybierz metodę synchronizacji danych, a następnie kliknij przycisk **dalej**. W przypadku większości scenariuszy wybierz pozycję **pełna synchronizacja danych**. Aby uzyskać więcej informacji na temat metod synchronizacji danych, zobacz [stronę Wybieranie początkowej synchronizacji danych (zawsze włączone kreatorzy grup dostępności)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Przejrzyj wyniki na stronie **walidacji** . Popraw problemy zaległe i w razie potrzeby Uruchom ponowną weryfikację. Kliknij przycisk **Dalej**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Sprawdź ustawienia na stronie **Podsumowanie** , a następnie kliknij przycisk **Zakończ**.
15. Rozpocznie się proces aprowizacji. Po pomyślnym zakończeniu działania kreatora kliknij przycisk **Zamknij** , aby zakończyć pracę kreatora.

> [!NOTE]
> Kreator dodawania repliki Azure tworzy plik dziennika w Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ten plik dziennika może służyć do rozwiązywania problemów z niepowodzeniem wdrożeniami repliki platformy Azure. Jeśli Kreator nie wykona żadnej akcji, wszystkie poprzednie operacje zostaną wycofane, włącznie z usunięciem zainicjowanej maszyny wirtualnej.
> 
> 

## <a name="create-an-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności
Po utworzeniu grupy dostępności należy utworzyć odbiornik, dla którego klienci mają łączyć się z replikami. Odbiorniki kierują bezpośrednie połączenia przychodzące do podstawowej lub pomocniczej repliki w trybie tylko do odczytu. Aby uzyskać więcej informacji na temat odbiorników, zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Następne kroki
Oprócz korzystania z **Kreatora dodawania repliki platformy Azure** w celu przełączenia zawsze włączonej grupy dostępności na platformę Azure można całkowicie przenieść niektóre SQL Server obciążenia na platformę Azure. Aby rozpocząć, zobacz [Inicjowanie obsługi administracyjnej SQL Server maszyny wirtualnej na platformie Azure](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

