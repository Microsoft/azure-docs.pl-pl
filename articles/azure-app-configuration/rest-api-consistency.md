---
title: Interfejs API REST usługi Azure App Configuration — spójność
description: Strony referencyjne zapewniające spójność w czasie rzeczywistym za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424344"
---
# <a name="real-time-consistency"></a>Spójność w czasie rzeczywistym

Ze względu na charakter niektórych systemów rozproszonych, spójność w czasie rzeczywistym między żądaniami jest trudna do wymuszenia niejawnie. Rozwiązaniem jest umożliwienie obsługi protokołu w postaci wielu **tokenów synchronizacji**. Tokeny synchronizacji są opcjonalne.

## <a name="initial-request"></a>Początkowe żądanie

W celu zagwarantowania spójności w czasie rzeczywistym między różnymi wystąpieniami klientów i żądaniami Użyj opcjonalnych `Sync-Token` nagłówków żądania/odpowiedzi.

Składnia:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametr|Opis|
|--|--|
| `<id>` | Identyfikator tokenu (nieprzezroczysty) |
| `<value>` | Wartość tokenu (nieprzezroczysta). Zezwala na ciąg szyfrowany algorytmem Base64 |
| `<sn>` | Numer sekwencyjny tokenu (wersja). Wyższa oznacza nowszą wersję tego samego tokenu. Umożliwia lepsze buforowanie współbieżności i klienta. Klient może wybrać użycie tylko ostatniej wersji tokenu, ponieważ wersje tokenu są włącznie. Niewymagane w przypadku żądań. |

## <a name="response"></a>Reakcja

Usługa udostępnia `Sync-Token` Nagłówek z każdą odpowiedzią.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Kolejne żądania

Każde kolejne żądanie gwarantuje spójną odpowiedź w czasie **rzeczywistym** w odniesieniu do podanej `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Jeśli `Sync-Token` nagłówek zostanie pominięty z żądania, wówczas usługa będzie mogła odpowiedzieć na dane z pamięci podręcznej w krótkim czasie (aż do kilku sekund), zanim nastąpi wewnętrznie. Takie zachowanie może spowodować niespójne odczyty, jeśli zmiany wystąpiły natychmiast przed przeczytaniem.

## <a name="multiple-sync-tokens"></a>Wiele tokenów synchronizacji

Serwer może odpowiedzieć na wiele tokenów synchronizacji dla pojedynczego żądania. Aby zachować spójność w **czasie rzeczywistym** dla następnego żądania, klient musi odpowiedzieć na wszystkie odebrane tokeny synchronizacji. Na RFC, wiele wartości nagłówka musi być rozdzielonych przecinkami.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
