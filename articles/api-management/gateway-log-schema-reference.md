---
title: Odwołanie — dziennik zasobów usługi Azure API Management
description: Dokumentacja schematu dla dziennika zasobów usługi Azure API Management GatewayLogs
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: df0018e323bc0c5725c9752b25b77612f035c196
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582502"
---
# <a name="reference-api-management-resource-log-schema"></a>Dokumentacja: schemat dziennika zasobów API Management

Ten artykuł zawiera informacje o schemacie dotyczące dziennika zasobów usługi Azure API Management GatewayLogs. Wpisy dziennika zawierają także pola w [wspólnym schemacie najwyższego poziomu](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Aby włączyć zbieranie danych dziennika zasobów API Management, zobacz [monitorowanie opublikowanych interfejsów API](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>Schemat GatewayLogs

Następujące właściwości są rejestrowane dla każdego żądania interfejsu API.

| Właściwość  | Typ | Opis |
| ------------- | ------------- | ------------- |
| method | ciąg | Metoda HTTP żądania przychodzącego |
| url | ciąg | Adres URL żądania przychodzącego |
| responseCode | liczba całkowita | Kod stanu odpowiedzi HTTP wysłanej do klienta |
| responseSize | liczba całkowita | Liczba bajtów wysłanych do klienta podczas przetwarzania żądania | 
| cache | ciąg | Stan udziału w pamięci podręcznej API Management w przetwarzaniu żądania (trafienie, chybień, brak) | 
| apiId | ciąg | Identyfikator jednostki interfejsu API dla bieżącego żądania | 
| operationId | ciąg | Identyfikator jednostki operacji dla bieżącego żądania | 
| clientProtocol | ciąg | Wersja protokołu HTTP żądania przychodzącego |
| clientTime | liczba całkowita | Liczba milisekund spędzonych na ogólnej operacji we/wy klienta (łączenie, wysyłanie i otrzymywanie bajtów) | 
| apiRevision | ciąg | Poprawka interfejsu API dla bieżącego żądania | 
| clientTlsVersion| ciąg | Wersja protokołu TLS używana przez żądanie wysłania klienta |
| lastError | object | W przypadku niepowodzenia żądania szczegóły dotyczące ostatniego błędu przetwarzania żądania | 
| backendMethod | ciąg | Metoda HTTP żądania wysłanego do zaplecza |
| backendUrl | ciąg | Adres URL żądania wysłanego do zaplecza |
| backendResponseCode | liczba całkowita | Kod odpowiedzi HTTP odebranej z zaplecza |
| backedProtocol | ciąg | Wersja protokołu HTTP żądania wysłanego do zaplecza |
| backendTime | liczba całkowita | Liczba milisekund spędzonych na ogólnych operacjach we/wy zaplecza (łączenie, wysyłanie i otrzymywanie bajtów) | 


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania interfejsów API w API Management, zobacz [monitorowanie opublikowanych interfejsów API](api-management-howto-use-azure-monitor.md)
* Dowiedz się więcej [na temat wspólnych i specyficznych dla usługi schematów dla dzienników zasobów platformy Azure](../azure-monitor/essentials/resource-logs-schema.md)

