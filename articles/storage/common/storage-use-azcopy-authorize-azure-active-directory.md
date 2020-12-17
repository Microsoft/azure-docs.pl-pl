---
title: Autoryzuj dostęp do obiektów BLOB za pomocą AzCopy & Azure Active Directory | Microsoft Docs
description: Za pomocą Azure Active Directory (Azure AD) można podać poświadczenia autoryzacji dla operacji AzCopy.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 46a5c941822dd258b420b51c710734dc3152f30f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617412"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autoryzuj dostęp do obiektów BLOB za pomocą AzCopy i Azure Active Directory (Azure AD)

Możesz podać AzCopy z poświadczeniami autoryzacji za pomocą usługi Azure AD. Dzięki temu nie trzeba dołączać tokenu sygnatury dostępu współdzielonego (SAS) do poszczególnych poleceń. 

Zacznij od zweryfikowania przypisań ról. Następnie wybierz typ _podmiotu zabezpieczeń_ , który chcesz autoryzować. [Tożsamością użytkownika](../../active-directory/fundamentals/add-users-azure-active-directory.md), [zarządzaną tożsamością](../../active-directory/managed-identities-azure-resources/overview.md)i jednostką [usługi](../../active-directory/develop/app-objects-and-service-principals.md) są każdy typ podmiotu zabezpieczeń.

Tożsamość użytkownika jest dowolnym użytkownikiem, który ma tożsamość w usłudze Azure AD. Jest to najprostszy podmiot zabezpieczeń, który ma zostać autoryzowany. Zarządzane tożsamości i jednostki usługi są doskonałymi opcjami, jeśli planujesz używać AzCopy wewnątrz skryptu, który jest uruchamiany bez interakcji z użytkownikiem. Zarządzana tożsamość jest lepszym rozwiązaniem w przypadku skryptów uruchamianych z maszyny wirtualnej platformy Azure, a jednostka usługi jest lepsza dla skryptów uruchamianych lokalnie. 

