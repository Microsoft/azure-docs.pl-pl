---
title: Interfejs API REST usługi Azure App Configuration — spójność
description: Strony referencyjne zapewniające spójność w czasie rzeczywistym za pomocą interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932629"
---
# <a name="real-time-consistency"></a>Spójność w czasie rzeczywistym

Ze względu na charakter niektórych systemów rozproszonych, spójność w czasie rzeczywistym między żądaniami jest trudna do wymuszenia niejawnie. Rozwiązaniem jest umożliwienie obsługi protokołu w postaci wielu tokenów synchronizacji. Tokeny synchronizacji są opcjonalne.

## <a name="initial-request"></a>Początkowe żądanie

Aby zagwarantować spójność w czasie rzeczywistym między różnymi wystąpieniami klientów i żądaniami, należy użyć opcjonalnych `Sync-Token` nagłówków żądań i odpowiedzi.

Składnia:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametr|Opis|
|--|--|
| `<id>` | Identyfikator tokenu (nieprzezroczysty) |
| `<value>` | Wartość tokenu (nieprzezroczysta). Zezwala na ciąg szyfrowany algorytmem Base64. |
| `<sn>` | Numer sekwencyjny tokenu (wersja). Wyższa oznacza nowszą wersję tego samego tokenu. Umożliwia lepsze buforowanie współbieżności i klienta. Klient może wybrać użycie tylko ostatniej wersji tokenu, ponieważ wersje tokenu są włącznie. Ten parametr nie jest wymagany w przypadku żądań. |

## <a name="response"></a>Reakcja

Usługa udostępnia `Sync-Token` Nagłówek z każdą odpowiedzią.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Kolejne żądania

Każde kolejne żądanie gwarantuje spójną odpowiedź w czasie rzeczywistym w odniesieniu do podanej `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Jeśli pominięto `Sync-Token` Nagłówek z żądania, istnieje możliwość, aby usługa mogła reagować z danymi buforowanymi przez krótki czas (do kilku sekund), zanim nastąpi wewnętrznie. Takie zachowanie może spowodować niespójne odczyty, jeśli zmiany pojawiły się bezpośrednio przed odczytem.

## <a name="multiple-sync-tokens"></a>Wiele tokenów synchronizacji

Serwer może odpowiedzieć na wiele tokenów synchronizacji dla pojedynczego żądania. Aby zachować spójność w czasie rzeczywistym dla następnego żądania, klient musi odpowiedzieć na wszystkie odebrane tokeny synchronizacji. Wiele wartości nagłówka musi być rozdzielonych przecinkami.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
