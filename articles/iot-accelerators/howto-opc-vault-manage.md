---
title: Jak zarządzać usługą certyfikatów magazynu OPC — Azure | Microsoft Docs
description: Zarządzanie certyfikatami głównego urzędu certyfikacji magazynu OPC i uprawnieniami użytkownika.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646367"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Zarządzanie usługą certyfikatów magazynu OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

W tym artykule opisano zadania administracyjne dotyczące usługi zarządzania certyfikatami magazynu OPC na platformie Azure. Zawiera informacje o sposobach odnawiania certyfikatów urzędu certyfikacji wystawcy, jak odnowić listę odwołania certyfikatów (CRL) i sposób udzielania dostępu do użytkowników i odwoływania się do nich.

## <a name="create-or-renew-the-root-ca-certificate"></a>Utwórz lub odnów certyfikat głównego urzędu certyfikacji

Po wdrożeniu magazynu OPC należy utworzyć certyfikat głównego urzędu certyfikacji. Bez prawidłowego certyfikatu urzędu certyfikacji wystawcy nie można podpisać ani wystawiać certyfikatów aplikacji. Zapoznaj się z [certyfikatami](howto-opc-vault-secure-ca.md#certificates) , aby zarządzać certyfikatami przy użyciu rozsądnych, bezpiecznych okresów istnienia. Odnów certyfikat wystawcy urzędu certyfikacji po połowie okresu istnienia. Podczas odnawiania należy również wziąć pod uwagę, że skonfigurowany okres istnienia certyfikatu aplikacji z nowo podpisanym certyfikatem nie powinien przekroczyć okresu istnienia certyfikatu urzędu certyfikacji wystawcy.
> [!IMPORTANT]
> Rola Administrator jest wymagana do utworzenia lub odnowienia certyfikatu urzędu certyfikacji wystawcy.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup-app.azurewebsites.net` i zaloguj się.
2. Przejdź do pozycji **grupy certyfikatów**.
3. Na liście znajduje się jedna domyślna grupa certyfikatów. Wybierz pozycję **Edytuj**.
4. W obszarze **Edytuj szczegóły grupy certyfikatów** możesz zmodyfikować nazwę podmiotu i okres istnienia urzędu certyfikacji i certyfikatów aplikacji. Temat i okresy istnienia powinny być ustawiane tylko raz przed wystawieniem pierwszego certyfikatu urzędu certyfikacji. Zmiany okresu istnienia podczas operacji mogą spowodować niespójne okresy istnienia wystawionych certyfikatów i list CRL.
5. Wprowadź prawidłowy temat (na przykład `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > W przypadku zmiany tematu należy odnowić certyfikat wystawcy lub usługa nie będzie mogła podpisać certyfikatów aplikacji. Podmiot konfiguracji jest sprawdzany pod względem tematu aktywnego certyfikatu wystawcy. Jeśli tematy nie są zgodne, odmówiono podpisania certyfikatu.
6. Wybierz pozycję **Zapisz**.
7. Jeśli w tym momencie napotkasz błąd "zabronione", poświadczenia użytkownika nie mają uprawnienia administratora do modyfikowania lub tworzenia nowego certyfikatu głównego. Domyślnie użytkownik, który wdrożył usługę, ma role administratora i podpisywania usługi. Inni użytkownicy muszą zostać dodani do ról osoby zatwierdzającej, składnika zapisywania lub administratora, odpowiednio do rejestracji aplikacji Azure Active Directory (Azure AD).
8. Wybierz pozycję **szczegóły**. Powinno to spowodować wyświetlenie zaktualizowanych informacji.
9. Wybierz pozycję **odnów certyfikat urzędu certyfikacji** , aby wystawić certyfikat pierwszego wystawcy urzędu certyfikacji lub odnowić certyfikat wystawcy. Następnie wybierz przycisk **OK**.
10. Po kilku sekundach zobaczysz **Szczegóły certyfikatu**. Aby pobrać najnowszy certyfikat urzędu certyfikacji i listę CRL na potrzeby dystrybucji do aplikacji OPC UA, wybierz **wystawcę** lub **listę CRL**.

Teraz usługa zarządzania certyfikatami OPC UA jest gotowa do wystawiania certyfikatów dla aplikacji OPC UA.

## <a name="renew-the-crl"></a>Odnów listę CRL

Odnowienie listy CRL jest aktualizacją, która powinna być dystrybuowana do aplikacji w regularnych odstępach czasu. Urządzenia OPC UA, które obsługują rozszerzenie x509 punktu dystrybucji listy CRL, mogą bezpośrednio aktualizować listę CRL z punktu końcowego mikrousługi. Inne urządzenia OPC UA mogą wymagać ręcznej aktualizacji lub można je zaktualizować za pomocą rozszerzeń wypychania serwera GDS (*), aby zaktualizować listy zaufania za pomocą certyfikatów i list CRL.

W poniższym przepływie pracy wszystkie żądania certyfikatów w usuniętych Stanach są odwoływane na listach CRL, które odnoszą się do certyfikatu wystawcy urzędu certyfikacji, dla którego zostały wystawione. Numer wersji listy CRL jest zwiększany o 1. <br>
> [!NOTE]
> Wszystkie wystawione listy CRL są ważne do momentu wygaśnięcia certyfikatu urzędu certyfikacji wystawcy. Wynika to z faktu, że Specyfikacja OPC UA nie wymaga obowiązkowego, deterministycznego modelu dystrybucji dla listy CRL.

> [!IMPORTANT]
> Aby odnowić listę CRL wystawcy, wymagana jest rola administratora.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup.azurewebsites.net` i zaloguj się.
2. Przejdź do strony **grupy certyfikatów** .
3. Wybierz pozycję **szczegóły**. Powinno to spowodować wyświetlenie bieżącego certyfikatu i informacji o liście CRL.
4. Wybierz pozycję **Aktualizuj listę odwołania certyfikatów (CRL)** , aby wystawić zaktualizowaną listę CRL dla wszystkich aktywnych certyfikatów wystawcy w magazynie magazynu OPC.
5. Po kilku sekundach zobaczysz **Szczegóły certyfikatu**. Aby pobrać najnowszy certyfikat urzędu certyfikacji i listę CRL na potrzeby dystrybucji do aplikacji OPC UA, wybierz **wystawcę** lub **listę CRL**.

## <a name="manage-user-roles"></a>Zarządzanie rolami użytkowników

Zarządzanie rolami użytkowników dla mikrousługi magazynu OPC w aplikacji Azure AD Enterprise. Aby uzyskać szczegółowy opis definicji ról, zobacz [role](howto-opc-vault-secure-ca.md#roles).

Domyślnie uwierzytelniony użytkownik w dzierżawie może zalogować się w usłudze jako czytelnik. Wyższe role uprzywilejowane wymagają ręcznego zarządzania w Azure Portal lub przy użyciu programu PowerShell.

### <a name="add-user"></a>Dodaj użytkownika

1. Otwórz witrynę Azure Portal.
2. Przejdź do pozycji **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz pozycję Rejestracja mikrousługi magazynu OPC (domyślnie `resourceGroupName-service` ).
4. Przejdź do pozycji **Użytkownicy i grupy**.
5. Wybierz pozycję **Dodaj użytkownika**.
6. Wybierz lub Zaproś użytkownika o przypisanie do określonej roli.
7. Wybierz rolę dla użytkowników.
8. Wybierz opcję **Przypisz**.
9. W przypadku użytkowników z rolą administratora lub osoby zatwierdzającej Kontynuuj dodawanie Azure Key Vault zasad dostępu.

### <a name="remove-user"></a>Usuwanie użytkownika

1. Otwórz witrynę Azure Portal.
2. Przejdź do pozycji **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.
3. Wybierz pozycję Rejestracja mikrousługi magazynu OPC (domyślnie `resourceGroupName-service` ).
4. Przejdź do pozycji **Użytkownicy i grupy**.
5. Wybierz użytkownika z rolą do usunięcia, a następnie wybierz pozycję **Usuń**.
6. Aby usunąć użytkowników z roli administrator lub osoba zatwierdzająca, usuń je również z zasad Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Dodawanie zasad dostępu użytkowników do Azure Key Vault

Dodatkowe zasady dostępu są wymagane dla osób zatwierdzających i administratorów.

Domyślnie tożsamość usługi ma tylko ograniczone uprawnienia dostępu Key Vault, aby zapobiec podniesionym operacjom lub zmianom, które mają zostać wykonane bez personifikacji użytkownika. Podstawowe uprawnienia usługi są uzyskiwane i pojawiają się dla wpisów tajnych i certyfikatów. W przypadku wpisów tajnych istnieje tylko jeden wyjątek: usługa może usunąć klucz prywatny z magazynu wpisów tajnych po jego zaakceptowaniu przez użytkownika. Wszystkie inne operacje wymagają uprawnień personifikowanych przez użytkownika.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku roli osoby zatwierdzającej należy dodać do Key Vault następujące uprawnienia

1. Otwórz witrynę Azure Portal.
2. Przejdź do magazynu OPC, który jest `resourceGroupName` używany podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx` .
4. Przejdź do pozycji **zasady dostępu**.
5. Wybierz pozycję **Dodaj nowy**.
6. Pomiń szablon. Nie istnieje szablon, który spełnia wymagania.
7. Wybierz **pozycję Wybierz podmiot zabezpieczeń** i wybierz użytkownika do dodania lub Zaproś nowego użytkownika do dzierżawy.
8. Wybierz następujące **uprawnienia**: **pobieranie**, **Wyświetlanie** i **podpisywanie**.
9. Wybierz następujące **uprawnienia tajne**: **Get**, **list**, **Set** i **delete**.
10. Wybierz następujące **uprawnienia certyfikatów**: **Pobierz** i **Wyświetl**.
11. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zapisz**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>W przypadku roli administratora należy dodać do Key Vault następujące uprawnienia

1. Otwórz witrynę Azure Portal.
2. Przejdź do magazynu OPC, który jest `resourceGroupName` używany podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx` .
4. Przejdź do pozycji **zasady dostępu**.
5. Wybierz pozycję **Dodaj nowy**.
6. Pomiń szablon. Nie istnieje szablon, który spełnia wymagania.
7. Wybierz **pozycję Wybierz podmiot zabezpieczeń** i wybierz użytkownika do dodania lub Zaproś nowego użytkownika do dzierżawy.
8. Wybierz następujące **uprawnienia**: **pobieranie**, **Wyświetlanie** i **podpisywanie**.
9. Wybierz następujące **uprawnienia tajne**: **Get**, **list**, **Set** i **delete**.
10. Wybierz następujące **uprawnienia certyfikatów**: **pobieranie**, **Wyświetlanie**, **Aktualizowanie**, **Tworzenie** i **Importowanie**.
11. Wybierz **przycisk OK**, a następnie wybierz pozycję **Zapisz**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Usuń zasady dostępu użytkowników z Azure Key Vault

1. Otwórz witrynę Azure Portal.
2. Przejdź do magazynu OPC, który jest `resourceGroupName` używany podczas wdrażania.
3. Przejdź do Key Vault `resourceGroupName-xxxxx` .
4. Przejdź do pozycji **zasady dostępu**.
5. Znajdź użytkownika do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać certyfikatami i użytkownikami magazynu OPC, możesz:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja urządzeń OPC](howto-opc-vault-secure.md)
