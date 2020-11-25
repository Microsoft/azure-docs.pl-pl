---
title: Weryfikuj ustawienia usługi Azure Traffic Manager
description: W tym artykule dowiesz się, jak weryfikować ustawienia Traffic Manager i testować metody routingu ruchu.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022330"
---
# <a name="verify-traffic-manager-settings"></a>Weryfikowanie ustawień usługi Traffic Manager

Aby przetestować ustawienia Traffic Manager, musisz mieć wielu klientów w różnych lokalizacjach, z których można uruchomić testy. Następnie umieść punkty końcowe w profilu Traffic Manager pojedynczo.

* Ustaw niską wartość czasu wygaśnięcia DNS, aby zmiany były propagowane szybko (na przykład 30 sekund).
* Sprawdź adresy IP usług i witryn sieci Web w chmurze platformy Azure w testowanym profilu.
* Użyj narzędzi, które pozwalają rozpoznać nazwę DNS na adres IP i wyświetlić ten adres.

Sprawdzasz, czy nazwy DNS są rozpoznawane jako adresy IP punktów końcowych w Twoim profilu. Nazwy powinny być rozpoznawane w sposób zgodny z metodą routingu ruchu zdefiniowaną w profilu Traffic Manager. Aby rozpoznać nazwy DNS, można użyć narzędzi takich jak **nslookup** lub **Dig** .

Poniższe przykłady ułatwiają przetestowanie profilu Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Sprawdź profil Traffic Manager przy użyciu polecenia nslookup i ipconfig w systemie Windows

1. Otwórz wiersz polecenia lub programu Windows PowerShell jako administrator.
2. Wpisz `ipconfig /flushdns` , aby opróżnić pamięć podręczną programu rozpoznawania nazw DNS.
3. Wpisz `nslookup <your Traffic Manager domain name>`. Na przykład następujące polecenie sprawdza nazwę domeny z prefiksem *MojaApl. contoso*

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Typowy wynik zawiera następujące informacje:

    + Nazwa DNS i adres IP serwera DNS, do którego uzyskuje się dostęp do tej nazwy domeny Traffic Manager.
    + Nazwa domeny Traffic Manager wpisana w wierszu polecenia po "nslookup" i adres IP, do którego jest rozpoznawana domena Traffic Manager. Drugi adres IP jest ważnym elementem do sprawdzenia. Powinien być zgodny z publicznym wirtualnym adresem IP (VIP) dla jednej z usług w chmurze lub witryn sieci Web w testowanym profilu Traffic Manager.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Testowanie metody routingu ruchu trybu failover

1. Pozostaw wszystkie punkty końcowe w górę.
2. Przy użyciu jednego klienta należy zażądać rozpoznawania nazw DNS dla domeny firmowej przy użyciu narzędzia Nslookup lub podobnego.
3. Upewnij się, że rozpoznany adres IP odpowiada podstawowemu punktowi końcowemu.
4. Wywołaj podstawowy punkt końcowy lub usuń plik monitorowania, aby Traffic Manager uważać, że aplikacja nie działa.
5. Poczekaj, aż upłynie czas wygaśnięcia (TTL) usługi DNS profilu Traffic Manager i dodatkowe dwie minuty. Na przykład jeśli czas wygaśnięcia usługi DNS wynosi 300 sekund (5 minut), należy poczekać siedem minut.
6. Opróżnij pamięć podręczną klienta DNS i zażądaj rozpoznawania nazw DNS przy użyciu narzędzia Nslookup. W systemie Windows można opróżnić pamięć podręczną DNS za pomocą polecenia ipconfig/flushdns.
7. Upewnij się, że rozpoznany adres IP pasuje do pomocniczego punktu końcowego.
8. Powtórz ten proces, łącząc poszczególne punkty końcowe z kolei. Sprawdź, czy serwer DNS zwraca adres IP następnego punktu końcowego z listy. Gdy wszystkie punkty końcowe nie działają, należy ponownie uzyskać adres IP podstawowego punktu końcowego.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Testowanie ważonej metody routingu ruchu

1. Pozostaw wszystkie punkty końcowe w górę.
2. Przy użyciu jednego klienta należy zażądać rozpoznawania nazw DNS dla domeny firmowej przy użyciu narzędzia Nslookup lub podobnego.
3. Upewnij się, że rozpoznany adres IP odpowiada jednemu z punktów końcowych.
4. Opróżnij pamięć podręczną klienta DNS i powtórz kroki 2 i 3 dla każdego punktu końcowego. Powinny zostać wyświetlone różne adresy IP, które są zwracane dla każdego z punktów końcowych.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Testowanie metody routingu ruchu wydajności

W celu efektywnego przetestowania metody routingu ruchu wydajności należy mieć klientów znajdujących się w różnych częściach świata. Możesz tworzyć klientów w różnych regionach świadczenia usługi Azure, których można użyć do testowania usług. Jeśli masz sieć globalną, możesz zdalnie zalogować się do klientów w innych częściach świata i uruchomić testy z tego miejsca.

Alternatywnie dostępne są bezpłatne wyszukiwanie i Dig usługi DNS oparte na sieci Web. Niektóre z tych narzędzi dają możliwość sprawdzania rozpoznawania nazw DNS z różnych lokalizacji na całym świecie. Aby zapoznać się z przykładami, wyszukaj ciąg "wyszukiwanie DNS". Usług innych firm, takich jak Gomez lub prezentację, można użyć do sprawdzenia, czy Twoje profile dystrybuują ruch zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

* [Informacje o metodach routingu ruchu Traffic Manager](traffic-manager-routing-methods.md)
* [Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
* [Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Traffic Manager](traffic-manager-troubleshooting-degraded.md)
