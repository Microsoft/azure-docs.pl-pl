---
title: Wymagania systemowe
description: Zawiera listę wymagań systemowych dla renderowania zdalnego na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 536f5e7d2fa12d1f9a9f09a31b463d2096dcad37
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207414"
---
# <a name="system-requirements"></a>Wymagania systemowe

> [!IMPORTANT]
> **Zdalne renderowanie na platformie Azure** jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten rozdział zawiera listę minimalnych wymagań systemowych, które należy wykonać, aby korzystać z *zdalnego renderowania platformy Azure* (ARR).

## <a name="development-pc"></a>KOMPUTER deweloperski

* Windows 10 w wersji 1903 lub nowszej.
* Aktualne sterowniki grafiki.
* Opcjonalnie: [H265 sprzętowy dekoder wideo](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7), jeśli chcesz użyć lokalnej wersji zapoznawczej zdalnie renderowanej zawartości (na przykład w aparacie Unity).

> [!IMPORTANT]
> Usługi Windows Update nie zawsze dostarczają najnowszych sterowników procesora GPU, należy sprawdzić witrynę sieci Web producenta procesora, aby uzyskać najnowsze sterowniki:
>
> * [Sterowniki AMD](https://www.amd.com/en/support)
> * [Sterowniki firmy Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Sterowniki NVIDIA](https://www.nvidia.com/Download/index.aspx)

Poniższa tabela zawiera listę procesorów, które obsługują sprzętowe dekodowanie wideo H265.

| Producent procesora GPU | Obsługiwane modele |
|-----------|:-----------|
| GRAFICZNY | Zapoznaj się z **macierzą pomocy technicznej NVDEC** [w dolnej części tej strony](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Procesor GPU potrzebuje tak w kolumnie **H. 265 4:2:0 8-bitowej** . |
| PROCESORÓW | Procesory GPU z co najmniej wersją 6 [ujednoliconego dekodera wideo](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)firmy AMD. |
| Stronę | Skylake i nowsze procesory CPU |

Mimo że może być zainstalowany poprawny koder-dekoder H265, właściwości zabezpieczeń w bibliotekach DLL koderów-dekoder mogą spowodować błędy inicjowania koderów [Przewodnik rozwiązywania problemów](../resources/troubleshoot.md#h265-codec-not-available) zawiera opis kroków rozwiązywania tego problemu. Problem z biblioteką DLL może wystąpić tylko w przypadku korzystania z usługi w aplikacji klasycznej na przykład w środowisku Unity.

## <a name="devices"></a>Urządzenia

Zdalne renderowanie na platformie Azure obecnie obsługuje tylko urządzenia **HoloLens 2** i Windows Desktop jako urządzenie docelowe. Zapoznaj się z sekcją [ograniczenia dotyczące platformy](../reference/limits.md#platform-limitations) .

Ważne jest, aby użyć najnowszego kodera HEVC, ponieważ w nowszych wersjach wprowadzono znaczne ulepszenia opóźnienia. Aby sprawdzić, która wersja jest zainstalowana na urządzeniu:

1. Uruchom **Microsoft Store**.
1. Kliknij przycisk **"..."** w prawym górnym rogu.
1. Wybierz pozycję **pobrania i aktualizacje**.
1. Przeszukaj listę **rozszerzeń wideo HEVC od producenta urządzenia**. Jeśli ten element nie jest wymieniony w obszarze aktualizacje, Najnowsza wersja jest już zainstalowana.
1. Upewnij się, że na wymienionym dekoderze nie ma co najmniej wersji **1.0.21821.0**.
1. Kliknij przycisk **Pobierz aktualizacje** i poczekaj na jego instalację.

## <a name="network"></a>Sieć

Stabilne połączenie sieciowe o małym opóźnieniu ma kluczowe znaczenie dla dobrego środowiska użytkownika.

Zapoznaj się z tematem specjalny rozdział [wymagań sieci](../reference/network-requirements.md).

Rozwiązywanie problemów z siecią można znaleźć w [przewodniku rozwiązywania problemów](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-ports"></a>Porty sieciowe

Upewnij się, że zapory (na urządzeniu, wewnątrz routerów itp.) nie blokują następujących portów:

| Port              | Protokół | Zezwalaj    | Opis |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Przeznaczony | Połączenie początkowe (uzgadnianie HTTP) |
| 8266              | UDP      | Przeznaczony | Transfer danych |
| 5000, 5433, 8443  | TCP      | Przeznaczony | Wymagane dla [Narzędzia ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Oprogramowanie

Należy zainstalować następujące oprogramowanie:

* Najnowsza wersja programu **Visual Studio 2019** [(pobieranie)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools dla rzeczywistości mieszanej](/windows/mixed-reality/install-the-tools). W szczególnych przypadkach następujące instalacje *obciążenia* są obowiązkowe:
  * **Programowanie aplikacji klasycznych w języku C++**
  * **Programowanie platforma uniwersalna systemu Windows (platformy UWP)**
* **Windows SDK 10.0.18362.0** [(pobieranie)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(pobieranie)](https://git-scm.com/downloads)
* Opcjonalnie: Aby wyświetlić strumień wideo z serwera na komputerze stacjonarnym, potrzebne są **rozszerzenia wideo HEVC** [(Link Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Upewnij się, że Najnowsza wersja jest zainstalowana, sprawdzając aktualizacje w sklepie.

## <a name="unity"></a>Unity

W przypadku programowania przy użyciu aparatu Unity Zainstaluj

* 2019.3.1 Unity [(pobieranie)](https://unity3d.com/get-unity/download)
* Zainstaluj te moduły w aparacie Unity:
  * **Platformy UWP** — obsługa kompilacji platforma uniwersalna systemu Windows
  * **IL2CPP** — obsługa kompilacji systemu Windows (IL2CPP)

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)