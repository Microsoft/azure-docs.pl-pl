---
title: Certyfikaty usługi Azure firewall Premium Preview
description: Aby prawidłowo skonfigurować inspekcję protokołu TLS w usłudze Azure firewall Premium Preview, należy skonfigurować i zainstalować pośrednie certyfikaty urzędu certyfikacji.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549909"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certyfikaty usługi Azure firewall Premium Preview 

> [!IMPORTANT]
> Usługa Azure firewall Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Aby poprawnie skonfigurować inspekcję usługi Azure firewall Premium w wersji zapoznawczej, należy podać prawidłowy certyfikat pośredniego urzędu certyfikacji i złożyć go w magazynie kluczy platformy Azure.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certyfikaty używane przez usługę Azure firewall Premium w wersji zapoznawczej

W typowym wdrożeniu są używane trzy typy certyfikatów:

- **Pośredni certyfikat urzędu certyfikacji (certyfikat urzędu certyfikacji)**

   Urząd certyfikacji to organizacja, która jest zaufana do podpisywania certyfikatów cyfrowych. Urząd certyfikacji weryfikuje tożsamość i autentyczność firmy lub indywidualnego żądania certyfikatu. Jeśli weryfikacja zakończy się pomyślnie, urząd certyfikacji wystawia podpisany certyfikat. Gdy serwer przedstawia certyfikat dla klienta (na przykład przeglądarki sieci Web) podczas uzgadniania SSL/TLS, klient próbuje zweryfikować podpis na liście *znanych dobrych* nadawców. Przeglądarki sieci Web zwykle są dostarczane z listami urzędów certyfikacji, z których niejawnie ufają, aby identyfikować hosty. Jeśli urząd nie znajduje się na liście, podobnie jak w przypadku niektórych witryn, które podpisują własne certyfikaty, przeglądarka ostrzega użytkownika o tym, że certyfikat nie jest podpisany przez uznawany Urząd i prosi użytkownika o kontynuowanie komunikacji z niezweryfikowaną witryną.

- **Certyfikat serwera (certyfikat witryny sieci Web)**

   Certyfikat skojarzony z konkretną nazwą domeny. Jeśli witryna sieci Web ma prawidłowy certyfikat, oznacza to, że urząd certyfikacji podjął kroki w celu sprawdzenia, czy adres internetowy rzeczywiście należy do tej organizacji. Po wpisaniu adresu URL lub kliknięciu linku do bezpiecznej witryny sieci Web przeglądarka sprawdzi certyfikat pod kątem następujących cech:
   - Adres witryny sieci Web jest zgodny z adresem w certyfikacie.
   - Certyfikat jest podpisany przez urząd certyfikacji, który jest rozpoznawany przez przeglądarkę jako *zaufany* urząd.
   
   Czasami użytkownicy mogą łączyć się z serwerem z niezaufanym certyfikatem. Zapora platformy Azure porzuci połączenie, tak jakby serwer zakończył połączenie.

- **Certyfikat głównego urzędu certyfikacji (certyfikat główny)**

   Urząd certyfikacji może wystawiać wiele certyfikatów w formie struktury drzewa. Certyfikat główny jest najwyższego poziomu certyfikatu drzewa.

Usługa Azure firewall Premium w wersji zapoznawczej może przechwytywać ruch wychodzący HTTP/S i automatycznie generować certyfikat serwera dla programu `www.website.com` . Ten certyfikat jest generowany przy użyciu pośredniego certyfikatu urzędu certyfikacji, który jest udostępniany. Aby ta procedura działała, przeglądarka użytkownika końcowego i aplikacje klienckie muszą ufać certyfikatowi głównego urzędu certyfikacji lub pośredniemu certyfikatowi urzędu certyfikacji. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Proces certyfikatu":::

## <a name="intermediate-ca-certificate-requirements"></a>Wymagania dotyczące certyfikatu pośredniego urzędu certyfikacji

Upewnij się, że certyfikat urzędu certyfikacji spełnia następujące wymagania:

- Po wdrożeniu jako wpis tajny Key Vault należy użyć pliku PFX bez hasła (PKCS12) z certyfikatem i kluczem prywatnym.

