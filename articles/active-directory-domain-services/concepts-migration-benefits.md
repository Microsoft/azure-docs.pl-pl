---
title: Zalety klasycznej migracji wdrożenia w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się więcej o zaletach migrowania klasycznego wdrożenia Azure Active Directory Domain Services do modelu wdrażania Menedżer zasobów
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 93dcc1202c08be905cf08513f38e79a8a7674e01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650132"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Zalety migracji z klasycznego do Menedżer zasobów modelu wdrażania w programie Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) umożliwia Migrowanie istniejącej domeny zarządzanej, która korzysta z klasycznego modelu wdrażania do modelu wdrażania Menedżer zasobów. Domeny zarządzane AD DS platformy Azure korzystające z modelu wdrażania Menedżer zasobów oferują dodatkowe funkcje, takie jak szczegółowe zasady haseł, dzienniki inspekcji i ochrona blokady konta.

W tym artykule przedstawiono zalety migracji. Aby rozpocząć, zobacz [migrowanie Azure AD Domain Services z klasycznego modelu sieci wirtualnej do Menedżer zasobów][howto-migrate].

> [!NOTE]
> W 2017 Azure AD Domain Services stało się dostępne do hostowania w sieci Azure Resource Manager. Od tego czasu mogliśmy stworzyć bezpieczniejsze usługi przy użyciu nowoczesnych możliwości Azure Resource Manager. Ponieważ wdrożenia Azure Resource Manager w pełni zastępują wdrożenia klasyczne, wdrożenia usługi Azure AD DS klasycznej sieci wirtualnej zostaną wycofane 1 marca 2023.
>
> Aby uzyskać więcej informacji, zobacz [oficjalne powiadomienie o zaniechaniu](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Zalety migracji

Proces migracji obejmuje istniejącą domenę zarządzaną, która korzysta z klasycznego modelu wdrażania i przenosi do korzystania z Menedżer zasobów model wdrażania. Podczas migrowania domeny zarządzanej z modelu wdrażania klasycznego do Menedżer zasobów można uniknąć konieczności ponownego przyłączania maszyn do domeny zarządzanej lub usuwania domeny zarządzanej i utworzenia jej od podstaw. Maszyny wirtualne nadal są przyłączone do domeny zarządzanej po zakończeniu procesu migracji.

Po migracji usługa Azure AD DS udostępnia wiele funkcji, które są dostępne tylko dla domen korzystających z Menedżer zasobów modelu wdrażania, takich jak następujące:

* [Szczegółowe wsparcie dla zasad haseł][password-policy].
* Szybsza szybkość synchronizacji między usługą Azure AD i Azure AD Domain Services.
* Dwa nowe [atrybuty, które synchronizują się z usługi Azure AD][attributes]  -  *Manager* i *IDPracownika*.
* Dostęp do wyższych kontrolerów domeny podczas [uaktualniania jednostki SKU][skus].
* Ochrona blokady konta usługi AD.
* [Powiadomienia e-mail dotyczące alertów w domenie zarządzanej][email-alerts].
* [Użyj skoroszytów platformy Azure i usługi Azure monitor, aby wyświetlić dzienniki inspekcji i aktywność logowania][workbooks].
* W obszarze Obsługiwane regiony [strefy dostępności platformy Azure][availability-zones].
* Integracja z innymi produktami platformy Azure, takimi jak [Azure Files][azure-files], [HD Insights][hd-insights]i [Virtual Desktop systemu Windows][wvd].
* Pomoc techniczna ma dostęp do większej ilości danych telemetrycznych i może szybciej rozwiązywać problemy.
* Szyfrowanie w spoczynku przy użyciu [usługi Azure Managed disks][managed-disks] dla danych na zarządzanych kontrolerach domeny.

Domeny zarządzane, które używają Menedżer zasobów model wdrażania, ułatwiają aktualny dostęp do najnowszych nowych funkcji. Nowe funkcje nie są dostępne dla domen zarządzanych, które korzystają z klasycznego modelu wdrażania.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz [migrowanie Azure AD Domain Services z klasycznego modelu sieci wirtualnej do Menedżer zasobów][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
