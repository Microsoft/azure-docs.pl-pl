---
title: Wyświetlanie i modyfikowanie nazwy hostów | Dokumenty firmy Microsoft
description: Jak wyświetlać i zmieniać nazwy hostów dla maszyn wirtualnych platformy Azure, ról sieci Web i procesów roboczych rozpoznawania nazw
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 09c911838d34a083de8450145ce849fc966eb0e8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459155"
---
# <a name="viewing-and-modifying-hostnames"></a>Wyświetlanie i modyfikowanie nazwy hostów
Aby zezwolić na odwoływanie się do wystąpień roli za pomocą nazwy hosta, należy ustawić wartość nazwy hosta w pliku konfiguracji usługi dla każdej roli. Można to zrobić, dodając żądaną nazwę hosta do atrybutu **vmName** elementu **Role.** Wartość atrybutu **vmName** jest używana jako podstawa dla nazwy hosta każdego wystąpienia roli. Na przykład, jeśli **vmName** jest *webrole* i istnieją trzy wystąpienia tej roli, nazwy hostów wystąpień będzie *webrole0*, *webrole1*i *webrole2*. Nie trzeba określać nazwy hosta dla maszyn wirtualnych w pliku konfiguracji, ponieważ nazwa hosta maszyny wirtualnej jest wypełniana na podstawie nazwy maszyny wirtualnej. Aby uzyskać więcej informacji na temat konfigurowania usługi Microsoft Azure, zobacz [Schemat konfiguracji usługi Azure (plik cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Wyświetlanie nazwy hostów
Nazwy hostów maszyn wirtualnych i wystąpień ról w usłudze w chmurze można wyświetlić przy użyciu dowolnego z poniższych narzędzi.

### <a name="service-configuration-file"></a>Plik konfiguracji usługi
Można pobrać plik konfiguracji usługi dla wdrożonej usługi z **bloku Konfigurowanie** usługi w witrynie Azure portal. Następnie można wyszukać atrybut **vmName** dla elementu **nazwa roli,** aby wyświetlić nazwę hosta. Należy pamiętać, że ta nazwa hosta jest używana jako podstawa dla nazwy hosta każdego wystąpienia roli. Na przykład, jeśli **vmName** jest *webrole* i istnieją trzy wystąpienia tej roli, nazwy hostów wystąpień będzie *webrole0*, *webrole1*i *webrole2*.

### <a name="remote-desktop"></a>Pulpit zdalny
Po włączeniu połączeń pulpitu zdalnego (Windows), windows PowerShell (Windows) lub SSH (Linux i Windows) z maszynami wirtualnymi lub wystąpieniami roli można wyświetlić nazwę hosta z aktywnego połączenia pulpitu zdalnego na różne sposoby:

* Wpisz hostname w wierszu polecenia lub terminalu SSH.
* Wpisz ipconfig /all w wierszu polecenia (tylko windows).
* Wyświetlanie nazwy komputera w ustawieniach systemowych (tylko w systemie Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
Od klienta REST postępuj zgodnie z poniższymi instrukcjami:

1. Upewnij się, że masz certyfikat klienta do łączenia się z witryną Azure portal. Aby uzyskać certyfikat klienta, wykonaj kroki opisane w [sekcji Jak: Pobierz i zaimportuj ustawienia publikowania oraz Informacje o subskrypcji](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Ustaw wpis nagłówka o nazwie x-ms-version o wartości 2013-11-01.
3. Wyślij żądanie w następującym formacie:\/https:\</management.core.windows.net/ subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Poszukaj **elementu Nazwa hosta** dla każdego elementu **RoleInstance.**

> [!WARNING]
> Wewnętrzny sufiks domeny usługi w chmurze można również wyświetlić z odpowiedzi na wywołania REST, sprawdzając element **InternalDnsSuffix** lub uruchamiając ipconfig /all z wiersza polecenia w sesji pulpitu zdalnego (Windows) lub uruchamiając cat /etc/resolv.conf z terminala SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modyfikowanie nazwy hosta
Nazwę hosta dla dowolnej maszyny wirtualnej lub wystąpienia roli można zmodyfikować, przekazując zmodyfikowany plik konfiguracji usługi lub zmieniając nazwę komputera z sesji pulpitu zdalnego.

## <a name="next-steps"></a>Następne kroki
[Rozpoznawanie nazw (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schemat konfiguracji usługi Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schemat konfiguracji sieci wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Określanie ustawień DNS przy użyciu plików konfiguracji sieci](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

