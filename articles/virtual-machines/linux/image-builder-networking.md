---
title: Opcje sieci usługi Azure Image Builder
description: Informacje o opcjach sieciowych podczas wdrażania usługi Azure VM Image Builder
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669447"
---
# <a name="azure-image-builder-service-networking-options"></a>Opcje sieci usługi Azure Image Builder

Musisz wybrać wdrożenie narzędzia Azure Image Builder z istniejącą siecią wirtualną lub bez niej.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Wdróż bez określania istniejącej sieci wirtualnej

Jeśli nie określisz istniejącej sieci wirtualnej, Konstruktor obrazów platformy Azure utworzy sieć wirtualną i podsieci w tymczasowej grupie zasobów. Zasób publicznego adresu IP jest używany z grupą zabezpieczeń sieci do ograniczania ruchu przychodzącego do usługi Azure Image Builder. Publiczny adres IP ułatwia kanał poleceń usługi Azure Image Builder podczas kompilowania obrazu. Po zakończeniu kompilacji zostaną usunięte maszyny wirtualne, publiczny adres IP, dyski i Sieć wirtualna. Aby użyć tej opcji, nie należy określać żadnych właściwości sieci wirtualnej.

## <a name="deploy-using-an-existing-vnet"></a>Wdrażanie przy użyciu istniejącej sieci wirtualnej

W przypadku określenia sieci wirtualnej i podsieci usługa Azure Image Builder wdraża maszynę wirtualną kompilacji do wybranej sieci wirtualnej. Możesz uzyskać dostęp do zasobów, które są dostępne w sieci wirtualnej. Można również utworzyć silosową sieć wirtualną, która nie jest połączona z żadną inną siecią wirtualną. W przypadku określenia sieci wirtualnej usługa Azure Image Builder nie korzysta z publicznego adresu IP. Komunikacja z usługi Azure Image Builder na maszynę wirtualną kompilacji używa technologii Azure Private link.

Aby uzyskać więcej informacji, zobacz jeden z następujących przykładów:

* [Korzystanie z usługi Azure Image Builder dla maszyn wirtualnych z systemem Windows, które umożliwiają dostęp do istniejącej sieci wirtualnej platformy Azure](../windows/image-builder-vnet.md)
* [Korzystanie z usługi Azure Image Builder dla maszyn wirtualnych z systemem Linux, które umożliwiają dostęp do istniejącej sieci wirtualnej platformy Azure](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Co to jest łącze prywatne platformy Azure?

Link prywatny platformy Azure zapewnia prywatne połączenie z sieci wirtualnej z usługą Azure Platform as a Service (PaaS), własnością klienta lub usługami partnerskimi firmy Microsoft. Upraszcza to architekturę sieci i zabezpiecza połączenie między punktami końcowymi na platformie Azure, eliminując narażenie danych na publiczny Internet. Aby uzyskać więcej informacji, zobacz [dokumentację linku prywatnego](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Wymagane uprawnienia dla istniejącej sieci wirtualnej

Konstruktor obrazów platformy Azure wymaga określonych uprawnień do korzystania z istniejącej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure](image-builder-permissions-cli.md) lub [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Co jest wdrażane podczas kompilowania obrazu?

Korzystanie z istniejącej sieci wirtualnej oznacza, że usługa Azure Image Builder wdraża dodatkową maszynę wirtualną (VM) i Azure Load Balancer (ALB), która jest połączona z linkiem prywatnym. Ruch z usługi AIB przechodzi przez prywatny link do ALB. ALB komunikuje się z maszyną wirtualną proxy przy użyciu portu 60001 dla systemu operacyjnego Linux lub 60000 dla systemu operacyjnego Windows. Serwer proxy przekazuje polecenia do maszyny wirtualnej kompilacji przy użyciu portu 22 dla systemu operacyjnego Linux lub 5986 dla systemu operacyjnego Windows.

> [!NOTE]
> Sieć wirtualna musi znajdować się w tym samym regionie co region usługi Azure Image Builder.
> 

### <a name="why-deploy-a-proxy-vm"></a>Dlaczego warto wdrożyć maszynę wirtualną serwera proxy?

Jeśli maszyna wirtualna bez publicznego adresu IP znajduje się za wewnętrzną Load Balancer, nie ma dostępu do Internetu. Azure Load Balancer używany na potrzeby sieci wirtualnej jest wewnętrzny. Maszyna wirtualna serwera proxy umożliwia dostęp do Internetu dla maszyny wirtualnej kompilacji podczas kompilacji. W celu ograniczenia dostępu do maszyny wirtualnej można użyć skojarzonych grup zabezpieczeń sieci.

Rozmiar wdrożonej maszyny wirtualnej serwera proxy to standardowy A1_v2 oprócz maszyny wirtualnej kompilacji. Maszyna wirtualna serwera proxy służy do wysyłania poleceń między usługą Azure Image Builder a maszyną wirtualną kompilacji. Nie można zmienić właściwości maszyny wirtualnej serwera proxy, w tym rozmiaru lub systemu operacyjnego. Nie można zmienić właściwości maszyny wirtualnej serwera proxy dla kompilacji obrazów systemów Windows i Linux.

### <a name="image-template-parameters-to-support-vnet"></a>Parametry szablonu obrazu do obsługi sieci wirtualnej
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Ustawienie | Opis |
|---------|---------|
| name | Obowiązkowe Nazwa istniejącej sieci wirtualnej. |
| subnetName | Nazwa podsieci w określonej sieci wirtualnej. Musi być określony, jeśli i tylko wtedy, gdy *Nazwa* jest określona. |
| resourceGroupName | Nazwa grupy zasobów zawierającej określoną sieć wirtualną. Musi być określony, jeśli i tylko wtedy, gdy *Nazwa* jest określona. |

Usługa link prywatny wymaga adresu IP z danej sieci wirtualnej i podsieci. Obecnie platforma Azure nie obsługuje zasad sieciowych dla tych adresów IP. W związku z tym należy wyłączyć zasady sieciowe w podsieci. Aby uzyskać więcej informacji, zobacz [dokumentację linku prywatnego](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>Lista kontrolna dotycząca korzystania z sieci wirtualnej

1. Zezwalaj Azure Load Balancer (ALB) na komunikację z maszyną wirtualną serwera proxy w sieciowej grupy zabezpieczeń
    * [Przykład AZ CLI](image-builder-vnet.md#add-network-security-group-rule)
    * [Przykład programu PowerShell](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Wyłącz zasady usługi prywatnej w podsieci
    * [Przykład AZ CLI](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [Przykład programu PowerShell](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Zezwalaj programowi Azure Image Builder na tworzenie ALB i Dodawanie maszyn wirtualnych do sieci wirtualnej
    * [Przykład AZ CLI](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [Przykład programu PowerShell](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Zezwalaj programowi Azure Image Builder na odczyt/zapis obrazów źródłowych i tworzenie obrazów
    * [Przykład AZ CLI](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [Przykład programu PowerShell](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Upewnij się, że używasz sieci wirtualnej w tym samym regionie co region usługi Azure Image Builder.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Image Builder](../image-builder-overview.md).