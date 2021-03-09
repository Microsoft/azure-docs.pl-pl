---
title: Wdrażanie i Konfigurowanie certyfikatów urzędu certyfikacji przedsiębiorstwa dla usługi Azure firewall Premium Preview
description: Dowiedz się, jak wdrażać i konfigurować certyfikaty urzędu certyfikacji przedsiębiorstwa dla usługi Azure firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525499"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Wdrażanie i Konfigurowanie certyfikatów urzędu certyfikacji przedsiębiorstwa na potrzeby wersji zapoznawczej usługi Azure firewall

> [!IMPORTANT]
> Usługa Azure firewall Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Usługa Azure firewall Premium w wersji zapoznawczej obejmuje funkcję inspekcji TLS, która wymaga łańcucha uwierzytelniania certyfikatu. W przypadku wdrożeń produkcyjnych należy użyć infrastruktury PKI przedsiębiorstwa do wygenerowania certyfikatów używanych z usługą Azure firewall Premium. Ten artykuł służy do tworzenia certyfikatu pośredniego urzędu certyfikacji dla usługi Azure firewall Premium Preview i zarządzania nim.

Aby uzyskać więcej informacji na temat certyfikatów używanych przez usługę Azure firewall Premium w wersji zapoznawczej, zobacz artykuł [Azure firewall Premium Preview Certificates](premium-certificates.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby użyć urzędu certyfikacji przedsiębiorstwa do wygenerowania certyfikatu do użycia z usługą Azure firewall Premium w wersji zapoznawczej, muszą być dostępne następujące zasoby: 

- Las Active Directory 
- Główny urząd certyfikacji Active Directory usług certyfikacji z włączoną rejestracją w sieci Web 
- Zasady zapory platformy Azure w warstwie Premium z usługą Premium 
- Azure Key Vault 
- Zarządzana tożsamość z uprawnieniami do odczytu dla **certyfikatów i wpisów tajnych** zdefiniowanych w zasadach dostępu Key Vault 

## <a name="request-and-export-a-certificate"></a>Żądanie i Eksportowanie certyfikatu

1. Uzyskaj dostęp do witryny rejestracji w sieci Web w głównym urzędzie certyfikacji, zazwyczaj `https://<servername>/certsrv` i wybierz pozycję **Żądaj certyfikatu**.
1. Wybierz pozycję **Zaawansowane żądanie certyfikatu**.
1. Wybierz pozycję **Utwórz i prześlij żądanie do tego urzędu certyfikacji**.
1. Wypełnij formularz przy użyciu szablonu podrzędnego urzędu certyfikacji, jak pokazano poniżej:
1. Prześlij żądanie i Zainstaluj certyfikat.
1. Przy założeniu, że to żądanie jest nawiązywane z serwera z systemem Windows przy użyciu programu Internet Explorer, Otwórz **Opcje internetowe**.
1. Przejdź do karty **zawartość** i wybierz pozycję **Certyfikaty**.
1. Wybierz certyfikat, który został właśnie wystawiony, a następnie wybierz pozycję **Eksportuj**.
1. Wybierz pozycję **dalej** , aby rozpocząć pracę z kreatorem. Wybierz opcję **tak, eksportuj klucz prywatny**, a następnie wybierz przycisk **dalej**.
1. Format pliku PFX jest domyślnie wybrany. **Jeśli to możliwe, należy usunąć zaznaczenie opcji Dołącz wszystkie certyfikaty do ścieżki certyfikacji**. W przypadku wyeksportowania całego łańcucha certyfikatów proces importowania do zapory platformy Azure zakończy się niepowodzeniem.
1. Przypisz i Potwierdź hasło, aby chronić klucz, a następnie wybierz przycisk **dalej**.
1. Wybierz nazwę pliku i lokalizację eksportowania, a następnie wybierz przycisk **dalej**.
1. Wybierz pozycję **Zakończ** i Przenieś wyeksportowany certyfikat do bezpiecznej lokalizacji.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Dodawanie certyfikatu do zasad zapory

1. W Azure Portal przejdź do strony certyfikaty Key Vault i wybierz pozycję **Generuj/Importuj**.
1. Wybierz pozycję **Importuj** jako metodę tworzenia, nazwij certyfikat, wybierz wyeksportowany plik PFX, wprowadź hasło, a następnie wybierz pozycję **Utwórz**.
1. Przejdź do strony **inspekcji protokołu TLS (wersja zapoznawcza)** zasad zapory i wybierz zarządzaną tożsamość, Key Vault i certyfikat. 
1. Wybierz pozycję **Zapisz**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="Inspekcja protokołu TLS":::

## <a name="validate-tls-inspection"></a>Weryfikuj inspekcję protokołu TLS

1. Utwórz regułę aplikacji przy użyciu inspekcji protokołu TLS dla wybranego docelowego adresu URL lub nazwy FQDN.  Na przykład: `*bing.com`.
1. Z komputera przyłączonego do domeny w zakresie źródłowym reguły przejdź do miejsca docelowego i wybierz symbol blokady obok paska adresu w przeglądarce. Certyfikat powinien wskazywać, że został wystawiony przez urząd certyfikacji przedsiębiorstwa, a nie publiczny urząd certyfikacji.
1. Pokaż certyfikat, aby wyświetlić więcej szczegółów, w tym ścieżkę certyfikatu.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="Szczegóły certyfikatu":::
1. W Log Analytics Uruchom następujące zapytanie KQL, aby zwrócić wszystkie żądania, które podlegają inspekcji protokołu TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Wynik pokazuje pełny adres URL przekontrolowanego ruchu: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="zapytanie KQL":::

## <a name="next-steps"></a>Następne kroki

[Zapora Azure Premium w wersji zapoznawczej w Azure Portal](premium-portal.md)
