---
title: Napisz kod do śledzenia żądań za pomocą usługi Azure Application Insights | Microsoft Docs
description: Napisz kod do śledzenia żądań za pomocą Application Insights, aby można było uzyskać profile dla swoich żądań.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930251"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napisz kod do śledzenia żądań za pomocą Application Insights

Aby wyświetlić profile dla aplikacji na stronie wydajność, usługa Azure Application Insights musi śledzić żądania dla aplikacji. Application Insights może automatycznie śledzić żądania dla aplikacji, które są oparte na strukturach, które są już Instrumentacją. Dwa przykłady to ASP.NET i ASP.NET Core. 

W przypadku innych aplikacji, takich jak role procesu roboczego platformy Azure Cloud Services i Service Fabric bezstanowych interfejsów API, należy napisać kod, aby poinformować Application Insights, gdzie Twoje żądania zaczynają się i kończą. Po napisaniu tego kodu dane telemetryczne są wysyłane do Application Insights. Możesz wyświetlić dane telemetryczne na stronie wydajność, a dla tych żądań są zbierane profile. 

Aby ręcznie śledzić żądania, wykonaj następujące czynności:

  1. Wcześniej w okresie istnienia aplikacji Dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat konfiguracji globalnego klucza instrumentacji, zobacz [używanie Service Fabric z Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Dla dowolnego fragmentu kodu, który ma być instrumentem, Dodaj do `StartOperation<RequestTelemetry>` niego instrukcję **using** , jak pokazano w poniższym przykładzie:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Wywoływanie `StartOperation<RequestTelemetry>` w innym `StartOperation<RequestTelemetry>` zakresie nie jest obsługiwane. `StartOperation<DependencyTelemetry>`Zamiast tego można użyć w zagnieżdżonym zakresie. Na przykład:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
