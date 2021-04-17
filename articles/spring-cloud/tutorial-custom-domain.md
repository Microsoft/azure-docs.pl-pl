---
title: 'Samouczek: mapowanie istniejącej domeny niestandardowej na Azure Spring Cloud'
description: Mapowanie istniejącej niestandardowej nazwy usługi nazw rozproszonych (DNS) na Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 493752a3857b80b43668b6bf1b20480604442955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567999"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Samouczek: mapowanie istniejącej domeny niestandardowej na Azure Spring Cloud

**Ten artykuł dotyczy: ✔️** Java ✔️ C #

Usługa nazw domen (DNS) to technika przechowywania nazw węzłów sieciowych w całej sieci. Ten samouczek mapuje domenę, taką jak www.contoso.com, przy użyciu rekordu CNAME. Zabezpiecza domenę niestandardową przy użyciu certyfikatu i pokazuje, jak wymuszać protokół Transport Layer Security (TLS), znany również jako protokół Secure Sockets Layer (SSL). 

Certyfikaty szyfrują ruch internetowy. Te certyfikaty TLS/SSL mogą być przechowywane w Azure Key Vault. 

## <a name="prerequisites"></a>Wymagania wstępne
* Aplikacja wdrożona w Azure Spring Cloud (zobacz [Szybki start:](spring-cloud-quickstart.md)uruchamianie istniejącej aplikacji Azure Spring Cloud przy użyciu Azure Portal lub używanie istniejącej aplikacji).
* Nazwa domeny z dostępem do rejestru DNS dla dostawcy domeny, takiego jak GoDaddy.
* Certyfikat prywatny (czyli certyfikat z podpisem własnym) od zewnętrznego dostawcy. Certyfikat musi być zgodne z domeną.
* Wdrożone wystąpienie [Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>Zagadnienia dotyczące Private Link kluczy

Wirtualne Azure Spring Cloud zarządzania urządzeniami nie są jeszcze częścią zaufanej usługi azure usługi firmy Microsoft. W związku z tym, aby Azure Spring Cloud ładować certyfikaty z serwera chronionego Key Vault połączeniami prywatnego punktu końcowego, należy dodać następujące Azure Key Vault zapory:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Importowanie certyfikatu
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Przygotowywanie pliku certyfikatu w pliku PFX (opcjonalnie)
Azure Key Vault importowanie certyfikatu prywatnego w formacie PEM i PFX. Jeśli plik PEM [uzyskany](#save-certificate-in-key-vault)od dostawcy certyfikatów nie działa w poniższej sekcji: Zapisywanie certyfikatu w programie Key Vault , wykonaj kroki opisane tutaj, aby wygenerować plik PFX dla Azure Key Vault.

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

Wyeksportuj scalony certyfikat TLS/SSL z kluczem prywatnym, za pomocą których zostało wygenerowane żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _&lt; private-key-file>_ i _&lt; merged-certificate-file>_ ścieżkami do klucza prywatnego i scalonego pliku certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Po wyświetleniu monitu określ hasło eksportu. Tego hasła użyjesz podczas przekazywania certyfikatu TLS/SSL do usługi Azure Key Vault później.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Zapisywanie certyfikatu w Key Vault
Procedura importowania certyfikatu wymaga, aby plik zakodowany w formacie PEM lub PFX był na dysku i musi mieć klucz prywatny. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Aby przekazać certyfikat do magazynu kluczy:
1. Przejdź do wystąpienia magazynu kluczy.
1. W okienku nawigacji po lewej stronie kliknij **pozycję Certyfikaty.**
1. W górnym menu kliknij pozycję **Generuj/zaimportuj**.
1. W **oknie dialogowym Tworzenie certyfikatu** w **obszarze Metoda tworzenia certyfikatu** wybierz pozycję `Import` .
1. W **obszarze Przekaż plik certyfikatu** przejdź do lokalizacji certyfikatu i wybierz go.
1. W **obszarze** Hasło wprowadź klucz prywatny certyfikatu.
1. Kliknij pozycję **Utwórz**.

    ![Importowanie certyfikatu 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Udzielanie Azure Spring Cloud dostępu do magazynu kluczy

Przed zaimportowaniem certyfikatu Azure Spring Cloud dostępu do magazynu kluczy:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Przejdź do wystąpienia magazynu kluczy.
1. W okienku nawigacji po lewej stronie kliknij pozycję **Uzyskaj dostęp do aplikacji Policy**.
1. W górnym menu kliknij pozycję **Dodaj zasady dostępu.**
1. Wypełnij informacje, a następnie kliknij przycisk **Dodaj,** a następnie **pozycję Zapisz** policy dostępu.

| Uprawnienie do tajnego | Uprawnienie do certyfikatu | Wybieranie podmiotu zabezpieczeń |
|--|--|--|
| Pobierz, Lista | Pobierz, Lista | Azure Spring Cloud Domain-Management |

![Importowanie certyfikatu 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Ułatw Azure Spring Cloud dostęp do odczytu do magazynu kluczy, zastąp `<key vault resource group>` elementy i w poniższym `<key vault name>` poleceniu.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importowanie certyfikatu do Azure Spring Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Przejdź do wystąpienia usługi. 
1. W lewym okienku nawigacji aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL.**
1. Następnie kliknij **pozycję Import Key Vault Certificate (Importuj certyfikat).**

    ![Importowanie certyfikatu](./media/custom-dns-tutorial/import-certificate.png)

1. Po pomyślnym zaimportowaniu certyfikatu zostanie on wyświetlony na liście certyfikatów **klucza prywatnego**.

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
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, należy powiązać certyfikat z określoną domeną. Wykonaj kroki opisane w tej sekcji: [Dodawanie powiązania SSL.](#add-ssl-binding)

## <a name="add-custom-domain"></a>Dodaj Custom Domain
Rekord CNAME umożliwia mapowanie niestandardowej nazwy DNS na Azure Spring Cloud. 

> [!NOTE] 
> Rekord A nie jest obsługiwany. 

### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME
Przejdź do dostawcy DNS i dodaj rekord CNAME, aby zamapować domenę na <service_name>.azuremicroservices.io. Tutaj <service_name> to nazwa Twojego Azure Spring Cloud wystąpienia. Obsługujemy domenę z symbolami wieloznaczny i domenę podrzędną. Po dodaniu rekordu CNAME strona rekordów DNS będzie wyglądać następująco: 

![Strona rekordów DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapowanie domeny niestandardowej na Azure Spring Cloud aplikacji
Jeśli nie masz aplikacji w programie Azure Spring Cloud, postępuj zgodnie z instrukcjami w przewodniku Szybki [start:](./spring-cloud-quickstart.md)uruchamianie istniejącej aplikacji Azure Spring Cloud przy użyciu Azure Portal .

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Przejdź do strony aplikacji.

1. Wybierz **Custom Domain**.
2. Następnie **dodaj Custom Domain**. 

    ![Domena niestandardowa](./media/custom-dns-tutorial/custom-domain.png)

3. Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład www.contoso.com. Upewnij się, że typ rekordu Nazwa hosta jest ustawiony na wartość CNAME (<service_name>.azuremicroservices.io)
4. Kliknij **pozycję Weryfikuj,** aby włączyć **przycisk** Dodaj.
5. Kliknij pozycję **Dodaj**.

    ![Dodawanie domeny niestandardowej](./media/custom-dns-tutorial/add-custom-domain.png)

Jedna aplikacja może mieć wiele domen, ale jedna domena może być mapowana tylko na jedną aplikację. Po pomyślnym zamapować domenę niestandardową na aplikację zobaczysz ją w tabeli domen niestandardowych.

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
> Etykieta **Nie zabezpieczona** dla domeny niestandardowej oznacza, że nie jest jeszcze powiązana z certyfikatem SSL. Każde żądanie HTTPS z przeglądarki do domeny niestandardowej otrzyma błąd lub ostrzeżenie.

## <a name="add-ssl-binding"></a>Dodawanie powiązania SSL

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
W tabeli domeny niestandardowej wybierz pozycję **Dodaj powiązanie ssl,** jak pokazano na poprzedniej ilustracji.  
1. Wybierz certyfikat **lub** zaimportuj go.
1. Kliknij pozycję **Zapisz**.

    ![Dodawanie powiązania SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Po pomyślnym dodaniu powiązania SSL stan domeny będzie bezpieczny: W dobrej **kondycji**. 

![Dodawanie powiązania SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS
Domyślnie każda osoba nadal może uzyskać dostęp do Aplikacji przy użyciu protokołu HTTP, ale możesz przekierować wszystkie żądania HTTP do portu HTTPS.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na stronie aplikacji w lewym panelu nawigacyjnym wybierz pozycję **Custom Domain**. Następnie ustaw pozycję **Tylko HTTPS na** wartość *True.*

![Dodawanie powiązania SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Po zakończeniu operacji przejdź do dowolnego z adresów URL protokołu HTTPS, które wskazują aplikację. Zwróć uwagę, że adresy URL HTTP nie działają.

## <a name="see-also"></a>Zobacz też
* [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
* [Importowanie certyfikatu](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Uruchamianie aplikacji Spring Cloud przy użyciu interfejsu wiersza polecenia platformy Azure](./spring-cloud-quickstart.md)
