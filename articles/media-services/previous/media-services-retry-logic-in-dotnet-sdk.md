---
title: Logika ponawiania w zestawie Media Services SDK dla platformy .NET | Microsoft Docs
description: Temat zawiera omówienie logiki ponawiania w zestawie Media Services SDK dla platformy .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: feda0ccfa1dc6d02153b98ad084bd775a055e9e3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012908"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika ponawiania w zestawie Media Services SDK dla platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Podczas pracy z usługami Microsoft Azureymi mogą wystąpić błędy przejściowe. Jeśli wystąpi błąd przejściowy, w większości przypadków po kilku ponownych próbach operacja zakończy się pomyślnie. Zestaw Media Services SDK dla platformy .NET implementuje logikę ponawiania w celu obsługi błędów przejściowych związanych z wyjątkami i błędami, które są spowodowane żądaniami sieci Web, wykonywaniem zapytań, zapisywaniem zmian i operacjami magazynu.  Domyślnie zestaw Media Services SDK dla programu .NET wykonuje cztery ponowne próby przed ponownym przesłaniem wyjątku do aplikacji. Kod w aplikacji musi następnie prawidłowo obsłużyć ten wyjątek.  

 Poniżej przedstawiono krótkie wytyczne dotyczące żądań sieci Web, magazynu, zapytań i zasad metody SaveChanges:  

* Zasady magazynu są używane na potrzeby operacji usługi BLOB Storage (przekazywanie lub pobieranie plików zasobów).  
* Zasady żądania sieci Web są używane w przypadku ogólnych żądań sieci Web (na przykład w celu uzyskania tokenu uwierzytelniania i rozpoznawania punktu końcowego klastra użytkowników).  
* Zasady zapytania służą do wykonywania zapytań dotyczących jednostek z REST (na przykład mediaContext. assets. WHERE (...)).  
* Zasady metody SaveChanges są używane do wykonywania wszystkich operacji, które zmieniają dane w ramach usługi (na przykład tworząc jednostkę aktualizującą jednostkę, wywołując funkcję usługi dla operacji).  
  
  W tym temacie wymieniono typy wyjątków i kody błędów, które są obsługiwane przez zestaw Media Services SDK dla logiki ponawiania prób programu .NET.  

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli opisano wyjątki, które są obsługiwane przez zestaw Media Services SDK dla platformy .NET lub nie są obsługiwane w przypadku niektórych operacji, które mogą spowodować błędy przejściowe.  

| Wyjątek | Żądanie sieci Web | Storage | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Aby uzyskać więcej informacji, zobacz sekcję [kody stanu WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) . |Tak |Tak |Tak |Tak |
| DataServiceClientException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceQueryException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceRequestException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceTransportException |Nie |Nie |Tak |Tak |
| TimeoutException |Tak |Tak |Tak |Nie |
| SocketException |Tak |Tak |Tak |Tak |
| Storageexception |Nie |Tak |Nie |Nie |
| IOException |Nie |Tak |Nie |Nie |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Kody stanu wyjątków WebException
W poniższej tabeli przedstawiono kody błędów usługi WebException, których logika ponawiania jest zaimplementowana. Wyliczenie [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) definiuje kody stanu.  

| Stan | Żądanie sieci Web | Storage | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Tak |Tak |Tak |Tak |
| NameResolutionFailure |Tak |Tak |Tak |Tak |
| ProxyNameResolutionFailure |Tak |Tak |Tak |Tak |
| SendFailure |Tak |Tak |Tak |Tak |
| PipelineFailure |Tak |Tak |Tak |Nie |
| ConnectionClosed |Tak |Tak |Tak |Nie |
| KeepAliveFailure |Tak |Tak |Tak |Nie |
| UnknownError |Tak |Tak |Tak |Nie |
| ReceiveFailure |Tak |Tak |Tak |Nie |
| RequestCanceled |Tak |Tak |Tak |Nie |
| Limit czasu |Tak |Tak |Tak |Nie |
| ProtocolError <br/>Ponowienie w ProtocolError jest kontrolowane przez obsługę kodu stanu HTTP. Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Tak |Tak |Tak |Tak |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Kody stanu błędu HTTP
Gdy zapytania i metody SaveChanges operacji zwracają DataServiceClientException, DataServiceQueryException lub DataServiceQueryException, kod stanu błędu HTTP jest zwracany we właściwości StatusCode.  W poniższej tabeli przedstawiono, w których kodach błędów jest implementowana logika ponowień.  

| Stan | Żądanie sieci Web | Storage | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Tak |Nie |Nie |
| 403 |Nie |Tak<br/>Obsługa ponawiania prób z dłuższymi czekami. |Nie |Nie |
| 408 |Tak |Tak |Tak |Tak |
| 429 |Tak |Tak |Tak |Tak |
| 500 |Tak |Tak |Tak |Nie |
| 502 |Tak |Tak |Tak |Nie |
| 503 |Tak |Tak |Tak |Tak |
| 504 |Tak |Tak |Tak |Nie |

Jeśli chcesz zapoznać się z rzeczywistą implementacją logiki usługi Media Services SDK dla platformy .NET, zobacz [Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
