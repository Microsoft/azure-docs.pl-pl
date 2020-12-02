---
title: 'Samouczek: wymagania wstępne dotyczące Cognitive Services na platformie Azure Synapse'
description: Samouczek dotyczący konfigurowania wymagań wstępnych dotyczących używania Cognitive Services na platformie Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468374"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Samouczek: wymagania wstępne dotyczące używania Cognitive Services na platformie Azure Synapse

W tym samouczku dowiesz się, jak skonfigurować wymagania wstępne, aby bezpiecznie wykorzystać Cognitive Services na platformie Azure Synapse.

W tym samouczku opisano następujące czynności:
> [!div class="checklist"]
> - Utwórz zasoby Cognitive Services. Na przykład analiza tekstu lub wykrywacz anomalii.
> - Przechowuj klucz uwierzytelniania, aby Cognitive Services zasoby jako wpisy tajne w magazynie kluczy Azure, i Skonfiguruj dostęp do obszaru roboczego usługi Azure Synapse.
> - Utwórz połączoną usługę Azure Key magazynu w obszarze roboczym usługi Azure Synapse Analytics.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu Cognitive Services

[Platforma Azure Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492) obejmuje wiele różnych typów usług. Poniżej przedstawiono przykłady, które są używane w samouczkach Synapse.

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii
Utwórz [detektor anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) w Azure Portal.

![Utwórz detektor anomalii](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Tworzenie zasobu analiza tekstu
Utwórz zasób [Analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) w Azure Portal.

![Tworzenie analizy tekstu](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Tworzenie Key Vault i Konfigurowanie wpisów tajnych i dostępu

1. Utwórz [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) w Azure Portal.
2. Przejdź do **zasad dostępu Key Vault->** i Udziel uprawnień do pliku [MSI obszaru roboczego usługi Azure Synapse](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) , aby odczytywać wpisy tajne z Azure Key Vault.

>Upewnij się, że zmiany zasad zostały zapisane. Ten krok można łatwo pominąć.

![Dodawanie zasad dostępu](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Przejdź do zasobu usługi poznawczej, na przykład **detektor anomalii — > klucze i punkt końcowy**, skopiuj jeden z dwóch kluczy do Schowka.

4. Przejdź do pozycji **Key Vault-> Secret** , aby utworzyć nowy wpis tajny. Określ nazwę wpisu tajnego, a następnie wklej klucz z poprzedniego kroku do pola "value" (wartość). Na koniec kliknij pozycję **Utwórz**.

![Utwórz klucz tajny](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Pamiętaj, aby zapamiętać lub zanotować tę nazwę klucza tajnego. Będzie on używany później podczas łączenia się z Cognitive Services z usługi Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Tworzenie połączonej usługi magazynu kluczy platformy Azure w usłudze Azure Synapse

1. Otwórz obszar roboczy w usłudze Azure Synapse Studio. Przejdź do **Narzędzia zarządzaj > połączonymi usługami**. Utwórz połączoną usługę AB "Azure Key Vault" wskazującą Key Vault, który właśnie utworzyliśmy. Następnie sprawdź połączenie, klikając przycisk "Test connection" i sprawdzając, czy jest on zielony. Jeśli wszystko działa prawidłowo, kliknij przycisk "Utwórz", a następnie kliknij pozycję "Opublikuj wszystko", aby zapisać zmiany.
![Połączona usługa](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Teraz możesz przystąpić do kontynuowania pracy z jednym z samouczków dotyczących korzystania z usługi Azure Cognitive Services w usłudze Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Analiza tonacji przy użyciu usługi Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: wykrywanie anomalii w usłudze Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: ocenianie modelu uczenia maszynowego w ramach dedykowanych pul SQL usługi Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Możliwości Machine Learning w usłudze Azure Synapse Analytics](what-is-machine-learning.md)