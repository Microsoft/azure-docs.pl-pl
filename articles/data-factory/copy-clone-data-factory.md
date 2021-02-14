---
title: Kopiowanie lub klonowanie fabryki danych w Azure Data Factory
description: Dowiedz się, jak skopiować lub sklonować fabrykę danych w Azure Data Factory
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1ea16785502ce8a82087b79dd8e7f0014c0059fc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375242"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiowanie lub klonowanie fabryki danych w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób kopiowania lub klonowania fabryki danych w Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Przypadki użycia do klonowania fabryki danych

Poniżej przedstawiono niektóre sytuacje, w których warto przystąpić do kopiowania lub klonowania fabryki danych:

- **Przenieś Data Factory** do nowego regionu. Jeśli chcesz przenieść Data Factory do innego regionu, najlepszym sposobem jest utworzenie kopii w regionie wskazanym i usunięcie istniejącej.

- **Zmiana nazwy Data Factory**. Platforma Azure nie obsługuje zmieniania nazw zasobów. Jeśli chcesz zmienić nazwę fabryki danych, możesz sklonować fabrykę danych z inną nazwą i usunąć istniejącą.

- **Debugowanie zmian** , gdy funkcje debugowania nie są wystarczające. W większości scenariuszy można używać [debugowania](iterative-development-debugging.md). W innych przypadkach testowanie zmian w sklonowanym środowisku piaskownicy jest bardziej zrozumiałe. Na przykład, w jaki sposób sparametryzowane potoki ETL zachowywać się, gdy wyzwalacz zostanie wyzwolony po nadejściu pliku, a nie za pośrednictwem przedziału czasu wirowania, może nie być łatwo weryfikowalne za pośrednictwem samego debugowania. W takich przypadkach może być konieczne sklonowanie środowiska piaskownicy na potrzeby eksperymentowania. Ponieważ Azure Data Factory są naliczane przede wszystkim przez liczbę przebiegów, druga fabryka nie prowadzi do żadnych dodatkowych opłat.

## <a name="how-to-clone-a-data-factory"></a>Jak sklonować fabrykę danych

1. Jako warunek wstępny najpierw musisz utworzyć docelową fabrykę danych z Azure Portal.

1. Jeśli jesteś w trybie GIT:
    1. Za każdym razem, gdy publikujesz z portalu, szablon Menedżer zasobów fabryki jest zapisywany w usłudze GIT w \_ rozgałęzieniu publikowania ADF
    1. Podłącz nową fabrykę do tego _samego_ repozytorium i Kompiluj ją z \_ gałęzi publikowania ADF. Zasoby, takie jak potoki, zestawy danych i wyzwalacze, będą przenoszone przez

1. Jeśli jesteś w trybie na żywo:
    1. Interfejs użytkownika Data Factory umożliwia eksportowanie całego ładunku fabryki danych do pliku szablonu Menedżer zasobów i pliku parametrów. Dostęp do nich można uzyskać z przycisku szablonu usługi **ARM \ eksportuj Menedżer zasobów szablonu** w portalu.
    1. Możesz wprowadzić odpowiednie zmiany w pliku parametrów i zamienić nowe wartości dla nowej fabryki
    1. Następnie można ją wdrożyć za pomocą standardowych metod wdrażania szablonu Menedżer zasobów.

1. Jeśli w fabryki źródłowej znajduje się SelfHosted IntegrationRuntime, musisz utworzyć ją z tą samą nazwą w fabryce docelowej. Jeśli chcesz udostępnić SelfHosted Integration Runtime między różnymi fabrykami, możesz użyć wzorca opublikowanego w [tym miejscu](create-shared-self-hosted-integration-runtime-powershell.md) na potrzeby udostępniania SelfHosted IR.

1. Ze względów bezpieczeństwa wygenerowany szablon Menedżer zasobów nie będzie zawierał żadnych informacji tajnych, na przykład hasła dla połączonych usług. W związku z tym należy podać poświadczenia jako parametry wdrożenia. Jeśli ręczne wprowadzanie poświadczeń nie jest odpowiednie dla Twoich ustawień, należy rozważyć pobranie parametrów połączenia i haseł z Azure Key Vault zamiast tego. [Zobacz więcej](store-credentials-in-key-vault.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze wskazówkami dotyczącymi tworzenia fabryki danych w Azure Portal w temacie [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md).
