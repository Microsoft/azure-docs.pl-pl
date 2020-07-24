---
title: Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak zezwolić na dostęp anonimowy do danych obiektów BLOB dla konta magazynu lub go zabronić. Ustaw wartość ustawienia dostęp publiczny kontenera, aby kontenery i obiekty blob były dostępne dla dostępu anonimowego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: a153a3735bbc46dbbce7e58374e1015ac1ec0bfb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133184"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB

Usługa Azure Storage obsługuje opcjonalny anonimowy dostęp do odczytu dla kontenerów i obiektów BLOB. Domyślnie dostęp anonimowy do danych jest nigdy niedozwolony. O ile jawnie nie włączysz dostępu anonimowego, wszystkie żądania do kontenera i jego obiektów BLOB muszą być autoryzowane przy użyciu jednej z Azure Active Directory (Azure AD) lub autoryzacji klucza współużytkowanego. W przypadku skonfigurowania ustawienia publicznego poziomu dostępu kontenera w celu zezwolenia na dostęp anonimowy klienci mogą odczytywać dane w tym kontenerze bez autoryzowania żądania.

> [!WARNING]
> Gdy kontener jest skonfigurowany pod kątem dostępu publicznego, każdy klient może odczytywać dane w tym kontenerze. Dostęp publiczny stwarza potencjalne zagrożenie bezpieczeństwa, więc jeśli twój scenariusz nie jest wymagany, firma Microsoft zaleca, aby nie zezwalać na konto magazynu. Aby uzyskać więcej informacji, zobacz [zapobieganie Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md).

