---
title: Samouczek — mapowanie istniejącej domeny niestandardowej na chmurę z platformą Azure
description: Jak zmapować istniejącą nazwę niestandardowego usługi nazw rozproszonych (DNS) na chmurę Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d06a6eb8b504f2c5dd09de70d79f50a3ed5d89a3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844731"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapowanie istniejącej domeny niestandardowej na chmurę z platformą Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Usługa nazw domen (DNS) to technika przechowywania nazw węzłów sieciowych w całej sieci. Ten samouczek mapuje domenę, taką jak www.contoso.com, przy użyciu rekordu CNAME. Zabezpiecza domenę niestandardową za pomocą certyfikatu i pokazuje, jak wymusić Transport Layer Security (TLS), znane także jako SSL (SSL). 

Certyfikaty szyfrują ruch internetowy. Te certyfikaty TLS/SSL mogą być przechowywane w Azure Key Vault. 

## <a name="prerequisites"></a>Wymagania wstępne
* Aplikacja wdrożona w chmurze Azure wiosennej (zobacz [Szybki Start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal](spring-cloud-quickstart.md)lub użycie istniejącej aplikacji).
* Nazwa domeny z dostępem do rejestru DNS dla dostawcy domeny, na przykład GoDaddy.
* Certyfikat prywatny (czyli certyfikat z podpisem własnym) od innego dostawcy. Certyfikat musi być zgodny z domeną.
* Wdrożone wystąpienie [Azure Key Vault](../key-vault/general/overview.md)

