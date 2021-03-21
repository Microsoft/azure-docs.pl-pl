---
title: Cykl życia Eksplorator usługi Azure Storage pomocy technicznej | Microsoft Docs
description: Omówienie zasad pomocy technicznej i cyklu życia dla Eksplorator usługi Azure Storage
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679943"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Eksplorator usługi Azure Storage i zasad cyklu pomocy technicznej

W tym dokumencie opisano cykl wsparcia technicznego i zasady dotyczące Eksplorator usługi Azure Storage.

## <a name="update-schedule-and-process"></a>Aktualizowanie harmonogramu i procesu

Eksplorator usługi Azure Storage jest wydana od czterech do sześciu razy w roku. Firma Microsoft może również ominąć ten harmonogram w celu wydania poprawek w przypadku problemów krytycznych.

Eksplorator usługi Storage sprawdza aktualizacje co godzinę i pobiera je, gdy są dostępne. Aby rozpocząć proces instalacji, wymagane jest jednak akceptacja użytkownika. Jeśli użytkownik zdecyduje się zaktualizować w innym czasie, może ręcznie sprawdzić dostępność aktualizacji. W systemach Windows i Linux użytkownicy mogą sprawdzać dostępność aktualizacji, wybierając pozycję **Sprawdź aktualizacje** w menu **Pomoc** . W systemie macOS **Sprawdź, czy aktualizacje** są dostępne w **menu aplikacji**. Użytkownicy mogą również bezpośrednio przejść do strony pobierania [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) , aby pobrać i zainstalować najnowszą wersję.

Zdecydowanie zalecamy, aby zawsze używać najnowszych wersji Eksplorator usługi Storage. Jeśli problem uniemożliwia zaktualizowanie do najnowszej wersji Eksplorator usługi Storage, Otwórz problem w naszym serwisie [GitHub](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Cykl życia pomocy technicznej

Eksplorator usługi Storage podlegają [nowoczesnym zasadom cyklu życia](https://support.microsoft.com/help/30881/modern-lifecycle-policy). Należy się spodziewać, że użytkownicy zachowują swoją instalację Eksplorator usługi Storage. Dzięki temu użytkownicy mogą korzystać z najnowszych możliwości, ulepszeń wydajności, zabezpieczeń i niezawodności usługi.

Począwszy od wersji 1.14.1, każda wersja Eksplorator usługi Storage, która jest nowsza niż 12 miesięcy, zostanie uznana za pomoc techniczną. Wszystkie wersje przed 1.14.1em będą uważane za pomoc techniczną od 14 lipca 2021. Wersje, które nie są obsługiwane, nie są już gwarantowane w pełni zaprojektowane i oczekiwane. Aby zapoznać się z listą wszystkich wersji, ich daty wydania i ich końcami, zobacz [releases](#releases).

Począwszy od wersji 1.13.0, alert w aplikacji będzie wyświetlany, gdy wersja nie zostanie wyświetlona w ciągu około miesiąca. Ten alert zachęca użytkowników do aktualizacji do najnowszej wersji Eksplorator usługi Storage. Gdy wersja nie jest obsługiwana, w każdym uruchomieniu zostanie wyświetlony alert w aplikacji.

## <a name="releases"></a>Wydania

W tej tabeli opisano datę wydania i koniec okresu pomocy technicznej dla każdej wersji Eksplorator usługi Azure Storage.

| Wersja Eksplorator usługi Storage  | Data wydania       | Data zakończenia obsługi |
|:-------------------------:|:------------------:|:-------------------:|
| 1.17.0 v                   | 15 grudnia 2020  | 15 grudnia 2021   |
| 1.16.0 v                   | 10 listopada 2020 r.  | 10 listopada 2021   |
| 1.15.1 v                   | 2 września 2020  | 2 września 2021   |
| 1.15.0 v                   | 27 sierpnia 2020    | 27 sierpnia 2021     |
| 1.14.2 v                   | 16 lipca 2020      | 16 lipca 2021       |
| 1.14.1 v                   | 14 lipca 2020 r.      | 14 lipca 2021       |
| 1.14.0 v                   | 24 czerwca 2020      | 14 lipca 2021       |
| 1.13.1 v                   | 18 maja 2020       | 14 lipca 2021       |
| 1.13.0 v                   | 1 maja 2020        | 14 lipca 2021       |
| 1.12.0 v                   | 16 stycznia 2020   | 14 lipca 2021       |
| 1.11.2 v                   | 17 grudnia 2019 r.  | 14 lipca 2021       |
| 1.11.1 v                   | 20 listopada 2019 r.  | 14 lipca 2021       |
| 1.11.0 v                   | 4 listopada 2019 r.   | 14 lipca 2021       |
| 1.10.1 v                   | 19 września 2019 | 14 lipca 2021       |
| 1.10.0 v                   | 12 września 2019 | 14 lipca 2021       |
| 1.9.0 v                    | 1 lipca 2019       | 14 lipca 2021       |
| 1.8.1 v                    | 10 maja 2019       | 14 lipca 2021       |
| 1.8.0 v                    | 2 maja 2019        | 14 lipca 2021       |
| 1.7.0 v                    | 5 marca 2019      | 14 lipca 2021       |
| v 1.6.2                    | 8 stycznia 2019    | 14 lipca 2021       |
| 1.6.1 v                    | 18 grudnia, 2018  | 14 lipca 2021       |
| 1.6.0 v                    | 4 grudnia 2018 r.   | 14 lipca 2021       |
| 1.5.0 v                    | 29 października 2018 r.   | 14 lipca 2021       |
| 1.4.4 v                    | 15 października 2018   | 14 lipca 2021       |
| v 1.4.2                    | Wrzesień 24, 2018 | 14 lipca 2021       |
| v 1.4.1                    | 28 sierpnia 2018 r.    | 14 lipca 2021       |
| v 1.3.1                    | 11 lipca 2018      | 14 lipca 2021       |
| 1.2.0 v                    | 12 czerwca 2018 r.      | 14 lipca 2021       |
| 1.1.0 v                    | 9 maja 2018        | 14 lipca 2021       |
| 1.0.0 v (i starsze)        | 16 kwietnia 2018     | 14 lipca 2021       |
