---
title: Co to jest Azure Application Gateway
description: Dowiedz się, jak za pomocą usługi Azure Application Gateway zarządzać ruchem internetowym kierowanym do aplikacji.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 7ccc83a61ac4ffe6e1bb6767a9c611bd3fcc0edf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88892783"
---
# <a name="what-is-azure-application-gateway"></a>Co to jest Azure Application Gateway?

Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Tradycyjne moduły równoważenia obciążenia działają w warstwie transportu (warstwie OSI 4 — TCP i UDP) i kierują ruch na podstawie źródłowego adresu IP i portu do docelowego adresu IP i portu.

Application Gateway może podejmować decyzje dotyczące routingu na podstawie dodatkowych atrybutów żądania HTTP, na przykład ścieżki URI lub nagłówków hosta. Na przykład można kierować ruch na podstawie przychodzącego adresu URL. Jeśli w przychodzącym adresie URL jest element `/images`, można kierować ruch do określonego zestawu serwerów (nazywanego pulą) skonfigurowanego na potrzeby obrazów. Jeśli `/video` znajduje się w adresie URL, ten ruch jest kierowany do innej puli, która jest zoptymalizowana pod kątem filmów wideo.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ten typ routingu jest nazywany równoważeniem obciążenia warstwy aplikacji (warstwy OSI 7). Usługa Azure Application Gateway może wykonywać routing oparty na adresach URL i nie tylko.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli potrzebujesz wysokiej wydajności, niskiego opóźnienia i równoważenia obciążenia warstwy 4, zobacz [co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Jeśli szukasz globalnego równoważenia obciążenia DNS, zobacz [co to jest Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Kompleksowe scenariusze mogą przynieść korzyści wynikające z łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funkcje

Aby dowiedzieć się więcej o funkcjach Application Gateway, zobacz [Azure Application Gateway Features](features.md).

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA

Aby uzyskać informacje o cenach Application Gateway, zobacz [Cennik usługi Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/).

Aby uzyskać informacje dotyczące Application Gateway umów SLA, zobacz [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Co nowego

Aby dowiedzieć się, co nowego w usłudze Azure Application Gateway, zobacz [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Następne kroki

W zależności od wymagań i środowiska można utworzyć test Application Gateway przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal](quick-create-portal.md)
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure PowerShell](quick-create-powershell.md)
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
