---
title: Konfigurowanie protokołu TLS dla usługi w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak określić punkt końcowy HTTPS dla roli sieci web i jak przekazać certyfikat TLS/SSL w celu zabezpieczenia aplikacji. W tych przykładach użyto witryny Azure portal.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: c69b74cf91d8e097f8ad8a9ba2a16f3375f483ae
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024850"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Konfigurowanie protokołu TLS dla aplikacji na platformie Azure

Transport Layer Security (TLS), wcześniej znany jako szyfrowanie SSL (Secure Socket Layer), jest najczęściej stosowaną metodą zabezpieczania danych przesyłanych przez Internet. W tym typowym zadaniu omówiono sposób określania punktu końcowego protokołu HTTPS dla roli sieci web i przekazywania certyfikatu TLS/SSL w celu zabezpieczenia aplikacji.

> [!NOTE]
> Procedury w tym zadaniu dotyczą usług w chmurze azure; w przypadku usług app services, zobacz [ten](../app-service/configure-ssl-bindings.md)plik .
>

To zadanie używa wdrożenia produkcyjnego. Informacje na temat korzystania z wdrożenia przemieszczania znajduje się na końcu tego tematu.

Przeczytaj [to](cloud-services-how-to-create-deploy-portal.md) najpierw, jeśli nie utworzono jeszcze usługi w chmurze.

## <a name="step-1-get-a-tlsssl-certificate"></a>Krok 1: Uzyskaj certyfikat TLS/SSL
Aby skonfigurować TLS dla aplikacji, należy najpierw uzyskać certyfikat TLS/SSL podpisany przez urząd certyfikacji (CA), zaufaną stronę trzecią, która wystawia w tym celu certyfikaty. Jeśli jeszcze go nie masz, musisz go uzyskać od firmy, która sprzedaje certyfikaty TLS/SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów TLS/SSL na platformie Azure:

* Certyfikat musi zawierać klucz publiczny.
* Certyfikat musi zostać utworzony w celu wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* Nazwa podmiotu certyfikatu musi być zgodna z domeną używaną do uzyskiwania dostępu do usługi w chmurze. Nie można uzyskać certyfikatu TLS/SSL od urzędu certyfikacji (CA) dla domeny cloudapp.net. Musisz uzyskać niestandardową nazwę domeny, aby używać podczas uzyskiwania dostępu do usługi. Podczas żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną do uzyskiwania dostępu do aplikacji. Na przykład, jeśli niestandardowa nazwa domeny jest **contoso.com** żądanie certyfikatu od urzędu certyfikacji dla ***.contoso.com** lub **www\.contoso.com**.
* Certyfikat musi używać szyfrowania 2048-bitowego.

Do celów testowych można [utworzyć](cloud-services-certs-create.md) i używać certyfikatu z podpisem własnym. Certyfikat z podpisem własnym nie jest uwierzytelniony za pośrednictwem urzędu certyfikacji i może używać domeny cloudapp.net jako adresu URL witryny sieci Web. Na przykład następujące zadanie używa certyfikatu z podpisem własnym, w którym nazwa pospolita (CN) używana w certyfikacie jest **sslexample.cloudapp.net**.

Następnie należy dołączyć informacje o certyfikacie w plikach definicji usługi i konfiguracji usługi.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Modyfikowanie definicji usługi i plików konfiguracyjnych
Aplikacja musi być skonfigurowana do używania certyfikatu i musi zostać dodany punkt końcowy HTTPS. W rezultacie definicji usługi i pliki konfiguracji usługi muszą zostać zaktualizowane.

1. W środowisku deweloperskim otwórz plik definicji usługi (CSDEF), dodaj sekcję **Certyfikaty** w sekcji **WebRole** i dołącz następujące informacje o certyfikacie (i certyfikatach pośrednich):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Sekcja **Certyfikaty** definiuje nazwę naszego certyfikatu, jego lokalizację i nazwę magazynu, w którym się znajduje.

   Uprawnienia (atrybut)`permissionLevel` można ustawić na jedną z następujących wartości:

   | Wartość uprawnień | Opis |
   | --- | --- |
   | limitedOrElevated |**(Domyślnie)** Wszystkie procesy roli mogą uzyskiwać dostęp do klucza prywatnego. |
   | Podwyższone |Tylko podwyższone poziomach procesów może uzyskać dostęp do klucza prywatnego. |

