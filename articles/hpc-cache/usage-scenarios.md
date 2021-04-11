---
title: Scenariusze pamięci podręcznej platformy Azure HPC
description: Opisuje, w jaki sposób sprawdzić, czy zadanie przetwarzania danych działa prawidłowo z pamięcią podręczną platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259975"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Czy zadanie dobrze pasuje do pamięci podręcznej platformy Azure HPC?

Pamięć podręczna Azure HPC umożliwia przyspieszenie dostępu do danych w przypadku zadań obliczeniowych o wysokiej wydajności w różnych dziedzinach. Ale nie jest to idealne dla wszystkich typów przepływów pracy. Ten artykuł zawiera wskazówki dotyczące decydowania, czy pamięć podręczna HPC jest dobrym rozwiązaniem dla Twoich potrzeb.

Artykuł z [omówieniem](hpc-cache-overview.md) zawiera również Krótki zarys sytuacji, w których należy używać pamięci podręcznej platformy Azure HPC i niektórych przykładów przypadków użycia.

Przeczytaj także [ten artykuł](nfs-blob-considerations.md) dotyczący sposobu efektywnego używania [magazynu obiektów BLOB zainstalowanego w systemie plików NFS](../storage/blobs/network-file-system-protocol-support.md), który jest w wersji zapoznawczej.

## <a name="nfs-version-30-applications"></a>System plików NFS w wersji 3,0

Pamięć podręczna Azure HPC obsługuje tylko klientów systemu NFS 3,0.

## <a name="high-read-to-write-ratio"></a>Wysoki współczynnik odczytu i zapisu

Obciążenia, w których klienci obliczeniowi wykonują więcej odczytu niż piszą, są zazwyczaj dobrymi kandydatami dla pamięci podręcznej. Na przykład, jeśli współczynnik odczytu do zapisu to 80/20 lub 70/30, pamięć podręczna Azure HPC może pomóc w obsłużeniu często żądanych plików z pamięci podręcznej, a nie konieczności pobierania ich z magazynu zdalnego w systemie i.

Pobieranie pliku i przechowywanie go w pamięci podręcznej po raz pierwszy ma niewielki dodatkowy czas oczekiwania na normalne żądanie klienta bezpośrednio do magazynu, dzięki czemu zwiększenie wydajności jest dostępne przy następnym zażądaniu tego samego pliku przez klienta. Jest to szczególnie prawdziwe w przypadku dużych plików. Jeśli każde żądanie klienta jest unikatowe, wpływ pamięci podręcznej HPC jest ograniczony. Jednak im większy plik, tym lepsza wydajność jest w czasie po pierwszym dostępie.

## <a name="file-based-analytic-workload"></a>Obciążenie analityczne oparte na plikach

Pamięć podręczna Azure HPC jest idealna dla potoku korzystającego z danych opartych na plikach i wykonywanych przez dużą liczbę klientów obliczeniowych, zwłaszcza jeśli klienci obliczeniowi są maszynami wirtualnymi platformy Azure. Może pomóc w rozwiązaniu powolnej lub niespójnej wydajności, która jest spowodowana przez długi czas dostępu do pliku.

## <a name="remote-data-access"></a>Zdalny dostęp do danych

Pamięć podręczna Azure HPC może pomóc w zmniejszeniu opóźnień, jeśli obciążenie wymaga dostępu do danych zdalnych, które nie mogą zostać przeniesione bliżej zasobów obliczeniowych. Na przykład rekordy mogą znajdować się na końcu środowiska sieci WAN, w innym regionie świadczenia usługi Azure lub w centrum danych klienta. (Jest to czasami nazywane "rozruchem plików".)

## <a name="heavy-request-load"></a>Duże obciążenie żądaniami

Jeśli duża liczba klientów żąda danych ze źródła w tym samym czasie, pamięć podręczna Azure HPC może przyspieszyć dostęp do plików. Na przykład gdy jest używany z klastrem obliczeniowym o wysokiej wydajności, pamięć podręczna platformy Azure HPC zapewnia skalowalność dla dużej liczby równoczesnych żądań za pomocą pamięci podręcznej.

## <a name="compute-resources-are-located-in-azure"></a>Zasoby obliczeniowe znajdują się na platformie Azure

Usługa Azure Virtual Machines to skalowalna i ekonomiczna odpowiedź na obciążenie obliczeniowe o wysokiej wydajności. Pamięć podręczna Azure HPC może pomóc w przeniesieniu informacji, które są im bliżej nich, zwłaszcza jeśli oryginalne dane są przechowywane w systemie zdalnym.

Jeśli klient chce uruchomić swój bieżący potok "zgodnie z oczekiwaniami" w usłudze Azure Virtual Machines, w pamięci podręcznej platformy Azure HPC można zapewnić skalowalny Magazyn oparty na systemie POSIX (lub buforowanie).

Za pomocą pamięci podręcznej platformy Azure HPC nie trzeba ponownie tworzyć architektury potoku służbowego w celu wykonywania wywołań natywnych do usługi Azure Blob Storage. Możesz uzyskać dostęp do danych w oryginalnym systemie lub użyć pamięci podręcznej HPC, aby przenieść ją do nowego kontenera obiektów BLOB.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, jak planować i konfigurować pamięć podręczną w artykułach [Przegląd](hpc-cache-overview.md) i [wymagania wstępne](hpc-cache-prerequisites.md)
* Zagadnienia dotyczące korzystania z usługi [BLOB Storage](nfs-blob-considerations.md) (wersja zapoznawcza) w usłudze Azure HPC cache
