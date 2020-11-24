---
title: Omówienie usługi Azure Spatial Anchors
description: Dowiedz się, jak usługa Azure Spatial Anchors ułatwia opracowanie międzyplatformowych środowisk rzeczywistości mieszanej.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: a422371bacddf049365562fce9af7e61f35089a1
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487388"
---
# <a name="azure-spatial-anchors-overview"></a>Omówienie usługi Azure Spatial Anchors

Witamy w usłudze Azure Spatial Anchors. Usługa Azure Spatial Anchors udostępnia deweloperom podstawowe funkcje umożliwiające tworzenie aplikacji rzeczywistości mieszanej z obsługą orientacji przestrzennej. Te aplikacje mogą obsługiwać urządzenia Microsoft HoloLens, urządzenia z systemem iOS wspierające platformę ARKit i urządzenia z systemem Android wspierające platformę ARCore. Usługa Azure Spatial Anchors umożliwia deweloperom pracę z platformami rzeczywistości mieszanej w celu obserwowania przestrzeni, wyznaczania dokładnych punktów orientacyjnych i przywoływania tych punktów orientacyjnych z obsługiwanych urządzeń.
Te dokładne punkty orientacyjne są określane jako kotwice przestrzenne.

![Wiele platform](./media/cross-platform.png)

## <a name="examples"></a>Przykłady

Przykładowe przypadki użycia możliwe dzięki zastosowaniu usługi Spatial Anchors obejmują:

- [Środowiska z wieloma użytkownikami](tutorials/tutorial-share-anchors-across-devices.md). Kotwice przestrzenne platformy Azure ułatwiają osobom w tym samym miejscu uczestnictwo w aplikacjach rzeczywistości mieszanej o różnych użytkownikach. Na przykład dwie osoby mogą uruchomić grę w szachy w rzeczywistości mieszanej, umieszczając wirtualną szachownicę na stole. Następnie, nakierowując swoje urządzenia na stół, mogą wspólnie wyświetlać i wchodzić w interakcje z wirtualną szachownicą.

- [Znajdowanie drogi](concepts/anchor-relationships-way-finding.md). Deweloperzy mogą także łączyć kotwice przestrzenne, tworząc między nimi relacje. Na przykład aplikacja może obejmować środowisko z co najmniej dwoma punktami orientacyjnymi, z którymi użytkownik musi wejść w interakcję, aby ukończyć zadanie. Te punkty orientacyjne mogą być tworzone jako połączone. Później, gdy użytkownik wykonuje zadanie wieloetapowe, aplikacja może zapytać o kotwice znajdujące się w pobliżu bieżącej kotwicy, aby skierować użytkownika do następnego kroku w zadaniu.

- [Utrwalanie wirtualnej zawartości w świecie rzeczywistym](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Aplikacja może umożliwić użytkownikowi umieszczenie wirtualnego kalendarza na ścianie sali konferencyjnej, który użytkownicy mogą zobaczyć za pomocą aplikacji na telefon lub urządzenia HoloLens. W środowisku przemysłowym użytkownik może otrzymywać informacje kontekstowe o maszynie, kierując na nią aparat obsługiwanego urządzenia.

Usługa Azure Spatial Anchors składa się z usługi zarządzanej i zestawów SDK klienta dla obsługiwanych platform urządzeń. Poniższe sekcje zawierają informacje o rozpoczynaniu tworzenia aplikacji przy użyciu usługi Azure Spatial Anchors.

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację z zakotwiczeniami przestrzennymi platformy Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
