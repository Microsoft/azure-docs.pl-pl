---
title: Co zrobić, jeśli wystąpi przerwanie działania usługi platformy Azure, które ma wpływ na zarządzane moduły HSM Azure Key Vault | Microsoft Docs
description: Dowiedz się, co zrobić, aby wykonać f, istnieje przerwa w działaniu usługi platformy Azure, która wpływa na zarządzane moduły HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 7dbb7b3fdc15c0a9d502fbe9a0d12d084f9ddf29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760397"
---
# <a name="managed-hsm-disaster-recovery"></a>Odzyskiwanie po awarii zarządzanego modułu HSM

Można utworzyć dokładną replikę modułu HSM, Jeśli oryginał zostanie utracony lub niedostępny z jednego z poniższych powodów:

- Został usunięty, a następnie przeczyszczony.
- Katastrofalny błąd w regionie spowodował zniszczenie wszystkich partycji elementów członkowskich.

Wystąpienie modułu HSM można utworzyć ponownie w tym samym lub innym regionie, jeśli są dostępne następujące elementy:
- [Domena zabezpieczeń](security-domain.md) źródłowego modułu HSM.
- Klucze prywatne (co najmniej numer kworum), które szyfrują domenę zabezpieczeń.
- Najnowsza pełna [kopia zapasowa](backup-restore.md) modułu HSM ze źródłowego modułu HSM.

Poniżej przedstawiono kroki procedury odzyskiwania po awarii:

1. Utwórz nowe wystąpienie modułu HSM.
1. Aktywuj "odzyskiwanie domeny zabezpieczeń". Nowa para kluczy RSA (klucz wymiany domeny zabezpieczeń) zostanie wygenerowana na potrzeby transferu domeny zabezpieczeń i wysyłana w odpowiedzi, która zostanie pobrana jako SecurityDomainExchangeKey (klucz publiczny).
1. Utwórz, a następnie Przekaż "plik transferu domeny zabezpieczeń". Wymagane są klucze prywatne, które szyfrują domenę zabezpieczeń. Klucze prywatne są używane lokalnie i nigdy nie są przesyłane w dowolnym miejscu w ramach tego procesu.
1. Utwórz kopię zapasową nowego modułu HSM. Kopia zapasowa jest wymagana przed przywróceniem, nawet jeśli moduł HSM jest pusty. Kopie zapasowe umożliwiają łatwe wycofywanie.
1. Przywróć ostatnią kopię zapasową modułu HSM ze źródłowego modułu HSM

Zawartość Twojego magazynu kluczy jest replikowana w regionie i do regionu pomocniczego, co najmniej 150 kilometrów, ale w tej samej lokalizacji geograficznej. Ta funkcja zapewnia wysoką trwałość kluczy i wpisów tajnych. Aby uzyskać szczegółowe informacje na temat konkretnych par regionów, zobacz dokument dotyczący [sparowanych regionów platformy Azure](../../best-practices-availability-paired-regions.md) .

## <a name="create-a-new-managed-hsm"></a>Tworzenie nowego zarządzanego modułu HSM

Użyj `az keyvault create` polecenia, aby utworzyć zarządzany moduł HSM. Ten skrypt ma trzy parametry obowiązkowe: nazwę grupy zasobów, nazwę modułu HSM i lokalizację geograficzną.

Musisz podać następujące dane wejściowe, aby utworzyć zarządzany zasób modułu HSM:

- Nazwa modułu HSM.
- Grupa zasobów, w której zostanie umieszczona w Twojej subskrypcji.
- Lokalizacja platformy Azure.
- Lista administratorów początkowych.

Poniższy przykład tworzy moduł HSM o nazwie **ContosoMHSM**w grupie zasobów  **ContosoResourceGroup**, znajdujący się w lokalizacji **Wschodnie stany USA 2** , z **obecnie zalogowanym użytkownikiem** jako jedynego administratora.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Polecenie CREATE może potrwać kilka minut. Po pomyślnym powrocie można aktywować moduł HSM.

Dane wyjściowe tego polecenia przedstawiają właściwości zarządzanego modułu HSM, który został utworzony. Dwie najważniejsze właściwości to:

* **Nazwa**: w tym przykładzie nazwa to ContosoMHSM. Ta nazwa będzie używana do innych poleceń Key Vault.
* **hsmUri**: w przykładzie identyfikator URI to " https://contosohsm.managedhsm.azure.net ." Aplikacje korzystające z modułu HSM za pomocą interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym zarządzanym module HSM. Od tej pory nikt nie jest autoryzowany.

