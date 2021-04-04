---
title: Konfigurowanie grup dostępności dla SQL Server na maszynach wirtualnych RHEL na maszynach wirtualnych platformy Azure z systemem Linux | Microsoft Docs
description: Dowiedz się więcej o konfigurowaniu wysokiej dostępności w środowisku klastra RHEL i konfigurowaniu STONITH
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 533f5c9e38818a8e37482cbbb3a90602366eca6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587217"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Samouczek: Konfigurowanie grup dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> W tym samouczku używamy SQL Server 2017 z RHEL 7,6, ale można użyć SQL Server 2019 w RHEL 7 lub RHEL 8, aby skonfigurować wysoką dostępność. Polecenia służące do konfigurowania klastra pacemake i zasobów grupy dostępności uległy zmianie w RHEL 8. warto zapoznać się z artykułem [Tworzenie zasobu grupy dostępności](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) i zasobów RHEL 8, aby uzyskać więcej informacji na temat poprawnych poleceń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie nowej grupy zasobów, zestawu dostępności i maszyn wirtualnych z systemem Linux (VM)
> - Włącz wysoką dostępność (HA)
> - Tworzenie klastra Pacemaker
> - Konfigurowanie Agenta ogrodzenia przez utworzenie urządzenia STONITH
> - Instalowanie SQL Server i narzędzi MSSQL w systemie RHEL
> - Skonfiguruj zawsze włączona Grupa dostępności SQL Server
> - Konfigurowanie zasobów grupy dostępności (AG) w klastrze Pacemaker
> - Testowanie trybu failover i Agenta ogrodzenia

Ten samouczek będzie używać interfejsu wiersza polecenia platformy Azure do wdrażania zasobów na platformie Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.30 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli masz więcej niż jedną subskrypcję, [Ustaw subskrypcję](/cli/azure/manage-azure-subscriptions-azure-cli) , do której chcesz wdrożyć te zasoby.

