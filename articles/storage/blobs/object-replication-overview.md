---
title: Omówienie replikacji obiektów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Replikacja obiektów (wersja zapoznawcza) asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Użyj replikacji obiektów, aby zminimalizować opóźnienia w żądaniach odczytu, zwiększyć wydajność obciążeń obliczeniowych, zoptymalizować dystrybucję danych i zminimalizować koszty.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: abec9811cd407c1fab91cfb60412aabdd969690d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036985"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replikacja obiektów dla blokowych obiektów BLOB (wersja zapoznawcza)

Replikacja obiektów (wersja zapoznawcza) asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Niektóre scenariusze obsługiwane przez replikację obiektów obejmują:

- **Minimalizacja opóźnień.** Replikacja obiektów może zmniejszyć opóźnienia żądań odczytu przez umożliwienie klientom korzystania z danych z regionu, który znajduje się bliżej fizycznej bliskości.
- **Zwiększenie wydajności obciążeń obliczeniowych.** W przypadku replikacji obiektów obciążenia obliczeniowe mogą przetwarzać te same zestawy blokowych obiektów BLOB w różnych regionach.
- **Optymalizacja dystrybucji danych.** Dane można przetwarzać lub analizować w jednej lokalizacji, a następnie replikować tylko wyniki do dodatkowych regionów.
- **Optymalizowanie kosztów.** Po zreplikowaniu danych możesz obniżyć koszty, przenosząc je do warstwy archiwum, korzystając z zasad zarządzania cyklem życia.

Na poniższym diagramie pokazano, jak replikacja obiektów replikuje blokowe obiekty blob z konta magazynu źródłowego w jednym regionie do kont docelowych w dwóch różnych regionach.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram przedstawiający sposób działania replikacji obiektów":::

Aby dowiedzieć się, jak skonfigurować replikację obiektów, zobacz [Konfigurowanie replikacji obiektów (wersja zapoznawcza)](object-replication-configure.md).

## <a name="object-replication-policies-and-rules"></a>Zasady i reguły replikacji obiektów

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji określające źródłowe konto magazynu i konto docelowe. Zasady replikacji obejmują co najmniej jedną regułę określającą kontener źródłowy i kontener docelowy i wskazujący, które blokowe obiekty blob w kontenerze źródłowym zostaną zreplikowane.

Po skonfigurowaniu replikacji obiektów usługa Azure Storage sprawdza źródło zmian na koncie źródłowym okresowo i asynchronicznie replikuje wszelkie operacje zapisu lub usuwania na koncie docelowym. Opóźnienie replikacji zależy od rozmiaru zreplikowanego zablokowanego obiektu BLOB.

> [!IMPORTANT]
> Ponieważ dane blokowych obiektów BLOB są replikowane asynchronicznie, konto źródłowe i docelowe nie są natychmiast synchronizowane. Obecnie nie ma umowy SLA dotyczącej tego, jak długo trwa replikowanie danych do konta docelowego.

### <a name="replications-policies"></a>Zasady replikacji

Podczas konfigurowania replikacji obiektów zasady replikacji są tworzone zarówno na koncie źródłowym, jak i na koncie docelowym za pośrednictwem dostawcy zasobów usługi Azure Storage. Zasady replikacji są identyfikowane przez identyfikator zasad. Zasady na kontach źródłowych i docelowych muszą mieć ten sam identyfikator zasad, aby replikacja była przeprowadzana.

Konto magazynu może stanowić konto źródłowe dla maksymalnie dwóch kont docelowych. A konto docelowe może zawierać nie więcej niż dwa konta źródłowe. Konta źródłowe i docelowe mogą znajdować się w różnych regionach. W celu replikowania danych do każdego z kont docelowych można skonfigurować oddzielne zasady replikacji.

### <a name="replication-rules"></a>Reguły replikacji

Reguły replikacji określają, w jaki sposób usługa Azure Storage będzie replikować obiekty blob z kontenera źródłowego do kontenera docelowego. Dla każdej zasady replikacji można określić maksymalnie 10 reguł replikacji. Każda reguła definiuje jeden kontener źródłowy i docelowy, a każdy kontener źródłowy i docelowy może być używany tylko w jednej regule.

