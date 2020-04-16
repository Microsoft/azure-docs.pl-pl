---
title: Kopiowanie lub klonowanie fabryki danych w usłudze Azure Data Factory
description: Dowiedz się, jak skopiować lub sklonować fabrykę danych w usłudze Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414106"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiowanie lub klonowanie fabryki danych w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób kopiowania lub klonowania fabryki danych w usłudze Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Przypadki użycia do klonowania fabryki danych

Oto niektóre z okoliczności, w których może okazać się przydatne skopiowanie lub sklonowanie fabryki danych:

-   **Zmiana nazwy zasobów**. Platforma Azure nie obsługuje zmiany nazwy zasobów. Jeśli chcesz zmienić nazwę fabryki danych, możesz sklonować fabrykę danych o innej nazwie, a następnie usunąć istniejącą.

-   **Debugowanie zmienia** się, gdy funkcje debugowania nie są wystarczające. Czasami, aby przetestować zmiany, możesz przetestować zmiany w innej fabryce przed zastosowaniem ich do głównej. W większości scenariuszy można użyć debugowania. Zmiany w wyzwalaczach, takie jak zachowanie zmian, gdy wyzwalacz jest wywoływany automatycznie lub przez przedział czasu, może nie być łatwo sprawdzalny bez zaewidencjonowania. W takich przypadkach klonowanie fabryki i stosowanie zmian ma wiele sensu. Ponieważ usługa Azure Data Factory pobiera opłaty głównie przez liczbę uruchomień, druga fabryka nie prowadzi do żadnych dodatkowych opłat.

## <a name="how-to-clone-a-data-factory"></a>Jak sklonować fabrykę danych

1. Interfejs użytkownika fabryki danych w witrynie Azure portal umożliwia eksportowanie całego ładunku fabryki danych do szablonu Menedżera zasobów wraz z plikiem parametrów, który umożliwia zmianę wszelkich wartości, które chcesz zmienić podczas klonowania fabryki.

1. Jako warunek wstępny należy utworzyć fabrykę danych docelowych z witryny Azure portal.

1. Jeśli masz SelfHosted IntegrationRuntime w fabryce źródła, należy wstępnie utworzyć go o tej samej nazwie w fabryce docelowej. Jeśli chcesz udostępnić identyfikatory SelfHosted między różnymi fabrykami, możesz użyć wzorca opublikowanego [tutaj](source-control.md#best-practices-for-git-integration).

1. Jeśli jesteś w trybie GIT, za każdym razem, gdy publikujesz z portalu, szablon Menedżera zasobów fabryki jest zapisywany w GIT w adf_publish gałęzi repozytorium.

1. W przypadku innych scenariuszy szablon Menedżera zasobów można pobrać, klikając przycisk **Szablon Menedżera zasobów eksportu** w portalu.

1. Po pobraniu szablonu Menedżera zasobów można go wdrożyć za pomocą standardowych metod wdrażania szablonów Menedżera zasobów.

1. Ze względów bezpieczeństwa wygenerowany szablon Menedżera zasobów nie zawiera żadnych tajnych informacji, takich jak hasła do połączonych usług. W związku z tym należy podać te hasła jako parametry wdrażania. Jeśli podanie parametrów nie jest pożądane, należy uzyskać parametry połączenia i hasła połączonych usług z usługi Azure Key Vault.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze wskazówkami dotyczącymi tworzenia fabryki danych w witrynie Azure portal w obszarze Tworzenie fabryki danych przy użyciu interfejsu [użytkownika usługi Azure Data Factory.](quickstart-create-data-factory-portal.md)
