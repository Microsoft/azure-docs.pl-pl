---
title: Tworzenie obrazów systemu Linux bez agenta aprowizacji
description: Utwórz uogólnione obrazy systemu Linux bez agenta aprowizacji na platformie Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c7ca147f0a5b907ee0c5c66d53a219fe75ab2179
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551712"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Tworzenie uogólnionych obrazów bez agenta aprowizacji

Microsoft Azure udostępnia agentów aprowizacji dla maszyn wirtualnych z systemem Linux w postaci [walinuxagent](https://github.com/Azure/WALinuxAgent) lub [Cloud-init](https://github.com/canonical/cloud-init) (zalecane). Może jednak wystąpić scenariusz, gdy nie chcesz używać żadnej z tych aplikacji dla agenta aprowizacji, na przykład:

- Dystrybucji systemu Linux/wersja nie obsługuje agenta Cloud-init/Linux.
- Wymagane są określone właściwości maszyny wirtualnej, takie jak nazwa hosta.

> [!NOTE] 
>
> Jeśli nie są wymagane żadne właściwości lub nie zainicjowano obsługi administracyjnej, należy rozważyć utworzenie wyspecjalizowanego obrazu.

W tym artykule pokazano, jak skonfigurować obraz maszyny wirtualnej w celu spełnienia wymagań platformy Azure i ustawić nazwę hosta bez instalowania agenta aprowizacji.

## <a name="networking-and-reporting-ready"></a>Gotowe do sieci i raportowania

Aby maszyna wirtualna z systemem Linux komunikuje się ze składnikami platformy Azure, musisz mieć klienta DHCP, aby pobrać adres IP hosta z sieci wirtualnej, a także rozpoznawanie nazw DNS i trasy. Większość dystrybucje dostarcza z tymi narzędziami. Narzędzia, które zostały przetestowane na platformie Azure przez dostawców dystrybucji systemu Linux `dhclient` , obejmują, `network-manager` `systemd-networkd` i inne.

> [!NOTE]
>
> Obecnie tworzenie uogólnionych obrazów bez agenta aprowizacji obsługuje tylko maszyny wirtualne z obsługą protokołu DHCP.

Po zainstalowaniu i skonfigurowaniu sieci należy "Raport gotowy". Poinformuje to platformę Azure, że maszyna wirtualna została pomyślnie zainicjowana.

> [!IMPORTANT]
>
> Niepowodzenie raportowania gotowego do platformy Azure spowoduje ponowne uruchomienie maszyny wirtualnej.

## <a name="demosample"></a>Demonstracja/przykład

Ten pokaz pokazuje, jak można zastosować istniejący obraz portalu Marketplace (w tym przypadku maszynę wirtualną Debian Buster) i usunąć agenta systemu Linux (walinuxagent), ale również utworzyć najbardziej podstawowy proces do raportowania na platformie Azure, że maszyna wirtualna jest gotowa.

### <a name="create-the-resource-group-and-base-vm"></a>Utwórz grupę zasobów i podstawową maszynę wirtualną:

```bash
$ az group create --location eastus --name demo1
```

Utwórz podstawową maszynę wirtualną:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Usuń agenta aprowizacji obrazu

Po aprowizacji maszyny wirtualnej można do niej wykonać protokół SSH i usunąć agenta systemu Linux:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Dodawanie wymaganego kodu do maszyny wirtualnej

Poza maszyną wirtualną, ponieważ został usunięty Agent systemu Azure Linux, musimy udostępnić mechanizm zgłaszania gotowości. 

#### <a name="python-script"></a>Skrypt języka Python

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Kroki ogólne (bez używania języka Python)

Jeśli na maszynie wirtualnej nie zainstalowano ani nie jest dostępny język Python, można programowo odtworzyć tę logikę skryptu, wykonując następujące czynności:

1. Pobierz `ContainerId` i `InstanceId` przez analizowanie odpowiedzi z WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Utwórz następujące dane XML, wstrzyknąć przeanalizowane `ContainerId` i `InstanceId` z powyższego kroku:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Opublikuj te dane w WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatyzacja uruchamiania kodu przy pierwszym rozruchu

W tej wersji demonstracyjnej jest używany system, który jest najbardziej typowym systemem init w nowoczesnych dystrybucje Linux. Dlatego najłatwiejszym i najbardziej natywnym sposobem upewnienia się, że ten mechanizm gotowości raportu jest uruchamiany w odpowiednim czasie, to utworzenie systemowej jednostki usługi. Można dodać następujący plik jednostki do (w `/etc/systemd/system` tym przykładzie nazwa pliku jednostki `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Ta usługa systemowa ma trzy rzeczy na potrzeby podstawowej aprowizacji:

1. Raporty gotowe do platformy Azure (w celu wskazania, że zostały pomyślnie dołączone).
1. Zmienia nazwę maszyny wirtualnej na podstawie nazwy maszyny wirtualnej dostarczonej przez użytkownika, pobierając te dane z [usługi Azure instance Metadata Service (IMDS)](./instance-metadata-service.md). **Uwaga** IMDS udostępnia również inne [metadane wystąpienia](./instance-metadata-service.md#access-azure-instance-metadata-service), takie jak publiczne klucze SSH, aby można było ustawić więcej niż nazwę hosta.
1. Wyłącza, tak aby była uruchamiana tylko przy pierwszym rozruchu, a nie w kolejnych ponownych uruchomieniach.

W przypadku jednostki w systemie plików uruchom następujące polecenie, aby je włączyć:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Teraz maszyna wirtualna jest gotowa do uogólnionia i ma utworzony obraz. 

#### <a name="completing-the-preparation-of-the-image"></a>Ukończenie przygotowywania obrazu

Na komputerze deweloperskim Uruchom następujące polecenie, aby przygotować się do utworzenia obrazu z podstawowej maszyny wirtualnej:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

I Utwórz obraz z tej maszyny wirtualnej:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Teraz wszystko jest gotowe do utworzenia nowej maszyny wirtualnej (lub wielu maszyn wirtualnych) z obrazu:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Ważne jest, aby ustawić `--enable-agent` , `false` ponieważ walinuxagent nie istnieje na tej maszynie wirtualnej, która ma zostać utworzona na podstawie obrazu.

Ta maszyna wirtualna powinna pomyślnie zainicjowania obsługi. Po zalogowaniu się do nowo zainicjowanej maszyny wirtualnej powinny być widoczne dane wyjściowe przygotowanej usługi systemowej:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Pomoc techniczna

Jeśli zaimplementowasz własny kod lub agenta aprowizacji, będziesz mieć możliwość obsługi tego kodu, a pomoc techniczna firmy Microsoft sprawdzi tylko problemy związane z interfejsami aprowizacji, które nie są dostępne. Ciągle wprowadzamy ulepszenia i zmiany w tym obszarze, więc musisz monitorować zmiany w programie Cloud-init i agencie systemu Azure Linux na potrzeby inicjowania obsługi administracyjnej zmian interfejsu API.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi systemu Linux](provisioning.md).
