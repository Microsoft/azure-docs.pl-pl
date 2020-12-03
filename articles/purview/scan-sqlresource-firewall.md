---
title: Skanowanie usługi Azure SQL DB za zaporą
description: Dowiedz się, jak skanować zasoby za zaporą za pomocą portalu Azure kontrolą.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553810"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Skanowanie usługi Azure SQL DB za zaporą w usłudze Azure kontrolą

W tym artykule dowiesz się, jak używać usługi Azure kontrolą do skanowania zasobów za zaporą.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Twoja [dzierżawa Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Konfigurowanie magazynu SQL za zaporą

Pierwszym krokiem jest dodanie pliku MSI wykazu do bazy danych Azure SQL za pośrednictwem [rejestru i przeskanowanie Azure SQL Database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Skanuj bazę danych SQL z kontrolą

1. Przejdź do strony głównej kontrolą.

1. Na lewym pasku nawigacyjnym wybierz pozycję **centrum zarządzania** .

1. Wybierz pozycję **źródła danych** w obszarze **źródła i skanowanie**.

1. Wybierz pozycję **+ Nowy** , aby dodać źródło danych.

1. Wybierz **Azure SQL Database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Zrzut ekranu przedstawiający wybór programu SQL Server.":::

1. Podaj nazwę nowego źródła danych, wybierz **subskrypcję platformy Azure**, a następnie wybierz swoją subskrypcję i nazwę serwera z listy rozwijanej.

   Wybierz pozycję **Zakończ** , aby zakończyć rejestrację. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Zrzut ekranu, aby zakończyć wybór":::

1. Wybierz pozycję **Skonfiguruj skanowanie** dla magazynu, który znajduje się za zaporą i Testuj połączenie. Połączenie wskazuje, że zakończyło się pomyślnie. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Zrzut ekranu przedstawiający wybór ustawienia skanowania T0.":::

1. Skonfiguruj częstotliwość skanowania i wybierz pozycję **Kontynuuj**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Zrzut ekranu przedstawiający zaznaczenie w celu uruchomienia skanowania SQL.":::

1.  Skanowanie zostanie ukończone, a stan zostanie zaktualizowany na liście źródeł danych.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Zrzut ekranu przedstawiający skanowanie komunikatów zakończone":::
   
## <a name="next-steps"></a>Następne kroki

Następnie można skonfigurować zestaw reguł skanowania [Utwórz regułę skanowania](create-a-scan-rule-set.md) , aby grupować skany ze sobą.
