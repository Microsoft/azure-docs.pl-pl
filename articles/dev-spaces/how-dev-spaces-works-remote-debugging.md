---
title: Jak zdalne debugowanie kodu za pomocą Azure Dev Spaces działa
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Zawiera opis procesów zdalnego debugowania w usłudze Azure Kubernetes Service przy użyciu Azure Dev Spaces
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975046"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Jak zdalne debugowanie kodu za pomocą Azure Dev Spaces działa

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem w klastrze usługi Azure Kubernetes Service (AKS). Gdy projekt zostanie uruchomiony w obszarze deweloperskim, Azure Dev Spaces zapewnia sposób dołączania i debugowania uruchomionej aplikacji w AKS.

W tym artykule opisano sposób, w jaki działa zdalne debugowanie z funkcją dev Spaces.

## <a name="debug-your-code"></a>Debugowanie kodu

W przypadku aplikacji Java, .NET Core i Node.js można debugować aplikację uruchamianą bezpośrednio w obszarze deweloperskim przy użyciu Visual Studio Code lub Visual Studio. Visual Studio Code i program Visual Studio udostępniają narzędzia do łączenia się z obszarem deweloperskim, uruchamiania aplikacji i dołączania debugera. Po uruchomieniu `azds prep` można otworzyć projekt w Visual Studio Code lub Visual Studio. Visual Studio Code lub program Visual Studio wygeneruje własne pliki konfiguracji służące do nawiązywania połączeń, które nie są uruchamiane `azds prep` . Z poziomu Visual Studio Code lub programu Visual Studio można ustawić punkty przerwania i uruchomić aplikację w obszarze dev.

![Debugowanie kodu](media/get-started-node/debug-configuration-nodejs2.png)

Gdy uruchamiasz aplikację przy użyciu Visual Studio Code lub programu Visual Studio do debugowania, obsługują one uruchamianie i łączenie się z miejscem deweloperskim w taki sam sposób jak w przypadku uruchamiania `azds up` . Ponadto narzędzia po stronie klienta w Visual Studio Code i Visual Studio udostępniają dodatkowy parametr z określonymi informacjami na potrzeby debugowania. Parametr zawiera nazwę obrazu debugera, lokalizację debugera w obrazie debugera i lokalizację docelową w kontenerze aplikacji do zainstalowania folderu debugera.

Obraz debugera jest automatycznie ustalany przez narzędzia po stronie klienta. Używa metody podobnej do używanej podczas pliku dockerfile i wykresu Helm generowanego podczas uruchamiania `azds prep` . Gdy debuger zostanie zainstalowany w obrazie aplikacji, jest uruchamiany za pomocą polecenia `azds exec` .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](how-dev-spaces-works.md)
