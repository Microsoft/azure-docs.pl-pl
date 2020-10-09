---
title: 'Praca zdalna przy użyciu bastionu: Azure bastionu'
description: Na tej stronie opisano, jak można korzystać z usługi Azure bastionu, aby umożliwić pracę zdalną z powodu COVID-19 Pandemic.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80619397"
---
# <a name="working-remotely-using-azure-bastion"></a>Praca zdalna przy użyciu usługi Azure bastionu

Usługa Azure bastionu odgrywa przestawianą rolę w obsłudze scenariuszy pracy zdalnej przez umożliwienie użytkownikom łączności z Internetem w celu uzyskania dostępu do maszyn wirtualnych platformy Azure. W szczególności umożliwia ona administratorom IT zarządzanie swoimi aplikacjami działającymi na platformie Azure w dowolnym miejscu i z dowolnego miejsca na całym świecie.

>[!NOTE]
>W tym artykule opisano, jak korzystać z usługi Azure bastionu, Azure, Microsoft Network i ekosystemu partnerów platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są nawiązane z powodu kryzysu COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Bezpieczny dostęp do maszyn wirtualnych

Usługa Azure bastionu zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi w sieci wirtualnej platformy Azure, bezpośrednio w Azure Portal bez użycia publicznego adresu IP. Aby uzyskać więcej informacji na temat architektury i kluczowych funkcji platformy Azure, zobacz [co to jest usługa Azure bastionu](bastion-overview.md).

Platforma Azure bastionu jest wdrażana dla każdej sieci wirtualnej, co oznacza, że firmy mogą konfigurować jeden bastionu platformy Azure i zarządzać nim, aby szybko obsługiwać dostęp użytkowników zdalnych do maszyn wirtualnych w ramach sieci wirtualnej platformy Azure. Aby uzyskać wskazówki dotyczące tworzenia i zarządzania usługą Azure bastionu, zobacz [Tworzenie hosta bastionu](bastion-create-host-portal.md).

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj usługę Azure bastionu za pomocą [Azure Portal](bastion-create-host-portal.md), [programu PowerShell](bastion-create-host-powershell.md)lub interfejsu wiersza polecenia platformy Azure.

* Przeczytaj [często zadawane pytania](bastion-faq.md) dotyczące usługi bastionu, aby uzyskać dodatkowe informacje.
