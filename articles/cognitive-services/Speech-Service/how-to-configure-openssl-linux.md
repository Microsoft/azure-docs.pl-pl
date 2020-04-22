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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683167"
---
# <a name="configure-openssl-for-linux"></a>Konfigurowanie biblioteki OpenSSL dla systemu Linux

W przypadku korzystania z dowolnej wersji SDK mowy przed 1.9.0, [OpenSSL](https://www.openssl.org) jest dynamicznie skonfigurowany do wersji systemu hosta. W nowszych wersjach SDK mowy OpenSSL (wersja [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)jest statycznie połączony z podstawową biblioteką SDK mowy.

Aby zapewnić łączność, sprawdź, czy certyfikaty OpenSSL zostały zainstalowane w systemie. Uruchom polecenie:
```bash
openssl version -d
```

Dane wyjściowe w systemach opartych na Ubuntu/Debianie powinny być następujące:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Sprawdź, czy `certs` w obszarze OPENSSLDIR nie ma podkatalogu. W powyższym przykładzie `/usr/lib/ssl/certs`będzie .

* Jeśli istnieje `/usr/lib/ssl/certs` i zawiera wiele pojedynczych `.crt` `.pem` plików certyfikatów (z lub rozszerzeniem), nie ma potrzeby dalszych działań.

* Jeśli OPENSSLDIR jest `/usr/lib/ssl` czymś innym niż i/lub zamiast wielu pojedynczych plików znajduje się plik pakietu pojedynczego certyfikatu, należy ustawić odpowiednią zmienną środowiskową SSL, aby wskazać, gdzie można znaleźć certyfikaty.

## <a name="examples"></a>Przykłady

- OPENSSLDIR `/opt/ssl`jest . Istnieje `certs` podkatalog `.crt` z `.pem` wieloma plikami lub plikami.
Ustaw zmienną `SSL_CERT_DIR` środowiskową, `/opt/ssl/certs` aby wskazać przed uruchomieniem programu, który używa zestawu SDK mowy. Przykład:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` jest (podobnie jak w systemach opartych na RHEL/CentOS). Istnieje `certs` podkatalog z plikiem pakietu `ca-bundle.crt`certyfikatów, na przykład .
Przed uruchomieniem programu korzystającego z zestawu SDK mowy należy ustawić zmienną `SSL_CERT_FILE` środowiskową, aby wskazywała ten plik. Przykład:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Warto również zauważyć, że niektóre dystrybucje Linuksa nie mają zdefiniowanej zmiennej środowiskowej TMP lub TMPDIR. Spowoduje to, że SDK mowy do pobrania listy odwołania certyfikatów (CRL) za każdym razem, a nie buforowanie listy CRL na dysku do ponownego użycia, dopóki nie wygasną. Aby poprawić wydajność połączenia [początkowego, można utworzyć zmienną środowiskową o nazwie TMPDIR i ustawić ją na ścieżce wybranego katalogu tymczasowego.](https://help.ubuntu.com/community/EnvironmentVariables).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)