- Musi to być jeden certyfikat i nie powinien zawierać całego łańcucha certyfikatów.  

- Okres ten musi być ważny przez jeden rok.  

- Musi to być klucz prywatny RSA o minimalnym rozmiarze 4096 bajtów.  

- Musi mieć `KeyUsage` rozszerzenie oznaczone jako krytyczne z `KeyCertSign` flagą (RFC 5280; użycie klucza 4.2.1.3).

- Musi mieć `BasicContraints` rozszerzenie oznaczone jako krytyczne (RFC 5280; 4.2.1.9 podstawowe ograniczenia).  

- `CA`Flaga musi mieć ustawioną wartość true.

- Długość ścieżki musi być większa lub równa jednej.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) to magazyn tajny zarządzany przez platformę, za pomocą którego można chronić klucze tajne oraz certyfikaty protokołu TLS/SSL. Usługa Azure firewall Premium obsługuje integrację z Key Vaultami dla certyfikatów serwera, które są dołączone do zasad zapory.
 
Aby skonfigurować magazyn kluczy:

- Należy zaimportować istniejący certyfikat z parą kluczy do magazynu kluczy. 
- Alternatywnie można również użyć klucza tajnego magazynu kluczy, który jest przechowywany jako plik PFX o formacie mniejszym niż 64.  Plik PFX jest certyfikatem cyfrowym zawierającym klucz prywatny i klucz publiczny.
- Zaleca się użycie importu certyfikatu urzędu certyfikacji, ponieważ umożliwia skonfigurowanie alertu na podstawie daty wygaśnięcia certyfikatu.
- Po zaimportowaniu certyfikatu lub wpisu tajnego należy zdefiniować zasady dostępu w magazynie kluczy, aby zezwolić na uzyskanie dostępu do certyfikatu/klucza tajnego.
- Podany certyfikat urzędu certyfikacji musi być zaufany przez obciążenie platformy Azure. Upewnij się, że zostały poprawnie wdrożone.

Można utworzyć lub ponownie użyć istniejącej tożsamości zarządzanej przypisanej przez użytkownika, która jest wykorzystywana przez zaporę platformy Azure do pobierania certyfikatów z Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Konfigurowanie certyfikatu w zasadach

Aby skonfigurować certyfikat urzędu certyfikacji w zasadach warstwy Premium zapory, wybierz zasady, a następnie wybierz pozycję **Inspekcja TLS (wersja zapoznawcza)**. Na stronie **Inspekcja protokołu TLS** wybierz pozycję **włączone** . Następnie wybierz certyfikat urzędu certyfikacji w Azure Key Vault, jak pokazano na poniższym rysunku:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagram omówienia usługi Azure firewall Premium":::
 
> [!IMPORTANT]
> Aby wyświetlić i skonfigurować certyfikat z Azure Portal należy dodać konto użytkownika platformy Azure do zasad dostępu Key Vault. Nadaj kontu użytkownika uprawnienia **Get** i **list** w obszarze **uprawnień klucza tajnego**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Zasady dostępu Azure Key Vault":::


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli certyfikat urzędu certyfikacji jest prawidłowy, ale nie możesz uzyskać dostępu do nazw FQDN ani adresów URL w ramach inspekcji protokołu TLS, sprawdź następujące elementy:

- Upewnij się, że certyfikat serwera sieci Web jest prawidłowy.  

- Upewnij się, że certyfikat głównego urzędu certyfikacji jest zainstalowany w systemie operacyjnym klienta.  

- Upewnij się, że przeglądarka lub klient HTTPS zawiera prawidłowy certyfikat główny. Program Firefox i inne przeglądarki mogą mieć specjalne zasady certyfikacji.  

- Upewnij się, że typ docelowy adresu URL w regule aplikacji dotyczy poprawnej ścieżki i innych hiperlinków osadzonych na docelowej stronie HTML. Symboli wieloznacznych można używać do łatwego pokrycia całej wymaganej ścieżki URL.  


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o funkcjach usługi Azure firewall Premium](premium-features.md)
