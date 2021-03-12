---
title: Wdrażanie modułów z poziomu Azure Portal — Azure IoT Edge
description: Użyj IoT Hub w Azure Portal, aby wypchnąć moduł IoT Edge z IoT Hub do urządzenia IoT Edge zgodnie z konfiguracją manifestu wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9248c9578d94b000c04c82b33eeeb089e55a26ef
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200316"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdróż moduły Azure IoT Edge z Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Po utworzeniu modułów IoT Edge za pomocą logiki biznesowej należy wdrożyć je na urządzeniach, aby działały na brzegu. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, możesz wdrożyć je wszystkie jednocześnie i zadeklarować reguły routingu, które je łączą.

W tym artykule pokazano, jak Azure Portal prowadzi użytkownika przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
* Urządzenie IoT Edge.

  Jeśli nie masz skonfigurowanego urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów z przewodnikiem Szybki Start, aby [utworzyć wirtualne urządzenie](quickstart-linux.md) z systemem Linux lub [utworzyć wirtualne urządzenie systemu Windows](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON. Składa się z trzech kroków: **Dodawanie modułów**, **Określanie tras** i **przeglądanie wdrożenia**.

>[!NOTE]
>Kroki opisane w tym artykule odzwierciedlają najnowszą wersję schematu IoT Edge agenta i centrum. Wersja schematu 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza funkcje kolejności uruchamiania modułu i określania priorytetów trasy.
>
>W przypadku wdrażania na urządzeniu z uruchomioną wersją 1.0.9 lub wcześniejszą należy zmodyfikować **Ustawienia środowiska uruchomieniowego** **w kroku w kreatorze, aby** użyć wersji schematu 1,0.

### <a name="select-device-and-add-modules"></a>Wybierz urządzenie i Dodaj moduły

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. W lewym okienku wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **ustawienia Container Registry** na stronie podaj poświadczenia, aby uzyskać dostęp do wszelkich prywatnych rejestrów kontenerów zawierających obrazy modułu.
1. W sekcji **IoT Edge modułów** na stronie wybierz pozycję **Dodaj**.
1. Wybierz jeden z trzech typów modułów z menu rozwijanego:

   * **Moduł IoT Edge** — Podaj nazwę modułu i identyfikator URI obrazu kontenera. Na przykład identyfikator URI obrazu dla przykładowego modułu SimulatedTemperatureSensor to `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Jeśli obraz modułu jest przechowywany w rejestrze kontenera prywatnego, Dodaj poświadczenia na tej stronie, aby uzyskać dostęp do tego obrazu.
   * Moduły **portalu Marketplace** hostowane w witrynie Azure Marketplace. Niektóre moduły portalu Marketplace wymagają dodatkowej konfiguracji, dlatego Przejrzyj szczegóły modułu na liście [modułów IoT Edge portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * Moduły modułów **Azure Stream Analytics** wygenerowane na podstawie obciążenia Azure Stream Analyticsowego.

1. Po dodaniu modułu wybierz z listy nazwę modułu, aby otworzyć ustawienia modułu. Wypełnij pola opcjonalne w razie potrzeby.

   Aby uzyskać więcej informacji na temat dostępnych ustawień modułu, zobacz [Konfiguracja modułu i zarządzanie nimi](module-composition.md#module-configuration-and-management).

   Aby uzyskać więcej informacji na temat sznurka modułu, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

1. Powtórz kroki od 6 do 8, aby dodać kolejne moduły do wdrożenia.
1. Wybierz pozycję **Dalej: trasy** , aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określ trasy

Na karcie **trasy** należy określić sposób przekazywania komunikatów między modułami i IoT Hub. Komunikaty są konstruowane przy użyciu par nazwa/wartość. Domyślnie pierwsze wdrożenie nowego urządzenia obejmuje trasę o nazwie **Route** i zdefiniowana jako **od/messages/ \* do $upstream**, co oznacza, że wszystkie komunikaty przesyłane przez wszystkie moduły są wysyłane do centrum IoT Hub.  

**Priorytet** i **czas wygaśnięcia** parametrów są opcjonalnymi parametrami, które można uwzględnić w definicji trasy. Parametr Priority umożliwia wybranie tras, które powinny być przetwarzane jako pierwsze, lub które trasy powinny być przetwarzane jako ostatnie. Priorytet jest określany przez ustawienie liczby 0-9, gdzie 0 oznacza najwyższy priorytet. Parametr Time to Live umożliwia zadeklarować, jak długo komunikaty w tej trasie powinny być przechowywane, dopóki nie zostaną przetworzone lub usunięte z kolejki.

Aby uzyskać więcej informacji o sposobach tworzenia tras, zobacz [deklarowanie tras](module-composition.md#declare-routes).

Po ustawieniu tras wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do następnego kroku kreatora.

### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzednich dwóch sekcjach. Należy zauważyć, że istnieją dwa moduły zadeklarowane jako niedodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się na [IoT Edge środowisko uruchomieniowe](iot-edge-runtime.md) i są wymagane wartości domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich na stronie Szczegóły urządzenia w IoT Hub. Ta strona zawiera nazwę każdego wdrożonego modułu, a także przydatne informacje, takie jak stan wdrożenia i kod zakończenia.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażanie modułów z witryny Azure Marketplace

[Portal Azure Marketplace](https://azuremarketplace.microsoft.com/) to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane pod kątem działania na platformie Azure, w tym [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Moduł IoT Edge można wdrożyć z poziomu portalu Azure Marketplace i z IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Wdrażanie z poziomu portalu Azure Marketplace

Zapoznania moduły IoT Edge w portalu Marketplace i po znalezieniu, w którym chcesz ją wdrożyć, wybierz pozycję **Utwórz** lub **Pobierz teraz**. Postępuj zgodnie z krokami kreatora wdrażania, które mogą się różnić w zależności od wybranego modułu IoT Edge:

1. Potwierdź warunki użytkowania dostawcy i zasady ochrony prywatności, wybierając pozycję **Kontynuuj**. Konieczne może być podanie informacji kontaktowych.
1. Wybierz swoją subskrypcję i IoT Hub, do której jest dołączone urządzenie docelowe.
1. Wybierz pozycję **Wdróż na urządzeniu**.
1. Wprowadź nazwę urządzenia lub wybierz pozycję **Znajdź urządzenie** , aby przeglądać urządzenia zarejestrowane w centrum.
1. Wybierz pozycję **Utwórz** , aby kontynuować standardowy proces konfigurowania manifestu wdrożenia, łącznie z dodaniem innych modułów w razie potrzeby. Szczegóły dotyczące nowego modułu, takie jak identyfikator URI obrazu, opcje tworzenia i żądane właściwości, są wstępnie zdefiniowane, ale można je zmienić.

Sprawdź, czy moduł został wdrożony w IoT Hub w Azure Portal. Wybierz urządzenie, wybierz pozycję **Ustaw moduły** , a moduł powinien zostać wyświetlony w sekcji **IoT Edge moduły** .

### <a name="deploy-from-azure-iot-hub"></a>Wdrażanie z poziomu usługi Azure IoT Hub

Możesz szybko wdrożyć moduł z portalu Azure Marketplace na urządzeniu w IoT Hub w Azure Portal.

1. W Azure Portal przejdź do IoT Hub.
1. W okienku po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.
1. Wybierz urządzenie IoT Edge, na którym ma zostać odebrane wdrożenie.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **IoT Edge modułów** kliknij pozycję **Dodaj**, a następnie wybierz pozycję **moduł Marketplace** z menu rozwijanego.

![Dodawanie modułu w IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Wybierz moduł na stronie **witryny Marketplace modułu IoT Edge** . Wybrany moduł jest automatycznie konfigurowany dla Twojej subskrypcji, grupy zasobów i urządzenia. Zostanie ona wyświetlona na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji.

> [!TIP]
> Informacje o modułach IoT Edge z usługi Azure IoT Hub są ograniczone. Możesz najpierw dowiedzieć się więcej na temat [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) w portalu Azure Marketplace.

Wybierz kolejno pozycje **Dalej: trasy** i Kontynuuj wdrażanie zgodnie z opisem w temacie [Określanie tras](#specify-routes) i [przeglądanie wdrożenia](#review-deployment) wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak wdrażać i monitorować moduły IoT Edge w odpowiedniej skali](how-to-deploy-at-scale.md)
