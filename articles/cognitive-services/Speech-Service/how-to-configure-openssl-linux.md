---
title: Jak skonfigurować bibliotekę OpenSSL dla systemu Linux
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować OpenSSL dla systemu Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683167"
---
# <a name="configure-openssl-for-linux"></a>Konfigurowanie biblioteki OpenSSL dla systemu Linux

W przypadku korzystania z dowolnej wersji zestawu Speech SDK przed 1.9.0, [OpenSSL](https://www.openssl.org) jest konfigurowana dynamicznie z wersją systemu hosta. W nowszych wersjach zestawu Speech SDK OpenSSL (wersja [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) jest statycznie połączony z podstawową biblioteką zestawu Speech SDK.

Aby zapewnić łączność, sprawdź, czy certyfikaty OpenSSL zostały zainstalowane w systemie. Uruchom polecenie:
```bash
openssl version -d
```

Dane wyjściowe w systemach opartych na systemie Ubuntu/Debian powinny być następujące:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Sprawdź, czy w `certs` obszarze OPENSSLDIR znajduje się podkatalog. W powyższym przykładzie będzie to możliwe `/usr/lib/ssl/certs`.

* Jeśli istnieje `/usr/lib/ssl/certs` i zawiera wiele pojedynczych plików certyfikatów (z `.crt` lub `.pem` rozszerzeniem), nie ma potrzeby wykonywania dalszych czynności.

* Jeśli OPENSSLDIR jest coś innego niż `/usr/lib/ssl` i/lub istnieje pojedynczy plik pakietu certyfikatów zamiast wielu pojedynczych plików, należy ustawić odpowiednią zmienną środowiskową SSL, aby wskazać, gdzie można znaleźć certyfikaty.

## <a name="examples"></a>Przykłady

- OPENSSLDIR `/opt/ssl`. `certs` Istnieje podkatalog z wieloma `.crt` plikami lub `.pem` .
Ustaw zmienną `SSL_CERT_DIR` środowiskową, aby `/opt/ssl/certs` wskazywała przed uruchomieniem programu korzystającego z zestawu Speech SDK. Przykład:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR jest `/etc/pki/tls` (podobnie jak w systemach opartych na RHEL/CentOS). `certs` Istnieje podkatalog z plikiem pakietu certyfikatów, na przykład `ca-bundle.crt`.
Ustaw zmienną `SSL_CERT_FILE` środowiskową na ten plik przed uruchomieniem programu korzystającego z zestawu Speech SDK. Przykład:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Warto również zauważyć, że niektóre dystrybucje systemu Linux nie mają zdefiniowanej zmiennej środowiskowej TMP ani TMPDIR. Spowoduje to, że zestaw SDK usługi Speech pobiera listę odwołania certyfikatów (CRL) za każdym razem, zamiast buforowania listy CRL na dysk do ponownego użycia do momentu wygaśnięcia. Aby zwiększyć wydajność początkowego połączenia, można [utworzyć zmienną środowiskową o nazwie TMPDIR i ustawić ją na ścieżkę wybranego katalogu tymczasowego.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)
