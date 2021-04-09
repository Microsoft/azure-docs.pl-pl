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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577448"
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

Sprawdź, czy `certs` w obszarze OPENSSLDIR znajduje się podkatalog. W powyższym przykładzie będzie to możliwe `/usr/lib/ssl/certs` .

* Jeśli istnieje `/usr/lib/ssl/certs` i zawiera wiele pojedynczych plików certyfikatów (z `.crt` lub `.pem` rozszerzeniem), nie ma potrzeby wykonywania dalszych czynności.

* Jeśli OPENSSLDIR jest coś innego niż `/usr/lib/ssl` i/lub istnieje pojedynczy plik pakietu certyfikatów zamiast wielu pojedynczych plików, należy ustawić odpowiednią zmienną środowiskową SSL, aby wskazać, gdzie można znaleźć certyfikaty.

## <a name="examples"></a>Przykłady

- OPENSSLDIR `/opt/ssl` . Istnieje `certs` podkatalog z wieloma `.crt` `.pem` plikami lub.
Ustaw zmienną środowiskową `SSL_CERT_DIR` , aby wskazywała `/opt/ssl/certs` przed uruchomieniem programu korzystającego z zestawu Speech SDK. Na przykład:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR jest `/etc/pki/tls` (podobnie jak w systemach opartych na RHEL/CentOS). Istnieje `certs` podkatalog z plikiem pakietu certyfikatów, na przykład `ca-bundle.crt` .
Ustaw zmienną środowiskową `SSL_CERT_FILE` na ten plik przed uruchomieniem programu korzystającego z zestawu Speech SDK. Na przykład:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Sprawdzanie odwołania certyfikatów
W przypadku nawiązywania połączenia z usługą Speech SDK aparat rozpoznawania mowy sprawdzi, czy certyfikat TLS używany przez usługę mowy nie został odwołany. Aby przeprowadzić to sprawdzenie, zestaw mowy SDK będzie potrzebować dostępu do punktów dystrybucji listy CRL dla urzędów certyfikacji używanych przez platformę Azure. Listę możliwych lokalizacji pobierania listy CRL można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes). Jeśli certyfikat został odwołany lub lista CRL nie może zostać pobrana, zestaw Speech SDK przerywa połączenie i zgłosi zdarzenie anulowane.

W przypadku, gdy sieć, w której jest używany zestaw Speech SDK, jest skonfigurowana w sposób, który nie zezwala na dostęp do lokalizacji pobierania listy CRL, sprawdzanie listy CRL można wyłączyć lub ustawić jako niepowodzenie, jeśli nie można pobrać listy CRL. Ta konfiguracja odbywa się za pomocą obiektu konfiguracji użytego do utworzenia obiektu aparatu rozpoznawania.

Aby kontynuować połączenie, gdy nie można pobrać listy CRL, ustaw właściwość OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Po ustawieniu na wartość "true" zostanie podjęta próba pobrania listy CRL, a jeśli pobieranie zakończyło się pomyślnie, certyfikat zostanie sprawdzony pod kątem odwołania, jeśli pobieranie nie powiedzie się, połączenie będzie mogło być kontynuowane.

Aby całkowicie wyłączyć sprawdzanie odwołań certyfikatów, ustaw właściwość OPENSSL_DISABLE_CRL_CHECK na wartość "true".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Warto również zauważyć, że niektóre dystrybucje systemu Linux nie mają zdefiniowanej zmiennej środowiskowej TMP ani TMPDIR. Spowoduje to, że zestaw SDK usługi Speech pobiera listę odwołania certyfikatów (CRL) za każdym razem, zamiast buforowania listy CRL na dysk do ponownego użycia do momentu wygaśnięcia. Aby zwiększyć wydajność początkowego połączenia, można [utworzyć zmienną środowiskową o nazwie TMPDIR i ustawić ją na ścieżkę wybranego katalogu tymczasowego.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)
