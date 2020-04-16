---
title: Tworzenie wizualne
description: Dowiedz się, jak używać tworzenia wizualizacji w usłudze Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418494"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Tworzenie wizualne w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Środowisko interfejsu użytkownika usługi Azure Data Factory (UX) umożliwia wizualne tworzenie i wdrażanie zasobów dla fabryki danych bez konieczności pisania kodu. Można przeciągać działania do kanwy potoku, wykonywać przebiegi testowe, debugować iteracyjnie oraz wdrażać i monitorować przebiegi potoku.

Obecnie środowisko użytkownika usługi Azure Data Factory UX jest obsługiwane tylko w przeglądarkach Microsoft Edge i Google Chrome.

## <a name="authoring-canvas"></a>Tworzenie kanwy

Aby otworzyć **kanwę tworzenia,** kliknij ikonę ołówka. 

![Kanwa do tworzenia](media/author-visually/authoring-canvas.png)

W tym miejscu zostaną autorstwa potoków, działań, zestawów danych, połączonych usług, przepływów danych, wyzwalaczy i środowiskach uruchomieniowych integracji, które składają się na fabrykę. Aby rozpocząć tworzenie potoku przy użyciu kanwy autora, zobacz [Kopiowanie danych przy użyciu działania kopiowania](tutorial-copy-data-portal.md). 

Domyślne środowisko tworzenia wizualnego jest bezpośrednio pracy z usługą data factory. Integracja z usługą Azure Repos Git lub GitHub jest również obsługiwana, aby umożliwić kontrolę źródła i współpracę w celu pracy nad potokami fabryki danych. Aby dowiedzieć się więcej o różnicach między tymi środowiskami tworzenia, zobacz [Kontrola źródła w usłudze Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Wyrażenia i funkcje

Wyrażenia i funkcje mogą służyć zamiast wartości statycznych, aby określić wiele właściwości w usłudze Azure Data Factory.

Aby określić wyrażenie dla wartości właściwości, wybierz pozycję **Dodaj zawartość dynamiczną** lub kliknij przycisk **Alt + P,** koncentrując się na polu.

![Dodawanie zawartości dynamicznej](media/author-visually/dynamic-content-1.png)

Spowoduje to otwarcie **Konstruktora wyrażeń fabryki danych,** w którym można tworzyć wyrażenia z obsługiwanych zmiennych systemowych, danych wyjściowych aktywności, funkcji oraz zmiennych lub parametrów określonych przez użytkownika. 

![Konstruktor wyrażeń](media/author-visually/dynamic-content-2.png)

Aby uzyskać informacje na temat języka wyrażeń, zobacz [Wyrażenia i funkcje w usłudze Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Przekazywanie opinii

Wybierz **opinię,** aby skomentować funkcje lub powiadomić firmę Microsoft o problemach z narzędziem:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz [Programowo monitorowanie potoków i zarządzanie nimi](monitor-programmatically.md).
