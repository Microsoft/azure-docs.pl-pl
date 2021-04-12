---
title: Co nowego w Azure Key Vault
description: Najnowsze aktualizacje dla Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 01/12/2020
ms.author: mbaldwin
ms.openlocfilehash: ef2c42ae76dc5e1511494635904228a8b574d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132192"
---
# <a name="whats-new-for-azure-key-vault"></a>Co nowego w Azure Key Vault

W tym miejscu nowości nowe Azure Key Vault. Nowe funkcje i ulepszenia są również ogłoszone w [kanale usługi Azure updates Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="october-2020"></a>Październik 2020 r.

> [!WARNING]
> Te aktualizacje mają możliwość wpływu na implementacje Azure Key Vault.

Aby obsłużyć opcję [usuwania nietrwałego teraz włączona domyślnie](#soft-delete-on-by-default), do Azure Key Vault poleceń cmdlet programu PowerShell wprowadzono dwie zmiany:

- Parametry DisableSoftDelete i EnableSoftDelete elementu [Update-AzKeyVault](/powershell/module/az.keyvault/update-azkeyvault) zostały wycofane.
- Dane wyjściowe polecenia cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) nie mają już `SecretValueText` atrybutu.

## <a name="july-2020"></a>Lipiec 2020 r.

> [!WARNING]
> Te dwie aktualizacje mają możliwość wpływu na implementacje Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Domyślnie Usuń nietrwałe

**Aby można było włączyć opcję usuwania nietrwałego dla wszystkich magazynów kluczy**, zarówno nowych, jak i istniejących. W ciągu następnych kilku miesięcy możliwość rezygnacji z usuwania nietrwałego będzie przestarzała. Aby uzyskać szczegółowe informacje na temat tej potencjalnej zmiany, a także kroki znajdowania odpowiednich magazynów kluczy i zaktualizować je wcześniej, zobacz artykuł [soft-DELETE zostanie włączony dla wszystkich magazynów kluczy](soft-delete-change.md).

### <a name="azure-tls-certificate-changes"></a>Zmiany certyfikatu protokołu TLS platformy Azure

Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Ta zmiana jest wykonywana, ponieważ bieżące certyfikaty urzędu certyfikacji nie są zgodne z jednym z wymagań linii bazowej urzędu certyfikacji/przeglądarki.  Aby uzyskać szczegółowe informacje, zobacz [zmiany certyfikatu protokołu TLS platformy Azure](../../security/fundamentals/tls-certificate-changes.md).

## <a name="june-2020"></a>Czerwiec 2020 r.

Azure Monitor dla Key Vault jest teraz w wersji zapoznawczej.  Azure Monitor zapewnia kompleksowe monitorowanie magazynów kluczy, udostępniając ujednolicony wgląd w żądania Key Vault, wydajność, błędy i opóźnienia. Aby uzyskać więcej informacji, zobacz [Azure monitor for Key Vault (wersja zapoznawcza).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maj 2020 r.

Key Vault "Przenieś swój własny klucz" (BYOK) jest teraz ogólnie dostępny. Zapoznaj się ze [specyfikacją Azure Key Vault BYOK](../keys/byok-specification.md)i Dowiedz się, jak [zaimportować klucze chronione przez moduł HSM do Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Marzec 2020 r.

Prywatne punkty końcowe są teraz dostępne w wersji zapoznawczej. Usługa link prywatny platformy Azure umożliwia dostęp do Azure Key Vault i hostowanych usług partnerskich klientów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.  Dowiedz się, jak [zintegrować Key Vault z prywatnym łączem platformy Azure](private-link-service.md).

## <a name="2019"></a>2019

- Wydanie zestawów SDK Azure Key Vault nowej generacji. Przykłady ich użycia znajdują się w temacie Azure Key Vaulte Przewodniki Szybki Start dla języka [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)i [Node.js](../secrets/quick-create-node.md)
- Nowe zasady platformy Azure do zarządzania certyfikatami magazynu kluczy. Zapoznaj się z [Azure Policy wbudowanymi definicjami Key Vault](../policy-reference.md).
- Azure Key Vault rozszerzenie maszyny wirtualnej jest teraz ogólnie dostępne.  Zobacz [Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/key-vault-linux.md) i [Key Vault rozszerzenie maszyny wirtualnej w systemie Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Zarządzanie kluczami tajnymi opartymi na zdarzeniach dla Azure Key Vault jest teraz dostępne w programie Azure Event Grid. Aby uzyskać więcej informacji, zobacz [schemat Event Grid dla zdarzeń w Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD] i Dowiedz się, jak [odbierać powiadomienia magazynu kluczy i odpowiadać na nie za pomocą Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nowe funkcje i integracje wydane w tym roku:

- Integracja z Azure Functions. Aby zapoznać się z przykładowym scenariuszem dotyczącym [Azure Functions](../../azure-functions/index.yml) operacji magazynu kluczy, zobacz [Automatyzowanie obrotu klucza tajnego](../secrets/tutorial-rotation.md).
- [Integracja z Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). W tym Azure Databricks teraz obsługiwane są dwa typy zakresów tajnych: Azure Key Vault-kopia zapasowa i datakostki — kopia zapasowa. Aby uzyskać więcej informacji, zobacz [Tworzenie zakresu tajnego kopii zapasowej Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nowe funkcje wydane w tym roku:

- Zarządzane klucze konta magazynu. Funkcja kluczy konta magazynu ułatwia integrację z usługą Azure Storage. Aby uzyskać więcej informacji, zobacz temat Omówienie [zarządzanych kluczy kont magazynu](../secrets/overview-storage-keys.md).
- Usuwanie nietrwałe. Funkcja usuwania nietrwałego usprawnia ochronę danych magazynów kluczy i obiektów magazynu kluczy. Aby uzyskać więcej informacji, zobacz temat Omówienie [usuwania nietrwałego](./soft-delete-overview.md).

## <a name="2015"></a>2015

Nowe funkcje wydane w tym roku:
- Zarządzanie certyfikatami. Dodano jako funkcję do wersji GA 2015-06-01 w dniu 26 września 2016.

Ogólna dostępność (wersja 2015-06-01) została ogłoszona 24 czerwca 2015. W tej wersji wprowadzono następujące zmiany:
- Usuń klucz — pole "Użyj" zostało usunięte.
- Pobierz informacje o kluczu "use" usunięte.
- Zaimportuj klucz do magazynu — usunięte pole "Użyj".
- Przywróć klucz — usunięte pole "use".
- Zmieniono "RSA_OAEP" na "RSA-OAEP" dla algorytmów RSA. Zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).

Druga wersja zapoznawcza (wersja 2015-02-01-Preview) została ogłoszona 20 kwietnia 2015. Aby uzyskać więcej informacji, zobacz wpis w blogu [interfejsu API REST](/archive/blogs/kv/rest-api-update) . Zaktualizowano następujące zadania:

- Wyświetl listę kluczy w magazynie — dodaliśmy obsługę dzielenia na strony.
- Aby wyświetlić listę wersji klucza, Wyświetl listę wersji operacji dodanej do klucza.
- Wyświetlanie wpisów tajnych w magazynie — obsługa dzielenia na strony.
- Wyświetl listę wersji wpisu tajnego, aby wyświetlić listę wersji klucza tajnego.
- Wszystkie operacje — dodano utworzone/zaktualizowane sygnatury czasowe do atrybutów.
- Utwórz wpis tajny typu zawartości, który został dodany do wpisów tajnych.
- Utwórz Tagi dodane do klucza jako informacje opcjonalne.
- Utwórz Tagi tajne dodane jako opcjonalne informacje.
- Zaktualizuj znaczniki dodawane do klucza jako informacje opcjonalne.
- Zaktualizuj Tagi tajne dodane jako informacje opcjonalne.
- Zmieniono maksymalny rozmiar wpisów tajnych z 10 K na 25 KB. Zobacz [temat informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).

## <a name="2014"></a>2014

Pierwsza wersja zapoznawcza (wersja 2014-12-08-Preview) została ogłoszona 8 stycznia 2015.

## <a name="next-steps"></a>Następne kroki

Jeśli masz dodatkowe pytania, skontaktuj się z nami, korzystając z [pomocy technicznej](https://azure.microsoft.com/support/options/).
