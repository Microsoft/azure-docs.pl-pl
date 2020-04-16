---
title: Wymagania systemowe
description: Wyświetla listę wymagań systemowych dla renderowania zdalnego platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411128"
---
# <a name="system-requirements"></a>Wymagania systemowe

> [!IMPORTANT]
> **Renderowanie zdalne platformy Azure** jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym rozdziale wymieniono minimalne wymagania systemowe do pracy z *renderowaniem zdalnym platformy Azure* (ARR).

## <a name="development-pc"></a>Programistyczny komputer

* Windows 10 w wersji 1903 lub nowszej.
* Aktualne sterowniki graficzne.
* Opcjonalnie: sprzętowy dekoder wideo H265, jeśli chcesz użyć lokalnego podglądu zdalnie renderowanych treści (na przykład w Unity).

> [!IMPORTANT]
> Usługa Windows Update nie zawsze dostarcza najnowsze sterowniki gpu, sprawdź w witrynie producenta procesora graficznego najnowsze sterowniki:
>
> * [Sterowniki AMD](https://www.amd.com/en/support)
> * [Sterowniki Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Sterowniki NVIDIA](https://www.nvidia.com/Download/index.aspx)

W poniższej tabeli wymieniono, które procesory graficzne obsługują sprzętowe dekodowanie wideo H265.

| Producent gpu | Obsługiwane modele |
|-----------|:-----------|
| Nvidia | Sprawdź matrycę **wsparcia NVDEC** [na dole tej strony](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Twój procesor graficzny potrzebuje tak w kolumnie **8-bitowej H.265 4:2:0.** |
| Amd | Procesory graficzne z co najmniej 6 wersją [unified video decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)firmy AMD . |
| Intel | Skylake i nowsze procesory |

Mimo że może być zainstalowany poprawny koder-dekodu H265, właściwości zabezpieczeń bibliotek DLL koder-dekodu mogą powodować błędy inicjowania koder-dekodu. W [przewodniku dotyczącym rozwiązywania problemów](../resources/troubleshoot.md#h265-codec-not-available) opisano kroki, jak rozwiązać ten problem. Problem dll może wystąpić tylko podczas korzystania z usługi w aplikacji klasycznej, na przykład w Unity.

## <a name="devices"></a>Urządzenia

Renderowanie zdalne platformy Azure obecnie obsługuje tylko **HoloLens 2** i pulpit systemu Windows jako urządzenie docelowe. Zobacz sekcję [ograniczenia platformy.](../reference/limits.md#platform-limitations)

Ważne jest, aby używać najnowszego kodeka HEVC, ponieważ nowsze wersje mają znaczną poprawę opóźnienia. Aby sprawdzić, która wersja jest zainstalowana na urządzeniu:

1. Uruchom sklep **Microsoft Store**.
1. Kliknij przycisk **"..."** w prawym górnym rogu.
1. Wybierz **pozycję Pliki do pobrania i aktualizacje**.
1. Wyszukaj na liście **rozszerzeń wideo HEVC od producenta urządzenia**.
1. Upewnij się, że kodek na liście ma co najmniej wersję **1.0.21821.0**.
1. Kliknij przycisk **Pobierz aktualizacje** i poczekaj na jego zainstalowanie.

## <a name="network"></a>Sieć

Stabilne połączenie sieciowe o małym opóźnieniu ma kluczowe znaczenie dla dobrego środowiska użytkownika.

Zobacz specjalny rozdział dotyczący [wymagań sieciowych](../reference/network-requirements.md).

Aby zapoznać się z problemami z siecią, zapoznaj się z [Przewodnikiem rozwiązywania problemów](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Oprogramowanie

Należy zainstalować następujące oprogramowanie:

* Najnowsza wersja **programu Visual Studio 2019** [(do pobrania)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(do pobrania)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(do pobrania)](https://git-scm.com/downloads)
* Opcjonalnie: Aby wyświetlić strumień wideo z serwera na komputerze stacjonarnym, potrzebne są **rozszerzenia wideo HEVC** [(łącze Microsoft Store).](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)

## <a name="unity"></a>Unity

Aby rozwijać się z Unity, zainstaluj

* Unity 2019.3.1 [(do pobrania)](https://unity3d.com/get-unity/download)
* Zainstaluj te moduły w unity:
  * **Uwp** — obsługa kompilacji platformy systemu Windows
  * **IL2CPP** - Obsługa kompilacji systemu Windows (IL2CPP)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: renderowanie modelu za pomocą unity](../quickstarts/render-model.md)
