---
title: Konta usługi Azure Storage
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto usługi Azure Storage do użycia z usługą Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f37b453a294a0d0a7b9a99bfebe8f3eff09e8956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291198"
---
# <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services.

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się użycie kont magazynu w tej samej lokalizacji co konto Media Services, aby uniknąć dodatkowych kosztów opóźnienia i danych wyjściowych.

Musisz mieć jedno konto magazynu **podstawowego** i można mieć dowolną liczbę kont magazynu **pomocniczego** skojarzonych z Twoim kontem Media Services. Usługa Media Services obsługuje konta **Ogólnego przeznaczenia, wersja 2** (GPv2) i **Ogólnego przeznaczenia, wersja 1** (GPv1). Konta obiektów BLOB są niedozwolone jako **podstawowe**.

Zalecamy korzystanie z programu GPv2, dzięki czemu można korzystać z najnowszych funkcji i wydajności. Aby dowiedzieć się więcej na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Tylko Warstwa dostępu gorąca jest obsługiwana w przypadku Azure Media Services, chociaż inne warstwy dostępu mogą służyć do obniżenia kosztów magazynowania zawartości, która nie jest aktywnie używana.

Istnieją różne jednostki SKU, które można wybrać dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](/cli/azure/storage/account?view=azure-cli-latest). Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU do produkcji należy rozważyć `--sku Standard_RAGRS` , która zapewnia replikację geograficzną w celu zapewnienia ciągłości działania.

## <a name="assets-in-a-storage-account"></a>Zasoby na koncie magazynu

W Media Services v3 interfejsy API magazynu są używane do przekazywania plików do zasobów. Aby uzyskać więcej informacji, zobacz [zasoby w Azure Media Services v3](assets-concept.md).

> [!Note]
> Nie należy próbować zmieniać zawartości kontenerów obiektów blob, które zostały wygenerowane przez zestaw SDK Media Services bez używania Media Services interfejsów API.

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić zasoby w spoczynku, zasoby powinny być szyfrowane przez szyfrowanie po stronie magazynu. W poniższej tabeli przedstawiono, w jaki sposób szyfrowanie po stronie magazynu działa w Media Services v3:

|Opcja szyfrowania|Opis|Media Services v3|
|---|---|---|
|Media Services szyfrowanie magazynu| Szyfrowanie AES-256, klucz zarządzany przez Media Services. |Nieobsługiwane. <sup>(1)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](../../storage/common/storage-service-encryption.md)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane.|
|[Szyfrowanie po stronie klienta magazynu](../../storage/common/storage-client-side-encryption.md)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w Key Vault.|Nieobsługiwane.|

<sup>1</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2, co oznacza, że V3 działa z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie umożliwia tworzenia nowych.

## <a name="storage-account-errors"></a>Błędy konta magazynu

Stan „odłączone” dla konta usługi Media Services wskazuje, że konto nie ma już dostępu do co najmniej jednego dołączonego konta magazynu z powodu zmiany kluczy dostępu do magazynu. Aktualne klucze dostępu do magazynu są wymagane przez usługę Media Services do wykonywania wielu zadań na koncie.

Poniżej przedstawiono podstawowe scenariusze, które mogłyby spowodować, że konto usługi Media Services nie będzie mieć dostępu do dołączonych kont magazynu.

|Problem|Rozwiązanie|
|---|---|
|Konto usługi Media Services lub dołączone konta magazynu zostały poddane migracji do oddzielnych subskrypcji. |Przeprowadź migrację kont magazynu lub konta Media Services, aby znajdowały się one w tej samej subskrypcji. |
|Konto usługi Media Services używa dołączonego konta magazynu w innej subskrypcji, ponieważ było to wcześniejsze konto usługi Media Services, na którym było to obsługiwane. Wszystkie konta wczesnego Media Services zostały przekonwertowane na nowoczesne konta oparte na usłudze Azure Resources Manager i będą miały stan rozłączenia. |Przeprowadź migrację konta magazynu lub konta Media Services, aby znajdowały się one w tej samej subskrypcji.|

## <a name="azure-storage-firewall"></a>Zapora usługi Azure Storage

Azure Media Services nie obsługuje kont magazynu z włączoną zaporą usługi Azure Storage lub [prywatnymi punktami końcowymi](../../storage/common/storage-network-security.md) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dołączyć konto magazynu do konta Media Services, zobacz [Tworzenie konta](./create-account-howto.md).
