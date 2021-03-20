---
title: 'Praca zdalna przy użyciu bastionu: Azure bastionu'
description: Na tej stronie opisano, jak można korzystać z usługi Azure bastionu, aby umożliwić pracę zdalną z powodu COVID-19 Pandemic.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077953"
---
# <a name="working-remotely-using-azure-bastion"></a>Praca zdalna przy użyciu usługi Azure bastionu

Usługa Azure bastionu odgrywa przestawianą rolę w obsłudze scenariuszy pracy zdalnej przez umożliwienie użytkownikom łączności z Internetem w celu uzyskania dostępu do maszyn wirtualnych platformy Azure. W szczególności umożliwia ona administratorom IT zarządzanie swoimi aplikacjami działającymi na platformie Azure w dowolnym miejscu i z dowolnego miejsca na całym świecie.

>[!NOTE]
>W tym artykule opisano, jak korzystać z usługi Azure bastionu, Azure, Microsoft Network i ekosystemu partnerów platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są nawiązane z powodu kryzysu COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Bezpieczny dostęp do maszyn wirtualnych

Usługa Azure bastionu zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi w sieci wirtualnej platformy Azure, bezpośrednio w Azure Portal bez użycia publicznego adresu IP. Aby uzyskać więcej informacji na temat architektury i kluczowych funkcji platformy Azure, zobacz [co to jest usługa Azure bastionu](bastion-overview.md).

Platforma Azure bastionu jest wdrażana dla każdej sieci wirtualnej, co oznacza, że firmy mogą konfigurować jeden bastionu platformy Azure i zarządzać nim, aby szybko obsługiwać dostęp użytkowników zdalnych do maszyn wirtualnych w ramach sieci wirtualnej platformy Azure. Aby uzyskać wskazówki dotyczące tworzenia i zarządzania usługą Azure bastionu, zobacz [Tworzenie hosta bastionu](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj usługę Azure bastionu za pomocą [Azure Portal](./tutorial-create-host-portal.md), [programu PowerShell](bastion-create-host-powershell.md)lub interfejsu wiersza polecenia platformy Azure.

* Przeczytaj [często zadawane pytania](bastion-faq.md) dotyczące usługi bastionu, aby uzyskać dodatkowe informacje.