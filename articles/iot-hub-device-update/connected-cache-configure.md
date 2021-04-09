---
title: Konfigurowanie połączonej pamięci podręcznej firmy Microsoft dla aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Omówienie połączonej pamięci podręcznej firmy Microsoft dla aktualizacji urządzenia dla platformy Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663161"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Konfigurowanie połączonej pamięci podręcznej firmy Microsoft dla aktualizacji urządzenia dla platformy Azure IoT Hub

Połączona pamięć podręczna firmy Microsoft jest wdrażana do Azure IoT Edge bram jako moduł Azure IoT Edge. Podobnie jak w przypadku innych modułów Azure IoT Edge, zmienne środowiskowe wdrożenia modułu MCC i opcje tworzenia kontenera są używane do konfigurowania modułów pamięci podręcznej połączonej z firmą Microsoft.  Ta sekcja definiuje zmienne środowiskowe i opcje tworzenia kontenerów, które są wymagane, aby Klient pomyślnie wdrożyć moduł połączonej pamięci podręcznej firmy Microsoft do użycia przez aktualizację urządzenia dla platformy Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Szczegóły wdrożenia modułu Azure IoT Edge połączonej pamięci podręcznej firmy Microsoft

Nazwa modułu połączonej pamięci podręcznej firmy Microsoft jest według uznania administratora. Nie ma żadnych innych interakcji modułów lub usług, które opierają się na nazwie modułu do komunikacji. Ponadto relacja nadrzędna elementu podrzędnego serwerów połączonej pamięci podręcznej firmy Microsoft nie jest zależna od nazwy tego modułu, ale raczej nazwy FQDN lub adresu IP bramy Azure IoT Edge, która została skonfigurowana zgodnie z wcześniejszym opisem.

Zmienne środowiskowe połączonej pamięci podręcznej firmy Microsoft Azure IoT Edge są używane do przekazywania podstawowych informacji o tożsamości modułu oraz ustawień modułu funkcjonalnego do kontenera.

| Nazwa zmiennej                 | Format wartości                           | Wymagane/Opcjonalne | Funkcjonalność                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Identyfikator GUID identyfikatora subskrypcji platformy Azure             | Wymagane          | Jest to klucz klienta, który zapewnia bezpieczną<br>Uwierzytelnianie węzła pamięci podręcznej w celu optymalizacji dostarczania<br>Services. Wymagane w celu działania modułu. |
| CACHE_NODE_ID                 | Identyfikator GUID węzła pamięci podręcznej                     | Wymagane          | Unikatowa identyfikacja pamięci podręcznej połączonej z firmą Microsoft<br>Usługi optymalizacji między węzłami. Wymagane w pożądanej kolejności<br> dla modułu do działania. |
| CUSTOMER_KEY                  | Identyfikator GUID klucza klienta                     | Wymagane          | Jest to klucz klienta, który zapewnia bezpieczną<br>Uwierzytelnianie węzła pamięci podręcznej do usług optymalizacji dostarczania.<br>Wymagane w celu działania modułu.|
| STORAGE_ *N* _SIZE_GB           | Gdzie N to liczba wymaganych GB   | Wymagane          | Określ do dziewięciu dysków, aby buforować zawartość i określić<br>Maksymalna ilość miejsca w gigabajtach do przydzielenia zawartości na każdym dysku pamięci podręcznej. Przykłady:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Liczba dysków musi być zgodna z określonymi wartościami powiązania dysku pamięci podręcznej<br>w opcji tworzenia kontenera MicrosoftConnectedCache *N* wartość|
| UPSTREAM_HOST                 | NAZWA FQDN/ADRES IP                                | Opcjonalne          | Ta wartość może określać połączenie nadrzędne firmy Microsoft<br>Węzeł pamięci podręcznej, który działa jako serwer proxy, jeśli węzeł podłączonej pamięci podręcznej<br> jest odłączony od Internetu. To ustawienie służy do obsługi<br> Zagnieżdżony scenariusz IoT.<br>**Uwaga:** Połączona pamięć podręczna firmy Microsoft nasłuchuje na domyślnym porcie http 80.|
| UPSTREAM_PROXY                | NAZWA FQDN/ADRES IP: PORT                           | Opcjonalne          | Wychodzący internetowy serwer proxy.<br>Może to być również Niemniej w przypadku serwera proxy DMZ w przypadku sieci ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/ADRES IP<br>Nazwa FQDN                        | Opcjonalne          | Wymagane do obsługi niestandardowych repozytoriów pakietów.<br>Repozytoria mogą być hostowane lokalnie lub w Internecie.<br>Nie ma żadnego limitu liczby hostów niestandardowych, które można skonfigurować.<br><br>Przykłady:<br>Nazwa = CACHEABLE_CUSTOM_1_HOST wartość = packages.foo.com<br> Nazwa = CACHEABLE_CUSTOM_2_HOST wartość = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Opcjonalne          | Wymagane do obsługi niestandardowych repozytoriów pakietów.<br>Ta wartość może być używana jako alias i będzie używana przez serwer pamięci podręcznej do odwoływania się<br>różne nazwy DNS. Na przykład nazwa hosta zawartości repozytorium może być packages.foo.com,<br>ale dla różnych regionów może istnieć dodatkowy prefiks, który jest dodawany do nazwy hosta<br>takich jak westuscdn.packages.foo.com i eastuscdn.packages.foo.com.<br>Ustawiając alias kanoniczny, upewnij się, że zawartość nie jest zduplikowana.<br>dla zawartości pochodzącej z tego samego hosta, ale różnych źródeł sieci CDN.<br>Format wartości kanonicznej nie jest ważny, ale musi być unikatowy dla hosta.<br>Najłatwiej ustawić wartość zgodną z wartością hosta.<br><br>Przykłady na podstawie przykładów hosta niestandardowego:<br>Nazwa = CACHEABLE_CUSTOM_1_CANONICAL wartość = foopackages<br> Nazwa = CACHEABLE_CUSTOM_2_CANONICAL wartość = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Prawda lub FAŁSZ                          | Opcjonalne          | Umożliwia wyświetlanie raportu podsumowującego w sieci lokalnej lub Internecie.<br>Użycie klucza interfejsu API (omówionego później) jest wymagane do wyświetlenia raportu podsumowującego, jeśli ustawiono wartość true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Prawda lub FAŁSZ                          | Opcjonalne          | Umożliwia wyświetlanie raportu podsumowującego w sieci lokalnej lub Internecie bez<br>Użycie klucza interfejsu API z dowolnego urządzenia w sieci. Użyj, jeśli nie chcesz blokować dostępu<br>Wyświetlanie danych podsumowania serwera pamięci podręcznej za pośrednictwem przeglądarki. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opcja tworzenia kontenera modułu Azure IoT Edge połączonej pamięci podręcznej firmy Microsoft

