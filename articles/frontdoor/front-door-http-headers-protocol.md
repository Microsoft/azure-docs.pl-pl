---
title: Obsługa protokołu dla nagłówków HTTP w zewnętrznych drzwiach platformy Azure | Microsoft Docs
description: W tym artykule opisano protokoły nagłówkowe protokołu HTTP obsługiwane przez funkcję front-drzwi.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: cd721f13ffa128e83072819a20b17f305118b13c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626296"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Obsługa protokołu dla nagłówków HTTP w ramach zewnętrznych drzwi platformy Azure
W tym artykule opisano protokół, który obsługuje drzwiczki z częściami ścieżki wywołania (Zobacz obraz). Poniższe sekcje zawierają więcej informacji na temat nagłówków HTTP obsługiwanych przez tylne drzwi.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protokół HTTP dla drzwi z przodu platformy Azure":::

>[!IMPORTANT]
>Drzwi z przodu nie poświadczają żadnych nagłówków HTTP, które nie są udokumentowane w tym miejscu.

## <a name="client-to-front-door"></a>Klient do przednich drzwi
Drzwi z przodu akceptują większość nagłówków dla żądania przychodzącego bez konieczności ich modyfikacji. Niektóre zarezerwowane nagłówki są usuwane z przychodzącego żądania, jeśli są wysyłane, włącznie z nagłówkami z prefiksem X-FD-*.

## <a name="front-door-to-backend"></a>Drzwi z przodu do zaplecza

Przód drzwi obejmuje nagłówki dla żądania przychodzącego, o ile nie zostały usunięte z powodu ograniczeń. Drzwi z przodu również dodaje następujące nagłówki:

| Header  | Przykład i opis |
| ------------- | ------------- |
| Korzystając |  Za pośrednictwem: 1,1 Azure </br> Przód drzwi dodaje wersję HTTP klienta, a następnie *platformę Azure* jako wartość dla nagłówka Via. Ten nagłówek wskazuje wersję HTTP klienta i że przedniego drzwi były pośrednim odbiorcą dla żądania między klientem a zapleczem.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Reprezentuje adres IP klienta skojarzony z przetwarzanym żądaniem. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forward-for, aby wskazać adres IP oryginalnego obiektu wywołującego. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Reprezentuje adres IP gniazda skojarzonego z połączeniem TCP, z którego pochodzi bieżące żądanie. Adres IP klienta żądania może nie być taki sam jak adres IP gniazda, ponieważ może zostać zawolnie nadpisany przez użytkownika.|
| X-Azure-ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi. Jest on używany do wyszukiwania dzienników dostępu i do rozwiązywania problemów.|
| X-Azure-RequestChain |  X-Azure-RequestChain: przeskoki = 1 </br> Nagłówek, za pomocą którego przede wszystkim wykrywa pętle żądań, a użytkownicy nie powinni na niej korzystać. |
| X-Azure-FDID | X-Azure-FDID: 55ce4ed1-4B06-4bf1-b40e-4638452104da<br/> Ciąg odwołania, który identyfikuje żądanie dostarczone z określonego zasobu z przodu. Wartość można zobaczyć w witrynie Azure Portal lub pobrać przy użyciu interfejsu API zarządzania. Tego nagłówka można użyć w połączeniu z listami ACL protokołu IP w celu zablokowania punktu końcowego w celu akceptowania tylko żądań z określonego zasobu z przodu. Zobacz często zadawane pytania, aby uzyskać [więcej szczegółów](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door) |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Pole nagłówka HTTP X-Forwarded-For (XFF) często identyfikuje źródłowy adres IP klienta łączącego się z serwerem sieci Web za pośrednictwem serwera proxy HTTP lub modułu równoważenia obciążenia. Jeśli istnieje nagłówek XFF, następnie drzwi do przodu dołącza do niego adres IP gniazda klienta lub dodaje nagłówek XFF z adresem IP gniazda klienta. |
| X-Forward-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pole nagłówka HTTP X-forwardd-host jest wspólną metodą służącą do identyfikowania oryginalnego hosta żądanego przez klienta w nagłówku żądania HTTP hosta. Wynika to z faktu, że nazwa hosta z drzwi przednich może się różnić w przypadku serwera wewnętrznej bazy danych obsługującego żądanie. |
| X-Forwarded-proto | X-Forwarded-proto: http </br> Pole nagłówka HTTP X-Forwarded-proto jest często używane do identyfikowania źródłowego protokołu żądania HTTP, ponieważ przód drzwi na podstawie konfiguracji mogą komunikować się z zapleczem przy użyciu protokołu HTTPS. Jest to prawdziwe, nawet jeśli żądanie do zwrotnego serwera proxy jest HTTP. |
| X-FD-HealthProbe | Pole nagłówka HTTP X-FD-HealthProbe służy do identyfikowania sondy kondycji z czołowych drzwi. Jeśli ten nagłówek ma ustawioną wartość 1, żądanie jest sondą kondycji. Można użyć, gdy chcesz ścisły dostęp z konkretnych drzwi z przodu przy użyciu pola nagłówka X-forwardd-host. |
|X-Azure-FDID | Nagłówek X-FDID: 437c82cd-360A-4a54-94c3-5ff707647783 </br> To pole zawiera frontdoorID, które może służyć do identyfikowania, z której drzwi są odbierane przychodzące żądania. To pole jest wypełniane przez usługę frontonu. | 


## <a name="front-door-to-client"></a>Przód do klienta

Wszystkie nagłówki wysyłane do przednich drzwi z zaplecza są również przekazywane do klienta. Poniżej przedstawiono nagłówki wysyłane z czołowych drzwi do klientów.

| Header  | Przykład |
| ------------- | ------------- |
| X-Azure-ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, co jest niezwykle ważne w przypadku rozwiązywania problemów, które jest używane do wyszukiwania dzienników dostępu.|

## <a name="next-steps"></a>Następne kroki

- [Tworzenie usługi Front Door](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)
