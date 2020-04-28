---
title: Praca z modułami Node. js
description: Dowiedz się, jak korzystać z modułów Node. js podczas korzystania z Azure App Service lub Cloud Services.
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
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70309272"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Using Node.js Modules with Azure applications (Używanie modułów Node.js z aplikacjami platformy Azure)
Ten dokument zawiera wskazówki dotyczące używania modułów Node. js z aplikacjami hostowanymi na platformie Azure. Zawiera on wskazówki dotyczące zapewnienia, że aplikacja korzysta z określonej wersji modułu, a także przy użyciu modułów macierzystych z platformą Azure.

Jeśli wiesz już, jak korzystać z modułów Node. js, **Package. JSON** i **npm-shrinkwrap. JSON** , następujące informacje zawierają krótkie podsumowanie dotyczące tego artykułu:

* Azure App Service rozumie pliki **Package. JSON** i **npm-shrinkwrap. JSON** oraz mogą instalować moduły na podstawie wpisów w tych plikach.

* Usługa Azure Cloud Services oczekuje, że wszystkie moduły zostaną zainstalowane w środowisku deweloperskim, a **katalog\_modułów węzła** , który ma zostać dołączony jako część pakietu wdrożeniowego. Można włączyć obsługę instalowania modułów przy użyciu plików **Package. JSON** lub **npm-shrinkwrap. JSON** na Cloud Services; Ta konfiguracja wymaga jednak dostosowania domyślnych skryptów używanych przez projekty usługi w chmurze. Aby zapoznać się z przykładem sposobu konfigurowania tego środowiska, zobacz [zadanie uruchamiania platformy Azure, aby uruchomić instalację npm, aby uniknąć wdrożenia modułów węzłów](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> W tym artykule nie omówiono Virtual Machines platformy Azure, ponieważ środowisko wdrażania na maszynie wirtualnej jest zależne od systemu operacyjnego hostowanego przez maszynę wirtualną.
> 
> 

## <a name="nodejs-modules"></a>Moduły Node. js
Moduły są załadowanymi pakietami języka JavaScript, które zapewniają konkretne funkcje aplikacji. Moduły są zwykle instalowane przy użyciu narzędzia wiersza polecenia **npm** , ale niektóre moduły (takie jak moduł http) są dostarczane jako część podstawowego pakietu Node. js.

Po zainstalowaniu modułów są one przechowywane w katalogu **modułów węzła\_** w katalogu głównym struktury katalogu aplikacji. Każdy moduł w katalogu **modułów\_węzła** zachowuje swój własny katalog, który zawiera moduły, od których jest zależna, a to zachowanie powtarza się dla każdego modułu, tak jak w przypadku łańcucha zależności. W tym środowisku każdy zainstalowany moduł ma własne wymagania dotyczące wersji dla modułów, od których zależy, jednak może to spowodować powstanie dużej struktury katalogów.

Wdrożenie katalogu **modułów\_węzła** w ramach aplikacji zwiększa rozmiar wdrożenia w porównaniu z użyciem pliku **Package. JSON** lub **npm-shrinkwrap. JSON** ; jednak gwarantuje to, że wersje modułów używanych w środowisku produkcyjnym są takie same jak moduły używane podczas opracowywania.

### <a name="native-modules"></a>Moduły macierzyste
Chociaż większość modułów ma zwykłe pliki JavaScript, niektóre moduły są obrazami binarnymi specyficznymi dla platformy. Te moduły są kompilowane w czasie instalacji, zazwyczaj przy użyciu języka Python i Node-GYP. Ponieważ platforma Azure Cloud Services korzysta z **folderu\_modułów węzła** wdrażanego w ramach aplikacji, każdy moduł macierzysty dołączony jako część zainstalowanych modułów powinien współpracować w usłudze w chmurze, o ile został zainstalowany i skompilowany w systemie Windows Development.

Azure App Service nie obsługuje wszystkich modułów macierzystych i może się nie powieść podczas kompilowania modułów z określonymi wymaganiami wstępnymi. Chociaż niektóre popularne moduły, takie jak MongoDB, mają opcjonalne zależności natywne i pracują bez nich, dwa obejścia zostały pomyślnie sprawdzone z niemal wszystkimi dostępnymi modułami macierzystymi:

* Uruchom **instalację npm** na komputerze z systemem Windows, na którym zainstalowano wszystkie wymagania wstępne modułu macierzystego. Następnie wdróż utworzony folder **modułów węzła\_** jako część aplikacji do Azure App Service.

  * Przed kompilacją upewnij się, że lokalna instalacja środowiska Node. js ma zgodną architekturę, a wersja jest możliwie najbliżej tej, która jest używana na platformie Azure (bieżące wartości można sprawdzić w środowisku uruchomieniowym z poziomu **procesu właściwości. Arch** i **Process. Version**).

* Azure App Service można skonfigurować do wykonywania niestandardowych skryptów bash lub powłoki podczas wdrażania, co daje możliwość wykonywania poleceń niestandardowych i precyzyjnego konfigurowania sposobu uruchamiania **instalacji npm** . Aby obejrzeć film wideo przedstawiający sposób konfigurowania tego środowiska, zobacz [niestandardowe skrypty wdrażania witryny sieci Web za pomocą kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Korzystanie z pliku Package. JSON

Plik **Package. JSON** jest sposobem na określenie zależności najwyższego poziomu wymaganych przez aplikację, aby platforma hostingu mogła zainstalować zależności, zamiast wymagać dołączenia folderu **modułów węzła\_** w ramach wdrożenia. Po wdrożeniu aplikacji polecenie **npm Install** służy do analizowania pliku **Package. JSON** i instalowania wszystkich wymienionych zależności.

Podczas programowania można użyć parametrów **--Save**, **--Save-dev**lub **--Save-Optional** podczas instalowania modułów, aby automatycznie dodać wpis do pliku **Package. JSON** . Aby uzyskać więcej informacji, zobacz [npm-Install](https://docs.npmjs.com/cli/install).

Jednym z potencjalnych problemów z plikiem **Package. JSON** jest określenie tylko wersji dla zależności najwyższego poziomu. Każdy zainstalowany moduł może lub nie może określić wersji modułów, od których jest zależna, i dlatego istnieje możliwość, że można utworzyć inny łańcuch zależności niż używany podczas opracowywania.

> [!NOTE]
> W przypadku wdrażania do Azure App Service, jeśli plik <b>Package. JSON</b> odwołuje się do modułu macierzystego, w przypadku publikowania aplikacji za pomocą narzędzia Git może zostać wyświetlony błąd podobny do poniższego:
> 
> npm błąd! module-name@0.6.0Zainstaluj: "Node-GYP Configure build"
> 
> npm błąd! polecenie "cmd"/c "" Node-GYP Configure build "" nie powiodło się z 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Korzystanie z pliku NPM-shrinkwrap. JSON
Plik **npm-shrinkwrap. JSON** jest próbą rozwiązania ograniczeń wersji modułu pliku **Package. JSON** . Plik **Package. JSON** zawiera tylko wersje modułów najwyższego poziomu, plik **npm-shrinkwrap. JSON** zawiera wymagania dotyczące wersji pełnego łańcucha zależności modułu.

Gdy aplikacja jest gotowa do produkcji, można zablokować wymagania dotyczące wersji i utworzyć plik **npm-shrinkwrap. JSON** przy użyciu polecenia **npm shrinkwrap** . To polecenie spowoduje użycie wersji aktualnie zainstalowanych w folderze **modułów węzła\_** i zapisanie tych wersji w pliku **npm-shrinkwrap. JSON** . Po wdrożeniu aplikacji w środowisku hostingu polecenie **npm Install** służy do analizowania pliku **npm-shrinkwrap. JSON** i instalowania wszystkich wymienionych zależności. Aby uzyskać więcej informacji, zobacz [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> W przypadku wdrażania do Azure App Service, jeśli plik <b>npm-shrinkwrap. JSON</b> odwołuje się do modułu macierzystego, po opublikowaniu aplikacji przy użyciu narzędzia Git może zostać wyświetlony błąd podobny do poniższego:
> 
> npm błąd! module-name@0.6.0Zainstaluj: "Node-GYP Configure build"
> 
> npm błąd! polecenie "cmd"/c "" Node-GYP Configure build "" nie powiodło się z 1
> 
> 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy rozumiesz, jak używać modułów Node. js z platformą Azure, Dowiedz się, jak [określić wersję środowiska Node. js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [skompilować i wdrożyć aplikację sieci Web w języku Node. js](app-service/app-service-web-get-started-nodejs.md)oraz [jak korzystać z interfejsu wiersza polecenia platformy Azure dla systemów Mac i Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
