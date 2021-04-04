---
title: Konfigurowanie grupy dostępności (Azure Portal)
description: Użyj Azure Portal, aby utworzyć klaster trybu failover systemu Windows, odbiornik grupy dostępności i wewnętrzny moduł równoważenia obciążenia na maszynie wirtualnej SQL Server na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359510"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Użyj Azure Portal, aby skonfigurować grupę dostępności (wersja zapoznawcza) dla SQL Server na maszynie wirtualnej platformy Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano, jak za pomocą [Azure Portal](https://portal.azure.com) skonfigurować grupę dostępności dla SQL Server na maszynach wirtualnych platformy Azure. 

Użyj Azure Portal, aby utworzyć nowy klaster lub dołączyć istniejący klaster, a następnie utwórz grupę dostępności, odbiornik i wewnętrzną usługę równoważenia obciążenia. 

Ta funkcja jest obecnie w wersji zapoznawczej. 

Chociaż w tym artykule Azure Portal skonfigurować środowisko grupy dostępności, można to również zrobić przy użyciu [programu PowerShell lub interfejsu wiersza polecenia platformy Azure](availability-group-az-commandline-configure.md), [szablonów szybkiego startu platformy Azure](availability-group-quickstart-template-configure.md)lub [ręcznie](availability-group-manually-configure-tutorial.md) . 


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować grupę dostępności zawsze włączona przy użyciu Azure Portal, należy spełnić następujące wymagania wstępne: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Grupa zasobów z kontrolerem domeny. 
- Co najmniej jedna maszyna wirtualna przyłączona [do domeny na platformie Azure z systemem SQL Server 2016 (lub nowszym) Enterprise Edition](./create-sql-vm-portal.md) w *tym samym* zestawie dostępności lub w *różnych* strefach dostępności, które zostały [zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS w trybie pełnego zarządzania](sql-agent-extension-manually-register-single-vm.md) i używają tego samego konta domeny dla usługi SQL Server na każdej maszynie wirtualnej.
- Dostępne są dwa adresy IP (nieużywane przez żadne jednostki). Jeden z nich dotyczy wewnętrznego modułu równoważenia obciążenia. Druga dotyczy odbiornika grupy dostępności w tej samej podsieci, w której znajduje się grupa dostępności. Jeśli używasz istniejącego modułu równoważenia obciążenia, potrzebny jest tylko jeden dostępny adres IP dla odbiornika grupy dostępności. 

## <a name="permissions"></a>Uprawnienia

Aby skonfigurować grupę dostępności przy użyciu Azure Portal, potrzebne są następujące uprawnienia: 

- Istniejące konto użytkownika domeny, które ma uprawnienie **Tworzenie obiektu komputera** w domenie. Na przykład konto administratora domeny ma zwykle wystarczające uprawnienia (na przykład: account@domain.com ). _To konto powinno być również częścią lokalnej grupy administratorów na każdej maszynie wirtualnej w celu utworzenia klastra._
- Konto użytkownika domeny kontrolujące SQL Server. Powinno to być to samo konto dla każdej SQL Serverej maszyny wirtualnej, która ma zostać dodana do grupy dostępności. 

## <a name="configure-cluster"></a>Konfiguruj klaster

Skonfiguruj klaster przy użyciu Azure Portal. Możesz utworzyć nowy klaster lub, jeśli masz już istniejący klaster, możesz dołączyć go do rozszerzenia SQL IaaS Agent, aby móc zarządzać portalem.


### <a name="create-a-new-cluster"></a>Tworzenie nowego klastra

