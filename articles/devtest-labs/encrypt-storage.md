---
title: Szyfruj konto usługi Azure Storage używane przez laboratorium w Azure DevTest Labs
description: Dowiedz się, jak skonfigurować szyfrowanie usługi Azure Storage używanej przez laboratorium w Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149317"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Szyfruj magazyn platformy Azure używany przez laboratorium w Azure DevTest Labs
Każde laboratorium utworzone w Azure DevTest Labs jest tworzone przy użyciu skojarzonego konta usługi Azure Storage. Konto magazynu jest używane w następujących celach: 

- Przechowywanie dokumentów [formuł](devtest-lab-manage-formulas.md) , których można użyć do tworzenia maszyn wirtualnych.
- Przechowywanie wyników artefaktów zawierających dzienniki wdrożenia i rozszerzenia wygenerowane na podstawie zastosowania artefaktów. 
- [Przekazywanie wirtualnych dysków twardych (VHD) do tworzenia obrazów niestandardowych w laboratorium.](devtest-lab-create-template.md)
- Buforowanie często używanych [artefaktów](add-artifact-vm.md) i [szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md) do szybszego pobierania podczas tworzenia maszyny wirtualnej/środowiska.

> [!NOTE]
> Powyższe informacje mają kluczowe znaczenie dla laboratorium do działania. Jest on przechowywany w okresie istnienia laboratorium (i zasobów Lab), chyba że zostanie jawnie usunięty. Ręczne usunięcie tych zasobów może prowadzić do błędów podczas tworzenia maszyn wirtualnych laboratorium i/lub formuł do uszkodzenia w przyszłości. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Znajdź konto magazynu i Wyświetl jego zawartość

1. Na stronie głównej laboratorium wybierz **grupę zasobów** na stronie **Przegląd** . Powinna zostać wyświetlona strona **grupy zasobów** dla grupy zasobów zawierającej laboratorium. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Wybierz grupę zasobów na stronie Przegląd":::
1. Wybierz konto usługi Azure Storage dla laboratorium. Konwencja nazewnictwa dla konta magazynu laboratorium to: `a<labNameWithoutInvalidCharacters><4-digit number>` . Na przykład, jeśli nazwa laboratorium to `contosolab` , nazwa konta magazynu może być taka sama `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Wybierz konto magazynu w grupie zasobów laboratorium":::
3. Na stronie **konto magazynu** wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)** w menu po lewej stronie, a następnie wybierz pozycję **kontenery obiektów BLOB** , aby znaleźć odpowiednią zawartość powiązaną z laboratorium. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Eksplorator usługi Storage (wersja zapoznawcza)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Szyfrowanie konta magazynu laboratorium
Usługa Azure Storage automatycznie szyfruje dane, gdy są utrwalane w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga sprostać zobowiązaniom dotyczącym zabezpieczeń i zgodności w organizacji. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).

Dane na koncie magazynu laboratorium są szyfrowane za pomocą **klucza zarządzanego przez firmę Microsoft**. Możesz polegać na kluczach zarządzanych przez firmę Microsoft na potrzeby szyfrowania danych. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy. W przypadku wybrania opcji zarządzania szyfrowaniem przy użyciu własnych kluczy konta magazynu laboratorium można określić **klucz zarządzany przez klienta** z Azure Key Vault, który będzie używany do szyfrowania/odszyfrowywania danych w usłudze BLOB Storage i w Azure Files. Więcej informacji o kluczach zarządzanych przez klienta znajduje się w temacie [Używanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault do zarządzania szyfrowaniem usługi Azure Storage](../storage/common/customer-managed-keys-overview.md).

Aby dowiedzieć się, jak skonfigurować klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage, zobacz następujące artykuły: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Interfejs wiersza polecenia platformy Azure](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Zarządzanie cyklem życia usługi Azure Blob Storage
Jak wspomniano wcześniej, informacje przechowywane na koncie magazynu laboratorium mają kluczowe znaczenie dla laboratorium, które ma działać bez żadnych błędów. O ile nie zostanie on jawnie usunięty, te dane nadal pozostają na koncie magazynu laboratorium na potrzeby cyklu życia laboratorium lub cyklu życia określonych maszyn wirtualnych laboratorium, w zależności od typu danych.

### <a name="uploaded-vhds"></a>Przekazane wirtualne dyski twarde
Te wirtualne dyski twarde są używane do tworzenia obrazów niestandardowych. Usunięcie ich spowoduje, że nie będzie już można tworzyć obrazów niestandardowych z tych wirtualnych dysków twardych.

### <a name="artifacts-cache"></a>Pamięć podręczna artefaktów
Te pamięci podręczne zostaną ponownie utworzone w przypadku zastosowania artefaktów czasu. Zostaną one odświeżone przy użyciu najnowszej zawartości z odpowiednich repozytoriów, do których się odwołuje. W związku z tym, jeśli usuniesz te informacje w celu zapisania wydatków związanych z magazynem, zwolnienie będzie tymczasowe.

### <a name="azure-resource-manager-template-cache"></a>Pamięć podręczna szablonów Azure Resource Manager
Te pamięci podręczne zostaną ponownie utworzone dla wszystkich repozytoriów szablonów opartych na Azure Resource Managerach, które są połączone i można je zainstalować w środowisku laboratoryjnym. Zostaną one odświeżone przy użyciu najnowszej zawartości z odpowiednich repozytoriów, do których się odwołuje. W związku z tym, jeśli usuniesz te informacje w celu zapisania wydatków związanych z magazynem, zwolnienie będzie tymczasowe.

### <a name="formulas"></a>Formuły
Te dokumenty są używane do obsługi opcji zarówno do tworzenia formuł z istniejących maszyn wirtualnych, jak i tworzenia maszyn wirtualnych na podstawie formuł. Usunięcie tych dokumentów formuły może prowadzić do błędów podczas wykonywania następujących operacji:

- Tworzenie formuły z istniejącej maszyny wirtualnej laboratorium
- Tworzenie lub aktualizowanie formuł 
- Tworzenie maszyny wirtualnej na podstawie formuły.

### <a name="artifact-results"></a>Wyniki artefaktu
W miarę stosowania artefaktów rozmiar odpowiednich wyników artefaktu może wzrosnąć w czasie w zależności od liczby i typu artefaktów uruchamianych na maszynach wirtualnych laboratorium. Dlatego jako właściciel laboratorium możesz chcieć kontrolować cykl życia takich dokumentów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Zalecamy wykonanie tego kroku w celu obniżenia wydatków związanych z kontem usługi Azure Storage. 

Na przykład Poniższa reguła służy do ustawiania 90-dniowej reguły wygaśnięcia dla wyników artefaktów. Gwarantuje to, że starsze wyniki artefaktów są odtwarzane z konta magazynu w regularnych erze.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak skonfigurować klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage, zobacz następujące artykuły: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Interfejs wiersza polecenia platformy Azure](../storage/common/customer-managed-keys-configure-key-vault.md)