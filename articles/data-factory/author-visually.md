---
title: Tworzenie wizualne
description: Dowiedz się, jak używać tworzenia wizualnego w Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 09/08/2020
ms.openlocfilehash: 704360ac37e016de9efe2248181f7db358f5a7cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371485"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Tworzenie wizualne w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Środowisko interfejsu użytkownika Azure Data Factory (UX) umożliwia wizualne tworzenie i wdrażanie zasobów dla fabryki danych bez konieczności pisania kodu. Możesz przeciągać działania na kanwę potoku, wykonywać przebiegi testowe, debugować iteracyjnie i wdrażać oraz monitorować przebiegi potoków.

Obecnie Azure Data Factory UX jest obsługiwane tylko w przeglądarkach Microsoft Edge i Google Chrome.

## <a name="authoring-canvas"></a>Kanwa tworzenia

Aby otworzyć **kanwę tworzenia**, kliknij ikonę ołówka. 

![Kanwa tworzenia](media/author-visually/authoring-canvas.png)

Tutaj można tworzyć potoki, działania, zestawy danych, połączone usługi, przepływy danych, wyzwalacze i środowiska Integration Runtime wchodzące w skład Twojego fabryki. Aby rozpocząć tworzenie potoku przy użyciu kanwy tworzenia, zobacz [Kopiowanie danych za pomocą działania kopiowania](tutorial-copy-data-portal.md). 

Domyślne środowisko tworzenia wizualizacji działa bezpośrednio z usługą Data Factory. Azure Repos integracja z usługą Git lub GitHub jest również obsługiwana, aby umożliwić kontrolę źródła i współpracę w ramach potoków usługi Data Factory. Aby dowiedzieć się więcej o różnicach między tymi środowiskami tworzenia, zobacz [Kontrola źródła w Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>Okienko właściwości

W przypadku zasobów najwyższego poziomu, takich jak potoki, zestawy danych i przepływy, właściwości wysokiego poziomu można edytować w okienku właściwości po prawej stronie kanwy. Okienko właściwości zawiera właściwości, takie jak nazwa, opis, adnotacje i inne właściwości wyższego poziomu. Podzasoby, takie jak działania potokowe i przekształcenia przepływu danych, są edytowane przy użyciu panelu w dolnej części kanwy. 

![Okienko właściwości](media/author-visually/properties-pane.png)

Okienko właściwości zostanie otwarte domyślnie podczas tworzenia zasobu. Aby go edytować, kliknij ikonę okienka właściwości znajdującą się w prawym górnym rogu kanwy.

### <a name="related-resources"></a>Powiązane zasoby

W okienku właściwości można zobaczyć, jakie zasoby są zależne od wybranego zasobu, wybierając kartę **pokrewne** . Wszystkie zasoby odwołujące się do bieżącego zasobu zostaną wyświetlone w tym miejscu.

![Powiązane zasoby](media/author-visually/related-resources.png)

Na przykład na powyższym obrazie jeden potok i dwa przepływy danych używają aktualnie wybranego zestawu danych.

## <a name="management-hub"></a>Centrum zarządzania

Centrum zarządzania, do którego uzyskuje się dostęp na karcie *Zarządzanie* w Azure Data Factory środowisku użytkownika, jest portalem, który hostuje globalne akcje zarządzania dla fabryki danych. W tym miejscu można zarządzać połączeniami z magazynami danych i obliczeniami zewnętrznymi, konfiguracją kontroli źródła i ustawieniami wyzwalacza. Aby uzyskać więcej informacji, zapoznaj się z możliwościami [centrum zarządzania](author-management-hub.md).

![Zarządzanie połączonymi usługami](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Wyrażenia i funkcje

Wyrażenia i funkcje mogą być używane zamiast wartości statycznych, aby określić wiele właściwości w Azure Data Factory.

Aby określić wyrażenie dla wartości właściwości, wybierz pozycję **Dodaj zawartość dynamiczną** , a następnie kliknij przycisk **Alt + P** , jednocześnie skupiając się na tym polu.

![Dodaj zawartość dynamiczną](media/author-visually/dynamic-content-1.png)

Spowoduje to otwarcie **konstruktora wyrażeń Data Factory** , w którym można tworzyć wyrażenia z obsługiwanych zmiennych systemowych, danych wyjściowych działań, funkcji i zmiennych lub parametrów określonych przez użytkownika. 

![Konstruktor wyrażeń](media/author-visually/dynamic-content-2.png)

Aby uzyskać informacje na temat języka wyrażeń, zobacz [Expressions and Functions in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Wyraź opinię

Wybierz **opinię** , aby skomentować informacje o funkcjach lub powiadomić firmę Microsoft o problemach z narzędziem:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz temat [monitorowanie potoków i zarządzanie nimi programowo](monitor-programmatically.md).
