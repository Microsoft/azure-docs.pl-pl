---
title: Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis
description: Dowiedz się, jak usunąć protokoły TLS 1,0 i 1,1 z aplikacji podczas komunikowania się z usługą Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545314"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Usuń protokoły TLS 1,0 i 1,1 z używania z usługą Azure cache for Redis

Na wyłączne korzystanie z Transport Layer Security (TLS) w wersji 1,2 lub nowszej jest na całym branżą. Protokoły TLS w wersji 1,0 i 1,1 są podatne na ataki, takie jak BEAST i POODLE, a także zawierają inne słabe luki w zabezpieczeniach i zagrożenia (CVE). Nie obsługują one również nowoczesnych metod szyfrowania i mechanizmów szyfrowania zalecanych przez standardy zgodności z kartą płatniczą (PCI). Ten [Blog dotyczący zabezpieczeń protokołu TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) wyjaśnia niektóre z tych luk w bardziej szczegółowy sposób.

W ramach tego wysiłku wprowadzamy następujące zmiany w usłudze Azure cache dla Redis:

* **Faza 1:** Skonfigurujemy domyślną minimalną wersję protokołu TLS do 1,2 dla nowo utworzonych wystąpień pamięci podręcznej (wcześniej była to protokół TLS 1,0). Istniejące wystąpienia pamięci podręcznej nie zostaną zaktualizowane w tym momencie. Można nadal używać Azure Portal lub innych interfejsów API zarządzania, aby [zmienić minimalną wersję protokołu TLS](cache-configure.md#access-ports) na 1,0 lub 1,1 w celu zapewnienia zgodności z poprzednimi wersjami, jeśli zachodzi taka potrzeba.
* **Faza 2:** Zatrzymamy obsługę protokołów TLS 1,1 i TLS 1,0. Po tej zmianie aplikacja musi korzystać z protokołu TLS 1,2 lub nowszego, aby komunikować się z pamięcią podręczną. Oczekuje się, że usługa Azure cache for Redis jest dostępna podczas migracji do obsługi tylko protokołu TLS 1,2 lub nowszego.

  > [!NOTE]
  > Faza 2 jest wstępnie zaplanowana do rozpoczęcia nie wcześniejszą niż 31 grudnia 2020. Zdecydowanie zaleca się jednak, aby od razu rozpocząć planowanie tej zmiany i zaktualizować klientów do obsługi protokołu TLS 1,2 lub nowszego. 
  >

W ramach tej zmiany zostanie również usunięta obsługa starszych zestawów szyfr, które nie są bezpieczne. Nasze obsługiwane pakiety szyfr zostaną ograniczone do następujących zestawów, gdy pamięć podręczna zostanie skonfigurowana z użyciem co najmniej protokołu TLS 1,2:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ten artykuł zawiera ogólne wskazówki dotyczące wykrywania zależności od wcześniejszych wersji protokołu TLS i usuwania ich z aplikacji.

Daty wprowadzenia tych zmian są następujące:

| Chmura                | Data rozpoczęcia fazy 1 | Data rozpoczęcia fazy 2         |
|----------------------|--------------------|----------------------------|
| Azure (globalny)       |  13 stycznia 2020  | Odroczone ze względu na COVID-19  |
| Azure Government     |  13 marca 2020    | Odroczone ze względu na COVID-19  |
| Azure (Niemcy)        |  13 marca 2020    | Odroczone ze względu na COVID-19  |
| Azure w Chinach — 21Vianet |  13 marca 2020    | Odroczone ze względu na COVID-19  |

> [!NOTE]
> Faza 2 jest wstępnie zaplanowana do rozpoczęcia nie wcześniejszą niż 31 grudnia 2020. Ten artykuł zostanie zaktualizowany po ustawieniu określonych dat.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Sprawdź, czy aplikacja jest już zgodna

Najprostszym sposobem, aby dowiedzieć się, czy aplikacja będzie działać z protokołem TLS 1,2, to ustawienie **minimalnej wartości wersji TLS** na TLS 1,2 w przypadku testu lub tymczasowej pamięci podręcznej, a następnie uruchom testy. Ustawienie **minimalnej wersji protokołu TLS** znajduje się w [ustawieniach zaawansowanych](cache-configure.md#advanced-settings) wystąpienia pamięci podręcznej w Azure Portal.  Jeśli aplikacja będzie działać zgodnie z oczekiwaniami po tej zmianie, prawdopodobnie jest zgodna. Może być konieczne skonfigurowanie biblioteki klienta Redis używanej przez aplikację w celu włączenia protokołu TLS 1,2 w celu nawiązania połączenia z usługą Azure cache for Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Konfigurowanie aplikacji do korzystania z protokołu TLS 1,2

Większość aplikacji korzysta z bibliotek klienckich Redis do obsługi komunikacji z pamięciami podręcznymi. Poniżej znajdują się instrukcje dotyczące konfigurowania niektórych popularnych bibliotek klientów w różnych językach programowania i strukturach w celu użycia protokołu TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Klienci platformy Redis .NET domyślnie używają najstarszej wersji protokołu TLS w .NET Framework 4.5.2 lub starszym i używają najnowszej wersji protokołu TLS na platformie .NET Framework 4,6 lub nowszej. Jeśli używasz starszej wersji .NET Framework, można ręcznie włączyć protokół TLS 1,2:

* **Stackexchange. Redis:** Ustaw `ssl=true` i `sslprotocols=tls12` w parametrach połączenia.
* **ServiceStack. Redis:** Postępuj zgodnie z instrukcjami dotyczącymi [ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) i wymagam co najmniej elementu ServiceStack. Redis v 5.6.

### <a name="net-core"></a>.NET Core

Redis klienci .NET Core domyślnie do domyślnej wersji protokołu TLS systemu operacyjnego, która oczywiście zależy od samego systemu operacyjnego. 

W zależności od wersji systemu operacyjnego i wszelkich poprawek, które zostały zastosowane, skuteczna domyślna wersja protokołu TLS może się różnić. Istnieje jedno źródło informacji o [tym, że jest to artykuł](/dotnet/framework/network-programming/tls#support-for-tls-12) dla systemu Windows. 

Jeśli jednak używasz starego systemu operacyjnego lub chcesz upewnić się, że zalecamy ręczne skonfigurowanie preferowanej wersji protokołu TLS za pośrednictwem klienta programu.


### <a name="java"></a>Java

Klienci Java Redis używają protokołu TLS 1,0 w wersji 6 lub starszej. Jedis, sałata i Redisson nie mogą połączyć się z pamięcią podręczną platformy Azure dla Redis, jeśli protokół TLS 1,0 jest wyłączony w pamięci podręcznej. Uaktualnij strukturę języka Java, aby korzystać z nowych wersji protokołu TLS.

W przypadku języka Java 7 klienci Redis domyślnie nie używają protokołu TLS 1,2, ale można go skonfigurować dla niego. Jedis pozwala określić podstawowe ustawienia protokołu TLS przy użyciu następującego fragmentu kodu:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Klienci sałaty i Redisson nie obsługują jeszcze określania wersji protokołu TLS, więc będą przerywane, jeśli pamięć podręczna akceptuje tylko połączenia TLS 1,2. Poprawki dla tych klientów są przeglądane, więc sprawdź te pakiety pod kątem zaktualizowanej wersji za pomocą tej obsługi.

W języku Java 8 protokół TLS 1,2 jest domyślnie używany i nie należy w większości przypadków wymagać aktualizacji konfiguracji klienta. Aby można było bezpiecznie, Przetestuj aplikację.

### <a name="nodejs"></a>Node.js

Węzeł Redis i IORedis domyślnie używają protokołu TLS 1,2.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Wersje wcześniejsze niż PHP 7: Predis obsługują tylko protokół TLS 1,0. Te wersje nie współpracują z protokołem TLS 1,2; należy przeprowadzić uaktualnienie, aby użyć protokołu TLS 1,2.
 
* PHP 7,0 do PHP 7.2.1: Predis domyślnie używa tylko protokołu TLS 1,0 lub 1,1. Aby użyć protokołu TLS 1,2, można użyć następującego obejścia. Podczas tworzenia wystąpienia klienta należy określić protokół TLS 1,2:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7,3 i nowsze wersje: Predis używa najnowszej wersji protokołu TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis nie obsługuje protokołu TLS w żadnej wersji języka PHP.

### <a name="python"></a>Python

Redis-PR domyślnie używa protokołu TLS 1,2.

### <a name="go"></a>JĘZYK GO

Redigo domyślnie używa protokołu TLS 1,2.

## <a name="additional-information"></a>Dodatkowe informacje

- [Jak skonfigurować usługę Azure cache for Redis](cache-configure.md)