---
title: Żądaj przydziału hosta i Włącz rozwiązanie VMware dla platformy Azure
description: Dowiedz się, jak poprosić o przydział/pojemność hosta i włączyć dostawcę zasobów rozwiązania VMware platformy Azure. Możesz również zażądać większej liczby hostów w istniejącej chmurze prywatnej rozwiązania VMware platformy Azure.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653174"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Żądaj przydziału hosta i Włącz rozwiązanie VMware dla platformy Azure

W tym przewodniku dowiesz się, jak poprosić o przydział/pojemność hosta i włączyć dostawcę zasobów [rozwiązania VMware platformy Azure](introduction.md) , który umożliwia korzystanie z usługi. Aby można było włączyć rozwiązanie VMware dla platformy Azure, należy przesłać bilet pomocy technicznej w celu przydzielenia hostów. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i potrzebujesz większej liczby hostów, postępuj zgodnie z tym samym procesem.

>[!IMPORTANT]
>Przydzielenie hostów może potrwać kilka dni w zależności od żądanego numeru.  Należy więc poprosić o to, co jest potrzebne do aprowizacji, aby nie trzeba było zażądać zwiększenia limitu przydziału.


Ogólny proces jest prosty i obejmuje dwa kroki:
- Żądaj dodatkowego limitu przydziału/pojemności hosta dla klientów z [umowami EA](#request-host-quota-for-ea-customers) lub dla [klientów korzystających](#request-host-quota-for-csp-customers) z programu CSP 
- Włącz dostawcę zasobów Microsoft. Automatyczna synchronizacja

## <a name="eligibility-criteria"></a>Kryteria kwalifikacji

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure. Subskrypcja platformy Azure musi być zgodna z jednym z następujących kryteriów:

- Subskrypcja w ramach [usługi Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.
- Subskrypcja zarządzana przez dostawcę rozwiązań w chmurze (CSP) w ramach istniejącego dostawcy CSP Azure oferuje kontrakt lub plan platformy Azure.

## <a name="request-host-quota-for-ea-customers"></a>Żądaj przydziału hosta dla klientów z umowami EA

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


## <a name="request-host-quota-for-csp-customers"></a>Żądaj przydziału hosta dla klientów programu CSP 

Dostawcy usług kryptograficznych muszą korzystać z [Centrum partnerskiego firmy Microsoft](https://partner.microsoft.com) , aby umożliwić klientom korzystanie z rozwiązań VMware platformy Azure. W tym artykule jest stosowany [Plan platformy Azure dla dostawcy usług kryptograficznych](/partner-center/azure-plan-lp) , który ilustruje procedurę zakupu dla partnerów.

Uzyskaj dostęp do Azure Portal przy użyciu procedury **administratora w imieniu** (AOBO) z Centrum partnerskiego.

>[!IMPORTANT] 
>Usługa Azure VMware Solution Service nie zapewnia wymaganej wielu dzierżawców. Partnerzy hostingu wymagający tego nie są obsługiwani. 

1. Skonfiguruj plan Azure dla dostawcy CSP:

   1. W **centrum partnerskim** wybierz **dostawcę CSP** , aby uzyskać dostęp do obszaru **Customers** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Obszar klientów Centrum partnerskiego firmy Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Wybierz klienta, a następnie wybierz pozycję **Dodaj produkty**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centrum Partnerskie Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Wybierz pozycję **Azure plan** , a następnie wybierz pozycję **Dodaj do koszyka**. 
   
   1. Przejrzyj i Zakończ ogólne ustawienia subskrypcji planu platformy Azure dla klienta. Aby uzyskać więcej informacji, zobacz [dokumentację Centrum partnerskiego firmy Microsoft](/partner-center/azure-plan-manage).

1. Po skonfigurowaniu planu platformy Azure i włączeniu wymaganych [uprawnień usługi Azure RBAC](/partner-center/azure-plan-manage) na potrzeby subskrypcji zostanie zażądany przydział dla subskrypcji planu platformy Azure. 

   1. Uzyskaj dostęp do Azure Portal z [Centrum partnerskiego firmy Microsoft](https://partner.microsoft.com) przy użyciu procedury **administratora w imieniu** (AOBO).
   
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

## <a name="register-the-microsoftavs-resource-provider"></a>Zarejestruj dostawcę zasobów **Microsoft. Automatyczna synchronizacja**

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Następne kroki

Po włączeniu zasobu oraz odpowiedniej sieci na miejscu możesz [utworzyć chmurę prywatną](tutorial-create-private-cloud.md).
