---
title: Jak chronić serwery centrum administracyjnego systemu Windows za pomocą Azure Security Center
description: W tym artykule wyjaśniono, jak zintegrować Azure Security Center z centrum administracyjnym systemu Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: fc4207547a1f34fca1ef302626f1365572a236b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101311"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Ochrona zasobów centrum administracyjnego systemu Windows za pomocą Security Center

Centrum administracyjne systemu Windows to narzędzie do zarządzania dla serwerów z systemem Windows. Jest to jedna lokalizacja dla administratorów systemu, aby uzyskać dostęp do większości najczęściej używanych narzędzi administracyjnych. Z poziomu Centrum administracyjnego systemu Windows możesz bezpośrednio dołączyć serwery lokalne do Azure Security Center. Możesz wyświetlić podsumowanie zaleceń dotyczących zabezpieczeń i alertów bezpośrednio w środowisku centrum administracyjnego systemu Windows.

> [!NOTE]
> Aby można było włączyć integrację centrum administracyjnego systemu Windows, w ramach subskrypcji platformy Azure i powiązanego obszaru roboczego Log Analytics należy włączyć usługę Azure Defender.
> Usługa Azure Defender jest bezpłatna przez pierwsze 30 dni, jeśli nie została wcześniej użyta w subskrypcji i obszarze roboczym. Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i według regionu, zobacz [Security Center Cennik](https://azure.microsoft.com/pricing/details/security-center/).
>

Po pomyślnym dołączeniu serwera z centrum administracyjnego systemu Windows do Azure Security Center można:

* Wyświetlanie alertów zabezpieczeń i zaleceń w ramach rozszerzenia Security Center w centrum administracyjnym systemu Windows
* Wyświetlanie stan zabezpieczeń i pobieranie dodatkowych szczegółowych informacji o zarządzanych serwerach centrum administracyjnego systemu Windows w Security Center w ramach Azure Portal (lub za pośrednictwem interfejsu API)

Łącząc te dwa narzędzia, Security Center stać się jednym okienkiem Glass, aby wyświetlić wszystkie informacje o zabezpieczeniach, niezależnie od zasobu: Ochrona centrum administracyjnego systemu Windows zarządzanych serwerów lokalnych, maszyn wirtualnych i innych obciążeń PaaS.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Dołączanie serwerów zarządzanych w centrum administracyjnym systemu Windows do Security Center

1. W centrum administracyjnym systemu Windows wybierz jeden z serwerów, a następnie w okienku **Narzędzia** wybierz rozszerzenie Azure Security Center:

    ![Azure Security Center rozszerzenie w centrum administracyjnym systemu Windows](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Jeśli serwer został już dołączony do Security Center, okno Ustawienia nie zostanie wyświetlone.

1. Kliknij przycisk **Zaloguj się do platformy Azure i skonfiguruj**.
    ![Dołączanie rozszerzenia centrum administracyjnego systemu Windows do Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Postępuj zgodnie z instrukcjami, aby połączyć serwer z Security Center. Po wprowadzeniu niezbędnych szczegółów i potwierdzeniu Security Center wprowadza niezbędne zmiany konfiguracji, aby upewnić się, że spełnione są wszystkie następujące wymagania:
    * Brama platformy Azure jest zarejestrowana.
    * Serwer ma obszar roboczy do raportowania i skojarzonej subskrypcji.
    * Rozwiązanie Log Analytics Security Center jest włączone w obszarze roboczym. To rozwiązanie udostępnia funkcje usługi Azure Defender dla *wszystkich* serwerów i maszyn wirtualnych raportowanych w tym obszarze roboczym.
    * Usługa Azure Defender dla serwerów jest włączona w ramach subskrypcji.
    * Agent Log Analytics jest zainstalowany na serwerze i skonfigurowany do raportowania do wybranego obszaru roboczego. Jeśli serwer już jest raportowany do innego obszaru roboczego, jest on skonfigurowany do raportowania do nowo wybranego obszaru roboczego.

    > [!NOTE]
    > Po dołączeniu do zaleceń może upłynąć trochę czasu. W rzeczywistości w zależności od aktywności serwera nie można otrzymywać *żadnych* alertów. Aby generować alerty testowe w celu sprawdzenia, czy alerty działają prawidłowo, postępuj zgodnie z instrukcjami podanymi w [procedurze walidacji alertu](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Wyświetlanie zaleceń i alertów dotyczących zabezpieczeń w centrum administracyjnym systemu Windows

Po dołączeniu można wyświetlić swoje alerty i zalecenia bezpośrednio w obszarze Azure Security Center centrum administracyjnego systemu Windows. Kliknij rekomendację lub alert, aby wyświetlić je w Azure Portal. W tym miejscu uzyskasz dodatkowe informacje i dowiesz się, jak skorygować problemy.

[![Security Center zalecenia i alerty wyświetlane w centrum administracyjnym systemu Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Wyświetlanie zaleceń dotyczących zabezpieczeń i alertów dla serwerów zarządzanych w centrum administracyjnym systemu Windows w Security Center
Z Azure Security Center:

* Aby wyświetlić zalecenia dotyczące zabezpieczeń dla wszystkich serwerów centrum administracyjnego systemu Windows, Otwórz [spis zasobów](asset-inventory.md) i odfiltruj do typu komputera, który chcesz zbadać. Wybierz kartę **maszyny wirtualne i komputery** .

* Aby wyświetlić alerty zabezpieczeń dla wszystkich serwerów centrum administracyjnego systemu Windows, Otwórz aplet **alerty zabezpieczeń**. Kliknij przycisk **Filtruj** i upewnij się, że wybrano **tylko** opcję "nie na platformie Azure":

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrowanie alertów zabezpieczeń dla serwerów zarządzanych w centrum administracyjnym systemu Windows" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::