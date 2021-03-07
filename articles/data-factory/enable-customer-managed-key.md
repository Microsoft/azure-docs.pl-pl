---
title: Szyfrowanie Azure Data Factory z kluczem zarządzanym przez klienta
description: Zwiększ bezpieczeństwo Data Factory za pomocą Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: c6c376e44c6135a800e6f7e281f8ea85b828329a
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443888"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Szyfrowanie Azure Data Factory przy użyciu kluczy zarządzanych przez klienta

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory szyfruje dane w spoczynku, w tym Definicje jednostek i wszelkie dane buforowane podczas uruchamiania są w toku. Domyślnie dane są szyfrowane przy użyciu losowo generowanego klucza zarządzanego przez firmę Microsoft, który jest unikatowy dla fabryki danych. Aby zapewnić dodatkowe gwarancje bezpieczeństwa, można teraz włączyć Bring Your Own Key (BYOK) z użyciem funkcji klucze zarządzane przez klienta w Azure Data Factory. W przypadku określenia klucza zarządzanego przez klienta Data Factory szyfrowania danych klienta przy użyciu __zarówno__ klucza systemu fabryki, jak i CMK. Brak żadnego z nich spowoduje odmowa dostępu do danych i fabryki.

Azure Key Vault jest wymagane do przechowywania kluczy zarządzanych przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Magazyn kluczy i Data Factory muszą znajdować się w tej samej dzierżawie Azure Active Directory (Azure AD) i w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Informacje o kluczach zarządzanych przez klienta

Na poniższym diagramie przedstawiono sposób, w jaki Data Factory używa Azure Active Directory i Azure Key Vault do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagram przedstawiający sposób działania kluczy zarządzanych przez klienta w Azure Data Factory.":::

Poniższa lista zawiera opis kroków w diagramie:

1. Administrator Azure Key Vault przyznaje uprawnienia do kluczy szyfrowania do zarządzanej tożsamości skojarzonej z Data Factory
1. Administrator Data Factory włącza funkcję klucz zarządzany przez klienta w fabryce
1. Data Factory używa zarządzanej tożsamości skojarzonej z fabryką w celu uwierzytelniania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory
1. Data Factory zawija klucz szyfrowania fabryki przy użyciu klucza klienta w Azure Key Vault
1. W przypadku operacji odczytu/zapisu Data Factory wysyła żądania do Azure Key Vault w celu odpakowania klucza szyfrowania konta w celu wykonania operacji szyfrowania i odszyfrowywania

Istnieją dwa sposoby dodawania szyfrowania kluczy zarządzanych przez klienta do fabryk danych. Jeden z nich jest w trakcie tworzenia przez fabrykę w Azure Portal, a drugi to tworzenie fabryczne w Data Factory interfejsie użytkownika.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Wymagania wstępne — konfigurowanie Azure Key Vault i generowanie kluczy

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Włącz usuwanie nietrwałe i nie czyść na Azure Key Vault

Użycie kluczy zarządzanych przez klienta z Data Factory wymaga, aby na Key Vault zostały ustawione dwie właściwości, __nietrwałe usuwanie__ i __nie przeczyszczanie__. Te właściwości można włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy. Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz [Azure Key Vault zarządzanie odzyskiwaniem z użyciem nietrwałego usuwania i przeczyszczania](../key-vault/general/key-vault-recovery.md)

W przypadku tworzenia nowego Azure Key Vault za pomocą Azure Portal, __usuwanie__ nietrwałe i __nie przeczyszczania__ można włączyć w następujący sposób:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usuwania nietrwałego i przeczyszczania podczas tworzenia Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Przyznaj Data Factory dostęp do Azure Key Vault

Upewnij się, że Azure Key Vault i Azure Data Factory znajdują się w tej samej dzierżawie Azure Active Directory (Azure AD) i w _tym samym regionie_. W ramach kontroli dostępu Azure Key Vault Udziel usłudze Data Factory następujących uprawnień: _Pobierz_, _Odpakuj klucz_ i _Zawijaj klucz_. Te uprawnienia są wymagane, aby włączyć klucze zarządzane przez klienta w Data Factory.

