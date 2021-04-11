---
title: Informacje o integracji Forescout
description: Integracja usługi Azure Defender for IoT z platformą Forescout zapewnia nowy poziom scentralizowanego wglądu, monitorowania i kontroli dla IoT i niekrajobrazu.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784071"
---
# <a name="about-the-forescout-integration"></a>Informacje o integracji Forescout

Usługa Azure Defender for IoT oferuje usługę ICS i IoT cyberbezpieczeństwa platformę opracowaną przez ekspertów dla zespołów z niebieską grupą. Usługa Defender for IoT to jedyna platforma z opatentowaną analizą zagrożeń i uczeniem maszynowym. Usługa Defender for IoT oferuje następujące informacje:

- Natychmiastowy wgląd w szczegółowe informacje o tym, jak usługa ICS, ma szeroki zakres szczegółowych informacji o atrybutach.
- Oparta na usłudze ICS Szczegółowa baza wiedzy o protokołach, urządzeniach, aplikacjach i ich zachowaniach.
- Natychmiastowe uzyskiwanie szczegółowych informacji na temat luk w zabezpieczeniach oraz znanych i zerowych zagrożeń.
- Automatyczna Technologia modelowania zagrożeń dla usługi ICS do przewidywania najbardziej najprawdopodobniej ścieżek skierowanych do nich ataków usługi ICS za pośrednictwem własnej analizy.

Integracja usługi Defender for IoT z platformą Forescout zapewnia nowy poziom scentralizowanej widoczności, monitorowania i kontroli na potrzeby IoT i niekrajobrazu.

Te platformy połączone umożliwiają automatyczne wgląd i zarządzanie urządzeniami do wcześniej nieosiągalnych urządzeń ICS i przepływów pracy z silosami.

Integracja zapewnia analitykom SOC z wielopoziomowym wglądem w protokoły wdrożone w środowiskach przemysłowych. Szczegóły są dostępne dla elementów, takich jak oprogramowanie układowe, typy urządzeń, systemy operacyjne i oceny ryzyka na podstawie własnościowej usługi Azure Defender dla technologii IoT.

> [!Note]
> Odwołania do CyberX odnoszą się do usługi Azure Defender dla IoT.
## <a name="devices"></a>Urządzenia

### <a name="device-visibility-and-management"></a>Widoczność i zarządzanie urządzeniami

Spis urządzenia jest wzbogacany o atrybuty krytyczne wykryte przez usługę Defender for IoT. Dzięki temu można:

- Zyskaj kompleksowy i ciągły wgląd w urządzenia z jednym okienkiem Glass.
- Uzyskiwanie analizy w czasie rzeczywistym informacji o zagrożeniach.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Spis urządzeń":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Szczegóły urządzenia":::

### <a name="device-control"></a>Kontrola urządzenia

Integracja z usługą Forescout pozwala skrócić czas wymagany dla organizacji infrastruktury przemysłowej i krytycznej do wykrywania i reagowania na zagrożenia cybernetycznymi.

- Aby zamknąć cykl zabezpieczeń, należy użyć usługi Azure Defender do analizy urządzeń IoT, co powoduje wyzwolenie akcji zasad Forescout. Na przykład możesz automatycznie wysyłać wiadomości e-mail z alertami do administratorów SOC po wykryciu określonych protokołów lub zmianie szczegółów oprogramowania układowego.

- Skorelować usługę Defender dla informacji IoT z innymi modułami *EyeExtended Forescout* , które nadzorują monitorowanie, zarządzanie zdarzeniami i sterowanie urządzeniami.

## <a name="system-requirements"></a>Wymagania systemowe

- Usługa Azure Defender dla IoT w wersji 2,4 lub nowszej
- Forescout w wersji 8,0 lub nowszej
- Licencja na moduł *Forescout eyeExtend* dla platformy Azure Defender for IoT.

### <a name="getting-more-forescout-information"></a>Więcej informacji Forescout

