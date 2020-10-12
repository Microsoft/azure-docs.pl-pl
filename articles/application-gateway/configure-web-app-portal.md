---
title: Zarządzanie ruchem do aplikacji wielodostępnych przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera wskazówki dotyczące sposobu konfigurowania aplikacji sieci Web usługi Azure App Service jako członków w puli zaplecza na istniejącej lub nowej bramie aplikacji.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: victorh
ms.openlocfilehash: df92e08e91761d77c606ccb5389eee7dc219c101
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323388"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurowanie App Service przy użyciu Application Gateway

Ponieważ usługa App Service jest usługą z wieloma dzierżawami zamiast wdrożenia dedykowanego, używa nagłówka hosta w żądaniu przychodzącym, aby rozwiązać żądanie do poprawnego punktu końcowego usługi App Service. Zwykle nazwa DNS aplikacji, która z kolei jest nazwą DNS skojarzoną z frontonem usługi App Service, różni się od nazwy domeny usługi aplikacji zaplecza. W związku z tym nagłówek hosta w oryginalnym żądaniu odebranym przez bramę aplikacji nie jest taki sam jak nazwa hosta usługi wewnętrznej bazy danych. Z tego względu, jeśli nagłówek hosta w żądaniu z bramy Application Gateway do zaplecza nie zostanie zmieniony na nazwę hosta usługi wewnętrznej bazy danych, nadmiarowe punkty końcowe z wieloma dzierżawcami nie będą mogły rozpoznać żądania do prawidłowego punktu końcowego.

Application Gateway udostępnia przełącznik, `Pick host name from backend target` który zastępuje nagłówek hosta w żądaniu nazwą hosta zaplecza, gdy żądanie jest kierowane z Application Gateway do zaplecza. Ta funkcja umożliwia obsługę zaplecza z wieloma dzierżawami, takich jak usługa Azure App Service i API Management. 

W tym artykule omówiono sposób wykonywania następujących zadań:

- Edytowanie puli zaplecza i dodawanie do niej App Service
- Edytowanie ustawień protokołu HTTP z włączonym przełącznikiem "Wybierz hosta"

## <a name="prerequisites"></a>Wymagania wstępne

- Application Gateway: Tworzenie bramy aplikacji bez docelowej puli zaplecza. Aby uzyskać więcej informacji, zobacz [Szybki Start: bezpośredni ruch internetowy za pomocą platformy Azure Application Gateway — Azure Portal](quick-create-portal.md)

- App Service: Jeśli nie masz istniejącej usługi App Service, zobacz [dokumentację usługi App Service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Dodawanie usługi App Service jako puli zaplecza

1. W Azure Portal Wybierz bramę aplikacji.

2. W obszarze **Pule zaplecza**wybierz pulę zaplecza.

4. W obszarze **Typ docelowy**wybierz pozycję **App Services**.

5. W obszarze **cel** wybierz App Service.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Zaplecze usługi App Service":::
   
   > [!NOTE]
   > Na liście rozwijanej są wypełniane tylko te usługi aplikacji, które znajdują się w tej samej subskrypcji co Application Gateway. Jeśli chcesz użyć usługi App Service, która znajduje się w innej subskrypcji niż ta, w której znajduje się Application Gateway, a następnie wybierz pozycję **App Services** na liście rozwijanej **elementy docelowe** , zaznacz opcję **adres IP lub nazwa hosta** , a następnie wprowadź nazwę hosta (przykład. azurewebsites.net) usługi App Service.
1. Wybierz pozycję **Zapisz**.

## <a name="edit-http-settings-for-app-service"></a>Edytuj ustawienia protokołu HTTP dla App Service

1. W obszarze **Ustawienia protokołu HTTP**Wybierz istniejące ustawienie protokołu HTTP.

2. W obszarze **Przesłoń z nową nazwą hosta**wybierz pozycję **tak**.
3. W obszarze **przesłonięcie nazwy hosta**wybierz pozycję Wybierz **nazwę hosta z elementu docelowego zaplecza**.
4. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Zaplecze usługi App Service":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Dodatkowa konfiguracja w przypadku przekierowania do ścieżki względnej usługi App Service

Gdy usługa App Service wysyła odpowiedź przekierowania do klienta w celu przekierowania do ścieżki względnej (na przykład przekierowanie z `contoso.azurewebsites.net/path1` do `contoso.azurewebsites.net/path2` ), używa tej samej nazwy hosta w nagłówku lokalizacji odpowiedzi jako tej w żądaniu otrzymanej od bramy aplikacji. Klient wyśle żądanie bezpośrednio, aby nie przechodzić `contoso.azurewebsites.net/path2` przez bramę aplikacji ( `contoso.com/path2` ). Pomijanie bramy aplikacji nie jest pożądane.

Jeśli w Twoim przypadku użycia, istnieją scenariusze, w których usługa App Service będzie musiała wysłać odpowiedź przekierowania do klienta, wykonaj [dodatkowe kroki w celu ponownego zapisania nagłówka lokalizacji](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Ograniczanie dostępu

Aplikacje sieci Web wdrożone w tych przykładach używają publicznych adresów IP, do których można uzyskać dostęp bezpośrednio z Internetu. Ułatwia to rozwiązywanie problemów podczas uczenia się o nowej funkcji i podejmowanie nowych zadań. Ale jeśli zamierzasz wdrożyć funkcję w środowisku produkcyjnym, musisz dodać więcej ograniczeń.

Jednym ze sposobów ograniczenia dostępu do aplikacji sieci Web jest użycie [Azure App Service ograniczeń statycznych adresów IP](../app-service/app-service-ip-restrictions.md). Można na przykład ograniczyć aplikację sieci Web tak, aby tylko odbierał ruch z bramy aplikacji. Użyj funkcji ograniczenia adresów IP usługi App Service, aby wyświetlić listę adresów VIP bramy aplikacji jako jedyny adres z dostępem.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi App Service i innej obsługi wielu dzierżawców za pomocą usługi Application Gateway, zobacz [Obsługa wielu dzierżawców w usłudze Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
