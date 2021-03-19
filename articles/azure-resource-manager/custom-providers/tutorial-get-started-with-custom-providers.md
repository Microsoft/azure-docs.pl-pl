---
title: Tworzenie niestandardowych akcji i zasobów
description: W tym samouczku pokazano, jak utworzyć niestandardowe akcje i zasoby w Azure Resource Manager. Przedstawiono w nim również sposób współpracy niestandardowych przepływów pracy z szablonami Azure Resource Manager, interfejsem wiersza polecenia platformy Azure, Azure Policy i dziennikiem aktywności platformy Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75649897"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Tworzenie niestandardowych akcji i zasobów na platformie Azure

Dostawca niestandardowy to kontrakt między platformą Azure i punktem końcowym. Dostawcy niestandardowi mogą zmieniać przepływy pracy na platformie Azure, dodając nowe interfejsy API do Azure Resource Manager. Za pomocą tych niestandardowych interfejsów API Menedżer zasobów mogą korzystać z nowych możliwości wdrażania i zarządzania.

W tym samouczku przedstawiono prosty przykład dodawania nowych akcji i zasobów do platformy Azure oraz sposobu ich integrowania.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure

W pierwszej części tego samouczka opisano sposób konfigurowania aplikacji funkcji platformy Azure do pracy z dostawcami niestandardowymi:

- [Konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md)

Dostawcy niestandardowi mogą współpracować z dowolnym publicznym adresem URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Tworzenie punktu końcowego RESTful dla dostawców niestandardowych

W drugiej części tego samouczka opisano sposób tworzenia punktu końcowego RESTful dla dostawców niestandardowych:

- [Tworzenie punktu końcowego RESTful dla dostawców niestandardowych](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Tworzenie i używanie dostawcy niestandardowego

W trzeciej części tego samouczka opisano sposób tworzenia niestandardowego dostawcy i używania jego niestandardowych akcji i zasobów:

- [Tworzenie i używanie dostawcy niestandardowego](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje o dostawcach niestandardowych i sposobach ich kompilowania. Aby przejść do następnego samouczka, zobacz [Samouczek: konfigurowanie Azure Functions dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md).

Jeśli szukasz odwołań lub przewodnika Szybki Start, Oto kilka przydatnych linków:

- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