2. W pliku definicji usługi dodaj element **InputEndpoint** w sekcji **Punkty końcowe,** aby włączyć protokół HTTPS:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. W pliku definicji usługi dodaj element **powiązania** w sekcji **Witryny.** Ten element dodaje powiązanie HTTPS do mapowania punktu końcowego do witryny:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Wszystkie wymagane zmiany w pliku definicji usługi zostały zakończone; ale nadal trzeba dodać informacje o certyfikacie do pliku konfiguracji usługi.
4. W pliku konfiguracji usługi (CSCFG), ServiceConfiguration.Cloud.cscfg, dodaj **wartość certyfikatów** do wartości certyfikatu. Poniższy przykład kodu zawiera szczegółowe informacje o **certyfikaty** sekcji, z wyjątkiem wartości odcisku palca.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(W tym przykładzie używa **sha1** dla algorytmu odcisku palca. Określ odpowiednią wartość dla algorytmu odcisku palca certyfikatu.)

Teraz, gdy pliki definicji usługi i konfiguracji usługi zostały zaktualizowane, spakować wdrożenie do przekazania na platformę Azure. Jeśli używasz **cspack**, nie używaj **/generateConfigurationFile** flagi, ponieważ spowoduje to zastąpienie informacji o po prostu wstawionych.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Przekazywanie certyfikatu
Połącz się z portalem Azure i...

1. W sekcji **Wszystkie zasoby** portalu wybierz usługę w chmurze.

    ![Publikowanie usługi w chmurze](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kliknij **pozycję Certyfikaty**.

    ![Kliknij ikonę certyfikatów](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kliknij **pozycję Przekaż** u góry obszaru certyfikatów.

    ![Kliknij element menu Przekaż](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Podaj **plik**, **hasło**, a następnie kliknij przycisk **Przekaż** u dołu obszaru wprowadzania danych.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Łączenie się z wystąpieniem roli przy użyciu protokołu HTTPS
Teraz, gdy wdrożenie jest uruchomione na platformie Azure, można połączyć się z nim przy użyciu protokołu HTTPS.

1. Kliknij **adres URL witryny,** aby otworzyć przeglądarkę internetową.

   ![Kliknij adres URL witryny](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. W przeglądarce internetowej zmodyfikuj łącze, aby użyć **https** zamiast **http**, a następnie odwiedź stronę.

   > [!NOTE]
   > Jeśli używasz certyfikatu z podpisem własnym, podczas przeglądania punktu końcowego HTTPS skojarzonego z certyfikatem z podpisem własnym może pojawić się błąd certyfikatu w przeglądarce. Używanie certyfikatu podpisanego przez zaufany urząd certyfikacji eliminuje ten problem; w międzyczasie można zignorować błąd. (Inną opcją jest dodanie certyfikatu z podpisem własnym do magazynu certyfikatów zaufanych certyfikatów użytkownika).
   >
   >

   ![Podgląd witryny](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Jeśli chcesz użyć protokołu TLS dla wdrożenia przejściowego zamiast wdrożenia produkcyjnego, musisz najpierw określić adres URL używany do wdrożenia przejściowego. Po wdrożeniu usługi w chmurze adres URL środowiska przejściowego jest określany przez identyfikator **guiD identyfikatora wdrożenia** w tym formacie:`https://deployment-id.cloudapp.net/`  
   >
   > Utwórz certyfikat o nazwie pospolitej (CN) równej adresowi URL opartemu na identyfikatorze GUID (na przykład **328187776e774ceda8fc57609d404462.cloudapp.net**). Użyj portalu, aby dodać certyfikat do usługi w chmurze etapowej. Następnie dodaj informacje o certyfikacie do plików CSDEF i CSCFG, przepakuj aplikację i zaktualizuj wdrożenie etapowe, aby użyć nowego pakietu.
   >

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą w chmurze](cloud-services-how-to-manage-portal.md).



