---
title: Udostępnianie HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zapewnić własne HttpClient i serwer proxy do łączenia się z usługą Azure AD przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064627"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Udostępnianie własnych HttpClient i proxy za pomocą MSAL.NET
Podczas [inicjowania publicznej aplikacji klienckiej](msal-net-initializing-client-applications.md)można użyć programu, `.WithHttpClientFactory method` Aby zapewnić własne HttpClient.  Udostępnienie własnych HttpClient umożliwia zaawansowane scenariusze, takie jak precyzyjne sterowanie serwerem proxy HTTP, Dostosowywanie nagłówków agentów użytkowników lub wymuszanie MSAL przy użyciu określonego HttpClient (na przykład w ASP.NET Core aplikacje/interfejsy API sieci Web).

## <a name="initialize-with-httpclientfactory"></a>Inicjowanie za pomocą HttpClientFactory
Poniższy przykład pokazuje, aby utworzyć `HttpClientFactory` a następnie zainicjować publiczną aplikację kliencką:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient i Xamarin iOS
W przypadku korzystania z platformy Xamarin iOS zaleca się utworzenie `HttpClient` , która jawnie używa `NSURLSession` programu obsługi systemu iOS 7 lub nowszego. MSAL.NET automatycznie tworzy `HttpClient` program, który używa `NSURLSessionHandler` dla systemu iOS 7 lub nowszego. Aby uzyskać więcej informacji, Przeczytaj [dokumentację platformy Xamarin dla systemu iOS dla HttpClient](/xamarin/cross-platform/macios/http-stack).