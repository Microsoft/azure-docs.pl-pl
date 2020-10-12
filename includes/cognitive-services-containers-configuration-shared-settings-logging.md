---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68320513"
---
`Logging`Ustawienia zarządzają obsługą rejestrowania ASP.NET Core dla kontenera. Można użyć tych samych ustawień konfiguracji i wartości dla kontenera, który jest używany dla aplikacji ASP.NET Core. 

Kontener obsługuje następujących dostawców rejestrowania:

|Dostawca|Przeznaczenie|
|--|--|
|[Konsola](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|`Console`Dostawca rejestrowania ASP.NET Core. Obsługiwane są wszystkie ASP.NET Core ustawienia konfiguracji i domyślne wartości dla tego dostawcy rejestrowania.|
|[Rozpocząć](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|`Debug`Dostawca rejestrowania ASP.NET Core. Obsługiwane są wszystkie ASP.NET Core ustawienia konfiguracji i domyślne wartości dla tego dostawcy rejestrowania.|
|[Dysk](#disk-logging)|Dostawca rejestrowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika do instalacji wyjściowej.|

To polecenie kontenera przechowuje informacje rejestrowania w formacie JSON w instalacji wyjściowej:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

To polecenie kontenera wyświetla informacje o debugowaniu, poprzedzone prefiksem `dbug` , podczas gdy kontener jest uruchomiony:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Rejestrowanie dysków

`Disk`Dostawca rejestrowania obsługuje następujące ustawienia konfiguracji:

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Format` | Ciąg | Format danych wyjściowych dla plików dziennika.<br/> **Uwaga:** Ta wartość musi być ustawiona na, `json` Aby włączyć dostawcę rejestrowania. Jeśli ta wartość jest określona bez również określania instalacji wyjściowej podczas tworzenia wystąpienia kontenera, wystąpi błąd. |
| `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar pliku dziennika (w megabajtach (MB). Gdy rozmiar bieżącego pliku dziennika spełnia lub przekracza tę wartość, dostawca rejestrowania uruchamia nowy plik dziennika. Jeśli określono wartość-1, rozmiar pliku dziennika jest ograniczony tylko przez maksymalny rozmiar pliku (jeśli istnieje) dla instalacji wyjściowej. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania ASP.NET Core, zobacz [Konfiguracja pliku ustawień](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

