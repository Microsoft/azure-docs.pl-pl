---
title: Znajdowanie i usuwanie niedołączonych kart sieciowych platformy Azure
description: Jak znaleźć i usunąć karty sieciowe platformy Azure, które nie są dołączone do maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016203"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Jak znaleźć i usunąć niedołączone karty interfejsu sieciowego (nic) dla maszyn wirtualnych platformy Azure
Po usunięciu maszyny wirtualnej (VM) na platformie Azure domyślnie karty interfejsu sieciowego (nic) nie są usuwane. Jeśli utworzysz i usuniesz wiele maszyn wirtualnych, nieużywane karty sieciowe nadal będą używać wewnętrznych dzierżaw adresów IP. Podczas tworzenia innych kart sieciowych maszyn wirtualnych mogą nie być w stanie uzyskać dzierżawy IP w przestrzeni adresowej podsieci. W tym artykule pokazano, jak znaleźć i usunąć niedołączone karty sieciowe.

## <a name="find-and-delete-unattached-nics"></a>Znajdowanie i usuwanie niedołączonych kart sieciowych

Właściwość *virtualMachine* karty sieciowej przechowuje identyfikator i grupę zasobów maszyny wirtualnej, do której jest dołączona karta sieciowa. Poniższy skrypt pętli przez wszystkie karty sieciowe w subskrypcji i sprawdza, czy właściwość *virtualMachine* ma wartość null. Jeśli ta właściwość ma wartość null, karta sieciowa nie jest podłączona do maszyny wirtualnej.

Aby wyświetlić wszystkie niedołączone karty sieciowe, zdecydowanie zalecamy, aby najpierw uruchomić skrypt ze zmienną *deleteUnattachedNics* na *0*. Aby usunąć wszystkie niedołączone karty sieciowe po przejrzeniu danych wyjściowych na liście, uruchom skrypt z *deleteUnattachedNics* do *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych na platformie Azure i zarządzania nimi, zobacz [Tworzenie sieci VMNetwork i zarządzanie nimi](tutorial-virtual-network.md).