## <a name="activate-the-security-domain-recovery-mode"></a>Aktywuj tryb odzyskiwania domeny zabezpieczeń

W przypadku normalnego procesu tworzenia w tym momencie inicjujemy i pobieramy nową domenę zabezpieczeń. Jednak ponieważ wykonujemy procedurę odzyskiwania po awarii, żądanie modułu HSM do wejścia w tryb odzyskiwania domeny zabezpieczeń i pobieranie klucza wymiany domeny zabezpieczeń. Klucz wymiany domeny zabezpieczeń jest kluczem publicznym RSA, który będzie używany do szyfrowania domeny zabezpieczeń przed przekazaniem go do modułu HSM. Odpowiedni klucz prywatny jest chroniony wewnątrz modułu HSM, aby zachować bezpieczeństwo zawartości domeny zabezpieczeń podczas transferu.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Przekaż domenę zabezpieczeń do docelowego modułu HSM

W tym kroku będą potrzebne następujące elementy:
- Klucz wymiany domeny zabezpieczeń pobrany w poprzednim kroku.
- Domena zabezpieczeń źródłowego modułu HSM.
- Co najmniej numer kworum kluczy prywatnych, które były używane do szyfrowania domeny zabezpieczeń.

`az keyvault security-domain upload`Polecenie wykonuje następujące operacje:

- Odszyfruj źródłową domenę zabezpieczeń modułu HSM przy użyciu dostarczonych kluczy prywatnych. 
- Utwórz obiekt BLOB przekazywania w domenie zabezpieczeń zaszyfrowany przy użyciu klucza wymiany domeny zabezpieczeń, który został pobrany w poprzednim kroku, a następnie
- Przekaż obiekt BLOB przekazywania domeny zabezpieczeń do modułu HSM, aby ukończyć odzyskiwanie domeny zabezpieczeń

W poniższym przykładzie stosujemy domenę zabezpieczeń z **ContosoMHSM**, 2 z odpowiednich kluczy prywatnych i przekażemy ją do **ContosoMHSM2**, która oczekuje na odebranie domeny zabezpieczeń. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Teraz zarówno źródłowy moduł HSM (ContosoMHSM), jak i docelowy moduł HSM (ContosoMHSM2) mają tę samą domenę zabezpieczeń. Teraz możemy przywrócić pełną kopię zapasową ze źródłowego modułu HSM do docelowego modułu HSM.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Utwórz kopię zapasową (jako punkt przywracania) nowego modułu HSM

Zawsze dobrym pomysłem jest utworzenie pełnej kopii zapasowej przed wykonaniem pełnego przywracania modułu HSM, tak aby punkt przywracania miał miejsce w przypadku, gdy wystąpił problem z przywróceniem.

Aby utworzyć kopię zapasową modułu HSM, potrzebne są następujące elementy:
- Konto magazynu, na którym będzie przechowywana kopia zapasowa
- Kontener magazynu obiektów BLOB na tym koncie magazynu, w którym proces tworzenia kopii zapasowej utworzy nowy folder do przechowywania zaszyfrowanej kopii zapasowej

Używamy `az keyvault backup` polecenia do tworzenia kopii zapasowych modułu HSM w kontenerze magazynu **mhsmbackupcontainer**, który znajduje się na koncie magazynu **ContosoBackup** w poniższym przykładzie. Utworzymy token sygnatury dostępu współdzielonego, który wygaśnie w ciągu 30 minut i zapewnisz, że w celu utworzenia kopii zapasowej zarządzany moduł HSM.

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Przywróć kopię zapasową ze źródłowego modułu HSM

W tym kroku są potrzebne następujące elementy:

- Konto magazynu i kontener obiektów blob, w których są przechowywane kopie zapasowe źródłowego modułu HSM.
- Nazwa folderu, z którego ma zostać przywrócona kopia zapasowa. Jeśli tworzysz regularne kopie zapasowe, w tym kontenerze będzie wiele folderów.


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Teraz ukończono pełny proces odzyskiwania po awarii. Zawartość źródłowego modułu HSM podczas tworzenia kopii zapasowej jest kopiowana do docelowego modułu HSM, w tym wszystkich kluczy, wersji, atrybutów, tagów i przypisań ról.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o domenie zabezpieczeń, zobacz [Informacje o zarządzanej domenie zabezpieczeń modułu HSM](security-domain.md)
- Postępuj zgodnie z [najlepszymi rozwiązaniami modułu HSM](best-practices.md)
