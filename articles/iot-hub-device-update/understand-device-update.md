---
title: Wprowadzenie do aktualizacji urządzenia dla platformy Azure IoT Hub | Microsoft Docs
description: Aktualizacja urządzenia dla IoT Hub to usługa, która umożliwia wdrażanie na urządzeniach IoT aktualizacji opartych na środowisku AIR (OTA).
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 60dfd448a66ca67a241f97570c91f683323a7d6d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232379"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Aktualizacja urządzenia dla IoT Hub (wersja zapoznawcza) — Omówienie

Aktualizacja urządzenia dla IoT Hub to usługa, która umożliwia wdrażanie na urządzeniach IoT aktualizacji opartych na środowisku AIR (OTA).

W miarę jak organizacje chcą dodatkowo zapewnić produktywność i wydajność operacyjną, rozwiązania Internet rzeczy (IoT) nadal są wdrażane przy zwiększonych stawkach. To sprawia, że urządzenia tworzące te rozwiązania są oparte na podstawie niezawodności i bezpieczeństwa i są łatwe do nawiązywania połączenia i zarządzania nimi na dużą skalę. Aktualizacja urządzenia dla IoT Hub to kompleksowa platforma, której klienci mogą używać do publikowania, rozpowszechniania i zarządzania aktualizacjami za pośrednictwem środowiska AIR dla wszystkiego z niewielkich czujników na urządzenia na poziomie bramy. 

Aby w pełni wykorzystać zalety transformacji cyfrowej obsługiwanej przez IoT, klienci potrzebują tej możliwości do obsługi, konserwacji i aktualizowania urządzeń w odpowiedniej skali. Poznaj zalety implementowania aktualizacji urządzeń dla IoT Hub, które obejmują szybkie reagowanie na zagrożenia bezpieczeństwa i wdrażanie nowych funkcji w celu uzyskania celów firmy bez ponoszenia dodatkowych kosztów deweloperskich i konserwacyjnych związanych z tworzeniem własnych platform aktualizacji.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Obsługa szerokiego zakresu urządzeń IoT


