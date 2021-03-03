---
title: Język Bicep dla szablonów Azure Resource Manager
description: Opisuje język Bicep na potrzeby wdrażania infrastruktury na platformie Azure za pomocą szablonów Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747190"
---
# <a name="what-is-bicep-preview"></a>Co to jest Bicep (wersja zapoznawcza)?

Bicep to język służący do deklaratywnego wdrażania zasobów platformy Azure. Upraszcza to proces tworzenia, zapewniając zwięzłą składnię i lepszą obsługę modularności i kodu. Bicep to język specyficzny dla domeny (DSL), który oznacza, że jest przeznaczony dla określonego scenariusza lub domeny. Bicep nie jest zamierzeniem ogólnego języka programowania do pisania aplikacji.

Bicep to przezroczyste streszczenie za pośrednictwem szablonów Azure Resource Manager (szablony ARM). Każdy plik Bicep kompiluje się do standardowego szablonu ARM. Typy zasobów, wersje interfejsu API i właściwości, które są prawidłowe w szablonie ARM, są prawidłowe w pliku Bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć od Bicep, [Zainstaluj narzędzia](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Po zainstalowaniu narzędzi Wypróbuj [samouczek Bicep](./bicep-tutorial-create-first-bicep.md). Seria samouczków przeprowadzi Cię przez strukturę i możliwości Bicep. Wdrażasz pliki Bicep i konwertujesz szablon ARM na odpowiedni plik Bicep.

Aby wyświetlić równoważne pliki JSON i Bicep obok siebie, zobacz [Bicep plac zabaw](https://aka.ms/bicepdemo).

Jeśli masz istniejący szablon ARM do przekonwertowania na Bicep, zobacz [dekompilowanie kodu JSON do Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Udoskonalenia Bicep

Bicep oferuje łatwiejszy i bardziej zwięzłą składnię w porównaniu z równoważnym formatem JSON. Nie można używać `[...]` wyrażeń. Zamiast tego można bezpośrednio wywoływać funkcje, pobierać wartości z parametrów i zmiennych oraz zasobów referencyjnych. Aby zapoznać się z pełnym porównaniem składni, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).

Bicep automatycznie zarządza zależnościami między zasobami. Można uniknąć ustawienia `dependsOn` , gdy Nazwa symboliczna zasobu jest używana w innej deklaracji zasobu.

Za pomocą Bicep można przerwać projekt w wielu modułach.

Struktura pliku Bicep jest bardziej elastyczna niż szablon JSON. Można zadeklarować parametry, zmienne i dane wyjściowe w dowolnym miejscu w pliku. W formacie JSON należy zadeklarować wszystkie parametry, zmienne i dane wyjściowe w odpowiednich sekcjach szablonu.

VS Code rozszerzenie Bicep oferuje bogatszą weryfikację i funkcję IntelliSense. Na przykład rozszerzenie ma funkcję IntelliSense do pobierania właściwości zasobu.

## <a name="faq"></a>Często zadawane pytania

**Dlaczego warto utworzyć nowy język zamiast korzystać z istniejącego?**

Możesz traktować Bicep jako poprawkę do istniejącego języka szablonów ARM, a nie nowego języka. Składnia została zmieniona, ale podstawowe funkcje i środowisko uruchomieniowe pozostają takie same.

Przed rozpoczęciem opracowywania Bicep uważamy, że korzystasz z istniejącego języka programowania. Mamy zdecydowaną, że docelowi odbiorcy będą łatwiej poznać Bicep zamiast zacząć pracę z innym językiem.

**Dlaczego nie należy skoncentrować się na Terraform lub innej infrastrukturze innej firmy jako oferty kodu?**

Różni użytkownicy preferują różne języki i narzędzia konfiguracyjne. Chcemy upewnić się, że wszystkie te narzędzia zapewniają doskonałe środowisko na platformie Azure. Bicep jest częścią tego nakładu pracy.

Jeśli jesteś zadowolony z używania Terraform, nie ma powodów do przełączenia. Firma Microsoft dokłada starań, aby upewnić się, że Terraform na platformie Azure są najlepszym rozwiązaniem.

W przypadku klientów, którzy mają wybrane szablony ARM, firma Microsoft uważa, że Bicep ulepsza proces tworzenia. Bicep również pomaga w przejściu klientów, którzy nie przyjęli infrastruktury jako kodu.

**Czy jest Bicep tylko na platformie Azure?**

Bicep to DSL ukierunkowana na wdrażanie kompletnych rozwiązań na platformie Azure. Spełnienie tego celu wymaga pracy z niektórymi interfejsami API, które znajdują się poza platformą Azure. Oczekujemy, że dla tych scenariuszy są używane punkty rozszerzalności.

**Co się stanie z moimi istniejącymi szablonami ARM?**

Nadal działają tak samo, jak zawsze. Nie musisz wprowadzać żadnych zmian. Będziemy nadal obsługiwać podstawowy język JSON szablonu ARM. Pliki Bicep kompilują do formatu JSON, a ten kod JSON jest wysyłany do platformy Azure w celu wdrożenia.

Gdy wszystko będzie gotowe, można [przekonwertować pliki JSON na Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z [samouczka Bicep](./bicep-tutorial-create-first-bicep.md).
