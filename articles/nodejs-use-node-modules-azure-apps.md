---
title: Praca z modułami Node.js
description: Dowiedz się, jak współpracować z modułami Node.js przy użyciu Azure App Service lub Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 6c1bbe48ca5205cf1db49d67a711e9a7523e1845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88077122"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)
Ten dokument zawiera wskazówki dotyczące korzystania z modułów Node.js w aplikacjach hostowanych na platformie Azure. Zawiera on wskazówki dotyczące zapewnienia, że aplikacja korzysta z określonej wersji modułu, a także przy użyciu modułów macierzystych z platformą Azure.

Jeśli masz już doświadczenie w korzystaniu z modułów Node.js, **package.json** i **npm-shrinkwrap.jsdla** plików, następujące informacje zawierają krótkie podsumowanie zagadnień opisanych w tym artykule:

* Azure App Service rozumie **package.js** i **npm-shrinkwrap.jsdla** plików i może instalować moduły na podstawie wpisów w tych plikach.

* Usługa Azure Cloud Services oczekuje, że wszystkie moduły zostaną zainstalowane w środowisku deweloperskim, a **katalog \_ modułów węzła** , który ma zostać dołączony jako część pakietu wdrożeniowego. Można włączyć obsługę instalowania modułów przy użyciu **package.jsna** lub **npm-shrinkwrap.js** plików na Cloud Services; Ta konfiguracja wymaga jednak dostosowania domyślnych skryptów używanych przez projekty usługi w chmurze. Aby zapoznać się z przykładem sposobu konfigurowania tego środowiska, zobacz [zadanie uruchamiania platformy Azure, aby uruchomić instalację npm, aby uniknąć wdrożenia modułów węzłów](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> W tym artykule nie omówiono Virtual Machines platformy Azure, ponieważ środowisko wdrażania na maszynie wirtualnej jest zależne od systemu operacyjnego hostowanego przez maszynę wirtualną.
>
>

## <a name="nodejs-modules"></a>Moduły Node.js
Moduły są załadowanymi pakietami języka JavaScript, które zapewniają konkretne funkcje aplikacji. Moduły są zwykle instalowane przy użyciu narzędzia wiersza polecenia **npm** , ale niektóre moduły (takie jak moduł http) są dostarczane jako część pakietu podstawowego Node.js.

Po zainstalowaniu modułów są one przechowywane w katalogu ** \_ modułów węzła** w katalogu głównym struktury katalogu aplikacji. Każdy moduł w katalogu ** \_ modułów węzła** zachowuje swój własny katalog, który zawiera moduły, od których jest zależna, a to zachowanie powtarza się dla każdego modułu, tak jak w przypadku łańcucha zależności. W tym środowisku każdy zainstalowany moduł ma własne wymagania dotyczące wersji dla modułów, od których zależy, jednak może to spowodować powstanie dużej struktury katalogów.

Wdrożenie katalogu ** \_ modułów węzła** w ramach aplikacji zwiększa rozmiar wdrożenia w porównaniu z użyciem **package.js** lub **npm-shrinkwrap.jsna** pliku; jednak gwarantuje to, że wersje modułów używanych w środowisku produkcyjnym są takie same jak moduły używane podczas opracowywania.

### <a name="native-modules"></a>Moduły macierzyste
Chociaż większość modułów ma zwykłe pliki JavaScript, niektóre moduły są obrazami binarnymi specyficznymi dla platformy. Te moduły są kompilowane w czasie instalacji, zazwyczaj przy użyciu języka Python i Node-GYP. Ponieważ platforma Azure Cloud Services korzysta z **folderu \_ modułów węzła** wdrażanego w ramach aplikacji, każdy moduł macierzysty dołączony jako część zainstalowanych modułów powinien współpracować w usłudze w chmurze, o ile został zainstalowany i skompilowany w systemie Windows Development.

Azure App Service nie obsługuje wszystkich modułów macierzystych i może się nie powieść podczas kompilowania modułów z określonymi wymaganiami wstępnymi. Chociaż niektóre popularne moduły, takie jak MongoDB, mają opcjonalne zależności natywne i pracują bez nich, dwa obejścia zostały pomyślnie sprawdzone z niemal wszystkimi dostępnymi modułami macierzystymi:

* Uruchom **instalację npm** na komputerze z systemem Windows, na którym zainstalowano wszystkie wymagania wstępne modułu macierzystego. Następnie wdróż utworzony folder ** \_ modułów węzła** jako część aplikacji do Azure App Service.

  * Przed kompilacją Sprawdź, czy lokalna instalacja Node.js ma zgodną architekturę, a wersja jest możliwie jak najbliżej tej, która jest używana na platformie Azure (bieżące wartości można sprawdzić w środowisku uruchomieniowym z poziomu właściwości **Process. Arch** i **Process. Version**).

* Azure App Service można skonfigurować do wykonywania niestandardowych skryptów bash lub powłoki podczas wdrażania, co daje możliwość wykonywania poleceń niestandardowych i precyzyjnego konfigurowania sposobu uruchamiania **instalacji npm** . Aby obejrzeć film wideo przedstawiający sposób konfigurowania tego środowiska, zobacz [niestandardowe skrypty wdrażania witryny sieci Web za pomocą kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Używanie package.jsw pliku

**package.jsw** pliku to sposób, aby określić zależności najwyższego poziomu wymagane przez aplikację, aby platforma hostingu mogła zainstalować zależności, zamiast wymagać dołączenia folderu ** \_ modułów węzłów** w ramach wdrożenia. Po wdrożeniu aplikacji polecenie **npm Install** służy do analizowania **package.jsw** pliku i instalowania wszystkich wymienionych zależności.

Podczas programowania można użyć parametrów **--Save**, **--Save-dev**lub **--Save-Optional** w przypadku instalowania modułów, aby automatycznie dodać wpis dla modułu do **package.jsna** pliku. Aby uzyskać więcej informacji, zobacz [npm-Install](https://docs.npmjs.com/cli/install).

Jednym z potencjalnych problemów z **package.jsw** pliku jest określenie tylko wersji dla zależności najwyższego poziomu. Każdy zainstalowany moduł może lub nie może określić wersji modułów, od których jest zależna, i dlatego istnieje możliwość, że można utworzyć inny łańcuch zależności niż używany podczas opracowywania.

> [!NOTE]
> W przypadku wdrażania do Azure App Service, jeśli <b>package.jsna</b> pliku odwołuje się do modułu macierzystego, w przypadku publikowania aplikacji za pomocą narzędzia Git może zostać wyświetlony błąd podobny do poniższego:
>
> npm błąd! module-name@0.6.0 Zainstaluj: "Node-GYP Configure build"
>
> npm błąd! polecenie "cmd"/c "" Node-GYP Configure build "" nie powiodło się z 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Używanie npm-shrinkwrap.jsw pliku
**npm-shrinkwrap.jsw** pliku to próba rozwiązania ograniczeń wersji modułu **package.js** pliku. Gdy **package.jsw** pliku zawiera tylko wersje modułów najwyższego poziomu, **npm-shrinkwrap.jsna** pliku zawiera wymagania dotyczące wersji pełnego łańcucha zależności modułu.

Gdy aplikacja jest gotowa do produkcji, można zablokować wymagania dotyczące wersji i utworzyć **npm-shrinkwrap.jsw** pliku przy użyciu polecenia **npm shrinkwrap** . To polecenie spowoduje użycie wersji aktualnie zainstalowanych w folderze ** \_ modułów węzła** i zapisanie tych wersji w **npm-shrinkwrap.js** pliku. Po wdrożeniu aplikacji w środowisku hostingu polecenie **npm Install** służy do analizowania **npm-shrinkwrap.jsw** pliku i instalowania wszystkich wymienionych zależności. Aby uzyskać więcej informacji, zobacz [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> W przypadku wdrażania do Azure App Service, jeśli <b>npm-shrinkwrap.jsna</b> pliku odwołuje się do modułu macierzystego, w przypadku publikowania aplikacji za pomocą narzędzia Git może zostać wyświetlony błąd podobny do poniższego:
>
> npm błąd! module-name@0.6.0 Zainstaluj: "Node-GYP Configure build"
>
> npm błąd! polecenie "cmd"/c "" Node-GYP Configure build "" nie powiodło się z 1
>
>

## <a name="next-steps"></a>Następne kroki
Teraz, gdy rozumiesz, jak używać modułów Node.js z platformą Azure, Dowiedz się, jak [określić wersję Node.js](https://github.com/squillace/nodejs-microservice), [skompilować i wdrożyć aplikację internetową Node.js](app-service/quickstart-nodejs.md)oraz [jak korzystać z interfejsu Command-Line platformy Azure dla systemów Mac i Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/azure/developer/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
