---
title: Narzędzie do inspekcji ArrInspector
description: Podręcznik użytkownika narzędzia ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723829"
---
# <a name="the-arrinspector-inspection-tool"></a>Narzędzie do inspekcji ArrInspector

ArrInspector to narzędzie oparte na sieci Web służące do sprawdzania uruchomionej sesji renderowania zdalnego platformy Azure. Ma być używany do celów debugowania, do sprawdzania struktury renderowanej sceny, wyświetlania komunikatów dziennika i monitorowania wydajności na żywo na serwerze.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Łączenie z ArrInspector

Po uzyskaniu nazwy hosta (kończącej `mixedreality.azure.com` ) serwera z ARR, Połącz się za pomocą [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Ta funkcja tworzy `StartArrInspector.html` na urządzeniu, na którym działa aplikacja. Aby uruchomić ArrInspector, otwórz ten plik za pomocą przeglądarki (Edge, Firefox lub Chrome) na komputerze. Plik jest prawidłowy tylko przez 24 godziny.

Jeśli aplikacja, która wywołuje program, `ConnectToArrInspectorAsync` jest już uruchomiona na komputerze:

* Jeśli używasz integracji aparatu Unity, może ona zostać automatycznie uruchomiona.
* W przeciwnym razie plik zostanie znaleziony w *folderach użytkowników \\ LocalAppData \\ [your_app] \\ AC \\ temp*.

Jeśli aplikacja działa na serwerze HoloLens:

1. Dostęp do urządzenia HoloLens przy użyciu [portalu urządzeń z systemem Windows](/windows/mixed-reality/using-the-windows-device-portal).
1. Przejdź do *Eksploratora systemu > plików*.
1. Przejdź do *folderów użytkowników \\ LocalAppData \\ [your_app] \\ AC \\ temp*.
1. Zapisz *StartArrInspector.html* na komputerze.
1. Otwórz *StartArrInspector.html* , aby załadować ArrInspector sesji.

## <a name="the-performance-panel"></a>Panel wydajności

![Panel wydajności](./media/performance-panel.png)

Ten panel przedstawia wykresy wszystkich wartości wydajności poszczególnych klatek uwidocznionych przez serwer. Obecnie wartości obejmują czas klatki, FPS, użycie procesora CPU i pamięci, statystyki pamięci, takie jak całkowite użycie pamięci RAM, liczniki obiektów itd.

Aby wizualizować jeden z tych parametrów, kliknij przycisk **Dodaj nowy** i wybierz jedną z dostępnych wartości wyświetlanych w oknie dialogowym. Ta akcja dodaje nowy wykres przewijania do panelu, śledząc wartości w czasie rzeczywistym. Po prawej stronie można zobaczyć *minimalną*, *maksymalną* i *bieżącą* wartość.

Możesz przesunąć wykres, przeciągając jego zawartość za pomocą myszy, jednak przesuwanie w poziomie jest możliwe tylko wtedy, gdy ArrInspector jest w stanie wstrzymania.

Przytrzymaj klawisz CTRL podczas przeciągania, aby powiększyć. Powiększenie w poziomie można również kontrolować przy użyciu suwaka u dołu.

Zakres pionowy jest domyślnie obliczany na podstawie aktualnie wyświetlanych wartości, a wartości minimalna i maksymalna są wyświetlane w polach tekstowych po prawej stronie. Gdy wartości są ustawiane ręcznie, przez wpisanie ich bezpośrednio w polu tekstowym lub przez panoramowanie/powiększanie, wykres będzie używać tych wartości. Aby przywrócić automatyczne ramki pionowe, kliknij ikonę w prawym górnym rogu.

![zakres pionowy](./media/vertical-range.png)

## <a name="the-log-panel"></a>Panel dziennik

![Panel dziennik](./media/log-panel.png)

Panel dziennik przedstawia listę komunikatów dziennika generowanych po stronie serwera. W przypadku połączenia będą wyświetlane do 200 poprzednich komunikatów dziennika i będą drukowane w miarę ich występowania.

Listę można filtrować na podstawie typu dziennika `[Error/Warning/Info/Debug]` za pomocą przycisków znajdujących się u góry.
![Przyciski filtru dziennika](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Panel przechwytywania danych chronometrażu

![Przechwytywanie danych chronometrażu](./media/timing-data-capture.png)

Ten panel służy do przechwytywania informacji o chronometrażu z serwera i pobierania go. Plik używa [formatu JSON śledzenia Chrome](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Aby sprawdzić dane, Otwórz program Chrome w adresie URL, `Chrome://tracing` a następnie przeciągnij i upuść pobrany plik na stronę. Dane chronometrażu są stale zbierane w buforze pierścieniowym o ustalonym rozmiarze. Po zapisaniu przechwycenie obejmuje tylko informacje o przeszłości, co oznacza kilka sekund.

## <a name="the-scene-inspection-panel"></a>Panel Inspekcja sceny

![Panel inspekcji sceny](./media/scene-inspection-panel.png)

Ten panel przedstawia strukturę renderowanej sceny. Hierarchia obiektów znajduje się po lewej stronie, zawartość zaznaczonego obiektu znajduje się po prawej stronie. Panel jest tylko do odczytu i jest aktualizowany w czasie rzeczywistym.

## <a name="the-vm-debug-information-panel"></a>Panel informacje debugowania maszyny wirtualnej

![Panel informacji debugowania maszyny wirtualnej](./media/state-debugger-panel.png)

Ten panel oferuje pewne funkcje debugowania.

### <a name="restart-service"></a>Ponownie uruchom usługę

Przycisk **Uruchom ponownie usługę** uruchamia ponownie środowisko uruchomieniowe na maszynie wirtualnej, z którą jest połączona arrInspector. Każdy dołączony klient zostanie odłączony, a strona arrInspector musi zostać ponownie załadowana, aby można było połączyć się z uruchomioną usługą.

### <a name="collect-debug-information"></a>Zbierz informacje debugowania

Przycisk **Zbierz informacje debugowania dla maszyny wirtualnej** otwiera okno dialogowe, które umożliwia Wyzwól wystąpienie klasy ARR w celu zebrania informacji debugowania na maszynie wirtualnej:

![Okno dialogowe informacji debugowania maszyny wirtualnej](./media/state-debugger-dialog.png)

Informacje debugowania ułatwiają zespołowi renderowania zdalnego systemu Azure analizowanie wszelkich problemów występujących w działającym wystąpieniu ARR. Okno dialogowe zawiera pole tekstowe umożliwiające dostarczenie dodatkowych informacji, na przykład kroki umożliwiające odtworzenie problemu.

Po kliknięciu przycisku **Rozpocznij zbieranie** okno dialogowe zostanie zamknięte i rozpocznie się proces zbierania danych. Zbieranie informacji na maszynie wirtualnej może potrwać kilka minut.

![Trwa zbieranie informacji o debugowaniu maszyny wirtualnej](./media/state-debugger-panel-in-progress.png)

Po zakończeniu zbierania zostanie wyświetlone powiadomienie w oknie ArrInspector. To powiadomienie zawiera identyfikator, który identyfikuje tę określoną kolekcję. Pamiętaj, aby zapisać ten identyfikator w celu przekazania go do zespołu zdalnego renderowania platformy Azure.

![Pomyślne zbieranie informacji debugowania maszyny wirtualnej](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Nie można pobrać ani uzyskać w inny sposób dostępu do informacji debugowania maszyny wirtualnej. Tylko zespół zdalnego renderowania platformy Azure ma dostęp do zebranych danych. Musisz skontaktować się z nami i wysłać identyfikator kolekcji, aby sprawdzić, czy problem widzisz.

## <a name="pause-mode"></a>Tryb wstrzymania

W prawym górnym rogu przełącznik umożliwia wstrzymanie aktualizacji na żywo paneli. Ten tryb może być przydatny do dokładnego zbadania określonego stanu.

![Tryb wstrzymania](./media/pause-mode.png)

Po ponownym włączeniu aktualizacji na żywo wszystkie panele zostaną zresetowane.