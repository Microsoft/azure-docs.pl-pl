---
title: Samouczek dotyczący kontroli dostępu & aplikacji — Azure Security Center
description: W tym samouczku przedstawiono sposób konfigurowania zasad dostępu just in Time do maszyny wirtualnej oraz zasad kontroli aplikacji.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 89d04588d07407be5dbd7ddbafd6543ed461462e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945723"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Samouczek: Ochrona zasobów przy użyciu usługi Azure Security Center
Usługa Security Center ogranicza narażenia na zagrożenia poprzez zastosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań. Dostęp do maszyn wirtualnych just-in-Time (JIT) zmniejsza narażenie na ataki przez umożliwienie trwałego dostępu do maszyn wirtualnych. Zamiast tego możesz zapewnić kontrolowany, monitorowany dostęp do maszyn wirtualnych tylko w razie potrzeby. Adaptacyjne kontrole aplikacji pomagają zabezpieczać maszyny wirtualne przed złośliwym oprogramowaniem poprzez kontrolowanie aplikacji, które mogą być uruchamiane na maszynach wirtualnych. Security Center wykorzystuje Uczenie maszynowe do analizowania procesów uruchomionych na maszynie wirtualnej i pomaga stosować reguły zezwalania na korzystanie z tej analizy.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej
> * Konfigurowanie zasad kontroli aplikacji

## <a name="prerequisites"></a>Wymagania wstępne
Aby przekroczyć funkcje omówione w tym samouczku, musisz mieć włączoną usługę Azure Defender. Możesz bezpłatnie wypróbować usługę Azure Defender. Aby uzyskać więcej informacji, zobacz [Wypróbuj usługę Azure Defender](security-center-pricing.md).

## <a name="manage-vm-access"></a>Zarządzanie dostępem do maszyny wirtualnej
Dostęp typu „ just in time” do maszyny wirtualnej może być używany do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure w celu zmniejszenia narażenia na ataki przy zapewnieniu łatwego dostępu do maszyn wirtualnych na potrzeby łączenia się z nimi w razie potrzeby.

Porty zarządzania nie muszą być otwarte przez cały czas. Muszą być otwarte tylko wtedy, gdy nawiązano połączenie z maszyną wirtualną, np. aby wykonać zadania związane z zarządzaniem lub konserwacją. Gdy jest włączona funkcja just-in-Time, Security Center używa reguł grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń), które ograniczają dostęp do portów zarządzania, przez co nie mogą być objęte przez osoby atakujące.

Postępuj zgodnie ze wskazówkami w temacie [Zabezpieczanie portów zarządzania przy użyciu dostępu just in Time](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Ochrona maszyn wirtualnych przed złośliwym oprogramowaniem
Funkcje adaptacyjnego sterowania aplikacjami ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach zasobów, co poza innymi korzyściami zabezpiecza maszyny wirtualne przed złośliwym oprogramowaniem. Security Center wykorzystuje Uczenie maszynowe do analizowania procesów uruchomionych na maszynie wirtualnej i pomaga stosować reguły zezwalania na korzystanie z tej analizy.

Postępuj zgodnie ze wskazówkami w temacie [Korzystanie z adaptacyjnych kontrolek aplikacji, aby zmniejszyć powierzchnie ataków na maszynę](security-center-adaptive-application.md).

## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono sposób ograniczania narażenia na zagrożenia poprzez:

> [!div class="checklist"]
> * Konfigurowanie zasad dostępu just in Time do maszyny wirtualnej w celu zapewnienia kontroli dostępu do maszyn wirtualnych i przeprowadzania na nich inspekcji tylko w razie potrzeby
> * konfigurowanie zasad adaptacyjnych kontroli aplikacji w celu kontrolowania aplikacji, które mogą być uruchamiane na maszynach wirtualnych.

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat reagowania na przypadki naruszenia zabezpieczeń.

> [!div class="nextstepaction"]
> [Samouczek: Reagowanie na zdarzenia związane z zabezpieczeniami](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
