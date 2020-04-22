---
title: Odzyskiwanie po awarii dla FarmBeats
description: W tym artykule opisano, jak odzyskiwanie danych chroni przed utratą danych.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683891"
---
# <a name="disaster-recovery-for-farmbeats"></a>Odzyskiwanie po awarii dla FarmBeats

Odzyskiwanie danych chroni przed utratą danych w przypadku, takich jak upadek regionu platformy Azure. W takim przypadku można uruchomić pracę awaryjną i odzyskać dane przechowywane we wdrożeniu FarmBeats.

Odzyskiwanie danych nie jest funkcją domyślną w usłudze Azure FarmBeats. Tę funkcję można skonfigurować ręcznie, konfigurując wymagane zasoby platformy Azure, które są używane przez Grupę FarmBeats do przechowywania danych w sparowanym regionie platformy Azure. Użyj aktywnego — pasywne podejście, aby włączyć odzyskiwanie.

Poniższe sekcje zawierają informacje dotyczące sposobu konfigurowania odzyskiwania danych w usłudze Azure FarmBeats:

- [Włączanie nadmiarowości danych](#enable-data-redundancy)
- [Przywracanie usługi z kopii zapasowej online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Włączanie nadmiarowości danych

FarmBeats przechowuje dane w trzech usługach pierwszej firmy platformy Azure, którymi są **magazyn azure,** **usługa Cosmos DB** i **time series insights.** Wykonaj następujące kroki, aby włączyć nadmiarowość danych dla tych usług do sparowanego regionu platformy Azure:

1.  **Usługa Azure Storage** — postępuj zgodnie z tym wytyczną, aby włączyć nadmiarowość danych dla każdego konta magazynu we wdrożeniu FarmBeats.
2.  **Usługa Azure Cosmos DB** — postępuj zgodnie z tym wytyczną, aby włączyć nadmiarowość danych dla konta usługi Cosmos DB wdrożenia FarmBeats.
3.  **Usługa Azure Time Series Insights (TSI)** — TSI obecnie nie oferuje nadmiarowości danych. Aby odzyskać dane usługi Time Series Insights, przejdź do swojego partnera czujnika/pogody i ponownie wypchnij dane do wdrożenia FarmBeats.

## <a name="restore-service-from-online-backup"></a>Przywracanie usługi z kopii zapasowej online

Można zainicjować pracy awaryjnej i odzyskać dane przechowywane dla których, każdy z wyżej wymienionych magazynów danych dla wdrożenia FarmBeats. Po odzyskaniu danych dla usługi Azure Storage i usługi Cosmos DB utwórz inne wdrożenie FarmBeats w regionie sparowanym na platformie Azure, a następnie skonfiguruj nowe wdrożenie do używania danych z przywróconych magazynów danych (tj. usługi Azure Storage i usługi Cosmos DB), wykonując poniższe kroki:

1. [Konfigurowanie usługi Cosmos DB](#configure-cosmos-db)
2. [Konfigurowanie konta magazynu](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurowanie usługi Cosmos DB

Skopiuj klucz dostępu przywróconego bazy danych Cosmos DB i zaktualizuj nową skarbiec kluczy Danych FarmBeats Datahub.


  ![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Skopiuj adres URL przywróconej bazy danych Usługi Cosmos DB i zaktualizuj go w nowej konfiguracji usługi aplikacji Datahub Firmy FarmBeats. Teraz można usunąć konto usługi Cosmos DB w nowym wdrożeniu FarmBeats.

  ![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Konfigurowanie konta magazynu

Skopiuj klucz dostępu przywróconego konta magazynu i zaktualizuj go w nowym magazynie kluczy Danych Pola Danych FarmBeats.

![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Upewnij się, że można zaktualizować nazwę konta magazynu w nowym pliku konfiguracyjnym farmbeats batch VM.

![Odzyskiwanie po awarii](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Podobnie, jeśli włączono odzyskiwanie danych dla konta magazynu akceleratora, wykonaj krok 2, aby zaktualizować klucz dostępu do konta magazynu akceleratora i nazwę w nowym wystąpieniu FarmBeats.
