---
title: Zarządzanie i aktualizowanie Azure HPC Cache
description: Jak zarządzać maszyną i aktualizować Azure HPC Cache przy użyciu interfejsu wiersza Azure Portal platformy Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862601"
---
# <a name="manage-your-cache"></a>Zarządzanie pamięcią podręczną

Strona przeglądu pamięci podręcznej w Azure Portal zawiera szczegóły projektu, stan pamięci podręcznej i podstawowe statystyki dotyczące pamięci podręcznej. Ma również kontrolki służące do zatrzymania lub uruchomienia pamięci podręcznej, usunięcia pamięci podręcznej, opróżninia danych do magazynu długoterminowego i zaktualizowania oprogramowania.

W tym artykule wyjaśniono również, jak wykonywać te podstawowe zadania za pomocą interfejsu wiersza polecenia platformy Azure.

Aby otworzyć stronę przeglądu, wybierz zasób pamięci podręcznej w Azure Portal. Na przykład załaduj **stronę Wszystkie zasoby** i kliknij nazwę pamięci podręcznej.

![zrzut ekranu przedstawiający Azure HPC Cache przegląd wystąpienia](media/hpc-cache-overview.png)

Przyciski w górnej części strony mogą ułatwić zarządzanie pamięcią podręczną:

* **Uruchamianie** i [**zatrzymywanie**](#stop-the-cache) — wznawia lub wstrzymuje działanie pamięci podręcznej
* [**Opróżnianie**](#flush-cached-data) — zapisuje zmienione dane w celach magazynu
* [**Uaktualnianie**](#upgrade-cache-software) — aktualizuje oprogramowanie pamięci podręcznej
* [**Zbieranie danych diagnostycznych**](#collect-diagnostics) — przekazywanie informacji debugowania
* **Odświeżanie** — ponownie ładuje stronę przeglądu
* [**Usuwanie**](#delete-the-cache) — trwałe niszczenie pamięci podręcznej

Przeczytaj więcej na temat tych opcji poniżej.

Kliknij poniższy obraz, aby obejrzeć [film wideo,](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) w których przedstawiono zadania zarządzania pamięcią podręczną.

[![miniatura wideo: Azure HPC Cache: Zarządzanie (kliknij, aby odwiedzić stronę wideo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Zatrzymywanie pamięci podręcznej

Pamięć podręczną można zatrzymać, aby zmniejszyć koszty w okresie nieaktywnym. Nie są naliczane opłaty za czas pracy, gdy pamięć podręczna jest zatrzymana, ale opłaty są naliczane za przydzielony magazyn dyskowy pamięci podręcznej. (Aby uzyskać [szczegółowe informacje,](https://aka.ms/hpc-cache-pricing) zobacz stronę cennika).

Zatrzymana pamięć podręczna nie odpowiada na żądania klientów. Przed zatrzymaniem pamięci podręcznej należy odinstalować klientów.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Przycisk **Zatrzymaj** wstrzymuje aktywną pamięć podręczną. Przycisk **Zatrzymaj** jest dostępny, gdy stan pamięci podręcznej to **W dobrej kondycji** lub **Obniżona wydajność.**

![zrzut ekranu przedstawiający górne przyciski z wyróżniona akcję Zatrzymaj i komunikatem podręcznym z opisem akcji zatrzymania i pytaniem "Czy chcesz kontynuować?". z przyciskami Tak (ustawienie domyślne) i Bez](media/stop-cache.png)

Po kliknięciu przycisku Tak w celu potwierdzenia zatrzymania pamięci podręcznej pamięć podręczna automatycznie opróżnia jej zawartość do obiektów docelowych magazynu. Ten proces może zająć trochę czasu, ale zapewnia spójność danych. Na koniec stan pamięci podręcznej zmieni się **na Zatrzymano.**

Aby ponownie uaktywnić zatrzymaną pamięć podręczną, kliknij **przycisk** Uruchom. Nie jest wymagane żadne potwierdzenie.

![zrzut ekranu przedstawiający górne przyciski z wyróżnionem menu Start](media/start-cache.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Tymczasowo wstrzymaj pamięć podręczną za pomocą [polecenia az hpc-cache stop.](/cli/azure/hpc-cache#az_hpc_cache_stop) Ta akcja jest prawidłowa tylko wtedy, gdy stan pamięci podręcznej to **W dobrej kondycji** lub **Obniżona wydajność.**

Pamięć podręczna automatycznie opróżnia zawartość do obiektów docelowych magazynu przed zatrzymaniem. Ten proces może zająć trochę czasu, ale zapewnia spójność danych.

Po zakończeniu akcji stan pamięci podręcznej zmieni się na **Zatrzymano.**

Ponownie uaktywnij zatrzymaną pamięć podręczną za pomocą [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start).

Po uruchomieniu polecenia uruchamiania lub zatrzymania wiersz polecenia wyświetla komunikat o stanie "Running" (Uruchomiona), dopóki operacja nie zostanie ukończona.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Po zakończeniu komunikat zostanie uaktualniony do stanu "Zakończono" i będzie zawierał kody powrotne oraz inne informacje.

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

## <a name="flush-cached-data"></a>Opróżnij dane w pamięci podręcznej

Przycisk **Opróżnij** na stronie przeglądu nakazuje pamięci podręcznej natychmiastowe zapisanie wszystkich zmienionych danych przechowywanych w pamięci podręcznej w docelowych magazynach na zadomowie. Pamięć podręczna regularnie zapisuje dane w celach magazynu, więc nie trzeba tego robić ręcznie, chyba że chcesz upewnić się, że system magazynowania na zakładzie jest aktualny. Można na przykład użyć funkcji **Flush przed** zrobieniem migawki magazynu lub sprawdzeniem rozmiaru zestawu danych.

> [!NOTE]
> Podczas procesu opróżniania pamięć podręczna nie może obsługiwać żądań klientów. Dostęp do pamięci podręcznej zostanie wstrzymany i wznowiony po zakończeniu operacji.

Po uruchomieniu operacji opróżniania pamięci podręcznej pamięć podręczna przestaje akceptować żądania klientów, a stan pamięci podręcznej na stronie przeglądu zmienia się **na Opróżnianie**.

Dane w pamięci podręcznej są zapisywane w odpowiednich celach magazynu. W zależności od tego, ile danych należy opróżnić, proces może potrwać kilka minut lub ponad godzinę.

Po zapisaniu wszystkich danych w celach magazynu pamięć podręczna automatycznie ponownie zacznie pobierać żądania klientów. Stan pamięci podręcznej jest przywracany do stanu **W dobrej kondycji.**

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby opróżnić pamięć podręczną, kliknij przycisk **Opróżnij,** a następnie kliknij przycisk **Tak,** aby potwierdzić akcję.

![zrzut ekranu przedstawiający górne przyciski z wyróżniona akcję Opróżnij i wyskakującym komunikatem z opisem akcji opróżniania i pytaniem "Czy chcesz kontynuować?". z przyciskami Tak (ustawienie domyślne) i Bez](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj [az hpc-cache flush,](/cli/azure/hpc-cache#az_hpc_cache_flush) aby wymusić, aby pamięć podręczna zapisywała wszystkie zmienione dane w celach magazynu.

Przykład:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Po zakończeniu opróżnień jest zwracany komunikat o sukcesie.

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

## <a name="upgrade-cache-software"></a>Uaktualnianie oprogramowania pamięci podręcznej

Jeśli dostępna jest nowa wersja oprogramowania, **przycisk Uaktualnij** staje się aktywny. W górnej części strony powinien zostać również wyświetlony komunikat o aktualizowaniu oprogramowania.

![zrzut ekranu przedstawiający górny wiersz przycisków z włączonym przyciskiem Uaktualnij](media/hpc-cache-upgrade-button.png)

Dostęp klienta nie jest przerywany podczas uaktualniania oprogramowania, ale wydajność pamięci podręcznej jest niska. Zaplanuj uaktualnienie oprogramowania poza godzinami szczytu lub w okresie planowanej konserwacji.

Aktualizacja oprogramowania może potrwać kilka godzin. Uaktualnianie pamięci podręcznych skonfigurowanych z wyższą przepływnością trwa dłużej niż w przypadku pamięci podręcznych o mniejszych wartościach szczytowej przepływności.

Jeśli uaktualnienie oprogramowania jest dostępne, będziesz mieć tydzień lub tak, aby zastosować je ręcznie. Data zakończenia jest wymieniona w komunikacie o uaktualnieniu. Jeśli nie uaktualnisz w tym czasie, platforma Azure automatycznie stosuje aktualizację do pamięci podręcznej. Nie można skonfigurować chronometrażu automatycznego uaktualniania. Jeśli martwisz się o wpływ na wydajność pamięci podręcznej, przed upływem okresu należy samodzielnie uaktualnić oprogramowanie.

Jeśli pamięć podręczna zostanie zatrzymana po dacie zakończenia, pamięć podręczna automatycznie uaktualni oprogramowanie przy następnym jej uruchamianiu. (Aktualizacja może nie rozpoczynać się od razu, ale rozpocznie się od pierwszej godziny).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Kliknij przycisk **Uaktualnij,** aby rozpocząć aktualizację oprogramowania. Stan pamięci podręcznej zmienia się **na Uaktualnianie** do momentu ukończenia operacji.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

W interfejsie wiersza polecenia platformy Azure nowe informacje o oprogramowaniu są uwzględniane na końcu raportu o stanie pamięci podręcznej. (Użyj [az hpc-cache show,](/cli/azure/hpc-cache#az_hpc_cache_show) aby to sprawdzić). Poszukaj ciągu "upgradeStatus" w komunikacie.

Użyj [az hpc-cache upgrade-firmware,](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) aby zastosować aktualizację, jeśli taka istnieje.

Jeśli żadna aktualizacja nie jest dostępna, ta operacja nie ma wpływu.

W tym przykładzie pokazano stan pamięci podręcznej (nie jest dostępna żadna aktualizacja) i wyniki polecenia upgrade-firmware.

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

Przycisk **Zbieraj** dane diagnostyczne ręcznie uruchamia proces zbierania informacji o systemie i przekazywania ich do usług i pomocy technicznej firmy Microsoft w celu rozwiązywania problemów. Pamięć podręczna automatycznie zbiera i przesyła te same informacje diagnostyczne w przypadku wystąpienia poważnego problemu z pamięcią podręczną.

Użyj tej kontrolki, jeśli zażąda tego usługa firmy Microsoft i pomoc techniczna.

Po kliknięciu przycisku kliknij przycisk **Tak,** aby potwierdzić przekazanie.

![zrzut ekranu przedstawiający wyskakujący komunikat z potwierdzeniem uruchomienia kolekcji diagnostyki. Przycisk domyślny "tak" jest wyróżniony.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Usuwanie pamięci podręcznej

Przycisk **Usuń** niszczy pamięć podręczną. Po usunięciu pamięci podręcznej wszystkie jej zasoby są niszczone i nie są już naliczane opłaty za konto.

Usunięcie pamięci podręcznej nie ma wpływu na woluminy magazynu na zadużych używane jako obiekty docelowe magazynu. Później możesz dodać je do przyszłej pamięci podręcznej lub zlikwidować je oddzielnie.

> [!NOTE]
> Azure HPC Cache nie zapisuje automatycznie zmienionych danych z pamięci podręcznej do systemów magazynowania na zadumie przed usunięciem pamięci podręcznej.
>
> Aby upewnić się, że wszystkie dane w pamięci podręcznej zostały zapisane w magazynie długoterminowym, zatrzymaj pamięć [podręczną](#stop-the-cache) przed jej usunięciem. Przed usunięciem upewnij się, że jest **on w stanie** Zatrzymano.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Po zatrzymaniu pamięci podręcznej kliknij przycisk **Usuń,** aby trwale usunąć pamięć podręczną.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj polecenia interfejsu wiersza polecenia platformy Azure [az hpc-cache delete,](/cli/azure/hpc-cache#az_hpc_cache_delete) aby trwale usunąć pamięć podręczną.

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

## <a name="cache-metrics-and-monitoring"></a>Metryki i monitorowanie pamięci podręcznej

Na stronie przeglądu przedstawiono wykresy dla niektórych podstawowych statystyk pamięci podręcznej — przepływności pamięci podręcznej, operacji na sekundę i opóźnienia.

![zrzut ekranu przedstawiający trzy liniowe wykresy przedstawiające wspomniane powyżej statystyki przykładowej pamięci podręcznej](media/hpc-cache-overview-stats.png)

Te wykresy są częścią wbudowanych narzędzi do monitorowania i analizy platformy Azure. Dodatkowe narzędzia i alerty są dostępne na stronach pod **nagłówkiem Monitorowanie** na pasku bocznym portalu. Dowiedz się więcej w sekcji portalu [dokumentacji monitorowania platformy Azure.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)

## <a name="view-warnings"></a>Wyświetlanie ostrzeżeń

Jeśli pamięć podręczna przejdzie w stan złej kondycji, sprawdź **stronę Ostrzeżenia.** Na tej stronie są wyświetlane powiadomienia z oprogramowania pamięci podręcznej, które mogą pomóc w zrozumieniu jego stanu.

Te powiadomienia nie są wyświetlane w dzienniku aktywności, ponieważ nie są kontrolowane przez Azure Portal. Są one często skojarzone z ustawieniami niestandardowymi, które mogły zostać wprowadzone.

Rodzaje ostrzeżeń, które mogą być wyświetlane w tym miejscu, obejmują:

* Pamięć podręczna nie może uzyskać do serwera NTP
* Pamięć podręczna nie może pobrać informacji o nazwie użytkownika rozszerzonych grup
* Niestandardowe ustawienia DNS zostały zmienione dla obiektu docelowego magazynu

![zrzut ekranu przedstawiający stronę > monitorowania z komunikatem informujący o tym, że nie można pobrać nazw użytkowników grup rozszerzonych](media/warnings-page.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [metrykach i narzędziach statystyk platformy Azure](../azure-monitor/index.yml)
* Uzyskaj [pomoc Azure HPC Cache](hpc-cache-support-ticket.md)