Jeśli masz już klaster, Pomiń tę sekcję i przenieś ją do [istniejącego klastra](#onboard-existing-cluster) . 

Jeśli nie masz jeszcze istniejącego klastra, utwórz go przy użyciu Azure Portal z następującymi krokami:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu [maszyny wirtualnej SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. W obszarze **Ustawienia** wybierz pozycję **wysoka dostępność** . 
1. Wybierz pozycję **+ nowy klaster trybu failover systemu Windows Server** , aby otworzyć stronę **Konfigurowanie klastra trybu failover systemu Windows** .  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Utwórz nowy klaster, wybierając pozycję + nowy klaster w portalu":::

1. Nazwij klaster i podaj konto magazynu, które będzie używane jako monitor w chmurze. Użyj istniejącego konta magazynu lub wybierz pozycję **Utwórz nowe** , aby utworzyć nowe konto magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Podaj nazwę, konto magazynu i poświadczenia dla klastra":::

1. Rozwiń pozycję **poświadczenia klastra trybu failover systemu Windows Server** , aby podać [poświadczenia](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) dla konta usługi SQL Server, a także operator klastra i konta Bootstrap, jeśli są inne niż konto używane dla usługi SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Podaj poświadczenia dla konta usługi SQL, konta operatora klastra i konta Bootstrap klastra":::

1. Wybierz Maszyny wirtualne SQL Server, które chcesz dodać do klastra. Zwróć uwagę, czy jest wymagane ponowne uruchomienie komputera, i postępuj zgodnie z przestrogą. Widoczne są tylko maszyny wirtualne zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS w trybie pełnego zarządzania i znajdują się w tej samej lokalizacji, domenie i w tej samej sieci wirtualnej, co w przypadku podstawowej maszyny wirtualnej SQL Server. 
1. Wybierz pozycję **Zastosuj** , aby utworzyć klaster. Stan wdrożenia można sprawdzić w **dzienniku aktywności** , który jest dostępny z ikony dzwonka na górnym pasku nawigacyjnym. 
1. Aby klaster trybu failover był obsługiwany przez firmę Microsoft, musi on przejść w celu sprawdzenia poprawności klastra. Połącz się z maszyną wirtualną przy użyciu preferowanej metody (takiej jak Remote Desktop Protocol (RDP)) i sprawdź, czy klaster przeszedł sprawdzanie poprawności przed dalszym kontynuowaniem. Niewykonanie tej czynności spowoduje pozostawienie klastra w nieobsługiwanym stanie. Można sprawdzić poprawność klastra przy użyciu Menedżer klastra trybu failover (FCM) lub następującego polecenia programu PowerShell:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Dołącz istniejący klaster

Jeśli masz już klaster skonfigurowany w środowisku SQL Server VM, możesz dołączyć go z Azure Portal.

W tym celu wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu [maszyny wirtualnej SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. W obszarze **Ustawienia** wybierz pozycję **wysoka dostępność** . 
1. Wybierz pozycję Dołącz **istniejący klaster trybu failover systemu Windows Server** , aby otworzyć stronę **klastra trybu failover systemu Windows Server** . 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Dołączanie istniejącego klastra ze strony wysokiej dostępności w zasobie usługi SQL Virtual Machines":::

1. Sprawdź ustawienia klastra. 
1. Wybierz pozycję **Zastosuj** , aby dołączyć klaster, a następnie wybierz pozycję **tak** w wierszu polecenia, aby wykonać operację.

## <a name="create-availability-group"></a>Utwórz grupę dostępności

Po utworzeniu lub dołączeniu klastra utwórz grupę dostępności przy użyciu Azure Portal. W tym celu wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu [maszyny wirtualnej SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. W obszarze **Ustawienia** wybierz pozycję **wysoka dostępność** . 
1. Wybierz pozycję **+ Nowa zawsze włączona Grupa dostępności** , aby otworzyć stronę **Tworzenie grupy dostępności** .

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Wybierz pozycję Nowa zawsze włączona Grupa dostępności, aby otworzyć stronę Tworzenie grupy dostępności.":::

1. Wprowadź nazwę grupy dostępności. 
1. Wybierz pozycję **Konfiguruj odbiornik** , aby otworzyć stronę **Konfigurowanie odbiornika grupy dostępności** . 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Podaj nazwę grupy dostępności i skonfiguruj odbiornik":::

1. Wypełnij wartości, a następnie użyj istniejącego modułu równoważenia obciążenia lub wybierz pozycję **Utwórz nowy** , aby utworzyć nowy moduł równoważenia obciążenia.  Wybierz pozycję **Zastosuj** , aby zapisać ustawienia i utworzyć odbiornik i moduł równoważenia obciążenia. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Wypełnij wartości w formularzu, aby utworzyć nowy odbiornik i moduł równoważenia obciążenia":::

1. Wybierz pozycję **+ Wybierz replikę** , aby otworzyć stronę **Konfigurowanie replik grupy dostępności** .
1. Wybierz Maszyny wirtualne, które chcesz dodać do grupy dostępności, a następnie wybierz ustawienia grupy dostępności, które najlepiej odpowiadają potrzebom biznesowym. Wybierz pozycję **Zastosuj** , aby zapisać ustawienia. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Wybierz Maszyny wirtualne, które chcesz dodać do grupy dostępności, a następnie skonfiguruj ustawienia odpowiednie dla Twojej firmy":::

1. Sprawdź ustawienia grupy dostępności, a następnie wybierz pozycję **Zastosuj** , aby utworzyć grupę dostępności. 

Stan wdrożenia można sprawdzić w **dzienniku aktywności** , który jest dostępny z ikony dzwonka na górnym pasku nawigacyjnym. 

  > [!NOTE]
  > **Kondycja synchronizacji** na stronie **wysokiej dostępności** Azure Portal będzie widoczna jako **niezdrowa** do momentu dodania baz danych do grupy dostępności. 


## <a name="add-database-to-availability-group"></a>Dodaj bazę danych do grupy dostępności

Po zakończeniu wdrożenia Dodaj swoje bazy danych do grupy dostępności. Poniższe kroki wykorzystują SQL Server Management Studio (SSMS), ale można również użyć [języka Transact-SQL lub programu PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . 

Aby dodać bazy danych do grupy dostępności przy użyciu SQL Server Management Studio, wykonaj następujące kroki:

1. Nawiąż połączenie z jedną z maszyn wirtualnych SQL Server przy użyciu preferowanej metody, takiej jak Podłączanie pulpitu zdalnego (RDP). 
1. Otwórz SQL Server Management Studio (SSMS).
1. Nawiąż połączenie z wystąpieniem SQL Server. 
1. Rozwiń pozycję **zawsze w przypadku wysokiej dostępności** w **Eksplorator obiektów**.
1. Rozwiń węzeł **grupy dostępności**, kliknij prawym przyciskiem myszy grupę dostępności, a następnie wybierz polecenie **Dodaj bazę danych.**...

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Kliknij prawym przyciskiem myszy grupę dostępności w Eksploratorze obiektów i wybierz polecenie Dodaj bazę danych":::

1. Postępuj zgodnie z monitami, aby wybrać bazy danych, które chcesz dodać do grupy dostępności. 
1. Wybierz **przycisk OK** , aby zapisać ustawienia i dodać bazę danych do grupy dostępności. 
1. Po dodaniu bazy danych Odśwież **Eksplorator obiektów** , aby potwierdzić stan bazy danych jako `synchronized` . 

Po dodaniu baz danych można sprawdzić stan grupy dostępności w Azure Portal: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Sprawdź stan grupy dostępności na stronie wysokiej dostępności z Azure Portal po zsynchronizowaniu baz danych":::

## <a name="add-more-vms"></a>Dodaj więcej maszyn wirtualnych

Aby dodać więcej SQL Server maszyn wirtualnych do klastra, wykonaj następujące kroki: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu [maszyny wirtualnej SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. W obszarze **Ustawienia** wybierz pozycję **wysoka dostępność** . 
1. Wybierz pozycję **Konfiguruj klaster trybu failover systemu Windows Server** , aby otworzyć stronę **Konfigurowanie klastra trybu failover systemu Windows Server** . 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Wybierz pozycję Konfiguruj klaster trybu failover systemu Windows Server, aby dodać maszyny wirtualne do klastra.":::

1. Rozwiń pozycję **poświadczenia klastra trybu failover systemu Windows Server** i wprowadź konta używane dla usług SQL Server, operatora klastra i kont Bootstrap klastra. 
1. Wybierz Maszyny wirtualne SQL Server, które chcesz dodać do klastra. 
1. Wybierz przycisk **Zastosuj**. 

Stan wdrożenia można sprawdzić w **dzienniku aktywności** , który jest dostępny z ikony dzwonka na górnym pasku nawigacyjnym. 


## <a name="modify-availability-group"></a>Modyfikuj grupę dostępności 


Możesz **dodać więcej replik** do grupy dostępności, **skonfigurować odbiornik** lub **usunąć odbiornik** ze strony **wysokiej dostępności** w Azure Portal, wybierając wielokropek (...) obok grupy dostępności: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Wybierz wielokropek obok grupy dostępności, a następnie wybierz pozycję Dodaj replikę, aby dodać więcej replik do grupy dostępności.":::

## <a name="remove-cluster"></a>Usuń klaster

Usuń wszystkie SQL Server maszyny wirtualne z klastra, aby je zniszczyć, a następnie usuń metadane klastra z rozszerzenia programu SQL IaaS Agent. Można to zrobić przy użyciu najnowszej wersji [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub programu PowerShell. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw usuń wszystkie SQL Server maszyny wirtualne z klastra. Spowoduje to fizyczne usunięcie węzłów z klastra i zniszczenie klastra:  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Jeśli są to jedyne maszyny wirtualne w klastrze, klaster zostanie zniszczony. Jeśli w klastrze znajdują się inne maszyny wirtualne, oprócz SQL Server maszyn wirtualnych, które zostały usunięte, inne maszyny wirtualne nie zostaną usunięte i klaster nie zostanie zniszczony. 

Następnie usuń metadane klastra z rozszerzenia programu SQL IaaS Agent: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw usuń wszystkie SQL Server maszyny wirtualne z klastra. Spowoduje to fizyczne usunięcie węzłów z klastra i zniszczenie klastra: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Jeśli są to jedyne maszyny wirtualne w klastrze, klaster zostanie zniszczony. Jeśli w klastrze znajdują się inne maszyny wirtualne, oprócz SQL Server maszyn wirtualnych, które zostały usunięte, inne maszyny wirtualne nie zostaną usunięte i klaster nie zostanie zniszczony. 


Następnie usuń metadane klastra z rozszerzenia programu SQL IaaS Agent: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy, możesz sprawdzić historię wdrożenia i przejrzeć typowe błędy oraz ich rozwiązania. 

### <a name="check-deployment-history"></a>Sprawdź historię wdrożenia

Zmiany w klastrze i grupie dostępności za pośrednictwem portalu są wykonywane przez wdrożenia. Historia wdrożenia może zawierać więcej szczegółów w przypadku problemów z tworzeniem lub dołączania klastra lub tworzenia grupy dostępności.

Aby wyświetlić dzienniki wdrożenia i sprawdzić historię wdrożenia, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Przejdź do grupy zasobów.
1. Wybierz pozycję **wdrożenia** w obszarze **Ustawienia**.
1. Wybierz wdrożenie zainteresowania, aby dowiedzieć się więcej o wdrożeniu. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Wybierz wdrożenie, które chcesz poznać." :::

### <a name="common-errors"></a>Typowe błędy

Przejrzyj następujące typowe błędy i ich rozwiązania. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Konto używane do uruchamiania usługi SQL nie jest kontem domeny

Oznacza to, że dostawca zasobów nie mógł uzyskać dostępu do usługi SQL Server przy użyciu podanych poświadczeń. Niektóre typowe rozwiązania:
- Upewnij się, że kontroler domeny jest uruchomiony.
- Sprawdź poprawność poświadczeń podanych w portalu, które pasują do tych usługi SQL Server. 


## <a name="next-steps"></a>Następne kroki


Aby uzyskać więcej informacji na temat grup dostępności, zobacz:

- [Przegląd grup dostępności](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Administrowanie grupą dostępności](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorowanie grup dostępności &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Instrukcje języka Transact-SQL grupy dostępności ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Polecenia programu PowerShell dotyczące grup dostępności](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Aby uzyskać więcej informacji na temat maszyn wirtualnych SQL Server, zobacz: 

* [Przegląd SQL Server maszyn wirtualnych](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Informacje o wersji dla maszyn wirtualnych SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Często zadawane pytania dotyczące SQL Server maszyn wirtualnych](frequently-asked-questions-faq.md)
