---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729880"
---
`Logging`Ustawienia zarządzają obsługą rejestrowania ASP.NET Core dla kontenera. Można użyć tych samych ustawień konfiguracji i wartości dla kontenera, który jest używany dla aplikacji ASP.NET Core. 

Kontener obsługuje następujących dostawców rejestrowania:

|Dostawca|Przeznaczenie|
|--|--|
|[Konsola](/aspnet/core/fundamentals/logging/#console-provider)|`Console`Dostawca rejestrowania ASP.NET Core. Obsługiwane są wszystkie ASP.NET Core ustawienia konfiguracji i domyślne wartości dla tego dostawcy rejestrowania.|
|[Debugowanie](/aspnet/core/fundamentals/logging/#debug-provider)|`Debug`Dostawca rejestrowania ASP.NET Core. Obsługiwane są wszystkie ASP.NET Core ustawienia konfiguracji i domyślne wartości dla tego dostawcy rejestrowania.|
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

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania ASP.NET Core, zobacz [Konfiguracja pliku ustawień](/aspnet/core/fundamentals/logging/).