Aby uzyskać więcej informacji na temat platformy Forescout, zobacz [Centrum zasobów Forescout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Konfiguracja systemu

W tym artykule opisano sposób konfigurowania komunikacji między platformą Defender for IoT i platformą Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Konfigurowanie usługi Defender for IoT

Aby zapewnić komunikację z usługi Defender for IoT z usługą Forescout, wygeneruj token dostępu w usłudze Defender for IoT.

Tokeny dostępu umożliwiają zewnętrznym systemom dostęp do danych wykrytych przez usługę Defender for IoT i wykonywanie akcji z tymi danymi przy użyciu zewnętrznego interfejsu API REST za pośrednictwem połączeń SSL. Można generować tokeny dostępu w celu uzyskania dostępu do usługi Azure Defender dla interfejsu API REST.

Aby wygenerować token:

1. Zaloguj się do usługi Defender for IoT sensor, który będzie pytany przez Forescout.

1. Wybierz pozycję **Ustawienia systemowe** , a następnie wybierz pozycję **tokeny dostępu** w sekcji **Ogólne** . Zostanie otwarte okno dialogowe **tokeny dostępu** .
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Tokeny dostępu":::
1. W oknie dialogowym **tokeny dostępu** wybierz pozycję **Generuj nowy token** .
1. Wprowadź opis tokenu w oknie dialogowym **nowy token dostępu** .
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nowy token dostępu":::
1. Wybierz opcję **Dalej**. Token jest wyświetlany w oknie dialogowym. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Wyświetl token":::
   > [!NOTE]
   > *Zapisz token w bezpiecznym miejscu. Będzie on potrzebny podczas konfigurowania platformy Forescout*.
1. Wybierz pozycję **Zakończ**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Zakończ Dodawanie tokenu":::

### <a name="set-up-the-forescout-platform"></a>Skonfiguruj platformę Forescout

Można skonfigurować platformę Forescout do komunikacji z usługą Defender dla czujnika IoT.

Aby skonfigurować:

1. Zainstaluj *moduł Forescout eyeExtend dla CyberX* na platformie Forescout.

1. Zaloguj się do konsoli przeciwdziałania i wybierz **Opcje** z menu **Narzędzia** . Zostanie otwarte okno dialogowe **Opcje** .

1. Przejdź do folderu **modułów** i wybierz go.

1. W okienku **moduły** wybierz pozycję **CyberX platform**. Zostanie otwarte okienko usługi Defender for IoT.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender dla ustawień modułu IoT":::

   Wprowadź następujące informacje:

   - **Adres serwera** — wprowadź adres IP usługi Defender for IoT sensor, który będzie pytany przez urządzenie Forescout.
   - **Token dostępu** — wprowadź token dostępu wygenerowany dla czujnika usługi Defender for IoT, który będzie łączył się z urządzeniem Forescout. Aby wygenerować token, zobacz [Konfigurowanie usługi Defender for IoT](#set-up-the-defender-for-iot-platform).

1. Wybierz przycisk **Zastosuj**.

Jeśli chcesz, aby platforma Forescout mogła komunikować się z innym czujnikiem:

1. Utwórz nowy token dostępu w odpowiedniej usłudze Defender dla czujnika IoT.

1. W oknie dialogowym Platforma **Forescout modułów**  >  **CyberX** :

   - Usuń wyświetlane informacje.
   
   - Wprowadź nowy adres IP czujnika i informacje o nowym tokenie dostępu.

### <a name="verify-communication"></a>Weryfikowanie komunikacji

Po skonfigurowaniu usługi Defender for IoT i Forescout Otwórz okno dialogowe tokeny dostępu czujnika w usłudze Defender for IoT.

Jeśli w **używanym** polu dla tego tokenu zostanie wyświetlona wartość **N/A** , połączenie między czujnikiem i urządzeniem Forescout nie działa.

**Używany** wskazuje czas ostatniego wywołania zewnętrznego z tym tokenem.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Sprawdza, czy token został odebrany":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Wyświetlanie usługi Defender na potrzeby wykrywania IoT w programie Forescout

Aby wyświetlić atrybuty urządzenia:

1. Zaloguj się do platformy Forescout, a następnie przejdź do **spisu zasobów**.

1. Przejdź do **platformy CyberX**. Następujące atrybuty urządzenia są wyświetlane dla urządzeń niewykrywanych przez usługę Defender for IoT.

   | Element | Opis |
   |--|--|
   | Autoryzowane przez usługę Azure Defender dla IoT | Urządzenie wykryte w sieci przez usługę Defender for IoT w okresie uczenia sieci. |
   | Oprogramowanie układowe | Szczegóły oprogramowania układowego urządzenia. Na przykład, model i szczegóły wersji. |
   | Nazwa | Nazwa urządzenia. |
   | System operacyjny | System operacyjny urządzenia. |
   | Typ | Typ urządzenia. Na przykład stacja PLC, Historian lub inżynieryjna. |
   | Dostawca | Dostawca urządzenia. Na przykład Automatyzacja Rockwell. |
   | Poziom ryzyka | Poziom ryzyka obliczony przez usługę Defender for IoT. |
   | Protokoły | Protokoły wykryte w ruchu generowanym przez urządzenie. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Wyświetlanie atrybutów oprogramowania układowego.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Wyświetlanie atrybutów dostawców.":::

### <a name="viewing-more-details"></a>Wyświetlanie większej ilości szczegółów

Wyświetl dodatkowe informacje o urządzeniu dla urządzeń kierowanych przez usługę Defender for IoT. Na przykład Forescout zgodność i informacje dotyczące zasad.

Aby to osiągnąć, kliknij prawym przyciskiem myszy urządzenie w sekcji **hosty spisu urządzeń** . Zostanie otwarte okno dialogowe Szczegóły hosta z dodatkowymi informacjami.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Szczegóły hosta":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Tworzenie zasad usługi Azure Defender dla IoT w Forescout

Zasady Forescoutymi mogą służyć do automatyzowania kontroli i zarządzania urządzeniami wykrytymi przez usługę Defender for IoT. Na przykład

- Po wykryciu określonych wersji oprogramowania układowego automatycznie wysyła pocztą e-mail administratorów SOC.

- Dodaj konkretną usługę Defender dla urządzeń z wykrytymi usługami IoT do grupy Forescout, aby obsłużyć dalsze obsługiwanie przepływów pracy zdarzeń i zabezpieczeń, na przykład z innymi integracją SIEM.

Utwórz zasady niestandardowe Forescout z użyciem usługi Defender for IoT przy użyciu właściwości warunku.

Aby uzyskać dostęp do właściwości usługi Defender dla usługi IoT:

1. W oknie dialogowym **warunki zasad** przejdź do **drzewa właściwości** .

1. Rozwiń folder **platform CyberX** w **drzewie Właściwości**. Dostępne są następujące właściwości usługi Defender for IoT.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Właściwości":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