Aktualizacja urządzenia dla IoT Hub została zaprojektowana w celu oferowania zoptymalizowanego wdrożenia aktualizacji i usprawnień operacji dzięki integracji z [usługą Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/). Dzięki tej integracji można łatwo przyjąć aktualizację urządzenia na dowolnym istniejącym rozwiązaniu. Zapewnia rozwiązanie hostowane w chmurze, które umożliwia połączenie praktycznie dowolnego urządzenia. Aktualizacja urządzenia obsługuje szeroką gamę systemów operacyjnych IoT, w tym Linux i [Azure RTO](https://azure.microsoft.com/en-us/services/rtos/) (system operacyjny w czasie rzeczywistym) — i jest rozszerzalna za pośrednictwem programu typu open source. Firma Microsoft opracowuje aktualizację urządzeń dla ofert IoT Hub za pomocą naszych partnerów półprzewodnikowych, takich jak STMicroelectronics, NXP, Renesas i mikroukład. Zapoznaj się z [przykładami](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) najważniejszych tablic ewaluacyjnych, które zawierają przewodniki wprowadzenie, aby dowiedzieć się, jak konfigurować, kompilować i wdrażać aktualizacje za pośrednictwem środowiska AIR (OTA) na urządzeniach klasy mikrokontrolery. 

Podano zarówno obrazy binarne i Raspberry Pi dotyczące symulatora agenta aktualizacji urządzeń.
Aktualizacja urządzenia dla IoT Hub obsługuje również aktualizowanie urządzeń Azure IoT Edge. Agent aktualizacji urządzeń został udostępniony dla platformy Ubuntu Server 18,04 amd64. Aktualizacja urządzenia dla IoT Hub udostępnia również kod typu open source, jeśli nie uruchomiono jednej z powyższych platform. Możesz przenieść agenta do dystrybucji, która jest uruchomiona.

Aktualizacja urządzenia współpracuje z usługą IoT Plug and Play (PnP) i może zarządzać dowolnym urządzeniem obsługującym wymagane interfejsy PnP. Aby uzyskać więcej informacji, zobacz [Aktualizacja urządzenia dla IoT Hub i IoT Plug and Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Obsługa szerokiej gamy artefaktów aktualizacji

Aktualizacja urządzenia dla IoT Hub obsługuje dwie formy aktualizacji — oparte na obrazach i pakietach.

Aktualizacje oparte na pakiecie są przeznaczonymi do aktualizacji, które zmieniają tylko określony składnik lub aplikację na urządzeniu. Prowadzi to do obniżenia zużycia przepustowości i skrócenia czasu pobierania i instalowania aktualizacji. Aktualizacje pakietu zwykle umożliwiają zmniejszenie przestojów urządzeń podczas stosowania aktualizacji i unikania nakładów tworzenia obrazów.

Aktualizacje obrazów zapewniają wyższy poziom zaufania w stanie końcowym urządzenia. Zwykle łatwiej jest replikować wyniki aktualizacji obrazu między środowiskiem przedprodukcyjnym i środowiskiem produkcyjnym, ponieważ nie ma ona takich samych wyzwań jak pakiety i ich zależności.
Ze względu na ich charakter niepodzielny, jeden może również łatwo przyjmować model trybu failover A/B.

Nie ma żadnej prawej odpowiedzi i można wybrać inaczej w zależności od konkretnych przypadków użycia. Aktualizacja urządzenia dla IoT Hub obsługuje zarówno formularz, jak i pakiet aktualizacji, co pozwala wybrać odpowiedni model aktualizacji dla danego środowiska urządzenia.

## <a name="flexible-features-for-updating-devices"></a>Elastyczne funkcje aktualizowania urządzeń

Aktualizacja urządzenia dla IoT Hub funkcje zapewnia zaawansowane i elastyczne środowisko, w tym:

* ŚRODOWISKO użytkownika zarządzania aktualizacjami zintegrowane z platformą Azure IoT Hub
* Stopniowe wprowadzanie aktualizacji przy użyciu grupowania urządzeń i kontrolek planowania aktualizacji
* Programistyczne interfejsy API umożliwiające obsługę automatyzacji i portalu niestandardowego
* Szybkie aktualizowanie widoków zgodności i stanu w ramach flot urządzeń heterogenicznych
* Obsługa odpornych aktualizacji urządzeń (A/B) w celu zapewnienia bezproblemowego wycofywania
* Opcje subskrypcji i kontroli dostępu opartej na rolach dostępne za pośrednictwem portalu Azure.com
* Oparta na lokalnej pamięci podręcznej zawartości i obsługa zagnieżdżonych krawędzi do włączenia aktualizowania odłączonych urządzeń w chmurze
* Szczegółowe narzędzia do zarządzania aktualizacjami i raportowania 

Dzięki aktualizacji urządzenia do IoT Hub zarządzania i kontroli wdrażania użytkownicy mogą zmaksymalizować produktywność i zaoszczędzić cenny czas. Aktualizacja urządzenia dla IoT Hub obejmuje możliwość grupowania urządzeń i określania, do których urządzeń należy wdrożyć aktualizację. Użytkownicy mogą również wyświetlać stan wdrożeń aktualizacji i upewnić się, że każde urządzenie pomyślnie zastosuje aktualizacje.

W przypadku niepowodzenia aktualizacji aktualizacja urządzenia dla IoT Hub umożliwia użytkownikom zidentyfikowanie urządzeń, które nie zastosowały aktualizacji, a także Zobacz szczegóły dotyczące niepowodzeń. Możliwość zidentyfikowania, które urządzenia nie zostały zaktualizowane, oznacza, że niezliczone godziny ręczne zostały zapisane, próbując zlokalizować źródło.

### <a name="best-in-class-security-at-global-scale"></a>Najlepsze w swojej klasie zabezpieczenia na skalę globalną

Microsoft Azure obsługuje więcej niż miliard urządzeń IoT na całym świecie — liczbę, która rośnie szybko o dzień. Aktualizacja urządzenia dla IoT Hub jest oparta na tym doświadczeniu i sprawdzonej niezawodności wykazanej przez platformę Windows Update, dzięki czemu urządzenia mogą być bezproblemowo aktualizowane na skalę globalną.

Aktualizacja urządzenia dla IoT Hub korzysta z kompleksowego zabezpieczenia chmurowego opracowanego dla Microsoft Azure, dzięki czemu klienci nie muszą poświęcać czasu na ustalenie sposobu, w jaki można to samodzielnie skompilować.


## <a name="device-update-workflows"></a>Przepływy pracy aktualizacji urządzeń

Funkcje aktualizacji urządzeń można podzielić na trzy obszary: integrację, importowanie i zarządzanie agentem.

### <a name="device-update-agent"></a>Agent aktualizacji urządzeń

Po odebraniu na urządzeniu polecenia Update zostanie wykonane żądanie aktualizacji (pobranie, zainstalowanie i zastosowanie). W każdej fazie stan jest zwracany do aktualizacji urządzenia za pośrednictwem IoT Hub, aby można było wyświetlić bieżący stan wdrożenia. Jeśli nie ma żadnych aktualizacji, stan jest zwracany jako "bezczynne". Wdrożenie można anulować w dowolnym momencie.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagram przepływu pracy agenta aktualizacji urządzeń." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Dowiedz się więcej](device-update-agent-overview.md) o agencie aktualizacji urządzeń. 

### <a name="importing"></a>Importowanie

Importowanie polega na tym, jak aktualizacje są pozyskiwane w ramach aktualizacji urządzenia, dzięki czemu można je wdrożyć na urządzeniach. Aktualizacja urządzenia obsługuje wdrażanie jednej aktualizacji na urządzenie. Sprawia to, że jest to idealne rozwiązanie w przypadku aktualizacji w pełnym obrazie, które aktualizują całą partycję systemu operacyjnego jednocześnie, lub manifest apt, który opisuje wszystkie pakiety, które chcesz zaktualizować na urządzeniu. Aby zaimportować aktualizacje do aktualizacji urządzeń, należy najpierw utworzyć manifest importu opisujący aktualizację, przekazać pliki aktualizacji i manifest importu do lokalizacji dostępnej do Internetu. Następnie można użyć Azure Portal lub [interfejsu API REST importowania aktualizacji urządzenia](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) , aby zainicjować asynchroniczny proces importowania aktualizacji. Aktualizacja urządzenia przekazuje pliki, przetwarza je i udostępnia je do dystrybucji na urządzeniach IoT.

W przypadku zawartości poufnej należy chronić pobieranie przy użyciu sygnatury dostępu współdzielonego (SAS, Shared Access Signature), takiego jak wielohoc SAS dla usługi Azure Blob Storage. [Więcej informacji na temat sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagram aktualizacji urządzenia dla IoT Hub importowania przepływu pracy." lightbox="media/understand-device-update/import-update.png":::

[Dowiedz się więcej](import-concepts.md) o importowaniu aktualizacji. 

### <a name="grouping-and-deployment"></a>Grupowanie i wdrażanie

Po zaimportowaniu aktualizacji można wyświetlić zgodne aktualizacje dla urządzeń i klas urządzeń.

Aktualizacja urządzenia obsługuje koncepcje **grup** za pośrednictwem tagów w IoT Hub. Wdrożenie aktualizacji do grupy testowej najpierw jest dobrym sposobem na zmniejszenie ryzyka związanego z problemami w trakcie wprowadzania produkcyjnego.

W ramach aktualizacji urządzenia wdrożenia są sposobem łączenia odpowiedniej zawartości z określonym zestawem zgodnych urządzeń. Aktualizacja urządzenia organizuje proces wysyłania poleceń do poszczególnych urządzeń, co poinstruuje je o pobranie i zainstalowanie aktualizacji oraz pobranie stanu.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagram aktualizacji urządzenia dla przepływu pracy grupowania i wdrażania IoT Hub." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Dowiedz się więcej](device-update-compliance.md) o pojęciach dotyczących wdrażania

[Dowiedz się więcej](device-update-groups.md) o grupach aktualizacji urządzeń


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Utwórz konto i wystąpienie aktualizacji urządzenia](create-device-update-account.md)
