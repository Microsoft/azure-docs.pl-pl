---
title: Zarządzanie i aktualizowanie pamięci podręcznej platformy Azure HPC
description: Jak zarządzać i aktualizować pamięć podręczną Azure HPC przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 08/31/2020
ms.author: v-erkel
ms.openlocfilehash: 19950ca215abbac3a56bdb901448c9d92ad369be
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613059"
---
# <a name="manage-your-cache"></a>Zarządzanie pamięcią podręczną

Na stronie Przegląd pamięci podręcznej w Azure Portal są wyświetlane szczegóły projektu, stan pamięci podręcznej i podstawowe statystyki pamięci podręcznej. Ma także kontrolki do zatrzymywania lub uruchamiania pamięci podręcznej, usuwania pamięci podręcznej, opróżniania danych do długoterminowego przechowywania i aktualizacji oprogramowania.

W tym artykule opisano również sposób wykonywania tych podstawowych zadań przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby otworzyć stronę przegląd, wybierz zasób pamięci podręcznej w Azure Portal. Na przykład Załaduj stronę **wszystkie zasoby** i kliknij nazwę pamięci podręcznej.

![zrzut ekranu przedstawiający stronę omówienia wystąpienia pamięci podręcznej platformy Azure HPC](media/hpc-cache-overview.png)

Przyciski w górnej części strony mogą pomóc w zarządzaniu pamięcią podręczną:

* **Uruchamianie** i [**Zatrzymywanie**](#stop-the-cache) — wznawianie lub wstrzymywanie operacji pamięci podręcznej
* [**Opróżnianie**](#flush-cached-data) — zapisuje zmienione dane w celu przechowywania
* [**Upgrade**](#upgrade-cache-software) — aktualizuje oprogramowanie pamięci podręcznej
* [**Zbieranie danych diagnostycznych**](#collect-diagnostics) — przekazywanie informacji debugowania
* **Refresh** -ponownie ładuje stronę przeglądu
* [**Usuń**](#delete-the-cache) — trwale niszczy pamięć podręczną

Przeczytaj więcej na temat tych opcji poniżej.

Kliknij poniższy obraz, aby obejrzeć [film wideo](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) przedstawiający zadania zarządzania pamięcią podręczną.

[![Miniatura wideo: Azure HPC cache: zarządzanie (kliknij, aby odwiedzić stronę wideo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Zatrzymaj pamięć podręczną

Można zatrzymać pamięć podręczną, aby zmniejszyć koszty w nieaktywnym okresie. Nie są naliczane opłaty za czas przestoju, gdy pamięć podręczna jest zatrzymana, ale opłaty są naliczane za przydzieloną pamięć podręczną. (Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem](https://aka.ms/hpc-cache-pricing) ).

Zatrzymana pamięć podręczna nie odpowiada na żądania klientów. Przed zatrzymaniem pamięci podręcznej należy odinstalować klientów.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Przycisk **Zatrzymaj** wstrzymuje aktywną pamięć podręczną. Przycisk **Zatrzymaj** jest dostępny, gdy stan pamięci podręcznej jest w **dobrej kondycji** lub ma negatywny wpływ na **wydajność**.

![zrzut ekranu górnych przycisków z wyróżnioną pozycją Zatrzymaj i podręczny komunikat opisujący akcję zatrzymania i pytanie "czy chcesz kontynuować?" z opcją Yes (domyślnie) i bez przycisków](media/stop-cache.png)

Po kliknięciu przycisku tak, aby potwierdzić zatrzymywanie pamięci podręcznej, pamięć podręczna automatycznie opróżni swoją zawartość do miejsc docelowych magazynu. Ten proces może zająć trochę czasu, ale zapewnia spójność danych. Na koniec stan pamięci podręcznej zmieni się na **zatrzymany**.

Aby ponownie uaktywnić zatrzymaną pamięć podręczną, kliknij przycisk **Uruchom** . Nie jest wymagany żaden monit.

![zrzut ekranu górnych przycisków z wyróżnioną pozycją Start](media/start-cache.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Tymczasowe wstrzymywanie pamięci podręcznej za pomocą polecenia [AZ HPC-cache Stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Ta akcja jest prawidłowa tylko wtedy, gdy stan pamięci podręcznej jest w **dobrej kondycji** lub ma negatywny wpływ na **wydajność**.

Pamięć podręczna automatycznie opróżnia zawartość do miejsc docelowych magazynu przed zatrzymaniem. Ten proces może zająć trochę czasu, ale zapewnia spójność danych.

Po zakończeniu akcji stan pamięci podręcznej zmieni się na **zatrzymany**.

Ponownie Aktywuj zatrzymaną pamięć podręczną za pomocą [AZ HPC-cache Start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Po wydaniu polecenia Uruchom lub Zatrzymaj w wierszu polecenia zostanie wyświetlony komunikat o stanie "uruchomiona", dopóki operacja nie zostanie ukończona.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Po zakończeniu komunikat zostanie zaktualizowany do "zakończony" i są wyświetlane kody powrotne i inne informacje.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Opróżnij buforowane dane

Przycisk **opróżniania** na stronie Przegląd informuje pamięć podręczną, aby natychmiast napisać wszystkie zmienione dane przechowywane w pamięci podręcznej do obiektów docelowych magazynu zaplecza. Pamięć podręczna zapisuje w sposób rutynowy dane do miejsc docelowych magazynu, dlatego nie trzeba tego robić ręcznie, chyba że chcesz upewnić się, że system przechowywania zaplecza jest aktualny. Można na przykład użyć operacji **opróżniania** przed wykonaniem migawki magazynu lub sprawdzaniem rozmiaru zestawu danych.

> [!NOTE]
> W procesie opróżniania pamięć podręczna nie może udostępniać żądań klientów. Dostęp do pamięci podręcznej jest zawieszony i wznawiany po zakończeniu operacji.

Po uruchomieniu operacji opróżniania pamięci podręcznej pamięć podręczna nie akceptuje żądań klientów, a stan pamięci podręcznej na stronie Przegląd zmieni się na wartość **opróżniania**.

Dane w pamięci podręcznej są zapisywane do odpowiednich obiektów docelowych magazynu. W zależności od ilości danych, które muszą zostać opróżnione, proces może potrwać kilka minut lub za godzinę.

Po zapisaniu wszystkich danych w celu przechowania pamięci podręcznej automatycznie zaczynają ponownie żądania klientów. Stan pamięci podręcznej powraca do stanu **dobrej kondycji**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby opróżnić pamięć podręczną, kliknij przycisk **opróżniania** , a następnie kliknij przycisk **tak** w celu potwierdzenia akcji.

![zrzut ekranu górnych przycisków z wyróżnioną opcją Opróżnij i podręczny komunikat opisujący akcję opróżniania i pytanie "czy chcesz kontynuować?" z opcją Yes (domyślnie) i bez przycisków](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Użyj [AZ HPC-cache Flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) , aby wymusić zapisanie wszystkich zmienionych danych w pamięci podręcznej dla obiektów docelowych magazynu.

Przykład:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Po zakończeniu opróżniania zostanie zwrócony komunikat o powodzeniu.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Uaktualnij oprogramowanie pamięci podręcznej

Jeśli dostępna jest nowa wersja oprogramowania, przycisk **Uaktualnij** zostanie uaktywniony. W górnej części strony pojawi się również komunikat o aktualizowaniu oprogramowania.

![zrzut ekranu przedstawiający górny wiersz przycisków z włączonym przyciskiem Uaktualnij](media/hpc-cache-upgrade-button.png)

Dostęp klienta nie zostanie przerwany podczas uaktualniania oprogramowania, ale wydajność pamięci podręcznej jest niska. Zaplanuj uaktualnienie oprogramowania w godzinach użycia poza szczytem lub w planowanym okresie konserwacji.

Aktualizacja oprogramowania może potrwać kilka godzin. W przypadku pamięci podręcznych skonfigurowanych do większej przepływności trwa dłużej niż w przypadku pamięci podręcznych o mniejszych wartościach o najwyższej wydajności.

Po udostępnieniu uaktualnienia oprogramowania użytkownik będzie miał tydzień lub powinien go zastosować ręcznie. Data końcowa jest wyświetlana w komunikacie uaktualniania. Jeśli w tym czasie nie zostanie uaktualniony, platforma Azure automatycznie zastosuje aktualizację do pamięci podręcznej. Nie można skonfigurować chronometrażu automatycznego uaktualniania. Jeśli chodzi o wpływ na wydajność pamięci podręcznej, należy uaktualnić oprogramowanie samodzielnie przed upływem czasu.

Jeśli pamięć podręczna zostanie zatrzymana po upływie daty zakończenia, pamięć podręczna będzie automatycznie uaktualniać oprogramowanie przy kolejnym uruchomieniu. (Aktualizacja może nie zacząć od razu, ale rozpocznie się w ciągu pierwszej godziny).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Kliknij przycisk **Uaktualnij** , aby rozpocząć aktualizację oprogramowania. Stan pamięci podręcznej zmieni się na **uaktualnienie** do momentu zakończenia operacji.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

W interfejsie wiersza polecenia platformy Azure na końcu raportu o stanie pamięci podręcznej są uwzględniane nowe informacje o oprogramowaniu. (Użyj [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) do check). Wyszukaj ciąg "upgradeStatus" w komunikacie.

Użyj [AZ HPC-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) , aby zastosować aktualizację, jeśli istnieje.

Jeśli aktualizacja nie jest dostępna, ta operacja nie ma żadnego wpływu.

Ten przykład pokazuje stan pamięci podręcznej (brak dostępnej aktualizacji) oraz wyniki polecenia upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Zbieranie danych diagnostycznych

Przycisk **Zbierz diagnostykę** ręcznie uruchamia proces zbierania informacji o systemie i przekazywania go do usługi firmy Microsoft i pomocy technicznej w celu rozwiązywania problemów. Pamięć podręczna zbiera i przekazuje te same informacje diagnostyczne w przypadku wystąpienia poważnego problemu z pamięcią podręczną.

Użyj tej kontrolki, jeśli usługa i pomoc techniczna firmy Microsoft żądają tego formantu.

Po kliknięciu przycisku kliknij przycisk **tak** , aby potwierdzić przekazywanie.

![zrzut ekranu przedstawiający podręczny komunikat potwierdzający rozpoczęcie zbierania danych diagnostycznych. Przycisk domyślny "tak" jest wyróżniony.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Usuń pamięć podręczną

Przycisk **Usuń** niszczy pamięć podręczną. Po usunięciu pamięci podręcznej wszystkie jej zasoby zostaną zniszczone i nie będą już naliczane opłaty za konto.

W przypadku usunięcia pamięci podręcznej nie ma to żadnego oddziaływania na woluminy magazynu zaplecza używane jako cele magazynu. Możesz dodać je do przyszłej pamięci podręcznej później lub zlikwidować je osobno.

> [!NOTE]
> Pamięć podręczna Azure HPC nie zapisuje automatycznie zmienionych danych z pamięci podręcznej w systemach magazynu zaplecza przed usunięciem pamięci podręcznej.
>
> Aby upewnić się, że wszystkie dane w pamięci podręcznej zostały zapisaną do magazynu długoterminowego, [Zatrzymaj pamięć podręczną](#stop-the-cache) przed jego usunięciem. Upewnij się, że jest wyświetlany stan **zatrzymane** przed usunięciem.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Po zatrzymaniu pamięci podręcznej kliknij przycisk **Usuń** , aby trwale usunąć pamięć podręczną.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Użyj interfejsu wiersza polecenia platformy Azure [AZ HPC-cache Delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) , aby trwale usunąć pamięć podręczną.

Przykład:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Metryki pamięci podręcznej i monitorowanie

Na stronie Przegląd przedstawiono wykresy dla niektórych podstawowych statystyk pamięci podręcznej — przepływność pamięci podręcznej, operacje na sekundę i opóźnienie.

![zrzut ekranu przedstawiający trzy wykresy liniowe pokazujący wymienione powyżej dane statystyczne dla przykładowej pamięci podręcznej](media/hpc-cache-overview-stats.png)

Te wykresy są częścią wbudowanych narzędzi do monitorowania i analizowania danych platformy Azure. Dodatkowe narzędzia i alerty są dostępne na stronach pod nagłówkiem **monitorowanie** na pasku bocznym portalu. Więcej informacji znajduje się w sekcji Portal dokumentacji dotyczącej [monitorowania platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [narzędziach metryk i statystyk dotyczących platformy Azure](../azure-monitor/index.yml)
* Uzyskaj [Pomoc dotyczącą pamięci podręcznej platformy Azure HPC](hpc-cache-support-ticket.md)
