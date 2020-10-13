---
title: Przestarzałe funkcje szyfrowania danych Azure Site Recovery | Microsoft Docs
description: Details Azure Site Recovery regarig — funkcja szyfrowania danych
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426320"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Przestarzałe funkcje szyfrowania danych Site Recovery

W tym dokumencie opisano szczegóły dotyczące wycofania i akcji korygowania, które należy wykonać w przypadku korzystania z funkcji szyfrowania danych Site Recovery podczas konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure. 

## <a name="deprecation-information"></a>Informacje o zaniechaniu


Funkcja szyfrowanie danych Site Recovery była dostępna dla klientów chroniących maszyny wirtualne funkcji Hyper-V w celu zapewnienia ochrony replikowanych danych przed zagrożeniami bezpieczeństwa. Ta funkcja zostanie zaniechana **30 kwietnia 2022**. Jest ona zastępowana przez bardziej zaawansowane [szyfrowanie w funkcji REST](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) , która używa [szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md) (SSE). W przypadku używania instrukcji SSE dane są szyfrowane przed utrwalaniem magazynu i odszyfrowywane podczas pobierania, a po przejściu do trybu failover na platformie Azure maszyny wirtualne będą uruchamiane z zaszyfrowanego konta magazynu, co pozwala na udoskonalony cel czasu odzyskiwania (RTO).

Zwróć uwagę na to, że jeśli jesteś istniejącym klientem korzystającym z tej funkcji, otrzymasz komunikat z informacjami o wycofaniu i procedurami naprawczymi. 


## <a name="what-are-the-implications"></a>Jakie są implikacje?

Po **30 kwietnia 2022**wszystkie maszyny wirtualne, które nadal korzystają z wycofywanej funkcji szyfrowania, nie będą mogły wykonywać trybu failover. 

## <a name="required-action"></a>Wymagana akcja
Aby kontynuować pomyślne operacje przełączania do trybu failover, wykonaj czynności opisane poniżej:

Wykonaj następujące kroki dla każdej maszyny wirtualnej: 
1.  [Wyłącz replikację](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Utwórz nowe zasady replikacji](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [Włącz replikację](./hyper-v-vmm-azure-tutorial.md#enable-replication) i wybierz konto magazynu z WŁĄCZONĄ funkcją SSE.

Po zakończeniu replikacji początkowej na konta magazynu z włączoną obsługą funkcji SSE maszyny wirtualne będą używać szyfrowania przechowywanego w Azure Site Recovery.


## <a name="next-steps"></a>Następne kroki
Zaplanuj wykonywanie kroków zaradczych i wykonaj je najwcześniej. Jeśli masz jakieś zapytania dotyczące tego wycofania, skontaktuj się z pomoc techniczna firmy Microsoft. Aby dowiedzieć się więcej na temat scenariusza funkcji Hyper-V do platformy Azure, zobacz [tutaj](hyper-v-vmm-architecture.md).
