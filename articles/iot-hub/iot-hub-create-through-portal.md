---
title: Użyj Azure Portal, aby utworzyć IoT Hub | Microsoft Docs
description: Sposób tworzenia i usuwania centrów Azure IoT Hub oraz zarządzania nimi za pomocą Azure Portal. Zawiera informacje o warstwach cenowych, skalowaniu, zabezpieczeniach i konfiguracji obsługi komunikatów.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536049"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Tworzenie Centrum IoT Hub przy użyciu Azure Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

W tym artykule opisano sposób tworzenia centrów IoT i zarządzania nimi przy użyciu [Azure Portal](https://portal.azure.com).

Aby skorzystać z kroków opisanych w tym samouczku, potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Zmień ustawienia Centrum IoT Hub

Ustawienia istniejącego Centrum IoT można zmienić po jego utworzeniu w okienku IoT Hub.

![Zrzut ekranu przedstawiający Ustawienia Centrum IoT](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Poniżej przedstawiono niektóre właściwości, które można ustawić dla Centrum IoT Hub:

**Cennik i skala**: można użyć tej właściwości do migracji do innej warstwy lub ustawienia liczby jednostek IoT Hub. 

**Monitorowanie operacji**: Włącz lub Wyłącz różne kategorie monitorowania, takie jak rejestrowanie zdarzeń związanych z komunikatami z urządzenia do chmury lub komunikatami z chmury do urządzenia.

**Filtr IP**: Określ zakres adresów IP, które zostaną zaakceptowane lub odrzucone przez Centrum IoT.

**Właściwości**: zawiera listę właściwości, które można kopiować i używać w innym miejscu, takich jak identyfikator zasobu, Grupa zasobów, lokalizacja itd.

### <a name="shared-access-policies"></a>Zasady dostępu współużytkowanego

Możesz również wyświetlić lub zmodyfikować listę zasad dostępu współdzielonego, klikając pozycję **zasady dostępu współużytkowanego** w sekcji **Ustawienia** . Te zasady definiują uprawnienia dla urządzeń i usług w celu nawiązania połączenia z usługą IoT Hub. 

Kliknij przycisk **Dodaj** , aby otworzyć blok **Dodawanie zasad dostępu współdzielonego** .  Możesz wprowadzić nową nazwę zasad i uprawnienia, które mają być skojarzone z tymi zasadami, jak pokazano na poniższej ilustracji:

![Zrzut ekranu przedstawiający Dodawanie zasad dostępu współdzielonego](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Zasady **zapisu** **Odczytaj** i Zapisz rejestru przyznają prawa dostępu do odczytu i zapisu w rejestrze tożsamości. Te uprawnienia są używane przez usługi zaplecza w chmurze do zarządzania tożsamościami urządzeń. Wybranie opcji zapisu powoduje automatyczne wybranie opcji odczytu.

* Zasady **łączenia usług** przyznają uprawnienia dostępu do punktów końcowych usługi. To uprawnienie jest używane przez usługi w chmurze zaplecza do wysyłania i odbierania komunikatów z urządzeń oraz do aktualizowania i odczytywania danych z sznurka i modułu.

* Zasady **łączenia urządzeń** przyznają uprawnienia do wysyłania i otrzymywania komunikatów przy użyciu IoT Hub punktów końcowych po stronie urządzenia. To uprawnienie jest używane przez urządzenia do wysyłania i odbierania komunikatów z usługi IoT Hub, aktualizowania i odczytywania danych z sznurka i modułu, a następnie wykonywania operacji przekazywania plików.

Kliknij przycisk **Utwórz** , aby dodać nowo utworzone zasady do istniejącej listy.

Aby uzyskać bardziej szczegółowe informacje o dostępie udzielonym przez określone uprawnienia, zobacz [IoT Hub uprawnienia](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Routing komunikatów dla Centrum IoT

Kliknij pozycję **Routing komunikatów** w obszarze **Obsługa** komunikatów, aby wyświetlić okienko routing wiadomości, w którym można definiować trasy i niestandardowe punkty końcowe dla centrum. [Routing komunikatów](iot-hub-devguide-messages-d2c.md) umożliwia zarządzanie sposobem wysyłania danych z urządzeń do punktów końcowych. Pierwszym krokiem jest dodanie nowej trasy. Następnie można dodać istniejący punkt końcowy do trasy lub utworzyć nowy jeden z obsługiwanych typów, takich jak usługa BLOB Storage. 

![Okienko routingu wiadomości](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Trasy

Trasy to pierwsza karta w okienku routing wiadomości. Aby dodać nową trasę, kliknij pozycję +**Dodaj**. Zobaczysz poniższy ekran. 

![Zrzut ekranu przedstawiający Dodawanie nowej trasy](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nadaj nazwę trasie. Nazwa trasy musi być unikatowa na liście tras dla tego centrum. 

W przypadku **punktu końcowego** można wybrać jeden z listy rozwijanej lub dodać nowy. W tym przykładzie konto magazynu i kontener są już dostępne. Aby dodać je jako punkt końcowy, kliknij przycisk +**Dodaj** obok listy rozwijanej punkt końcowy i wybierz pozycję **BLOB Storage**. Na poniższym ekranie przedstawiono, gdzie określono konto magazynu i kontener.

![Zrzut ekranu przedstawiający Dodawanie punktu końcowego magazynu dla reguły routingu](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Kliknij pozycję **Wybierz kontener** , aby wybrać konto magazynu i kontener. Po wybraniu tych pól wraca do okienka punktów końcowych. Użyj wartości domyślnych dla pozostałych pól i **Utwórz** , aby utworzyć punkt końcowy dla konta magazynu i dodać go do reguł routingu.

W obszarze **Źródło danych** wybierz pozycję komunikaty telemetryczne urządzenia. 

Następnie Dodaj zapytanie routingu. W tym przykładzie komunikaty, które mają właściwość aplikacji o nazwie `level` o wartości równej, `critical` są kierowane do konta magazynu.

![Zrzut ekranu przedstawiający zapisywanie nowej reguły routingu](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Kliknij przycisk **Zapisz** , aby zapisać regułę routingu. Powrócisz do okienka routing wiadomości i zostanie wyświetlona Nowa reguła routingu.

### <a name="custom-endpoints"></a>Niestandardowe punkty końcowe

Kliknij kartę **niestandardowe punkty końcowe** . Zobaczysz już utworzone niestandardowe punkty końcowe. W tym miejscu możesz dodać nowe punkty końcowe lub usunąć istniejące punkty końcowe. 

> [!NOTE]
> Po usunięciu trasy nie są usuwane punkty końcowe przypisane do tej trasy. Aby usunąć punkt końcowy, kliknij kartę niestandardowe punkty końcowe, wybierz punkt końcowy, który chcesz usunąć, a następnie kliknij przycisk Usuń.
>

Więcej informacji na temat niestandardowych punktów końcowych można znaleźć w [punktach końcowych programu Reference — IoT Hub](iot-hub-devguide-endpoints.md).

Można zdefiniować maksymalnie 10 niestandardowych punktów końcowych dla Centrum IoT Hub. 

Aby zobaczyć pełny przykład korzystania z niestandardowych punktów końcowych z routingiem, zobacz [Routing komunikatów z IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Znajdowanie określonego Centrum IoT Hub

Poniżej przedstawiono dwa sposoby znajdowania określonego Centrum IoT Hub w ramach subskrypcji:

1. Jeśli znasz grupę zasobów, do której należy Centrum IoT, kliknij pozycję **grupy zasobów**, a następnie wybierz z listy grupę zasobów. Na ekranie Grupa zasobów są wyświetlane wszystkie zasoby z tej grupy, w tym centra IoT Hub. Kliknij centrum, którego szukasz.

2. Kliknij pozycję **Wszystkie zasoby**. W okienku **wszystkie zasoby** znajduje się lista rozwijana, która ma wartość domyślną `All types` . Kliknij listę rozwijaną, a następnie usuń zaznaczenie `Select all` . Znajdź `IoT Hub` i sprawdź. Kliknij pole listy rozwijanej, aby je zamknąć, a wpisy zostaną przefiltrowane, pokazując tylko centra IoT.

## <a name="delete-the-iot-hub"></a>Usuwanie Centrum IoT Hub

Aby usunąć Centrum IoT Hub, Znajdź Centrum IoT, które chcesz usunąć, a następnie kliknij przycisk **Usuń** poniżej nazwy Centrum IoT.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Routing komunikatów za pomocą IoT Hub](tutorial-routing.md)
* [Monitorowanie Centrum IoT Hub](monitor-iot-hub.md)
