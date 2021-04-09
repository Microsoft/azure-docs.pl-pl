---
title: Tworzenie niestandardowych stron błędów Application Gateway platformy Azure
description: W tym artykule pokazano, jak utworzyć Application Gateway niestandardowe strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592724"
---
# <a name="create-application-gateway-custom-error-pages"></a>Tworzenie niestandardowych stron błędów Application Gateway

Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.

Na przykład możesz zdefiniować własną stronę obsługi, jeśli aplikacja sieci Web jest niedostępna. Lub można utworzyć nieautoryzowaną stronę dostępu, jeśli złośliwe żądanie jest wysyłane do aplikacji sieci Web.

Niestandardowe strony błędów są obsługiwane w następujących dwóch scenariuszach:

- **Strona obsługi** — Ta niestandardowa strona błędu jest wysyłana zamiast nieprawidłowej strony bramy 502. Jest on wyświetlany, gdy Application Gateway nie ma wewnętrznej bazy danych, do której należy kierować ruchem. Na przykład w przypadku zaplanowanej konserwacji lub nieprzewidzianego problemu dostęp do puli zaplecza.
- **Strona nieautoryzowanego dostępu** — Ta niestandardowa strona błędu jest wysyłana zamiast strony nieautoryzowany dostęp do 403. Jest on wyświetlany, gdy Application Gateway WAF wykrywa złośliwy ruch i blokuje go.

Jeśli wystąpi błąd pochodzący z serwerów wewnętrznej bazy danych, zostanie on zakończony niezmodyfikowanym z powrotem do obiektu wywołującego. Niestandardowa strona błędu nie jest wyświetlana. Gdy żądanie nie może nawiązać połączenia z zapleczem, Brama aplikacji może wyświetlić niestandardową stronę błędu.

Niestandardowe strony błędów można definiować na poziomie globalnym i na poziomie odbiornika:

- **Poziom globalny** — strona błędu ma zastosowanie do ruchu dla wszystkich aplikacji sieci Web wdrożonych na tej bramie aplikacji.
- **Poziom odbiornika** — strona błędu jest stosowana do ruchu otrzymanego w tym odbiorniku.
- **Obie** — strona błędu niestandardowego zdefiniowana na poziomie odbiornika zastępuje zestaw ustawiony na poziomie globalnym.

Aby utworzyć niestandardową stronę błędu, musisz mieć:

- kod stanu odpowiedzi HTTP.
- odpowiadająca lokalizacja strony błędu. 
- publicznie dostępny obiekt BLOB usługi Azure Storage dla tej lokalizacji.
- Typ rozszerzenia *. htm lub *. html. 

Rozmiar strony błędu musi być mniejszy niż 1 MB. Jeśli na stronie błędu są połączone obrazy, muszą one być publicznie dostępnymi bezwzględnymi adresami URL lub kodowanym obrazem Base64 na stronie błędu niestandardowego. Linki względne z obrazami w tej samej lokalizacji obiektów BLOB nie są obecnie obsługiwane. 

Po określeniu strony błędu Brama aplikacji pobiera ją z lokalizacji obiektu blob magazynu i zapisuje ją w lokalnej pamięci podręcznej bramy aplikacji. Następnie strona błędu jest obsługiwana bezpośrednio z poziomu bramy aplikacji. Aby zmodyfikować istniejącą stronę błędu niestandardowego, należy wskazać inną lokalizację obiektu BLOB w konfiguracji bramy aplikacji. Brama aplikacji nie sprawdza okresowo lokalizacji obiektu BLOB w celu pobrania nowych wersji.

## <a name="portal-configuration"></a>Konfiguracja portalu

1. Przejdź do Application Gateway w portalu i wybierz bramę aplikacji.

    ![Zrzut ekranu przedstawia stronę przegląd bramy aplikacji.](media/custom-error/ag-overview.png)
2. Kliknij pozycję **detektory** i przejdź do określonego odbiornika, gdzie chcesz określić stronę błędu.

    ![Application Gateway detektory](media/custom-error/ag-listener.png)
3. Skonfiguruj niestandardową stronę błędów dla błędu WAF 403 lub strony obsługi 502 na poziomie odbiornika.

    > [!NOTE]
    > Tworzenie niestandardowych stron błędu niestandardowego na podstawie Azure Portal nie jest obecnie obsługiwane.

4. Określ publicznie dostępny adres URL obiektu BLOB dla danego kodu stanu błędu i kliknij przycisk **Zapisz**. Application Gateway jest teraz skonfigurowana za pomocą niestandardowej strony błędu.

   ![Application Gateway kody błędów](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Konfiguracja programu Azure PowerShell

Za pomocą Azure PowerShell można skonfigurować niestandardową stronę błędu. Na przykład globalna strona błędu niestandardowego:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Lub stronę błędu poziomu odbiornika:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Aby uzyskać więcej informacji, zobacz [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) i [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat diagnostyki Application Gateway, zobacz temat [kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway](application-gateway-diagnostics.md).
