---
title: Udostępnianie HttpClient & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zapewnić własne HttpClient i serwer proxy do łączenia się z usługą Azure AD przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695057"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Udostępnianie własnych HttpClient i proxy za pomocą MSAL.NET
Podczas [inicjowania publicznej aplikacji klienckiej](msal-net-initializing-client-applications.md)można użyć programu `.WithHttpClientFactory method` , aby zapewnić własne HttpClient.  Udostępnienie własnych HttpClient umożliwia zaawansowane scenariusze, takie jak precyzyjne sterowanie serwerem proxy HTTP, Dostosowywanie nagłówków agentów użytkowników lub wymuszanie MSAL przy użyciu określonego HttpClient (na przykład w ASP.NET Core aplikacje/interfejsy API sieci Web).

## <a name="initialize-with-httpclientfactory"></a>Inicjowanie za pomocą HttpClientFactory
Poniższy przykład pokazuje, aby utworzyć `HttpClientFactory` a następnie zainicjować publiczną aplikację kliencką:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient i Xamarin iOS
W przypadku korzystania z platformy Xamarin iOS zaleca się utworzenie `HttpClient` , która jawnie używa `NSURLSession`programu obsługi systemu iOS 7 lub nowszego. MSAL.NET automatycznie tworzy `HttpClient` program, który `NSURLSessionHandler` używa dla systemu iOS 7 lub nowszego. Aby uzyskać więcej informacji, Przeczytaj [dokumentację platformy Xamarin dla systemu iOS dla HttpClient](/xamarin/cross-platform/macios/http-stack).