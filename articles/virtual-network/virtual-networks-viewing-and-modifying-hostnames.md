---
title: Przeglądanie i modyfikowanie nazw hostów | Microsoft Docs
description: Jak wyświetlać i zmieniać nazwy hostów dla maszyn wirtualnych platformy Azure, ról sieci Web i procesu roboczego na potrzeby rozpoznawania nazw
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222789"
---
# <a name="viewing-and-modifying-hostnames"></a>Przeglądanie i modyfikowanie nazw hostów
Aby zezwolić wystąpieniu roli na odwoływanie się według nazwy hosta, należy ustawić wartość nazwy hosta w pliku konfiguracji usługi dla każdej roli. Można to zrobić, dodając żądaną nazwę hosta do atrybutu **vmName** elementu **role** . Wartość atrybutu **vmName** jest używana jako podstawa dla nazwy hosta każdego wystąpienia roli. Na przykład jeśli **vmName** jest *rolą webrole* i istnieją trzy wystąpienia tej roli, nazwy hostów wystąpień będą *webrole0*, *webrole1* i *webrole2*. Nie trzeba określać nazwy hosta dla maszyn wirtualnych w pliku konfiguracji, ponieważ nazwa hosta maszyny wirtualnej jest wypełniana na podstawie nazwy maszyny wirtualnej. Aby uzyskać więcej informacji na temat konfigurowania usługi Microsoft Azure, zobacz [Schemat konfiguracji usługi platformy Azure (plik cscfg).](/previous-versions/azure/reference/ee758710(v=azure.100))

## <a name="viewing-hostnames"></a>Wyświetlanie nazw hostów
Nazwy hostów maszyn wirtualnych i wystąpień ról można wyświetlić w usłudze w chmurze przy użyciu dowolnego z poniższych narzędzi.

### <a name="service-configuration-file"></a>Plik konfiguracji usługi
Plik konfiguracji usługi dla wdrożonej usługi można pobrać z bloku **Konfiguracja** usługi w Azure Portal. Następnie można wyszukać atrybut **vmName** dla elementu **Nazwa roli** , aby wyświetlić nazwę hosta. Należy pamiętać, że ta nazwa hosta jest używana jako podstawa dla nazwy hosta każdego wystąpienia roli. Na przykład jeśli **vmName** jest *rolą webrole* i istnieją trzy wystąpienia tej roli, nazwy hostów wystąpień będą *webrole0*, *webrole1* i *webrole2*.

### <a name="remote-desktop"></a>Pulpit zdalny
Po włączeniu połączeń Pulpit zdalny (Windows), komunikacji zdalnej programu Windows PowerShell (Windows) lub protokołu SSH (Linux i Windows) z maszynami wirtualnymi lub wystąpieniami ról można wyświetlić nazwę hosta z aktywnego połączenia Pulpit zdalny na różne sposoby:

* Wpisz nazwę hosta w wierszu polecenia lub terminalu SSH.
* Wpisz polecenie ipconfig/all w wierszu polecenia (tylko system Windows).
* Wyświetl nazwę komputera w ustawieniach systemowych (tylko system Windows).

### <a name="azure-service-management-rest-api"></a>Interfejs API REST zarządzania usługami platformy Azure
W przypadku klienta REST wykonaj następujące instrukcje:

1. Upewnij się, że masz certyfikat klienta, aby nawiązać połączenie z Azure Portal. Aby uzyskać certyfikat klienta, wykonaj kroki przedstawione w sekcji [jak pobrać i zaimportować ustawienia publikowania oraz informacje o subskrypcji](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70)). 
2. Ustaw wpis nagłówka o nazwie x-MS-Version o wartości 2013-11-01.
3. Wyślij żądanie w następującym formacie: https: \/ /Management.Core.Windows.NET/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? embed-detail = true
4. Wyszukaj element **hostname** dla każdego elementu **RoleInstance** .

> [!WARNING]
> Możesz również wyświetlić sufiks domeny wewnętrznej dla usługi w chmurze z odpowiedzi na wywołanie REST, sprawdzając element **InternalDnsSuffix** lub uruchamiając polecenie ipconfig/all w wierszu polecenia w sesji pulpit zdalny (Windows) lub przez uruchomienie Cat/etc/resolv.conf z terminalu SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modyfikowanie nazwy hosta
Możesz zmodyfikować nazwę hosta dla dowolnej maszyny wirtualnej lub wystąpienia roli, przekazując zmodyfikowany plik konfiguracji usługi lub zmieniając nazwę komputera z sesji Pulpit zdalny.

## <a name="next-steps"></a>Następne kroki
[Rozpoznawanie nazw (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schemat konfiguracji usługi platformy Azure (cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Schemat konfiguracji usługi Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))

[Określanie ustawień DNS przy użyciu plików konfiguracji sieci](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)