Opcje tworzenia kontenera dla wdrożenia modułu MCC zapewniają kontrolę ustawień związanych z magazynem i portami używanymi przez moduł MCC. Jest to lista wymaganych zmiennych kontenerów użytych do wdrożenia MCC.

### <a name="container-to-host-os-drive-mappings"></a>Kontener mapowania dysków systemu operacyjnego

Wymagane do zamapowania lokalizacji magazynu kontenerów na lokalizację magazynu na dysku. można określić < do dziewięciu lokalizacji.

>[!Note]
>Liczba dysków musi być zgodna z wartościami powiązania dysku pamięci podręcznej określonymi w zmiennej środowiskowej STORAGE_ *N* _SIZE_GB wartość. ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Kontener do mapowania portów TCP

Ta opcja określa port HTTP maszyny zewnętrznej, który MCC nasłuchuje w przypadku żądań zawartości. Domyślnie HostPort jest port 80, a inne porty nie są obecnie obsługiwane, ponieważ klient ADU wysyła żądania na port 80 dzisiaj. Port TCP 8081 jest portem wewnętrznym kontenera, na którym nasłuchuje MCC i nie można go zmienić.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>Mapowania portów TCP usługi Container Service

Moduł połączonej pamięci podręcznej firmy Microsoft ma usługę .NET Core, która jest używana przez aparat buforowania dla różnych funkcji.

>[!Note]
>Aby można było obsługiwać zagnieżdżoną krawędź usługi Azure IoT, HostPort nie może być ustawiony na 5000, ponieważ moduł proxy rejestru już nasłuchuje na porcie 5000.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Raport podsumowujący połączonej pamięci podręcznej firmy Microsoft

Raport podsumowujący jest obecnie jedynym sposobem, aby klient mógł wyświetlać dane buforowania dla wystąpień połączonej pamięci podręcznej firmy Microsoft wdrożonych w celu Azure IoT Edge bram. Raport jest generowany w odstępach 15-sekundowych i zawiera średnie statystyki dla tego okresu oraz zagregowane statystyki dotyczące okresu istnienia modułu. Kluczowe statystyki dotyczące najważniejszych klientów:

* **hitBytes** — Suma bajtów dostarczonych bezpośrednio z pamięci podręcznej.
* Błędy **niezerowe —** jest to suma bajtów dostarczona przez połączoną pamięć podręczną firmy Microsoft w celu wyświetlenia pamięci podręcznej.
* **eggressBytes** — jest to suma hitBytes i niezwiązanych z nią wartości, a to całkowita liczba bajtów dostarczanych do klientów.
* **hitRatioBytes** — stosunek HitBytes do egressBytes.  Jeśli 100% eggressBytes dostarczonych w danym okresie była równa hitBytes, może to być 1 na przykład.

Raport podsumowujący jest dostępny pod adresem `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (zobacz szczegóły zmiennej środowiskowej poniżej, aby uzyskać informacje na temat widoczności tego raportu).
