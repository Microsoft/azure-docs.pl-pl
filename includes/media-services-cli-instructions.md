---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555698"
---
## <a name="use-cli-shell"></a>Korzystanie z powłoki interfejsu wiersza polecenia

Zalecane jest używanie [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest) do wykonywania poleceń interfejsu wiersza polecenia. **Cloud Shell** to bezpłatna, interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Oferuje ona elastyczny wybór środowiska powłoki, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Użytkownicy systemu Linux mogą wybrać środowisko powłoki Bash, a użytkownicy systemu Windows — program PowerShell.

Interfejs wiersza polecenia można także zainstalować lokalnie. Instrukcje dotyczące platformy można znaleźć w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) .

### <a name="sign-in"></a>Zaloguj

Przy użyciu lokalnej instalacji interfejsu wiersza polecenia wymagane jest zalogowanie się do platformy Azure. Ten krok nie jest wymagany w przypadku Azure Cloud Shell. Zaloguj się przy użyciu `az login` polecenia.

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć stronę przeglądarki i postępować zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do https://aka.ms/devicelogin przeglądarki w przeglądarce.

### <a name="specify-location-of-files"></a>Określ lokalizację plików

Wiele poleceń interfejsu wiersza polecenia Media Services pozwala przekazać parametr z nazwą pliku. Jeśli używasz **Cloud Shell**, możesz przekazać plik do CloudDrive (przy użyciu programu bash lub PowerShell). 

![Przekazywanie plików]

Niezależnie od tego, czy używasz lokalnego interfejsu wiersza polecenia, czy **Cloud Shell**, musisz określić ścieżkę pliku zgodnie z systemem operacyjnym lub Cloud Shell (bash lub PowerShell), którego używasz. Poniżej przedstawiono kilka przykładów:

Ścieżka względna do pliku (wszystkie systemy operacyjne)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Ścieżka bezwzględna do pliku w systemie Linux/Mac i systemie operacyjnym Windows

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Użyj `{file}` , jeśli polecenie prosi o ścieżkę do pliku. Na przykład `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Użyj `@{file}` , jeśli polecenie ma załadować określony plik. Na przykład `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Przekazywanie plików]: ./media/media-services-cli/upload-download-files.png