* Jeśli chcesz dodać szyfrowanie klucza zarządzanego [przez klienta po utworzeniu fabryki w Data Factory interfejsie użytkownika](#post-factory-creation-in-data-factory-ui), upewnij się, że tożsamość usługi zarządzanej fabryki danych (msi) ma trzy uprawnienia do Key Vault
* Jeśli chcesz dodać szyfrowanie klucza zarządzanego [przez klienta podczas procesu tworzenia fabryki w Azure Portal](#during-factory-creation-in-azure-portal), upewnij się, że zarządzana tożsamość użytkownika (UA-mi) ma trzy uprawnienia do Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Zrzut ekranu przedstawiający sposób włączania Data Factory dostępu do Key Vault.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Generuj lub Przekaż klucz zarządzany przez klienta do Azure Key Vault

Możesz utworzyć własne klucze i zapisać je w magazynie kluczy. Można też użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Tylko 2048-bitowe klucze RSA są obsługiwane przez szyfrowanie Data Factory. Aby uzyskać więcej informacji, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Zrzut ekranu przedstawiający sposób generowania klucza Customer-Managed.":::

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

### <a name="post-factory-creation-in-data-factory-ui"></a>Tworzenie po utworzeniu fabryki w Data Factory interfejsie użytkownika

W tej sekcji omówiono proces dodawania szyfrowania kluczy zarządzanych przez klienta w Data Factory interfejsie użytkownika _po_ utworzeniu fabryki.

> [!NOTE]
> Klucz zarządzany przez klienta można skonfigurować tylko w przypadku pustej fabryki danych. Fabryka danych nie może zawierać żadnych zasobów, takich jak połączone usługi, potoki i przepływy danych. Zaleca się włączenie klucza zarządzanego przez klienta bezpośrednio po utworzeniu fabryki.

> [!IMPORTANT]
> To podejście nie działa z fabrykami obsługującymi zarządzane sieci wirtualne. Rozważ użycie [trasy alternatywnej](#during-factory-creation-in-azure-portal), jeśli chcesz zaszyfrować takie fabryki.

1. Upewnij się, że tożsamość usługi zarządzanej (MSI) fabryki danych ma uprawnienia _pobieranie_, odpakowywanie _klucza_ i _Zawijanie klucza_ do Key Vault.

1. Upewnij się, że Data Factory jest puste. Fabryka danych nie może zawierać żadnych zasobów, takich jak połączone usługi, potoki i przepływy danych. Na razie wdrożenie klucza zarządzanego przez klienta do niepustej fabryki spowoduje wystąpienie błędu.

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do Azure Key Vault i wybierz ustawienie klucze. Wybierz żądany klucz, a następnie wybierz klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia

1. Skopiuj wartość pola Identyfikator klucza, która dostarcza :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="zrzutu ekranu identyfikatora URI pobierającego identyfikator URI klucza z Key Vault.":::

1. Uruchom Azure Data Factory Portal i korzystając z paska nawigacyjnego po lewej stronie, przejdź do Data Factory portal zarządzania

1. Kliknij ikonę klucza zarządzanego przez __klienta__ , :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Aby włączyć klucz zarządzany przez klienta w interfejsie użytkownika Data Factory.":::

1. Wprowadź identyfikator URI dla klucza zarządzanego przez klienta, który został skopiowany wcześniej

1. Kliknij przycisk __Zapisz__ i szyfrowanie klucza zarządzane przez klienta jest włączone dla Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Podczas tworzenia fabryki w Azure Portal

W tej sekcji omówiono procedurę dodawania szyfrowania kluczy zarządzanych przez klienta w Azure Portal podczas wdrażania _w_ fabryce.

Aby zaszyfrować fabrykę, Data Factory musi najpierw pobrać klucz zarządzany przez klienta z Key Vault. Ponieważ wdrożenie fabryki jest nadal w toku, tożsamość usługi zarządzanej (MSI) nie jest jeszcze dostępne do uwierzytelnienia przy użyciu Key Vault. W związku z tym aby użyć tego podejścia, klient musi przypisać tożsamość zarządzaną przez użytkownika (UA-MI) do fabryki danych. Przyjmujemy role zdefiniowane w UA-MI i uwierzytelniają się za pomocą Key Vault.

Aby dowiedzieć się więcej o tożsamości zarządzanej przypisanej przez użytkownika, zobacz [typy tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) i [Przypisywanie ról dla tożsamości zarządzanej przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Upewnij się, że tożsamość zarządzana przypisana przez użytkownika (UA-MI) ma uprawnienia _pobieranie_, odpakowywanie _klucza_ i _Zawijanie klucza_ do Key Vault

1. Na karcie __Zaawansowane__ zaznacz pole wyboru _Włącz szyfrowanie przy użyciu_ zrzutu ekranu klucza zarządzanego przez klienta 
   :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="na karcie Zaawansowane dla środowiska tworzenia fabryki danych w Azure Portal.":::

1. Podaj adres URL klucza zarządzanego przez klienta przechowywanego w Key Vault

1. Wybierz odpowiednią tożsamość zarządzaną przypisaną przez użytkownika do uwierzytelniania za pomocą Key Vault

1. Kontynuuj wdrażanie fabryczne

## <a name="update-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować fabrykę danych, aby korzystała z nowej wersji. Wykonaj podobne kroki zgodnie z opisem w sekcji [Data Factory interfejsie użytkownika](#post-factory-creation-in-data-factory-ui), w tym:

1. Zlokalizuj identyfikator URI nowej wersji klucza za pomocą portalu Azure Key Vault

1. Przejdź do ustawień __klucza zarządzanego przez klienta__

1. Zastąp i wklej identyfikator URI nowego klucza

1. Kliknij przycisk __Zapisz__ , a Data Factory będzie teraz szyfrowany przy użyciu nowej wersji klucza

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania Data Factory, należy ręcznie zaktualizować ustawienia w Data Factory. Wykonaj podobne kroki zgodnie z opisem w sekcji [Data Factory interfejsie użytkownika](#post-factory-creation-in-data-factory-ui), w tym:

1. Zlokalizuj identyfikator URI nowego klucza za pomocą portalu Azure Key Vault

1. Przejdź do ustawienia __klucza zarządzanego przez klienta__

1. Zastąp i wklej identyfikator URI nowego klucza

1. Kliknij przycisk __Zapisz__ , a Data Factory będzie teraz szyfrowany przy użyciu nowego klucza

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po włączeniu funkcji klucz zarządzany przez klienta nie można usunąć dodatkowego kroku zabezpieczeń. Zawsze oczekujemy, że klucz dostarczony przez klienta zaszyfruje fabrykę i dane.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.