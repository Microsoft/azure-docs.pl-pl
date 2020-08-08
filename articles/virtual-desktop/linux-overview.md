---
title: Linux Support pulpitu wirtualnego systemu Windows — Azure
description: Krótki przegląd obsługi systemu Linux dla pulpitu wirtualnego Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008699"
---
# <a name="linux-support"></a>Obsługa systemu Linux

Można uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z urządzeń z systemem Linux z następującymi obsługiwanymi klientami dostarczonymi przez naszych partnerów klientów z systemem Linux. Pracujemy z wieloma partnerami, aby umożliwić obsługę klientów klasycznych systemu Windows w większej liczbie systemów operacyjnych i urządzeń z systemem Linux. Jeśli chcesz, aby obsługa pulpitu wirtualnego systemu Windows była obsługiwana na platformie z systemem Linux, która nie jest wymieniona w tym miejscu, poinformuj nas o naszej [stronie usługi UserVoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Nawiązywanie połączenia z urządzeniem z systemem Linux

Następujący partnerzy mają zatwierdzonych klientów pulpitu wirtualnego systemu Windows dla urządzeń z systemem Linux.

|Partner|Dokumentacja dotycząca partnerów|Pomoc techniczna dla partnerów|
|:------|:--------------------|:--------------|
|![Logo IGEL](./media/partners/igel.png)|[Dokumentacja klienta IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Obsługa IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Co to jest zestaw SDK systemu Linux?

Partnerzy dla klientów z systemem Linux mogą używać interfejsów API zestawu Windows Virtual Desktop Linux SDK do pobierania źródeł zasobów, łączenia się z sesjami aplikacji klasycznych i zdalnych oraz korzystać z wielu przekierowań obsługiwanych przez naszych klientów pierwszej firmy. Zestaw SDK jest zgodny z większością systemów operacyjnych opartych na systemie Ubuntu 18,04 lub nowszym.

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

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z naszą dokumentacją dla następujących klientów:

- [Klient klasyczny systemu Windows](connect-windows-7-10.md)
- [Klient internetowy](connect-web.md)
- [Klient systemu Android](connect-android.md)
- [Klient systemu macOS](connect-macos.md)
- [Klient systemu iOS](connect-ios.md)
