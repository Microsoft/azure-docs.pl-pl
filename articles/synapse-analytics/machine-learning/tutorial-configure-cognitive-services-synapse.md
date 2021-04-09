---
title: 'Samouczek: wymagania wstępne dotyczące Cognitive Services w usłudze Azure Synapse Analytics'
description: Dowiedz się, jak skonfigurować wymagania wstępne dotyczące używania Cognitive Services w usłudze Azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936686"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Samouczek: wymagania wstępne dotyczące używania Cognitive Services w usłudze Azure Synapse Analytics

W ramach tego samouczka nauczysz się, jak bezpiecznie skonfigurować wymagania wstępne za pomocą usługi Azure Cognitive Services w usłudze Azure Synapse Analytics.

W tym samouczku opisano następujące czynności:
> [!div class="checklist"]
> - Utwórz zasób Cognitive Services, taki jak analiza tekstu lub detektor anomalii.
> - Przechowuj klucz uwierzytelniania, aby Cognitive Services zasoby jako wpisy tajne w Azure Key Vault, i Skonfiguruj dostęp do obszaru roboczego usługi Azure Synapse Analytics.
> - Utwórz połączoną usługę Azure Key Vault w obszarze roboczym analizy usługi Azure Synapse.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu Azure Data Lake Storage Gen2 skonfigurowanym jako magazyn domyślny. Musisz być *współautorem danych obiektów blob magazynu* w systemie plików Azure Data Lake Storage Gen2, z którym pracujesz.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

[Usługa Azure Cognitive Services](../../cognitive-services/index.yml) obejmuje wiele typów usług. Analiza tekstu i detektor anomalii są dwa przykłady w samouczkach usługi Azure Synapse.

W Azure Portal można utworzyć zasób [Analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) :

![Zrzut ekranu przedstawiający analiza tekstu w portalu przy użyciu przycisku Utwórz.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

W Azure Portal można utworzyć zasób [wykrywania anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) :

![Zrzut ekranu, który pokazuje detektor anomalii w portalu przy użyciu przycisku Utwórz.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Tworzenie magazynu kluczy i Konfigurowanie kluczy tajnych i dostępu

1. Utwórz [Magazyn kluczy](https://ms.portal.azure.com/#create/Microsoft.KeyVault) w Azure Portal.
2. Przejdź do   >  **zasad dostępu** Key Vault i Udziel uprawnień do pliku [MSI obszaru roboczego usługi Azure Synapse](../security/synapse-workspace-managed-identity.md) , aby odczytywać wpisy tajne z Azure Key Vault.

   > [!NOTE]
   > Upewnij się, że zmiany zasad zostały zapisane. Ten krok można łatwo pominąć.

   ![Zrzut ekranu pokazujący wybory dotyczące dodawania zasad dostępu.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Przejdź do zasobu Cognitive Services. Na przykład przejdź do pozycji klucze **wykrywania anomalii**  >  **i punkt końcowy**. Następnie skopiuj jeden z dwóch kluczy do Schowka.

4. Przejdź do **Key Vault**  >  **wpisu tajnego** , aby utworzyć nowy wpis tajny. Określ nazwę wpisu tajnego, a następnie wklej klucz z poprzedniego kroku w polu **wartość** . Na koniec wybierz pozycję **Utwórz**.

   ![Zrzut ekranu pokazujący wybory tworzenia wpisu tajnego.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Pamiętaj, aby zapamiętać lub zanotować tę nazwę klucza tajnego. Będzie on używany później podczas łączenia się z Cognitive Services z usługi Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Tworzenie połączonej usługi Azure Key Vault na platformie Azure Synapse

1. Otwórz obszar roboczy w usłudze Azure Synapse Studio. 
2. Przejdź do obszaru **Zarządzanie**  >  **połączonymi usługami**. Utwórz połączoną usługę **Azure Key Vault** , wskazując do właśnie utworzonego magazynu kluczy. 
3. Sprawdź połączenie, wybierając przycisk **Test connection** . Jeśli połączenie jest kolorem zielonym, wybierz pozycję **Utwórz** , a następnie wybierz pozycję **Opublikuj wszystko** , aby zapisać zmiany.

![Zrzut ekranu przedstawiający Azure Key Vault jako nową połączoną usługę.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Teraz możesz przystąpić do kontynuowania pracy z jednym z samouczków dotyczących korzystania z usługi Azure Cognitive Services w usłudze Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Analiza tonacji przy użyciu usługi Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: wykrywanie anomalii w usłudze Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Samouczek: ocenianie modelu uczenia maszynowego w ramach dedykowanych pul SQL usługi Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics](what-is-machine-learning.md)