---
title: Zalecenia dotyczące magazynu & danych — Azure Security Center
description: Ten dokument zawiera zalecenia dotyczące Azure Security Center, które pomagają chronić dane oraz usługę Azure SQL i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552869"
---
# <a name="protect-azure-data-and-storage-services"></a>Ochrona usług Azure Data and Storage
Gdy Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule wyjaśniono **stronę zabezpieczenia danych** w sekcji zabezpieczenia zasobów Security Center.

Aby zapoznać się z pełną listą zaleceń, które mogą zostać wyświetlone na tej stronie, zobacz [zalecenia dotyczące danych i magazynu](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Wyświetlanie informacji o zabezpieczeniach danych

1. W sekcji **higiena zabezpieczeń zasobów** kliknij pozycję **zasoby danych i magazyn**.

    ![Zasoby magazynu danych &](./media/security-center-monitoring/click-data.png)

    Zostanie otwarta strona **zabezpieczenia danych** z zaleceniami dotyczącymi zasobów danych.

    [![Zasoby danych](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Na tej stronie można:

    * Kliknij kartę **Przegląd** , aby wyświetlić listę wszystkich zaleceń dotyczących zasobów danych do skorygowania. 
    * Kliknij każdą kartę i przejrzyj zalecenia według typu zasobu.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat szyfrowania magazynu, zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w stanie spoczynku](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Korygowanie zalecenia dotyczącego zasobu danych

1. Na dowolnych kartach zasobów kliknij zasób. Zostanie otwarta strona informacje z listą zaleceń, które należy skorygować.

    ![Informacje o zasobach](./media/security-center-monitoring/sql-recommendations.png)

2. Kliknij zalecenie. Zostanie otwarta strona rekomendacja i zostanie wyświetlona **procedura korygowania** służąca do zaimplementowania zalecenia.

   ![Instrukcje rozwiązania problemu](./media/security-center-monitoring/remediate1.png)

3. Kliknij przycisk **podejmij akcję**. Zostanie wyświetlona strona Ustawienia zasobów.

    ![Włącz rekomendację](./media/security-center-monitoring/remediate2.png)

4. Wykonaj **czynności zaradcze** i kliknij przycisk **Zapisz**.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Pełna lista referencyjna zaleceń dotyczących zabezpieczeń Azure Security Center](recommendations-reference.md)
* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)