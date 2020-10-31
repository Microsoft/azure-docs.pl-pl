---
title: Uruchom Azure Stream Analytics na Azure Stack (wersja zapoznawcza)
description: Utwórz zadanie Azure Stream Analytics Edge i wdróż je w centrum Azure Stack za pośrednictwem środowiska uruchomieniowego IoT Edge.
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 21cf432576829b575d70a94227f28df373a4d899
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126162"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Uruchom Azure Stream Analytics na Azure Stack (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest w wersji zapoznawczej i nie jest zalecana do użycia w środowisku produkcyjnym.

Azure Stream Analytics można uruchomić na Azure Stack Hub jako moduł IoT Edge. Dodano konfiguracje do modułu IoT Edge, co umożliwia działowi IT współdziałanie z usługą BLOB Storage, Event Hubs i centrami IoT działającymi w ramach subskrypcji usługi Azure Stack Hub przez umożliwienie niestandardowych adresów URL znalezionych w każdym Azure Stackym zatrudnieniu centrum.

Dzięki Stream Analytics na Azure Stack można tworzyć prawdziwie architekturę hybrydową do przetwarzania strumieniowego w prywatnej, autonomicznej chmurze, która może być połączona lub rozłączona z aplikacjami natywnymi w chmurze przy użyciu spójnych usług platformy Azure w środowisku lokalnym. 

W tym artykule opisano sposób przesyłania strumieniowego danych z IoT Hub lub centrum zdarzeń do innego centrum zdarzeń lub Blob Storage w ramach subskrypcji centrum Azure Stack Hub i przetwarzania go przy użyciu Stream Analytics.

## <a name="set-up-environments"></a>Konfigurowanie środowisk

Azure Stream Analytics jest usługą hybrydową w centrum Azure Stack. Jest to moduł IoT Edge, który jest skonfigurowany na platformie Azure, ale może być uruchamiany w centrum Azure Stack.  

Jeśli jesteś nowym do Azure Stack centrum lub IoT Edge, postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować środowiska.

### <a name="prepare-the-azure-stack-hub-environment"></a>Przygotowanie środowiska Azure Stack Hub

Utwórz subskrypcję centrum Azure Stack. Aby uzyskać więcej informacji, zobacz [Samouczek dotyczący tworzenia subskrypcji centrum Azure Stack.](/azure-stack/user/azure-stack-subscribe-services/)

Jeśli chcesz oszacować Azure Stack centrum na własnym serwerze, możesz użyć Azure Stack Development Kit (ASDK).  Aby uzyskać więcej informacji na temat ASDK, zobacz [Omówienie usługi ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Aby można było uruchomić Azure Stream Analytics w centrum Azure Stack, urządzenie musi mieć IoT Edge środowisko uruchomieniowe i musi mieć łączność sieciową z koncentratorem Azure Stack lub być maszyną wirtualną działającą w centrum Azure Stack. Środowisko uruchomieniowe IoT Edge umożliwia Stream Analytics zadań brzegowych do integracji z usługą Azure Storage i usługą Azure Event Hubs działającą w centrum Azure Stack. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md) 

Oprócz dostępu sieciowego do zasobów centrum Azure Stack IoT Edge urządzenie (lub maszyna wirtualna) potrzebuje dostępu do IoT Hub platformy Azure w chmurze publicznej platformy Azure w celu skonfigurowania modułu Stream Analytics. 

W poniższych przewodnikach pokazano, jak skonfigurować środowisko uruchomieniowe IoT Edge na urządzeniu lub maszynie wirtualnej:

* [Install the Azure IoT Edge runtime on Windows (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemie Windows)](../iot-edge/how-to-install-iot-edge.md)
* [Install the Azure IoT Edge runtime on Debian-based Linux systems (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemach Linux opartych na rozwiązaniu Debian)](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Tworzenie zadania Azure Stream Analytics Edge

Zadania programu ASA Edge są uruchamiane w kontenerach wdrożonych na Azure IoT Edge urządzeniach. Składają się one z dwóch części:
* Część w chmurze odpowiedzialna za definicję zadania: Użytkownicy definiują dane wejściowe, wyjściowe, zapytania i inne ustawienia (zdarzenia poza kolejnością itp.) w chmurze.
* Moduł uruchomiony na urządzeniach IoT. Zawiera aparat ASA i otrzymuje definicję zadania z chmury.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W przypadku tworzenia zadania usługi Azure Stream Analytics do uruchamiania na urządzeniu usługi IoT Edge musi być ono przechowywane w sposób umożliwiający wywoływanie go z urządzenia. Możesz użyć istniejącego konta usługi Azure Storage lub utworzyć nowe.
1. W Azure Portal przejdź do obszaru **Tworzenie zasobu > magazynu > konto magazynu — obiekt BLOB, plik, tabela, kolejka** .
2. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Wartość |
   | --- | --- |
   | Nazwa | Wprowadź unikatową nazwę konta magazynu. |
   | Lokalizacja | Wybierz bliską lokalizację.|
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub.|
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych, które tworzysz podczas [IoT Edge przewodników szybki start](../iot-edge/quickstart.md) i samouczków. Na przykład **IoTEdgeResources** . |

3. Zachowaj wartości domyślne dla pozostałych pól i wybierz pozycję **Utwórz** .


### <a name="create-a-new-job"></a>Tworzenie nowego zadania

1. W Azure Portal przejdź do pozycji **Tworzenie zasobu > Internet rzeczy > Stream Analytics zadanie** .
2. Podaj następujące wartości, aby utworzyć konto magazynu:

   | Pole | Wartość |
   | --- | --- |
   | Nazwa zadania | Podaj nazwę zadania. Na przykład **IoTEdgeJob** |
   | Subskrypcja | Wybierz tę samą subskrypcję co używana dla centrum IoT Hub.|
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych, które tworzysz podczas [IoT Edge przewodników szybki start](../iot-edge/quickstart.md) i samouczków. Na przykład **IoTEdgeResources** . |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Środowisko hostingu | Wybierz pozycję **Edge** . |

3. Wybierz pozycję **Utwórz** .

### <a name="configure-your-job"></a>Konfigurowanie zadania

Po utworzeniu zadania usługi Stream Analytics w witrynie Azure Portal można je skonfigurować za pomocą danych wejściowych, danych wyjściowych i zapytania w celu uruchamiania na danych, które przez nie przechodzą. Możesz ręcznie określić dane wejściowe z IoT Hub lub centrum zdarzeń w ramach subskrypcji centrum Azure Stack.

1. Przejdź do zadania usługi Stream Analytics w witrynie Azure Portal.
2. W obszarze **Konfiguracja** wybierz pozycję **Ustawienia konta magazynu** i wybierz konto magazynu utworzone w poprzednim kroku.
   > [!div class="mx-imgBorder"]
   > [![Ustawienie ](media/on-azure-stack/storage-account-settings.png) konta magazynu zadań](media/on-azure-stack/storage-account-settings.png#lightbox)
3. W obszarze **topologia zadania** wybierz pozycję **wejścia** , a następnie **Dodaj dane wejściowe strumienia.**
4. Z listy rozwijanej wybierz pozycję **IoT Hub** , **centrum zdarzeń** lub **centrum brzegowe** . 
5. Jeśli dane wejściowe są centrum zdarzeń lub IoT Hub w subskrypcji centrum Azure Stack, podaj informacje ręcznie, jak pokazano poniżej.

   #### <a name="event-hub"></a>Centrum zdarzeń

   | Pole | Wartość |
   | --- | --- |
   | Alias danych wejściowych | Przyjazna nazwa używana w zapytaniu zadania do odwoływania się do tych danych wejściowych. |
   | Service Bus przestrzeń nazw | Przestrzeń nazw jest kontenerem dla zestawu jednostek obsługi komunikatów. Podczas tworzenia nowego centrum zdarzeń należy również utworzyć przestrzeń nazw. (Przykład: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com* ) |
   | Nazwa centrum zdarzeń | Nazwa centrum zdarzeń do użycia jako dane wejściowe. |
   | Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego zapewniające dostęp do centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Ta opcja jest wypełniana automatycznie, chyba że zostanie wybrana opcja ręcznego dostarczania ustawień centrum zdarzeń. |
   | Klucz zasad centrum zdarzeń | Współużytkowany klucz dostępu używany do autoryzacji dostępu do centrum zdarzeń. Ta opcja jest wypełniana automatycznie, chyba że zostanie wybrana opcja ręcznego dostarczania ustawień centrum zdarzeń. Można je znaleźć w ustawieniach centrum zdarzeń. |
   | Grupa odbiorców centrum zdarzeń (opcjonalnie) | Zdecydowanie zaleca się użycie odrębnej grupy odbiorców dla każdego zadania Stream Analytics. Ten ciąg identyfikuje grupę odbiorców, która ma być używana do pozyskiwania danych z centrum zdarzeń. Jeśli grupa odbiorców nie zostanie określona, zadanie Stream Analytics używa $Default grupy odbiorców. |
   | Liczba partycji | Liczba partycji to liczba partycji w centrum zdarzeń. |

   > [!div class="mx-imgBorder"]
   > [![Dane wejściowe ](media/on-azure-stack/event-hub-input.png) centrum zdarzeń](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>Usługa IoT Hub

   | Pole | Wartość |
   | --- | --- |
   | Alias danych wejściowych | Przyjazna nazwa używana w zapytaniu zadania do odwoływania się do tych danych wejściowych. |
   | Usługa IoT Hub | Nazwa IoT Hub do użycia jako dane wejściowe. (Przykład: *<IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com* ) |
   | Nazwa zasad dostępu współdzielonego | Zasady dostępu współdzielonego zapewniające dostęp do IoT Hub. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
   | Klucz zasad dostępu współdzielonego | Współużytkowany klucz dostępu używany do autoryzacji dostępu do IoT Hub. Ta opcja jest wypełniana automatycznie, chyba że zostanie wybrana opcja ręcznego dostarczania ustawień Centrum IoT. |
   | Grupa konsumentów (opcjonalnie) | Zdecydowanie zaleca się użycie innej grupy odbiorców dla każdego zadania Stream Analytics. Grupa konsumentów służy do pozyskiwania danych z IoT Hub. Stream Analytics używa grupy konsumentów $Default, chyba że określisz inaczej. |
   | Liczba partycji | Liczba partycji to liczba partycji w centrum zdarzeń. |

   > [!div class="mx-imgBorder"]
   > [![IoT Hub dane wejściowe ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Pozostaw wartości domyślne dla pozostałych pól, a następnie wybierz pozycję Zapisz.
7. W obszarze Topologia zadania otwórz pozycję Dane wyjściowe, a następnie wybierz pozycję Dodaj.
8. Z listy rozwijanej wybierz pozycję Blob Storage, centrum zdarzeń lub centrum brzegowe.
9. Jeśli dane wyjściowe są centrum zdarzeń lub Blob Storage w subskrypcji centrum Azure Stack, podaj informacje ręcznie, jak pokazano poniżej.

   #### <a name="event-hub"></a>Centrum zdarzeń

   | Pole | Wartość |
   | --- | --- |
   | Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego centrum zdarzeń. |
   | Service Bus przestrzeń nazw | Kontener dla zestawu jednostek obsługi komunikatów. Po utworzeniu nowego centrum zdarzeń można również utworzyć przestrzeń nazw usługi Service Bus. (Przykład: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com* ) |
   | Nazwa centrum zdarzeń | Nazwa danych wyjściowych centrum zdarzeń. |
   | Nazwa zasad centrum zdarzeń | Zasady dostępu współdzielonego, które można utworzyć na karcie Konfiguracja centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. |
   | Klucz zasad centrum zdarzeń | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw centrum zdarzeń. |

   > [!div class="mx-imgBorder"]
   > [![Dane wyjściowe ](media/on-azure-stack/event-hub-output.png) centrum zdarzeń](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Pole | Wartość |
   | --- | --- |
   | Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego magazynu obiektów BLOB. |
   | Konto magazynu | Nazwa konta magazynu, do którego wysyłane są dane wyjściowe. (Przykład: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com* ) |
   | Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu. Ta opcja jest wypełniana automatycznie, chyba że zostanie wybrana opcja ręcznego dostarczania ustawień magazynu obiektów BLOB. |

> [!NOTE]
> Format Parquet nie jest obsługiwany w przypadku zadań brzegowych w centrum Azure Stack. W przypadku minimalnych wierszy i maksymalnego czasu należy użyć wartości 0 lub pozostawić je puste.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Wdróż Stream Analytics na maszynie wirtualnej lub urządzeniu podłączonym do Azure Stack

1. W Azure Portal Otwórz IoT Hub. Przejdź do **IoT Edge** i kliknij urządzenie (maszynę wirtualną), które ma być przeznaczone dla tego wdrożenia.
2. Wybierz pozycję **Ustaw moduły** . Następnie wybierz pozycję **+ Dodaj** i wybierz **moduł Azure Stream Analytics** . 
3. Wybierz subskrypcję i utworzone zadanie Edge analizy pary. Kliknij przycisk **Zapisz** i wybierz pozycję **Dalej: trasy** .

   > [!div class="mx-imgBorder"]
   > [![Dodaj moduły ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Kliknij przycisk **Przegląd + utwórz >** .
5. W kroku **Przegląd + tworzenie** wybierz pozycję **Utwórz** . 
   > [!div class="mx-imgBorder"]
   > [![Manifest ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Upewnij się, że moduł został dodany do listy.
   > [!div class="mx-imgBorder"]
   > [![Strona ](media/on-azure-stack/edge-deployment.png) wdrożenia](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Następne kroki
- [Azure Stream Analytics na urządzeniach IoT Edge](./stream-analytics-edge.md)
- [Opracowywanie zadań Stream Analytics Edge](/stream-analytics-query/stream-analytics-query-language-reference)