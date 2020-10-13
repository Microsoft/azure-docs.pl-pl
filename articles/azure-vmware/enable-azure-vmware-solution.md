---
title: Jak włączyć zasób rozwiązania Azure VMware
description: Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób rozwiązania VMware platformy Azure. Możesz również zażądać większej liczby węzłów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: dc90a322b5592ca7f400a82deca65ea753711c27
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948735"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak włączyć zasób rozwiązania VMware dla platformy Azure
Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób rozwiązania VMware platformy Azure. Możesz również zażądać większej liczby węzłów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.

## <a name="eligibility-criteria"></a>Kryteria kwalifikacji

* Będziesz potrzebować [platformy Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.
* Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure.


## <a name="enable-azure-vmware-solution-resource"></a>Włącz zasób rozwiązania VMware dla platformy Azure
Przed utworzeniem zasobu rozwiązania Azure VMware należy przesłać bilet pomocy technicznej w celu przydzielenia węzłów. Gdy zespół pomocy technicznej otrzyma Twoje żądanie, zajmie do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić węzły. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i chcesz uzyskać więcej węzłów, przejdziesz do tego samego procesu.


1. W Azure Portal w obszarze **Pomoc i obsługa techniczna**Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
   - **Typ problemu:** Naukow
   - **Subskrypcja:** Wybierz swoją subskrypcję
   - **Usługa:** Wszystkie usługi > rozwiązanie VMware dla platformy Azure
   - **Zasób:** Pytanie ogólne 
   - **Podsumowanie:** Potrzebna pojemność
   - **Typ problemu:** Problemy z zarządzaniem pojemnością
   - **Podtyp problemu:** Żądanie klienta dotyczące dodatkowego przydziału/pojemności hosta

1. W **opisie** biletu pomocy technicznej na karcie Szczegóły podaj następujące **informacje** :

   - ZK lub produkcja 
   - Nazwa regionu
   - Liczba węzłów
   - Wszelkie inne szczegóły

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy węzły do roztworzenia chmury prywatnej oraz węzłów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i żądasz dodatkowych węzłów, pamiętaj, że potrzebujemy pięciu dni roboczych na przydzielenie węzłów. 

1. Przed udostępnieniem węzłów upewnij się, że rejestrujesz dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).