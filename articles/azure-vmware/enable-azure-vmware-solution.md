---
title: Jak włączyć zasób rozwiązania Azure VMware
description: Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób rozwiązania VMware platformy Azure. Możesz również zażądać większej liczby hostów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 526d6b38f4b4e3f6c4806b71b4728dee90cf558a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325081"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak włączyć zasób rozwiązania VMware dla platformy Azure
Dowiedz się, jak przesłać żądanie pomocy technicznej, aby włączyć zasób [rozwiązania VMware platformy Azure](introduction.md) . Możesz również zażądać większej liczby hostów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.

## <a name="eligibility-criteria"></a>Kryteria kwalifikacji

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure. Subskrypcja platformy Azure musi być zgodna z jednym z następujących kryteriów:

* Subskrypcja w ramach [usługi Azure Umowa Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.
* Subskrypcja zarządzana przez dostawcę rozwiązań w chmurze (CSP) w ramach planu platformy Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Włącz rozwiązanie VMware dla platformy Azure dla klientów z umowami EA
Przed utworzeniem zasobu rozwiązania Azure VMware należy przesłać bilet pomocy technicznej w celu przydzielenia hostów. Gdy zespół pomocy technicznej otrzyma Twoje żądanie, zajmie do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić hosty. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i potrzebujesz większej liczby hostów, przejdziesz do tego samego procesu.


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
   - Liczba hostów
   - Wszelkie inne szczegóły

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy hosty, które umożliwiają zapełnienie chmury prywatnej oraz dla hostów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i żądasz dodatkowych hostów, pamiętaj, że potrzebujemy pięciu dni roboczych, aby przydzielić hosty. 

1. Przed udostępnieniem hostów upewnij się, że rejestrujesz dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Włącz rozwiązanie VMware dla platformy Azure dla klientów programu CSP 

Dostawcy usług kryptograficznych muszą korzystać z [Centrum partnerskiego firmy Microsoft](https://partner.microsoft.com) , aby umożliwić klientom korzystanie z rozwiązań VMware platformy Azure. 

   >[!IMPORTANT] 
   >Usługa Azure VMware Solution Service nie zapewnia wymaganej wielu dzierżawców. Partnerzy hostingu wymagający tego nie są obsługiwani. 

1. W **centrum partnerskim** wybierz **dostawcę CSP** , aby uzyskać dostęp do obszaru **Customers** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Obszar klientów Centrum partnerskiego firmy Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Wybierz klienta, a następnie wybierz pozycję **Dodaj produkty**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centrum Partnerskie Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Wybierz pozycję **Azure plan** , a następnie wybierz pozycję **Dodaj do koszyka**. 

1. Przejrzyj i Zakończ ogólne ustawienia subskrypcji planu platformy Azure dla klienta. Aby uzyskać więcej informacji, zobacz [dokumentację Centrum partnerskiego firmy Microsoft](/partner-center/azure-plan-manage).

Po skonfigurowaniu planu platformy Azure i wymaganych uprawnieniach vSphere RBAC są stosowane jako dostawcy CSP, firma Microsoft może włączyć limit przydziału dla subskrypcji planu platformy Azure. Dostęp do Azure Portal z Centrum partnerskiego przy użyciu procedury **administratora w imieniu** (AOBO).

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
   - Liczba hostów
   - Wszelkie inne szczegóły
   - Czy jest przeznaczony do hostowania wielu klientów?

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy hosty, które umożliwiają zapełnienie chmury prywatnej oraz dla hostów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i żądasz dodatkowych hostów, pamiętaj, że potrzebujemy pięciu dni roboczych, aby przydzielić hosty. 

1. Jeśli subskrypcja jest zarządzana przez dostawcę usług, jego zespół administracyjny musi uzyskać dostęp do Azure Portal przy użyciu procedury ponownie **administrator w imieniu** (AOBO) z Centrum partnerskiego. Jeden w Azure Portal uruchomić wystąpienie [Cloud Shell](../cloud-shell/overview.md) i zarejestrować dostawcę zasobów **Microsoft. Automatyczna synchronizacja** i kontynuować wdrażanie chmury prywatnej rozwiązania Azure VMware.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

1. Jeśli subskrypcja jest zarządzana bezpośrednio przez klienta, Rejestracja dostawcy zasobów **Microsoft. Automatyczna synchronizacja** musi zostać wykonana przez użytkownika z wystarczającymi uprawnieniami w ramach subskrypcji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) , aby uzyskać więcej szczegółów i sposobów rejestrowania dostawcy zasobów. 


## <a name="next-steps"></a>Następne kroki

Po włączeniu zasobu rozwiązania Azure VMware i umieszczeniu odpowiedniej sieci na miejscu można [utworzyć chmurę prywatną](tutorial-create-private-cloud.md).