W tym artykule opisano sposób konfigurowania anonimowego publicznego dostępu do odczytu dla kontenera i jego obiektów BLOB. Aby uzyskać informacje na temat uzyskiwania anonimowego dostępu do danych obiektów blob z aplikacji klienckiej, zobacz [dostęp do publicznych kontenerów i obiektów BLOB anonimowo za pomocą platformy .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Anonimowy publiczny dostęp do odczytu — informacje

Publiczny dostęp do danych jest zawsze zabroniony domyślnie. Istnieją dwa oddzielne ustawienia, które mają wpływ na dostęp publiczny:

1. **Zezwalaj na dostęp publiczny do konta magazynu.** Domyślnie konto magazynu zezwala użytkownikowi z odpowiednimi uprawnieniami do umożliwienia publicznego dostępu do kontenera. Dane obiektów BLOB nie są dostępne do publicznego dostępu, chyba że użytkownik wykona dodatkowy krok, aby jawnie skonfigurować ustawienie dostępu publicznego kontenera.
1. **Skonfiguruj ustawienie dostępu publicznego kontenera.** Domyślnie ustawienie dostępu publicznego kontenera jest wyłączone, co oznacza, że autoryzacja jest wymagana dla każdego żądania do kontenera lub jego danych. Użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby włączyć dostęp anonimowy tylko wtedy, gdy dostęp anonimowy jest dozwolony dla konta magazynu.

W poniższej tabeli podsumowano, jak oba ustawienia mają wpływ na publiczny dostęp do kontenera.

| Ustawienie dostępu publicznego | Dostęp publiczny jest wyłączony dla kontenera (ustawienie domyślne) | Dostęp publiczny do kontenera jest ustawiony na kontener | Dostęp publiczny kontener jest ustawiony na obiekt BLOB |
|--|--|--|--|
| Dostęp publiczny jest niedozwolony dla konta magazynu | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. |
| Dostęp publiczny jest dozwolony dla konta magazynu (ustawienie domyślne) | Brak publicznego dostępu do tego kontenera (Konfiguracja domyślna). | Dostęp publiczny jest dozwolony dla tego kontenera i jego obiektów BLOB. | Dostęp publiczny jest dozwolony dla obiektów BLOB w tym kontenerze, ale nie do samego kontenera. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Zezwalaj lub nie Zezwalaj na publiczny dostęp do odczytu dla konta magazynu

Domyślnie dostęp publiczny jest dozwolony dla poszczególnych kontenerów na koncie magazynu. Jeśli dozwolony jest dostęp publiczny, użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby umożliwić anonimowy dostęp publiczny do danych w tym kontenerze.

Należy pamiętać, że publiczny dostęp do kontenera jest zawsze domyślnie wyłączony i musi być jawnie skonfigurowany do zezwalania na żądania anonimowe. Niezależnie od ustawienia na koncie magazynu Twoje dane nigdy nie będą dostępne do publicznego dostępu, chyba że użytkownik z odpowiednimi uprawnieniami podejmie ten dodatkowy krok w celu włączenia publicznego dostępu do kontenera.

Niezezwalanie na dostęp publiczny do konta magazynu uniemożliwia anonimowy dostęp do wszystkich kontenerów i obiektów BLOB w ramach tego konta. Jeśli dostęp publiczny jest niedozwolony dla konta, nie można skonfigurować ustawienia dostępu publicznego dla kontenera w celu zezwolenia na dostęp anonimowy. W celu zwiększenia bezpieczeństwa firma Microsoft zaleca, aby nie zezwalać na dostęp publiczny do kont magazynu, chyba że scenariusz wymaga, aby użytkownicy mieli anonimowe dostęp do zasobów obiektów BLOB.

> [!IMPORTANT]
> Niezezwalanie na dostęp publiczny do konta magazynu zastępuje ustawienia dostępu publicznego dla wszystkich kontenerów na tym koncie magazynu. Gdy dostęp publiczny jest niedozwolony dla konta magazynu, wszelkie przyszłe żądania anonimowe do tego konta będą kończyć się niepowodzeniem. Przed zmianą tego ustawienia należy zapoznać się z tematem wpływ na aplikacje klienckie, które mogą anonimowo uzyskiwać dostęp do danych na koncie magazynu. Aby uzyskać więcej informacji, zobacz [zapobieganie Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md).

Aby zezwolić na dostęp publiczny do konta magazynu lub go uniemożliwić, użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby skonfigurować właściwość **blobPublicAccess** konta. Ta właściwość jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Aby uzyskać więcej informacji, zobacz temat [konto magazynu — Omówienie](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby zezwolić na dostęp publiczny do konta magazynu w Azure Portal lub go uniemożliwić, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia**Znajdź ustawienie **Konfiguracja** .
1. Ustaw **dostęp publiczny obiektu BLOB** na **włączony** lub **wyłączony**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób zezwalania lub zapobiegania dostępowi do dostępu publicznego obiektu BLOB dla konta":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zezwolić na dostęp publiczny do konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure lub go uniemożliwić, należy najpierw uzyskać identyfikator zasobu dla konta magazynu, wywołując polecenie [AZ Resource show](/cli/azure/resource#az-resource-show) . Następnie Wywołaj polecenie [AZ Resource Update](/cli/azure/resource#az-resource-update) , aby ustawić właściwość **allowBlobPublicAccess** dla konta magazynu. Aby zezwolić na dostęp publiczny, ustaw właściwość **allowBlobPublicAccess** na true; Aby nie zezwalać, ustaw dla niego **wartość false**.

Poniższy przykład uniemożliwia publiczny dostęp do obiektów BLOB dla konta magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> Niezezwalanie na dostęp publiczny do konta magazynu nie ma wpływu na żadne statyczne witryny internetowe hostowane na tym koncie magazynu. Kontener **$Web** jest zawsze dostępny publicznie.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Sprawdź, czy dostęp publiczny jest dozwolony dla konta magazynu

Aby sprawdzić, czy dostęp publiczny jest dozwolony dla konta magazynu, Pobierz wartość właściwości **allowBlobPublicAccess** . Aby sprawdzić tę właściwość dla dużej liczby kont magazynu jednocześnie, użyj Eksploratora Azure Resource Graph.

> [!IMPORTANT]
> Właściwość **allowBlobPublicAccess** nie jest domyślnie ustawiona i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona. Konto magazynu umożliwia dostęp publiczny, gdy wartość właściwości jest **równa null** lub jeśli jest **spełniony**.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Sprawdź, czy dostęp publiczny jest dozwolony dla pojedynczego konta magazynu

Aby sprawdzić, czy dostęp publiczny jest dozwolony dla jednego konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Resource show](/cli/azure/resource#az-resource-show) i zapytanie dla właściwości **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Sprawdź, czy dostęp publiczny jest dozwolony dla zestawu kont magazynu

Aby sprawdzić, czy dostęp publiczny jest dozwolony między zestawem kont magazynu z optymalną wydajnością, możesz użyć Eksploratora Azure Resource Graph w Azure Portal. Aby dowiedzieć się więcej o korzystaniu z Eksploratora grafów zasobów, zobacz [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](/azure/governance/resource-graph/first-query-portal).

Uruchomienie następującego zapytania w Eksploratorze grafu zasobów zwraca listę kont magazynu i wyświetla wartość właściwości **allowBlobPublicAccess** dla każdego konta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Ustawianie publicznego poziomu dostępu dla kontenera

Aby udzielić anonimowym użytkownikom dostępu do odczytu do kontenera i jego obiektów blob, najpierw Zezwól na dostęp publiczny do konta magazynu, a następnie ustaw poziom dostępu publicznego kontenera. Jeśli dostęp publiczny zostanie odrzucony dla konta magazynu, nie będzie można skonfigurować dostępu publicznego dla kontenera.

Jeśli dostęp publiczny jest dozwolony dla konta magazynu, można skonfigurować kontener z następującymi uprawnieniami:

- **Brak publicznego dostępu do odczytu:** Do kontenera i jego obiektów BLOB można uzyskać dostęp tylko za pomocą autoryzowanego żądania. Ta opcja jest wartością domyślną dla wszystkich nowych kontenerów.
- **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty blob w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne anonimowo. Klienci anonimowi nie mogą wyliczyć obiektów BLOB w kontenerze.
- **Publiczny dostęp do odczytu dla kontenera i jego obiektów blob:** Dane kontenera i obiektu BLOB mogą być odczytywane przez żądanie anonimowe, z wyjątkiem ustawień uprawnień kontenera i metadanych kontenera. Klienci mogą wyliczać obiekty blob w kontenerze przez żądanie anonimowe, ale nie mogą wyliczać kontenerów na koncie magazynu.

Nie można zmienić publicznego poziomu dostępu dla pojedynczego obiektu BLOB. Poziom dostępu publicznego jest ustawiany tylko na poziomie kontenera.

Aby ustawić publiczny poziom dostępu kontenera, użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure. Można ustawić publiczny poziom dostępu kontenera podczas tworzenia kontenera lub zaktualizować to ustawienie w istniejącym kontenerze.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby zaktualizować poziom dostępu publicznego dla jednego lub kilku istniejących kontenerów w Azure Portal, wykonaj następujące kroki:

1. Przejdź do omówienia konta magazynu w Azure Portal.
1. W obszarze **BLOB Service** w bloku menu wybierz pozycję **kontenery**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj przycisku **Zmień poziom dostępu** , aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **poziom dostępu publicznego** , a następnie kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

    ![Zrzut ekranu przedstawiający sposób ustawiania poziomu dostępu publicznego w portalu](./media/anonymous-read-access-configure/configure-public-access-container.png)

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie można ustawić publicznego poziomu dostępu kontenera. Jeśli zostanie podjęta próba ustawienia publicznego poziomu dostępu kontenera, zobaczysz, że ustawienie jest wyłączone, ponieważ dostęp publiczny jest niedozwolony dla konta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Zrzut ekranu pokazujący, że poziom dostępu publicznego kontenera jest blokowany, gdy publiczny dostęp nie jest dozwolony":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage Container Set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autoryzuj tę operację przez przekazanie klucza konta, parametrów połączenia lub sygnatury dostępu współdzielonego (SAS). Operacja [ustawiania listy ACL kontenera](/rest/api/storageservices/set-container-acl) , która ustawia poziom dostępu publicznego kontenera, nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Poniższy przykład ustawia ustawienia dostępu publicznego dla kontenera, aby umożliwić anonimowy dostęp do kontenera i jego obiektów BLOB. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie można ustawić publicznego poziomu dostępu kontenera. Jeśli podjęto próbę ustawienia publicznego poziomu dostępu kontenera, wystąpi błąd wskazujący, że dostęp publiczny nie jest dozwolony na koncie magazynu.

---

## <a name="check-the-container-public-access-setting"></a>Sprawdź ustawienie dostępu publicznego kontenera

Aby sprawdzić ustawienia dostępu publicznego dla co najmniej jednego kontenera, można użyć Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, jednej z bibliotek klienta usługi Azure Storage lub dostawcy zasobów usługi Azure Storage. Poniższe sekcje zawierają kilka przykładów.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Sprawdź ustawienia dostępu publicznego dla pojedynczego kontenera

Aby uzyskać publiczny poziom dostępu dla co najmniej jednego kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage Container show Permission](/cli/azure/storage/container#az-storage-container-show-permission) . Autoryzuj tę operację przez przekazanie klucza konta, parametrów połączenia lub sygnatury dostępu współdzielonego (SAS). Operacja [pobrania listy ACL kontenera](/rest/api/storageservices/get-container-acl) zwracająca publiczny poziom dostępu do kontenera nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Poniższy przykład odczytuje ustawienia dostępu publicznego dla kontenera. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Sprawdź ustawienia dostępu publicznego dla zestawu kontenerów

Istnieje możliwość sprawdzenia, które kontenery w co najmniej jednym koncie magazynu są skonfigurowane do dostępu publicznego przez wystawienie kontenerów i sprawdzenie ustawienia dostępu publicznego. To podejście jest praktyczną opcją, gdy konto magazynu nie zawiera dużej liczby kontenerów lub gdy sprawdzasz ustawienia na małej liczbie kont magazynu. Wydajność może jednak wystąpić, jeśli spróbujesz wyliczyć dużą liczbę kontenerów.

Poniższy przykład używa programu PowerShell, aby uzyskać ustawienia dostępu publicznego dla wszystkich kontenerów na koncie magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Następne kroki

- [Zapobiegaj Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md)
- [Anonimowe uzyskiwanie dostępu do publicznych kontenerów i obiektów BLOB przy użyciu platformy .NET](anonymous-read-access-client.md)
- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
