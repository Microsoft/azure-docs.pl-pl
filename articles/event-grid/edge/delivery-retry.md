---
title: Dostarczanie i ponawianie Azure Event Grid IoT Edge | Microsoft Docs
description: Dostarczenie i ponowienie próby Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171622"
---
# <a name="delivery-and-retry"></a>Dostarczanie i ponawianie prób

Event Grid zapewnia trwałe dostarczanie. Próbuje dostarczyć każdy komunikat co najmniej raz dla każdej pasującej subskrypcji. Jeśli punkt końcowy subskrybenta nie potwierdzi odbioru zdarzenia lub jeśli wystąpi błąd, Event Grid ponowienia próby w oparciu o ustalony **harmonogram ponownych prób** i **zasady ponawiania** prób.  Domyślnie moduł Event Grid dostarcza jedno zdarzenie naraz do subskrybenta. Ładunek jest jednak tablicą z pojedynczym zdarzeniem. Moduł może dostarczyć więcej niż jedno zdarzenie naraz, włączając funkcję tworzenia wsadowego danych wyjściowych. Aby uzyskać szczegółowe informacje na temat tej funkcji, zobacz Tworzenie [wsadowe danych wyjściowych](delivery-output-batching.md).  

> [!IMPORTANT]
>Brak obsługi trwałości dla danych zdarzenia. Oznacza to, że ponowne wdrożenie lub uruchomienie modułu Event Grid spowoduje utratę wszystkich zdarzeń, które nie zostały jeszcze dostarczone.

## <a name="retry-schedule"></a>Harmonogram ponownych prób

W przypadku odpowiedzi po dostarczeniu komunikatu Event Grid czekaj do 60 sekund. Jeśli punkt końcowy subskrybenta nie potwierdzą odpowiedzi, komunikat zostanie przesunięty do kolejki w jednej z kolejek wycofania dla kolejnych ponownych prób.

Istnieją dwie wstępnie skonfigurowane kolejki z przywróceniem z powrotem, które określają harmonogram, w którym zostanie podjęta próba ponowienia próby. Są to:

| Zaplanuj | Opis |
| ---------| ------------ |
| 1 minuta | Komunikaty kończące się w tym miejscu są podejmowane co minutę.
| 10 minut | Komunikaty kończące się w tym miejscu są podejmowane co 10 minut.

### <a name="how-it-works"></a>Jak to działa

1. Komunikat dociera do modułu Event Grid. Podjęto próbę dostarczenia go od razu.
1. Jeśli dostarczenie nie powiedzie się, komunikat zostanie przekolejkowane do kolejki 1-minutowej i ponowiony po minucie.
1. Jeśli dostarczenie nie powiedzie się, komunikat zostanie przetworzony do kolejki 10-minutowej i ponowiony co 10 minut.
1. Dostawy są wypróbowywane do momentu osiągnięcia pomyślnych limitów zasad lub ponowień.

## <a name="retry-policy-limits"></a>Limity zasad ponawiania

Istnieją dwie konfiguracje, które określają zasady ponawiania. Są to:

* Maksymalna liczba prób
* Czas wygaśnięcia zdarzenia (TTL)

Zdarzenie zostanie usunięte, jeśli zostanie osiągnięty jeden z limitów zasad ponawiania prób. Sam harmonogram ponownych prób został opisany w sekcji harmonogram ponownych prób. Konfigurację tych limitów można wykonać dla wszystkich subskrybentów lub subskrypcji. Poniższa sekcja zawiera szczegółowe informacje o każdej z nich.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurowanie wartości domyślnych dla wszystkich subskrybentów

Istnieją dwie właściwości: `brokers__defaultMaxDeliveryAttempts` i `broker__defaultEventTimeToLiveInSeconds` które można skonfigurować w ramach wdrożenia Event Grid, które sterują ponownymi próbami domyślnymi zasad dla wszystkich subskrybentów.

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maksymalna liczba prób dostarczenia zdarzenia. Wartość domyślna: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Czas wygaśnięcia zdarzenia w sekundach, po upływie którego zdarzenie zostanie usunięte, jeśli nie zostanie dostarczone. Wartość domyślna: **7200** s

## <a name="configuring-defaults-per-subscriber"></a>Konfigurowanie wartości domyślnych na subskrybenta

Możesz również określić limity zasad ponawiania dla każdej subskrypcji.
Zapoznaj się z naszą [dokumentacją interfejsu API](api.md) , aby uzyskać informacje na temat konfigurowania wartości domyślnych na subskrybencie. Ustawienia domyślne poziomu subskrypcji zastępują konfiguracje poziomu modułu.

## <a name="examples"></a>Przykłady

Poniższy przykład konfiguruje zasady ponawiania w module Event Grid za pomocą maxNumberOfAttempts = 3 i zdarzenia TTL o wartości 30 minut

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Poniższy przykład konfiguruje subskrypcję elementu webhook z maxNumberOfAttempts = 3 i czasem wygaśnięcia zdarzenia wynoszącym 30 minut

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
