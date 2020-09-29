---
title: Delegowanie podsieci do usługi Azure NetApp Files | Microsoft Docs
description: Dowiedz się, jak delegować podsieć do Azure NetApp Files. Określ delegowaną podsieć podczas tworzenia woluminu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bed1375631c017d23ed53b6102c424533237099e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447568"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegowanie podsieci do usługi Azure NetApp Files 

Podsieć należy delegować do usługi Azure NetApp Files.   Podczas tworzenia woluminu należy określić delegowaną podsieć.

## <a name="considerations"></a>Zagadnienia do rozważenia

* Kreator tworzenia nowej podsieci przyjmuje wartość domyślną maski sieci /24, która zapewnia 251 dostępnych adresów IP. Korzystanie z maski sieci/28, która zapewnia 11 przydatnych adresów IP, jest wystarczające dla usługi.
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.   
   System Azure umożliwia tworzenie wielu delegowanych podsieci w sieci wirtualnej.  Jednak wszelkie próby utworzenia nowego woluminu zakończą się niepowodzeniem, jeśli zostanie użyta więcej niż jedna delegowana podsieć.  
   W sieci wirtualnej można mieć tylko jedną delegowaną podsieć. Konto NetApp może wdrażać woluminy w wielu sieci wirtualnych, z których każda ma własną podsieć delegowaną.  
* W delegowanej podsieci nie można wyznaczyć sieciowej grupy zabezpieczeń ani punktu końcowego usługi. Jeśli się to zrobi, delegowanie podsieci nie powiedzie się.
* Dostęp do woluminu z globalnej, równorzędnej sieci wirtualnej nie jest obecnie obsługiwany.
* [Trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) (UDR) i sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie są obsługiwane w delegowanych podsieciach dla Azure NetApp Files. Można jednak zastosować UDR i sieciowych grup zabezpieczeń do innych podsieci, nawet w ramach tej samej sieci wirtualnej co podsieć delegowana do Azure NetApp Files.  
   Azure NetApp Files tworzy trasę systemową do delegowanej podsieci. Trasa jest pokazywana w **efektywnych trasach** w tabeli tras, jeśli będzie potrzebna do rozwiązywania problemów.

## <a name="steps"></a>Kroki

1.  Przejdź do bloku **sieci wirtualne** w Azure Portal i wybierz sieć wirtualną, której chcesz użyć do Azure NetApp Files.    

1. Wybierz pozycję **Podsieci** w bloku Sieć wirtualna, a następnie kliknij przycisk **+ Podsieć**. 

1. Utwórz nową podsieć na potrzeby usługi Azure NetApp Files, wypełniając następujące wymagane pola na stronie Dodawanie podsieci:
    * **Nazwa**: Określ nazwę podsieci.
    * **Zakres adresów**: Określ zakres adresów IP.
    * **Delegowanie podsieci**: wybierz pozycję **Microsoft. NetApp/Volumes**. 

      ![Delegowanie podsieci](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Podsieć można również utworzyć i delegować podczas [tworzenia woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


