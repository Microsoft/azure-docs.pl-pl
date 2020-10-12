---
title: Przenosimy obrazy maszyn wirtualnych do magazynu dysków zarządzanych w portalu Azure Marketplace
description: Aby zapewnić szybszy, bardziej niezawodny magazyn i obsługę nowych funkcji i możliwości witryny Marketplace, przenosimy obrazy maszyn wirtualnych z witryny Marketplace do magazynu Managed Disks.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: iqshahmicrosoft
ms.author: iqshah
ms.openlocfilehash: 814a4012ace95e84e75bcb4071c549822b2b0633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182432"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Przenosimy obrazy maszyn wirtualnych (VM) w witrynie Azure Marketplace do magazynu dyskowego zarządzanego

Aby zapewnić szybszy, bardziej niezawodny magazyn i obsługę nowych funkcji i możliwości witryny Marketplace, przenosimy obrazy maszyn wirtualnych z witryny Marketplace do magazynu Managed Disks.

Od 2 stycznia 2020 przeniesiemy obrazy maszyn wirtualnych do magazynu dysków zarządzanych w fazach. W pierwszej fazie przeniesiemy tylko obrazy bez nowych wdrożeń lub uruchamiania maszyn wirtualnych w ciągu ostatnich 90 dni. Przed przekazaniem dowolnego obrazu wyślemy wiadomość e-mail z informacją o tym, które obrazy zostaną przeniesione i kiedy zostaną przeniesione.

Wydawcy ani konsumenci nie muszą podejmować żadnych działań i nie mają wpływu na użytkowników. Oferty portalu Marketplace pozostaną dostępne, a klienci nadal będą mogli wdrażać zarządzane maszyny wirtualne z tych obrazów podczas przenoszenia i po nim.

Jeśli masz jakieś pytania, [skontaktuj się z nami](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Często zadawane pytania

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Czy użytkownicy moich obrazów maszyn wirtualnych napotykają awarię?

Użytkownicy obrazów maszyn wirtualnych nie będą napotykać przestojów. 

W pierwszej fazie przeniesiemy tylko obrazy maszyn wirtualnych, które nie mają uruchomionych maszyn wirtualnych. Ponieważ nie ma żadnych użytkowników dla tych obrazów, nie ma żadnego wpływu. W przypadku kolejnych faz nie będzie to miało wpływu na użytkowników.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Jak długo trwa proces realizacji procesu?

Ukończenie migracji może zająć do 24 godzin.

### <a name="do-i-need-to-take-any-action"></a>Czy muszę wykonać dowolne działanie?

Nie. Wydawcy ani konsumenci nie muszą podejmować żadnych działań.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Czy muszę zaktualizować system, aby wywoływać interfejsy API portalu w chmurze w inny sposób, po przeniesieniu ich do magazynu Managed disks?

Nie. Istniejące wywołania interfejsu API będą nadal działały.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Czy wszystkie obrazy maszyn wirtualnych będą przenoszone do dysku zarządzanego w tym samym czasie?

Wszystkie obrazy maszyn wirtualnych będą przenoszone w tym samym dniu. Powiadomimy Cię o przeniesieniu.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Czy mogę zażądać późniejszego zaplanowania przenoszenia obrazów maszyn wirtualnych?

Zalecamy przeniesienie obrazów w zaplanowanym terminie. Jeśli jednak masz problemy, skontaktuj się z nami, aby ponownie zaplanować przeniesienie.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Czy mogę publikować aktualizacje obrazów maszyn wirtualnych podczas przenoszenia?

Podczas przenoszenia nie można wprowadzać aktualizacji obrazów maszyn wirtualnych.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Czy proces publikowania zostanie zmieniony po przeniesieniu obrazu maszyny wirtualnej na dysk zarządzany?

Nie, proces publikowania pozostanie taki sam. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Czy Wydawca może przenieść swoje oferty na dysk zarządzany?

Nie, wydawcy nie mogą przenieść swoich ofert do dysku zarządzanego. Będą musieli czekać, a ich obrazy będą przenoszone automatycznie. Wyślemy powiadomienia do wydawcy przed wprowadzeniem jakichkolwiek zmian.
