---
title: Logika ponawiania w zestawie Media Services SDK dla platformy .NET | Microsoft Docs
description: Temat zawiera omówienie logiki ponawiania w zestawie Media Services SDK dla platformy .NET.
author: Juliako
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 144db6a5ceaf56a35d3ce11dd54e1dfb4c97d7e3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264117"
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
| WebException<br/>Aby uzyskać więcej informacji, zobacz sekcję [kody stanu WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) . |Yes |Yes |Yes |Yes |
| DataServiceClientException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceQueryException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceRequestException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceTransportException |Nie |Nie |Yes |Yes |
| TimeoutException |Yes |Yes |Yes |Nie |
| SocketException |Yes |Yes |Yes |Yes |
| Storageexception |Nie |Yes |Nie |Nie |
| IOException |Nie |Yes |Nie |Nie |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Kody stanu wyjątków WebException
W poniższej tabeli przedstawiono kody błędów usługi WebException, których logika ponawiania jest zaimplementowana. Wyliczenie [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) definiuje kody stanu.  

| Stan | Żądanie sieci Web | Storage | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NameResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| SendFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |Nie |
| ConnectionClosed |Yes |Yes |Yes |Nie |
| KeepAliveFailure |Yes |Yes |Yes |Nie |
| UnknownError |Yes |Yes |Yes |Nie |
| ReceiveFailure |Yes |Yes |Yes |Nie |
| RequestCanceled |Yes |Yes |Yes |Nie |
| Limit czasu |Yes |Yes |Yes |Nie |
| ProtocolError <br/>Ponowienie w ProtocolError jest kontrolowane przez obsługę kodu stanu HTTP. Aby uzyskać więcej informacji, zobacz [kody stanu błędów HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Kody stanu błędu HTTP
Gdy zapytania i metody SaveChanges operacji zwracają DataServiceClientException, DataServiceQueryException lub DataServiceQueryException, kod stanu błędu HTTP jest zwracany we właściwości StatusCode.  W poniższej tabeli przedstawiono, w których kodach błędów jest implementowana logika ponowień.  

| Stan | Żądanie sieci Web | Storage | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Yes |Nie |Nie |
| 403 |Nie |Yes<br/>Obsługa ponawiania prób z dłuższymi czekami. |Nie |Nie |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |Nie |
| 502 |Yes |Yes |Yes |Nie |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |Nie |

Jeśli chcesz zapoznać się z rzeczywistą implementacją logiki usługi Media Services SDK dla platformy .NET, zobacz [Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
