---
title: Zarządzanie wieloma dzierżawcami na platformie Azure — wskaźnikiem dostawcy usługi zabezpieczeń zarządzanych | Microsoft Docs
description: Jak dołączyć wiele dzierżawców i zarządzać nimi na platformie Azure — jako dostawca zarządzanego usługi zabezpieczeń (MSSP) za pomocą usługi Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578154"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Zarządzanie wieloma dzierżawcami na platformie Azure — wskaźnikiem MSSP

Jeśli jesteś zarządzanym dostawcą usług zabezpieczeń (MSSP) i korzystasz z usługi [Azure Lighthouse](../lighthouse/overview.md) do oferowania klientom usług Security Operations Center (SOC), możesz zarządzać zasobami centrów danych platformy Azure dla klientów bezpośrednio z własnej dzierżawy platformy Azure bez konieczności nawiązywania połączenia z dzierżawcą klienta. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Dołączanie usługi Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Aby to działało prawidłowo, dzierżawca (dzierżawa MSSP) musi mieć zarejestrowanych dostawców zasobów usługi Azure wskaźnikowej na co najmniej jednej subskrypcji. Ponadto wszyscy dzierżawcy klientów muszą mieć zarejestrowane dostawcy zasobów. Jeśli w dzierżawie zarejestrowano wskaźnik na platformie Azure, a klienci są w ich ramach, możesz rozpocząć pracę. Aby zweryfikować rejestrację, wykonaj następujące czynności:

    1. Wybierz pozycję **subskrypcje** z Azure Portal, a następnie wybierz odpowiednią subskrypcję z menu.

    1. W menu nawigacji na ekranie subskrypcja w obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.

    1. Z ** *nazwy subskrypcji* | Ekranu dostawcy zasobów** , Wyszukaj i wybierz pozycję *Microsoft. OperationalInsights* i *Microsoft. SecurityInsights*i Sprawdź kolumnę **stan** . Jeśli dostawca jest w stanie *NotRegistered*, wybierz pozycję **zarejestruj**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Sprawdź dostawców zasobów":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Jak uzyskać dostęp do platformy Azure ze wskaźnikami zarządzanymi dzierżawcami

1. W obszarze **katalog i subskrypcja**wybierz pozycję katalogi delegowane (katalog = dzierżawca) oraz subskrypcje, w których znajdują się obszary robocze usługi Azure — wskaźnik na klienta.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Sprawdź dostawców zasobów":::

1. Otwórz wskaźnik platformy Azure. Zobaczysz wszystkie obszary robocze w wybranych subskrypcjach i będzie można bezproblemowo współpracować z nimi, podobnie jak w przypadku dowolnego obszaru roboczego w Twojej dzierżawie.

> [!NOTE]
> Nie będzie można wdrażać łączników na platformie Azure z poziomu zarządzanego obszaru roboczego. Aby wdrożyć łącznik, musisz bezpośrednio zalogować się do dzierżawy, w której ma zostać wdrożony łącznik, i uwierzytelnić się z wymaganymi uprawnieniami.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób bezproblemowego zarządzania wieloma dzierżawcami usługi Azure Wskaźnikowania. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