Użyj następującego polecenia, aby utworzyć grupę zasobów `<resourceGroupName>` w regionie. Zamień na `<resourceGroupName>` wybraną nazwę. Korzystamy z `East US 2` tego samouczka. Aby uzyskać więcej informacji, zobacz poniższy [Przewodnik Szybki Start](../../../application-gateway/quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Następnym krokiem jest utworzenie zestawu dostępności. Uruchom następujące polecenie w Azure Cloud Shell i Zastąp wartość `<resourceGroupName>` nazwą grupy zasobów. Wybierz nazwę dla `<availabilitySetName>` .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Po zakończeniu wykonywania polecenia należy uzyskać następujące wyniki:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Tworzenie maszyn wirtualnych RHEL wewnątrz zestawu dostępności

> [!WARNING]
> Jeśli wybierzesz obraz RHEL z opcją płatność zgodnie z rzeczywistym użyciem i skonfigurujesz wysoką dostępność (HA), może być konieczne zarejestrowanie subskrypcji. Może to spowodować dwukrotne zapłacenie subskrypcji, ponieważ zostanie naliczona opłata za subskrypcję usługi Microsoft Azure RHEL dla maszyny wirtualnej i subskrypcję usługi Red Hat. Aby uzyskać więcej informacji, zobacz https://access.redhat.com/solutions/2458541.
>
> Aby uniknąć "podwójnego rozliczania", Użyj obrazu RHEL HA podczas tworzenia maszyny wirtualnej platformy Azure. Obrazy oferowane jako obrazy RHEL-HA to również obrazy PAYG z wstępnie włączonym repozytorium HA.

1. Pobierz listę obrazów maszyn wirtualnych oferujących RHEL o wysokiej dostępności:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    W tym samouczku wybieramy obraz `RedHat:RHEL-HA:7.6:7.6.2019062019` dla przykładu RHEL 7 i wybieramy `RedHat:RHEL-HA:8.1:8.1.2020021914` przykład RHEL 8.
    
    Możesz również wybrać SQL Server 2019 wstępnie zainstalowane na obrazach RHEL8-HA. Aby uzyskać listę tych obrazów, uruchom następujące polecenie:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    W przypadku tworzenia maszyn wirtualnych za pomocą jednego z powyższych obrazów zostanie zainstalowany SQL Server 2019. Pomiń sekcję [Install SQL Server i MSSQL-Tools](#install-sql-server-and-mssql-tools) zgodnie z opisem w tym artykule.
    
    
    > [!IMPORTANT]
    > Aby można było skonfigurować grupę dostępności, nazwy maszyn muszą mieć mniej niż 15 znaków. Nazwa użytkownika nie może zawierać wielkich liter, a hasła muszą składać się z więcej niż 12 znaków.

1. Chcemy utworzyć 3 maszyny wirtualne w zestawie dostępności. W poniższym poleceniu Zastąp następujące polecenie:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` — Przykładem może być "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Powyższe polecenie tworzy maszyny wirtualne i tworzy domyślną sieć wirtualną dla tych maszyn wirtualnych. Aby uzyskać więcej informacji o różnych konfiguracjach, zobacz [AZ VM Create](/cli/azure/vm) article.

Po zakończeniu wykonywania polecenia dla każdej maszyny wirtualnej powinny zostać wyświetlone wyniki podobne do następujących:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Domyślny obraz tworzony za pomocą powyższego polecenia domyślnie tworzy dysk o 32 GB systemu operacyjnego. W przypadku tej instalacji domyślnej można było za mało miejsca. Do powyższego polecenia można użyć następującego parametru, `az vm create` Aby utworzyć dysk systemu operacyjnego z 128 GB na przykład: `--os-disk-size-gb 128` .
>
> Następnie można [skonfigurować Menedżera woluminów logicznych (LVM)](/previous-versions/azure/virtual-machines/linux/configure-lvm) , jeśli konieczne jest rozwinięcie odpowiednich woluminów folderu w celu zaspokojenia instalacji.

### <a name="test-connection-to-the-created-vms"></a>Test connection utworzonych maszyn wirtualnych

Połącz się z usługą VM1 lub innymi maszynami wirtualnymi przy użyciu następującego polecenia w Azure Cloud Shell. Jeśli nie możesz znaleźć adresów IP maszyn wirtualnych, postępuj zgodnie z tym [przewodnikiem Szybki Start na Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

W przypadku pomyślnego nawiązania połączenia powinny zostać wyświetlone następujące dane wyjściowe reprezentujące terminal systemu Linux:

```output
[<username>@<VM1> ~]$
```

Wpisz `exit` , aby opuścić sesję SSH.

## <a name="enable-high-availability"></a>Włącz wysoką dostępność

> [!IMPORTANT]
> Aby ukończyć tę część samouczka, musisz mieć subskrypcję usługi RHEL i dodatek o wysokiej dostępności. Jeśli używasz obrazu zalecanego w poprzedniej sekcji, nie musisz rejestrować kolejnej subskrypcji.
 
Połącz się z każdym węzłem maszyny wirtualnej i postępuj zgodnie z poniższym przewodnikiem, aby włączyć HA. Aby uzyskać więcej informacji, zobacz [Włączanie subskrypcji wysokiej dostępności dla RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Będzie to łatwiejsze, jeśli otworzysz sesję SSH do każdej z maszyn wirtualnych jednocześnie, ponieważ te same polecenia będą musiały zostać uruchomione na poszczególnych maszynach wirtualnych w całym artykule.
>
> Jeśli kopiujesz i wklejasz wiele `sudo` poleceń i zostanie wyświetlony monit o podanie hasła, dodatkowe polecenia nie zostaną uruchomione. Uruchom każde polecenie osobno.


1. Uruchom następujące polecenia na każdej maszynie wirtualnej, aby otworzyć porty zapory Pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Zaktualizuj i zainstaluj pakiety Pacemaker na wszystkich węzłach przy użyciu następujących poleceń:

    > [!NOTE]
    > **Nmap** jest instalowany w ramach tego bloku poleceń jako narzędzie do znajdowania dostępnych adresów IP w sieci. Nie musisz instalować **Nmap**, ale będzie to przydatne w dalszej części tego samouczka.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Ustaw hasło dla domyślnego użytkownika tworzonego podczas instalowania pakietów Pacemaker. Użyj tego samego hasła we wszystkich węzłach.

    ```bash
    sudo passwd hacluster
    ```

1. Użyj poniższego polecenia, aby otworzyć plik Hosts i skonfigurować rozpoznawanie nazw hostów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) na konfigurowaniu pliku Hosts.

    ```
    sudo vi /etc/hosts
    ```

    W edytorze **VI** wpisz polecenie, `i` Aby wstawić tekst, a w pustym wierszu Dodaj **prywatny adres IP** odpowiedniej maszyny wirtualnej. Następnie Dodaj nazwę maszyny wirtualnej po miejscu obok adresu IP. Każdy wiersz powinien mieć oddzielny wpis.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Zalecamy korzystanie z powyższego **prywatnego adresu IP** . Użycie publicznego adresu IP w tej konfiguracji spowoduje niepowodzenie instalacji i nie zalecamy uwidaczniania maszyny wirtualnej w sieciach zewnętrznych.

    Aby wyjść z edytora **VI** , najpierw naciśnij klawisz **ESC** , a następnie wprowadź polecenie, `:wq` Aby napisać plik i zakończyć pracę.

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

W tej sekcji zostanie włączona i uruchomiona usługa pcsd, a następnie zostanie skonfigurowany klaster. W przypadku SQL Server on Linux zasoby klastra nie są tworzone automatycznie. Należy ręcznie włączyć i utworzyć zasoby Pacemaker. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [konfigurowania wystąpienia klastra trybu failover dla usługi RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Włączanie i uruchamianie usługi pcsd oraz Pacemaker

1. Uruchom polecenia we wszystkich węzłach. Te polecenia umożliwiają węzłom ponowne dołączenie do klastra po ponownym uruchomieniu.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Usuń istniejącą konfigurację klastra ze wszystkich węzłów. Uruchom następujące polecenie:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. W węźle podstawowym Uruchom następujące polecenia, aby skonfigurować klaster.

    - Po uruchomieniu `pcs cluster auth` polecenia w celu uwierzytelnienia węzłów klastra zostanie wyświetlony monit o podanie hasła. Wprowadź hasło utworzone wcześniej przez użytkownika **hacluster** .

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    W przypadku RHEL 8 konieczne będzie uwierzytelnianie węzłów oddzielnie. Po wyświetleniu monitu wprowadź ręcznie nazwę użytkownika i hasło dla **hacluster** .

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy wszystkie węzły są w trybie online.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Jeśli wszystkie węzły są w trybie online, zostaną wyświetlone dane wyjściowe podobne do następujących:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Ustaw oczekiwane głosy w klastrze na żywo na 3. To polecenie ma wpływ tylko na aktywny klaster i nie zmienia plików konfiguracji.

    Na wszystkich węzłach Ustaw oczekiwane głosy przy użyciu następującego polecenia:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Skonfiguruj Agenta ogrodzenia

Urządzenie STONITH udostępnia Agenta ogrodzenia. Poniższe instrukcje są modyfikowane w tym samouczku. Aby uzyskać więcej informacji, zobacz [Tworzenie urządzenia STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Sprawdź wersję agenta usługi Azure ogrodzenia, aby upewnić się, że został zaktualizowany](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Użyj następującego polecenia:

```bash
sudo yum info fence-agents-azure-arm
```

Powinny być widoczne podobne dane wyjściowe do poniższego przykładu.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Zarejestruj nową aplikację w Azure Active Directory
 
 1. Przejdź do strony https://portal.azure.com
 2. Otwórz [blok Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Przejdź do pozycji właściwości i Zapisz identyfikator katalogu. Jest to `tenant ID`
 3. Kliknij [ **rejestracje aplikacji**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Kliknij pozycję **Nowa rejestracja**
 5. Wprowadź **nazwę** , na przykład `<resourceGroupName>-app` , wybierz **konta tylko w tym katalogu organizacji**
 6. Wybierz pozycję typ aplikacji **Sieć Web**, wprowadź adres URL logowania (na przykład http://localhost) , a następnie kliknij przycisk Dodaj. Adres URL logowania nie jest używany i może być dowolnym prawidłowym adresem URL. Po zakończeniu kliknij pozycję **zarejestruj** .
 7. Wybierz pozycję **Certyfikaty i wpisy tajne** dla nowej rejestracji aplikacji, a następnie kliknij pozycję **nowy klucz tajny klienta** .
 8. Wprowadź opis nowego klucza (klucz tajny klienta), wybierz pozycję **nigdy nie wygasa** , a następnie kliknij przycisk **Dodaj** .
 9. Zapisz wartość klucza tajnego. Służy jako hasło dla nazwy głównej usługi
10. Wybierz pozycję **Omówienie**. Zapisz identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (identyfikator logowania w poniższych krokach) nazwy głównej usługi
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Utwórz rolę niestandardową dla agenta ogranicznika

Postępuj zgodnie z samouczkiem, aby [utworzyć rolę niestandardową Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Plik JSON powinien wyglądać podobnie do poniższego:

- Zastąp ciąg `<username>` wybraną nazwą. Ma to na celu uniknięcie duplikowania podczas tworzenia tej definicji roli.
- Zastąp ciąg `<subscriptionId>` identyfikatorem subskrypcji platformy Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Aby dodać rolę, uruchom następujące polecenie:

- Zamień na `<filename>` nazwę pliku.
- Jeśli wykonujesz polecenie z ścieżki innej niż folder, w którym zapisano plik, dołącz ścieżkę do pliku w poleceniu.

```bash
az role definition create --role-definition "<filename>.json"
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Przypisywanie roli niestandardowej do nazwy głównej usługi

Przypisz rolę niestandardową `Linux Fence Agent Role-<username>` utworzoną w ostatnim kroku do nazwy głównej usługi. Nie używaj już roli właściciela!
 
1. Przejdź do strony https://portal.azure.com
2. Otwórz [blok wszystkie zasoby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Wybierz maszynę wirtualną pierwszego węzła klastra
4. Kliknij pozycję **Kontrola dostępu (IAM)**
5. Kliknij pozycję **Dodaj przypisanie roli**
6. Wybierz rolę `Linux Fence Agent Role-<username>` z listy **ról**
7. Z listy **Wybierz** wprowadź nazwę utworzonej aplikacji, `<resourceGroupName>-app`
8. Kliknij pozycję **Zapisz**.
9. Powtórz powyższe kroki dla węzła wszystkie klastry.

### <a name="create-the-stonith-devices"></a>Tworzenie urządzeń STONITH

Uruchom następujące polecenia w węźle 1:

- Zamień na `<ApplicationID>` wartość identyfikatora z rejestracji aplikacji.
- Zastąp `<servicePrincipalPassword>` wartość wartością z wpisu tajnego klienta.
- Zastąp wartość `<resourceGroupName>` with grupy zasobów z Twojej subskrypcji, która została użyta w tym samouczku.
- Zastąp wartość `<tenantID>` i `<subscriptionId>` z subskrypcji platformy Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Ponieważ została już dodana reguła do zapory, aby zezwolić na usługę HA ( `--add-service=high-availability` ), nie ma potrzeby otwierania następujących portów zapory na wszystkich węzłach: 2224, 3121, 21064, 5405. Jeśli jednak występują problemy z połączeniem z HA, użyj następującego polecenia, aby otworzyć te porty, które są skojarzone z HA.

> [!TIP]
> Opcjonalnie możesz dodać wszystkie porty w tym samouczku, aby zaoszczędzić trochę czasu. Porty, które należy otworzyć, zostały omówione w sekcji względnej poniżej. Jeśli chcesz teraz dodać wszystkie porty, Dodaj dodatkowe porty: 1433 i 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalowanie SQL Server i narzędzi MSSQL

> [!NOTE]
> Jeśli maszyny wirtualne zostały utworzone przy użyciu wstępnie zainstalowanej SQL Server 2019 w systemie RHEL8-HA, możesz pominąć poniższe kroki, aby zainstalować narzędzia SQL Server i MSSQL-Tools i uruchomić sekcję **Konfigurowanie grupy dostępności** po skonfigurowaniu hasła administratora systemu na wszystkich maszynach wirtualnych, uruchamiając polecenie `sudo /opt/mssql/bin/mssql-conf set-sa-password` na wszystkich maszynach wirtualnych.

Poniższa sekcja służy do instalowania SQL Server i narzędzi MSSQL na maszynach wirtualnych. Możesz wybrać jedną z poniższych przykładów, aby zainstalować SQL Server 2017 w RHEL 7 lub SQL Server 2019 w RHEL 8. Wykonaj każdą z tych akcji na wszystkich węzłach. Aby uzyskać więcej informacji, zobacz [installing SQL Server in the Red Hat VM](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>Instalowanie SQL Server na maszynach wirtualnych

Następujące polecenia służą do instalowania SQL Server:

**RHEL 7 z SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 z SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Otwórz port zapory 1433 dla połączeń zdalnych

Aby można było połączyć się zdalnie, należy otworzyć port 1433 na maszynie wirtualnej. Użyj następujących poleceń, aby otworzyć port 1433 w zaporze każdej maszyny wirtualnej:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalowanie SQL Server narzędzi wiersza polecenia

Następujące polecenia służą do instalowania SQL Server narzędzia wiersza polecenia. Aby uzyskać więcej informacji, zobacz [instalowanie SQL Server narzędzia wiersza polecenia](/sql/linux/quickstart-install-connect-red-hat#tools).

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Dla wygody Dodaj/opt/MSSQL-Tools/bin/do zmiennej środowiskowej PATH. Dzięki temu można uruchamiać narzędzia bez określania pełnej ścieżki. Uruchom następujące polecenia, aby zmodyfikować zmienną PATH zarówno dla sesji logowania, jak i sesji interaktywnych/bez logowania:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Sprawdź stan SQL Server

Po zakończeniu konfiguracji można sprawdzić stan SQL Server i sprawdzić, czy jest on uruchomiony:

```bash
systemctl status mssql-server --no-pager
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Konfigurowanie grupy dostępności

Wykonaj następujące kroki, aby skonfigurować grupę dostępności dla maszyn wirtualnych w SQL Server zawsze włączona. Aby uzyskać więcej informacji, zobacz [SQL Server Konfigurowanie grup dostępności, które są zawsze włączone w systemie Linux](/sql/linux/sql-server-linux-availability-group-configure-ha) .

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Włącz zawsze włączone grupy dostępności i uruchom ponownie program MSSQL-Server

Włącz zawsze włączone grupy dostępności na każdym węźle, który hostuje wystąpienie SQL Server. Następnie uruchom ponownie program MSSQL-Server. Uruchom poniższy skrypt:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Tworzenie certyfikatu

Obecnie nie obsługujemy uwierzytelniania usługi AD w punkcie końcowym AG. W związku z tym musimy używać certyfikatu do szyfrowania za pomocą usługi AG.

1. Połącz się ze **wszystkimi węzłami** przy użyciu narzędzia SQL Server Management Studio (SSMS) lub polecenia SQL. Uruchom następujące polecenia, aby włączyć sesję AlwaysOn_health i utworzyć klucz główny:

    > [!IMPORTANT]
    > Jeśli łączysz się zdalnie z wystąpieniem SQL Server, musisz mieć otwarty port 1433 w zaporze. Należy również zezwolić na połączenia przychodzące do portu 1433 w sieciowej grupy zabezpieczeń dla każdej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) na potrzeby tworzenia reguły zabezpieczeń dla ruchu przychodzącego.

    - Zastąp wartość `<Master_Key_Password>` własnym hasłem.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Połącz się z repliką podstawową przy użyciu programu SSMS lub CMD. Poniższe polecenia spowodują utworzenie certyfikatu przy użyciu `/var/opt/mssql/data/dbm_certificate.cer` klucza prywatnego w `var/opt/mssql/data/dbm_certificate.pvk` replice SQL Server głównej:

    - Zastąp wartość `<Private_Key_Password>` własnym hasłem.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Zakończ sesję programu SQL CMD, uruchamiając `exit` polecenie i wróć do sesji SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Kopiuj certyfikat do replik pomocniczych i Utwórz certyfikaty na serwerze

1. Skopiuj dwa pliki, które zostały utworzone w tej samej lokalizacji na wszystkich serwerach, które będą hostować repliki dostępności.
 
    Na serwerze podstawowym Uruchom następujące `scp` polecenie, aby skopiować certyfikat na serwery docelowe:

    - Zastąp `<username>` `<VM2>` wartości i nazwą użytkownika i docelową nazwą maszyny wirtualnej, której używasz.
    - Uruchom to polecenie dla wszystkich replik pomocniczych.

    > [!NOTE]
    > Nie trzeba uruchamiać programu `sudo -i` , który zapewnia środowisko główne. Po prostu można uruchomić `sudo` polecenie przed każdym poleceniem, tak jak wcześniej w tym samouczku.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Na serwerze docelowym Uruchom następujące polecenie:

    - Zamień `<username>` na nazwę użytkownika.
    - `mv`Polecenie przenosi pliki lub katalog z jednego miejsca do drugiego.
    - `chown`Polecenie służy do zmiany właściciela i grupy plików, katalogów lub linków.
    - Uruchom te polecenia dla wszystkich replik pomocniczych.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Poniższy skrypt języka Transact-SQL tworzy certyfikat z kopii zapasowej utworzonej w replice SQL Server głównej. Zaktualizuj skrypt przy użyciu silnych haseł. Hasło odszyfrowywania to to samo hasło, które zostało użyte do utworzenia pliku. PVK w poprzednim kroku. Aby utworzyć certyfikat, uruchom następujący skrypt przy użyciu programu SQL CMD lub SSMS na wszystkich serwerach pomocniczych:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Utwórz punkty końcowe dublowania bazy danych dla wszystkich replik

Uruchom następujący skrypt na wszystkich wystąpieniach SQL Server przy użyciu polecenia SQL CMD lub programu SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Utwórz grupę dostępności

Połącz się z wystąpieniem SQL Server, które obsługuje replikę podstawową przy użyciu programu SQL CMD lub narzędzia SSMS. Uruchom następujące polecenie, aby utworzyć grupę dostępności:

- Zamień `ag1` na żądaną nazwę grupy dostępności.
- Zastąp `<VM1>` `<VM2>` wartości, i `<VM3>` wartościami nazw wystąpień SQL Server, które obsługują repliki.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Utwórz SQL Server Login dla Pacemaker

Na wszystkich wystąpieniach SQL Server Utwórz SQL Server Login dla Pacemaker. Poniższy kod Transact-SQL tworzy nazwę logowania.

- Zamień `<password>` na własne hasło złożone.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Na wszystkich wystąpieniach SQL Server Zapisz poświadczenia używane do logowania SQL Server. 

1. Utwórz plik:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Dodaj następujące 2 wiersze do pliku:

    ```bash
    pacemakerLogin
    <password>
    ```

    Aby wyjść z edytora **VI** , najpierw naciśnij klawisz **ESC** , a następnie wprowadź polecenie, `:wq` Aby napisać plik i zakończyć pracę.

1. Uczyń plik tylko do odczytu w katalogu głównym:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Sprzęganie replik pomocniczych z grupą dostępności

1. Aby można było przyłączyć repliki pomocnicze do usługi AG, należy otworzyć port 5022 na zaporze dla wszystkich serwerów. Uruchom następujące polecenie w sesji SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. W replikach pomocniczych Uruchom następujące polecenia, aby przyłączyć je do sieci AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Uruchom następujący skrypt języka Transact-SQL w replice podstawowej i każdej pomocniczej replice:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Po przyłączeniu replik pomocniczych można je zobaczyć w programie SSMS Eksplorator obiektów, rozszerzając węzeł **Always On High Availability** :

    ![Zrzut ekranu przedstawia podstawową i pomocniczą replikę dostępności.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Dodawanie bazy danych do grupy dostępności

[Po dodaniu bazy danych będziemy przestrzegać artykułu Konfigurowanie grupy dostępności](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

W tym kroku są używane następujące polecenia języka Transact-SQL. Uruchom następujące polecenia w replice podstawowej:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Sprawdź, czy baza danych została utworzona na serwerach pomocniczych

W każdej pomocniczej replice SQL Server uruchom następujące zapytanie, aby sprawdzić, czy baza danych DB1 została utworzona i czy jest w stanie ZSYNCHRONIZOWANym:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Jeśli `synchronization_state_desc` listy są synchronizowane z `db1` , oznacza to, że repliki są synchronizowane. Serwery pomocnicze są wyświetlane `db1` w replice podstawowej.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Tworzenie zasobów grupy dostępności w klastrze Pacemaker

[W celu utworzenia zasobów grupy dostępności w klastrze Pacemaker](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)będzie dostępny przewodnik.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

### <a name="create-the-ag-cluster-resource"></a>Tworzenie zasobu w klastrze AG

1. Użyj jednego z następujących poleceń w oparciu o środowisko wybrane wcześniej, aby utworzyć zasób `ag_cluster` w grupie dostępności `ag1` .

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Sprawdź zasób i upewnij się, że są w trybie online przed kontynuowaniem pracy przy użyciu następującego polecenia:

    ```bash
    sudo pcs resource
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Tworzenie zasobu wirtualnego adresu IP

1. Użyj dostępnego statycznego adresu IP z sieci, aby utworzyć zasób wirtualnego adresu IP. Można go znaleźć za pomocą narzędzia poleceń `nmap` .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Ustaw właściwość **stonith** na wartość false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Utwórz zasób wirtualnego adresu IP za pomocą następującego polecenia:

    - Zastąp `<availableIP>` wartość poniżej nieużywanym adresem IP.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Dodaj ograniczenia

1. Aby upewnić się, że adres IP i Grupa dostępności są uruchomione w tym samym węźle, należy skonfigurować ograniczenie między lokalizacjami. Uruchom następujące polecenie:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Utwórz ograniczenie porządkowania, aby upewnić się, że w ramach tego zasobu jest uruchomiona wartość "AG" przed adresem IP. Chociaż ograniczenie wspólnej lokalizacji implikuje ograniczenie kolejności, wymusza je.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Aby sprawdzić ograniczenia, uruchom następujące polecenie:

    ```bash
    sudo pcs constraint list --full
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Włącz ponownie stonith

Jesteśmy gotowi do testowania. Ponownie włącz stonith w klastrze, uruchamiając następujące polecenie w węźle 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Stan zasobów klastra można sprawdzić za pomocą następującego polecenia:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Aby upewnić się, że konfiguracja zakończyła się pomyślnie, przetestujemy tryb failover. Aby uzyskać więcej informacji, zobacz [zawsze włączone przełączanie do trybu failover grupy dostępności w systemie Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Uruchom następujące polecenie, aby ręcznie przełączyć replikę podstawową do trybu failover `<VM2>` . Zamień `<VM2>` na wartość nazwy serwera.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   Możesz również określić dodatkową opcję, aby tymczasowe ograniczenie, które zostało utworzone w celu przeniesienia zasobu do żądanego węzła, zostało wyłączone automatycznie i nie trzeba wykonywać kroków 2 i 3 poniżej.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Kolejną alternatywą dla automatyzowania kroków 2 i 3 poniżej, które czyści ograniczenie tymczasowe w samym poleceniu Przenieś zasób, jest połączenie wielu poleceń w jednym wierszu. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Jeśli ponownie sprawdzisz swoje ograniczenia, zobaczysz, że zostało dodane inne ograniczenie ze względu na ręczną pracę awaryjną:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Usuń ograniczenie o IDENTYFIKATORze `cli-prefer-ag_cluster-master` przy użyciu następującego polecenia:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Sprawdź zasoby klastra przy użyciu polecenia `sudo pcs resource` , aby zobaczyć, że wystąpienie podstawowe jest teraz `<VM2>` .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Ogrodzenie testowe

Możesz testować STONITH, uruchamiając następujące polecenie. Spróbuj uruchomić poniższe polecenie w programie `<VM1>` dla programu `<VM3>` .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Domyślnie akcja ogrodzenia powoduje, że węzeł jest wyłączony, a następnie włączony. Jeśli chcesz tylko przełączyć węzeł do trybu offline, użyj opcji `--off` w poleceniu.

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Więcej informacji na temat testowania urządzenia ogrodzenia można znaleźć w następującym artykule [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) .

## <a name="next-steps"></a>Następne kroki

Aby można było korzystać z odbiornika grupy dostępności dla wystąpień SQL Server, należy utworzyć i skonfigurować moduł równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie odbiornika grupy dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure](rhel-high-availability-listener-tutorial.md)
