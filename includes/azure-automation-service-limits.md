---
title: plik dołączany
description: plik dołączany
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 06/29/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: aa7b997fe112be5d974d0e82597e786fa219e1f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85570166"
---
#### <a name="process-automation"></a>Automatyzacja procesów

| Zasób | Limit |Uwagi|
| --- | --- |---|
| Maksymalna liczba nowych zadań, które mogą być przesyłane co 30 sekund na konto Azure Automation (zadania niezaplanowane) |100 |Po osiągnięciu tego limitu kolejne żądania utworzenia zadania kończą się niepowodzeniem. Klient otrzymuje odpowiedź na błąd.|
| Maksymalna liczba współbieżnych zadań uruchomionych w tym samym wystąpieniu czasu na konto usługi Automation (zadania niezaplanowane) |200 |Po osiągnięciu tego limitu kolejne żądania utworzenia zadania kończą się niepowodzeniem. Klient otrzymuje odpowiedź na błąd.|
| Maksymalny rozmiar magazynu metadanych zadania dla 30-dniowego okresu kroczącego | 10 GB (około 4 000 000 zadań)|Po osiągnięciu tego limitu kolejne żądania utworzenia zadania kończą się niepowodzeniem. |
| Maksymalny limit strumienia zadań|1 MiB|Pojedynczy strumień nie może być większy niż 1 MB.|
| Maksymalna liczba modułów, które mogą być importowane co 30 sekund na konto usługi Automation |5 ||
| Maksymalny rozmiar modułu |100 MB ||
| Czas wykonywania zadania, warstwa Bezpłatna |500 minut na subskrypcję na miesiąc kalendarzowy ||
| Maksymalna ilość miejsca na dysku dozwolona na piaskownicę<sup>1</sup> |1 GB |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna ilość pamięci podaną w piaskownicy<sup>1</sup> |400 MB |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna liczba gniazd sieciowych dozwolona na piaskownicę<sup>1</sup> |1000 |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalne dozwolone środowisko uruchomieniowe na element Runbook<sup>1</sup> |3 godziny |Dotyczy tylko piaskownic platformy Azure.|
| Maksymalna liczba kont usługi Automation w ramach subskrypcji |Bez ograniczeń ||
| Maksymalna liczba grup hybrydowych procesów roboczych na konto usługi Automation|4000||
|Maksymalna liczba współbieżnych zadań, które można uruchomić w jednym hybrydowym procesie roboczym elementu Runbook|50 ||
| Maksymalny rozmiar parametru zadania elementu Runbook   | 512 kilobajtów||
| Maksymalna liczba parametrów elementu Runbook   | 50|W przypadku osiągnięcia limitu 50-parametru można przekazać kod JSON lub ciąg XML do parametru i przeanalizować go za pomocą elementu Runbook.|
| Maksymalny rozmiar ładunku elementu webhook |  512 kilobajtów|
| Maksymalna liczba dni przechowywania danych zadania|30 dni|
| Maksymalny rozmiar stanu przepływu pracy programu PowerShell |5 MB| Dotyczy elementów Runbook przepływu pracy programu PowerShell podczas tworzenia punktów kontrolnych przepływu pracy.|

<sup>1</sup> Piaskownica to środowisko udostępnione, które może być używane przez wiele zadań. Zadania korzystające z tej samej piaskownicy są powiązane z ograniczeniami zasobów piaskownicy.

#### <a name="change-tracking-and-inventory"></a>Śledzenie zmian i spis

W poniższej tabeli przedstawiono limity śledzonych elementów na maszynę do śledzenia zmian.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie systemu Windows|250|Nie obejmuje aktualizacji oprogramowania.|
|Pakiety systemu Linux|1 250||
|Usługi|250||
|Demon|250||

#### <a name="update-management"></a>Zarządzanie aktualizacjami

W poniższej tabeli przedstawiono limity Update Management.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Liczba maszyn na wdrożenie aktualizacji|1000||