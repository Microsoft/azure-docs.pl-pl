---
title: Linux Support pulpitu wirtualnego systemu Windows — Azure
description: Krótki przegląd obsługi systemu Linux dla pulpitu wirtualnego Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422418"
---
# <a name="linux-support"></a>Obsługa systemu Linux

Partnerzy mogą korzystać z zestawu SDK dla systemu Linux dla klasycznego pulpitu wirtualnego systemu Windows. Można go również użyć do włączenia obsługi pulpitu wirtualnego systemu Windows w aplikacji klienckiej. Ten krótki przewodnik wyjaśnia, co to jest zestaw Linux SDK i jak zacząć z niego korzystać.

## <a name="connect-with-your-linux-device"></a>Nawiązywanie połączenia z urządzeniem z systemem Linux

Następujący partnerzy mają zatwierdzonych klientów pulpitu wirtualnego systemu Windows dla urządzeń z systemem Linux.

|Partner|Dokumentacja dotycząca partnerów|Pomoc techniczna dla partnerów|
|:------|:--------------------|:--------------|
|![Logo IGEL](./media/partners/igel.png)|[Dokumentacja klienta IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Obsługa IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Co to jest zestaw SDK systemu Linux?

Za pomocą interfejsów API zestawu SDK można pobierać źródła zasobów, łączyć się z sesjami pulpitu lub aplikacji zdalnych oraz korzystać z wielu przekierowań, które obsługują Klienci pierwszej firmy.

> [!NOTE]
> Zestaw SDK jest obecnie w trakcie opracowywania. Zaktualizujemy ten dokument przy użyciu instrukcji, aby uzyskać dostęp do zestawu SDK, gdy jest on ogólnie dostępny.

### <a name="supported-linux-distributions"></a>Obsługiwane dystrybucje systemu Linux

Zestaw SDK jest zgodny z większością systemów operacyjnych opartych na systemie Ubuntu 18,04 lub nowszym. Jeśli korzystasz z innej dystrybucji systemu Linux, możemy współpracować z nim, aby dowiedzieć się, jak najlepiej obsługiwać Twoje potrzeby.

### <a name="feature-support"></a>Obsługa funkcji

Zestaw SDK obsługuje wiele połączeń do sesji pulpitu i aplikacji zdalnych. Obsługiwane są następujące przekierowania:

| Przekierowania       | Obsługiwane |
| :---------------- | :-------: |
| Klawiatura          | &#10004;  |
| Mysz             | &#10004;  |
| Dźwięk w          | &#10004;  |
| Wyjście audio         | &#10004;  |
| Schowek (tekst)  | &#10004;  |
| Schowek (obraz) | &#10004;  |
| Schowek (plik)  | &#10004;  |
| Kart         | &#10004;  |
| Dysk/folder      | &#10004;  |

Zestaw SDK obsługuje również wiele konfiguracji wyświetlania monitorów, o ile monitory wybrane dla sesji są ciągłe.

Ten dokument zostanie zaktualizowany w miarę dodawania obsługi nowych funkcji i przekierowań. Jeśli chcesz zasugerować nowe funkcje i inne ulepszenia, odwiedź naszą [stronę usługi UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Wprowadzenie do zestawu SDK dla systemu Linux

Przed rozpoczęciem opracowywania klienta systemu Linux dla pulpitu wirtualnego z systemem Windows należy wykonać następujące czynności:

1. Kompiluj i wdrażaj środowisko pulpitu wirtualnego systemu Windows na potrzeby testowania lub użycia produkcyjnego.
2. Przetestuj dostępnych klientów pierwszej firmy, aby zapoznać się z interfejsem użytkownika pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszą dokumentacją dla następujących klientów:

- [Klient klasyczny systemu Windows](connect-windows-7-and-10.md)
- [Klient sieci Web](connect-web.md)
- [Klient systemu Android](connect-android.md)
- [Klient macOS](connect-macos.md)
- [Klient systemu iOS](connect-ios.md)
