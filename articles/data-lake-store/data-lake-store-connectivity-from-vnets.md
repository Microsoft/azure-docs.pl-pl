---
title: Nawiązywanie połączenia z usługą Azure Data Lake Storage Gen1 z sieci wirtualnych | Microsoft Docs
description: Nawiązywanie połączenia z usługą Azure Data Lake Storage Gen1 Azure sieci wirtualnych
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60878872"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Dostęp do Azure Data Lake Storage Gen1 z maszyn wirtualnych w sieci wirtualnej platformy Azure
Azure Data Lake Storage Gen1 to usługa PaaS, która jest uruchamiana na publicznych adresach IP. Wszystkie serwery, które mogą łączyć się z publiczną siecią Internet, zazwyczaj mogą łączyć się z Azure Data Lake Storage Gen1 punktami końcowymi. Domyślnie wszystkie maszyny wirtualne, które znajdują się w usłudze Azure sieci wirtualnych, mogą uzyskać dostęp do Internetu, dzięki czemu mogą uzyskać dostęp do Azure Data Lake Storage Gen1. Można jednak skonfigurować maszyny wirtualne w sieci wirtualnej tak, aby nie miały dostępu do Internetu. W przypadku takich maszyn wirtualnych dostęp do Azure Data Lake Storage Gen1 jest również ograniczony. Blokowanie publicznego dostępu do Internetu dla maszyn wirtualnych w usłudze Azure sieci wirtualnych można wykonać przy użyciu dowolnych z następujących metod:

* Konfigurując sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)
* Konfigurując trasy zdefiniowane przez użytkownika (UDR)
* Przez wymianę tras za pośrednictwem protokołu BGP (protokół dynamicznego routingu standardowego w branży), gdy jest używany ExpressRoute, blokuje dostęp do Internetu

W tym artykule dowiesz się, jak włączyć dostęp do Azure Data Lake Storage Gen1 z maszyn wirtualnych platformy Azure, które zostały ograniczone do uzyskiwania dostępu do zasobów przy użyciu jednej z trzech metod wymienionych wcześniej.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Włączanie łączności z maszynami wirtualnymi z ograniczoną łącznością Azure Data Lake Storage Gen1
Aby uzyskać dostęp do Azure Data Lake Storage Gen1 z takich maszyn wirtualnych, należy je skonfigurować w taki sposób, aby uzyskać dostęp do adresu IP dla regionu, w którym jest dostępne konto Azure Data Lake Storage Gen1. Adresy IP dla regionów kont Data Lake Storage Gen1 można zidentyfikować, rozwiązując nazwy DNS kont (`<account>.azuredatalakestore.net`). Aby rozpoznać nazwy DNS kont, można użyć narzędzi, takich jak **nslookup**. Otwórz wiersz polecenia na komputerze i uruchom następujące polecenie:

    nslookup mydatastore.azuredatalakestore.net

Dane wyjściowe są podobne do następujących. Wartość właściwości **adres** jest adresem IP skojarzonym z twoim kontem Data Lake Storage Gen1.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Włączanie łączności z maszyn wirtualnych z ograniczeniami przy użyciu sieciowej grupy zabezpieczeń
Gdy reguła sieciowej grupy zabezpieczeń jest używana do blokowania dostępu do Internetu, można utworzyć kolejną sieciowej grupy zabezpieczeń, która umożliwia dostęp do Data Lake Storage Gen1 adres IP. Więcej informacji o regułach sieciowej grupy zabezpieczeń można znaleźć w temacie [Network Security Groups Overview](../virtual-network/security-overview.md). Aby uzyskać instrukcje dotyczące sposobu tworzenia sieciowych grup zabezpieczeń, zobacz [How to Create a Network Security Group](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Włączanie łączności z maszyn wirtualnych z ograniczeniami przy użyciu UDR lub ExpressRoute
Gdy trasy UDR lub protokołu BGP są używane do blokowania dostępu do Internetu, należy skonfigurować specjalną trasę, tak aby maszyny wirtualne w tych podsieciach mogły uzyskiwać dostęp do Data Lake Storage Gen1 punktów końcowych. Aby uzyskać więcej informacji, zobacz [trasy zdefiniowane przez użytkownika — omówienie](../virtual-network/virtual-networks-udr-overview.md). Aby uzyskać instrukcje dotyczące tworzenia UDR, zobacz [Tworzenie UDR w Menedżer zasobów](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Włączanie łączności z maszyn wirtualnych z ograniczeniami przy użyciu ExpressRoute
W przypadku skonfigurowania obwodu ExpressRoute serwery lokalne mogą uzyskiwać dostęp do Data Lake Storage Gen1 za pomocą publicznej komunikacji równorzędnej. Więcej szczegółów na temat konfigurowania ExpressRoute na potrzeby publicznej komunikacji równorzędnej jest dostępnych na [ExpressRoute często zadawanych pytań](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Securing data stored in Azure Data Lake Storage Gen1 (Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1)](data-lake-store-security-overview.md)

