---
title: Wybieranie metody autoryzacji dostępu do danych obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Określ sposób autoryzacji operacji na danych obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure. Operacje na danych można autoryzować przy użyciu poświadczeń usługi Azure AD, klucza dostępu do konta lub z tokenem sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06b37e8b25d932115384124a45156c801fb9708f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361676"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Wybieranie metody autoryzacji dostępu do danych obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Storage udostępnia rozszerzenia interfejsu wiersza polecenia platformy Azure, które umożliwiają określenie sposobu autoryzacji operacji na danych obiektów BLOB. Operacje na danych można autoryzować w następujący sposób:

- Za pomocą podmiotu zabezpieczeń Azure Active Directory (Azure AD). Firma Microsoft zaleca korzystanie z poświadczeń usługi Azure AD w celu zapewnienia bezpieczeństwa i łatwość użycia.
- Za pomocą klucza dostępu do konta lub tokenu sygnatury dostępu współdzielonego (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Określ sposób autoryzacji operacji na danych

Polecenie interfejsu wiersza polecenia platformy Azure służące do odczytywania i zapisywania danych obiektów BLOB zawiera opcjonalny `--auth-mode` parametr. Określ ten parametr, aby wskazać, w jaki sposób ma być autoryzowana operacja na danych:

- Ustaw `--auth-mode` parametr na `login` , aby zalogować się przy użyciu podmiotu zabezpieczeń usługi Azure AD (zalecane).
- Ustaw `--auth-mode` parametr na starszą `key` wartość, aby próbować pobrać klucz dostępu konta do użycia na potrzeby autoryzacji. Jeśli pominięto `--auth-mode` parametr, interfejs wiersza polecenia platformy Azure podejmie również próbę pobrania klucza dostępu.

Aby użyć `--auth-mode` parametru, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom `az --version` , aby sprawdzić zainstalowaną wersję.

> [!NOTE]
> [W przypadku](/rest/api/storagerp/storageaccounts/listkeys) zablokowania konta magazynu z Azure Resource Manager blokadą **tylko do odczytu** nie jest dozwolone dla tego konta magazynu. **Klucze list** są operacją post, a wszystkie operacje post są blokowane, gdy dla konta skonfigurowano blokadę **tylko do odczytu** . Z tego powodu, gdy konto jest zablokowane w przypadku blokady **tylko do odczytu** , użytkownicy, którzy nie posiadają jeszcze kluczy konta, muszą używać poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB.

> [!IMPORTANT]
> Jeśli pominięto `--auth-mode` parametr lub ustawisz go na `key` , interfejs wiersza polecenia platformy Azure próbuje użyć klucza dostępu do konta w celu autoryzacji. W tym przypadku firma Microsoft zaleca, aby podać klucz dostępu przy użyciu polecenia lub zmiennej środowiskowej **AZURE_STORAGE_KEY** . Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zapoznaj się z sekcją [Ustawianie zmiennych środowiskowych dla parametrów autoryzacji](#set-environment-variables-for-authorization-parameters).
>
> Jeśli nie podasz klucza dostępu, interfejs wiersza polecenia platformy Azure podejmie próbę wywołania dostawcy zasobów usługi Azure Storage w celu pobrania go dla każdej operacji. Wykonanie wielu operacji na danych, które wymagają wywołania dostawcy zasobów może spowodować ograniczenie przepustowości. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [cele skalowalności i wydajności dla dostawcy zasobów usługi Azure Storage](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autoryzuj przy użyciu poświadczeń usługi Azure AD

Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez interfejs wiersza polecenia platformy Azure do autoryzowania kolejnych operacji na danych w odniesieniu do magazynu obiektów blob lub Queue. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Uprawnienia do danych obiektów BLOB można przypisywać do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat ról platformy Azure w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą funkcji RBAC platformy Azure](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Uprawnienia do wywoływania operacji na danych

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów BLOB. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure. Uprawnienia do kontenerów usługi Azure Storage są przypisywane za pośrednictwem usługi Azure RBAC. Na przykład, jeśli przypisano rolę **czytnika danych obiektów blob magazynu** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera. Jeśli przypisano rolę **współautor danych obiektów blob magazynu** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub zawarte w nim dane.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze, zobacz [wywoływanie operacji magazynu za pomocą tokenów OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Przykład: Autoryzuj operację do tworzenia kontenera przy użyciu poświadczeń usługi Azure AD

Poniższy przykład pokazuje, jak utworzyć kontener z interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby utworzyć kontener, należy zalogować się do interfejsu wiersza polecenia platformy Azure i będzie potrzebna Grupa zasobów i konto magazynu. Aby dowiedzieć się, jak utworzyć te zasoby, zobacz [Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md).

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról platformy Azure może potrwać kilka minut.

1. Wywołaj polecenie [AZ Storage Container Create](/cli/azure/storage/container#az-storage-container-create) z `--auth-mode` parametrem ustawionym na, aby `login` utworzyć kontener przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoryzuj przy użyciu klucza dostępu do konta

Jeśli posiadasz klucz konta, możesz wywołać dowolną operację danych usługi Azure Storage. Ogólnie rzecz biorąc, użycie klucza konta jest mniej bezpieczne. W przypadku naruszenia zabezpieczeń klucza konta mogą zostać naruszone wszystkie dane na koncie.

Poniższy przykład pokazuje, jak utworzyć kontener przy użyciu klucza dostępu do konta. Określ klucz konta i podaj `--auth-mode` parametr z `key` wartością:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> [W przypadku](/rest/api/storagerp/storageaccounts/listkeys) zablokowania konta magazynu z Azure Resource Manager blokadą **tylko do odczytu** nie jest dozwolone dla tego konta magazynu. **Klucze list** są operacją post, a wszystkie operacje post są blokowane, gdy dla konta skonfigurowano blokadę **tylko do odczytu** . Z tego powodu po zablokowaniu konta z blokadą **tylko do odczytu** użytkownicy muszą uzyskać dostęp do danych przy użyciu poświadczeń usługi Azure AD.

## <a name="authorize-with-a-sas-token"></a>Autoryzuj przy użyciu tokenu SAS

Jeśli masz token SAS, możesz wywołać operacje na danych, które są dozwolone przez sygnaturę dostępu współdzielonego. Poniższy przykład pokazuje, jak utworzyć kontener przy użyciu tokenu sygnatury dostępu współdzielonego:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ustawianie zmiennych środowiskowych dla parametrów autoryzacji

Możesz określić parametry autoryzacji w zmiennych środowiskowych, aby uniknąć uwzględniania ich przy każdym wywołaniu operacji na danych usługi Azure Storage. W poniższej tabeli opisano dostępne zmienne środowiskowe.

| Zmienna środowiskowa                  | Opis                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Nazwa konta magazynu. Ta zmienna powinna być używana w połączeniu z kluczem konta magazynu lub tokenem sygnatury dostępu współdzielonego. Jeśli żaden z nich nie istnieje, interfejs wiersza polecenia platformy Azure próbuje pobrać klucz dostępu konta magazynu za pomocą uwierzytelnionego konta usługi Azure AD. Jeśli wiele poleceń jest wykonywanych w tym samym czasie, może być osiągnięty limit ograniczenia dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [cele skalowalności i wydajności dla dostawcy zasobów usługi Azure Storage](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Klucz konta magazynu. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Parametry połączenia, które obejmują klucz konta magazynu lub token sygnatury dostępu współdzielonego. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sygnatury dostępu współdzielonego (SAS). Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Tryb autoryzacji, z którym ma zostać uruchomione polecenie. Dozwolone wartości to `login` (zalecane) lub `key` . Jeśli określisz `login` , interfejs wiersza polecenia platformy Azure używa poświadczeń usługi Azure AD do autoryzacji operacji na danych. W przypadku określenia starszego `key` trybu interfejs wiersza polecenia platformy Azure próbuje wykonać zapytanie o klucz dostępu do konta i autoryzować polecenie przy użyciu klucza.    |

## <a name="next-steps"></a>Następne kroki

- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-cli.md)
- [Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure](../common/storage-auth-aad-msi.md)