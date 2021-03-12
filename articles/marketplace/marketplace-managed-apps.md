---
title: Przewodnik publikowania oferty aplikacji zarządzanych przez aplikacje platformy Azure — Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania aplikacji zarządzanej w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 09badebab86da8e4fd8d35307aa6648a26e91821
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232260"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Przewodnik publikowania dla aplikacji zarządzanych przez platformę Azure

Oferta *aplikacji zarządzanej* przez platformę Azure to jeden ze sposobów publikowania aplikacji platformy Azure w portalu Azure Marketplace. Aplikacje zarządzane to oferty Transact wdrożone i rozliczane za pomocą witryny Azure Marketplace. Opcja wystaw, którą widzi użytkownik, otrzymuje *teraz*.

W tym artykule wyjaśniono wymagania dotyczące typu oferty aplikacji zarządzanej.

Użyj typu oferty aplikacji zarządzanej w następujących warunkach:

- Wdrażasz rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny wirtualnej (VM) lub całego rozwiązania opartego na infrastrukturze jako usługi (IaaS).
- Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera.

>[!NOTE]
>Na przykład partner może być integratorem systemów lub dostawcą usług zarządzanych (MSP).  

## <a name="managed-application-offer-requirements"></a>Wymagania oferty zarządzanej aplikacji

|Wymagania |Szczegóły  |
|---------|---------|
|Subskrypcja platformy Azure | Zarządzane aplikacje muszą być wdrożone w ramach subskrypcji klienta, ale mogą być zarządzane przez inną firmę. |
|Rozliczenia i pomiary    |  Zasoby są udostępniane w ramach subskrypcji platformy Azure klienta. Zasoby platformy Azure korzystające z modelu płatności zgodnie z rzeczywistym użyciem są transakcyjne dla klienta za pośrednictwem firmy Microsoft i są rozliczane za pośrednictwem subskrypcji platformy Azure klienta. <br><br> W przypadku zasobów platformy Azure z licencją na własne potrzeby firma Microsoft rozlicza wszelkie koszty związane z infrastrukturą, które są naliczane w ramach subskrypcji klienta, ale bezpośrednio obciążają klienta opłaty za Licencjonowanie oprogramowania.        |
|Pakiet aplikacji zarządzanej na platformie Azure    |   Skonfigurowany szablon Azure Resource Manager i tworzenie definicji interfejsu użytkownika, który zostanie użyty do wdrożenia aplikacji w ramach subskrypcji klienta.<br><br>Aby uzyskać więcej informacji na temat tworzenia aplikacji zarządzanej, zobacz [Omówienie aplikacji zarządzanej](../azure-resource-manager/managed-applications/publish-service-catalog-app.md).|

---

> [!NOTE]
> Zarządzane aplikacje muszą być wdrażane za pomocą witryny Azure Marketplace. Jeśli komunikacja z klientem jest istotna, skontaktuj się z zainteresowanymi klientami po włączeniu udostępniania potencjalnego klienta.  

> [!Note]
> Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna. Aby uzyskać więcej informacji na temat marketingu oferty za pomocą kanałów partnerów CSP firmy Microsoft, zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md).

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, Dowiedz się, jak [rozwijać swoją firmę w chmurze za pomocą witryny Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Aby zarejestrować się i rozpocząć pracę w centrum partnerskim:

- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz [Tworzenie oferty aplikacji platformy Azure](./create-new-azure-apps-offer.md) .
