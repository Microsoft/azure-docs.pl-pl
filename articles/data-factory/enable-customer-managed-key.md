---
title: Szyfrowanie Azure Data Factory z kluczem zarządzanym przez klienta
description: Zwiększ bezpieczeństwo Data Factory za pomocą Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: 2a943b82689184353b9d350b931a069df9c35ff2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392701"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Szyfrowanie Azure Data Factory przy użyciu kluczy zarządzanych przez klienta

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory szyfruje dane w spoczynku, w tym Definicje jednostek i wszelkie dane buforowane podczas uruchamiania są w toku. Domyślnie dane są szyfrowane przy użyciu losowo generowanego klucza zarządzanego przez firmę Microsoft, który jest unikatowy dla fabryki danych. Aby uzyskać dodatkowe gwarancje bezpieczeństwa, można teraz włączyć Bring Your Own Key (BYOK) z funkcją klucze zarządzane przez klienta w Azure Data Factory. W przypadku określenia klucza zarządzanego przez klienta Data Factory szyfrowania danych klienta przy użyciu __zarówno__ klucza systemu fabryki, jak i CMK. Brak żadnego z nich spowoduje odmowa dostępu do danych i fabryki.

Azure Key Vault jest wymagane do przechowywania kluczy zarządzanych przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Magazyn kluczy i Data Factory muszą znajdować się w tej samej dzierżawie Azure Active Directory (Azure AD) i w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

> [!NOTE]
> Klucz zarządzany przez klienta można skonfigurować tylko w przypadku pustej fabryki danych. Fabryka danych nie może zawierać żadnych zasobów, takich jak połączone usługi, potoki i przepływy danych. Zaleca się włączenie klucza zarządzanego przez klienta bezpośrednio po utworzeniu fabryki.

## <a name="about-customer-managed-keys"></a>Informacje o kluczach zarządzanych przez klienta

Na poniższym diagramie przedstawiono sposób, w jaki Data Factory używa Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

  ![Diagram przedstawiający sposób działania kluczy zarządzanych przez klienta w Azure Data Factory](media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

Poniższa lista zawiera opis kroków w diagramie:

1. Administrator Azure Key Vault przyznaje uprawnienia do kluczy szyfrowania do zarządzanej tożsamości skojarzonej z Data Factory
1. Administrator Data Factory włącza funkcję klucz zarządzany przez klienta w fabryce
1. Data Factory używa zarządzanej tożsamości skojarzonej z fabryką w celu uwierzytelniania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory
1. Data Factory zawija klucz szyfrowania fabryki przy użyciu klucza klienta w Azure Key Vault
1. W przypadku operacji odczytu/zapisu Data Factory wysyła żądania do Azure Key Vault w celu odpakowania klucza szyfrowania konta w celu wykonania operacji szyfrowania i odszyfrowywania

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Wymagania wstępne — konfigurowanie Azure Key Vault i generowanie kluczy

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Włącz usuwanie nietrwałe i nie czyść na Azure Key Vault

Użycie kluczy zarządzanych przez klienta z Data Factory wymaga, aby na Key Vault zostały ustawione dwie właściwości, __nietrwałe usuwanie__ i __nie przeczyszczanie__. Te właściwości można włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy. Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcję zatytułowaną _Włączanie usuwania nietrwałego_ i _Włączanie ochrony przed przeczyszczeniem_ w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../key-vault/general/key-vault-recovery.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../key-vault/general/key-vault-recovery.md)

W przypadku tworzenia nowego Azure Key Vault za pomocą Azure Portal, __usuwanie__ nietrwałe i __nie przeczyszczania__ można włączyć w następujący sposób:

  ![Zrzut ekranu Włącz trwałe usuwanie i przeczyszczanie ochrony podczas tworzenia Key Vault](media/enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Przyznaj Data Factory dostęp do Azure Key Vault

Upewnij się, że Azure Key Vault i Azure Data Factory znajdują się w tej samej dzierżawie Azure Active Directory (Azure AD) i w _tym samym regionie_. Z poziomu Azure Key Vault kontroli dostępu Udziel tożsamość usługi zarządzanej (MSI) następujących uprawnień: _Pobierz_, _Odpakuj klucz_ i _Zawijaj klucz_. Te uprawnienia są wymagane, aby włączyć klucze zarządzane przez klienta w Data Factory.

  ![Zrzut ekranu umożliwiający Data Factory dostęp do Key Vault](media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png)

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Generuj lub Przekaż klucz zarządzany przez klienta do Azure Key Vault

Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Tylko 2048-bitowe klucze RSA są obsługiwane przez szyfrowanie Data Factory. Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/general/about-keys-secrets-certificates.md).

  ![Zrzut ekranu generowanie klucza zarządzanego przez klienta](media/enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

1. Upewnij się, że Data Factory jest puste. Fabryka danych nie może zawierać żadnych zasobów, takich jak połączone usługi, potoki i przepływy danych. Na razie wdrożenie klucza zarządzanego przez klienta do niepustej fabryki spowoduje wystąpienie błędu.

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do Azure Key Vault i wybierz ustawienie klucze. Wybierz żądany klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia

1. Skopiuj wartość pola Identyfikator klucza, która dostarcza identyfikator URI

    ![Zrzut ekranu Pobieranie klucza URI z Key Vault](media/enable-customer-managed-key/04-get-key-identifier.png)

1. Uruchom Azure Data Factory Portal i korzystając z paska nawigacyjnego po lewej stronie, przejdź do Data Factory portal zarządzania

1. Kliknij ikonę __klucza zarządzanego przez klienta__

    ![Zrzut ekranu z włączonym kluczem zarządzanym przez klienta w Data Factory](media/enable-customer-managed-key/05-customer-managed-key-configuration.png)

1. Wprowadź identyfikator URI dla klucza zarządzanego przez klienta, który został skopiowany wcześniej

1. Kliknij przycisk __Zapisz__ i szyfrowanie klucza zarządzane przez klienta jest włączone dla Data Factory

## <a name="update-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować fabrykę danych, aby korzystała z nowej wersji. Wykonaj podobne kroki zgodnie z opisem w sekcji _Włączanie kluczy Customer-Managed_, w tym:

1. Zlokalizuj identyfikator URI nowej wersji klucza za pomocą portalu Azure Key Vault

1. Przejdź do ustawień __klucza zarządzanego przez klienta__

1. Zastąp i wklej identyfikator URI nowego klucza

1. Kliknij przycisk __Zapisz__ , a Data Factory będzie teraz szyfrowany przy użyciu nowej wersji klucza

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania Data Factory, należy ręcznie zaktualizować ustawienia w Data Factory. Wykonaj podobne kroki zgodnie z opisem w sekcji _Włączanie kluczy Customer-Managed_, w tym:

1. Zlokalizuj identyfikator URI nowego klucza za pomocą portalu Azure Key Vault

1. Przejdź do ustawienia __klucza zarządzanego przez klienta__

1. Zastąp i wklej identyfikator URI nowego klucza

1. Kliknij przycisk __Zapisz__ , a Data Factory będzie teraz szyfrowany przy użyciu nowego klucza

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze Customer-Managed

Po włączeniu funkcji klucz zarządzany przez klienta nie można usunąć dodatkowego kroku zabezpieczeń. Zawsze oczekujemy, że klucz dostarczony przez klienta zaszyfruje fabrykę i dane.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.