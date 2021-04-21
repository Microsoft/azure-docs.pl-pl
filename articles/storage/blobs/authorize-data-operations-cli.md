---
title: Wybieranie sposobu autoryzowania dostępu do danych obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Określ sposób autoryzowania operacji na danych względem danych obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure. Operacje na danych można autoryzować przy użyciu poświadczeń usługi Azure AD, klucza dostępu do konta lub tokenu sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 53ad6dd922c1ccebd79aebcd2966b23b38de00e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776901"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Wybieranie sposobu autoryzowania dostępu do danych obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Storage udostępnia rozszerzenia dla interfejsu wiersza polecenia platformy Azure, które umożliwiają określenie sposobu autoryzowania operacji na danych obiektów blob. Operacje na danych można autoryzować w następujący sposób:

- Z podmiotem zabezpieczeń Azure Active Directory (Azure AD). Firma Microsoft zaleca korzystanie z poświadczeń usługi Azure AD w celu najwyższego bezpieczeństwa i łatwości użycia.
- Przy użyciu klucza dostępu do konta lub tokenu sygnatury dostępu współdzielonego (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Określanie sposobu autoryzacji operacji na danych

Polecenia interfejsu wiersza polecenia platformy Azure służące do odczytywania i zapisywania danych obiektów blob zawierają opcjonalny `--auth-mode` parametr . Określ ten parametr, aby wskazać sposób autoryzacji operacji danych:

- Ustaw parametr na , aby zalogować się przy użyciu podmiotu zabezpieczeń usługi `--auth-mode` `login` Azure AD (zalecane).
- Ustaw parametr na starszą wartość, aby spróbować pobrać klucz dostępu do konta `--auth-mode` do użycia na użytek `key` autoryzacji. Jeśli pominiesz parametr , interfejs wiersza polecenia platformy Azure podejmie również próbę `--auth-mode` pobrania klucza dostępu.

Aby użyć parametru , upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji `--auth-mode` 2.0.46 lub nowszej. Uruchom `az --version` , aby sprawdzić zainstalowaną wersję.

> [!NOTE]
> Jeśli konto magazynu jest zablokowane za pomocą blokady Azure Resource Manager [](/rest/api/storagerp/storageaccounts/listkeys) **tylko** do odczytu, operacja listy kluczy nie jest dozwolona dla tego konta magazynu. **Klucze listy** to operacja POST, a wszystkie operacje POST są blokowane, gdy dla konta skonfigurowano blokadę **ReadOnly.** Z tego powodu, gdy konto jest zablokowane z blokadą **Tylko** odczyt, użytkownicy, którzy nie mają jeszcze kluczy kont, muszą używać poświadczeń usługi Azure AD, aby uzyskać dostęp do danych obiektów blob.

> [!IMPORTANT]
> Jeśli pominiesz parametr lub ustawisz go na wartość , interfejs wiersza polecenia platformy Azure spróbuje użyć klucza dostępu `--auth-mode` do konta w celu `key` autoryzacji. W takim przypadku firma Microsoft zaleca podanie klucza dostępu w poleceniu lub w zmiennej **AZURE_STORAGE_KEY** środowiskowej. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz sekcję zatytułowaną [Ustawianie zmiennych środowiskowych dla parametrów autoryzacji](#set-environment-variables-for-authorization-parameters).
>
> Jeśli nie podaniem klucza dostępu, interfejs wiersza polecenia platformy Azure spróbuje wywołać dostawcę zasobów usługi Azure Storage w celu pobrania go dla każdej operacji. Wykonywanie wielu operacji na danych, które wymagają wywołania do dostawcy zasobów, może spowodować ograniczenie przepustowości. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [Scalability and performance targets for the Azure Storage resource provider (Cele](../common/scalability-targets-resource-provider.md)dotyczące skalowalności i wydajności dostawcy zasobów usługi Azure Storage).

## <a name="authorize-with-azure-ad-credentials"></a>Autoryzowanie przy użyciu poświadczeń usługi Azure AD

Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD jest zwracany token dostępu OAuth 2.0. Ten token jest automatycznie używany przez interfejs wiersza polecenia platformy Azure do autoryzowania kolejnych operacji na danych w usłudze Blob Storage lub Queue Storage. W przypadku obsługiwanych operacji nie trzeba już przekazywać klucza konta ani tokenu SAS za pomocą polecenia .

Uprawnienia do danych obiektów blob można przypisywać do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu na podstawie ról (RBAC) platformy Azure. Aby uzyskać więcej informacji na temat ról platformy Azure w usłudze Azure Storage, zobacz Manage access rights to Azure Storage data with Azure RBAC (Zarządzanie prawami dostępu do [danych usługi Azure Storage przy użyciu kontroli dostępu na rolach platformy Azure).](../common/storage-auth-aad-rbac-portal.md)

### <a name="permissions-for-calling-data-operations"></a>Uprawnienia do wywoływania operacji na danych

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów blob. Operacje, które można wywołać, zależą od uprawnień przyznanych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure. Uprawnienia do kontenerów usługi Azure Storage są przypisywane za pośrednictwem kontroli RBAC platformy Azure. Jeśli na przykład masz przypisaną rolę Czytelnik danych obiektu blob usługi **Storage,** możesz uruchamiać polecenia skryptowe, które odczytują dane z kontenera. Jeśli masz przypisaną rolę Współautor danych obiektu blob usługi **Storage,** możesz uruchamiać polecenia skryptowe, które odczytują, zapisują lub usuwają kontener lub dane, które zawiera.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze, zobacz Call storage operations with OAuth tokens (Wywołaj operacje magazynu [z tokenami OAuth).](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Przykład: Autoryzowanie operacji w celu utworzenia kontenera przy użyciu poświadczeń usługi Azure AD

W poniższym przykładzie pokazano, jak utworzyć kontener z interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby utworzyć kontener, musisz zalogować się do interfejsu wiersza polecenia platformy Azure i musisz mieć grupę zasobów i konto magazynu. Aby dowiedzieć się, jak utworzyć te zasoby, zobacz [Szybki start: tworzenie, pobieranie](../blobs/storage-quickstart-blobs-cli.md)i wyświetlanie listy obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure.

1. Przed utworzeniem kontenera przypisz do siebie rolę Współautor danych obiektu blob usługi [Storage.](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz Use the Azure Portal to assign an Azure role for access to blob and queue data (Przypisywanie roli platformy Azure w celu uzyskania dostępu do danych [obiektów blob i kolejek).](../common/storage-auth-aad-rbac-portal.md)

    > [!IMPORTANT]
    > Propagacja przypisań ról platformy Azure może potrwać kilka minut.

1. Wywołaj [polecenie az storage container create](/cli/azure/storage/container#az_storage_container_create) z `--auth-mode` parametrem ustawionym na , `login` aby utworzyć kontener przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoryzacja przy użyciu klucza dostępu do konta

Jeśli posiadasz klucz konta, możesz wywołać dowolną operację danych usługi Azure Storage. Ogólnie rzecz biorąc, korzystanie z klucza konta jest mniej bezpieczne. W przypadku naruszenia zabezpieczeń klucza konta wszystkie dane na Twoim koncie mogą zostać naruszone.

W poniższym przykładzie pokazano, jak utworzyć kontener przy użyciu klucza dostępu do konta. Określ klucz konta i podaj `--auth-mode` parametr z `key` wartością:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Gdy konto magazynu jest zablokowane za pomocą blokady Azure Resource Manager [](/rest/api/storagerp/storageaccounts/listkeys) **ReadOnly,** operacja listy kluczy nie jest dozwolona dla tego konta magazynu. **Klucze listy** to operacja POST, a wszystkie operacje POST są blokowane, gdy dla konta skonfigurowano blokadę **ReadOnly.** Z tego powodu, gdy konto jest zablokowane z blokadą **ReadOnly,** użytkownicy muszą uzyskać dostęp do danych przy użyciu poświadczeń usługi Azure AD.

## <a name="authorize-with-a-sas-token"></a>Autoryzacja przy użyciu tokenu SAS

Jeśli masz token SAS, możesz wywołać operacje na danych, które są dozwolone przez sygnaturę dostępu współdzielonego. W poniższym przykładzie pokazano, jak utworzyć kontener przy użyciu tokenu SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ustawianie zmiennych środowiskowych dla parametrów autoryzacji

Możesz określić parametry autoryzacji w zmiennych środowiskowych, aby uniknąć ich uwzględnienia przy każdym wywołaniu operacji danych usługi Azure Storage. W poniższej tabeli opisano dostępne zmienne środowiskowe.

| Zmienna środowiskowa                  | Opis                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Nazwa konta magazynu. Tej zmiennej należy używać w połączeniu z kluczem konta magazynu lub tokenem SAS. Jeśli żadna z nich nie istnieje, interfejs wiersza polecenia platformy Azure próbuje pobrać klucz dostępu do konta magazynu przy użyciu uwierzytelnionego konta usługi Azure AD. Jeśli jednocześnie wykonywanych jest wiele poleceń, może zostać osiągnięty limit ograniczania dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [Scalability and performance targets for the Azure Storage resource provider (Cele](../common/scalability-targets-resource-provider.md)dotyczące skalowalności i wydajności dla dostawcy zasobów usługi Azure Storage).             |
|    AZURE_STORAGE_KEY                  |    Klucz konta magazynu. Tej zmiennej należy używać w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Parametrów połączenia, które obejmują klucz konta magazynu lub token SAS. Tej zmiennej należy używać w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sygnatury dostępu współdzielonego (SAS). Tej zmiennej należy używać w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Tryb autoryzacji, za pomocą którego należy uruchomić polecenie. Dozwolone wartości to `login` (zalecane) lub `key` . Jeśli określisz wartość , interfejs wiersza polecenia platformy Azure autoryzuje operację danych przy użyciu `login` poświadczeń usługi Azure AD. Jeśli określisz starszy tryb, interfejs wiersza polecenia platformy Azure spróbuje uzyskać klucz dostępu do konta i `key` autoryzować polecenie przy użyciu klucza.    |

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli platformy Azure na użytek dostępu do danych obiektów blob i kolejek przy użyciu interfejsu wiersza polecenia platformy Azure](../common/storage-auth-aad-rbac-cli.md)
- [Autoryzowanie dostępu do danych obiektów blob i kolejek przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](../common/storage-auth-aad-msi.md)