## <a name="import-certificate"></a>Importowanie certyfikatu
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Przygotowywanie pliku certyfikatu w PFX (opcjonalnie)
Azure Key Vault obsługiwać Importowanie certyfikatu prywatnego w formacie PEM i PFX. Jeśli plik PEM uzyskany od dostawcy certyfikatów nie działa w sekcji poniżej: [Zapisz certyfikat w Key Vault](#save-certificate-in-key-vault), postępuj zgodnie z poniższymi instrukcjami, aby wygenerować PFX dla Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Scalanie certyfikatów pośrednich

Jeśli Twój urząd certyfikacji dał Ci wiele certyfikatów w łańcuchu certyfikatów, musisz kolejno scalić certyfikaty.

Aby to zrobić, otwórz każdy otrzymany certyfikat w edytorze tekstów.

Utwórz plik scalonego certyfikatu o nazwie _mergedcertificate.crt_. W edytorze tekstów skopiuj zawartość każdego certyfikatu do tego pliku. Kolejność certyfikatów powinna być zgodna z kolejnością w łańcuchu certyfikatów, poczynając od Twojego certyfikatu i kończąc na certyfikacie głównym. Wygląda to następująco:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>Eksportowanie certyfikatu do pliku PFX

Wyeksportuj scalony certyfikat TLS/SSL z kluczem prywatnym, za pomocą którego zostało wygenerowane żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _&lt; pliku prywatnego-Key>_ i _&lt; scalonego pliku certyfikatu>_ ze ścieżkami do klucza prywatnego i scalonym plikiem certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Po wyświetleniu monitu określ hasło eksportu. To hasło będzie używane podczas przekazywania certyfikatu TLS/SSL do Azure Key Vault później.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Zapisz certyfikat w Key Vault
Procedura importowania certyfikatu wymaga, aby plik w formacie PEM lub PFX znajdował się na dysku, a użytkownik musi mieć klucz prywatny. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Aby przekazać certyfikat do magazynu kluczy:
1. Przejdź do wystąpienia magazynu kluczy.
1. W okienku nawigacji po lewej stronie kliknij pozycję **Certyfikaty**.
1. W górnym menu kliknij pozycję **Generuj/Importuj**.
1. W oknie dialogowym **Tworzenie certyfikatu** w obszarze **metoda tworzenia certyfikatu** wybierz opcję `Import` .
1. W obszarze **Przekaż plik certyfikatu** przejdź do lokalizacji certyfikatu i wybierz ją.
1. W obszarze **hasło** wprowadź klucz prywatny certyfikatu.
1. Kliknij pozycję **Utwórz**.

    ![Importuj certyfikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Przyznaj usłudze Azure sprężynowy dostęp do magazynu kluczy

Przed zaimportowaniem certyfikatu musisz przyznać usłudze Azure wiosny dostęp do magazynu kluczy.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Przejdź do wystąpienia magazynu kluczy.
1. W okienku nawigacji po lewej stronie kliknij pozycję **zasady dostępu**.
1. W górnym menu kliknij pozycję **Dodaj zasady dostępu**.
1. Wypełnij informacje, a następnie kliknij przycisk **Dodaj** , a następnie **Zapisz** zasady dostępu.

| Uprawnienie tajne | Uprawnienie do certyfikatu | Wybierz podmiot zabezpieczeń |
|--|--|--|
| Pobierz, Wyświetl | Pobierz, Wyświetl | Domain-Management chmury z wiosną platformy Azure |

![Importuj certyfikat 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Przyznaj usłudze Azure wiosny Cloud dostęp do odczytu do magazynu kluczy, Zastąp `<key vault resource group>` i `<key vault name>` w poniższym poleceniu.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importuj certyfikat do chmury wiosennej platformy Azure
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Przejdź do wystąpienia usługi. 
1. W okienku nawigacji po lewej stronie aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**.
1. Następnie kliknij pozycję **Importuj certyfikat Key Vault**.

    ![Importowanie certyfikatu](./media/custom-dns-tutorial/import-certificate.png)

1. Po pomyślnym zaimportowaniu certyfikatu zobaczysz go na liście **certyfikatów klucza prywatnego**.

    ![Certyfikat klucza prywatnego](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Aby wyświetlić listę zaimportowanych certyfikatów:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz powiązać certyfikat z określoną domeną. Wykonaj kroki opisane w tej sekcji: [Dodawanie powiązania SSL](#add-ssl-binding).

## <a name="add-custom-domain"></a>Dodaj domenę niestandardową
Możesz użyć rekordu CNAME, aby zmapować niestandardową nazwę DNS na chmurę wiosenną platformy Azure. 

> [!NOTE] 
> Rekord A nie jest obsługiwany. 

### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME
Przejdź do dostawcy DNS i Dodaj rekord CNAME, aby zmapować domenę na <service_name>. azuremicroservices.io. W tym miejscu <service_name> jest nazwą Twojego wystąpienia chmury Azure wiosennej. Obsługujemy wieloznaczną domenę i domenę podrzędną. Po dodaniu rekordu CNAME Strona rekordów DNS będzie wyglądać podobnie do poniższego przykładu: 

![Strona rekordów DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapowanie domeny niestandardowej do aplikacji w chmurze platformy Azure
Jeśli nie masz aplikacji w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Przejdź do strony aplikacji.

1. Wybierz opcję **domena niestandardowa**.
2. Następnie **Dodaj domenę niestandardową**. 

    ![Domena niestandardowa](./media/custom-dns-tutorial/custom-domain.png)

3. Wpisz w pełni kwalifikowaną nazwę domeny, do której dodano rekord CNAME, taki jak www.contoso.com. Upewnij się, że typ rekordu nazwy hosta jest ustawiony na wartość CNAME (<service_name>. azuremicroservices.io)
4. Kliknij przycisk **Sprawdź poprawność** , aby włączyć przycisk **Dodaj** .
5. Kliknij pozycję **Dodaj**.

    ![Dodaj domenę niestandardową](./media/custom-dns-tutorial/add-custom-domain.png)

Jedna aplikacja może mieć wiele domen, ale jedna domena może mapować tylko jedną aplikację. Po pomyślnym zmapowaniu domeny niestandardowej do aplikacji zobaczysz ją w tabeli domeny niestandardowej.

![Tabela domen niestandardowych](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Aby wyświetlić listę domen niestandardowych:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Etykieta **niezabezpieczona** dla domeny niestandardowej oznacza, że nie jest ona jeszcze powiązana z certyfikatem SSL. Wszystkie żądania HTTPS z przeglądarki do domeny niestandardowej otrzymają błąd lub ostrzeżenie.

## <a name="add-ssl-binding"></a>Dodawanie powiązania SSL

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
W tabeli domena niestandardowa wybierz pozycję **Dodaj powiązanie SSL** , jak pokazano na poprzedniej ilustracji.  
1. Wybierz **certyfikat** lub zaimportuj go.
1. Kliknij przycisk **Zapisz**.

    ![Dodawanie powiązania SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Po pomyślnym dodaniu powiązania SSL stan domeny będzie bezpieczny: **dobra kondycja**. 

![Dodawanie powiązania SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS
Domyślnie każdy może nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, ale można przekierować wszystkie żądania HTTP do portu HTTPS.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na stronie aplikacji w lewym okienku nawigacji wybierz pozycję **domena niestandardowa**. Następnie ustaw **tylko https** na *wartość true*.

![Dodawanie powiązania SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Po zakończeniu operacji przejdź do dowolnego adresu URL HTTPS, który wskazuje aplikację. Należy zauważyć, że adresy URL protokołu HTTP nie działają.

## <a name="see-also"></a>Zobacz także
* [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
* [Importowanie certyfikatu](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Uruchamianie aplikacji w chmurze wiosny przy użyciu interfejsu wiersza polecenia platformy Azure](./spring-cloud-quickstart.md)