Podczas tworzenia reguły replikacji domyślnie kopiowane są tylko nowe blokowe obiekty blob, które są następnie dodawane do kontenera źródłowego. Można również określić, że kopiowane są zarówno nowe, jak i istniejące blokowe obiekty blob, albo zdefiniować zakres kopiowania niestandardowego, który kopiuje blokowe obiekty blob utworzone od określonego momentu.

Można również określić jeden lub więcej filtrów jako część reguły replikacji, aby odfiltrować blokowe obiekty blob według prefiksu. Po określeniu prefiksu tylko obiekty blob pasujące do tego prefiksu w kontenerze źródłowym zostaną skopiowane do kontenera docelowego.

Oba kontenery źródłowe i docelowe muszą istnieć przed określeniem ich w regule. Po utworzeniu zasad replikacji kontener docelowy jest tylko do odczytu. Wszystkie próby zapisu w kontenerze docelowym kończą się niepowodzeniem z kodem błędu 409 (konflikt). Można jednak wywołać operację [ustawiania warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier) na obiekcie BLOB w kontenerze docelowym, aby przenieść ją do warstwy archiwum. Aby uzyskać więcej informacji o warstwie archiwum, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Replikacja obiektów jest obsługiwana tylko w przypadku kont magazynu ogólnego przeznaczenia w wersji 2. Replikacja obiektów jest dostępna w następujących regionach w wersji zapoznawczej:

- Francja Środkowa
- Kanada Wschodnia
- Kanada Środkowa
- Wschodnie stany USA 2
- Środkowe stany USA

Zarówno konto źródłowe, jak i docelowe muszą znajdować się w jednym z tych regionów, aby można było korzystać z replikacji obiektów. Konta mogą znajdować się w dwóch różnych regionach.

W ramach wersji zapoznawczej nie ma dodatkowych kosztów związanych z replikacją danych między kontami magazynu.

> [!IMPORTANT]
> Wersja zapoznawcza replikacji obiektów jest przeznaczona wyłącznie do użytku nieprodukcyjnego. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

### <a name="prerequisites-for-object-replication"></a>Wymagania wstępne dotyczące replikacji obiektów

Replikacja obiektów wymaga włączenia następujących funkcji usługi Azure Storage: 
- [Źródło zmian](storage-blob-change-feed.md)
- [Obsługa wersji](versioning-overview.md)

Przed skonfigurowaniem replikacji obiektów należy włączyć jej wymagania wstępne. Źródło zmian musi być włączone na koncie źródłowym, a wersja obiektu BLOB musi być włączona zarówno na koncie źródłowym, jak i docelowym. Aby uzyskać więcej informacji na temat włączania tych funkcji, zobacz następujące artykuły:

- [Włączanie i wyłączanie kanału informacyjnego zmiany](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)

Pamiętaj, aby zarejestrować się w celu uzyskania źródła zmian i wersji zapoznawczych obiektów BLOB przed ich włączeniem.

Włączenie kanału informacyjnego zmiany i przechowywanie wersji obiektów BLOB może pociągnąć za sobą dodatkowe koszty. Więcej informacji można znaleźć na [stronie cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Zarejestruj się w wersji zapoznawczej

Możesz zarejestrować się w celu uzyskania podglądu replikacji obiektów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Upewnij się, że rejestrujesz się również dla podglądów zmian i wersji zapoznawczych obiektów blob, jeśli jeszcze tego nie zrobiono.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się w celu korzystania z wersji zapoznawczej przy użyciu programu PowerShell, uruchom następujące polecenia:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się w celu korzystania z wersji zapoznawczej przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Sprawdź stan rejestracji

Stan żądań rejestracji można sprawdzić przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić stan żądań rejestracji przy użyciu programu PowerShell, uruchom następujące polecenia:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan żądań rejestracji przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Zadawaj pytania lub przekazanie opinii

Aby zadawać pytania dotyczące wersji zapoznawczej replikacji obiektów lub przekazać opinię, skontaktuj się z firmą Microsoft pod adresem AzureStorageFeedback@microsoft.com . Pomysły i sugestie dotyczące usługi Azure Storage są zawsze powitane na [forum opinii o usłudze Azure Storage](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Następne kroki

- [Konfiguruj replikację obiektów (wersja zapoznawcza)](object-replication-configure.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
