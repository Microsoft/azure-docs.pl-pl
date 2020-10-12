---
title: Tworzenie przepływu danych mapowania
description: Jak utworzyć przepływ danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416524"
---
# <a name="create-azure-data-factory-data-flow"></a>Tworzenie przepływu danych usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapowanie przepływów danych w podajniku APD zapewnia sposób przekształcania danych na dużą skalę bez konieczności kodowania. Możesz zaprojektować zadanie przekształcenia danych w Projektancie przepływu danych, tworząc serię transformacji. Zacznij od dowolnej liczby przekształceń źródłowych, po których następuje procedura przekształcania danych. Następnie ukończ przepływ danych za pomocą ujścia, aby wystawić wyniki w miejscu docelowym.

Rozpocznij od utworzenia nowego Data Factory w wersji 2 z Azure Portal. Po utworzeniu nowej fabryki kliknij kafelek "Tworzenie & Monitor", aby uruchomić Data Factory interfejs użytkownika.

![Opcje przepływu danych](media/data-flow/v2portal.png "Tworzenie przepływu danych")

Gdy korzystasz z interfejsu użytkownika Data Factory, możesz użyć przykładowych przepływów danych. Przykłady są dostępne w galerii szablonów ADF. W obszarze ADF Utwórz "potok z szablonu" i wybierz kategorię przepływu danych z galerii szablonów.

![Opcje przepływu danych](media/data-flow/template.png "Tworzenie przepływu danych")

Zostanie wyświetlony monit o wprowadzenie informacji o koncie usługi Azure Blob Storage.

![Opcje przepływu danych](media/data-flow/template2.png "przepływ danych — Tworzenie 2")

[Dane używane dla tych przykładów można znaleźć tutaj](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Pobierz przykładowe dane i Zapisz pliki na kontach usługi Azure Blob Storage, aby można było wykonać przykłady.

## <a name="create-new-data-flow"></a>Utwórz nowy przepływ danych

Użyj przycisku Utwórz zasób i zaloguj się w interfejsie użytkownika ADF, aby utworzyć przepływy danych.

![Opcje przepływu danych](media/data-flow/newresource.png "Nowy zasób")

## <a name="next-steps"></a>Następne kroki

Zacznij tworzyć transformację danych przy użyciu [transformacji źródłowej](data-flow-source.md).
