---
title: Obsługa protokołu dla nagłówków HTTP w Azure Front Door | Microsoft Docs
description: W tym artykule opisano protokoły nagłówka HTTP, Front Door obsługuje.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785761"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Obsługa protokołu dla nagłówków HTTP w Azure Front Door
W tym artykule przedstawiono protokół, który Front Door obsługuje z częściami ścieżki wywołania (zobacz obraz). Poniższe sekcje zawierają więcej informacji na temat nagłówków HTTP obsługiwanych przez Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door protokołu nagłówków HTTP":::

>[!IMPORTANT]
>Front Door nie certyfikuje nagłówków HTTP, które nie są tutaj udokumentowane.

## <a name="client-to-front-door"></a>Klient do Front Door
Front Door akceptuje większość nagłówków dla żądania przychodzącego bez ich modyfikowania. Niektóre zarezerwowane nagłówki są usuwane z żądania przychodzącego w przypadku wysłania, w tym nagłówki z prefiksem X-FD-*.

## <a name="front-door-to-backend"></a>Front Door do zaplecza

Front Door zawiera nagłówki dla żądania przychodzącego, chyba że zostaną usunięte z powodu ograniczeń. Front Door dodaje również następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| Via |  *Za pośrednictwem: 1.1 Azure* </br> Front Door dodaje wersję HTTP klienta, a następnie *platformę Azure* jako wartość nagłówka Via. Ten nagłówek wskazuje wersję HTTP klienta i Front Door był pośrednim adresatem żądania między klientem a zaplecza.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Reprezentuje adres IP klienta skojarzony z przetwarzanych żądań. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forwarded-For, aby wskazać adres IP oryginalnego obiektu wywołującego. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Reprezentuje adres IP gniazda skojarzony z połączeniem TCP, z którego pochodzi bieżące żądanie. Adres IP klienta żądania może nie być równy adresowi IP gniazda, ponieważ adres IP klienta może zostać dowolnie zastąpiony przez użytkownika.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez Front Door. Służy do przeszukiwania dzienników dostępu i ma kluczowe znaczenie dla rozwiązywania problemów.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Nagłówek, który Front Door używany do wykrywania pętli żądań, a użytkownicy nie powinni mieć od niego zależności. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Ciąg odwołania, który identyfikuje żądanie, pochodzi z określonego Front Door zasobu. Wartość można zobaczyć na stronie Azure Portal pobrać przy użyciu interfejsu API zarządzania. Tego nagłówka można używać w połączeniu z adresami ACL adresów IP, aby zablokować punkt końcowy tak, aby akceptował tylko żądania z określonego Front Door zasobu. Zobacz często zadawane pytania, aby [uzyskać więcej szczegółów](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-for | *X-Forwarded-For: 127.0.0.1* </br> Pole nagłówka HTTP X-Forwarded-For (XFF) często identyfikuje adres IP klienta łączącego się z serwerem internetowym za pośrednictwem serwera proxy HTTP lub usługi równoważenia obciążenia. Jeśli istnieje istniejący nagłówek XFF, Front Door dołącza do niego adres IP gniazda klienta lub dodaje nagłówek XFF z adresem IP gniazda klienta. |
| X-Forwarded-Host | *Host przekazywania X: contoso.azurefd.net* </br> Pole nagłówka HTTP X-Forwarded-Host jest powszechną metodą używaną do identyfikowania oryginalnego hosta żądanego przez klienta w nagłówku żądania HTTP hosta. Wynika to z tego, że nazwa hosta z Front Door może się różnić dla serwera zaplecza, który obsługi żądania. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Pole nagłówka HTTP X-Forwarded-Proto jest często używane do identyfikowania protokołu pochodzącego z żądania HTTP. Front Door oparte na konfiguracji mogą komunikować się z zaplecza przy użyciu protokołu HTTPS. Ta wartość jest prawdziwa, nawet jeśli żądanie do zwrotnego serwera proxy to HTTP. |
| X-FD-HealthProbe | Pole nagłówka HTTP X-FD-HealthProbe służy do identyfikowania sondy kondycji na Front Door. Jeśli ten nagłówek ma wartość 1, żądanie jest sondą kondycji. Można użyć, gdy chcesz ścisłego dostępu z określonych Front Door z X-Forwarded-Host pole nagłówka. |
| X-Azure-FDID | *Nagłówek X-Azure-FDID: 437c82cd-360a-4a54-94c3-5ff707647783* </br> To pole zawiera frontdoorID, który może służyć do identyfikowania, Front Door żądania przychodzącego. To pole jest wypełniane przez Front Door usługi. | 

## <a name="front-door-to-client"></a>Front Door do klienta

Wszystkie nagłówki wysyłane do Front Door z zaplecza są również przekazywane do klienta. Poniżej przedstawiono nagłówki wysyłane z usługi Front Door do klientów.

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez usługę Front Door, co ma kluczowe znaczenie dla rozwiązywania problemów, ponieważ jest on używany do przeszukiwania dzienników dostępu.|
| X-Cache | *X-Cache: TCP_HIT* </br> Ten nagłówek opisuje stan pamięci podręcznej żądania, co umożliwia określenie, czy zawartość odpowiedzi jest udostępniana z pamięci podręcznej Front Door. |

Musisz wysłać nagłówek żądania "X-Azure-DebugInfo: 1", aby włączyć następujące opcjonalne nagłówki odpowiedzi.

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Ten nagłówek zawiera kod stanu HTTP zwrócony przez zaplecza. Za pomocą tego nagłówka można zidentyfikować kod stanu HTTP zwrócony przez aplikację uruchamianą w za zaplecza bez konieczności korzystania z dzienników zaplecza. Ten kod stanu może różnić się od kodu stanu HTTP w odpowiedzi wysyłanej do klienta przez Front Door. Ten nagłówek umożliwia określenie, czy zaplecza nie działa nieprawidłowo lub czy problem dotyczy Front Door usługi. |
| X-Azure-InternalError | Ten nagłówek będzie zawierać kod błędu, który Front Door występuje podczas przetwarzania żądania. Ten błąd wskazuje, że problem jest wewnętrzny dla Front Door usługi/infrastruktury. Zgłoś problem do pomocy technicznej.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, urząd certyfikacji jest nieznany.* </br> Ten nagłówek zawiera kod błędu, który Front Door na serwerach podczas ustanawiania łączności z serwerem zaplecza w celu przetwarzania żądania. Ten nagłówek pomoże zidentyfikować problemy w połączeniu między Front Door a aplikacją zaplecza. Ten nagłówek będzie zawierać szczegółowy komunikat o błędzie, który pomoże zidentyfikować problemy z łącznością z zapleczami (na przykład rozpoznawanie nazw DNS, nieprawidłowy certyfikat itd.). |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie usługi Front Door](quickstart-create-front-door.md)
- [Jak Front Door działa](front-door-routing-architecture.md)
