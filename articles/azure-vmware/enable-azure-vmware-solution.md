---
title: Jak włączyć zasób rozwiązania Azure VMware
description: Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób rozwiązania VMware platformy Azure. Możesz również zażądać większej liczby węzłów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: c9c31d8ffbf62a511f4f2ab19f995b6bc0ee0ca4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695150"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak włączyć zasób rozwiązania VMware dla platformy Azure
Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób [rozwiązania VMware platformy Azure](introduction.md) . Możesz również zażądać większej liczby węzłów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.

## <a name="eligibility-criteria"></a>Kryteria kwalifikacji

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure. Subskrypcja platformy Azure musi być zgodna z jednym z następujących kryteriów:

* Subskrypcja w ramach [usługi Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.
* Subskrypcja zarządzana przez dostawcę rozwiązań w chmurze (CSP) w ramach planu platformy Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Włącz rozwiązanie VMware dla platformy Azure dla klientów z umowami EA
Przed utworzeniem zasobu rozwiązania Azure VMware należy przesłać bilet pomocy technicznej w celu przydzielenia węzłów. Gdy zespół pomocy technicznej otrzyma Twoje żądanie, zajmie do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić węzły. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i chcesz uzyskać więcej węzłów, przejdziesz do tego samego procesu.


1. W Azure Portal w obszarze **Pomoc i obsługa techniczna** Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
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

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Włącz rozwiązanie VMware dla platformy Azure dla klientów programu CSP 

Dostawcy usług kryptograficznych muszą korzystać z [Centrum partnerskiego firmy Microsoft](https://partner.microsoft.com) , aby umożliwić klientom korzystanie z rozwiązań VMware platformy Azure. 

   >[!IMPORTANT] 
   >Usługa Azure VMware Solution Service nie udostępnia środowiska wielodostępnego i dlatego partnerzy hostingu nie są jeszcze obsługiwani. 

1. W **centrum partnerskim** wybierz **dostawcę CSP** , aby uzyskać dostęp do obszaru **Customers** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Obszar klientów Centrum partnerskiego firmy Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Wybierz klienta, a następnie wybierz pozycję **Dodaj produkty**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centrum Partnerskie Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Wybierz pozycję **Azure plan** , a następnie wybierz pozycję **Dodaj do koszyka**. 

1. Przejrzyj i Zakończ ogólne ustawienia subskrypcji planu platformy Azure dla klienta. Aby uzyskać więcej informacji, zobacz [dokumentację Centrum partnerskiego firmy Microsoft](https://docs.microsoft.com/partner-center/azure-plan-manage).

Po skonfigurowaniu planu platformy Azure oraz wymaganych uprawnień RBAC w ramach dostawcy usług kryptograficznych nastąpi umożliwienie firmie Microsoft włączenia limitu przydziału dla subskrypcji planu platformy Azure. Dostęp do Azure Portal z Centrum partnerskiego przy użyciu procedury **administratora w imieniu** (AOBO).

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com).

1. Wybierz **dostawcę CSP** , aby uzyskać dostęp do obszaru **Customers** .

1. Rozwiń węzeł szczegóły klienta i wybierz pozycję **Portal zarządzania Microsoft Azure**.

1. W Azure Portal, w obszarze **Pomoc i obsługa techniczna**, Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
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
   - Czy jest przeznaczony do hostowania wielu klientów?

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy węzły do roztworzenia chmury prywatnej oraz węzłów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i żądasz dodatkowych węzłów, pamiętaj, że potrzebujemy pięciu dni roboczych na przydzielenie węzłów. 

1. Po dodaniu do planu platformy Azure i włączeniu limitu przydziału klient lub administrator partnera może wdrożyć chmurę prywatną rozwiązania VMware platformy Azure za pomocą Azure Portal. Przed udostępnieniem węzłów upewnij się, że rejestrujesz dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Następne kroki

Po włączeniu zasobu rozwiązania Azure VMware i umieszczeniu odpowiedniej sieci na miejscu można [utworzyć chmurę prywatną](tutorial-create-private-cloud.md).