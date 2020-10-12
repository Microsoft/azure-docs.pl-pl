---
title: Odzyskiwanie po awarii dla FarmBeats
description: W tym artykule opisano, jak odzyskiwanie danych chroni przed utratą danych.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683891"
---
# <a name="disaster-recovery-for-farmbeats"></a>Odzyskiwanie po awarii dla FarmBeats

Odzyskiwanie danych chroni przed utratą danych w zdarzeniu, takim jak zwinięcie regionu platformy Azure. W takim przypadku można uruchomić tryb failover i odzyskać dane przechowywane we wdrożeniu FarmBeats.

Odzyskiwanie danych nie jest funkcją domyślną w usłudze Azure FarmBeats. Tę funkcję można skonfigurować ręcznie, konfigurując wymagane zasoby platformy Azure, które są używane przez usługę FarmBeats do przechowywania danych w wieloskładnikowym regionie platformy Azure. Aby włączyć odzyskiwanie, użyj podejścia aktywnego — pasywnego.

W poniższych sekcjach znajdują się informacje o sposobie konfigurowania odzyskiwania danych w usłudze Azure FarmBeats:

- [Włącz nadmiarowość danych](#enable-data-redundancy)
- [Przywracanie usługi z kopii zapasowej online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Włącz nadmiarowość danych

FarmBeats przechowuje dane w trzech usługach pierwszej firmy Azure, które są w **usłudze Azure Storage**, **Cosmos DB** i **Time Series Insights**. Wykonaj następujące kroki, aby włączyć nadmiarowość danych dla tych usług w przypadku sparowanego regionu platformy Azure:

1.  **Azure Storage** — postępuj zgodnie z tymi wskazówkami, aby włączyć nadmiarowość danych dla każdego konta magazynu we wdrożeniu FarmBeats.
2.  **Azure Cosmos DB** — postępuj zgodnie z tymi wskazówkami, aby włączyć nadmiarowość danych dla Cosmos DB konta wdrożenie FarmBeats.
3.  **Azure Time Series Insights (TSI)** — TSI obecnie nie oferuje nadmiarowości danych. Aby odzyskać Time Series Insights dane, przejdź do partnera czujnika/pogody i wypchnij dane ponownie do wdrożenia FarmBeats.

## <a name="restore-service-from-online-backup"></a>Przywracanie usługi z kopii zapasowej online

Możesz inicjować tryb failover i odzyskiwać dane przechowywane dla każdego z powyższych magazynów danych dla wdrożenia FarmBeats. Po odzyskaniu danych dla usługi Azure Storage i Cosmos DB Utwórz inne wdrożenie FarmBeats w sparowanym regionie platformy Azure, a następnie skonfiguruj nowe wdrożenie w taki sposób, aby korzystało z danych z przywróconych magazynów danych (np. usługi Azure Storage i Cosmos DB), wykonując następujące czynności:

1. [Konfigurowanie usługi Cosmos DB](#configure-cosmos-db)
2. [Skonfiguruj konto magazynu](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurowanie usługi Cosmos DB

Skopiuj klucz dostępu przywróconej Cosmos DB i zaktualizuj nowe Key Vault FarmBeats Datahub.


  ![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Skopiuj adres URL przywróconej Cosmos DB i zaktualizuj go w nowej konfiguracji App Service FarmBeats Datahub. Teraz możesz usunąć konto Cosmos DB w nowym wdrożeniu FarmBeats.

  ![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Skonfiguruj konto magazynu

Skopiuj klucz dostępu przywróconego konta magazynu i zaktualizuj go w nowym FarmBeats Datahub Key Vault.

![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Upewnij się, że nazwa konta magazynu jest aktualizowana w nowym pliku konfiguracji maszyny wirtualnej FarmBeats Batch.

![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Podobnie, jeśli włączono odzyskiwanie danych dla konta magazynu akceleratora, wykonaj kroki opisane w sekcji Krok 2, aby zaktualizować klucz dostępu i nazwę konta magazynu akceleratora w nowym wystąpieniu FarmBeats.
