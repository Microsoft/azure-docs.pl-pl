---
title: 'Samouczek: Konfigurowanie rozważonego routingu ruchu w trybie okrężnym za pomocą usługi Azure Traffic Manager'
description: W tym samouczku wyjaśniono, jak równoważyć obciążenie ruchu przy użyciu metody okrężnej w Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207843"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu ważonego w Traffic Manager

Typowym wzorcem metody routingu ruchu jest zapewnienie zestawu identycznych punktów końcowych, takich jak usługi w chmurze i witryny sieci Web, a także wysyłanie ruchu do każdego z nich. Poniższe kroki przedstawiają sposób konfigurowania tego typu metody routingu ruchu.

> [!NOTE]
> Usługa Azure Web App już zapewnia funkcję równoważenia obciążenia z działaniem okrężnym dla witryn sieci Web w regionie świadczenia usługi Azure (co może obejmować wiele centrów danych). Traffic Manager umożliwia dystrybucję ruchu między witrynami sieci Web w różnych centrach danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Utwórz profil Traffic Manager z użyciem ważonego routingu.
> - Użyj profilu Traffic Manager.
> - Usuń profil Traffic Manager.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Konfigurowanie ważonej metody routingu ruchu

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Na pasku wyszukiwania portalu Wyszukaj nazwę **profilu Traffic Manager** utworzonego w poprzedniej sekcji i wybierz profil usługi Traffic Manager w wyświetlonych wynikach.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Wyszukaj profil Traffic Manager":::

1. Wybierz pozycję **Konfiguracja** i wybierz lub wprowadź następujące ustawienia:

    | Ustawienie         | Wartość                                              |
    | ---             | ---                                                |
    | Metoda routingu            | Wybierz opcję **ważone**. |    
    | Czas wygaśnięcia (TTL) DNS | Ta wartość określa, jak często serwer nazw buforowania lokalnego klienta będzie wysyłać zapytania do systemu Traffic Manager pod kątem zaktualizowanych wpisów DNS. Wszystkie zmiany dotyczące Traffic Manager, takie jak Metoda routingu ruchu zmiany lub zmiany w dostępności dodanych punktów końcowych, będą miały ten okres czasu odświeżania w całym systemie globalnym serwerów DNS. |
    | Protokół    | Wybierz protokół do monitorowania punktów końcowych. *Opcje: HTTP, HTTPS i TCP* |
    | Port | Określ numer portu. |
    | Ścieżka | Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie). |
    | Niestandardowe ustawienia nagłówka | Skonfiguruj niestandardowe nagłówki w formacie Host:contoso. com, newheader: NewValue. Maksymalna obsługiwana para to 8. Dotyczy protokołu HTTP i https. Dotyczy wszystkich punktów końcowych w profilu |
    | Oczekiwane zakresy kodu stanu (wartość domyślna: 200) | Skonfiguruj zakresy kodów stanu w formacie 200-299301-301. Maksymalny obsługiwany zakres to 8. Dotyczy protokołu HTTP i https. Dotyczy wszystkich punktów końcowych w profilu |
    | Interwał sondowania | Skonfiguruj interwał czasu między sondami kondycji punktu końcowego. Możesz wybrać 10 lub 30 sekund. |
    | Tolerowana liczba niepowodzeń | Skonfiguruj liczbę błędów sondy kondycji tolerowanych przed wyzwoleniem błędu punktu końcowego. Można wprowadzić liczbę z zakresu od 0 do 9. | 
    | Limit czasu sondy | Skonfiguruj czas wymagany przed upływem limitu czasu sondy kondycji punktu końcowego. Ta wartość musi być co najmniej 5 i mniejsza niż wartość interwału sondowania. |

1. Wybierz przycisk **Zapisz**, aby ukończyć konfigurację.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager konfiguracja ważona"::: 

1. Wybierz **punkt końcowy** i skonfiguruj wagę każdego punktu końcowego. Waga może należeć do zakresu od 1-1000. Im wyższa waga, tym wyższy priorytet.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Konfiguracja Traffic Manager ważonych punktów końcowych"::: 

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Traffic Manager

W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Nazwa może być używana przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. W takim przypadku wszystkie żądania są kierowane do każdego punktu końcowego w sposób okrężny.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager ważony przegląd"::: 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli profil Traffic Manager nie jest już potrzebny, zlokalizuj profil i wybierz pozycję **Usuń profil**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Usuń profil ważony Traffic Manager":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodzie routingu ważone, zobacz:

> [!div class="nextstepaction"]
> [Ważona Metoda routingu ruchu](traffic-manager-routing-methods.md#weighted)