Aby uzyskać więcej informacji na temat AzCopy, [Rozpocznij pracę z AzCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Weryfikowanie przypisań ról

Wymagany poziom autoryzacji zależy od tego, czy planujesz przekazywanie plików czy po prostu pobierasz je.

Jeśli chcesz tylko pobrać pliki, sprawdź, czy rola [czytnika danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) została przypisana do tożsamości użytkownika, tożsamości zarządzanej lub nazwy głównej usługi.

Jeśli chcesz przekazać pliki, sprawdź, czy jedna z tych ról została przypisana do podmiotu zabezpieczeń:

- [Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Role te można przypisać do podmiotu zabezpieczeń w dowolnym z następujących zakresów:

- Kontener (system plików)
- Konto magazynu
- Grupa zasobów
- Subskrypcja

Aby dowiedzieć się, jak weryfikować i przypisywać role, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Należy pamiętać, że propagacja ról platformy Azure może potrwać do 5 minut.

Nie trzeba mieć jednej z tych ról przypisanych do podmiotu zabezpieczeń, jeśli podmiot zabezpieczeń zostanie dodany do listy kontroli dostępu (ACL) docelowego kontenera lub katalogu. Na liście ACL podmiot zabezpieczeń musi mieć uprawnienia do zapisu w katalogu docelowym i uprawnienia do wykonywania w kontenerze i w każdym katalogu nadrzędnym.

Aby dowiedzieć się więcej, zobacz [model kontroli dostępu w Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autoryzuj tożsamość użytkownika

Po sprawdzeniu, czy tożsamość użytkownika ma wymagany poziom autoryzacji, Otwórz wiersz polecenia, wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login
```

Jeśli wystąpi błąd, spróbuj uwzględnić identyfikator dzierżawy organizacji, do której należy konto magazynu.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal.

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, podaj kod, a następnie wybierz przycisk **dalej** .

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie Zaloguj się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu możesz zamknąć okno przeglądarki i zacząć korzystać z AzCopy.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Autoryzacja tożsamości zarządzanej

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który jest uruchamiany bez interakcji z użytkownikiem, a skrypt jest uruchamiany z maszyny wirtualnej platformy Azure. W przypadku korzystania z tej opcji nie będzie konieczne przechowywanie żadnych poświadczeń na maszynie wirtualnej.

Możesz zalogować się do konta przy użyciu tożsamości zarządzanej na poziomie systemu, która została włączona na maszynie wirtualnej, lub przy użyciu identyfikatora klienta, identyfikatora obiektu lub identyfikatora zasobu tożsamości zarządzanej przypisanej przez użytkownika, która została przypisana do maszyny wirtualnej.

Aby dowiedzieć się więcej na temat włączania tożsamości zarządzanej na poziomie systemu lub tworzenia tożsamości zarządzanej przypisanej przez użytkownika, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autoryzuj przy użyciu tożsamości zarządzanej na poziomie systemu

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana na poziomie systemu. Zobacz [tożsamość zarządzana przypisana przez system](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Następnie w konsoli poleceń wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autoryzuj przy użyciu tożsamości zarządzanej przypisanej przez użytkownika

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana przypisana przez użytkownika. Zobacz [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Następnie w konsoli poleceń wpisz dowolne z poniższych poleceń, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Zastąp `<client-id>` symbol zastępczy identyfikatorem klienta tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Zamień `<object-id>` symbol zastępczy na identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Zastąp `<resource-id>` symbol zastępczy identyfikatorem zasobu tożsamości zarządzanej przypisanej przez użytkownika.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Autoryzuj nazwę główną usługi

Jest to świetna opcja, jeśli planujesz używać AzCopy wewnątrz skryptu, który działa bez interakcji z użytkownikiem, szczególnie w przypadku uruchamiania lokalnego. Jeśli planujesz uruchamiać AzCopy na maszynach wirtualnych działających na platformie Azure, zarządzana tożsamość usługi jest łatwiejsza do administrowania. Aby dowiedzieć się więcej, zobacz sekcję [Autoryzuj tożsamość zarządzaną](#authorize-a-managed-identity) w tym artykule.

Przed uruchomieniem skryptu należy zalogować się interaktywnie co najmniej jeden raz, aby zapewnić AzCopy z poświadczeniami nazwy głównej usługi.  Te poświadczenia są przechowywane w zabezpieczonym i zaszyfrowanym pliku, dzięki czemu skrypt nie musi podawać informacji poufnych.

Możesz zalogować się do konta przy użyciu klucza tajnego klienta lub przy użyciu hasła certyfikatu skojarzonego z rejestracją aplikacji jednostki usługi.

Aby dowiedzieć się więcej na temat tworzenia nazwy głównej usługi, zobacz [How to: Use the Portal, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md).

Aby dowiedzieć się więcej na temat ogólnych nazw głównych usług, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autoryzowanie jednostki usługi przy użyciu klucza tajnego klienta

Zacznij od ustawienia `AZCOPY_SPA_CLIENT_SECRET` zmiennej środowiskowej na klucz tajny klienta rejestracji aplikacji jednostki usługi.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiona w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. Dzięki temu wartość jest dostępna tylko dla bieżącej sesji.

Ten przykład pokazuje, jak można to zrobić w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Rozważ użycie monitu, jak pokazano w tym przykładzie. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń konsoli.  

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Zastąp `<application-id>` symbol zastępczy identyfikatorem aplikacji rejestracji aplikacji jednostki usługi. Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autoryzowanie jednostki usługi przy użyciu certyfikatu

Jeśli wolisz użyć własnych poświadczeń do autoryzacji, możesz przekazać certyfikat do rejestracji aplikacji, a następnie użyć tego certyfikatu do zalogowania.

Oprócz przekazywania certyfikatu do rejestracji aplikacji trzeba również mieć kopię certyfikatu zapisaną na komputerze lub maszynie wirtualnej, na której będzie działać AzCopy. Ta kopia certyfikatu powinna znajdować się w temacie. PFX lub. Format PEM i musi zawierać klucz prywatny. Klucz prywatny powinien być chroniony hasłem. Jeśli używasz systemu Windows, a certyfikat istnieje tylko w magazynie certyfikatów, upewnij się, że ten certyfikat został wyeksportowany do pliku PFX (w tym klucza prywatnego). Aby uzyskać wskazówki, zobacz [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Następnie ustaw dla `AZCOPY_SPA_CERT_PASSWORD` zmiennej środowiskowej hasło certyfikatu.

> [!NOTE]
> Upewnij się, że ta wartość jest ustawiona w wierszu polecenia, a nie w ustawieniach zmiennych środowiskowych systemu operacyjnego. Dzięki temu wartość jest dostępna tylko dla bieżącej sesji.

Ten przykład pokazuje, jak to zadanie można wykonać w programie PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Następnie wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Zastąp `<path-to-certificate-file>` symbol zastępczy względną lub w pełni kwalifikowaną ścieżką do pliku certyfikatu. AzCopy zapisuje ścieżkę do tego certyfikatu, ale nie zapisuje kopii certyfikatu, więc pamiętaj, aby zachować ten certyfikat w miejscu. Zastąp `<tenant-id>` symbol zastępczy identyfikatorem dzierżawy organizacji, do której należy konto magazynu. Aby znaleźć identyfikator dzierżawy, wybierz pozycję **Azure Active Directory właściwości > > identyfikator katalogu** w Azure Portal.

> [!NOTE]
> Rozważ użycie monitu, jak pokazano w tym przykładzie. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń konsoli. 

## <a name="authorize-without-a-secret-store"></a>Autoryzuj bez magazynu wpisów tajnych

Jeśli system operacyjny nie ma magazynu wpisów tajnych, takiego jak *zestaw kluczy* systemu Linux, `azcopy login` polecenie nie będzie działać. Zamiast tego można ustawić zmienne środowiskowe w pamięci przed uruchomieniem każdej operacji. 

### <a name="authorize-a-user-identity"></a>Autoryzuj tożsamość użytkownika

Po sprawdzeniu, czy tożsamość użytkownika ma wymagany poziom autoryzacji, wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Następnie Uruchom dowolne polecenie AzCopy (na przykład: `azcopy list https://contoso.blob.core.windows.net` ).

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, podaj kod, a następnie wybierz przycisk **dalej** .

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie Zaloguj się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu można wykonać operację.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autoryzuj przy użyciu tożsamości zarządzanej na poziomie systemu

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana na poziomie systemu. Zobacz [tożsamość zarządzana przypisana przez system](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Następnie Uruchom dowolne polecenie AzCopy (na przykład: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autoryzuj przy użyciu tożsamości zarządzanej przypisanej przez użytkownika

Najpierw upewnij się, że na maszynie wirtualnej została włączona tożsamość zarządzana przypisana przez użytkownika. Zobacz [tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Następnie wpisz dowolne z poniższych poleceń, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

Zastąp `<client-id>` symbol zastępczy identyfikatorem klienta tożsamości zarządzanej przypisanej przez użytkownika.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

Zamień `<object-id>` symbol zastępczy na identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

Zastąp `<resource-id>` symbol zastępczy identyfikatorem zasobu tożsamości zarządzanej przypisanej przez użytkownika.

Po ustawieniu tych zmiennych można uruchomić dowolne polecenie AzCopy (na przykład: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-a-service-principal"></a>Autoryzuj nazwę główną usługi

Przed uruchomieniem skryptu należy zalogować się interaktywnie co najmniej jeden raz, aby zapewnić AzCopy z poświadczeniami nazwy głównej usługi.  Te poświadczenia są przechowywane w zabezpieczonym i zaszyfrowanym pliku, dzięki czemu skrypt nie musi podawać informacji poufnych.

Możesz zalogować się do konta przy użyciu klucza tajnego klienta lub przy użyciu hasła certyfikatu skojarzonego z rejestracją aplikacji jednostki usługi.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autoryzowanie jednostki usługi przy użyciu klucza tajnego klienta

Wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
```

Zastąp `<application-id>` symbol zastępczy identyfikatorem aplikacji rejestracji aplikacji jednostki usługi. Zamień `<client-secret>` symbol zastępczy na wpis tajny klienta.

> [!NOTE]
> Rozważ użycie monitu w celu zebrania hasła od użytkownika. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń. 

Następnie Uruchom dowolne polecenie AzCopy (na przykład: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autoryzowanie jednostki usługi przy użyciu certyfikatu

Jeśli wolisz użyć własnych poświadczeń do autoryzacji, możesz przekazać certyfikat do rejestracji aplikacji, a następnie użyć tego certyfikatu do zalogowania.

Oprócz przekazywania certyfikatu do rejestracji aplikacji trzeba również mieć kopię certyfikatu zapisaną na komputerze lub maszynie wirtualnej, na której będzie działać AzCopy. Ta kopia certyfikatu powinna znajdować się w temacie. PFX lub. Format PEM i musi zawierać klucz prywatny. Klucz prywatny powinien być chroniony hasłem. 

Wpisz następujące polecenie, a następnie naciśnij klawisz ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
```

Zastąp `<path-to-certificate-file>` symbol zastępczy względną lub w pełni kwalifikowaną ścieżką do pliku certyfikatu. AzCopy zapisuje ścieżkę do tego certyfikatu, ale nie zapisuje kopii certyfikatu, więc pamiętaj, aby zachować ten certyfikat w miejscu. Zastąp `<certificate-password>` symbol zastępczy hasłem certyfikatu.

> [!NOTE]
> Rozważ użycie monitu w celu zebrania hasła od użytkownika. Dzięki temu Twoje hasło nie będzie wyświetlane w historii poleceń. 

Następnie Uruchom dowolne polecenie AzCopy (na przykład: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat AzCopy, [Zacznij korzystać z usługi AzCopy](storage-use-azcopy-v10.md)

- Jeśli masz pytania, problemy lub ogólne informacje zwrotne, prześlij je [na](https://github.com/Azure/azure-storage-azcopy) stronę usługi GitHub.