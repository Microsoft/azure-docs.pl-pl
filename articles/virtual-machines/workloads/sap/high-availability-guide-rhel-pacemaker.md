---
title: Konfigurowanie Pacemaker na RHEL na platformie Azure | Microsoft Docs
description: Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: radeltch
ms.openlocfilehash: 6e906e6c86d615852191e2fd65a2b1a58695ed34
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968557"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Przeczytaj najpierw następujące informacje i dokumenty SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane programy SAP i system operacyjny oraz kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure.
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2002167] zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze Pacemaker](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — SBD i fence_sbd](https://access.redhat.com/articles/2800691)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Zagadnienia dotyczące wdrażania RHEL 8 — wysoka dostępność i klastry](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Konfigurowanie protokołu SAP S/4HANA ASCS/wykres WYWOŁUJĄCYCH przy użyciu autonomicznej kolejki serwer 2 (ENSA2) w Pacemaker na RHEL 7,6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalacja klastra

![Pacemaker na RHEL — Omówienie](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat nie obsługuje emulowanego przez oprogramowanie licznika alarmowego. Red Hat nie obsługuje SBD na platformach w chmurze. Aby uzyskać szczegółowe informacje, zobacz [zasady pomocy technicznej dotyczące klastrów RHEL o wysokiej dostępności — SBD i fence_sbd](https://access.redhat.com/articles/2800691).
> Jedynym obsługiwanym mechanizmem ogrodzenia dla klastrów Pacemaker Red Hat Enterprise Linux na platformie Azure jest Agent usługi Azure ogrodzeni.  

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** Zarejestruj się. Ten krok nie jest wymagany, jeśli używany jest obraz RHEL 8. x z obsługą wysokiej dostępności.  

   Zarejestruj maszyny wirtualne i Dołącz je do puli zawierającej repozytoria dla RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Po dołączeniu puli do obrazu programu RHEL z witryny Azure Marketplace w witrynie PAYG będzie można efektywnie obciążać użycie RHEL: jeden dla obrazu PAYG i jeden raz dla uprawnienia RHEL w puli, którą dołączysz. Aby rozwiązać ten problem, platforma Azure udostępnia teraz obrazy BYOS RHEL. Więcej informacji jest dostępnych [tutaj](../redhat/byos.md).

1. **[A]** Włącz RHEL dla repozytoriów SAP. Ten krok nie jest wymagany, jeśli używany jest obraz RHEL 8. x z obsługą wysokiej dostępności.  

   Aby zainstalować wymagane pakiety, należy włączyć następujące repozytoria.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Zainstaluj Add-On ha RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Firma Microsoft zaleca, aby klienci korzystali z szybszych czasów pracy awaryjnej (lub nowszych), jeśli zakończy się niepowodzeniem, lub węzły klastra nie mogą komunikować się, które są już takie:  
   > RHEL 7,7 lub nowszy Użyj najnowszej dostępnej wersji pakietu "Horyzonty" agentów  
   > RHEL 7,6: ogrodzeni-Agents-4.2.1-11.el7_6.8  
   > RHEL 7,5: ogrodzeni-Agents-4.0.11-86.el7_5.8  
   > RHEL 7,4: ogrodzeni-Agents-4.0.11-66.el7_4.12  
   > Aby uzyskać więcej informacji, zobacz [maszynę wirtualną platformy Azure działającą jako SKŁADOWA RHEL o wysokiej dostępności trwa bardzo długo lub nie można przekroczyć limitu czasu przed zamknięciem maszyny wirtualnej](https://access.redhat.com/solutions/3408711).

   Sprawdź wersję agenta usługi Azure ogrodzenia. W razie potrzeby zaktualizuj ją do wersji równej lub nowszej niż podane powyżej.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Jeśli trzeba zaktualizować agenta usługi Azure ogrodzenia, a jeśli jest używana rola niestandardowa, należy zaktualizować rolę niestandardową w celu uwzględnienia akcji **wyłączenie**. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie roli niestandardowej dla agenta ogranicznika](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach.  

   >[!IMPORTANT]
   > W przypadku używania nazw hostów w konfiguracji klastra niezbędne jest niezawodne rozpoznawanie nazw hostów. Komunikacja klastra zakończy się niepowodzeniem, jeśli nazwy nie są dostępne i mogą prowadzić do opóźnień w przypadku awarii klastra.
   > Zaletą korzystania z/etc/hosts jest to, że klaster będzie niezależny od systemu DNS, co może być tylko pojedynczym punktem awarii.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Zmień hasło hacluster na to samo hasło

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** dodawanie reguł zapory dla Pacemaker

   Dodaj następujące reguły zapory do całej komunikacji między węzłami klastra.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Włącz podstawowe usługi klastra

   Uruchom następujące polecenia, aby włączyć usługę Pacemaker i ją uruchomić.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Utwórz klaster Pacemaker

   Uruchom następujące polecenia, aby uwierzytelnić węzły i utworzyć klaster. Ustaw token na 30000, aby zezwalać na konserwację pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance].  
   
   Jeśli tworzysz klaster na **RHEL 7. x**, użyj następujących poleceń:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Jeśli tworzysz klaster na **RHEL 8. X**, użyj następujących poleceń:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Sprawdź stan klastra, wykonując następujące polecenie:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Ustaw oczekiwane głosy. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Jeśli tworzysz klaster z wieloma węzłami, który jest klastrem z więcej niż dwoma węzłami, nie ustawiaj głosów na 2.    

1. **[1]** Zezwalaj na akcje współbieżnych ogrodzenia

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Utwórz urządzenie STONITH

Urządzenie STONITH używa nazwy głównej usługi do autoryzacji przed Microsoft Azure. Wykonaj następujące kroki, aby utworzyć nazwę główną usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwórz blok Azure Active Directory  
   Przejdź do pozycji właściwości i zanotuj identyfikator katalogu. To jest **Identyfikator dzierżawy**.
1. Kliknij Rejestracje aplikacji
1. Kliknij pozycję Nowa rejestracja
1. Wprowadź nazwę, wybierz pozycję "konta tylko w tym katalogu organizacji". 
2. Wybierz pozycję typ aplikacji "sieć Web", wprowadź adres URL logowania (na przykład http: \/ /localhost), a następnie kliknij przycisk Dodaj.  
   Adres URL logowania nie jest używany i może być dowolnym prawidłowym adresem URL
1. Wybierz pozycję Certyfikaty i wpisy tajne, a następnie kliknij pozycję Nowy wpis tajny klienta.
1. Wprowadź opis nowego klucza, wybierz pozycję "nigdy nie wygasa" i kliknij przycisk Dodaj.
1. Ustaw wartość w węźle. Służy jako **hasło** dla nazwy głównej usługi
1. Wybierz pozycję Omówienie. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (**Identyfikator logowania** w poniższych krokach) nazwy głównej usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Utwórz rolę niestandardową dla agenta ogranicznika

Jednostka usługi nie ma uprawnień dostępu do zasobów platformy Azure domyślnie. Należy nadać uprawnienia główne usługi, aby uruchomić i zatrzymać (wyłączyć) wszystkie maszyny wirtualne w klastrze. Jeśli rola niestandardowa nie została jeszcze utworzona, możesz ją utworzyć przy użyciu [programu PowerShell](../../../role-based-access-control/role-assignments-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](../../../role-based-access-control/role-assignments-cli.md)

Użyj następującej zawartości dla pliku wejściowego. Musisz dostosować zawartość do swoich subskrypcji, zastępując c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 identyfikatorami subskrypcji. Jeśli masz tylko jedną subskrypcję, Usuń drugą pozycję w AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Przypisz rolę niestandardową do jednostki usługi

Przypisz rolę niestandardową "rola agenta ogranicznika systemu Linux" utworzoną w ostatnim rozdziale do jednostki usługi. Nie używaj już roli właściciela!

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok wszystkie zasoby
1. Wybierz maszynę wirtualną pierwszego węzła klastra
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij pozycję Dodaj przypisanie roli
1. Wybierz rolę "rola agenta ogranicznika systemu Linux"
1. Wprowadź nazwę utworzonej aplikacji
1. Klikanie pozycji Zapisz.

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]** tworzenie urządzeń STONITH

Po edytowaniu uprawnień dla maszyn wirtualnych można skonfigurować urządzenia STONITH w klastrze.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> Opcja "pcmk_host_map" jest wymagana tylko w poleceniu, jeśli nazwy hosta RHEL i nazwy węzłów platformy Azure nie są identyczne. Zapoznaj się z sekcją pogrubienie w poleceniu.

Dla RHEL **7. X** Użyj następującego polecenia, aby skonfigurować urządzenie ogrodzenia:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

W przypadku RHEL **8. X** Użyj następującego polecenia, aby skonfigurować urządzenie ogrodzenia:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> Operacje monitorowania i ogrodzenia są deserializowane. W związku z tym, jeśli istnieje już uruchomiona operacja monitorowania i jednoczesne zdarzenie ogrodzenia, nie istnieje opóźnienie dla klastra w trybie failover z powodu już uruchomionej operacji monitorowania.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Włącz korzystanie z urządzenia STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Agent usługi Azure ogrodzenia wymaga łączności wychodzącej z publicznymi punktami końcowymi zgodnie z opisem, a także z możliwymi rozwiązaniami w [publicznej łączności punktu końcowego dla maszyn wirtualnych używających standardowej ILB](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
