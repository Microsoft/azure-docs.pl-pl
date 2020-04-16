---
title: Wdrażanie modelu usługi Mowy niestandardowej — mowa
titleSuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy za pomocą portalu mowy niestandardowej.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402193"
---
# <a name="deploy-a-custom-model"></a>Wdrażanie modelu niestandardowego

Po przekazaniu i sprawdzeniu danych, ocenie dokładności i przeszkoleniu modelu niestandardowego można wdrożyć niestandardowy punkt końcowy do użycia z aplikacjami, narzędziami i produktami. W tym dokumencie dowiesz się, jak utworzyć i wdrożyć punkt końcowy za pomocą [portalu Mowy niestandardowej](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Tworzenie niestandardowego punktu końcowego

Aby utworzyć nowy niestandardowy punkt końcowy, zaloguj się do [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech) i wybierz **polecenie Wdrożenie** z menu Mowa niestandardowa u góry strony. Jeśli jest to twój pierwszy bieg, można zauważyć, że nie ma żadnych punktów końcowych wymienionych w tabeli. Po utworzeniu punktu końcowego użyjesz tej strony do śledzenia każdego wdrożonego punktu końcowego.

Następnie wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **opis** niestandardowego punktu końcowego. Następnie wybierz model niestandardowy, który chcesz skojarzyć z tym punktem końcowym. Na tej stronie można również włączyć rejestrowanie. Rejestrowanie umożliwia monitorowanie ruchu w punktach końcowych. Jeśli ruch jest wyłączony, ruch nie będzie przechowywany.

![Jak wdrożyć model](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nie zapomnij zaakceptować warunków użytkowania i szczegółów cenowych.

Następnie wybierz pozycję **Utwórz**. Ta akcja powoduje powrót do **wdrożenia** strony. Tabela zawiera teraz wpis, który odpowiada niestandardowemu punktowi końcowemu. Stan punktu końcowego pokazuje jego bieżący stan. Utworzenie nowego punktu końcowego przy użyciu modeli niestandardowych może potrwać do 30 minut. Gdy stan wdrożenia zmieni się na **Ukończony,** punkt końcowy jest gotowy do użycia.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako łącze. Kliknij łącze, aby wyświetlić informacje specyficzne dla punktu końcowego, takie jak klucz punktu końcowego, adres URL punktu końcowego i przykładowy kod.

## <a name="view-logging-data"></a>Wyświetlanie danych rejestrowania

Dane rejestrowania są dostępne do pobrania w obszarze **Endpoint > Details**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak korzystać z modelu niestandardowego [tutaj](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Przygotowanie i przetestowanie danych](how-to-custom-speech-test-data.md)
* [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
