---
title: Wpływ na wydajność protokołu Kerberos na Azure NetApp Files NFSv 4.1 Microsoft Docs
description: Opisuje dostępne opcje zabezpieczeń, przetestowane wektory wydajności i oczekiwany wpływ na wydajność protokołu Kerberos na Azure NetApp Files woluminów NFSv 4.1.
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
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747210"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Wpływ na wydajność protokołu Kerberos na Azure NetApp Files woluminów NFSv 4.1

Azure NetApp Files obsługuje szyfrowanie [klienta NFS w trybach protokołu Kerberos](configure-kerberos-encryption.md) (krb5, krb5i i Krb5p) przy użyciu szyfrowania AES-256. W tym artykule opisano wpływ protokołu Kerberos na woluminy NFSv 4.1. 

## <a name="available-security-options"></a>Dostępne opcje zabezpieczeń 

Dostępne są następujące opcje zabezpieczeń dla woluminów NFSv 4.1: 

* **s = sys** używa lokalnych identyfikatorów systemu UNIX i GIDs przy użyciu AUTH_SYS do uwierzytelniania operacji NFS.
* **sek = krb5** korzysta z protokołu Kerberos V5 zamiast lokalnych identyfikatorów systemu UNIX i GIDs do uwierzytelniania użytkowników.
* **s = krb5i** korzysta z protokołu Kerberos V5 do uwierzytelniania użytkowników i przeprowadza sprawdzanie integralności operacji NFS przy użyciu zabezpieczonych sum kontrolnych, aby zapobiec manipulowaniu danymi.
* **s = Krb5p** używa protokołu Kerberos V5 do uwierzytelniania użytkowników i sprawdzania integralności. Szyfruje ruch NFS, aby zapobiec wykrywaniem ruchu. Ta opcja jest najbezpieczniejszym ustawieniem, ale również obejmuje najwięcej kosztów.

## <a name="performance-vectors-tested"></a>Przetestowane wektory wydajności

W tej sekcji opisano pojedynczy wpływ na wydajność po stronie klienta różnych `sec=*` opcji.

* Przetestowano wpływ na wydajność na dwóch poziomach: niska współbieżność (niskie obciążenie) i wysoka współbieżność (górne limity operacji we/wy i przepływności).  
* Przetestowano trzy typy obciążeń:  
    * Niewielka Losowa operacja odczytu/zapisu (przy użyciu FIO)
    * Operacja odczytu/zapisu dużej operacji sekwencyjnej (przy użyciu FIO)
    * Duże obciążenie metadanych w postaci wygenerowanej przez aplikacje, takie jak Git

## <a name="expected-performance-impact"></a>Oczekiwany wpływ na wydajność 

Istnieją dwa obszary fokusu: lekkie obciążenie i górny limit. Poniższa lista zawiera opis ustawienia zabezpieczeń wpływ na wydajność według ustawień zabezpieczeń i scenariusza według scenariusza. Wszystkie porównania są wykonywane względem `sec=sys` parametru Security. Test został wykonany na pojedynczym woluminie przy użyciu jednego klienta. 

Wpływ na wydajność krb5:

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,3 ms.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Krb5 nie ma wpływu na maksymalną przepustowość sekwencyjną.
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 30% w przypadku czystych operacji odczytu, które łączą cały wpływ na zero, gdy obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

Wpływ na wydajność krb5i: 

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,5 ms.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Maksymalna przepływność sekwencyjna spadła o 70% całości niezależnie od mieszaniny obciążenia.
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 50% w przypadku czysto czytanych obciążeń o całkowitym wpływie, które zmniejszają się do 25%, gdy obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

Wpływ na wydajność Krb5p:

* Niska współbieżność (r/w):
    * Opóźnienie sekwencyjne zwiększyło 0,8 MS.
    * Opóźnienie losowych operacji we/wy zwiększyło 0,2 ms.
    * Opóźnienie operacji we/wy metadanych wzrosło 0,2 ms.
* Wysoka współbieżność (r/w): 
    * Maksymalna przepływność sekwencyjna spadła o 85% całości niezależnie od mieszaniny obciążenia. 
    * Maksymalna liczba losowych operacji we/wy zmniejszyła się o 65% w przypadku czysto czytanych obciążeń o całkowitym wpływie, które zmniejszają 43%, ponieważ obciążenie przesunie się na czysty zapis. 
    * Maksymalna liczba obciążeń metadanych zmniejszyła się o 30%.

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie szyfrowania Kerberos w systemie plików NFS 4.1 dla usługi Azure NetApp Files](configure-kerberos-encryption.md) 
