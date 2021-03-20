---
title: Pobierz informacje o kluczu szyfrowania
titleSuffix: Azure Cognitive Search
description: Pobierz nazwę i wersję klucza szyfrowania używaną w indeksie lub mapie synonimów, aby można było zarządzać kluczem w Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932903"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Uzyskaj informacje o kluczu zarządzanym przez klienta z indeksów i map synonimów

W usłudze Azure Wyszukiwanie poznawcze klucze szyfrowania zarządzane przez klienta są tworzone, przechowywane i zarządzane w programie Azure Key Vault. Jeśli zachodzi potrzeba określenia, czy obiekt jest zaszyfrowany lub którego nazwa lub wersja została użyta, należy użyć interfejsu API REST lub zestawu SDK w celu pobrania właściwości **EncryptionKey** z definicji mapowania indeksu lub synonimu. 

Zalecamy [włączenie rejestrowania](../key-vault/general/logging.md) na Key Vault, aby można było monitorować użycie klucza.

## <a name="get-the-admin-api-key"></a>Pobierz klucz interfejsu API administratora

Aby uzyskać definicje obiektów z usługi wyszukiwania, należy uwierzytelnić się przy użyciu uprawnień administratora. Najprostszym sposobem uzyskania klucza interfejsu API administratora jest użycie portalu.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i Otwórz stronę przegląd usługi wyszukiwania.

1. Po lewej stronie kliknij pozycję **klucze** i skopiuj interfejs API administratora. Klucz administratora jest wymagany na potrzeby pobierania indeksu i synonimu mapy.

W przypadku pozostałych kroków Przełącz się do programu PowerShell i interfejsu API REST. W portalu nie są wyświetlane mapy synonimów ani właściwości klucza szyfrowania indeksów.

## <a name="use-powershell-and-rest"></a>Korzystanie z programu PowerShell i REST

Uruchom następujące polecenia, aby skonfigurować zmienne i pobrać definicje obiektów.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz, który klucz szyfrowania i wersja jest używana, możesz zarządzać kluczem w Azure Key Vault lub sprawdzić inne ustawienia konfiguracji.

+ [Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu programu PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w usłudze Azure Wyszukiwanie poznawcze](search-security-manage-encryption-keys.md)