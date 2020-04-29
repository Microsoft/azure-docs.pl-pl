---
title: Konfiguracja — IoT Edge Azure Event Grid | Microsoft Docs
description: Konfiguracja w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76846475"
---
# <a name="event-grid-configuration"></a>Konfiguracja Event Grid

Event Grid udostępnia wiele konfiguracji, które można modyfikować dla poszczególnych środowisk. Poniższa sekcja zawiera odwołania do wszystkich dostępnych opcji i ich wartości domyślnych.

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady ich użycia można znaleźć w [tym artykule](configure-api-protocol.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Zasady protokołu TLS modułu Event Grid. Wartość domyślna to tylko HTTPS.
|`inbound__serverAuth__serverCert__source`| Źródło certyfikatu serwera używane przez moduł Event Grid do konfiguracji protokołu TLS. Wartość domyślna to IoT Edge.

## <a name="incoming-client-authentication"></a>Przychodzące uwierzytelnianie klienta

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-client-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na certyfikatach. Wartość domyślna to true.
|`inbound__clientAuth__clientCert__source`| Źródło do sprawdzania poprawności certyfikatów klienta. Wartość domyślna to IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Zasady zezwalające na certyfikat klienta z podpisem własnym. Wartość domyślna to true.
|`inbound__clientAuth__sasKeys__enabled`| Aby włączyć/wyłączyć uwierzytelnianie klienta oparte na kluczu SAS. Wartość domyślna to off.
|`inbound__clientAuth__sasKeys__key1`| Jedna z wartości, aby zweryfikować przychodzące żądania.
|`inbound__clientAuth__sasKeys__key2`| Opcjonalna druga wartość do walidacji żądań przychodzących.

## <a name="outgoing-client-authentication"></a>Uwierzytelnianie klienta wychodzącego
Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-identity-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Aby włączyć/wyłączyć dołączanie certyfikatu tożsamości dla żądań wychodzących. Wartość domyślna to true.
|`outbound__clientAuth__clientCert__source`| Źródło pobierania certyfikatu wychodzącego modułu Event Grid. Wartość domyślna to IoT Edge.

## <a name="webhook-event-handlers"></a>Procedury obsługi zdarzeń elementu webhook

Aby uzyskać ogólne informacje na temat uwierzytelniania klientów, zobacz [zabezpieczenia i uwierzytelnianie](security-authentication.md). Przykłady można znaleźć w [tym artykule](configure-webhook-subscriber-auth.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Zasady umożliwiające kontrolowanie, czy dozwolone są tylko subskrybenci HTTPS. Wartość domyślna to true (tylko HTTPS).
|`outbound__webhook__skipServerCertValidation`| Oflaguj, aby kontrolować certyfikat subskrybenta. Wartość domyślna to true.
|`outbound__webhook__allowUnknownCA`| Zasady umożliwiające kontrolowanie, czy certyfikat z podpisem własnym może być prezentowany przez subskrybenta. Wartość domyślna to true. 

## <a name="delivery-and-retry"></a>Dostarczanie i ponawianie prób

Aby uzyskać ogólne informacje na temat tej funkcji, zobacz [dostarczanie i ponawianie próby](delivery-retry.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maksymalna liczba prób dostarczenia zdarzenia. Wartość domyślna to 30.
| `broker__defaultEventTimeToLiveInSeconds` | Czas wygaśnięcia (TTL) w sekundach, po upływie którego zdarzenie zostanie usunięte, jeśli nie zostanie dostarczone. Wartość domyślna to **7200** sekund

## <a name="output-batching"></a>Dzielenie na partie danych wyjściowych

Aby uzyskać ogólne informacje na temat tej funkcji, zobacz [dostarczanie i wyprowadzanie wsadowe](delivery-output-batching.md).

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maksymalna dozwolona wartość dla `ApproxBatchSizeInBytes` pokrętła. Wartość domyślna to `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maksymalna dozwolona wartość dla `MaxEventsPerBatch` pokrętła. Wartość domyślna to `50`.
| `broker__defaultMaxBatchSizeInBytes` | Maksymalny rozmiar żądania dostarczania, gdy `MaxEventsPerBatch` jest określony tylko. Wartość domyślna to `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maksymalna liczba zdarzeń do dodania do partii, gdy tylko `MaxBatchSizeInBytes` zostanie określony. Wartość domyślna to `10`.

## <a name="metrics"></a>Metryki

Aby dowiedzieć się więcej na temat używania metryk z Event Grid na IoT Edge, zobacz [temat monitorowanie tematów i subskrypcji](monitor-topics-subscriptions.md)

| Nazwa właściwości | Opis |
| ---------------- | ------------ |
| `metrics__reporterType` | Typ programu Reporter dla metryk Enpoint. Wartość domyślna `none` to i wyłącza metryki. Ustawienie umożliwiające `prometheus` włączenie metryk w formacie specyfikacji Prometheus.